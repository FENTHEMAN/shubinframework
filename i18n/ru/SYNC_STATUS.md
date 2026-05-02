# i18n/ru sync status — v2.0.0

Snapshot после этапа 2 миграции. Уровни перевода: **L1** — full quality translation; **L2** — partial sync (paths + terms + headings + TODO в начале); **L3** — stub с указанием на английский источник; **L4** — удалён, чтобы соответствовать английскому удалению.

## Root-уровень переводов

| Файл | v1 status | v2 action | Notes |
|---|---|---|---|
| `README.ru.md` (в корне репо) | translated | L1 — обновлён под v2 (3-layer model, projects.md, расширенный layout) | мирор `README.md` |

## i18n/ru/

| Файл | v1 status | v2 action | Notes |
|---|---|---|---|
| `ARCHITECTURE.md` | did not exist | L1 — переведён с актуальной EN | новый в v2 |
| `AGENTS.md` | did not exist (был `COMMON.md`) | L1 — переведён | новый в v2; ADR ref → 4.4 |
| `CONTRIBUTING.md` | did not exist | L1 — переведён | новый в v2 |
| `framework.md` | translated | L1 — переведён под v2 + stage 1 sync (4.2/4.3, гл. 12, ренамбер) | большой документ, поддерживается полностью |
| `lite.md` | translated | L1 — обновлён (graduation triggers, Projects-not-needed) | sync со stage 2 |
| `knowledge.md` | translated | L1 — переведён под v2 + stage 1 sync (главы 10–15) | большой документ, поддерживается полностью |
| `non-tech.md` | translated | L1 — обновлён (chapter 7 Projects v2 + ренамбер) | sync со stage 2 |
| `non-engineering.md` | translated | L1 — без изменений в этапе 2 | уже v2-aware из прошлой сессии |
| `projects.md` | did not exist | L3 — stub с TOC и указанием на EN | большой новый документ; полный перевод запланирован issue post-release |
| `guides/feature-cycle-example.md` | translated | L1 — переведён под v2 | sync со stage 1 |
| `templates/.github/ISSUE_TEMPLATE/*` (9 шаблонов + README + config.yml) | translated (часть в v1, часть новые) | L1 — все шаблоны зеркалятся | sync со stage 1 |
| `templates/README.md` | translated | L1 | unchanged in stage 2 |
| `templates/knowledge/AGENTS.md` | translated | L1 — sync со stage 1 (Project integration section) | sync со stage 1 |

## Не переводится

- `CHANGELOG.md` — версионная история обычно не переводится; ru-юзеры читают английский.
- `LICENSE` — стандартный MIT-текст.
- `conventions/GIT_CONVENTION.md` — внутренняя контрибьютор-конвенция; перевод низкоприоритетен.

## Issues to open после релиза

- `i18n/ru/projects.md` — full prose translation from L3 stub to L1 (~225 строк).
- Annual ru/EN drift review — раз в год сверять прозу `i18n/ru/framework.md` и `i18n/ru/knowledge.md` против root EN на предмет накопившегося дрейфа.
