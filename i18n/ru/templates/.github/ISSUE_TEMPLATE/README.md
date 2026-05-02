# Issue Templates для Shubin Framework

Скопируйте в `.github/ISSUE_TEMPLATE/` соответствующих репо проекта.

**Для кодовых репо** (frontend, backend, mobile, infra):

- `feature.md` — основной шаблон фичи, обязателен для P0 и P1 однопартовых.
- `feature-light.md` — для P2-фичей и небольших улучшений.
- `bug.md` — баг-репорты.
- `sub-issue.md` — sub-issue, относящийся к родительскому Issue (обычно cross-cutting Issue в docs-репе).

**Для docs-репы (vault):**

- `cross-cutting.md` — main Issue для фичей, затрагивающих две и более частей.
- `vault-task.md` — docs-only работа (написать гайд, поправить GLOSSARY, перевод).
- `adr-initiative.md` — крупная ADR-инициатива с несколькими PR.
- `retro-item.md` — следствие из ретроспективы.

`config.yml` включает blank issues и даёт ссылки на Discussions и docs-репу. URL поправьте под свою организацию и продукт.

Правила фреймворка о том, какой шаблон когда применять (и почему cross-cutting Issues живут в docs-репе) — в `framework.md`, гл. 4: 4.2 *Where Issues live* и 4.3 *Cross-cutting features*.

## Будущая тулза

Планируется CLI-расширение `gh-shubin`, которое будет создавать Issues из этих шаблонов с предзаполненными полями, линковать sub-issues между репо и обновлять поля в Project одной командой. Пока — вручную через GitHub UI или `gh issue create --template <name>`.
