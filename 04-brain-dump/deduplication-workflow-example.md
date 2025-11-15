# Brain Dump Deduplication - Workflow Example

## 📅 Сценарий использования

Пользователь делает brain dump несколько раз в течение дня. К концу дня накапливается много задач с дубликатами.

---

## 🔄 Workflow

### Step 1: Brain Dump сессии в течение дня

**Утро (08:00):**
```
купить молоко, подумать о новом проекте, позвонить андрею
```

**Output (brain-dump-prompt-v3):**
```json
{
  "tasks": [
    {
      "id": "task_morning_1",
      "title": "Купить молоко",
      "category": "shopping",
      "due_date": "2025-11-15",
      "priority": "medium",
      "source_session": "morning_session"
    },
    {
      "id": "task_morning_2",
      "title": "Подумать о новом проекте",
      "category": "work",
      "priority": "low",
      "source_session": "morning_session"
    },
    {
      "id": "task_morning_3",
      "title": "Позвонить Андрею",
      "category": "work",
      "priority": "medium",
      "source_session": "morning_session"
    }
  ]
}
```

---

**День (14:00):**
```
купить молоко и хлеб вечером, написать план для проекта, позвонить андрею по поводу встречи завтра в 14:00
```

**Output (brain-dump-prompt-v3):**
```json
{
  "tasks": [
    {
      "id": "task_afternoon_1",
      "title": "Купить молоко и хлеб",
      "category": "shopping",
      "due_date": "2025-11-15",
      "due_time": "18:00",
      "priority": "medium",
      "source_session": "afternoon_session"
    },
    {
      "id": "task_afternoon_2",
      "title": "Написать план для проекта",
      "category": "work",
      "due_date": null,
      "priority": "medium",
      "source_session": "afternoon_session"
    },
    {
      "id": "task_afternoon_3",
      "title": "Позвонить Андрею по поводу встречи",
      "category": "work",
      "due_date": "2025-11-16",
      "due_time": "14:00",
      "priority": "high",
      "source_session": "afternoon_session"
    }
  ]
}
```

---

**Вечер (19:00):**
```
срочно начать работу над проектом завтра утром в 9, купить хлеб если ещё не купил
```

**Output (brain-dump-prompt-v3):**
```json
{
  "tasks": [
    {
      "id": "task_evening_1",
      "title": "Начать работу над проектом",
      "category": "work",
      "due_date": "2025-11-16",
      "due_time": "09:00",
      "priority": "high",
      "emotional_state": "anxious",
      "source_session": "evening_session"
    },
    {
      "id": "task_evening_2",
      "title": "Купить хлеб",
      "category": "shopping",
      "due_date": "2025-11-15",
      "priority": "medium",
      "source_session": "evening_session"
    }
  ]
}
```

---

### Step 2: Дедупликация в конце дня

**Все задачи за день (8 total):**
```json
{
  "tasks": [
    // Morning
    {"id": "task_morning_1", "title": "Купить молоко", ...},
    {"id": "task_morning_2", "title": "Подумать о новом проекте", ...},
    {"id": "task_morning_3", "title": "Позвонить Андрею", ...},

    // Afternoon
    {"id": "task_afternoon_1", "title": "Купить молоко и хлеб", ...},
    {"id": "task_afternoon_2", "title": "Написать план для проекта", ...},
    {"id": "task_afternoon_3", "title": "Позвонить Андрею по поводу встречи", ...},

    // Evening
    {"id": "task_evening_1", "title": "Начать работу над проектом", ...},
    {"id": "task_evening_2", "title": "Купить хлеб", ...}
  ]
}
```

---

**Запускаем deduplicator:**

**Output (brain-dump-deduplicator-prompt):**
```json
{
  "consolidated_tasks": [
    {
      "id": "task_afternoon_1",
      "title": "Купить молоко и хлеб",
      "category": "shopping",
      "due_date": "2025-11-15",
      "due_time": "18:00",
      "priority": "medium",
      "merged_from": ["task_morning_1", "task_afternoon_1", "task_evening_2"],
      "merge_reason": "Shopping task evolved: молоко → молоко и хлеб. task_evening_2 (хлеб) is subset."
    },
    {
      "id": "task_evening_1",
      "title": "Начать работу над проектом",
      "category": "work",
      "due_date": "2025-11-16",
      "due_time": "09:00",
      "priority": "high",
      "emotional_state": "anxious",
      "merged_from": ["task_morning_2", "task_afternoon_2", "task_evening_1"],
      "merge_reason": "Task evolved: thinking → planning → action. Kept most specific (evening) version."
    },
    {
      "id": "task_afternoon_3",
      "title": "Позвонить Андрею по поводу встречи",
      "category": "work",
      "due_date": "2025-11-16",
      "due_time": "14:00",
      "priority": "high",
      "merged_from": ["task_morning_3", "task_afternoon_3"],
      "merge_reason": "Partial duplicate - afternoon version added context about meeting."
    }
  ],

  "duplicates_removed": [
    {
      "removed_id": "task_morning_1",
      "kept_id": "task_afternoon_1",
      "reason": "partial duplicate - afternoon version more complete",
      "confidence": 0.80
    },
    {
      "removed_id": "task_evening_2",
      "kept_id": "task_afternoon_1",
      "reason": "subset - хлеб already in молоко и хлеб",
      "confidence": 0.85
    },
    {
      "removed_id": "task_morning_2",
      "kept_id": "task_evening_1",
      "reason": "evolved task - from thinking to action",
      "confidence": 0.65
    },
    {
      "removed_id": "task_afternoon_2",
      "kept_id": "task_evening_1",
      "reason": "evolved task - from planning to implementation",
      "confidence": 0.70
    },
    {
      "removed_id": "task_morning_3",
      "kept_id": "task_afternoon_3",
      "reason": "partial duplicate - afternoon added context",
      "confidence": 0.85
    }
  ],

  "conflicts_resolved": [
    {
      "task_id": "task_evening_1",
      "conflict_type": "priority",
      "original_values": ["low", "medium", "high"],
      "resolved_value": "high",
      "resolution_strategy": "took highest priority"
    }
  ],

  "kept_separate": [],

  "metadata": {
    "total_input_tasks": 8,
    "total_output_tasks": 3,
    "duplicates_merged": 5,
    "conflicts_resolved": 1,
    "processing_notes": "Detected clear task evolution pattern for project work throughout the day. Shopping tasks consolidated efficiently. All conflicts resolved automatically."
  }
}
```

---

## 📊 Результат

### До дедупликации: 8 задач
```
SHOPPING:
- Купить молоко
- Купить молоко и хлеб
- Купить хлеб

WORK:
- Подумать о новом проекте
- Написать план для проекта
- Начать работу над проектом
- Позвонить Андрею
- Позвонить Андрею по поводу встречи
```

### После дедупликации: 3 задачи
```
SHOPPING:
✅ Купить молоко и хлеб (вечером 18:00)

WORK:
✅ Начать работу над проектом (завтра 09:00) [HIGH PRIORITY]
✅ Позвонить Андрею по поводу встречи (завтра 14:00)
```

**Результат:** 62% reduction, 0 потерянной информации ✨

---

## 🎯 Когда запускать дедупликатор?

### Option 1: End-of-day batch
```
Вечером (22:00):
Bot: "Сегодня было 3 brain dump сессии, нашёл 5 дубликатов.
     Хочешь почистить список задач?"

[Да, почистить] [Показать что объединится] [Нет, оставить как есть]
```

### Option 2: After each dump (live)
```
После каждого brain dump:
- Brain dump parser → добавляет задачи
- Deduplicator → проверяет на дубликаты с существующими
- Если найден дубликат (confidence > 0.8):
  Bot: "Это похоже на задачу 'Купить молоко', которую ты добавил утром.
       Объединить?"
  [Да] [Нет, это отдельная задача]
```

### Option 3: Manual trigger
```
User: "/dedupe" или "почистить задачи"

Bot: "Анализирую задачи..."
     "Нашёл 5 возможных дубликатов. Объединить?"
```

**Рекомендация:** Option 1 (end-of-day) + Option 3 (manual)
- Не раздражает пользователя в течение дня
- Даёт контроль

---

## 🔍 Edge Cases

### Case 1: Recurring vs Duplicate

**Input:**
```json
[
  {
    "title": "Погулять с собакой",
    "due_date": "2025-11-15",
    "due_time": "18:00"
  },
  {
    "title": "Погулять с собакой",
    "due_date": "2025-11-16",
    "due_time": "18:00"
  }
]
```

**Output:**
```json
{
  "consolidated_tasks": [
    {"title": "Погулять с собакой", "due_date": "2025-11-15", ...},
    {"title": "Погулять с собакой", "due_date": "2025-11-16", ...}
  ],
  "kept_separate": [
    {
      "task_ids": ["task_1", "task_2"],
      "similarity": 1.0,
      "reason": "Identical tasks but different dates - likely recurring, kept separate"
    }
  ]
}
```

**Decision:** ✅ Keep separate (different dates = different instances)

---

### Case 2: Ambiguous Similarity

**Input:**
```json
[
  {
    "title": "Позвонить Андрею",
    "description": "По поводу проекта"
  },
  {
    "title": "Позвонить Андрею",
    "description": "Поздравить с днём рождения"
  }
]
```

**Output:**
```json
{
  "consolidated_tasks": [
    {"title": "Позвонить Андрею", "description": "По поводу проекта", ...},
    {"title": "Позвонить Андрею", "description": "Поздравить с днём рождения", ...}
  ],
  "kept_separate": [
    {
      "task_ids": ["task_1", "task_2"],
      "similarity": 0.6,
      "reason": "Same person, same action, but completely different contexts - kept separate"
    }
  ]
}
```

**Decision:** ✅ Keep separate (different contexts)

---

### Case 3: Conflicting Times

**Input:**
```json
[
  {
    "title": "Встреча с командой",
    "due_date": "2025-11-16",
    "due_time": "14:00"
  },
  {
    "title": "Встреча с командой",
    "due_date": "2025-11-16",
    "due_time": "15:00"
  }
]
```

**Output:**
```json
{
  "consolidated_tasks": [
    {
      "title": "Встреча с командой",
      "due_date": "2025-11-16",
      "due_time": "14:00",
      "merged_from": ["task_1", "task_2"],
      "merge_reason": "Same meeting mentioned twice with different times"
    }
  ],
  "conflicts_resolved": [
    {
      "task_id": "merged_task",
      "conflict_type": "time",
      "original_values": ["14:00", "15:00"],
      "resolved_value": "14:00",
      "resolution_strategy": "took earliest time - user should verify!"
    }
  ],
  "metadata": {
    "processing_notes": "⚠️ Time conflict detected (14:00 vs 15:00). Kept earliest. User should verify correct meeting time."
  }
}
```

**Decision:** ⚠️ Merge but FLAG for user review

---

## 💡 UI/UX для дедупликации

### Notification после дедупликации:

```
📊 Дедупликация завершена

До: 8 задач
После: 3 задачи

Объединено:
• "Купить молоко" → "Купить молоко и хлеб"
• "Подумать о проекте" → "Начать работу над проектом"
• "Позвонить Андрею" → "Позвонить Андрею по поводу встречи"

⚠️ 1 конфликт:
Встреча с командой: выбрано 14:00 (было также 15:00)
[Изменить на 15:00]

[Принять] [Отменить] [Показать детали]
```

---

## 🧪 Testing

### Test Case 1: Simple Exact Duplicate
```javascript
test('merges exact duplicates', () => {
  const input = [
    { title: 'Купить молоко', category: 'shopping' },
    { title: 'купить молоко', category: 'shopping' }
  ];

  const result = deduplicator(input);

  expect(result.consolidated_tasks).toHaveLength(1);
  expect(result.duplicates_removed).toHaveLength(1);
  expect(result.duplicates_removed[0].confidence).toBe(1.0);
});
```

### Test Case 2: Semantic Duplicate
```javascript
test('merges semantic duplicates', () => {
  const input = [
    { title: 'Купить молоко' },
    { title: 'Сходить за молоком' }
  ];

  const result = deduplicator(input);

  expect(result.consolidated_tasks).toHaveLength(1);
  expect(result.duplicates_removed[0].reason).toBe('semantic duplicate');
  expect(result.duplicates_removed[0].confidence).toBeGreaterThan(0.8);
});
```

### Test Case 3: Keep Separate (Different Objects)
```javascript
test('keeps separate tasks with different objects', () => {
  const input = [
    { title: 'Позвонить Андрею' },
    { title: 'Позвонить Сергею' }
  ];

  const result = deduplicator(input);

  expect(result.consolidated_tasks).toHaveLength(2);
  expect(result.kept_separate).toHaveLength(1);
  expect(result.kept_separate[0].reason).toContain('Different people');
});
```

---

## 🚀 Implementation Plan

### Phase 1: Basic Deduplication
- [ ] Implement exact duplicate detection (confidence: 1.0)
- [ ] Implement basic title similarity (Levenshtein distance)
- [ ] Handle simple conflicts (priority, date)
- [ ] JSON output validation

### Phase 2: Smart Deduplication
- [ ] Semantic similarity using embeddings (OpenAI ada-002 or similar)
- [ ] Evolved task detection (vague → specific)
- [ ] Complex conflict resolution
- [ ] User review for ambiguous cases

### Phase 3: UX Integration
- [ ] End-of-day deduplication workflow
- [ ] Conflict review UI
- [ ] Undo/rollback functionality
- [ ] Analytics (how many duplicates per user)

---

**Next Steps:**
1. Review промпт `brain-dump-deduplicator-prompt.md`
2. Test с реальными данными
3. Integrate в pipeline после brain dump parser
4. Собрать фидбек от пользователей

🎯 Цель: Уменьшить task list на 40-60% без потери информации!
