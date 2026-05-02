# knowledge/AGENTS.md

Спецификация для AI-агента, который поддерживает `knowledge/wiki/`.

Этот файл описывает, как компилируется wiki. Сама wiki (`knowledge/wiki/*.md`) — пространство записи агента: её можно перегенерировать из источников в любой момент без потери информации.

Этот файл — *шаблон*, поставляемый с Shubin Framework. Скопируйте в markdown-репо команды, подключающей опциональный knowledge-модуль (`knowledge/AGENTS.md` в её репо), затем адаптируйте: в частности, заполните связанные кодовые репозитории в `knowledge/wiki/_meta.md` и обрежьте источники ingest, которые команда не использует.

## Inputs (что ingest'ить)

На каждом запуске compile агент читает:

1. **`knowledge/raw/`** — все файлы. Источник правды для внешнего материала (статьи, интервью, конкурентный анализ). Иммутабельно; новые файлы с прошлого запуска определяются по date-префиксу в имени (`YYYY-MM-DD-<slug>.md`).
2. **`decisions/`** — все ADR. Новые или изменённые с прошлого запуска определяются через `git log` (дата прошлого запуска записана в `knowledge/wiki/_meta.md`).
3. **`journal/`** — все записи (`feedback/`, `incidents/`, `retros/`). Тот же метод детекции.
4. **GitHub Issues и PRs** в связанных кодовых репозиториях — закрытые Issues и смерженные PR с прошлого запуска. Используется `gh` CLI или GitHub API. Список связанных кодовых репо лежит в `knowledge/wiki/_meta.md`.
5. **`patterns/`** — для cross-references. Когда wiki-страница обсуждает паттерн, ссылается на файл паттерна, а не пересказывает его.
6. **GitHub Project items**, если Project связан (id и field schema в `_meta.md`). Поле `Affected parts` Project'а — первичный сигнал классификации; см. секцию «Project integration» ниже.

## Outputs (что производить)

Wiki организована по **продуктовому домену**, не по типу источника и не хронологически.

У каждого домена одна страница: `knowledge/wiki/<domain>.md`. Границы доменов выводятся из поля `Affected parts` Project'а, если он есть; иначе — из лейблов Issues, заголовков ADR и кластеров feedback. Примеры: `auth.md`, `billing.md`, `onboarding.md`, `notifications.md`.

**Опциональная вторая ориентация: discovery-страницы.** Если команда ещё ведёт discovery-работу (интервью, конкурент-анализ, индустри-research), вторичные страницы живут в `knowledge/wiki/personas/`, `knowledge/wiki/competitors/` и т.п. по формату из `knowledge.md` главы 3. Они разделяют тот же compile-механизм, но источники для них в основном из `raw/`, не из Issues. Обе ориентации могут сосуществовать.

Агент **не** создаёт:

- Страницу на Issue (для этого есть GitHub — закрытый Issue и есть документ).
- Хронологический лог (для этого есть `journal/`).
- Страницу на ADR (ссылается на файл ADR напрямую).

## Формат страницы

Каждая `knowledge/wiki/<domain>.md` следует структуре:

````markdown
# <Имя домена>

**Last compiled:** YYYY-MM-DD
**Sources:** N raw файлов, M ADR, K closed Issues, L journal-записей

## Overview

Два-три абзаца, синтезирующих то, что команда знает и решила про этот домен. Собственная проза агента, опираясь на источники.

## Key decisions

- [ADR-0024: OAuth library choice](../../decisions/0024-oauth-library.md) — короткое summary
- [ADR-0031: Token rotation strategy](../../decisions/0031-token-rotation.md) — короткое summary

## Active issues and recent work

- [#142: SSO via Google](https://github.com/your-org/your-repo/issues/142) — закрыт YYYY-MM-DD
- [#198: 2FA enforcement](https://github.com/your-org/your-repo/issues/198) — закрыт YYYY-MM-DD

## External knowledge

- [[../raw/2025-11-12-oauth-vs-oidc-deep-dive]]
- [[../raw/2026-01-04-customer-interview-auth-pain]]

## Feedback and incidents

- [[../../journal/feedback/2026-02-15-customer-x-sso-blocker]]
- [[../../journal/incidents/2026-03-01-token-leak-postmortem]]

## Open questions

- (Что ещё не решено и что агент идентифицирует как пробел.)
````

## Каденс компиляции

По умолчанию: **раз в неделю**, например в понедельник утром. Триггер ad-hoc-запуска — крупные события: большой релиз, значимый инцидент, пачка новых ADR.

Механизм — запланированная сессия Claude Code, GitHub Action, дёргающий `claude --headless`, или ручной запуск человеком — не часть фреймворка. В контракт входит только input/output.

## Коммиты

Агент коммитит в ветку (например, `wiki/auto-ingest-YYYY-MM-DD`) и открывает PR с заголовком `wiki: weekly compile YYYY-MM-DD`. Body PR суммирует:

- Какие домены обновлены.
- Какие новые источники съедены (счётчики и несколько репрезентативных имён файлов).
- Какие решения добавлены или помечены superseded.
- Какой feedback и инциденты вплетены.

Человек ревьюит и мержит. Агент не пушит в `main` напрямую.

## Чего агент НЕ делает

- Не перефразирует ADR в прозовые summaries, которые могут разойтись с оригиналом. Всегда ссылается на файл ADR.
- Не выдумывает информацию, которой нет ни в одном источнике.
- Не удаляет содержимое без явной инструкции; если страница домена устарела — флагает её для ревью человеком.
- Не редактирует `knowledge/raw/`. Raw — иммутабельно.
- Не редактирует ADR. ADR — иммутабельны после acceptance.
- Не редактирует записи `journal/`. Журнал — append-only.

## Meta-файл

`knowledge/wiki/_meta.md` записывает:

- Дату последней компиляции.
- Список связанных кодовых репозиториев (для ingest Issues/PR).
- Список доменов и их соответствующих wiki-страниц.
- Известные проблемы с wiki (пробелы, конфликты), помеченные для follow-up человеком.

## Project integration

Когда GitHub Project связан (id и field schema в `_meta.md`), агент использует custom fields Project'а как первичные сигналы классификации — переопределяя эвристики по лейблам и заголовкам:

- **Affected parts** (multi-select) — самый сильный сигнал. Issue с `Affected parts: [auth, billing]` обновляет и `auth.md`, и `billing.md`.
- **Type** (single-select) — разделяет Feature, Bug, Tech debt, Spike, Doc. Формат страницы выше может расти подсекциями per-Type, когда это полезно.
- **Status** — только items в `Done` вплетаются в нарратив wiki; closed-as-`wontfix` пропускаются.
- **Iteration** — используется, когда агента спрашивают «что приземлилось в итерации N» или «начиная с итерации N».

Чтение Project через `gh`:

```
gh project item-list <number> --owner <org> --format json --limit 200 \
  --jq '.items[] | {title: .content.title, status: .Status,
                    parts: .["Affected parts"], type: .Type}'
```

Для крупных Project — пагинация или GraphQL напрямую.

Агент **не** модифицирует структуру Project — поля, views, workflows — это человеческие решения. Агент может добавлять закрытые Issues из связанных репо в Project, если auto-add workflow их пропустил, и обновлять значения per-item полей по явному запросу. Всё прочее требует действий человека.

Без связанного Project агент откатывается на: лейблы Issues, пути файлов в линкованных PR, теги journal-записей, ключевые слова заголовков ADR. Назначение домена в этом fallback-режиме промахивается ~10–20% items. Полная история интеграции — в `knowledge.md` главе 12, schema полей Project — в `projects.md`.

## Human override

Люди могут редактировать `knowledge/wiki/*.md` напрямую, когда агент ошибся в классификации или галлюцинировал. После правки человеком — добавьте однострочную заметку в этот `AGENTS.md`, уточняющую правило, чтобы следующий запуск compile её учёл. Считайте такие заметки тем способом, которым `knowledge/AGENTS.md` эволюционирует; не патчите симптомы в самой wiki.
