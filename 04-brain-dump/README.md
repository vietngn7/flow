# Brain Dump Structure - Summary & Next Steps

## 📋 Что сделано

### 1. Основной промпт для brain dump

**Файл:** `brain-dump-prompt-v3.md`

**Ключевые возможности:**
- ✅ Динамическая секция `{{CUSTOM_CATEGORIES}}` для пользовательских категорий
- ✅ Приоритет: сначала custom категории, потом default
- ✅ Детекция эмоционального состояния (frustrated, anxious, excited)
- ✅ Улучшенная логика парсинга дат и времени
- ✅ Подробные примеры с кастомными категориями
- ✅ XML-оптимизированная структура для Claude 4
- ✅ Четкая структура JSON output

**Основной принцип:**
```
Custom categories FIRST → Default categories SECOND
```

### 1.1. Дедупликатор для brain dump

**Файл:** `brain-dump-deduplicator-prompt.md`

**Проблема:** В течение дня пользователь делает несколько brain dump сессий и часто дублирует задачи.

**Решение:** Промпт анализирует массив задач и:
- 🔍 Находит точные дубликаты ("Купить молоко" x2)
- 🧠 Определяет семантические дубликаты ("Купить молоко" vs "Сходить за молоком")
- 🔄 Объединяет частичные дубликаты ("Позвонить Андрею" → "Позвонить Андрею по поводу проекта")
- ⚡ Разрешает конфликты (разные даты/приоритеты для одной задачи)
- 📈 Отслеживает эволюцию задач ("Подумать о проекте" → "Начать работу над проектом")

**Output:**
```json
{
  "consolidated_tasks": [...],      // Чистый список без дубликатов
  "duplicates_removed": [...],      // Что было объединено
  "conflicts_resolved": [...],      // Какие конфликты разрешены
  "kept_separate": [...],           // Похожие, но оставлены отдельно
  "metadata": {...}
}
```

---

### 2. Архитектура системы категорий

**Файл:** `category-system-design.md`

**Что включает:**
- 📊 **Database schema** - таблицы для хранения custom категорий
- 💻 **TypeScript types** - типы для категорий и матчинга
- 🔌 **API design** - CRUD операции для категорий
- 🧠 **Smart matching algorithm** - алгоритм выбора категории
- 🎨 **UI/UX flows** - пользовательские сценарии
- ⚡ **Performance** - кеширование и оптимизация
- 🧪 **Testing strategy** - тест-кейсы

**Ключевые компоненты:**

```typescript
interface CustomCategory {
  categoryKey: string;        // "mia", "startup"
  categoryName: string;        // "Мія", "Стартап"
  description: string;         // "Моя собака..."
  keywords: string[];          // ["собака", "мія", ...]
  examples?: string[];
  emoji?: string;              // "🐕"
}
```

---

### 3. Примеры и сравнение

**Файл:** `category-examples-comparison.md`

**Что включает:**
- 🔄 **Before/After сравнение** - v1 vs v2
- 💡 **Real-world примеры** - 3 типа пользователей
- 📊 **Analytics примеры** - недельная статистика
- 🎯 **Category overlap handling** - что делать при конфликтах
- 🚀 **Advanced use cases** - shared categories, временные категории
- 📈 **Migration path** - как перевести существующих пользователей

**Ключевые примеры:**

**Пример 1:** Pet owner (собака Мія)
**Пример 2:** Entrepreneur (day job + startup + podcast)
**Пример 3:** Parent + freelancer + homeowner

---

## 🎯 Структура категорий

### Default Categories (10 базовых)

```
work       💼 - Работа, проекты, встречи
personal   👤 - Личные дела, саморазвитие
shopping   🛒 - Покупки, заказы
health     🏥 - Здоровье, спорт, врачи
finance    💰 - Счета, платежи, финансы
home       🏠 - Дом, ремонт, уборка
learning   📚 - Обучение, курсы, чтение
social     👥 - Встречи, друзья, события
errands    📋 - Быстрые поручения, звонки
general    📌 - Всё остальное
```

### Custom Categories (пользовательские)

**Примеры:**
```
mia        🐕 - "Моя собака Мія - все дела связанные с ней"
startup    🚀 - "Мой SaaS стартап - продукт, маркетинг, продажи"
podcast    🎙️ - "Подкаст про AI - запись, монтаж, публикация"
kids       👶 - "Дети - школа, активности, здоровье"
freelance  💻 - "Фриланс проекты - клиенты, дедлайны"
```

---

## 🔧 Как это работает

### 1. Пользователь создаёт категорию

```
User: "хочу категорию для собаки"

Bot: "Как назовём? (короткое слово)"
User: "мія"

Bot: "Опиши категорию в одном предложении:"
User: "моя собака, все дела с ней - еда, прогулки, ветеринар"

Bot: "✅ Категория 'мія' создана! 🐕"
```

### 2. Система генерирует промпт

```typescript
function buildPrompt(userId) {
  const customCategories = getUserCategories(userId);

  // Inject into prompt template
  const prompt = basePrompt
    .replace('{{CUSTOM_CATEGORIES}}', formatCategories(customCategories))
    .replace('{{CURRENT_DATE}}', getCurrentDate())
    .replace('{{USER_TIMEZONE}}', getUserTimezone());

  return prompt;
}
```

**Result:**
```
### Custom Categories

User has defined the following custom categories:

- "mia": "Моя собака Мія - все дела связанные с ней (еда, прогулки, ветеринар)"
- "startup": "Мой SaaS стартап - разработка продукта, маркетинг, продажи"

When categorizing tasks, **FIRST** check if the task matches any custom category...
```

### 3. Brain dump парсится с учётом custom категорий

```
User: "купить корм для мії, код ревью, записать подкаст"

LLM:
1. Checks custom categories first:
   - "купить корм для мії" → matches "mia" (keyword: мія ✅)
   - "записать подкаст" → matches "podcast" (keyword: подкаст ✅)
   - "код ревью" → no custom match

2. Falls back to default:
   - "код ревью" → matches "work" (default)

Output:
- Task 1: category = "mia" 🐕
- Task 2: category = "work" 💼
- Task 3: category = "podcast" 🎙️
```

---

## 📊 Сравнение: v1 vs v2

| Aspect | v1 (только default) | v2 (default + custom) |
|--------|---------------------|----------------------|
| **Категории** | 10 фиксированных | 10 default + unlimited custom |
| **Персонализация** | ❌ | ✅ |
| **Точность** | ~60% | ~90%+ |
| **Визуальная группировка** | Текст | Emoji + названия |
| **Аналитика** | Базовая | Персонализированная |
| **User control** | Нет | Полный контроль |

---

## 🚀 Рекомендуемый план внедрения

### Phase 1: MVP (Week 1-2)

**Backend:**
- [ ] Добавить таблицу `user_custom_categories` в БД
- [ ] CRUD API для категорий
- [ ] Функция генерации динамического промпта
- [ ] Кеширование промптов (инвалидация при изменении категорий)

**Frontend (Telegram Bot):**
- [ ] Команда `/categories` - список категорий
- [ ] Команда `/add_category` - создать новую
- [ ] Команда `/edit_category` - редактировать
- [ ] Inline keyboard для выбора emoji

**Brain Dump:**
- [ ] Заменить статический промпт на динамический
- [ ] Тестирование парсинга с custom категориями

**Testing:**
- [ ] Unit tests для category matching
- [ ] Integration tests с LLM
- [ ] 5-10 alpha testers

**Success metrics:**
- Category matching accuracy > 85%
- User creates 2+ custom categories on average
- Response time < 3s (with caching)

---

### Phase 2: Smart Features (Week 3-4)

**Features:**
- [ ] Auto-suggest categories from user's task history
- [ ] Keyword extraction from description (NLP)
- [ ] Category analytics (weekly breakdown)
- [ ] Export/import category configs (JSON)

**Example:**
```
Bot: "Заметил 15 задач про 'подкаст' за месяц.
     Создать категорию 'podcast'?"
```

---

### Phase 3: Advanced (Week 5+)

**Features:**
- [ ] Shared categories (family/team accounts)
- [ ] Temporary categories (auto-archive after date)
- [ ] Category-based time blocking suggestions
- [ ] Nested sub-categories (optional)

---

## 💾 Database Migration

```sql
-- Migration: Add custom categories table

CREATE TABLE user_custom_categories (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  category_key VARCHAR(50) NOT NULL,
  category_name VARCHAR(100) NOT NULL,
  description TEXT NOT NULL,
  keywords TEXT[] DEFAULT '{}',
  examples TEXT[] DEFAULT '{}',
  emoji VARCHAR(10),
  color VARCHAR(7),
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW(),
  is_active BOOLEAN DEFAULT TRUE,

  CONSTRAINT unique_user_category UNIQUE(user_id, category_key)
);

CREATE INDEX idx_user_categories ON user_custom_categories(user_id, is_active);
CREATE INDEX idx_keywords ON user_custom_categories USING GIN(keywords);

-- Update tasks table
ALTER TABLE tasks ADD COLUMN category_type VARCHAR(10) DEFAULT 'default';
ALTER TABLE tasks ADD COLUMN category_display_name VARCHAR(100);

-- Backfill existing tasks
UPDATE tasks SET category_type = 'default', category_display_name = category;
```

---

## 🧪 Testing Checklist

### Unit Tests

- [ ] Category matching logic
  - [ ] Custom category keyword match
  - [ ] Default category fallback
  - [ ] Multiple keyword match
  - [ ] Confidence scoring
- [ ] Prompt generation
  - [ ] With 0 custom categories
  - [ ] With 1 custom category
  - [ ] With 10+ custom categories
- [ ] CRUD operations
  - [ ] Create category
  - [ ] Update category
  - [ ] Delete category (soft/hard)
  - [ ] Duplicate key handling

### Integration Tests

- [ ] Brain dump with custom categories
  - [ ] All tasks match custom
  - [ ] Mixed custom + default
  - [ ] No custom matches (all default)
- [ ] Category changes
  - [ ] Create category → tasks auto-categorized
  - [ ] Update keywords → re-categorization
  - [ ] Delete category → tasks migrated
- [ ] Performance
  - [ ] Prompt generation < 100ms
  - [ ] Brain dump parsing < 3s
  - [ ] Cache hit rate > 80%

### User Acceptance Tests

- [ ] Alpha testers create 2+ categories
- [ ] Category matching accuracy > 85%
- [ ] Users understand category system
- [ ] No confusion between custom/default
- [ ] Emoji display works in Telegram

---

## 📝 Документация для пользователей

### Quick Start Guide

```markdown
# Кастомные категории - Quick Start

## Зачем нужны кастомные категории?

Дефолтные категории (work, personal, shopping) слишком общие.
Кастомные категории помогают организовать задачи под ТВОЮ жизнь.

Примеры:
- 🐕 Собака - все дела про питомца
- 🚀 Стартап - side hustle проект
- 👶 Дети - школа, активности
- 💻 Freelance - клиенты, проекты

## Как создать категорию?

1. Напиши: "хочу категорию для..."
2. Бот спросит название (короткое слово)
3. Опиши категорию в одном предложении
4. Выбери emoji (опционально)
5. Готово! ✅

## Пример

User: "хочу категорию для собаки"
Bot: "Название?"
User: "мия"
Bot: "Описание?"
User: "моя собака, все дела с ней"
Bot: "✅ Создана! 🐕"

Теперь:
"купить корм для мии" → 🐕 мия (не shopping!)
"гулять с мией" → 🐕 мия
```

---

## 🎓 Best Practices

### Хорошие категории

✅ **Специфичные:**
- "mia" (собака) вместо "pets"
- "startup" (конкретный проект) вместо "side_hustle"

✅ **С хорошим описанием:**
- "Моя собака Мія - все дела связанные с ней: еда, прогулки, ветеринар"
- НЕ просто "собака"

✅ **С ключевыми словами:**
- Включить синонимы: "собака", "мія", "мия", "vet"
- Специфичные термины: "корм", "прогулка", "кинолог"

### Плохие категории

❌ **Слишком общие:**
- "stuff", "things", "other"

❌ **Слишком узкие:**
- "buy-food-for-dog" (это задача, не категория)

❌ **Без описания:**
- Название: "xyz"
- Описание: "xyz" ← не помогает LLM

---

## 🔗 Файлы в этой папке

```
/home/user/flow/04-brain-dump/
├── brain-dump-prompt-v3.md             ← Основной промпт (XML-оптимизированный)
├── brain-dump-deduplicator-prompt.md   ← Дедупликация и объединение задач
├── deduplication-workflow-example.md   ← Примеры работы дедупликатора
├── category-system-design.md           ← Архитектура системы категорий
├── category-examples-comparison.md     ← Примеры и сравнение
├── IMPLEMENTATION-TODO.md              ← 🎯 TODO для реализации полной системы
└── README.md                           ← Этот файл (summary)
```

**🎯 Для реализации системы смотри:** `IMPLEMENTATION-TODO.md`

---

## ❓ FAQ

### Q: Сколько категорий можно создать?

A: Технически unlimited, но рекомендуем 3-7 кастомных категорий.
Больше 10 → трудно управлять.

### Q: Можно ли изменить описание категории?

A: Да! `/edit_category <name>` → изменить описание, ключевые слова, emoji.

### Q: Что если задача подходит под несколько категорий?

A: LLM выберет НАИБОЛЕЕ СПЕЦИФИЧНУЮ категорию.
Custom категории имеют приоритет над default.

### Q: Можно ли удалить категорию?

A: Да. Существующие задачи переместятся в "general" (или можно выбрать другую).

### Q: Как поделиться категориями с семьёй?

A: (Phase 3) Shared categories для team/family аккаунтов.

### Q: Категории синхронизируются между устройствами?

A: Да, всё хранится на сервере. Любое устройство → одни категории.

---

## 🎯 Следующие шаги

1. **Review** - прочитать все 3 документа
2. **Discuss** - обсудить приоритеты (что делать сначала)
3. **Design** - database schema + API endpoints
4. **Implement** - Phase 1 MVP
5. **Test** - alpha testing с 5-10 пользователями
6. **Iterate** - на основе фидбека

---

## 📞 Обсуждение

**Вопросы для обсуждения:**

1. **Приоритет:** Делать сразу Phase 1 (MVP) или сначала другие фичи?

2. **UX:** Как лучше онбординг для категорий?
   - Auto-suggest при первом запуске?
   - Или пользователь сам добавляет когда захочет?

3. **Промпт:** Текущий промпт v2 ~3-5KB. Это ОК для LLM? (GPT-4/Claude)
   - Альтернатива: использовать system prompt для категорий

4. **Emoji:** Обязательны или опционально?
   - Влияние на UX

5. **Keywords:** Auto-extract из description или пользователь вручную?
   - NLP для extraction?

6. **Migration:** Как предложить существующим пользователям создать категории?
   - Analyze их historical tasks → suggest categories

**Мой рекомендация:**
- Start with Phase 1 MVP
- Auto-suggest categories from user history (важно!)
- Emoji optional but recommended (большой UX impact)
- Auto-extract keywords + allow manual edits

Готов обсуждать! 🚀
