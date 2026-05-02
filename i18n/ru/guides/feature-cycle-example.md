# Цикл фичи: разобранный пример

Полный проход по главе 5 `framework.md` на вымышленной фиче «redesign onboarding». Числа иллюстративные, не бенчмарк.

Репозиторий — `your-org/product`. Код в двух репо: `app` (фронтенд) и `api` (бэкенд). Команда — четверо: PM (Алиса), два разработчика (Боб и Кэрол), дизайнер (Дэн).

Смысл всего примера — показать, как выглядит каждый артефакт в v2-схеме: GitHub Issues держат живой workflow, markdown держит устойчивое знание, и как шаги перемещаются между ними.

---

## Шаг 1: Idea → Issue

Алиса замечает, что 60% пользователей отваливаются на втором экране onboarding'а, а среднее время на экране 2 — 145 секунд. Поднимает это на понедельничной weekly; команда соглашается посмотреть.

В тот же день она открывает **GitHub Issue #142** в `your-org/product` по Feature Template:

```
Title: Onboarding redesign

## Why
Текущий onboarding — 60% drop-off на экране 2, среднее время на этом
экране 145 секунд. Гипотеза: слишком много полей сразу.

## Scope
TBD после обсуждения.

## Success criteria
TBD после обсуждения.

## Decisions
(пока нет)

## Design
(в работе)

Assignee: @alice
Labels:   P1, product
Milestone: May sprint
```

Это всё. Никаких PR в markdown. Никакого `features/onboarding-redesign/index.md`. Issue — это спека; лейблы и milestone несут state.

---

## Шаг 2: Discussion

Утром во вторник Алиса и Боб тратят 45 минут, заполняя scope и success criteria через комментарии и правки тела issue. Дэн подключается за UX-частью. Body issue становится:

```
## Why
Текущий onboarding — 60% drop-off на экране 2, среднее время на этом
экране 145 секунд. Гипотеза: слишком много полей сразу.

## Scope
- 4 экрана вместо 5
- Прогресс-бар сверху
- Возможность пропустить onboarding
- Сохранение прогресса (resume, если пользователь ушёл)

NOT делаем в этой итерации:
- Видео-туториал
- Персонализация по плану
- A/B-тест вариантов

## Success criteria
- Drop-off на экране 2 ниже 30% (сейчас 60%)
- Median completion time ниже 90 секунд
- Измерение через две недели после релиза

## Decisions
(пока нет)

## Design
(в работе)
```

Алиса меняет лейбл `idea` на `validated` (или что команда использует). Слово «validated» больше нигде не пишется — лейблы и есть state.

История правок («Edited by Alice») в issue показывает, когда менялся body. Комментарии-обсуждения остаются там, где им место — под issue, а не дублируются в markdown-лог.

---

## Шаг 3: ADR

Боб замечает реальный выбор: сохранение прогресса — где хранить? localStorage, поле в `users` или отдельная таблица? Cross-system, тяжело отменить → ADR.

Открывает PR в markdown-репозитории, добавляющий `decisions/0023-onboarding-progress-storage.md`:

```markdown
# ADR-0023: Хранение прогресса onboarding

## Status

Accepted — 2026-04-19

## Context

Пользователь может уйти из onboarding и вернуться позже. Нужно сохранять,
на каком шаге он и что уже заполнил.

## Options considered

### 1. localStorage
- Просто, нулевая серверная логика.
- Теряется при смене устройства.

### 2. Поле в users-таблице
- Cross-device.
- Нужна миграция.

### 3. Отдельная таблица onboarding_progress
- Чистое разделение.
- Лишняя таблица под одну фичу.

## Decision

Поле `onboarding_state JSONB` в users-таблице.

## Consequences

- Cross-device.
- Нужна миграция (см. backend PR).

## Related

- Issue #142.
```

Кэрол ревьюит, апрувит, мержит.

Боб затем правит секцию `## Decisions` в body issue:

```
## Decisions
- ADR-0023: onboarding progress storage
  (your-org/markdown-repo/decisions/0023-onboarding-progress-storage.md)
```

ADR не получает свой GitHub Issue. ADR — единицы записи решений, не работа in-flight; поле `Status` — единственный state, который у него есть. Связь однонаправленная: issue ссылается на ADR; ADR опционально упоминает issue под `## Related`, но не обязан.

Дизайн идёт параллельно: Дэн открывает Figma-проект, собирает кликабельный прототип под четыре новых экрана и вставляет ссылку на Figma в секцию `## Design` тела issue. PR под это нет — это правка тела issue.

---

## Шаг 4: Implementation

Боб берёт работу. В репо `app`:

```
git checkout -b feature/onboarding-redesign
```

Запускает Claude Code:

```
> Реализуй onboarding redesign по issue your-org/product#142.
> Прочитай тело issue: Why / Scope / Success criteria.
> ADR: decisions/0023-onboarding-progress-storage.md.
> Figma: <ссылка из секции Design в issue>.
>
> Используй Figma MCP для чтения frame'ов.
> Используй компоненты из shared/design-system.
> Сверься с patterns/forms.md.
```

Claude читает issue, ADR, паттерны. Использует Figma MCP для frame'ов и генерирует код. Боб ревьюит, дописывает loading и error states, которые Claude пропустил, пишет тесты на валидацию форм.

Открывает **PR #87** в `app`:

```
Title: feat(onboarding): redesign onboarding flow

Body:
Closes your-org/product#142

Реализует onboarding redesign по ADR-0023.
```

Кэрол открывает **PR #45** в `api` с миграцией, добавляющей `onboarding_state`:

```
Title: feat(api): add onboarding_state column

Body:
Refs your-org/product#142
```

Оба PR появляются автоматически в панели «Linked pull requests» issue #142 — никакой ручной перекрёстной ссылки, никакой markdown-правки, чтобы их зафиксировать.

CI деплоит app PR на preview-environment. Дэн открывает preview, сравнивает с Figma, оставляет три комментария в PR — про отступы и недостающий hover state. Алиса проходит флоу на preview, подтверждает, что scope соблюдён. Боб правит, пушит, preview перевыкатывается. Через две итерации Дэн и Алиса апрувят.

---

## Шаг 5: Merge

Кэрол апрувит backend-PR. Миграция катится в production через deploy-pipeline. App-PR мержится с `Closes your-org/product#142` — issue #142 закрывается автоматически. Production подхватывает изменение.

Ничего в markdown не правится, чтобы пометить, что фича уехала. «Shipped»-маркера нет — закрытый issue (с исходными Why/Scope/Success criteria, обсуждением и связанными смерженными PR) и есть исторический документ.

---

## Шаг 6: Retrospective signal

Через две недели Алиса смотрит PostHog. Числа хорошие, и есть тихий сюрприз, который стоит зафиксировать. Она открывает PR, добавляющий `journal/feedback/2026-05-12-onboarding-results.md`:

```markdown
# Onboarding redesign — результаты, две недели

Reference: your-org/product#142, ADR-0023.

## Numbers

- Drop-off на экране 2: 24% (target был ниже 30% — успех).
- Median completion: 78 секунд (target был ниже 90 — успех).
- Skip rate: 12%. Target по этому числу не было; примечательно.

## User quotes

- «Полминуты заняло, я даже не заметил.» (интервью, 2026-05-08)
- «Скипнул, потому что хотел сразу увидеть дашборд.» (тикет #214)

## Conclusions

Опция skip используется чаще ожидаемого. Стоит follow-up фичи: тизер
onboarding'а внутри дашборда для тех, кто скипнул.
```

Append-only. Файл датирован; никогда не редактируется. Если возникает follow-up фича — это новый issue.

Если бы фича уехала гладко без сюрпризов, этот шаг был бы пропущен — закрытого issue достаточно. Не пиши retro ради retro.

---

## Что произошло в каждом репо

В markdown-репозитории:

- 1 PR на ADR (`decisions/0023-onboarding-progress-storage.md`).
- 1 PR на feedback-файл (`journal/feedback/2026-05-12-onboarding-results.md`).

Два маленьких PR, оба трогают файлы, существующие ровно один раз и больше не редактируемые.

В `your-org/product` (Issues): 1 issue (#142) — открыт, обсуждён, отредактирован несколько раз, чтобы заполнить scope/decisions/design, закрыт автоматически смерженным PR.

В `app`-репо: 1 PR (#87), ветка `feature/onboarding-redesign`, две итерации после preview-ревью.

В `api`-репо: 1 PR (#45), ветка `feature/onboarding-progress`, одна миграция.

Сравнение с v1: страница `features/onboarding-redesign/index.md` с frontmatter `status: idea → validated → in-progress → shipped` и руками вписанной секцией `## Log`. Каждое изменение статуса — маленький PR; половина этих PR никогда не открывалась, потому что кто-то забывал, и feature-страницы расходились с реальностью за пару месяцев. v2 кладёт ту же информацию в issue: бесплатно, автоматически, с timestamp'ами, assignee'ями и панелью linked PRs.

Trade-off: команда должна серьёзно использовать GitHub Issues. Если у вас не открывают issue под фичи, v2 пока не подходит — сначала закройте этот разрыв.

---

## Когда реальность расходится с планом

Иногда на шаге 4 разработчик понимает, что подход из ADR не работает — идея с JSONB-полем упирается в неучтённое ограничение.

Не надо тихо менять код и забывать про ADR.

А надо:

1. Обновить `Status` ADR-0023 на `Superseded by ADR-0024`.
2. Открыть ADR-0024 с новым решением и причиной отказа от старого.
3. Поправить body issue #142, если scope изменился; добавить ссылку на новый ADR в `## Decisions`.
4. Оставить комментарий в issue #142 с резюме разворота.

Пятнадцать минут. Через шесть месяцев никто не чешет затылок, почему код говорит одно, а ADR — другое.
