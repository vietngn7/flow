# Brain Dump System - Implementation TODO

## 🎯 Цель проекта

Создать систему для работы с задачами через natural language brain dumps с поддержкой:
- Создания задач из хаотичного текста
- Обновления существующих задач
- Дедупликации
- Queries (поиск/фильтрация)
- Умной валидации и обнаружения зависимостей

**Хранение:** JSON файлы (tasks.json, categories.json)

---

## 🏗️ Архитектура (5 агентов)

```
User Input
    ↓
┌─────────────────────────────────────┐
│  1. ORCHESTRATOR                    │  ← Координатор всего
│     - Классифицирует intent         │
│     - Роутит к агентам              │
└─────────────────────────────────────┘
    ↓
    ├─→ Simple case → прямо к агенту
    │
    └─→ Compound case ↓
         ┌─────────────────────────────┐
         │  2. OPERATION SPLITTER      │  ← Разбивает на операции
         │     - Парсит compound       │
         │     - Определяет intents    │
         └─────────────────────────────┘
              ↓
    ┌─────────┼─────────┬─────────┐
    ▼         ▼         ▼         ▼
┌────────┐ ┌────────┐ ┌────────┐ ┌────────┐
│3. BRAIN│ │4. TASK │ │5. QUERY│ │ DEDUPE │
│  DUMP  │ │ MODIFY │ │ AGENT  │ │ (exist)│
│ PARSER │ │        │ │        │ │        │
└────────┘ └────────┘ └────────┘ └────────┘
```

---

## 📋 TODO List

### ✅ Phase 0: Existing (Done)
- [x] Brain Dump Parser v3 (basic) - `brain-dump-prompt-v3.md`
- [x] Deduplicator - `brain-dump-deduplicator-prompt.md`
- [x] Category System Design - `category-system-design.md`

---

### 🎯 Phase 1: Core Agents (Priority)

#### TODO 1.1: Orchestrator Prompt
**File:** `orchestrator-prompt.md`

**Purpose:** Master coordinator который решает:
- Simple vs compound input?
- Какого агента вызвать?
- Как обработать результаты?

**Input:**
```json
{
  "user_input": "купил молоко, встреча срочная, что на завтра?",
  "existing_tasks": [...],
  "context": {
    "last_created_task_id": "task_123",
    "last_mentioned_task_id": "task_456",
    "session_history": [...]
  }
}
```

**Output:**
```json
{
  "classification": "compound",
  "routing_decision": "use_operation_splitter",
  "reason": "Detected multiple operations: complete, update, query"
}
```

OR for simple case:
```json
{
  "classification": "simple_brain_dump",
  "routing_decision": "brain_dump_parser",
  "reason": "Single task creation detected"
}
```

**Key Requirements:**
- Fast classification (regex + keywords для простых случаев)
- Fallback to LLM для ambiguous
- Return routing decision
- No actual processing (только координация)

**Edge Cases:**
- Empty input → ask user
- Unclear intent → ask for clarification
- Mixed language (русский + английский)

---

#### TODO 1.2: Operation Splitter Prompt
**File:** `operation-splitter-prompt.md`

**Purpose:** Разбивает compound input на отдельные операции

**Input:**
```json
{
  "user_input": "купил молоко, встреча срочная, что на завтра?"
}
```

**Output:**
```json
{
  "operations": [
    {
      "operation_id": "op_1",
      "text": "купил молоко",
      "intent": "complete",
      "confidence": 0.98,
      "indicators": ["past tense verb: купил"]
    },
    {
      "operation_id": "op_2",
      "text": "встреча срочная",
      "intent": "update",
      "confidence": 0.85,
      "indicators": ["modifier: срочная", "no new action verb"]
    },
    {
      "operation_id": "op_3",
      "text": "что на завтра",
      "intent": "query",
      "confidence": 0.95,
      "indicators": ["question word: что"]
    }
  ],
  "metadata": {
    "total_operations": 3,
    "parsing_notes": "Clear separation by commas and intent shifts"
  }
}
```

**Intent Types:**
- `create` - новая задача (инфинитив, "нужно", "надо")
- `update` - изменение (теперь, перенести, изменить)
- `complete` - выполнение (прошедшее время, "готово")
- `query` - вопрос (что, когда, сколько, покажи)
- `delete` - удаление (удалить, убрать)

**Key Requirements:**
- Detect operation boundaries (запятые, "и", "также", "еще")
- Classify intent для каждой операции
- Handle edge cases (nested operations)
- Preserve original text для каждой операции

**Edge Cases:**
- Single operation (return array with 1 item)
- Ambiguous separation ("купить молоко и хлеб" = 1 operation, not 2)
- Context-dependent operations ("это срочно" - что "это"?)

---

#### TODO 1.3: Task Modifier Prompt (CRITICAL!)
**File:** `task-modifier-prompt.md`

**Purpose:** Unified agent для модификации существующих задач:
- Match existing task (с scoring и disambiguation)
- Update attributes
- Mark as completed
- Delete tasks

**Input:**
```json
{
  "operation_type": "update", // or "complete", "delete"
  "user_input": "встреча с андреем срочная",
  "existing_tasks": [
    {
      "id": "task_001",
      "title": "Встреча с Андреем",
      "due_time": "14:00",
      "priority": "medium"
    },
    {
      "id": "task_002",
      "title": "Встреча с командой",
      "due_time": "10:00",
      "priority": "medium"
    }
  ],
  "context": {
    "last_mentioned_task_id": "task_001",
    "session_history": [...]
  }
}
```

**Output (successful match):**
```json
{
  "status": "success",
  "matched_task": {
    "id": "task_001",
    "confidence": 0.92,
    "match_reason": "keywords: встреча, андреем (exact person match)"
  },
  "changes": {
    "priority": "high"
  },
  "previous_values": {
    "priority": "medium"
  },
  "explanation": "Изменил приоритет встречи с Андреем с medium на high"
}
```

**Output (disambiguation needed):**
```json
{
  "status": "disambiguation_needed",
  "candidates": [
    {
      "id": "task_001",
      "title": "Встреча с Андреем",
      "confidence": 0.65,
      "match_reason": "keyword: встреча"
    },
    {
      "id": "task_002",
      "title": "Встреча с командой",
      "confidence": 0.63,
      "match_reason": "keyword: встреча"
    }
  ],
  "question": "Нашел 2 встречи. Какую сделать срочной?",
  "suggested_changes": {
    "priority": "high"
  }
}
```

**Output (no match):**
```json
{
  "status": "no_match",
  "reason": "No task found matching 'несуществующая задача'",
  "suggestion": "Создать новую задачу вместо этого?"
}
```

**Matching Algorithm:**

1. **Keyword matching** (0-0.4 points)
   - Extract keywords from user input
   - Match against task titles
   - Score: % of keywords matched

2. **Context boost** (+0.3 points)
   - last_mentioned_task matches
   - Recent conversation about this task

3. **Specific identifiers** (+0.3 points)
   - Person names: "андреем", "с клиентом"
   - Numbers: "PR #234", "в 14:00"
   - Category: "задача про мию"

4. **Threshold:** 0.8 для auto-apply, иначе ask user

**Update Types:**

**A. Priority:**
```
"срочно", "asap", "важно" → priority: high
"не важно", "потом" → priority: low
```

**B. Date/Time:**
```
"завтра" → due_date: tomorrow
"в 15:00" → due_time: "15:00"
"перенести на понедельник" → due_date: next Monday
```

**C. Category:**
```
"теперь это личное" → category: personal
```

**D. Additive (append):**
```
"добавь что нужно обсудить бюджет" → description += "Обсудить бюджет"
```

**E. Completion:**
```
"купил молоко" → completed: true, completion_time: now
```

**Key Requirements:**
- Robust matching with scoring
- Handle ambiguity gracefully (ask user)
- Support all update types
- Preserve history (before/after values)
- Batch updates ("все на завтра")

**Edge Cases:**
- Multiple candidates with similar scores
- No match found → suggest create new?
- Conflicting updates ("срочно но не важно")
- Batch operations ("все задачи категории X")

---

#### TODO 1.4: Query Agent Prompt
**File:** `query-agent-prompt.md`

**Purpose:** Поиск и фильтрация задач с natural language ответами

**Input:**
```json
{
  "user_query": "что срочного на сегодня?",
  "tasks": [...]
}
```

**Output:**
```json
{
  "query_type": "filter",
  "filters_applied": {
    "priority": "high",
    "due_date": "2025-11-15",
    "completed": false
  },
  "matched_tasks": [
    {"id": "task_001", "title": "Код ревью PR #234"},
    {"id": "task_002", "title": "Встреча с клиентом"}
  ],
  "count": 2,
  "natural_response": "Срочного на сегодня 2 задачи:\n• Код ревью PR #234 (14:00)\n• Встреча с клиентом (16:00)",
  "visualization_data": {
    "total_today": 5,
    "urgent": 2,
    "normal": 3
  }
}
```

**Query Types:**

**A. Filter queries:**
```
"что на сегодня?" → filter: {due_date: today}
"срочные задачи" → filter: {priority: high}
"задачи про мию" → filter: {category: "mia"}
"что не сделано" → filter: {completed: false}
```

**B. Search queries:**
```
"найди задачу про встречу" → search: title contains "встреча"
"задачи с андреем" → search: title/description contains "андреем"
```

**C. Aggregate queries:**
```
"сколько задач?" → count all
"сколько срочных?" → count where priority=high
```

**D. Time-based queries:**
```
"когда встреча?" → find task + return due_date/time
"что просрочено?" → filter: due_date < today AND completed=false
```

**Response Format:**
- Natural language (conversational)
- Structured lists для multiple results
- Include key details (time, priority)
- Suggestions если ничего не найдено

**Key Requirements:**
- Parse natural language queries
- Apply correct filters
- Format readable responses
- Handle zero results gracefully

**Edge Cases:**
- Ambiguous filters ("сегодня" = due_date или created_at?)
- No results → suggest alternatives
- Too many results → summarize + ask to narrow down

---

#### TODO 1.5: Extend Brain Dump Parser
**File:** Update `brain-dump-prompt-v3.md`

**New Features:**

**A. Actionability Validator**

Add to output:
```json
{
  "tasks": [
    {
      "id": "task_001",
      "title": "Подумать о проекте",
      "actionability": {
        "score": 0.3,
        "rating": "low",
        "issues": [
          "Vague action verb: подумать",
          "No measurable outcome",
          "Missing specifics"
        ],
        "suggestions": [
          "Написать список требований для проекта",
          "Создать mind map с идеями",
          "Обсудить scope с командой"
        ]
      },
      ...
    }
  ]
}
```

**Actionability Scoring:**
- HIGH (0.8-1.0): "Позвонить Андрею в 14:00" - clear action, specific, measurable
- MEDIUM (0.5-0.7): "Купить продукты" - clear action, но vague object
- LOW (0.0-0.4): "Подумать о жизни" - vague action, no outcome

**Indicators of LOW actionability:**
- Vague verbs: подумать, посмотреть, разобраться
- Abstract objects: жизнь, будущее, ситуация
- No measurable outcome
- No specific next step

**B. Dependency Detector**

Add to output:
```json
{
  "tasks": [
    {
      "id": "task_001",
      "title": "Написать план проекта",
      "dependencies": []
    },
    {
      "id": "task_002",
      "title": "Начать разработку",
      "dependencies": [
        {
          "depends_on": "task_001",
          "type": "sequential",
          "keyword_detected": "потом",
          "description": "Должна быть выполнена после написания плана"
        }
      ]
    }
  ]
}
```

**Dependency Keywords:**
- Sequential: "сначала X, потом Y", "после X сделать Y"
- Temporal: "когда получу ответ", "после встречи"
- Conditional: "если одобрят, то..."

**Dependency Types:**
- `sequential` - простая последовательность A → B
- `temporal` - зависит от времени события
- `conditional` - зависит от условия (более сложно)
- `resource` - зависит от доступности ресурса

---

### 🔄 Phase 2: Integration & Testing

#### TODO 2.1: JSON Storage Layer
**File:** `storage-handler.js` (or Python)

**Functions needed:**
```javascript
// Read/Write tasks
async function loadTasks()
async function saveTasks(tasks)

// Read/Write categories
async function loadCategories(userId)
async function saveCategory(userId, category)

// Read/Write context
async function loadContext(sessionId)
async function saveContext(sessionId, context)
```

**File Structure:**
```
/data/
  tasks/
    active.json       ← текущие задачи
    completed.json    ← завершенные
    archived.json     ← старые (>30 days)
  categories/
    user_123.json     ← custom categories
  sessions/
    session_abc.json  ← session context
```

---

#### TODO 2.2: Orchestrator Implementation
**File:** `orchestrator.js`

**Core Logic:**
```javascript
async function orchestrate(userInput, context) {
  // 1. Quick classification
  const intent = await classifyIntent(userInput);

  // 2. Route to appropriate agent(s)
  if (intent.type === 'simple') {
    return await routeSimple(intent, userInput, context);
  }

  if (intent.type === 'compound') {
    // Split operations
    const ops = await operationSplitter.split(userInput);

    // Execute each
    const results = [];
    for (const op of ops) {
      const agent = selectAgent(op.intent);
      const result = await agent.execute(op.text, context);

      // If disambiguation needed, pause and ask user
      if (result.status === 'disambiguation_needed') {
        const userChoice = await askUser(result.question, result.candidates);
        result = await agent.executeWithChoice(op.text, userChoice);
      }

      results.push(result);
    }

    return formatResults(results);
  }
}
```

---

#### TODO 2.3: Agent Integration Tests

**Test Cases:**

**Brain Dump Parser:**
- [ ] Simple task creation
- [ ] Multiple tasks in one dump
- [ ] Actionability detection (vague vs clear)
- [ ] Dependency detection (sequential tasks)
- [ ] Custom category matching
- [ ] Date/time parsing

**Task Modifier:**
- [ ] Update priority
- [ ] Update date/time
- [ ] Mark as complete
- [ ] Additive updates (append description)
- [ ] Batch updates ("все на завтра")
- [ ] Disambiguation flow
- [ ] No match handling

**Query Agent:**
- [ ] Filter by date
- [ ] Filter by priority
- [ ] Filter by category
- [ ] Search by keywords
- [ ] Aggregate queries (count)
- [ ] Empty results handling

**Operation Splitter:**
- [ ] Simple single operation
- [ ] Multiple operations (2-3)
- [ ] Complex compound (5+ operations)
- [ ] Ambiguous boundaries

**Orchestrator:**
- [ ] Route simple brain dump
- [ ] Route simple query
- [ ] Route compound operations
- [ ] Handle disambiguation
- [ ] Error handling

---

### 🎨 Phase 3: UX & Polish

#### TODO 3.1: Disambiguation UI
**File:** `disambiguation-ui.md`

**Telegram Bot Format:**
```
Bot: "Нашел 3 встречи. Какую сделать срочной?"

1. 📅 Встреча с Андреем (сегодня 14:00)
2. 👥 Встреча с командой (завтра 10:00)
3. 💼 Встреча с клиентом (пятница 16:00)

[1] [2] [3] [Все] [Отмена]
```

**Inline Keyboard:**
- Number buttons 1-N для выбора
- "Все" для batch operation
- "Отмена" для cancel

---

#### TODO 3.2: Clarification Flow
**File:** `clarification-prompts.md`

**For Low Actionability:**
```
Bot: "Заметил что 'подумать о проекте' довольно vague 🤔

     Что конкретно нужно сделать?

     Например:
     • Написать список требований
     • Создать mind map
     • Обсудить с командой

     Или добавить как есть?"

[Уточнить вручную] [Выбрать вариант] [Добавить как есть]
```

**For Dependencies:**
```
Bot: "Заметил что эти задачи связаны:
     1. Написать план проекта
     2. Начать разработку (после плана)

     Хочешь чтобы я автоматически планировал их последовательно?"

[Да, связать] [Нет, независимо]
```

---

#### TODO 3.3: Response Formatting
**File:** `response-templates.md`

**Success Messages:**
```
✅ Create: "Добавил задачу: {title}"
✅ Update: "Изменил {field} для '{title}'"
✅ Complete: "Отметил как выполненное: {title}"
✅ Query: "{count} задач найдено: ..."
```

**Error Messages:**
```
❌ No match: "Не нашел задачу '{query}'. Создать новую?"
❌ Ambiguous: "Нашел несколько вариантов, уточни:"
⚠️ Low actionability: "Задача слишком vague, уточни:"
```

---

### 📊 Phase 4: Analytics & Optimization

#### TODO 4.1: Usage Analytics
**Track:**
- Intent distribution (create vs update vs query)
- Disambiguation rate (% of ambiguous cases)
- Actionability scores (average per user)
- Most common update types
- Query patterns

#### TODO 4.2: Performance Optimization
**Optimize:**
- Caching для user categories (invalidate on change)
- Batching для multiple LLM calls
- Prompt size reduction
- Use cheaper models для simple routing

---

## 🧪 Testing Strategy

### Unit Tests
- [ ] Each agent prompt with sample inputs
- [ ] Edge cases для каждого агента
- [ ] Malformed input handling

### Integration Tests
- [ ] End-to-end flows (create → update → query)
- [ ] Compound operations
- [ ] Disambiguation flow
- [ ] Context persistence

### User Acceptance Tests
- [ ] 5-10 alpha testers
- [ ] Real-world brain dumps
- [ ] Measure: accuracy, satisfaction, clarity

---

## 📝 Implementation Notes

### Prompt Engineering Best Practices

1. **XML Tags для Claude:**
   - Use `<role>`, `<task>`, `<instructions>`
   - Clear section separation
   - Examples in `<example>` tags

2. **Output Format:**
   - Always JSON (no markdown wrappers)
   - Strict schema validation
   - Include confidence scores

3. **Error Handling:**
   - Graceful degradation
   - Clear error messages
   - Fallback strategies

### Context Management

**Session Context:**
```json
{
  "session_id": "sess_abc",
  "user_id": "user_123",
  "last_created_task_id": "task_456",
  "last_mentioned_task_id": "task_789",
  "conversation_history": [
    {
      "timestamp": "2025-11-15T10:00:00Z",
      "user_input": "купить молоко",
      "operation": "create",
      "task_id": "task_456"
    }
  ],
  "preferences": {
    "timezone": "Europe/Kiev",
    "language": "ru"
  }
}
```

**Update on each interaction:**
- Add to conversation_history
- Update last_mentioned_task_id
- Truncate history (keep last 10 interactions)

### Disambiguation Strategy

**Scoring Threshold:**
- `>= 0.8` - Auto-apply (high confidence)
- `0.5 - 0.8` - Ask user (medium confidence)
- `< 0.5` - No match (suggest alternatives)

**Context Boost:**
- `+0.3` if last_mentioned_task
- `+0.2` if in recent conversation
- `+0.1` if same category as recent tasks

### Actionability Thresholds

- `< 0.5` - Always ask for clarification
- `0.5 - 0.7` - Show suggestions, allow bypass
- `> 0.7` - Auto-accept, no clarification needed

---

## 🚀 Rollout Plan

### Week 1: Core Agents
- Day 1-2: Orchestrator + Operation Splitter
- Day 3-4: Task Modifier
- Day 5: Query Agent
- Day 6-7: Testing & bug fixes

### Week 2: Extensions
- Day 1-2: Brain Dump Parser extensions (actionability + dependencies)
- Day 3-4: Integration testing
- Day 5-7: UX polish + disambiguation flows

### Week 3: Alpha Testing
- Day 1: Deploy to alpha testers (5-10 users)
- Day 2-5: Collect feedback, iterate
- Day 6-7: Bug fixes, improvements

### Week 4: Beta & Launch
- Day 1-3: Beta testing (50+ users)
- Day 4-5: Final polish
- Day 6-7: Full launch

---

## 📞 Questions to Resolve

1. **Model Selection:**
   - Use Claude Opus for all agents?
   - Use Haiku for simple routing?
   - Cost vs accuracy tradeoff?

2. **Error Recovery:**
   - How many retries for LLM failures?
   - Fallback to simpler prompts?
   - User notification strategy?

3. **Rate Limiting:**
   - Max operations per compound input?
   - Prevent abuse (too many updates)?
   - Throttling strategy?

4. **Data Retention:**
   - How long to keep completed tasks?
   - Archive vs delete old tasks?
   - Session context expiration?

5. **Multi-Language:**
   - Support English + Russian only?
   - Add Ukrainian?
   - Mixed-language handling?

---

## 🎯 Success Metrics

### Technical Metrics
- Intent classification accuracy: > 90%
- Task matching accuracy: > 85%
- Disambiguation rate: < 20%
- Response time: < 3 seconds (p95)

### User Metrics
- User satisfaction: > 4.0/5.0
- Daily active users retention: > 60%
- Average tasks created per day: > 5
- Brain dumps per user per day: 2-4

### Business Metrics
- API cost per user per month: < $2
- Error rate: < 5%
- Support tickets: < 0.1 per user per month

---

## 📚 References

**Existing Files:**
- `brain-dump-prompt-v3.md` - Brain dump parser
- `brain-dump-deduplicator-prompt.md` - Deduplication logic
- `category-system-design.md` - Category architecture
- `deduplication-workflow-example.md` - Dedup examples

**Related Research:**
- Natural language task management systems
- Intent classification in conversational AI
- Entity matching and disambiguation
- Dependency detection in NLP

---

**Last Updated:** 2025-11-15
**Status:** Planning Phase
**Next Steps:** Start with TODO 1.1 (Orchestrator Prompt)
