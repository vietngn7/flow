# Category System Design - Brain Dump Parser

## Overview

Двухуровневая система категорий:
1. **Default Categories** - базовый набор для всех пользователей
2. **Custom Categories** - персональные категории с описаниями

---

## Architecture

### Database Schema

```sql
-- User Custom Categories
CREATE TABLE user_custom_categories (
  id UUID PRIMARY KEY,
  user_id UUID REFERENCES users(id),
  category_key VARCHAR(50) NOT NULL, -- "mia", "startup", "podcast"
  category_name VARCHAR(100) NOT NULL, -- "Мия", "Стартап", "Подкаст"
  description TEXT NOT NULL, -- "Моя собака Мия - все дела связанные с ней"
  keywords TEXT[], -- ["собака", "мия", "vet", "корм", "прогулка"]
  examples TEXT[], -- ["купить еду Мие", "гулять с Мией"]
  emoji VARCHAR(10), -- "🐕", "🚀", "🎙️" (optional)
  color VARCHAR(7), -- "#FF5733" hex color for UI
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW(),
  is_active BOOLEAN DEFAULT TRUE,

  UNIQUE(user_id, category_key)
);

-- Index for fast category matching
CREATE INDEX idx_user_categories_keywords
ON user_custom_categories USING GIN (keywords);

-- Tasks table (updated)
CREATE TABLE tasks (
  id UUID PRIMARY KEY,
  user_id UUID REFERENCES users(id),
  title TEXT,
  category_type ENUM('default', 'custom'),
  category_key VARCHAR(50), -- "work", "mia", "startup", etc.
  category_display_name VARCHAR(100), -- for display in UI
  -- ... other fields
);
```

---

## TypeScript Types

```typescript
// Default category keys (enum)
enum DefaultCategory {
  WORK = 'work',
  PERSONAL = 'personal',
  SHOPPING = 'shopping',
  HEALTH = 'health',
  FINANCE = 'finance',
  HOME = 'home',
  LEARNING = 'learning',
  SOCIAL = 'social',
  ERRANDS = 'errands',
  GENERAL = 'general'
}

// Custom category interface
interface CustomCategory {
  id: string;
  userId: string;
  categoryKey: string;        // "mia", "startup" - short unique key
  categoryName: string;        // "Мия", "Стартап" - display name
  description: string;         // "Моя собака Мия - все дела..."
  keywords: string[];          // ["собака", "мия", "vet"]
  examples?: string[];         // ["купить еду Мие"]
  emoji?: string;              // "🐕"
  color?: string;              // "#FF5733"
  createdAt: Date;
  updatedAt: Date;
  isActive: boolean;
}

// Task category reference
interface TaskCategory {
  type: 'default' | 'custom';
  key: string;                 // category key
  displayName: string;         // for UI
  emoji?: string;
}

// Category matching result
interface CategoryMatch {
  category: TaskCategory;
  confidence: number;          // 0-1 score
  matchedKeywords: string[];   // which keywords matched
  matchReason: 'keyword' | 'example' | 'contextual';
}
```

---

## Category Management API

### User Category CRUD

```typescript
// Create custom category
interface CreateCategoryRequest {
  categoryKey: string;        // "mia" (user chooses or auto-generated)
  categoryName: string;       // "Мія"
  description: string;        // "Моя собака..."
  keywords?: string[];        // optional, can be extracted from description
  examples?: string[];
  emoji?: string;
  color?: string;
}

// Bot flow for creating category
async function createCustomCategory(
  userId: string,
  request: CreateCategoryRequest
): Promise<CustomCategory> {
  // Validate category key uniqueness
  // Extract keywords from description if not provided
  // Store in database
  // Invalidate user's prompt cache
}

// Update category
async function updateCustomCategory(
  userId: string,
  categoryId: string,
  updates: Partial<CreateCategoryRequest>
): Promise<CustomCategory> {
  // Update category
  // Invalidate prompt cache
}

// Delete category
async function deleteCustomCategory(
  userId: string,
  categoryId: string
): Promise<void> {
  // Soft delete (set is_active = false)
  // OR hard delete + migrate existing tasks to 'general'
}

// List user categories
async function getUserCategories(
  userId: string
): Promise<CustomCategory[]> {
  // Return all active custom categories
}
```

---

## Prompt Generation

### Dynamic Prompt Assembly

```typescript
function buildBrainDumpPrompt(userId: string): string {
  const basePrompt = readFile('brain-dump-prompt-v2.md');

  // Get user's custom categories
  const customCategories = await getUserCategories(userId);

  // Build custom categories section
  const customCategoriesText = customCategories.length > 0
    ? customCategories.map(cat =>
        `- "${cat.categoryKey}": "${cat.description}"`
      ).join('\n')
    : "No custom categories defined.";

  // Replace template variables
  const prompt = basePrompt
    .replace('{{CURRENT_DATE}}', getCurrentDate())
    .replace('{{USER_TIMEZONE}}', getUserTimezone(userId))
    .replace('{{USER_PROFILE}}', getUserProfile(userId))
    .replace('{{CUSTOM_CATEGORIES}}', customCategoriesText);

  return prompt;
}

// Example output of {{CUSTOM_CATEGORIES}} section:
/*
- "mia": "Моя собака Мія - все дела связанные с ней (еда, прогулки, ветеринар)"
- "startup": "Мой стартап SaaS продукт - задачи по продукту, маркетингу, продажам"
- "podcast": "Подкаст про AI - запись, монтаж, публикация эпизодов"
*/
```

---

## User Flows

### Flow 1: Creating First Custom Category

```
User: "хочу добавить категорию для моей собаки"

Bot: "Отлично! Давай создадим категорию.

     Как назовём категорию? (короткое слово)"

User: "мия"

Bot: "Опиши категорию 'мия' в одном предложении.

     Что это такое? Какие дела сюда относятся?"

User: "моя собака, все дела связанные с ней - еда, прогулки, ветеринар"

Bot: "Понял!

     ┌─ НОВАЯ КАТЕГОРИЯ ────────────────┐
     │ Название: Мия                    │
     │ Ключ: mia                        │
     │                                  │
     │ Описание:                        │
     │ Моя собака, все дела связанные   │
     │ с ней - еда, прогулки, ветеринар │
     │                                  │
     │ Автоматически найденные слова:   │
     │ • собака                         │
     │ • мия                            │
     │ • еда                            │
     │ • прогулки                       │
     │ • ветеринар                      │
     │                                  │
     │ Хочешь добавить emoji? 🐕        │
     └──────────────────────────────────┘

     [Добавить emoji 🐕] [Другой emoji] [Без emoji]"

User: [Добавить 🐕]

Bot: "✅ Категория 'мия' создана!

     Теперь когда ты пишешь что-то про собаку,
     я автоматически отнесу это к категории 'мия' 🐕

     Попробуем?"

User: "купить корм для мии вечером"

Bot: [shows task with category "mia" 🐕]
```

### Flow 2: Managing Multiple Categories

```
User: "/categories" или "мои категории"

Bot: "📂 Твои категории:

     DEFAULT (10):
     • work, personal, shopping, health, finance
     • home, learning, social, errands, general

     CUSTOM (3):
     🐕 mia - Моя собака
     🚀 startup - Стартап SaaS
     🎙️ podcast - Подкаст про AI

     [Добавить новую] [Редактировать] [Удалить]"

User: [Добавить новую]

Bot: "Название новой категории?"

User: "спортзал"

Bot: "Опиши категорию 'спортзал':"

User: "тренировки, упражнения, фитнес план"

Bot: "✅ Категория 'спортзал' создана!

     Автослова: тренировки, упражнения, фитнес

     Добавить emoji?"

[...]
```

### Flow 3: Editing Existing Category

```
User: "/edit category mia"

Bot: "Редактируем категорию 'мия' 🐕

     Текущее описание:
     'Моя собака, все дела связанные с ней'

     Что хочешь изменить?"

[Описание] [Добавить ключевые слова] [Сменить emoji] [Удалить]

User: [Добавить ключевые слова]

Bot: "Текущие слова: собака, мія, еда, прогулки, ветеринар

     Добавить новые (через запятую):"

User: "кинолог, дрессировка, игрушки"

Bot: "✅ Добавлены слова: кинолог, дрессировка, игрушки

     Теперь задачи с этими словами попадут в 'мия' 🐕"
```

---

## Smart Category Matching Algorithm

```typescript
interface CategoryMatcher {
  matchCategory(
    taskText: string,
    userCategories: CustomCategory[]
  ): TaskCategory;
}

class SmartCategoryMatcher implements CategoryMatcher {

  // Priority: Custom -> Default
  matchCategory(
    taskText: string,
    userCategories: CustomCategory[]
  ): TaskCategory {

    // Step 1: Try custom categories first
    const customMatch = this.matchCustomCategories(taskText, userCategories);
    if (customMatch.confidence > 0.7) {
      return customMatch.category;
    }

    // Step 2: Fall back to default categories
    const defaultMatch = this.matchDefaultCategories(taskText);
    return defaultMatch.category;
  }

  private matchCustomCategories(
    taskText: string,
    categories: CustomCategory[]
  ): CategoryMatch {

    const matches: CategoryMatch[] = [];
    const lowerText = taskText.toLowerCase();

    for (const cat of categories) {
      let score = 0;
      const matchedKeywords: string[] = [];

      // Check keywords (highest weight)
      for (const keyword of cat.keywords) {
        if (lowerText.includes(keyword.toLowerCase())) {
          score += 0.4;
          matchedKeywords.push(keyword);
        }
      }

      // Check category key match
      if (lowerText.includes(cat.categoryKey.toLowerCase())) {
        score += 0.3;
      }

      // Check examples (partial match)
      if (cat.examples) {
        for (const example of cat.examples) {
          const exampleWords = example.toLowerCase().split(' ');
          const matchingWords = exampleWords.filter(w =>
            lowerText.includes(w)
          );
          if (matchingWords.length >= 2) {
            score += 0.2;
          }
        }
      }

      if (score > 0) {
        matches.push({
          category: {
            type: 'custom',
            key: cat.categoryKey,
            displayName: cat.categoryName,
            emoji: cat.emoji
          },
          confidence: Math.min(score, 1.0),
          matchedKeywords,
          matchReason: 'keyword'
        });
      }
    }

    // Return best match
    matches.sort((a, b) => b.confidence - a.confidence);
    return matches[0] || {
      category: { type: 'default', key: 'general', displayName: 'General' },
      confidence: 0,
      matchedKeywords: [],
      matchReason: 'contextual'
    };
  }

  private matchDefaultCategories(taskText: string): CategoryMatch {
    // Keyword-based matching for default categories
    // Similar logic but with predefined keywords for each category

    const patterns = {
      work: ['работа', 'проект', 'код', 'meeting', 'презентация', 'отчет'],
      shopping: ['купить', 'заказать', 'магазин', 'продукты'],
      health: ['врач', 'спорт', 'тренировка', 'здоровье', 'лекарство'],
      // ... etc
    };

    // Match and return best default category
    // Implementation details...
  }
}
```

---

## Best Practices

### 1. Category Naming

**Good category keys:**
- Short: "mia", "startup", "gym"
- Memorable: easy to type
- Unique: not conflicting with defaults

**Avoid:**
- Too generic: "stuff", "things"
- Too long: "my-dog-mia-related-tasks"
- Special chars: "mia's_stuff"

### 2. Writing Good Descriptions

**Good:**
```
"Моя собака Мія - все дела связанные с ней: еда, прогулки, ветеринар, игрушки"
```
- Specific context
- Lists examples
- Clear scope

**Bad:**
```
"собака"
```
- Too vague
- No examples
- Hard for LLM to match

### 3. Keywords Selection

**Auto-extract keywords from description:**
- Use NLP to extract nouns
- Include category key itself
- Include examples if provided

**User can manually add:**
- Synonyms
- Related terms
- Specific names

### 4. Handling Category Conflicts

When task could match multiple categories:

**Priority order:**
1. **Exact keyword match** (highest)
2. **Category key in text**
3. **Multiple keywords match**
4. **Example similarity**
5. **Contextual matching** (lowest)

**Example:**
```
Task: "купить игрушки для мии в зоомагазине"

Could match:
- "mia" (custom) - keywords: мия ✅, игрушки ✅ → confidence: 0.8
- "shopping" (default) - keywords: купить ✅ → confidence: 0.4

Winner: "mia" (higher confidence)
```

---

## UI/UX Considerations

### Visual Category Display

```
┌─ TODAY'S TASKS ──────────────────────┐
│ 🐕 mia (2)                           │
│ ├─ Купить корм для Мії               │
│ └─ Погулять с Мией вечером           │
│                                      │
│ 💼 work (3)                          │
│ ├─ Код ревью PR #234                 │
│ ├─ Встреча с командой в 15:00        │
│ └─ Написать документацию              │
│                                      │
│ 🛒 shopping (1)                      │
│ └─ Купить молоко                     │
└──────────────────────────────────────┘
```

### Category Statistics

```
📊 Weekly Breakdown:

🐕 mia: 12 tasks (18%)
💼 work: 35 tasks (53%)
🛒 shopping: 8 tasks (12%)
🏠 home: 5 tasks (8%)
Other: 6 tasks (9%)

Most active category: work 💼
```

---

## Migration & Backwards Compatibility

### When User Deletes Custom Category

**Option 1: Migrate to default**
```sql
UPDATE tasks
SET category_type = 'default',
    category_key = 'general'
WHERE user_id = ?
  AND category_key = 'deleted_category_key';
```

**Option 2: Keep as archived**
```sql
-- Keep category reference but mark as archived
-- Show in UI as "[Archived] mia"
```

### Exporting Categories

User can export their custom categories config:

```json
{
  "version": "1.0",
  "exported_at": "2025-11-15T10:30:00Z",
  "categories": [
    {
      "key": "mia",
      "name": "Мія",
      "description": "Моя собака...",
      "keywords": ["собака", "мия", ...],
      "emoji": "🐕",
      "color": "#FF5733"
    }
  ]
}
```

Import on new device/account.

---

## Performance Considerations

### Caching Strategy

```typescript
// Cache user's compiled prompt
class PromptCache {
  private cache: Map<string, { prompt: string, timestamp: number }>;

  async getPrompt(userId: string): Promise<string> {
    const cached = this.cache.get(userId);

    // Cache valid for 1 hour or until category change
    if (cached && Date.now() - cached.timestamp < 3600000) {
      return cached.prompt;
    }

    // Regenerate
    const prompt = await buildBrainDumpPrompt(userId);
    this.cache.set(userId, { prompt, timestamp: Date.now() });

    return prompt;
  }

  invalidate(userId: string): void {
    this.cache.delete(userId);
  }
}
```

### Query Optimization

```sql
-- Efficient category matching query
SELECT c.*,
       array_agg(c.keywords) as all_keywords
FROM user_custom_categories c
WHERE c.user_id = ?
  AND c.is_active = true
GROUP BY c.id;
```

---

## Testing Strategy

### Test Cases

1. **No custom categories** - should use only defaults
2. **One custom category** - should prefer it when matched
3. **Multiple custom categories** - should pick best match
4. **Ambiguous matches** - should handle gracefully
5. **Custom + default overlap** - custom should win
6. **Empty keywords** - should fall back to description matching
7. **Non-English text** - should handle Cyrillic, emoji, etc.

### Example Test

```typescript
describe('Category Matching', () => {
  it('should prefer custom category over default', () => {
    const categories = [
      {
        categoryKey: 'mia',
        keywords: ['собака', 'мия', 'корм'],
        // ...
      }
    ];

    const result = matcher.matchCategory(
      'купить корм для мії',
      categories
    );

    expect(result.type).toBe('custom');
    expect(result.key).toBe('mia');
  });
});
```

---

## Future Enhancements

### 1. Smart Keyword Suggestions

```
Bot: "Заметил, что ты часто пишешь 'дрессировка' в задачах про мию.

     Добавить 'дрессировка' в ключевые слова категории 'мия'?"

[Да] [Нет] [Не предлагать больше]
```

### 2. Category Analytics

```
📊 Category Usage Insights:

🐕 mia:
- Most common time: Evening (18:00-20:00)
- Average duration: 30 min
- Completion rate: 95%

💡 Suggestion: Block 18:30 daily for Mia tasks?
```

### 3. Shared Categories (Teams)

For team/family accounts:
- Shared custom categories
- Team-wide consistency
- Role-based category access

### 4. AI-Assisted Category Creation

```
User: "у меня теперь подкаст, надо категорию"

Bot: [AI analyzes user's tasks history]

     "Создаю категорию 'podcast' 🎙️

     Автоматически нашёл похожие задачи:
     - записать эпизод
     - монтаж аудио
     - публикация в Apple Podcasts

     Добавить эти слова в ключевые?"
```

---

End of design document.
