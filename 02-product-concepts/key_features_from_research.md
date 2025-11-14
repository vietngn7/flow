# Ключевые фичи из исследований для Productivity Companion

## Источники
Извлечено из:
- managing-emotional-dysregulation.md
- segmenters-and-integrators-time-planning.md
- rest-and-recovery-for-knowledge-workers.md
- преодоление-аверсивных-задач.md
- трехуровневая-система-сложных-задач.md

---

## 🔥 Что мы УПУСТИЛИ в текущей концепции

### 1. Emotional Dysregulation / "Tilt" Management

**Проблема**: Пользователь в стрессе/фрустрации после багов, конфликтов, критики → не может работать эффективно.

**Что делать в продукте:**

#### Real-time Interventions (когда пользователь "на тилте"):

```
Bot замечает паттерн:
- Несколько /stuck команд подряд
- Частые /pause без /resume
- Пользователь пишет эмоционально-окрашенные сообщения

Bot: "Похоже ты в стрессе. Хочешь быстрый протокол восстановления?"

[3-min Recovery] [5-min Deep] [I'm fine]

Если выбирает 3-min:
┌─ QUICK RECOVERY ─────────────┐
│ 1. Box breathing (90 sec)    │
│    Вдох 4-4-4-4              │
│    [Start Timer]             │
│                              │
│ 2. 5-4-3-2-1 Grounding       │
│    5 вещей видишь            │
│    4 слышишь                 │
│    3 можешь потрогать        │
│    2 запаха                  │
│    1 вкус                    │
│                              │
│ 3. Micro walk (2 min)        │
│    [Start Timer]             │
└──────────────────────────────┘
```

**Научная база:**
- Box breathing: d=-0.35 для stress reduction
- 5-4-3-2-1 grounding: shifts attention от distress к present moment
- Nature exposure: даже 5 минут улучшают attention

---

### 2. Segmenters vs Integrators (Work-Life Boundary Preferences)

**Проблема**: Не все хотят одинаковую систему планирования!

**Что упустили:**
- Некоторые люди хотят **четкое разделение** work/life (segmenters)
- Другие предпочитают **гибкое смешивание** (integrators)
- Навязывание одного подхода всем = снижение well-being

**Фича для продукта: Onboarding Quiz**

```
Bot: "Давай узнаем твой стиль работы. Что больше про тебя?"

┌─ BOUNDARY PREFERENCE ────────┐
│ Scenario 1:                  │
│ Тебе звонит семья в рабочее  │
│ время. Как ты себя чувствуешь?│
│                              │
│ [😤 Раздражен, это мешает]   │
│ [😊 Норм, могу переключиться]│
└──────────────────────────────┘

После 5 вопросов:
Bot: "Ты скорее Segmenter!
     Тебе нужны четкие границы между работой и личным.
     Я настрою систему под это."

ПЛАН ДЛЯ SEGMENTERS:
- Строгие временные блоки
- Работа НЕ вечером
- Отдельные контексты
- Technology restrictions в личное время

ПЛАН ДЛЯ INTEGRATORS:
- Гибкие окна
- Разрешение на work-life blending
- Focus на psychological detachment (навык!)
- Communication tactics > temporal tactics
```

**Научная база:**
- Misfit между preference и environment = выше burnout
- Integrators БОЛЬШЕ выигрывают от mindfulness training (для detachment)
- Segmenters нужна organizational support

---

### 3. Recovery & Breaks Intelligence

**Проблема**: Мы не учли КОГДА и КАК отдыхать между задачами!

**Что упустили из research:**

#### Micro-breaks (10 мин или меньше):

```
После каждого focus блока (50-90 мин):

Bot: "Чем займешься на 5-минутной перерыв?"

Quick replies:
[🚶 Walk] [🌳 Nature] [☕️ Coffee] [📱 Phone] [💬 Chat]

Если выбирает Phone:
Bot: "⚠️ Соцсети ухудшают recovery на 40%.
     Beta waves остаются elevated 15+ мин.

     Лучше Walk или Nature. Попробуешь?"

Если выбирает Walk/Nature:
Bot: "Отлично!
     ✅ Nature exposure: attention +20%
     ✅ Walking: prefrontal activation -62%

     [Start 5-min timer]"
```

**Specific break recommendations by task type:**

```
После analytical work (coding, data analysis):
→ Physical movement или social (НЕ cognitive activities)

После communication-heavy (meetings, emails):
→ Solitude или passive relaxation

После creative work:
→ Mastery в другой creative domain
```

**Научная база:**
- Social media breaks: WORSE performance than no break
- Nature exposure 5 min: measurable attention improvement
- Physical activity: -62% prefrontal activation, better working memory

---

### 4. Implementation Intentions (IF-THEN планирование)

**Проблема**: Мы не используем самую мощную технику для начала задач!

**Что добавить:**

```
При выборе Top-3:

Bot: "Для каждой задачи создадим IF-THEN план.
     Это увеличивает выполнение на 65%."

Задача: Написать отчет

Bot: "Когда и где ты будешь это делать?"

User: "утром за компом"

Bot: "Давай точнее. Формула:
     ЕСЛИ [конкретный триггер], ТО [первое действие]"

Template:
┌─ IF-THEN BUILDER ────────────┐
│ ЕСЛИ:                        │
│ [время] + [место] + [событие]│
│                              │
│ Пример:                      │
│ "9:30 утра, сел за комп,     │
│  открыл календарь"           │
│                              │
│ ТО:                          │
│ [первое физическое действие] │
│                              │
│ Пример:                      │
│ "открываю Google Docs и      │
│  пишу заголовок отчета"      │
└──────────────────────────────┘

Bot: "Прорепетируй мысленно 1 раз.
     Это усиливает эффект."
```

**Научная база:**
- d=0.65 для goal achievement (94 исследования)
- Переключение от deliberate control (lateral BA 10) к automatic (medial BA 10)
- Работает только с сильным goal intention

---

### 5. Temptation Bundling (для скучных задач)

**Проблема**: Рутинные задачи = мучение → откладывание

**Фича:**

```
User добавляет задачу: "почистить данные"

Bot: "Эта задача скучная? Хочешь Temptation Bundle?"

User: "да"

Bot: "Что ты любишь? (подкасты, музыка, видео)"

User: "подкасты про спорт"

Bot: "Отлично! Правило:
     Ты можешь слушать подкаст ТОЛЬКО
     во время чистки данных.

     Строгая привязка = ключ к успеху.

     Это увеличит выполнение на 51%."

[Agree] [Change reward]

Bot сохраняет:
Task: data cleaning
Bundle: sports podcasts
Rule: ONLY during task
```

**Научная база:**
- +51% gym attendance когда audiobooks ТОЛЬКО в зале
- Работает через immediate reward для delayed gratification
- Требует строгую привязку (иначе эффект исчезает)

---

### 6. "Stuck" Handler - Advanced Version

**Что мы упустили:** Разные ПРИЧИНЫ stuck требуют разных подходов!

#### Procrastination Detective (расширенная версия):

```
User: "/stuck рефакторинг легаси кода"

Bot: "Задача висит 3 дня. Что мешает?"

Quick replies:
[❓ Не знаю с чего начать]
[😰 Страшно сломать]
[😴 Скучная задача]
[📚 Не хватает инфы]
[⏰ Нет времени]
[🤯 Emotional overwhelm]  ← NEW

Если выбирает Emotional overwhelm:

Bot: "Понял. Сначала снизим стресс, потом задачу.

     Шаг 1: Emotional recovery (3 мин)
     [Box breathing protocol]

     Шаг 2: Cognitive reappraisal
     Твоя автоматическая мысль:
     'Это disaster / я failure'

     Альтернатива:
     'Это сложно И я могу работать по кусочкам'

     Шаг 3: Tiniest step
     Что самое маленькое можешь сделать за 2 мин?"
```

**Паттерны и решения:**

| Blocker | Solution | Scientific Basis |
|---------|----------|------------------|
| Не знаю с чего начать | Декомпозиция на 2-min chunks | Temporal discounting: малые задачи = низкий effort cost |
| Страшно сломать | Safety net (тесты, бэкап, ветка) | Снижает amygdala activation |
| Скучная задача | Temptation bundling | Immediate reward → дофамин |
| Эмоциональный overwhelm | Emotional dysregulation protocol | Box breathing d=-0.35, грounding |

---

### 7. MCII (Mental Contrasting + Implementation Intentions)

**Для больших целей типа "стать C-level":**

```
Weekly ritual (воскресенье):

Bot: "Твоя большая цель: 'Стать C-level в Uber'
     Давай используем MCII протокол."

Шаг 1: WISH
"Опиши как выглядит успех"

Шаг 2: OUTCOME
"Представь себя в этой роли. Что чувствуешь?"

Шаг 3: OBSTACLE
"Главное препятствие на пути к этому?"

User: "прокрастинирую сложные задачи"

Шаг 4: PLAN (автогенерация IF-THEN)
"IF я чувствую импульс сбежать от сложной задачи,
 THEN я делаю 3 глубоких вдоха и говорю:
 'Это именно тот дискомфорт, который ведет к C-level'
 и работаю еще 5 минут"
```

**Научная база:**
- g=0.34 overall (N=15,907)
- Experimenter-guided g=0.47 vs self-administered g=0.28
- Работает через неосознаваемые mental associations

---

### 8. Energy-Aware Scheduling (Advanced TMP)

**Что упустили:** Разные задачи требуют разного уровня энергии!

**Расширенный TMP генератор:**

```
После топ-3, Bot спрашивает:

"Когда у тебя обычно пик энергии?"

[Утро (8-12)] [День (12-16)] [Вечер (16-20)]

User: "Утро"

Bot анализирует топ-3:
1. Презентация (execution, creative) → HIGH energy
2. Баг в проде (exploration) → MEDIUM energy
3. Звонок маме (routine) → LOW energy

Bot генерирует energy-aware план:
┌─ ENERGY-OPTIMIZED PLAN ─────┐
│ 09:00-12:00 → Преза          │ ← HIGH energy task в peak time
│ 12:00-13:00 → Обед           │
│ 13:00-14:00 → БУФЕР          │
│ 14:00-15:30 → Баг (explore)  │ ← MEDIUM task после обеда
│ 15:30-16:00 → БУФЕР          │
│ 16:00-16:15 → Звонок маме    │ ← LOW energy task
└─────────────────────────────┘

"Я поставил креативную работу на утро,
 когда prefrontal cortex наиболее активна."
```

**Task Energy Classification:**

| Task Type | Energy Level | Best Time |
|-----------|--------------|-----------|
| Creative, Strategy | HIGH | Morning (cortisol peak) |
| Analytical, Debugging | MEDIUM | Mid-morning or post-lunch |
| Communication, Routine | LOW | Afternoon (допустима низкая энергия) |
| Learning new skills | HIGH | Morning (лучше consolidation) |

---

### 9. Recovery Experience Tracking

**Проблема:** Мы не помогаем пользователю ОТДЫХАТЬ правильно!

**Фича: Evening Recovery Coach**

```
Конец рабочего дня (17:00):

Bot: "Рабочий день закончен! Как чувствуешь?"

[😫 Exhausted] [😐 Ok] [😊 Good]

Если Exhausted:

Bot: "Понял. Для recovery нужны 4 вещи:

     1. ✅ Psychological Detachment
        → Отключи рабочий Slack до завтра

     2. ✅ Relaxation Activity
        Что выберешь?"

Quick replies:
[🚶 Walk in nature] [🧘 Meditation] [📺 Light TV]
[📱 Social media] [🏋️ Exercise] [👥 Friends]

Если выбирает Social media:
Bot: "⚠️ Экраны ухудшают recovery!
     Alpha waves не восстанавливаются 40+ мин.

     Рекомендую:
     - Nature walk (20 мин) = +16% working memory tomorrow
     - Exercise = psychological detachment
     - Friends = emotional restoration

     Хочешь попробовать что-то другое?"
```

**4 Recovery Experiences (Sonnentag):**
1. **Psychological Detachment** - mentally disengage from work
2. **Relaxation** - low activation + positive affect
3. **Mastery** - learning new skills (challenging hobby)
4. **Control** - autonomy over free time

---

### 10. Habit Formation Tracker (66-154 дня)

**Проблема:** Мы не отслеживаем формирование привычки!

**Фича: Habit Progress Tracker**

```
User выбирает recurring задачу: "тренировка 3x в неделю"

Bot: "Это привычка! Формирование займет 66-154 дня.

     Буду отслеживать:
     - Consistency (один пропуск ok, паттерн опасен)
     - Context stability (одно время/место)
     - Automaticity score (как легко стало начинать)

     Хочешь утренние или вечерние тренировки?"

User: "утренние"

Bot: "Отлично! Morning practice эффективнее.
     Когда именно? (нужен конкретный триггер)"

User: "после завтрака, 8:30"

Bot: "Perfect. Создал habit:

     ┌─ HABIT PLAN ──────────────┐
     │ Trigger: 8:30, после завтрака│
     │ Action: надеть кроссовки   │
     │ Duration target: 20 min    │
     │ Days: Mon/Wed/Fri          │
     │                            │
     │ Progress:                  │
     │ Day 1/66 ░░░░░░░░░░ 0%    │
     └───────────────────────────┘

     Каждый день в 8:30 спрошу: 'Сделал?'"

После 10 дней:
Bot: "🎉 10 дней streak!
     Automaticity check:

     Насколько легко начать тренировку? (1-10)
     [Очень сложно 1] ... [Автоматически 10]"

User: "4"

Bot: "День 10/66, автоматичность 40%.
     Нормально! Ожидаемо 60% к дню 30.

     Продолжай тот же триггер каждый день."
```

**График автоматизации:**
- Days 1-20: Rapid initial gains
- Days 21-40: Slower growth
- Days 41-66: Plateau (95% automaticity)
- Days 66-154: Full habit for complex behaviors

---

### 11. Weekly Review с Pattern Recognition

**Фича: Intelligence Layer**

```
Каждое воскресенье:

Bot: "Неделя закончена! Нашел паттерны:"

┌─ WEEKLY INSIGHTS ───────────┐
│ 📊 Your patterns:            │
│                              │
│ 🔴 Interruptions:            │
│ - Вторник 14:00-16:00 → 5 раз│
│ - Причины: встречи (60%)     │
│   Slack (40%)                │
│                              │
│ Suggestion:                  │
│ Block вторник 14-16 для      │
│ deep work, meetings после 16 │
│                              │
│ 🟢 Best performance:         │
│ - Среда 9-11 утра            │
│ - Ты выполнил 80% топ-3      │
│                              │
│ 🟡 Stuck patterns:           │
│ - "code review" = 3x /stuck  │
│ - Blocker: "скучная задача"  │
│                              │
│ Suggestion:                  │
│ Temptation bundle code review│
│ с подкастом?                 │
└─────────────────────────────┘

[Apply suggestions] [Customize]
```

**Паттерны для отслеживания:**
- Interruption frequency по времени/дню
- Task types causing most /stuck
- Energy peaks (когда выполняется больше всего)
- Recovery activities correlation с next-day performance

---

### 12. Cognitive Restructuring Assistant

**Для хронических "я не могу" мыслей:**

```
User пишет: "я не могу это сделать, слишком сложно"

Bot: "Поймал катастрофическую мысль! Хочешь разобрать?"

[Yes] [Not now]

Если Yes:

┌─ THOUGHT ANALYSIS ───────────┐
│ Automatic thought:           │
│ "Я не могу, слишком сложно"  │
│                              │
│ Evidence FOR:                │
│ [Напиши факты]               │
│                              │
│ Evidence AGAINST:            │
│ [Напиши факты]               │
│                              │
│ Alternative thought:         │
│ (Реалистичная, не позитивная)│
│                              │
│ Пример:                      │
│ "Это сложно И я могу работать│
│  над этим по 10 мин за раз"  │
└─────────────────────────────┘

Bot сохраняет в Evidence Log:
После 2 недель показывает:

"Твои предсказания vs реальность:

 Задача 1:
 Predicted difficulty: 9/10
 Actual: 6/10

 Задача 2:
 Predicted time: 3 hours
 Actual: 1.5 hours

 Паттерн: Ты переоцениваешь effort на ~40%"
```

**Научная база:**
- CBT d=0.85 для cognitive restructuring
- dlPFC modulates amygdala через semantic reinterpretation
- Привычное использование reappraisal устраняет performance impairment

---

## 🎯 Приоритизация по Impact × Feasibility

### Phase 1 (Must-Have):
1. ✅ **Implementation Intentions** - d=0.65, easy to implement
2. ✅ **Emotional Dysregulation Protocol** - critical for stress moments
3. ✅ **Recovery Intelligence** - prevents burnout
4. ✅ **Segmenter/Integrator Quiz** - personalization

### Phase 2 (High Value):
5. ✅ **Temptation Bundling** - +51%, needs strict tracking
6. ✅ **Energy-Aware Scheduling** - better TMP
7. ✅ **MCII for Big Goals** - g=0.34, weekly ritual
8. ✅ **Micro-breaks Intelligence** - prevents social media traps

### Phase 3 (Advanced):
9. ✅ **Habit Formation Tracker** - 66-154 days longitudinal
10. ✅ **Pattern Recognition** - requires data accumulation
11. ✅ **Cognitive Restructuring** - CBT lite, requires careful UX

---

## 💡 Интеграция с текущей концепцией

### Как это вписывается:

**Brain Dump Assistant** остается, но добавляем:
- Auto-detection эмоциональных слов → suggest dysregulation protocol

**Top-3 Coach** расширяется:
- IF-THEN plan для каждой задачи
- Energy classification tasks
- Temptation bundling для скучных задач

**TMP Planner** становится умнее:
- Energy-aware scheduling
- Optimal break timing (50-90 min cycles)
- Recovery blocks в конце дня

**Stuck Handler** получает:
- Procrastination Detective (6 blockers)
- Emotional recovery если нужно
- Evidence Log для cognitive restructuring

**Новые фичи:**
- Evening Recovery Coach
- Weekly Pattern Recognition
- Habit Formation Tracker
- Segmenter/Integrator personalization

---

## 📊 Научная база (Summary)

**Strongest evidence (RCTs + meta-analyses):**
- Implementation intentions: d=0.65 (94 studies)
- CBT for procrastination: g=0.55-0.93
- Box breathing: g=-0.35 stress reduction
- Nature exposure: +16-20% working memory
- MCII: g=0.34 (N=15,907)
- Psychological detachment: strongest predictor of recovery

**Moderate evidence:**
- Temptation bundling: +51% (single RCT)
- Energy-aware scheduling: observational data
- Habit formation: median 66 days (N=96)

**Emerging evidence:**
- Pattern recognition: diary studies
- Personalization by preferences: fit theory

---

## 🚀 Next Steps

1. Решить какие фичи Phase 1/2/3
2. Проработать UX для каждой
3. Написать промпты для LLM
4. Определить data schema (для tracking паттернов)
5. Создать decision tree: когда какой протокол предлагать
