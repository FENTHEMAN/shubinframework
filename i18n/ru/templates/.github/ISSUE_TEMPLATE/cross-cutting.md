---
name: Cross-cutting feature
about: Фича, затрагивающая две и более частей (frontend + backend и т.п.)
title: ''
labels: ['cross-cutting', 'feature']
assignees: ''
---

<!--
Этот Issue живёт в docs-репе как main-спека для фичи, охватывающей
несколько кодовых репо или частей. Sub-issues открываются в каждой
соответствующей кодовой/дизайн-репе по шаблону Sub-issue.
См. framework.md гл. 4.3.
-->

## Why

<!-- Один абзац: на кого влияет, какую проблему решает, почему сейчас. -->

## Scope

In:
-

Out:
-

## Affected parts

<!-- Отметьте каждую часть, требующую работы. На каждую отмеченную — sub-issue. -->

- [ ] Frontend (`acme-frontend`)
- [ ] Backend (`acme-backend`)
- [ ] Mobile (`acme-mobile`)
- [ ] Design (sub-issue в design-репе)
- [ ] Infra (`acme-infra`)
- [ ] Other:

## Sub-issues

<!-- Заполняется по мере открытия sub-issues. Формат: - [ ] repo#NNN — title. На закрытии — отметить. -->

- [ ]

## Success criteria

<!-- User-visible измеримые проверки. Не по частям. -->

-

## Decisions

<!-- ADR в этой docs-репе. -->

-

## Design

<!-- Figma-ссылка, если применимо. -->

## Closing

Этот Issue закрывается вручную, когда:

- Все sub-issues выше закрыты.
- Все указанные ADR смержены.
- Необходимые правки в ARCHITECTURE.md, GLOSSARY.md и т.п. — смержены.
