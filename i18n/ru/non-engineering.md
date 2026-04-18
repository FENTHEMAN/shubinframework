# Shubin Framework: Non-engineering concerns

**Полное название:** Минимум по Finance, Legal, Security, Data и Support для команды 2–8
**Часть:** Shubin Framework (companion document)
**Версия:** 1.0

**Для кого:** tech lead или founder команды 2–8 человек, которая до PMF и стабильного traction. Документ отвечает на вопрос «что минимально надо сделать в этих зонах, чтобы не попасть в катастрофу, и что можно отложить».

**Что это:** краткий набор минимальных практик по зонам, которые не относятся к core engineering, но которыми всё равно нужно заниматься.

**Что это НЕ:** полный playbook по Finance, Legal, Compliance или Security. Для команды с enterprise-клиентами, чувствительными к compliance, этого недостаточно — там нужны настоящие специалисты.

**Связанные документы:**
- `framework.md` — полный фреймворк

---

## Философия

Finance, Legal, Compliance, Support, Data для команды 2–8 — это **не основная деятельность**. Полные практики нужны после того, как вы нашли PMF, собрали traction и стали нанимать. Пока — минимальный набор.

Главная ошибка на этой стадии — две крайности:
1. **Cargo cult enterprise.** Команда 3 человек делает annual security review, SOC2, threat modelling, подписки на legal tools за $2K/мес. Результат: бюджет съеден, а реально важного — не сделано.
2. **«Сначала PMF, потом всё это».** Ноль security-практик, пароли в git, privacy policy скопирована с конкурента. Первый же incident — катастрофа.

Этот документ — про средний путь. Минимум, который реально защищает, без театра.

---

## 1. Finance

Что реально нужно:

**Таблица всех подписок сервисов** в vault или Google Sheets. Что платите, сколько, кому, когда renewal, кто owner. Пересматривается ежеквартально.

```markdown
| Service | Plan | $/month | Owner | Purpose | Renewal |
|---------|------|---------|-------|---------|---------|
| Vercel | Pro | 20 | @alice | Frontend | monthly |
| Supabase | Pro | 25 | @bob | DB | monthly |
```

**Burn rate и runway** в Google Sheets. Пересматриваются ежемесячно основателем. Если runway < 12 месяцев — начинайте думать о fundraise или cut costs. < 9 — активно действуйте. < 6 — cut costs не откладывая.

**Unit economics на pre-PMF стадии** — это фантазия с двумя неизвестными. LTV вы не знаете без стабильной истории оттока, true CAC — без стабильной воронки. Не тратьте время на красивые LTV/CAC = 6.2x до появления реального стабильного оттока. До PMF достаточно знать: ARPU, gross margin, burn. Этого хватает для sanity-check.

**Значимые финансовые решения** (pricing, major траты) — это ADR. См. `framework.md` глава 3.

---

## 2. Legal

Минимум для SaaS:

**Privacy Policy и Terms of Service.** Не пишите с нуля у юриста ($500–2000 и недели). Начните с шаблонов (Termly, iubenda, GetTerms) за $10–30, адаптируйте под ваш продукт. У юриста адаптируете, когда появятся первые enterprise-клиенты.

**IP assignment clause** во всех контрактах с сотрудниками и контракторами. Без этого код, который они пишут, юридически не ваш. Это одна строчка в контракте, без неё — серьёзная проблема при fundraise или acquisition.

**GDPR data export и deletion** как технические фичи, если у вас есть EU-пользователи. Это 1–2 спринта работы, не compliance program. Всё, что нужно:
- Пользователь может попросить дамп своих данных (export) — вы высылаете JSON или CSV
- Пользователь может попросить удалить все данные (deletion) — вы реально удаляете, не soft-delete

**Чего не делать до времени:**
- SOC 2 — только когда enterprise-клиенты его требуют (обычно при $1M+ ARR)
- ISO 27001 — при enterprise в Европе
- HIPAA — при health-data
- Trademarks — при появлении дохода и защитных нужд ($500–2000 + 6–12 месяцев процесса)
- Patents — почти никогда для software startup, исключение для deep-tech

Если ваш продукт касается денег (платежи, крипто, lending) или здоровья — legal-минимум сильно больше, это не покрыто этим документом, наймите специалиста.

---

## 3. Security

### 3.1. Минимум с первого дня

**Secrets в менеджере, не в git.** Doppler, 1Password Secrets Automation, или аналог. Никогда в git даже в private repo. Никогда в Slack/Discord. Разные секреты для dev/staging/prod.

**Dependabot или Renovate** для автоматических dependency updates. Supply chain attacks — реальная угроза 2026 года.

**HTTPS везде.** В 2026 это автоматически через хостинг-платформы (Vercel, Cloudflare). Проверить, что нигде не забыли.

**Пароли через argon2 или bcrypt**, никогда plain text. Если используете managed auth (Supabase, Auth0, Clerk) — это сделано за вас.

**Row-Level Security в Postgres** (или эквивалент в другой БД) для любой мультипользовательской системы. Одна из самых частых причин утечек — «запрос без WHERE user_id = ...».

**OWASP Top 10 быстрый проход по чек-листу** — полдня на чтение чек-листа и сверку. Закрытие найденных дыр — отдельная работа, может занять несколько дней, но это разово.

**Privacy policy и basic GDPR-compliance** (см. раздел 2) — не только legal, это и security-гигиена.

Всё это — 1–2 недели работы в первый месяц, с возвратами на мелкие доработки. Не 1 день, как часто обещают. И не 3 месяца.

### 3.2. Secrets rotation — честно

Советы «ротация каждые 90 дней» звучат красиво, но в команде 4 человека это часто пропускается, и вы живёте в иллюзии ротации. Лучше:
- Реальная ротация каждые 6 месяцев — и действительно делайте
- Обязательная ротация при ротации членов команды (ушёл человек — сменили prod-секреты в течение 24 часов)

### 3.3. Incident response

Минимум процесса (пишется в `log/incidents/README.md`):
1. Кто может объявить incident (любой член команды)
2. Кто становится owner'ом (обычно tech lead)
3. Communication channel (отдельный Slack channel при incident'е)
4. Priorities: contain → eradicate → recover → learn
5. Postmortem в течение 72 часов — обязательно, blameless

Каждый incident → запись в `log/incidents/YYYY-MM-DD-name.md` с timeline, root cause, prevention.

### 3.4. Чего не делать до времени

Threat model, SBOM, annual pen-test, bug bounty, full security review — нужны команде 15+ или при обработке денег/health в серьёзном объёме. До этого — over-engineering, потраченное время на документы, которые не меняют поведение.

Исключения (когда security-минимум больше): финтех, медтех, любые операции с деньгами клиентов. Там это не companion-документ, это core engineering — наймите security-специалиста.

---

## 4. Data и аналитика

### 4.1. Единственное, что критично с первого дня — event taxonomy

Без неё через полгода у вас свалка из `user_signup`, `user_signed_up`, `signup_completed`, `new_user_registered` — все разной давности и чуть разной семантики. Никто не помнит, чем они отличаются. Аналитика лжёт.

Положите `stable/events.md`:

```markdown
# Event Taxonomy

## Правила именования
- snake_case
- <object>_<past_tense_action>
- Примеры: user_signed_up, subscription_cancelled, invoice_paid

## Обязательные свойства любого события
- user_id (или anonymous_id если не авторизован)
- session_id
- timestamp
- source (web, mobile, api)

## События

### user_signed_up
Когда: после подтверждения email
Свойства: plan, referrer, signup_method

### subscription_created
Когда: после первого успешного платежа
Свойства: plan, amount, currency
```

### 4.2. Минимальный стек аналитики

- **PostHog** для product analytics (или Amplitude, Mixpanel — выберите один)
- **Plausible / Fathom** для web analytics (privacy-first, дешевле GA)
- **Sentry** для error tracking (это уже в security-минимуме)

Один product analytics, не три. Иначе данные расходятся.

### 4.3. North Star и поддерживающие

`stable/north-star.md` — **одна** главная метрика, 3–5 leading indicators. Не 15. Пятнадцать метрик — это отсутствие метрик.

Примеры North Star:
- SaaS: Weekly Active Users, engaged in core action
- Marketplace: Transactions completed per week
- Media: Minutes consumed per user per week

### 4.4. Про стоимость

PostHog на scale начинает стоить ощутимо ($450+/мес при 1M events). Закладывайте в бюджет на год вперёд. Дешёвая альтернатива — self-hosted PostHog на $20 VPS, но это операционный долг.

### 4.5. Чего не делать до времени

- Data warehouse (Snowflake, BigQuery) — не нужен до 100K MAU или $1M ARR
- ETL-пайплайны (Airbyte, Fivetran) — тем более
- Отдельная Data-роль — только после 10K+ пользователей и появления реальных data-вопросов

Для работы с аналитикой на стадии MVP достаточно встроенных dashboard'ов PostHog + ad-hoc SQL когда нужно.

---

## 5. Support

### 5.1. Когда нужен отдельный support

- До 20 тикетов/неделю — разработчики по очереди или founder. Медленно, но даёт прямой signal.
- 20–100 тикетов/неделю — первый support-инженер (часто part-time).
- 100+ — выделенная команда + self-service knowledge base.

### 5.2. Feedback loop — обязательный

Самое важное из support — **связь с продуктом**. Support видит больше боли пользователей, чем PM и разработчики вместе. Эта боль должна попадать в vault.

Workflow:
1. Support-агент получает тикет
2. Если это сигнал (не one-off), создаёт запись в `log/feedback/YYYY-MM-DD-<theme>.md` с описанием и ссылкой на тикет
3. Раз в неделю PM пробегает эти файлы и превращает паттерны в гипотезы в `active/features/` или в wiki (если используется knowledge-модуль)

Без этого loop вы пишете фичи вслепую.

### 5.3. Triage по уровням

Минимальный:

```markdown
## P0 — критично (урон/downtime/security)
Response: 1 час, all hands

## P1 — feature broken для многих
Response: 4 часа, same day resolution

## P2 — broken для одного, workaround есть
Response: 24 часа, next sprint

## P3 — feature request, UX улучшение
Response: 48 часов, prioritized в roadmap
```

Это **внутренние** SLA, не публичные.

### 5.4. Публичные SLA — осторожно

Публичные SLA (99.9% uptime, 30-минутный P0 response) — **только когда готовы платить за нарушение**. Иначе это рекламное обещание, которое скомпрометирует доверие при первом нарушении.

Команда 4 не может обеспечить 30-минутный P0 response без on-call rotation с minimum 3 людьми. Если on-call rotation нет — не обещайте.

---

## 6. Compliance

Для команды 2–8 на стадии до PMF — никакой compliance program. Требования compliance возникают от конкретных клиентов или регуляторов. Пока их нет — вы не знаете, что именно нужно, и любая «подготовка к compliance» будет потраченным временем.

Единственное, что стоит делать проактивно:
- **GDPR data export/deletion** — если EU-пользователи (см. Legal)
- **Subprocessors list** — список всех, кому передаёте данные (Vercel, Supabase, PostHog, etc). Нужно будет для первого enterprise-клиента и для корректного privacy policy.
- **Access log для production secrets** — кто и когда смотрел prod-секреты. Простая таблица в vault, обновляется при каждом доступе.

Всё остальное (SOC 2, ISO, HIPAA) — когда конкретный клиент попросит.

---

## 7. Интеграция с основным фреймворком

- **Financial, Legal, Security ADRs** живут в общем `active/decisions/`, нумерация сквозная с техническими.
- **Event taxonomy** — в `stable/events.md`.
- **Subscriptions table** — либо в `stable/subscriptions.md`, либо в отдельной Google Sheets с ссылкой.
- **Incidents** — в `log/incidents/`.
- **Feedback от support** — в `log/feedback/`.
- **Security руководства**, если разрастутся, — в `active/patterns/security/`.

Всё это органично вписывается в `stable/active/log/` структуру. Не нужно создавать отдельные top-level папки `finance/`, `legal/`, `security/` — это cargo cult.

---

## 8. Ежеквартальный review

Раз в квартал на strategy offsite (см. `framework.md` глава 10) — 30 минут на non-engineering concerns:

- Subscriptions table — есть ли что отменить
- Runway — где мы
- Privacy policy / terms — были ли изменения продукта, требующие обновления
- Security — были ли incidents, что вынесли
- North Star — всё ещё актуальна

Не отдельный security review, отдельный compliance review, отдельный finance review — всё в 30 минут в рамках общего offsite. Для команды 2–8 этого достаточно.

---

## Финальное

Non-engineering concerns для команды 2–8 — это зона, где **меньше часто лучше больше**. Каждая лишняя практика — это время, вычтенное из построения продукта. Каждая отсутствующая критичная практика — это риск катастрофы.

Этот документ — попытка провести границу между критичным минимумом и over-engineering. Если ваш продукт специфический (финтех, медтех, enterprise-deal с первого дня) — граница сдвигается, и вам нужны специалисты, а не companion-документ.

Для типичного B2B или B2C SaaS на стадии до $1M ARR — этого достаточно.
