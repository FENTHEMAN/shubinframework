# Цикл фичи: разобранный пример

Полный проход по главе 5 `framework.md` на вымышленной фиче «redesign onboarding». Числа иллюстративные, не бенчмарк.

Репозиторий — `your-org/product`. Код в двух репо: `app` (фронтенд) и `api` (бэкенд). Команда — четверо: PM (Алиса), два разработчика (Боб и Кэрол), дизайнер (Дэн).

Смысл всего примера — показать, как выглядит каждый артефакт в v2-схеме: markdown держит спеку, GitHub держит state, и как шаги перемещаются между ними.

---

## Шаг 1: Idea

Алиса замечает, что 60% пользователей отваливаются на втором экране onboarding'а, а среднее время на экране 2 — 145 секунд. Поднимает это на понедельничной weekly; команда соглашается посмотреть.

В тот же день она делает две вещи параллельно.

Открывает **GitHub Issue #142**:

```
Title: Onboarding redesign

Body:
60% drop-off на экране 2 onboarding. Гипотеза: слишком много полей.
Spec: features/onboarding-redesign/index.md

Assignee: @alice
Labels:   P1, product
Milestone: May sprint
```

Создаёт `features/onboarding-redesign/index.md`:

```markdown
# Onboarding redesign

**Tracking:** [#142](https://github.com/your-org/product/issues/142)

## Why

Текущий onboarding — 60% drop-off на втором экране. Гипотеза:
слишком много полей сразу.

## Scope

TBD после обсуждения.
```

Открывает PR в markdown-репозитории с этим файлом. Body PR ссылается на #142, но не закрывает — issue остаётся открытым до релиза.

Заметьте, чего *нет* в markdown: ни `status: idea`, ни `created: 2026-04-18`, ни секции `## Log`. State работы — что это *idea*, не *spec*, не *in-progress* — это state issue #142 плюс метки на нём. Markdown держит прозу; GitHub держит state.

---

## Шаг 2: Spec

Утром во вторник Алиса и Боб тратят 45 минут на заполнение спеки. Дэн подключается за UX-частью. Обновляют тот же `index.md`:

```markdown
# Onboarding redesign

**Tracking:** [#142](https://github.com/your-org/product/issues/142)

## Why

Текущий onboarding — 60% drop-off на втором экране. Гипотеза:
слишком много полей сразу.

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

PR заапрувлен, смержен. Алиса меняет на issue лейбл `idea` на `validated` (или что команда использует). Слово «validated» нигде в markdown не пишется.

---

## Шаг 3: ADR

Боб замечает: сохранение прогресса — где хранить? localStorage? БД? Cross-system решение, сложно отменить → ADR.

Пишет `decisions/0023-onboarding-progress-storage.md`:

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
- Миграция.

### 3. Отдельная таблица onboarding_progress

- Чистое разделение.
- Лишняя таблица под одну фичу.

## Decision

Поле `onboarding_state JSONB` в users-таблице.

## Consequences

- Cross-device.
- Нужна миграция (см. backend PR).

## Related

- features/onboarding-redesign/
- Tracking: #142
```

Открывает PR с этим файлом. Кэрол ревьюит, апрувит, мержит. Боб обновляет секцию `## Decisions` спеки на:

```markdown
## Decisions

- ADR-0023: onboarding progress storage
```

ADR не получает свой GitHub Issue. ADR — единицы записи решений, не работа in-flight. Поле `Status` — часть иммутабельного документа, единственный state, который у него есть.

---

## Шаг 4: Design

Дэн открывает Figma-проект, создаёт новый файл `[Feature] Onboarding`, кладёт четыре frame'а в секцию `In progress`, собирает кликабельный прототип.

Боб обновляет секцию `## Design` спеки:

```markdown
## Design

Figma: https://figma.com/file/.../Onboarding

- Секция «In progress» — финальные экраны
- Прототип: Play mode для демонстрации переходов
```

Маленький PR, смержен. Issue #142 не меняется — design часть спеки, не отдельный state.

---

## Шаг 5: Implementation

Боб берёт работу. В репо `app`:

```
git checkout -b feature/onboarding-redesign
```

Запускает Claude Code:

```
> Реализуй onboarding redesign. Контекст:
> - features/onboarding-redesign/index.md
> - decisions/0023-onboarding-progress-storage.md
> - Figma: <ссылка из index.md>
>
> Используй Figma MCP для чтения frame'ов.
> Используй компоненты из shared/design-system.
> Сверься с patterns/forms.md.
```

Claude читает спеку, ADR, паттерны. Использует Figma MCP для frame'ов и генерирует код. Боб ревьюит, дописывает loading и error states, которые Claude пропустил, пишет тесты на валидацию форм.

Открывает **PR #87** в `app`:

```
Title: feat(onboarding): redesign onboarding flow

Body:
Closes your-org/product#142

Реализует features/onboarding-redesign по ADR-0023.
```

Кэрол открывает **PR #45** в `api` с миграцией, добавляющей `onboarding_state`:

```
Title: feat(api): add onboarding_state column

Body:
Refs your-org/product#142
```

Боб **не** редактирует спек-страницу, добавляя секцию `## Implementation` с PR-ссылками. Панель «Linked pull requests» в issue #142 показывает их автоматически. Спека остаётся stateless.

---

## Шаг 6: Preview + review

CI деплоит app PR на preview-environment. Дэн открывает preview, сравнивает с Figma, оставляет три комментария в PR — про отступы и недостающий hover state. Алиса проходит флоу, подтверждает, что scope соблюдён. Боб правит, пушит, preview перевыкатывается. Через две итерации Дэн и Алиса апрувят.

---

## Шаг 7: Merge + deploy

Кэрол апрувит backend-PR. Миграция катится в production через deploy-pipeline. App-PR мержится с `Closes your-org/product#142` — issue #142 закрывается автоматически. Production подхватывает изменение.

Боб **не** редактирует `features/onboarding-redesign/index.md`, чтобы пометить, что фича уехала. В markdown нет «shipped» маркера. State issue (closed) и связанных PR (merged) — источник правды. Спек-страница теперь историческая: описывает, что построили, и остаётся такой.

---

## Шаг 8: Feedback loop

Через две недели Алиса смотрит PostHog. Создаёт `journal/feedback/2026-05-12-onboarding-results.md`:

```markdown
# Onboarding redesign — результаты, две недели

Reference: features/onboarding-redesign/, issue #142.

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

Append-only. Файл датирован; никогда не редактируется. Если возникает follow-up фича — это новая фича с новой issue и новой спек-страницей.

---

## Что произошло в каждом репо

В markdown-репозитории:

- 1 PR на черновик спеки (title + Why)
- 1 PR на scope и success criteria
- 1 PR на ADR
- 1 PR на Figma-ссылку и упоминание решения
- 1 PR на feedback-файл

Пять маленьких PR, ни один из них не трогает спек-страницу после шага 4. Спека финализирована на шаге 4 и остаётся как есть навсегда — или замещается будущей feature-страницей.

В `app`-репо: 1 PR (#87), ветка `feature/onboarding-redesign`, две итерации после preview-ревью.

В `api`-репо: 1 PR (#45), ветка `feature/onboarding-progress`, одна миграция.

---

## Сравнение с v1

В v1 спек-страница несла frontmatter `status: idea` → `validated` → `in-progress` → `shipped`, плюс секцию `## Log` с руками вписанными датами («2026-04-18: создано, 2026-04-19: ADR принят, ...»). Каждое изменение статуса было маленьким PR. Половина этих PR никогда не открывалась, потому что кто-то забывал, и feature-страницы расходились с реальностью за пару месяцев.

В v2 та же информация в GitHub Issues — бесплатно, автоматически, с timestamp'ами, с assignee'ями, с панелью linked PRs. Спек-страница держит то, чего GitHub не может: прозу *зачем* и *что*.

Trade-off: команда должна серьёзно использовать GitHub Issues. Если у вас не открывают issue под фичи, v1-стиль ближе к вашей реальности — и фреймворк, скорее всего, пока не подходит.

---

## Когда реальность расходится с планом

Иногда на шаге 5 разработчик понимает, что подход из ADR не работает — например, идея с JSONB-полем упирается в неучтённое ограничение.

Не надо тихо менять код и забывать про ADR.

А надо:

1. Обновить `Status` ADR-0023 на `Superseded by ADR-0024`.
2. Создать ADR-0024 с новым решением и причиной отказа от старого.
3. Обновить `features/onboarding-redesign/index.md`, если scope изменился.
4. Отметить изменение как комментарий в issue #142 — не как руками вписанную строку лога в markdown.

Пятнадцать минут. Через шесть месяцев никто не чешет затылок, почему код говорит одно, а ADR — другое.
