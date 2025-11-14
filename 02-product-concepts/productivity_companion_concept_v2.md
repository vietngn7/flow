# 🎯 Productivity Companion v2 - Полная концепция

## Ключевые принципы
- **LLM как помощник, НЕ автопилот** - человек принимает решения
- **Научная база** - каждая фича основана на peer-reviewed research
- **Персонализация** - система адаптируется под стиль пользователя
- **Минимальный friction** - кнопки вместо текста, быстрые протоколы

---

## 🧭 Onboarding: Определение стиля пользователя

**Проблема**: Разные люди работают по-разному (segmenters vs integrators)

**Решение: 5-минутный quiz при первом запуске**

```
Bot: "Привет! Я помогу тебе быть продуктивным без выгорания.
     Сначала узнаю твой стиль работы."

┌─ ВОПРОС 1/5 ────────────────┐
│ Тебе звонит семья в рабочее │
│ время. Как себя чувствуешь? │
│                             │
│ [😤 Раздражен - это мешает] │
│ [😊 Норм, могу переключиться]│
└─────────────────────────────┘

[После 5 вопросов]

Bot: "Ты SEGMENTER!

     Тебе важны:
     ✅ Четкие границы work/life
     ✅ Фиксированные временные блоки
     ✅ Минимум прерываний

     Я настрою систему под это."

[Sounds good] [Tell me more]
```

**Два профиля:**

### Segmenter (Разделитель):
- Strict time blocks
- Technology restrictions вечером
- Physical separation workspace
- Prefer temporal/physical tactics

### Integrator (Интегратор):
- Flexible time windows
- Work-life blending OK
- Focus на psychological detachment (навык!)
- Communication tactics > rigid schedules

**Научная база:**
- Misfit между preference и system = burnout
- Integrators выигрывают БОЛЬШЕ от mindfulness training (g=0.47 vs g=0.28)

---

## 📱 Core Features

### 1. Brain Dump Assistant (Enhanced)

```
User: "/start_day" или просто пишет задачи

User: "надо презу сделать, позвонить маме, купить молоко,
       разобраться с багом в проде, встреча с командой в 3,
       я уже заебался с этим багом блять"
                                    ↑ эмоциональные слова

LLM категоризирует:
├─ Work: Преза, Баг, Встреча
├─ Personal: Звонок маме, Молоко
├─ Urgent: Баг в проде (высокий приоритет)
├─ Time-bound: Встреча в 15:00
└─ ⚠️ Emotional state: Фрустрация detected

Bot: "Вижу 5 задач + встреча.

     ⚠️ Похоже ты в стрессе из-за бага.
     Хочешь сначала 3-минутный recovery протокол?"

[Yes, need recovery] [No, let's plan] [Tell me more]
```

**NEW**: Детекция эмоционального состояния → предложение dysregulation protocol

---

### 2. Emotional Dysregulation Protocol (NEW!)

**Триггеры:**
- Эмоциональные слова в сообщениях
- Несколько /stuck подряд
- Частые /pause без /resume
- Пользователь сам пишет "/tilt" или "/stressed"

**Quick Recovery (3 минуты):**

```
Bot: "3-минутный recovery. Поехали!"

┌─ STEP 1: Box Breathing (90 sec) ─┐
│ Вдох - 4 сек                     │
│ Держи - 4 сек                    │
│ Выдох - 4 сек                    │
│ Держи - 4 сек                    │
│                                  │
│ 6 циклов                         │
│ [Start Timer 90sec]              │
└──────────────────────────────────┘

[Done]

┌─ STEP 2: 5-4-3-2-1 Grounding ────┐
│ Назови вслух или про себя:       │
│ 5 вещей, которые ВИДИШЬ          │
│ 4 вещи, которые СЛЫШИШЬ          │
│ 3 вещи, которые можешь ПОТРОГАТЬ │
│ 2 ЗАПАХА                         │
│ 1 ВКУС                           │
│                                  │
│ [Done]                           │
└──────────────────────────────────┘

Bot: "Как сейчас? (1-10)"

[1-3: Still bad] [4-7: Better] [8-10: Good]

Если Still bad:
"Окей. Может стоит взять 5-min walk?
 Nature exposure снижает cortisol на 6%.

 Или переключиться на простую задачу на 15мин?"

[Walk] [Simple task] [Continue planning]
```

**Deep Recovery (5 минут):**

```
Если выбирает longer protocol:

1. Box breathing (90 sec)
2. Cognitive Reappraisal (2 min):

   Bot: "Что говорит внутренний голос про эту ситуацию?"

   User: "я облажался, всё сломал"

   Bot: "Это ОЩУЩЕНИЕ или ФАКТ?

        Факт: Баг в проде, требует фикса
        Ощущение: 'я failure'

        Альтернатива:
        'Это сложная ситуация И я могу работать над решением.
         Баги случаются, важно как я отреагирую.'

        Попробуй повторить альтернативу."

3. Physical release (2 min):
   "Встань и сделай 20 jumping jacks
    или пройдись 2 минуты.

    Это снижает cortisol и повышает дофамин."

    [Start Timer 2min]
```

**Научная база:**
- Box breathing: g=-0.35 stress reduction, работает за 5 минут
- Cognitive reappraisal: dlPFC modulates amygdala (r=-.18 to -.22)
- Physical movement: metabolizes stress hormones

---

### 3. Top-3 Selection + Implementation Intentions

**Цель**: Минимальный friction + IF-THEN планирование (d=0.65!)

```
После brain dump (или recovery):

┌─ TOP-3 QUICK SELECT ────────┐
│ Тапни 3 самые важные:       │
│                             │
│ [ ] Презентация             │
│ [ ] Баг в проде             │
│ [ ] Позвонить маме          │
│ [ ] Купить молоко           │
│ [ ] Встреча (уже в 15:00)   │
└─────────────────────────────┘

User: [выбирает Преза, Баг, Звонок]

Bot: "Топ-3 выбраны! Теперь типы задач."

┌─ TASK TYPES & ENERGY ────────┐
│ 1. Презентация:              │
│    Type: 🎯 Execution        │
│    Energy: 🔴 HIGH (creative)│
│                              │
│ 2. Баг в проде:              │
│    Type: 🔬 Exploration      │
│    Energy: 🟡 MEDIUM (debug) │
│                              │
│ 3. Звонок маме:              │
│    Type: 🎯 Execution        │
│    Energy: 🟢 LOW (routine)  │
└──────────────────────────────┘

Bot: "Когда у тебя обычно пик энергии?"

[Утро 8-12] [День 12-16] [Вечер 16-20]

User: [Утро]

Bot: "Отлично! Дальше создадим IF-THEN планы
     для каждой задачи. Это увеличит выполнение на 65%."
```

**IF-THEN Builder для каждой задачи:**

```
Задача: Презентация

Bot: "IF-THEN план = автоматический триггер для начала.

     ЕСЛИ [конкретный момент], ТО [первое действие]

     Давай для презы:"

┌─ IF-THEN BUILDER ────────────┐
│ ЕСЛИ:                        │
│ (время + место + событие)    │
│                              │
│ Шаблоны:                     │
│ [9:00 утра, сел за комп]     │
│ [После стендапа, за столом]  │
│ [Закрыл Slack, открыл ноут]  │
│ [Custom...]                  │
└──────────────────────────────┘

User: [тапает "9:00 утра, сел за комп"]

Bot: "ТО - первое физическое действие?"

┌─ THEN ACTION ────────────────┐
│ [Открываю Google Slides]     │
│ [Пишу заголовок презы]       │
│ [Читаю заметки к презе]      │
│ [Custom...]                  │
└──────────────────────────────┘

User: "открываю slides"

Bot: "Твой IF-THEN план:

     'ЕСЛИ 9:00 утра и я сел за комп,
      ТО открываю Google Slides для презы'

     Прорепетируй мысленно 1 раз - это усиливает эффект.
     Представь: завтра 9:00, садишься, открываешь Slides."

[Done] [Change plan]

Bot: "Отлично! Осталось 2 задачи."
```

**Для скучных задач - Temptation Bundling:**

```
Задача: Баг в проде (debugging - может быть скучным)

Bot: "Debugging может быть скучным. Хочешь Temptation Bundle?"

[What's that?] [Yes] [No]

Если Yes:

Bot: "Ты можешь слушать/смотреть что-то ТОЛЬКО во время этой задачи.

     Что любишь?"

Quick replies:
[🎧 Подкасты] [🎵 Музыка] [📺 YouTube] [Custom]

User: "подкасты про футбол"

Bot: "Отлично! ПРАВИЛО:
     Подкасты про футбол → ТОЛЬКО во время debugging бага.

     Важно: строгая привязка = ключ успеха (+51% выполнения).
     Если слушаешь в другое время, эффект пропадает.

     Готов попробовать?"

[Yes, bundle it] [No thanks]

IF-THEN план с bundling:
"ЕСЛИ начинаю debugging бага,
 ТО включаю подкаст про футбол И открываю код"
```

**Научная база:**
- Implementation intentions: d=0.65 (94 studies, N=8000+)
- Temptation bundling: +51% task completion
- IF-THEN автоматизирует control (медиальная BA 10 вместо lateral)

---

### 4. Energy-Aware + Interruption-Buffered TMP

**Генерация плана с учетом:**
1. Energy requirements задач
2. User's energy peaks
3. Interruption buffers
4. Segmenter/Integrator preference

```
User profile: Segmenter + Morning energy peak

Bot генерирует:
┌─ TIME MANAGEMENT PLAN ──────────┐
│ 09:00-12:00 → Преза (HIGH) 🔴   │ ← Peak energy для creative
│               IF-THEN: 9:00 сел │
│                                 │
│ 12:00-13:00 → Обед + Recovery   │
│                                 │
│ 13:00-14:00 → БУФЕР             │ ← Interruption buffer
│               (незапланированное)│
│                                 │
│ 14:00-15:30 → Баг debug (MED) 🟡│ ← Post-lunch OK для debug
│               IF-THEN: 14:00    │
│               Bundle: podcast   │
│                                 │
│ 15:30-16:00 → БУФЕР             │
│                                 │
│ 16:00-16:15 → Звонок маме (LOW)🟢│
│                                 │
│ 16:15-17:00 → Slack time +      │
│               Recovery prep     │
└─────────────────────────────────┘

Принципы плана:
✅ HIGH energy task на peak time (9-12)
✅ 1.5ч буферов на прерывания (ты сказал 2-3 в день)
✅ Deep work блоки утром (segmenter preference)
✅ Recovery time в конце дня
✅ IF-THEN triggers для каждой задачи

[Looks good] [Adjust] [Regenerate]
```

**Если Integrator:**

```
Bot генерирует более гибкий план:
┌─ FLEXIBLE PLAN (INTEGRATOR) ────┐
│ 09:00-12:00 → Преза (priority)  │
│               Может прерваться  │
│                                 │
│ 12:00-17:00 → Flexible window   │
│               ├─ Баг debug      │
│               ├─ Звонок маме    │
│               └─ Адаптируй      │
│                                 │
│ Note: Помни про psychological   │
│ detachment после работы!        │
└─────────────────────────────────┘

"Твой стиль - гибкость. План не жесткий.
 Но вечером важно ОТКЛЮЧИТЬСЯ от работы.
 Напомню в 17:00."
```

**Micro-breaks Intelligence:**

```
После каждого 50-90мин блока:

Bot: "Перерыв 5-10 мин! Чем займешься?"

Quick replies:
[🚶 Walk] [🌳 Nature view] [☕️ Coffee] [📱 Phone] [💬 Chat]

Если Phone:
Bot: "⚠️ Внимание!

     Соцсети УХУДШАЮТ recovery:
     - Beta waves elevated 15+ мин
     - Attention на -40%
     - Performance хуже чем без перерыва

     Лучше:
     - Walk: префронtal activation -62%
     - Nature: working memory +16%
     - Chat: emotional restoration

     Уверен в Phone?"

[Change to Walk] [Phone anyway]

Если Walk:
Bot: "Отлично! 5 минут movement.
     Bonus: если есть окно с видом на зелень - посмотри 30 сек.

     [Start Timer 5min]"
```

**Научная база:**
- Energy-aware: cortisol peak утром = best для cognitive work
- Micro-breaks: d=0.36 vigor, d=0.35 fatigue reduction
- Social media breaks: WORSE than no break (EEG studies)
- Nature 5 min: measurable attention improvement

---

### 5. Interruption Resume + Pattern Tracking

**Без LLM storage:**

```typescript
interface Interruption {
  timestamp: Date;
  currentTask: string;
  interruptionReason?: string;
  duration?: number;
  bufferUsed: boolean;
}
```

**UX Flow:**

```
User работает над презой 10:30

User: "/pause встреча с PM"

Bot: "✅ Сохранено:
     Task: Презентация (10:30-?)
     Reason: встреча PM
     Buffer: 14:00-15:00 available

     Возвращайся когда свободен."

[Back to Преза] [Switch task]

---

User: "/resume" (11:15)

Bot: "С возвращением!

     📌 Ты прервался на:
     - Презентация
     - 45 мин назад
     - Reason: встреча PM

     Продолжаешь или переключаешься?"

[Continue Преза] [Switch to Баг] [Take break]
```

**Pattern Recognition (weekly):**

```
После недели данных:

Bot: "📊 Недельный анализ готов!"

┌─ INTERRUPTION PATTERNS ──────────┐
│ Вторник 14:00-16:00: 5 прерываний│
│ Причины:                         │
│ - Незапланированные встречи (60%)│
│ - Slack notifications (40%)      │
│                                  │
│ 💡 Suggestion:                   │
│ Block вторник 14-16 для deep work│
│ Meetings only после 16:00        │
│                                  │
│ [Apply] [Customize] [Ignore]     │
└──────────────────────────────────┘

┌─ BEST PERFORMANCE TIME ───────────┐
│ Среда 9-11 утра                  │
│ - 80% топ-3 completed            │
│ - Минимум interruptions          │
│ - High reported energy           │
│                                  │
│ 💡 Suggestion:                   │
│ Ставь самые сложные задачи       │
│ на среду утром                   │
└──────────────────────────────────┘
```

---

### 6. Advanced Stuck Handler

**Proactive Detection:**

```
Bot замечает:
- Задача висит 3+ дня без прогресса
- User писал /stuck 2+ раза по этой задаче
- Task marked "complex" и deadline близко

Bot: "Рефакторинг висит 3 дня. Что мешает?"

┌─ BLOCKER DETECTION ──────────────┐
│ [❓ Не знаю с чего начать]       │
│ [😰 Страшно сломать]             │
│ [😴 Скучная задача]              │
│ [📚 Не хватает инфы]             │
│ [⏰ Нет времени]                 │
│ [🤯 Эмоциональный overwhelm]     │ ← NEW
│ [Other...]                       │
└──────────────────────────────────┘
```

**Responses по blocker type:**

#### Blocker: "Эмоциональный overwhelm"

```
Bot: "Понял. Сначала эмоции, потом задача.

     Шаг 1: Emotional Recovery (3 min)
     [Запускает dysregulation protocol]

     ---

     Шаг 2: Cognitive Reappraisal

     Твоя автоматическая мысль сейчас:
     'Это disaster / я failure / всё безнадёжно'

     Реалистичная альтернатива:
     'Это сложно И вызывает стресс.
      Я могу попросить помощь.
      Я могу работать маленькими кусками.'

     Повтори альтернативу вслух или про себя.

     ---

     Шаг 3: Tiniest Step

     Что САМОЕ МАЛЕНЬКОЕ можешь сделать за 2 минуты?"

Quick replies:
[Написать вопрос коллеге]
[Прочитать требования еще раз]
[Нарисовать схему на бумаге]
[Попросить помощь]
```

#### Blocker: "Страшно сломать"

```
Bot: "Safety-first подход!

     Шаг 1: Safety Net (30 мин)
     ├─ git checkout -b feature/refactor-safe
     ├─ Напиши 2-3 теста на текущее поведение
     ├─ Push ветку (бэкап готов)
     └─ Теперь безопасно экспериментировать

     Шаг 2: Tiniest change
     Выбери ОДНУ самую маленькую функцию для рефакторинга.

     Шаг 3: Test immediately
     Прогони тесты после каждого изменения.

     Начнем с safety net?"

[Yes] [Tell me more] [Different approach]
```

#### Blocker: "Скучная задача"

```
Bot: "Понял. Давай сделаем её менее скучной!

     Вариант 1: Temptation Bundling
     Привяжем что-то приятное ТОЛЬКО к этой задаче.
     Что любишь? (подкаст, музыка, видео)

     Вариант 2: Gamification
     Ставим таймер на 25 мин - Challenge mode!
     Сколько успеешь сделать?

     Вариант 3: Pairing
     Зови коллегу делать вместе - веселее.

     Какой вариант?"

[Bundle] [Gamify] [Pair] [Other]
```

**Научная база:**
- 6 main procrastination blockers from research
- Temptation bundling: +51%
- Cognitive reappraisal: d=0.85 for task aversion

---

### 7. MCII for Big Goals (Weekly Ritual)

**Для долгосрочных целей:**

```
Каждое воскресенье (или user выбирает день):

Bot: "Воскресный ритуал! Fresh Start Effect.

     Прошлая неделя закрыта ✅
     Новая неделя = новые возможности

     Твоя большая цель: 'Стать C-level в Uber'
     Давай MCII протокол."

┌─ MCII PROTOCOL ──────────────────┐
│ W - WISH                         │
│ Опиши цель своими словами:       │
│ [Text input]                     │
│                                  │
│ O - OUTCOME                      │
│ Представь успех. Что чувствуешь? │
│ Где ты? Что делаешь?             │
│ [Text input]                     │
│                                  │
│ O - OBSTACLE                     │
│ Главное препятствие на пути:     │
│ [Text input]                     │
│                                  │
│ P - PLAN (IF-THEN)               │
│ Автогенерация...                 │
└──────────────────────────────────┘

User вводит:
W: "Хочу стать C-level в Uber"
O: "Вижу себя принимающим стратегические решения, влияю на продукт"
O: "Прокрастинирую сложные задачи, которые развивают нужные навыки"

Bot генерирует IF-THEN:

"IF я чувствую импульс сбежать от сложной задачи,
 THEN я делаю 3 глубоких вдоха и говорю себе:
 'Это именно тот дискомфорт, который ведёт к C-level роли'
 и продолжаю работу ещё 5 минут."

Сохранить этот план?"

[Yes] [Edit] [Regenerate]

Bot: "План сохранён! Буду напоминать в моменты /stuck."
```

**Научная база:**
- MCII: g=0.34 overall (N=15,907)
- Experimenter-guided g=0.47
- Fresh Start Effect: +82% motivation на temporal landmarks

---

### 8. Habit Formation Tracker (Long-term)

**Для recurring задач:**

```
User добавляет задачу: "тренировка 3x в неделю"

Bot: "Это привычка! Хочешь tracking формирования?"

[Yes] [No, just reminder]

Если Yes:

Bot: "Формирование займет 66-154 дня (median 66).

     Буду отслеживать:
     ✅ Consistency (1 пропуск OK, паттерн опасен)
     ✅ Context stability (одно время/место)
     ✅ Automaticity (как легко становится)

     Когда лучше тренироваться?"

[Утро] [День] [Вечер]

User: "Утро"

Bot: "Отлично! Morning habits эффективнее на 40%.

     Конкретный триггер? (для IF-THEN)"

Templates:
[После завтрака, 8:30]
[Сразу после подъёма, 7:00]
[Перед душем]
[Custom...]

User: "после завтрака, 8:30"

Bot создает:
┌─ HABIT TRACKER ──────────────────┐
│ Habit: Тренировка                │
│ Frequency: 3x в неделю (Mon/Wed/Fri)│
│ Trigger: 8:30, после завтрака    │
│ Action: Надеть кроссовки         │
│ Duration: 20-30 мин              │
│                                  │
│ Progress:                        │
│ ░░░░░░░░░░ Day 1/66 (0%)        │
│                                  │
│ Automaticity: Not measured yet   │
└──────────────────────────────────┘

"Каждый день в 8:30 буду спрашивать: 'Сделал?'
 Один пропуск нормален. Паттерн пропусков - проблема."
```

**Tracking flow:**

```
День 1, 8:30:
Bot: "Время тренировки! IF-THEN:
     'После завтрака → надеть кроссовки'

     Сделал?"

[Done ✅] [Skip today] [Doing now]

---

День 10:
Bot: "🎉 10 дней streak! Halfway to habit.

     ███░░░░░░░ Day 10/66 (15%)

     Automaticity check:
     Насколько легко начать? (1-10)"

User: "4"

Bot: "Day 10, Automaticity 40%.
     Нормально! Ожидаемо ~60% к дню 30.

     Продолжай тот же триггер каждый день."

---

День 66:
Bot: "🎉🎉🎉 66 ДНЕЙ! HABIT FORMED!

     ██████████ Day 66/66 (100%)

     Automaticity: 85%
     Consistency: 95% (3 пропуска total)

     Теперь это часть тебя.
     Убрать из active tracking?"

[Keep tracking] [Archive] [Celebrate 🎉]
```

**Научная база:**
- Median 66 days для automaticity
- Morning practice превосходит вечернюю
- Context stability критична
- Один пропуск не вредит, паттерн пропусков = reset

---

### 9. Evening Recovery Coach

**Цель**: Правильный отдых для next-day performance

```
17:00 (или end of work day):

Bot: "Рабочий день закончен! 🏁

     Как себя чувствуешь?"

[😫 Exhausted] [😐 Ok] [😊 Energized]

Если Exhausted:

Bot: "Понял. Recovery критичен для завтрашнего дня.

     4 вещи для полного recovery:

     1️⃣ Psychological Detachment
     2️⃣ Relaxation
     3️⃣ Physical Activity (опционально)
     4️⃣ Mastery Experience

     Начнем с #1?"

[Yes] [Tell me more]

┌─ PSYCHOLOGICAL DETACHMENT ───────┐
│ Отключись от работы:             │
│ ✅ Закрой Slack до завтра        │
│ ✅ Отключи рабочий email         │
│ ✅ Убери ноутбук с глаз          │
│                                  │
│ Это самый важный фактор recovery │
│ (r=-0.49 с exhaustion)           │
│                                  │
│ [Done]                           │
└──────────────────────────────────┘

Bot: "Отлично! Теперь Relaxation Activity.
     Что выберешь?"

Quick replies:
[🚶 Walk in nature - best] [🧘 Meditation]
[📺 Light TV - ok] [📱 Social media - worst]
[🏋️ Exercise] [👥 Friends/family]

Если выбирает Social media:

Bot: "⚠️ Стоп!

     Экраны УХУДШАЮТ recovery:
     - Alpha waves не восстанавливаются 40+ мин
     - Tomorrow working memory -20%
     - Exhaustion recovery: none

     Рекомендую:
     🥇 Nature walk 20 мин = +16% WM tomorrow
     🥈 Exercise = psychological detachment
     🥉 Friends = emotional restoration

     Хочешь изменить выбор?"

[Change to Walk] [Meditation] [Social media anyway]

Если Walk:

Bot: "🎯 Perfect choice!

     20-min nature walk:
     ✅ Cortisol -6%
     ✅ Attention +16% tomorrow
     ✅ Mood improvement

     Bonus: если есть парк с водой (река, озеро)
     - эффект ещё сильнее!

     [Start Timer 20min]

     Увидимся завтра утром! 😊"
```

**Если Energized:**

```
Bot: "Классно! У тебя энергия.

     Хочешь Mastery Experience?
     (Learning something new, challenging hobby)

     Это builds positive resources, не просто reduces strain.

     Что интересно?"

[Learn new skill] [Creative hobby] [Sport/physical]
[Already have plans]

Если Already have plans:
"Отлично! Enjoy evening!
 Завтра в 9:00 начнём с brain dump."
```

**Научная база:**
- Psychological detachment: strongest predictor of recovery
- Nature 20 min: +16% working memory next day
- Social media: worse than no break
- 4 recovery experiences (Sonnentag): detachment, relaxation, mastery, control

---

### 10. Cognitive Restructuring Log

**Для chronic negative thoughts:**

```
User пишет: "я не могу это сделать, слишком сложно"
или
Multiple /stuck с одинаковым паттерном

Bot: "Поймал катастрофическую мысль!

     'Я не могу, слишком сложно'

     Хочешь разобрать? (2 минуты)"

[Yes] [Not now] [What's this?]

Если Yes:

┌─ THOUGHT ANALYSIS ───────────────┐
│ Automatic thought:               │
│ "Я не могу, слишком сложно"      │
│                                  │
│ Evidence FOR this thought:       │
│ [Напиши факты, не ощущения]      │
│                                  │
│ User: "я не знаю с чего начать"  │
│                                  │
│ Evidence AGAINST:                │
│ [Напиши факты]                   │
│                                  │
│ User: "я делал похожие задачи,   │
│        могу попросить помощь"    │
│                                  │
│ Alternative thought:             │
│ (Реалистичная, не позитивная)    │
│                                  │
│ Template:                        │
│ "Это сложно И я могу [action]"   │
│                                  │
│ User: "Это сложно и я могу       │
│        работать по 10 мин за раз"│
│                                  │
│ [Save to Evidence Log]           │
└──────────────────────────────────┘

Bot: "Отлично! Сохранил в Evidence Log.

     Теперь Behavioral Experiment:

     Prediction: 'Если работаю 10 мин, ничего не пойму'
     Experiment: Работай ровно 10 минут

     Засекаю время. После расскажешь результат."

[Start 10-min experiment]

---

После 10 минут:

Bot: "Результат эксперимента?"

Predicted difficulty: [1-10]
Actual difficulty: [1-10]

Predicted anxiety: [1-10]
Actual anxiety: [1-10]

Progress made: [text]

Bot сохраняет в Evidence Log.
```

**Weekly Evidence Log Review:**

```
После 2 недель:

Bot: "📊 Evidence Log готов!

     Твои предсказания vs реальность:

     ┌─ PREDICTIONS vs REALITY ─────┐
     │ Task 1: Рефакторинг          │
     │ Predicted difficulty: 9/10   │
     │ Actual: 6/10 ✅ (33% меньше) │
     │                              │
     │ Task 2: Новый feature        │
     │ Predicted time: 3 hours      │
     │ Actual: 1.5h ✅ (50% быстрее)│
     │                              │
     │ Task 3: Code review          │
     │ Predicted anxiety: 8/10      │
     │ Actual: 5/10 ✅ (38% меньше) │
     └──────────────────────────────┘

     ПАТТЕРН:
     Ты систематически переоцениваешь:
     - Difficulty на ~40%
     - Time needed на ~45%
     - Anxiety на ~35%

     Твой мозг врёт тебе! 🧠😅

     В следующий раз когда скажет 'это impossible',
     помни: скорее всего на 40% легче чем кажется."
```

**Научная база:**
- CBT for procrastination: g=0.55-0.93
- Cognitive restructuring: d=0.85
- Behavioral experiments: core CBT technique
- Evidence logs: combat cognitive distortions

---

### 11. Weekly Review + Pattern Recognition

**Каждое воскресенье:**

```
Bot: "📊 Недельный обзор готов!

     Сначала: как прошла неделя в целом?"

[😫 Hard] [😐 Ok] [😊 Great]

┌─ WEEKLY INSIGHTS ────────────────┐
│ 📈 Completion Rate:              │
│ Топ-3 tasks: 75% (15/20)         │
│ Up from last week: +10%          │
│                                  │
│ ⏱️ Time Accuracy:                │
│ Estimated: 30h                   │
│ Actual: 35h                      │
│ You underestimate by 15%         │
│                                  │
│ 🔴 Interruption Patterns:        │
│ Вторник 14:00-16:00: 5 раз       │
│ Четверг 10:00-12:00: 4 раза      │
│                                  │
│ Main causes:                     │
│ - Ad-hoc meetings (45%)          │
│ - Slack pings (35%)              │
│ - Personal (20%)                 │
│                                  │
│ 💡 Suggestion:                   │
│ Block вторник/четверг утра       │
│ for deep work only               │
│                                  │
│ [Apply blocking] [Customize]     │
└──────────────────────────────────┘

┌─ BEST PERFORMANCE ───────────────┐
│ ⭐ Star time: Среда 9-11 AM      │
│ - 85% task completion            │
│ - 0 interruptions                │
│ - High energy reported           │
│ - Complex tasks done best        │
│                                  │
│ 💡 Recommendation:               │
│ Schedule hardest work            │
│ for Wednesdays 9-11              │
│                                  │
│ [Auto-schedule] [Note it]        │
└──────────────────────────────────┘

┌─ STUCK PATTERNS ─────────────────┐
│ Tasks with most /stuck:          │
│ 1. "Code review" - 4x            │
│    Blocker: "Скучная задача"     │
│                                  │
│ 2. "Documentation" - 3x          │
│    Blocker: "Не знаю с чего"     │
│                                  │
│ 💡 Suggestions:                  │
│ - Code review: Temptation bundle │
│   с подкастом?                   │
│ - Documentation: Template готов, │
│   начни с него                   │
│                                  │
│ [Apply] [Dismiss]                │
└──────────────────────────────────┘

┌─ RECOVERY QUALITY ───────────────┐
│ Evening recovery activities:     │
│ 🥇 Nature walks: 3x              │
│    Next-day performance: 85%     │
│                                  │
│ 😐 Social media: 2x              │
│    Next-day performance: 60%     │
│                                  │
│ 📊 Correlation clear:            │
│ Nature = better tomorrow         │
│                                  │
│ Try more nature walks?           │
│ [Yes, remind me] [I know]        │
└──────────────────────────────────┘

Bot: "Fresh Start Effect активен!
     Прошлая неделя закрыта ✅

     Новая неделя = новый подход

     Готов к MCII ритуалу для big goal?"

[Yes, let's do it] [Skip this week] [Later]
```

**Научная база:**
- Pattern recognition: diary studies methodology
- Fresh Start Effect: +82% motivation
- Weekly review: shown to improve planning accuracy
- Data-driven insights: personalizes system

---

## 🎨 Complete Daily Flow

### Morning (9:00):

```
Bot: "Доброе утро! ☀️

     Сегодня четверг. Твой best performance time.

     Готов к brain dump?"

[Yes] [Need coffee first ☕️] [Check yesterday]

/start_day

User: "написать feature spec, code review, meeting в 15:00,
       купить подарок маме, спортзал"

LLM категоризирует автоматически:
├─ Work: Feature spec (HIGH energy), Code review (MEDIUM)
├─ Personal: Подарок маме, Спортзал
├─ Time-bound: Meeting 15:00
└─ Emotional state: Neutral ✅

Bot: "5 задач + meeting. Топ-3?"

[Quick Select mode with buttons]

User выбирает: Feature spec, Code review, Спортзал

Bot: "Типы + Energy?"
- Feature spec: Execution, HIGH
- Code review: Execution, MEDIUM
- Спортзал: Habit, MEDIUM

Bot: "IF-THEN планы для каждой..."

[Проходит IF-THEN builder для 3 задач]

Bot: "Code review скучный? Temptation bundle?"
User: "да, с подкастом"

Bot генерирует Energy-Aware TMP:
09:30-12:30 → Feature spec (HIGH) + IF-THEN
12:30-13:30 → Lunch + Recovery
13:30-14:30 → Code review (MEDIUM) + Podcast bundle
14:30-15:00 → Buffer
15:00-16:00 → Meeting
16:00-17:00 → Buffer + Slack time
18:00 → Спортзал (habit trigger)

"План готов! Поехали? 🚀"
```

### During Day:

```
10:30 - Working on feature spec

[Automatic check-in after 60 min]
Bot: "Перерыв 5 мин! Чем займешься?"
[Walk/Nature/Coffee - guided choice]

---

11:45 - Чувствует frustration

User: "/tilt баг не исправляется блять"

Bot: "Эмоции detected. Quick recovery (3 мин)?"
[Dysregulation protocol]

---

13:30 - Code review time

Bot: "IF-THEN trigger: 13:30, code review!

     Включай подкаст и поехали.
     Таймбокс 1ч."

[Start timer + track bundle compliance]

---

14:45 - Неожиданная встреча

User: "/pause внезапный call"

Bot: "✅ Saved. Buffer 14:30-15:00 used.
     Presentation moved to 15:30."

---

16:00 - Resume

User: "/resume"

Bot: "Code review: 1h done ✅ (bundle: compliant)
     Next: Slack time или early wrap?"
```

### Evening (17:00):

```
Bot: "День закончен! 🏁

     Что из топ-3 сделал?"

User: "feature spec done, code review done, спортзал нет"

Bot: "Отлично! 2/3 completed (67%)

     Спортзал - recurring habit. Пропуск #1 эта неделя.
     Ок для habit формирования.

     ---

     Exhausted?"

User: "да"

Bot: "Recovery protocol! 4 шага..."
[Evening Recovery Coach активируется]

User выбирает: Nature walk 20 min

Bot: "Perfect! Завтра performance +16%.
     Увидимся утром! 😊"
```

### Weekly (Sunday):

```
Bot: "📊 Weekly Review готов!

     Эта неделя: 15/20 топ-3 done (75%)

     [Показывает все Insights]

     ---

     Fresh Start! Новая неделя.
     Big goal MCII ритуал?"

[MCII protocol]

Bot: "План на неделю готов!
     Remember твой IF-THEN для big goal:

     'IF импульс сбежать от сложной задачи,
      THEN 3 вдоха + 'это путь к C-level' + 5 мин работы'

     Увидимся в понедельник! 🚀"
```

---

## 🧠 LLM Architecture

### Роли LLM (расширенные):

1. **Categorizer** (автоматический)
   - Сортирует brain dump
   - Детектит эмоциональное состояние
   - Определяет energy requirements

2. **Implementation Intentions Builder** (guided)
   - Помогает создавать IF-THEN планы
   - Предлагает шаблоны триггеров
   - Генерирует первые действия

3. **Energy-Aware Planner** (расширенный)
   - Учитывает energy peaks
   - Добавляет interruption buffers
   - Персонализирует под Segmenter/Integrator

4. **Dysregulation Coach** (новый)
   - Детектит tilt/stress
   - Ведёт через recovery protocols
   - Использует cognitive reappraisal

5. **Stuck Helper** (расширенный)
   - 6 типов blockers
   - Персонализированные решения
   - Интеграция с MCII

6. **Recovery Coach** (новый)
   - Evening guidance
   - Activity recommendations
   - Education о вреде social media

7. **Pattern Analyzer** (расширенный)
   - Weekly insights
   - Interruption patterns
   - Stuck patterns
   - Recovery effectiveness

8. **Cognitive Restructuring Guide** (новый)
   - Ловит catastrophic thoughts
   - Ведёт через thought analysis
   - Behavioral experiments
   - Evidence Log

9. **Habit Formation Tracker** (новый)
   - Long-term tracking 66-154 days
   - Automaticity measurements
   - Consistency monitoring

10. **MCII Facilitator** (новый)
    - Weekly ritual
    - Mental contrasting + IF-THEN
    - Big goals support

### Границы LLM:

**✅ LLM может:**
- Всё из v1 +
- Детектить эмоциональное состояние
- Генерировать IF-THEN планы (с подтверждением)
- Классифицировать blockers
- Рекомендовать recovery activities
- Анализировать patterns (описывать, не решать)
- Вести через CBT-lite протоколы

**❌ LLM НЕ может:**
- Принимать решения за пользователя
- Диагностировать mental health issues
- Форсировать протоколы (всегда опционально)
- Менять habit triggers без согласия

**🤝 Без LLM (simple logic):**
- `/pause`/`/resume` state storage
- Timer tracking
- Habit streak counting
- Interruption logging

---

## 🚀 Revised MVP Phases

### Phase 1: Core + Recovery (3-4 недели)

**Must-have:**
1. ✅ Brain dump с emotional detection
2. ✅ Top-3 + IF-THEN Builder
3. ✅ Energy-Aware TMP Generator
4. ✅ Emotional Dysregulation Protocol (Quick Recovery)
5. ✅ `/pause`/`/resume` (state storage)
6. ✅ Micro-breaks Intelligence
7. ✅ Segmenter/Integrator Quiz

**Success metrics:**
- Brain dump < 2 min
- IF-THEN plan creation < 3 min per task
- 80% users use dysregulation protocol when offered
- Recovery activity choice influences next-day performance

---

### Phase 2: Stuck + Habits (2-3 недели)

**Features:**
8. ✅ Advanced Stuck Handler (6 blockers)
9. ✅ Temptation Bundling setup
10. ✅ Habit Formation Tracker (start of long-term)
11. ✅ Evening Recovery Coach
12. ✅ Progress check-ins

**Metrics:**
- Stuck resolution time: 3 days → 30 minutes
- Habit adherence rate > 80% first 2 weeks
- Evening recovery protocol usage

---

### Phase 3: Intelligence + Optimization (3-4 недели)

**Features:**
13. ✅ Pattern Recognition (weekly)
14. ✅ Cognitive Restructuring Log
15. ✅ MCII Weekly Ritual
16. ✅ Weekly Review + Fresh Start
17. ✅ Evidence Log analysis

**Metrics:**
- Pattern-based suggestions acceptance rate
- Evidence Log: prediction vs reality gap
- MCII adherence
- Weekly retention

---

### Phase 4: Advanced (future)

- Integration: Google Calendar, Notion, Todoist
- Voice input for brain dump
- Mobile app (not just Telegram)
- Social features (accountability partners)
- Advanced analytics dashboard

---

## 💾 Database Schema

```sql
-- Users
CREATE TABLE users (
  id UUID PRIMARY KEY,
  telegram_id BIGINT UNIQUE,
  profile_type ENUM('segmenter', 'integrator'),
  energy_peak ENUM('morning', 'afternoon', 'evening'),
  timezone VARCHAR(50),
  created_at TIMESTAMP,
  settings JSONB
);

-- Tasks
CREATE TABLE tasks (
  id UUID PRIMARY KEY,
  user_id UUID REFERENCES users(id),
  title TEXT,
  category ENUM('work', 'personal', 'urgent'),
  task_type ENUM('execution', 'exploration', 'learning'),
  energy_level ENUM('high', 'medium', 'low'),
  status ENUM('pending', 'in_progress', 'completed', 'abandoned'),
  created_at TIMESTAMP,
  completed_at TIMESTAMP,
  estimated_duration INTEGER, -- minutes
  actual_duration INTEGER,
  difficulty_predicted INTEGER, -- 1-10
  difficulty_actual INTEGER,
  stuck_count INTEGER DEFAULT 0,
  blocker_types TEXT[], -- array of blocker types
  temptation_bundle TEXT
);

-- Implementation Intentions
CREATE TABLE implementation_intentions (
  id UUID PRIMARY KEY,
  task_id UUID REFERENCES tasks(id),
  if_trigger TEXT, -- "ЕСЛИ..."
  then_action TEXT, -- "ТО..."
  rehearsed BOOLEAN DEFAULT FALSE,
  created_at TIMESTAMP
);

-- Daily Plans
CREATE TABLE daily_plans (
  id UUID PRIMARY KEY,
  user_id UUID REFERENCES users(id),
  date DATE,
  top_3_tasks UUID[], -- array of task IDs
  interruption_buffer_mins INTEGER,
  plan_generated_at TIMESTAMP,
  plan JSONB -- full TMP structure
);

-- Interruptions
CREATE TABLE interruptions (
  id UUID PRIMARY KEY,
  user_id UUID REFERENCES users(id),
  task_id UUID REFERENCES tasks(id),
  timestamp TIMESTAMP,
  reason TEXT,
  duration INTEGER, -- minutes
  buffer_used BOOLEAN
);

-- Emotional Events
CREATE TABLE emotional_events (
  id UUID PRIMARY KEY,
  user_id UUID REFERENCES users(id),
  timestamp TIMESTAMP,
  trigger_type ENUM('text_analysis', 'manual_tilt', 'multiple_stuck'),
  intensity INTEGER, -- 1-10
  protocol_used ENUM('quick_recovery', 'deep_recovery', 'declined'),
  pre_state INTEGER, -- 1-10
  post_state INTEGER
);

-- Habits
CREATE TABLE habits (
  id UUID PRIMARY KEY,
  user_id UUID REFERENCES users(id),
  title TEXT,
  frequency_per_week INTEGER,
  trigger_time TIME,
  trigger_context TEXT,
  start_date DATE,
  target_days INTEGER DEFAULT 66,
  current_streak INTEGER DEFAULT 0,
  automaticity_scores INTEGER[], -- array of 1-10 scores over time
  status ENUM('forming', 'formed', 'abandoned')
);

-- Habit Logs
CREATE TABLE habit_logs (
  id UUID PRIMARY KEY,
  habit_id UUID REFERENCES habits(id),
  date DATE,
  completed BOOLEAN,
  automaticity_score INTEGER, -- 1-10, measured periodically
  notes TEXT
);

-- Cognitive Restructuring Entries
CREATE TABLE cognitive_entries (
  id UUID PRIMARY KEY,
  user_id UUID REFERENCES users(id),
  task_id UUID REFERENCES tasks(id),
  automatic_thought TEXT,
  evidence_for TEXT,
  evidence_against TEXT,
  alternative_thought TEXT,
  experiment_prediction TEXT,
  experiment_result TEXT,
  created_at TIMESTAMP
);

-- Recovery Activities
CREATE TABLE recovery_activities (
  id UUID PRIMARY KEY,
  user_id UUID REFERENCES users(id),
  date DATE,
  activity_type ENUM('nature_walk', 'exercise', 'meditation',
                     'social', 'tv', 'social_media', 'other'),
  duration INTEGER, -- minutes
  next_day_performance INTEGER -- 1-10, subjective or objective
);

-- MCII Goals
CREATE TABLE mcii_goals (
  id UUID PRIMARY KEY,
  user_id UUID REFERENCES users(id),
  wish TEXT,
  outcome TEXT,
  obstacle TEXT,
  plan TEXT, -- IF-THEN
  created_at TIMESTAMP,
  last_reviewed DATE,
  status ENUM('active', 'achieved', 'abandoned')
);

-- Weekly Insights (computed)
CREATE TABLE weekly_insights (
  id UUID PRIMARY KEY,
  user_id UUID REFERENCES users(id),
  week_start DATE,
  completion_rate FLOAT,
  interruption_patterns JSONB,
  best_performance_time JSONB,
  stuck_patterns JSONB,
  recovery_correlation JSONB,
  generated_at TIMESTAMP
);
```

---

## 📊 Metrics & Success Criteria

### User Engagement:
- Daily active rate > 70%
- Brain dump completion rate > 90%
- Top-3 selection < 2 min average
- IF-THEN creation compliance > 60%

### Effectiveness:
- Top-3 completion rate: target 70-80%
- Time estimation accuracy improvement: -20% error after 4 weeks
- Stuck resolution: 3 days → 30 min (target 80% cases)
- Habit formation: 60% reach day 66

### Well-being:
- Dysregulation protocol usage when offered: > 70%
- Recovery activity quality improvement over time
- User-reported productivity: +30% after 4 weeks
- User-reported stress: -20% after 4 weeks

### Retention:
- Week 1→2: 80%
- Week 2→4: 70%
- Week 4→8: 60%
- Week 8+: 50%

---

## 🤔 Open Questions

### Research needed:
1. Optimal frequency для check-ins (не стать annoying)
2. Персонализация tone of voice (строгий vs supportive)
3. Gamification элементы (streaks, badges) - помогает или отвлекает?
4. Social accountability - нужна ли?

### Technical:
5. Telegram vs dedicated app vs web
6. LLM provider (Claude vs GPT-4 vs local)
7. Notification strategy (push timing)
8. Data export format

### Monetization:
9. Freemium model: что в free, что в paid?
10. Subscription price point
11. Lifetime access option?

---

## 📋 Next Immediate Steps

### Week 1-2: Foundation
- [ ] Setup Telegram bot infrastructure
- [ ] Design DB schema (PostgreSQL)
- [ ] Write LLM prompts для каждой роли
- [ ] Implement Segmenter/Integrator quiz
- [ ] Build Brain Dump + emotional detection

### Week 3-4: Core Features
- [ ] Top-3 selection UI (inline keyboards)
- [ ] IF-THEN Builder flow
- [ ] Energy-Aware TMP Generator
- [ ] State storage для /pause-/resume
- [ ] Basic dysregulation protocol

### Week 5-6: Testing Alpha
- [ ] Recruit 10 alpha testers
- [ ] Iterate on UX based on feedback
- [ ] Add Temptation Bundling
- [ ] Implement micro-breaks intelligence
- [ ] Evening Recovery Coach

### Week 7-8: Beta Launch
- [ ] Stuck Handler (6 blockers)
- [ ] Habit Tracker начало
- [ ] Pattern Recognition v1
- [ ] Onboard 100 beta users
- [ ] Collect metrics

---

## 🎓 Scientific Foundation Summary

**Strongest evidence (RCTs, meta-analyses):**

| Feature | Effect Size | Study Base |
|---------|-------------|------------|
| Implementation Intentions | d=0.65 | 94 studies, N=8000+ |
| CBT for procrastination | g=0.55-0.93 | Multiple RCTs |
| Box breathing | g=-0.35 | N=785, meta-analysis |
| Nature exposure | +16-20% WM | Multiple studies |
| MCII | g=0.34 | N=15,907 |
| Temptation bundling | +51% | N=226, RCT |
| Psychological detachment | r=-0.49 exhaustion | N=26,592, meta |

**Total research base**: 50+ peer-reviewed studies, 100,000+ participants combined

---

Это полная концепция v2 с интеграцией всех 11 ключевых фичей из исследований!

Готов к обсуждению приоритизации или детализации любой части. 🚀
