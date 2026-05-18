# doc-first — Document Before Code

> **Stop guessing. Verify the docs first.**

A [DeepSeek TUI](https://github.com/deepseek-ai/deepseek-tui) skill that **prohibits execution** of any third-party API integration until four verifiable clearance conditions are met — a structured, auditable workflow that catches deprecated endpoints, stale parameters, and breaking changes *before* they ship.

---

## The Problem

AI coding assistants frequently generate API integration code from outdated training data. The result:

- HTTP 401 / 403 / 404 errors that waste debugging cycles
- Deprecated endpoints and sunset fields
- Parameter signatures that changed in a recent release
- Code that works locally but fails in production

**doc-first** eliminates this class of failure by enforcing a documentation-first gate before any implementation work begins.

---

## How It Works

The skill enforces a **four-condition clearance gate** that must pass before any API integration code is written or modified:

| # | Condition | Requirement |
|---|-----------|-------------|
| ① | **Feature Documentation URL** | Specific official docs page for the endpoint/feature being used |
| ② | **Changelog Source URL** | Independent changelog/release-notes page from the same vendor |
| ③ | **Explicit Version** | Concrete API version or page update date — `latest` / `current` not accepted |
| ④ | **Breaking Change Review** | Cited entries from ② relevant to the endpoint, or an explicit "none found" with date range |

Once all four are satisfied, the skill outputs a structured **Clearance Statement** and the AI proceeds with implementation — with verified, version-pinned source annotations.

---

## Trigger Conditions

The skill activates automatically in two scenarios:

### ① Before writing third-party integration code

Any mention of a specific service with implementation intent: Stripe, OpenAI, AWS, GCP, GitHub, WeChat Pay, Alipay, Twilio, Firebase, and 30+ more services.

### ② During debugging with API error signals

HTTP 401 / 403 / 404 / 429, signature failures, deprecated notices, SDK method-not-found, field type mismatches — even when local logic looks fine.

---

## Installation

Requires [DeepSeek TUI](https://github.com/deepseek-ai/deepseek-tui).

```bash
# Install from GitHub
deepseek skill install Felix-Alex/deepseek-doc-first-skill-EN
```

Or manually: copy `SKILL.md` into your project's `.deepseek/skills/` directory or user-level skills directory.

---

## Example Workflow

**User:** "Add Stripe payment to the checkout endpoint."

**AI (with doc-first):**
```
📋 Documentation Review
Doc page:       https://stripe.com/docs/api/payment_intents/create
Changelog:      https://stripe.com/docs/upgrades
Version:        API version: 2024-06-20
Breaking Change:
  Reviewed https://stripe.com/docs/upgrades entries from 2024-01-01 to 2024-06-20;
  no changes involving payment_intents/create found
Confirmed:
  - amount (integer, in smallest currency unit)
  - currency (ISO 4217, lowercase)
  - payment_method_types (array of strings)

→ Proceeding with implementation.
```

```python
# Docs:           https://stripe.com/docs/api/payment_intents/create (API version: 2024-06-20)
# Changelog:      https://stripe.com/docs/upgrades
# Breaking Change: Reviewed 2024-01-01 to 2024-06-20; no changes involving payment_intents/create
# Confirmed fields: amount (integer, cents), currency, payment_method_types
intent = stripe.PaymentIntent.create(
    amount=amount,
    currency="cny",
    payment_method_types=["card"],
)
```

**Without doc-first:** The AI might generate code against a deprecated API shape, burning 20 minutes of debugging before anyone notices.

---

## Supported Services

The skill covers any service with a public API, including but not limited to:

- **Payments**: Stripe, WeChat Pay, Alipay
- **AI / LLM**: OpenAI, Anthropic
- **Cloud**: AWS, GCP, Azure, Firebase
- **Communication**: Twilio, SendGrid
- **Platform**: GitHub, Feishu (Lark), DingTalk
- **Maps / Geo**: Google Maps, Amap
- **Social**: TikTok, Kuaishou

And any OAuth / SSO integration, Webhook implementation, or open-platform SDK call.

---

## Why doc-first?

| Without doc-first | With doc-first |
|---|---|
| Blind implementation from training data | Verifiable, version-pinned docs |
| "It works on my machine" | Same spec, every environment |
| Debug API errors reactively | Catch breaking changes before coding |
| No audit trail for integration decisions | Structured clearance statement in every PR |
| Guesswork on field types | Confirmed fields with types and units |

---

## License

MIT
