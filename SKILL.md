---
name: doc-first
description: |
  Mandatory trigger in the following two scenarios, applicable to all AI-assisted coding contexts:

  ① About to write any code that calls a third-party service (specific service name + implementation intent):
  WeChat, Alipay, Stripe, OpenAI, Anthropic, AWS, GCP, Azure, Firebase, Twilio,
  GitHub, Feishu, DingTalk, Amap, Google Maps, TikTok, Kuaishou, as well as any
  OAuth / SSO integration, Webhook implementation, SDK calls, or open platform onboarding.

  ② During debugging, when the following signals appear and local code logic shows no obvious issues:
  HTTP 401 / 403 / 404 / 429, signature validation failure, response fields missing or type mismatch,
  deprecated / sunset notices, SDK method not found, correct parameters but abnormal response, works locally but fails in production.
---

# doc-first

## Default State: Execution Prohibited

When third-party APIs are involved, execution is prohibited by default. Proceed only after all four clearance conditions are satisfied.

---

## Clearance Conditions (all four must be met)

**① Specific Feature Documentation Page URL**
Must be the specific page for that feature, with a domain belonging to the official vendor. Third-party sites are not accepted.
The URL path must be directly related to the feature currently being implemented.
✅ `https://stripe.com/docs/api/charges/create` — official specific page
❌ `https://stripe.com/docs` — official domain but not a specific page
❌ `https://stripe-api-guide.com/charges` — non-official domain

**② Change Record Source URL**
Must be the service's changelog, release notes, or version history page, independent of ①.
✅ `https://stripe.com/docs/upgrades`
❌ Same page as ①, or any non-official source

**③ Explicit Version Number or Page Update Date**
The following expressions are considered unsatisfied and must not appear in the clearance statement:
`latest` / `current` / `newest`
Must be a specific, verifiable value:
✅ `API version: 2024-06-20` / `v3.2.1` / `Page last updated: 2024-11-08`

**④ Breaking Change Review (must cite specific entries)**
Must be based on the change record from ②, and must cite entries that explicitly involve the current interface / feature name (must include endpoint name, field name, or path keywords).

- If a relevant change exists: cite the specific entry in the format `{date}: {change title or original key phrase}`
- If no relevant entry is found: explicitly state the date range reviewed, in the format:
  `Reviewed entries in {changelog URL} from {date} to {date}; no changes involving {endpoint/field keyword} found`

The following expressions are prohibited as answers for ④: `None` / `N/A` / `Not applicable` / `Not found`

---

## Clearance Statement Format (must be output before implementation)

```
📋 Documentation Review
Doc page:       {specific feature documentation page URL, official domain}
Changelog:      {changelog URL, official domain}
Version:        {version number or page update date — latest / current / newest are prohibited}
Breaking Change:
  {date}: {change title or original key phrase}                              ← if relevant change exists
  or: Reviewed {URL} entries from {date} to {date}; no changes involving {feature} found  ← if not found
Confirmed:
  - {specific field name / parameter name / endpoint path}
  - {list additional items if applicable}
```

---

## Execution Steps

**Step 1: Search**
```
{service name} official API documentation {current year}
{service name} official changelog {current year}
{service name} changelog {current year}
{service name} breaking changes {current year}
```
If the user has already provided documentation links, proceed directly to Step 2.

**Step 2: Verify all four clearance conditions**
If any single condition cannot be satisfied → go to "Request User Assistance".

**Step 3: Output clearance statement, then proceed with execution**

---

## Clearance Statement Invalidation Conditions

In the following three situations, an existing clearance statement is automatically invalidated and the review must be re-executed:

1. **New endpoint or feature added**: the current task involves an endpoint or feature from the same service not covered by the prior statement
2. **Debugging error triggered**: any error signal listed in ② of the description appears
3. **User explicitly requests re-review**

After invalidation, the original statement must not be reused — the full review process must be re-executed from scratch.

---

## Request User Assistance

```
📋 Documentation Review: Clearance Cannot Be Completed

Unable to confirm the following:
- {missing item — specify whether it is the doc page / changelog / version number / Breaking Change entry}

Please provide:
1. The specific documentation page URL (official domain) for {feature name}
2. The changelog or release notes URL for this service
3. The version number or update date shown on the page

Implementation or code modification will not proceed until the above is received.
```

---

## When a Debugging Error Occurs

The process is the same, in the same order: complete the clearance statement first, then decide how to modify the code.
Use the error information as input for searching the changelog:
```
{service name} {error code} {current year}
{service name} breaking changes {current year}
```

---

## Source Annotation in Code

```python
# Docs:           https://stripe.com/docs/api/charges/create (API version: 2024-06-20)
# Changelog:      https://stripe.com/docs/upgrades
# Breaking Change: Reviewed entries from 2024-01-01 to 2024-06-20; no changes involving charges/create found
# Confirmed fields: amount (integer, in cents), currency, payment_method
payload = {
    "amount": amount,
    "currency": "cny",
    "payment_method": payment_method_id,
}

# ⚠️ Depends on external specification. If issues arise, check the official changelog first.
TOKEN_EXPIRE_SECONDS = 7200
```
