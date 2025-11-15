# Brain Dump Prompt v3 - Summary

## ✅ Что сделано

Создана улучшенная версия промпта **v3**, оптимизированная для **Claude 4 Sonnet** на основе best practices.

**Файл:** `brain-dump-prompt-v3.md`

---

## 🎯 Ключевые улучшения

### 1. XML структура вместо Markdown

**v2 (Markdown):**
```markdown
## Parsing Rules

### 1. Date & Time Extraction
**Relative dates:**
- "завтра" → tomorrow
```

**v3 (XML):**
```xml
<parsing_rules>
  <date_time_extraction>
    Relative dates:
    - "завтра" → tomorrow
  </date_time_extraction>
</parsing_rules>
```

**Почему лучше:**
- Claude 4 нативно лучше парсит XML
- Четкая иерархия (вложенность тегов)
- Легче для модели выделить секции

---

### 2. Explicit Instructions вместо Generic

**v2:**
```markdown
## Execution Process
Follow this process for every brain dump:
1. Read the entire brain dump text
2. Identify distinct tasks
...
```

**v3:**
```xml
<instructions>
For every brain dump input, execute the following steps in order:

1. Read the entire input text carefully

2. Identify distinct tasks:
   - Apply task_splitting rules
   - Look for separators: commas, conjunctions, context shifts

3. For EACH task identified:
   a) Check CUSTOM categories first:
      - Does task text contain keywords from any custom category?
      - Does it match examples provided?
   ...
</instructions>
```

**Почему лучше:**
- Более конкретные инструкции (не просто "identify tasks", а "look for separators")
- Явные sub-steps (a, b, c)
- Меньше ambiguity

---

### 3. Reasoning в Examples

**v2:**
```json
{
  "output": {...}
}
```

**v3:**
```xml
<example>
  <output>...</output>

  <reasoning>
  Task 1: "купить еду для мии" contains keyword "мии" which matches
  custom category "mia". Even though it could also match "shopping",
  we prefer custom.

  Dates calculated from current_date (2025-11-15).
  </reasoning>
</example>
```

**Почему лучше:**
- Модель учится ПОЧЕМУ мы делаем такие решения
- Chain-of-thought в примерах
- Улучшает reasoning модели при парсинге

---

### 4. Hierarchical Structure

**v2:** Плоская структура с Markdown заголовками
```
# Title
## Section
### Subsection
```

**v3:** Вложенная XML иерархия
```xml
<role>...</role>
<context>
  <current_date>...</current_date>
  <custom_categories>...</custom_categories>
</context>
<task>...</task>
<parsing_rules>
  <date_time_extraction>...</date_time_extraction>
  <priority_detection>...</priority_detection>
</parsing_rules>
```

**Почему лучше:**
- Четкая граница между секциями
- Легче для модели найти нужную информацию
- Структура соответствует Claude 4 best practices

---

## 📊 Сравнение

| Метрика | v2 | v3 | Изменение |
|---------|----|----|-----------|
| Размер | 15.7 KB | 16.9 KB | +7% |
| Строки | 497 | 500 | +3 |
| Формат | Markdown | XML | ✅ Claude 4 optimized |
| Instructions | Generic | Explicit | ✅ Точнее |
| Examples | Basic | With reasoning | ✅ Лучше обучение |

---

## ✅ Core Фичи (все сохранены)

### 1. Dynamic Context
- ✅ `{{CURRENT_DATE}}`
- ✅ `{{USER_TIMEZONE}}`
- ✅ `{{USER_PROFILE}}`
- ✅ `{{CUSTOM_CATEGORIES}}`

### 2. Category System
- ✅ Custom categories FIRST priority
- ✅ Default categories fallback
- ✅ Keyword matching
- ✅ Example matching

### 3. Parsing Logic
- ✅ Date/time extraction (русский язык)
- ✅ Priority detection (HIGH/MEDIUM/LOW/NONE)
- ✅ Emotional state (frustrated/anxious/excited/neutral)
- ✅ Task splitting logic
- ✅ Duration estimation
- ✅ Title formatting

### 4. JSON Schema
- ✅ Точная структура: tasks + metadata
- ✅ Все поля: id, title, description, due_date, due_time, priority, category, tags, estimated_duration, emotional_state

### 5. Examples
- ✅ 3 детальных примера
- ✅ Input → Output mapping
- ✅ **NEW:** Reasoning для каждого

### 6. Output Requirements
- ✅ Только JSON, без markdown
- ✅ 11 CRITICAL правил
- ✅ Validation constraints

### 7. Special Cases
- ✅ No custom categories
- ✅ Multiple matches
- ✅ Ambiguous matches
- ✅ Ambiguous dates

---

## 🚀 Рекомендация

**Используй v3** для production:

**Почему:**
1. ✅ Оптимизирован для Claude 4 Sonnet (XML > Markdown)
2. ✅ Более explicit инструкции → меньше ошибок
3. ✅ Reasoning в examples → лучшее обучение модели
4. ✅ Четкая структура → легче читать и поддерживать
5. ✅ Все core фичи сохранены на 100%
6. ✅ Всего +7% размера (1.2 KB) - приемлемо

**Trade-off:**
- Немного длиннее (+1.2 KB)
- XML может быть менее привычен для редактирования

**Итог:** v3 > v2 для Claude 4

---

## 📝 Структура v3

```
<role>
  Brain Dump Parser definition
</role>

<context>
  Template variables + user config
</context>

<task>
  What to do
</task>

<output_schema>
  JSON structure
</output_schema>

<parsing_rules>
  <date_time_extraction>...</date_time_extraction>
  <priority_detection>...</priority_detection>
  <emotional_state_detection>...</emotional_state_detection>
  <category_matching>...</category_matching>
  <task_splitting>...</task_splitting>
  <duration_estimation>...</duration_estimation>
  <title_formatting>...</title_formatting>
</parsing_rules>

<instructions>
  Explicit step-by-step
</instructions>

<examples>
  <example name="...">
    <user_context>...</user_context>
    <input>...</input>
    <output>...</output>
    <reasoning>Why these choices</reasoning>
  </example>
</examples>

<output_requirements>
  CRITICAL constraints
</output_requirements>

<special_cases>
  Edge cases handling
</special_cases>
```

---

## 🧪 Следующие шаги

1. **Протестировать v3** с реальными brain dumps
2. **A/B тест** v2 vs v3 на точность категоризации
3. **Измерить:**
   - Category matching accuracy
   - Date parsing accuracy
   - Emotional detection precision
   - JSON validity rate

4. **Если v3 лучше:** использовать в production
5. **Если равны:** можно оставить v2 (проще редактировать)

---

## 💡 Дополнительные улучшения (future)

Если захочешь ещё улучшить:

1. **Few-shot examples в context**
   - Добавить 1-2 примера прямо в user message
   - Показать модели recent successful parses

2. **Chain-of-thought prompting**
   - Просить модель сначала вывести reasoning
   - Потом финальный JSON

3. **Structured output API** (Claude 4+)
   - Использовать native JSON schema validation
   - Гарантированный valid JSON output

4. **Prompt caching**
   - Кешировать статичные части промпта
   - Динамически инжектить только {{VARIABLES}}

---

## 📁 Файлы

```
/home/user/flow/
├── brain-dump-prompt-v2.md          ← Markdown версия (15.7 KB)
├── brain-dump-prompt-v3.md          ← XML версия (16.9 KB) ⭐ Рекомендуется
└── PROMPT-V3-SUMMARY.md             ← Этот файл
```

---

**Готово к тестированию!** 🎯
