# Brain Dump Category System - Examples & Comparison

## Comparison: Before vs After

### BEFORE (v1) - Only Default Categories

```
User: "купить еду для мии вечером, записать эпизод подкаста, созвон с инвестором по стартапу"

LLM Output:
{
  "tasks": [
    {
      "title": "Купить еду для Мии",
      "category": "shopping",  ❌ Too generic
      "tags": ["покупки", "еда"]
    },
    {
      "title": "Записать эпизод подкаста",
      "category": "work",  ❌ Not specific enough
      "tags": ["подкаст"]
    },
    {
      "title": "Созвон с инвестором по стартапу",
      "category": "work",  ❌ Everything is "work"
      "tags": ["встреча", "инвестор"]
    }
  ]
}
```

**Problems:**
- Everything goes to "work" or "shopping"
- No personalization
- Can't see at a glance what's related to dog, podcast, startup
- Tags help but not enough for organization

---

### AFTER (v2) - Default + Custom Categories

**User config:**
```json
{
  "customCategories": [
    {
      "key": "mia",
      "name": "Мія",
      "description": "Моя собака Мія - все дела связанные с ней",
      "keywords": ["собака", "мия", "мія", "корм", "прогулка"],
      "emoji": "🐕"
    },
    {
      "key": "podcast",
      "name": "Подкаст AI",
      "description": "Мой подкаст про AI - запись, монтаж, публикация",
      "keywords": ["подкаст", "эпизод", "запись", "монтаж"],
      "emoji": "🎙️"
    },
    {
      "key": "startup",
      "name": "Стартап",
      "description": "Мой SaaS стартап - продукт, маркетинг, продажи",
      "keywords": ["стартап", "инвестор", "питч", "продукт"],
      "emoji": "🚀"
    }
  ]
}
```

```
User: "купить еду для мии вечером, записать эпизод подкаста, созвон с инвестором по стартапу"

LLM Output:
{
  "tasks": [
    {
      "title": "Купить еду для Мії",
      "category": "mia",  ✅ Custom category!
      "category_display": "🐕 Мія",
      "tags": ["мия", "покупки", "еда"]
    },
    {
      "title": "Записать эпизод подкаста",
      "category": "podcast",  ✅ Custom category!
      "category_display": "🎙️ Подкаст AI",
      "tags": ["подкаст", "запись", "AI"]
    },
    {
      "title": "Созвон с инвестором по стартапу",
      "category": "startup",  ✅ Custom category!
      "category_display": "🚀 Стартап",
      "tags": ["startup", "инвестор", "встреча"]
    }
  ]
}
```

**Benefits:**
- ✅ Clear visual grouping (emoji + name)
- ✅ Personalized to user's life
- ✅ Easy to see priorities (e.g., all startup tasks together)
- ✅ Better filtering & analytics

---

## Real-World Usage Examples

### Example 1: Pet Owner + Knowledge Worker

**User Profile:** Software engineer with a dog

**Custom Categories:**
```json
[
  {
    "key": "mia",
    "name": "Мія",
    "description": "Моя собака - прогулки, еда, ветеринар, дрессировка",
    "keywords": ["собака", "мия", "мія", "vet", "корм", "прогулка", "кинолог"],
    "emoji": "🐕"
  }
]
```

**Brain Dump:**
```
"гулять с мией утром, код ревью PR #234, купить корм в зоомагазине,
встреча с командой в 15:00, записаться к ветеринару"
```

**Parsed Output:**
```json
{
  "tasks": [
    {
      "title": "Погулять с Мией",
      "category": "mia",  🐕
      "due_time": "09:00",
      "priority": "medium"
    },
    {
      "title": "Код ревью PR #234",
      "category": "work",  💼
      "priority": "medium"
    },
    {
      "title": "Купить корм в зоомагазине",
      "category": "mia",  🐕 (not "shopping"!)
      "priority": "medium"
    },
    {
      "title": "Встреча с командой",
      "category": "work",  💼
      "due_time": "15:00",
      "priority": "high"
    },
    {
      "title": "Записаться к ветеринару",
      "category": "mia",  🐕
      "priority": "low"
    }
  ]
}
```

**Daily View:**
```
┌─ TODAY ──────────────────────────────┐
│ 🐕 Мія (3 tasks)                     │
│ ├─ Погулять утром ⏰ 09:00           │
│ ├─ Купить корм в зоомагазине         │
│ └─ Записаться к ветеринару           │
│                                      │
│ 💼 Work (2 tasks)                    │
│ ├─ Код ревью PR #234                 │
│ └─ Встреча с командой ⏰ 15:00       │
└──────────────────────────────────────┘
```

**Why This Is Better:**
- All dog-related tasks grouped together
- Easy to delegate (e.g., partner can see all "mia" tasks)
- Clear separation: work vs personal pet care

---

### Example 2: Entrepreneur with Side Projects

**User Profile:** Full-time job + side hustle (SaaS) + content creation (podcast)

**Custom Categories:**
```json
[
  {
    "key": "dayjob",
    "name": "Day Job",
    "description": "Основная работа в компании - задачи, встречи, дедлайны",
    "keywords": ["work", "office", "boss", "project", "sprint"],
    "emoji": "💼"
  },
  {
    "key": "startup",
    "name": "SaaS Startup",
    "description": "Мой стартап - разработка продукта, маркетинг, продажи",
    "keywords": ["startup", "стартап", "saas", "product", "landing", "users"],
    "emoji": "🚀"
  },
  {
    "key": "podcast",
    "name": "AI Podcast",
    "description": "Подкаст про AI и машинное обучение - запись, монтаж, продвижение",
    "keywords": ["podcast", "подкаст", "эпизод", "гость", "запись", "youtube"],
    "emoji": "🎙️"
  }
]
```

**Brain Dump:**
```
"дописать фичу для saas лендинга, созвон с боссом в 14:00, записать эпизод с гостем про GPT-5,
код ревью для работы, смонтировать прошлый эпизод, питч инвесторам в пятницу"
```

**Parsed Output:**
```json
{
  "tasks": [
    {
      "title": "Дописать фичу для SaaS лендинга",
      "category": "startup",  🚀
      "priority": "high"
    },
    {
      "title": "Созвон с боссом",
      "category": "dayjob",  💼
      "due_time": "14:00",
      "priority": "high"
    },
    {
      "title": "Записать эпизод с гостем про GPT-5",
      "category": "podcast",  🎙️
      "priority": "medium"
    },
    {
      "title": "Код ревью для работы",
      "category": "dayjob",  💼
      "priority": "medium"
    },
    {
      "title": "Смонтировать прошлый эпизод",
      "category": "podcast",  🎙️
      "priority": "low"
    },
    {
      "title": "Питч инвесторам",
      "category": "startup",  🚀
      "due_date": "2025-11-19",
      "priority": "high"
    }
  ]
}
```

**Daily View:**
```
┌─ TODAY ──────────────────────────────┐
│ 💼 Day Job (2 tasks)                 │
│ ├─ Созвон с боссом ⏰ 14:00 🔴       │
│ └─ Код ревью для работы 🟡           │
│                                      │
│ 🚀 SaaS Startup (2 tasks)            │
│ ├─ Дописать фичу для лендинга 🔴     │
│ └─ Питч инвесторам (Fri) 🔴          │
│                                      │
│ 🎙️ AI Podcast (2 tasks)             │
│ ├─ Записать эпизод про GPT-5 🟡      │
│ └─ Смонтировать прошлый эпизод 🟢    │
└──────────────────────────────────────┘
```

**Why This Is Better:**
- Clear work-life separation
- Easy to see if one area is neglected
- Time blocking by category (e.g., evenings = startup/podcast)
- Analytics: "How much time on startup vs day job?"

---

### Example 3: Parent + Freelancer + Homeowner

**Custom Categories:**
```json
[
  {
    "key": "kids",
    "name": "Дети",
    "description": "Дети - школа, активности, здоровье, развлечения",
    "keywords": ["дети", "школа", "садик", "kids", "сын", "дочка"],
    "emoji": "👶"
  },
  {
    "key": "freelance",
    "name": "Freelance",
    "description": "Фриланс проекты - клиенты, дедлайны, invoices",
    "keywords": ["freelance", "client", "клиент", "invoice", "project"],
    "emoji": "💻"
  },
  {
    "key": "house",
    "name": "Дом",
    "description": "Дом и ремонт - починить, купить для дома, обустройство",
    "keywords": ["дом", "ремонт", "починить", "сантехник", "мебель"],
    "emoji": "🏠"
  }
]
```

**Brain Dump:**
```
"отвезти детей в школу утром, починить кран на кухне, отправить invoice клиенту А,
забрать сына из садика в 17:00, дизайн лендинга для клиента B"
```

**Parsed Output:**
```json
{
  "tasks": [
    {
      "title": "Отвезти детей в школу",
      "category": "kids",  👶
      "due_time": "09:00",
      "priority": "high"
    },
    {
      "title": "Починить кран на кухне",
      "category": "house",  🏠
      "priority": "medium"
    },
    {
      "title": "Отправить invoice клиенту А",
      "category": "freelance",  💻
      "priority": "high"
    },
    {
      "title": "Забрать сына из садика",
      "category": "kids",  👶
      "due_time": "17:00",
      "priority": "high"
    },
    {
      "title": "Дизайн лендинга для клиента B",
      "category": "freelance",  💻
      "priority": "medium"
    }
  ]
}
```

---

## Analytics Examples

### Weekly Category Breakdown

```
📊 Week of Nov 11-17, 2025

Category Distribution:

🚀 Startup:        18 tasks (35%) ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
💼 Day Job:        15 tasks (29%) ━━━━━━━━━━━━━━━━━━━━━━━━━━
🎙️ Podcast:        10 tasks (20%) ━━━━━━━━━━━━━━━━━━━━
🛒 Shopping:        5 tasks (10%) ━━━━━━━━━━
🏥 Health:          3 tasks (6%)  ━━━━━━

Time Spent:
🚀 Startup:        12.5 hours
💼 Day Job:        25 hours (still full-time!)
🎙️ Podcast:        6 hours
🏥 Health:          2 hours (gym)

💡 Insights:
- Day job taking more time than expected
- Startup progress good (12.5h side hustle)
- Consider batching podcast work (currently fragmented)

Recommendations:
✅ Block Saturday mornings for startup deep work
✅ Batch podcast tasks on Sunday
⚠️  Health category low - schedule 3x gym this week
```

### Category-Based Time Blocking

```
Bot: "Заметил паттерн!

     🚀 Startup tasks:
     - Обычно делаешь вечером 19:00-22:00
     - Completion rate: 85%
     - High energy tasks

     💡 Suggestion:
     Закрепить Mon/Wed/Fri 19:00-22:00 для startup?

     [Yes, block it] [Custom schedule] [No thanks]"
```

---

## Prompt Comparison

### BEFORE: Generic Prompt

```
## Category Auto-Detection
Based on context, assign to: work, personal, shopping, health, finance,
home, learning, social, errands, general
```

**Issues:**
- No user context
- Generic categories
- Everything is "work" or "personal"

---

### AFTER: Personalized Prompt

```
## User Context (Dynamic)

**User timezone**: UTC+2
**User profile type**: segmenter

### Custom Categories

User has defined the following custom categories:

- "mia": "Моя собака Мія - все дела связанные с ней (еда, прогулки, ветеринар)"
- "startup": "Мой SaaS стартап - разработка продукта, маркетинг, продажи"
- "podcast": "Подкаст про AI - запись, монтаж, публикация эпизодов"

When categorizing tasks, **FIRST** check if the task matches any custom category
based on:
- Keywords in the custom category description
- Examples provided by user
- Context clues in the brain dump text

If no custom category matches, use default categories.

## Default Categories

Use these if no custom category matches:
- work, personal, shopping, health, finance, home, learning, social, errands, general
```

**Benefits:**
- ✅ Custom categories FIRST
- ✅ User context included
- ✅ Clear priority order
- ✅ Personalized to user's life

---

## Category Overlap Handling

### Scenario: Task Could Match Multiple Categories

**Example:**
```
Task: "купить корм для собаки в зоомагазине"

Could match:
1. "mia" (custom) - keywords: собака ✅, корм ✅
2. "shopping" (default) - keywords: купить ✅, магазин ✅

LLM Decision Logic:
- Custom category "mia" has 2 keyword matches
- Default category "shopping" has 2 keyword matches
- BUT: Custom categories have PRIORITY
- Winner: "mia" 🐕
```

**Why Custom Wins:**
- More specific to user's life
- Better organizational value
- User intent: care about DOG tasks, not generic shopping

**But tags include both contexts:**
```json
{
  "category": "mia",
  "tags": ["мия", "собака", "корм", "покупки", "зоомагазин"]
}
```

---

## Advanced Use Cases

### 1. Shared Categories (Family Account)

```json
{
  "sharedCategories": [
    {
      "key": "family",
      "name": "Семья",
      "description": "Семейные дела - дети, родители, события",
      "sharedWith": ["wife_user_id", "husband_user_id"],
      "emoji": "👨‍👩‍👧"
    }
  ]
}
```

Both spouses see "family" tasks in shared view.

---

### 2. Project-Based Categories (Temporary)

```json
{
  "key": "wedding",
  "name": "Свадьба",
  "description": "Организация свадьбы - площадка, гости, платье, etc",
  "keywords": ["свадьба", "wedding", "venue", "guests"],
  "emoji": "💍",
  "isTemporary": true,
  "archiveAfter": "2025-08-15"  // Auto-archive after wedding date
}
```

After wedding, category auto-archives (tasks stay, but category inactive).

---

### 3. Nested Sub-Categories (Future)

```json
{
  "key": "startup",
  "name": "Startup",
  "subcategories": [
    {
      "key": "startup_product",
      "name": "Product Development",
      "emoji": "⚙️"
    },
    {
      "key": "startup_marketing",
      "name": "Marketing",
      "emoji": "📢"
    },
    {
      "key": "startup_sales",
      "name": "Sales",
      "emoji": "💰"
    }
  ]
}
```

View: `🚀 Startup > 📢 Marketing`

---

## Migration Path for Existing Users

### Step 1: Analyze Existing Tasks

```
Bot: "Заметил, что у тебя часто встречаются задачи со словами 'собака', 'мия'.

     Хочешь создать категорию 'мия' для этих задач?

     Найдено 23 задачи за последний месяц:
     - купить корм для мии
     - гулять с мией
     - ветеринар для собаки
     - ...

     [Создать категорию 'мия'] [Игнорировать] [Другие предложения]"
```

### Step 2: Auto-Recategorize

```
Bot: "Категория 'мия' создана! 🐕

     Хочешь пересортировать старые задачи?

     23 задачи будут перемещены:
     - shopping → mia (18 tasks)
     - personal → mia (5 tasks)

     [Да, пересортировать] [Нет, только новые]"
```

---

## Summary: Key Improvements

| Feature | v1 (Default Only) | v2 (Default + Custom) |
|---------|-------------------|----------------------|
| **Personalization** | ❌ Generic | ✅ User's life context |
| **Visual Grouping** | ❌ Text only | ✅ Emoji + names |
| **Category Accuracy** | ⚠️  ~60% | ✅ ~90%+ |
| **User Control** | ❌ No customization | ✅ Full control |
| **Analytics** | ⚠️  Generic insights | ✅ Personalized patterns |
| **Scalability** | ⚠️  Limited (10 categories) | ✅ Unlimited custom |
| **Migration** | N/A | ✅ Auto-suggest from history |
| **Sharing** | ❌ No | ✅ Family/team categories |
| **Prompt Size** | ~2KB | ~3-5KB (dynamic) |

---

## Implementation Recommendation

**Phase 1: MVP (Week 1-2)**
- ✅ Basic custom category CRUD
- ✅ Updated prompt with `{{CUSTOM_CATEGORIES}}` injection
- ✅ Simple keyword matching
- ✅ UI for creating/editing categories

**Phase 2: Smart Features (Week 3-4)**
- ✅ Auto-suggest categories from user history
- ✅ Confidence scoring for matches
- ✅ Category analytics & insights
- ✅ Export/import category configs

**Phase 3: Advanced (Week 5+)**
- ✅ Shared categories (family/team)
- ✅ Temporary/project-based categories
- ✅ Nested sub-categories
- ✅ AI-powered category optimization

---

End of comparison document.
