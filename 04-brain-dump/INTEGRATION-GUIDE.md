# Brain Dump System Integration Guide

## Overview

This document describes how to integrate the 6 agent prompts into a working task management system.

**Agents:**
1. **Orchestrator** - Entry point, classifies intent and routes to appropriate agent
2. **Brain Dump Parser** - Converts brain dumps into structured tasks
3. **Task Modifier** - Updates, completes, or deletes existing tasks
4. **Query Agent** - Searches and filters tasks
5. **Operation Splitter** - Breaks compound operations into individual operations
6. **Deduplicator** - Identifies and merges duplicate tasks

---

## System Architecture

```
User Input
    ↓
┌─────────────────┐
│  Orchestrator   │ ← Entry point, classifies intent
└────────┬────────┘
         ↓
    ┌────┴─────┬──────────┬─────────────┐
    ↓          ↓          ↓             ↓
┌────────┐ ┌────────┐ ┌────────┐ ┌──────────┐
│ Brain  │ │ Task   │ │ Query  │ │Operation │
│ Dump   │ │Modifier│ │ Agent  │ │ Splitter │
│ Parser │ └────────┘ └────────┘ └──────────┘
└───┬────┘                              ↓
    ↓                            (splits → Orchestrator)
┌────────────┐
│Deduplicator│ ← Called periodically
└────────────┘
```

---

## Data Flow Examples

### 1. Create Flow (Brain Dump)

**User Input:** "купить молоко, позвонить маме"

```javascript
// 1. Generate session ID for this brain dump
const sessionId = generateUUID(); // e.g., "session_abc123"

// 2. Prepare context
const context = {
  current_date: "2025-11-16",
  current_time: "10:30",
  current_day_of_week: "Saturday",
  timezone: "Europe/Kiev",
  user_custom_categories: ["mia", "startup"],
  session_id: sessionId,
  last_mentioned_task_id: null,
  has_existing_tasks: true,
  recent_operations: []
};

// 3. Call Orchestrator to classify
const routing = await orchestrator.classify({
  user_input: "купить молоко, позвонить маме",
  context: context
});
// Returns: { intent: "create", routing_decision: "brain_dump_parser", confidence: 0.95 }

// 4. Route to Brain Dump Parser
const result = await brainDumpParser.parse({
  user_input: "купить молоко, позвонить маме",
  context: context
});

// Returns:
// {
//   "tasks": [
//     {
//       "id": "task_001",
//       "title": "Купить молоко",
//       "due_date": null,
//       "priority": "medium",
//       "category": "🛒 shopping",
//       "completed": false,
//       "created_at": "2025-11-16T10:30:00+02:00",
//       "source_session": "session_abc123"
//     },
//     {
//       "id": "task_002",
//       "title": "Позвонить маме",
//       "due_date": null,
//       "priority": "medium",
//       "category": "👥 social",
//       "completed": false,
//       "created_at": "2025-11-16T10:30:00+02:00",
//       "source_session": "session_abc123"
//     }
//   ],
//   "metadata": { "total_tasks": 2 }
// }

// 5. Save tasks to database
for (const task of result.tasks) {
  await db.createTask(task);
}

// 6. Update context
context.last_mentioned_task_id = result.tasks[result.tasks.length - 1].id; // "task_002"
context.has_existing_tasks = true;
context.recent_operations.push({
  timestamp: new Date().toISOString(),
  operation_type: "create",
  user_input: "купить молоко, позвонить маме"
});

// 7. Check if deduplication needed (every 5 brain dumps)
brainDumpCounter++;
if (brainDumpCounter >= 5) {
  await runDeduplication();
  brainDumpCounter = 0;
}

// 8. Return to user
return {
  success: true,
  message: "✓ Создал 2 задачи",
  tasks: result.tasks
};
```

---

### 2. Update Flow (Task Modifier)

**User Input:** "встреча срочная"

```javascript
// 1. Orchestrator classifies
const routing = await orchestrator.classify({
  user_input: "встреча срочная",
  context: context
});
// Returns: { intent: "update", routing_decision: "task_modifier" }

// 2. Get existing tasks from database
const existingTasks = await db.getAllTasks({ completed: false });

// 3. Call Task Modifier
const result = await taskModifier.modify({
  user_input: "встреча срочная",
  existing_tasks: existingTasks,
  context: context
});

// Returns:
// {
//   "status": "success",
//   "matched_task": {
//     "id": "task_003",
//     "title": "Встреча с Андреем",
//     "match_score": 0.85
//   },
//   "changes": {
//     "priority": "high"
//   },
//   "previous_values": {
//     "priority": "medium"
//   },
//   "explanation": "Изменил приоритет встречи с Андреем на high"
// }

// 4. Apply changes to database
if (result.status === "success") {
  const task = await db.getTask(result.matched_task.id);

  // Apply changes
  Object.assign(task, result.changes);

  // Save
  await db.updateTask(task.id, task);

  // Update context
  context.last_mentioned_task_id = task.id;

  // Return to user
  return {
    success: true,
    message: `✓ ${result.explanation}`,
    task: task
  };
}

// 5. Handle disambiguation if needed
if (result.status === "disambiguation_needed") {
  return {
    needsChoice: true,
    question: result.question,
    candidates: result.candidates
  };
}
```

---

### 3. Query Flow

**User Input:** "что на сегодня?"

```javascript
// 1. Orchestrator classifies
const routing = await orchestrator.classify({
  user_input: "что на сегодня?",
  context: context
});
// Returns: { intent: "query", routing_decision: "query_agent" }

// 2. Get all tasks
const tasks = await db.getAllTasks();

// 3. Call Query Agent
const result = await queryAgent.query({
  user_input: "что на сегодня?",
  tasks: tasks,
  context: context
});

// Returns:
// {
//   "query_type": "filter",
//   "filters_applied": {
//     "due_date": "2025-11-16",
//     "completed": false
//   },
//   "matched_tasks": [
//     { "id": "task_001", "title": "Код ревью", "due_time": "14:00" },
//     { "id": "task_002", "title": "Встреча", "due_time": "16:00" }
//   ],
//   "count": 2,
//   "natural_response": "На сегодня 2 задачи:\n• Код ревью (14:00)\n• Встреча (16:00)"
// }

// 4. Display to user
return {
  message: result.natural_response,
  tasks: result.matched_tasks
};
```

---

### 4. Compound Flow (Operation Splitter)

**User Input:** "купил молоко, встреча срочная, что на завтра?"

```javascript
// 1. Orchestrator detects compound
const routing = await orchestrator.classify({
  user_input: "купил молоко, встреча срочная, что на завтра?",
  context: context
});
// Returns: { intent: "mixed", routing_decision: "operation_splitter" }

// 2. Call Operation Splitter
const splitResult = await operationSplitter.split({
  user_input: "купил молоко, встреча срочная, что на завтра?",
  context: context
});

// Returns:
// {
//   "operations": [
//     { "operation_id": "op_1", "text": "купил молоко", "intent": "complete" },
//     { "operation_id": "op_2", "text": "встреча срочная", "intent": "update" },
//     { "operation_id": "op_3", "text": "что на завтра?", "intent": "query" }
//   ]
// }

// 3. Execute each operation sequentially
const results = [];

for (const operation of splitResult.operations) {
  // Route each operation through Orchestrator
  const agent = selectAgentByIntent(operation.intent);
  const result = await agent.execute(operation.text, context);

  results.push(result);

  // Update context after each operation
  updateContext(context, result);
}

// 4. Return aggregated results
return {
  success: true,
  operations: results.length,
  details: results
};
```

---

## Context Management

### Context Structure

```javascript
const context = {
  // Date/time info (update on each request)
  current_date: "YYYY-MM-DD",          // e.g., "2025-11-16"
  current_time: "HH:MM",                // e.g., "14:30"
  current_day_of_week: "Saturday",      // Monday-Sunday
  timezone: "Europe/Kiev",

  // User config (load once, cache)
  user_custom_categories: ["mia", "startup"], // From database

  // Session tracking (regenerate per brain dump)
  session_id: "uuid",                   // New UUID per brain dump

  // Conversation state (maintain across requests)
  last_mentioned_task_id: "task_002",   // Updated after create/update
  has_existing_tasks: true,             // Updated after create/delete

  // History (rolling window)
  recent_operations: [                  // Keep last 5 only
    {
      timestamp: "2025-11-16T10:30:00+02:00",
      operation_type: "create",
      user_input: "купить молоко"
    }
  ]
};
```

### Context Update Rules

| After Agent Call | Context Updates |
|------------------|-----------------|
| **Brain Dump Parser** | • Set `last_mentioned_task_id` to last created task<br>• Generate new `session_id` for next dump<br>• Set `has_existing_tasks = true` |
| **Task Modifier** | • Set `last_mentioned_task_id` to modified task |
| **Query Agent** | • Optionally update `last_mentioned_task_id` if single result |
| **Any Agent** | • Append to `recent_operations` (keep last 5) |

### Context Initialization

```javascript
// On new conversation
async function initializeContext() {
  return {
    current_date: getCurrentDate(),
    current_time: getCurrentTime(),
    current_day_of_week: getDayOfWeek(),
    timezone: getUserTimezone(),
    user_custom_categories: await db.getCustomCategories(),
    session_id: generateUUID(),
    last_mentioned_task_id: null,
    has_existing_tasks: await db.hasAnyTasks(),
    recent_operations: []
  };
}
```

---

## Session Management

### What is a Session?

A **session** = single brain dump interaction (input → task creation).

```
User Input: "купить молоко, позвонить маме"
    ↓
Session ID generated: "session_abc123"
    ↓
Brain Dump Parser creates tasks:
  - task_001: source_session = "session_abc123"
  - task_002: source_session = "session_abc123"
    ↓
Session ends (tasks created)
```

### Session Lifecycle

```javascript
// 1. Before brain dump - generate new session ID
if (routing.intent === "create") {
  context.session_id = generateUUID();
}

// 2. Brain Dump Parser uses session_id
const tasks = await brainDumpParser.parse(userInput, context);

// 3. Each task tagged with source_session
tasks.forEach(task => {
  console.log(task.source_session); // "session_abc123"
});

// 4. After brain dump completes
// session_id stays in context until next brain dump
// (when new UUID is generated)
```

### Why Sessions Matter

Sessions enable deduplication:

```javascript
// User creates task in Session 1
Session 1: task_001 { title: "Купить молоко", source_session: "session_abc" }

// User forgets and creates again in Session 2
Session 2: task_002 { title: "Купить молоко", source_session: "session_def" }

// Deduplicator sees different sessions → likely duplicate!
// Merges task_002 into task_001
```

---

## Deduplication Strategy

### Recommended Approach

**MVP:** Run every 5 brain dumps

```javascript
let brainDumpCounter = 0;

async function afterBrainDump() {
  brainDumpCounter++;

  if (brainDumpCounter >= 5) {
    // Get tasks from last 5 sessions
    const last5Sessions = getLastNSessionIds(5);
    const tasks = await db.getTasksBySessionIds(last5Sessions);

    // Run deduplicator
    const result = await deduplicator.deduplicate({ tasks, context });

    // Apply merges
    for (const merge of result.duplicates_merged) {
      await db.mergeTask(merge.duplicate_id, merge.keep_id);
    }

    brainDumpCounter = 0;
  }
}
```

**Production:** Combine strategies

1. Every 5 brain dumps (real-time cleanup)
2. Daily at 23:00 (full cleanup)
3. Manual `/deduplicate` command

```javascript
// Combined implementation
class DeduplicationManager {
  constructor() {
    this.counter = 0;
  }

  async afterBrainDump() {
    this.counter++;
    if (this.counter >= 5) {
      await this.run('periodic');
      this.counter = 0;
    }
  }

  async run(trigger) {
    const tasks = await db.getAllTasks({ completed: false });
    const result = await deduplicator.deduplicate({ tasks, context });

    if (result.duplicates_merged.length > 0) {
      await applyDeduplication(result);

      if (trigger === 'manual') {
        return `✓ Объединил ${result.duplicates_merged.length} дубликатов`;
      }
    }
  }
}

// Daily batch
cron.schedule('0 23 * * *', async () => {
  await deduplicationManager.run('daily');
});

// Manual command
commands.register('/deduplicate', async () => {
  return await deduplicationManager.run('manual');
});
```

---

## Complete Integration Example

```javascript
// Main conversation loop
async function processUserInput(userInput, context) {
  try {
    // 1. Orchestrator classifies
    const routing = await orchestrator.classify({
      user_input: userInput,
      context: context
    });

    console.log(`Intent: ${routing.intent}, Routing: ${routing.routing_decision}`);

    // 2. Route to appropriate agent
    let result;

    switch (routing.routing_decision) {
      case "brain_dump_parser":
        // Generate new session for this brain dump
        context.session_id = generateUUID();

        result = await brainDumpParser.parse({
          user_input: userInput,
          context: context
        });

        // Save tasks
        for (const task of result.tasks) {
          await db.createTask(task);
        }

        // Update context
        context.last_mentioned_task_id = result.tasks[result.tasks.length - 1].id;
        context.has_existing_tasks = true;

        // Check deduplication
        await deduplicationManager.afterBrainDump();

        // Response
        return {
          message: `✓ Создал ${result.tasks.length} ${pluralize('задачу', result.tasks.length)}`,
          tasks: result.tasks
        };

      case "task_modifier":
        const existingTasks = await db.getAllTasks({ completed: false });

        result = await taskModifier.modify({
          user_input: userInput,
          existing_tasks: existingTasks,
          context: context
        });

        if (result.status === "success") {
          // Apply changes
          const task = await db.getTask(result.matched_task.id);
          Object.assign(task, result.changes);
          await db.updateTask(task.id, task);

          // Update context
          context.last_mentioned_task_id = task.id;

          return {
            message: `✓ ${result.explanation}`,
            task: task
          };
        }

        if (result.status === "disambiguation_needed") {
          return {
            needsChoice: true,
            question: result.question,
            candidates: result.candidates
          };
        }

        return {
          error: true,
          message: result.reason || "Задача не найдена"
        };

      case "query_agent":
        const tasks = await db.getAllTasks();

        result = await queryAgent.query({
          user_input: userInput,
          tasks: tasks,
          context: context
        });

        return {
          message: result.natural_response,
          tasks: result.matched_tasks,
          count: result.count
        };

      case "operation_splitter":
        const operations = await operationSplitter.split({
          user_input: userInput,
          context: context
        });

        // Execute each operation
        const results = [];
        for (const op of operations.operations) {
          const opResult = await processUserInput(op.text, context);
          results.push(opResult);
        }

        return {
          message: `✓ Выполнил ${operations.operations.length} операций`,
          operations: results
        };

      default:
        return {
          error: true,
          message: "Не понял запрос"
        };
    }

  } catch (error) {
    console.error('Error processing input:', error);
    return {
      error: true,
      message: "Произошла ошибка при обработке"
    };

  } finally {
    // 3. Update recent operations (always)
    context.recent_operations.push({
      timestamp: new Date().toISOString(),
      operation_type: routing?.intent || 'unknown',
      user_input: userInput
    });

    // Keep only last 5
    context.recent_operations = context.recent_operations.slice(-5);
  }
}
```

---

## Error Handling

### Agent Failures

```javascript
try {
  const result = await agent.execute(input, context);
} catch (error) {
  if (error.type === 'MALFORMED_JSON') {
    // Retry with schema reminder
    return await agent.execute(input, context, { includeSchemaReminder: true });
  }

  if (error.type === 'TIMEOUT') {
    // Use faster model
    return await agent.execute(input, context, { model: 'haiku' });
  }

  // Log and return friendly error
  logger.error('Agent failed', { agent: agent.name, error, input });
  return {
    error: true,
    message: "Извини, что-то пошло не так. Попробуй еще раз?"
  };
}
```

### Disambiguation Loops

```javascript
let retryCount = 0;

while (result.status === 'disambiguation_needed' && retryCount < 3) {
  const userChoice = await askUser({
    question: result.question,
    options: result.candidates
  });

  if (!userChoice) {
    return "Окей, отменил операцию";
  }

  // Retry with specific task
  result = await taskModifier.modify({
    user_input: `${userInput} (task: ${userChoice.id})`,
    existing_tasks: existingTasks,
    context: context
  });

  retryCount++;
}

if (retryCount >= 3) {
  return "Не смог понять какую задачу. Попробуй указать точнее?";
}
```

---

## Performance Considerations

### Model Selection

| Agent | Recommended Model | Latency Target |
|-------|-------------------|----------------|
| Orchestrator | Haiku | <100ms |
| Brain Dump Parser | Sonnet | <2s |
| Task Modifier | Sonnet | <1s |
| Query Agent | Sonnet | <150ms |
| Operation Splitter | Sonnet | <200ms |
| Deduplicator | Sonnet (background) | <3s |

### Caching

```javascript
// Cache Orchestrator classifications
const classificationCache = new LRU({ max: 100, ttl: 60000 });

async function classifyWithCache(userInput, context) {
  const key = `${userInput}:${context.has_existing_tasks}`;

  if (classificationCache.has(key)) {
    return classificationCache.get(key);
  }

  const result = await orchestrator.classify({ user_input: userInput, context });
  classificationCache.set(key, result);
  return result;
}
```

### Batching

```javascript
// Batch multiple queries
async function batchQuery(queries, tasks, context) {
  return await Promise.all(
    queries.map(q => queryAgent.query({ user_input: q, tasks, context }))
  );
}
```

---

## Testing

### Integration Test Example

```javascript
describe('Brain Dump Integration', () => {
  let context;

  beforeEach(async () => {
    context = await initializeContext();
    await db.clearAllTasks();
  });

  it('should create, update, then query task', async () => {
    // 1. Create task
    const createResult = await processUserInput("купить молоко завтра", context);
    expect(createResult.tasks).toHaveLength(1);
    expect(createResult.tasks[0].title).toBe('Купить молоко');

    const taskId = createResult.tasks[0].id;

    // 2. Update task
    const updateResult = await processUserInput("это срочно", context);
    expect(updateResult.task.priority).toBe('high');
    expect(updateResult.task.id).toBe(taskId);

    // 3. Query task
    const queryResult = await processUserInput("срочные задачи", context);
    expect(queryResult.count).toBe(1);
    expect(queryResult.tasks[0].id).toBe(taskId);
  });

  it('should handle compound operations', async () => {
    // Create initial task
    await processUserInput("купить молоко", context);

    // Compound operation
    const result = await processUserInput(
      "купил молоко, встреча срочная, что на сегодня?",
      context
    );

    expect(result.operations).toBe(3);
  });
});
```

---

## Deployment Checklist

- [ ] All 6 prompts loaded into LLM service
- [ ] Context initialization logic implemented
- [ ] Context update logic after each agent call
- [ ] Session ID generation on each brain dump
- [ ] Database with all required task fields:
  - [ ] `completed` (boolean)
  - [ ] `created_at` (ISO timestamp)
  - [ ] `source_session` (UUID string)
- [ ] Deduplication strategy selected and implemented
- [ ] Error handling and retries in place
- [ ] Logging and monitoring configured
- [ ] Integration tests passing
- [ ] Performance targets met

---

## Troubleshooting

### Common Issues

**Issue:** Tasks created without `source_session`
- **Cause:** Forgot to pass `session_id` in context
- **Fix:** Ensure `context.session_id = generateUUID()` before brain dump

**Issue:** Query Agent fails with missing `current_day_of_week`
- **Cause:** Context missing optional field
- **Fix:** Query Agent now calculates from `current_date` if missing

**Issue:** Task Modifier returns changes but task not updated
- **Cause:** Forgot to apply changes to database
- **Fix:** Always do `Object.assign(task, result.changes)` and `db.updateTask()`

**Issue:** Deduplicator never runs
- **Cause:** No invocation strategy implemented
- **Fix:** Implement periodic trigger (every 5 brain dumps or daily)

---

**Version:** 1.0
**Last Updated:** 2025-11-16
**Compatible with:** All prompts v1.0+
**Questions?** Check individual prompt files for detailed behavior
