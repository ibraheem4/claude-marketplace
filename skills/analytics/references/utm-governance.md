# UTM governance and campaign attribution

Use this reference when creating, reviewing or measuring tagged campaign links. A UTM is useful only
when it connects a known distribution decision to a verified outcome. It is not a substitute for a
conversion event or a campaign registry.

## The model

The five standard parameters answer different questions:

| Parameter | Question | Example |
|---|---|---|
| `utm_source` | Which platform, publisher or sender produced the visit? | `linkedin`, `google`, `newsletter` |
| `utm_medium` | What channel class delivered it? | `organic_social`, `paid_social`, `email`, `cpc` |
| `utm_campaign` | Which business initiative should receive credit? | `services_evergreen`, `agent_teams_launch` |
| `utm_content` | Which placement, creative or link variant was clicked? | `profile_button`, `post_demo_v2` |
| `utm_term` | Which paid-search keyword or targeting term? | `ai_engineering_firm` |

Use `utm_source`, `utm_medium` and `utm_campaign` on every tagged external campaign link. Use
`utm_content` only when it distinguishes placements or variants worth comparing. Reserve
`utm_term` for paid keyword or targeting analysis. Google documents the same source, medium,
campaign, content and term roles, and recommends source, medium and campaign on custom URLs:
<https://support.google.com/analytics/answer/10917952>.

Use `utm_id` only when a stable campaign identifier is needed to join analytics with ad cost or an
external campaign table. Keep the human-readable `utm_campaign` too.

## Start with the decision

Before naming a parameter, write:

1. **Objective:** the business result the campaign is meant to produce.
2. **Conversion:** the observable event that represents that result.
3. **Decision:** what will change if one source, placement or creative performs better.
4. **Window:** when the campaign begins, ends or is reviewed.

If the decision is only "see how many people click," the plan is incomplete. A useful campaign
connects tagged visits to an event such as `contact_submitted`, `call_booked`, `signup_completed` or
`purchase_completed`.

## Controlled vocabulary

### Source

Name the place that sent the visitor, not the audience or campaign:

`linkedin` · `google` · `bing` · `github` · `newsletter` · `partner_acme`

- Pick one spelling per platform. Do not mix `linkedin`, `linked_in` and `li`.
- Split platforms when the distinction matters: `facebook` and `instagram`, not `meta`, if reports
  need to compare them.
- For a named partner or publication, use a stable slug rather than a person's name.

### Medium

Use a small allowlist of channel classes. A practical default:

`organic_social` · `paid_social` · `social_dm` · `email` · `cpc` · `referral` · `affiliate` · `qr`

Do not use a placement such as `profile`, `footer` or `button` as the medium. That belongs in
`utm_content`. If an analytics provider has default-channel rules, align the allowlist with those
rules before publishing. Changing a medium later can split one campaign across channel groups.

### Campaign

Name the initiative or durable funnel, not the platform:

`services_evergreen` · `ai_visibility_launch` · `founder_referrals_2026q4`

- Omit dates for durable links that should accumulate history, such as a profile button.
- Add a date or cohort when the initiative is genuinely time-bounded or materially restarts.
- Do not encode source or medium again unless the campaign itself is channel-specific.
- Never change the meaning of an existing campaign slug. Create a new one.

### Content

Name the placement or creative variant:

`profile_button` · `featured_services` · `dm_ai_products_v1` · `post_case_note_v2`

The value should explain the difference being tested. `cta1`, `blue` and `version_b` are too weak
unless the registry says what they mean.

### Formatting

- Lowercase every value.
- Use one separator everywhere. Prefer `snake_case`.
- Use ASCII letters, digits and underscores in controlled values.
- Keep values concise enough to read in a report.
- Percent-encode URLs rather than inserting spaces or punctuation manually.
- Do not put names, email addresses, phone numbers, message text, company-confidential details or
  any other personal data in a UTM. URLs leak into browser history, logs, screenshots and referrer
  data. Google's campaign guidance also prohibits sending personally identifiable information:
  <https://support.google.com/analytics/answer/1037445>.

## Where UTMs belong

Tag links distributed outside the destination property:

- paid advertisements
- email campaigns and newsletters
- organic social posts
- profile, bio and featured links
- direct messages when comparing that funnel is useful
- partner placements, affiliate links and QR codes

Do **not** put UTMs on internal navigation. Internal UTMs overwrite or fragment the acquisition
context and turn one visit into a false new campaign touch. Capture the landing parameters once,
then carry attribution through events or stored campaign context.

Do not tag links you do not control merely to make a report look complete. Ordinary untagged
referrals, search and direct visits remain valid acquisition categories.

## Link examples

### Evergreen LinkedIn profile button

```text
https://example.com/work-with-us?utm_source=linkedin&utm_medium=organic_social&utm_campaign=services_evergreen&utm_content=profile_button
```

### Qualified LinkedIn direct message

```text
https://example.com/work-with-us?utm_source=linkedin&utm_medium=social_dm&utm_campaign=services_inbound&utm_content=dm_ai_products_v1
```

### Newsletter link

```text
https://example.com/report?utm_source=founder_newsletter&utm_medium=email&utm_campaign=ai_visibility_launch&utm_content=issue_04_primary
```

### Paid search

```text
https://example.com/ai-systems?utm_source=google&utm_medium=cpc&utm_campaign=ai_systems_demand&utm_content=regulated_ops_v2&utm_term=ai_workflow_automation
```

## Campaign registry

Every published tagged link needs one shared record. A spreadsheet is acceptable; a versioned YAML
or CSV registry is better when the website and campaigns are maintained in code.

Minimum fields:

| Field | Purpose |
|---|---|
| `link_id` | Immutable identifier for this publishable link variant |
| `status` | `draft`, `active`, `paused`, `complete` |
| `owner` | Person or function responsible |
| `objective` | Business result sought |
| `conversion_event` | Event that defines success |
| `source`, `medium`, `campaign`, `content`, `term` | Exact UTM values |
| `destination_url` | Canonical untagged destination |
| `tagged_url` | Published URL |
| `starts_at`, `ends_at` | Measurement window; nullable for evergreen links |
| `decision_rule` | What changes based on the result |
| `last_verified_at` | Last successful end-to-end test |

One row represents one publishable link variant. Reuse a campaign value across related links, but
give each placement or creative its own `utm_content` and registry row.

## Capture and attribution

UTMs identify the inbound touch. Conversion events prove what happened after it. Preserve both:

- **First touch:** the first known campaign that brought the visitor.
- **Last touch:** the most recent eligible campaign before conversion.
- **Landing context:** landing page, referrer and timestamp.
- **Conversion context:** event name, page, timestamp and the campaign values available then.

Do not invent an attribution model by accident. State whether reports use first touch, last touch,
or a provider's model and what its lookback window is. Different models can produce different but
internally valid answers.

If consent is required, persist and send campaign context only under the site's approved consent
policy. Never weaken consent behavior to recover attribution.

Do not assume the analytics tool carries campaign values through a multi-page funnel. Verify it.
Umami extracts the five standard parameters from the URL and stores them with events; its UTM
report needs no extra campaign fields: <https://docs.umami.is/docs/utm>. Other tools may expose
first-touch and latest-touch properties, require explicit event properties or apply their own
lookback rules.

For conversions that enter a CRM, copy approved attribution fields into the lead or opportunity
record. Keep personal details in the CRM, not in analytics properties or the tagged URL.

## Funnel reporting

Report outcomes, not raw visits alone:

| Stage | Example measure |
|---|---|
| Distribution | Posts, messages, sends, impressions or spend from the source platform |
| Visit | Unique tagged visitors or sessions |
| Intent | Relevant CTA click or form start |
| Conversion | Successfully submitted form, booked call, signup or purchase |
| Qualification | Lead accepted as relevant |
| Commercial outcome | Proposal, closed engagement or revenue |

For each source, medium, campaign and content variant, calculate the conversion rate between the
stages the business can actually observe. Ad platforms measure distribution; the destination's
analytics measures on-site behavior; the CRM or commerce system measures qualified and commercial
outcomes. Do not force one system to answer all three jobs.

## Validation

Before publishing:

- [ ] Destination resolves and keeps the query string through redirects.
- [ ] `utm_source`, `utm_medium` and `utm_campaign` use existing controlled values.
- [ ] `utm_content` identifies a real placement or variant.
- [ ] No personal or confidential data appears in the URL.
- [ ] The registry row exists and the tagged URL matches it exactly.
- [ ] The conversion event is implemented and has an owner.

After publishing a test link:

- [ ] One consented test visit appears under the expected campaign values.
- [ ] One test conversion appears once, not zero times or twice.
- [ ] The conversion can be segmented by the expected UTM values.
- [ ] First-touch and last-touch behavior matches the written attribution model.
- [ ] Internal navigation does not add or replace UTMs.
- [ ] Redirects, scheduling tools and cross-domain steps preserve attribution or explicitly record
      where it is lost.

## Output contract

Return:

1. The decision and conversion being measured.
2. The controlled taxonomy, including any new values.
3. A table of destination and tagged URLs.
4. The campaign registry update.
5. First-touch, last-touch and consent behavior.
6. Validation evidence and timestamp.
7. Known attribution limits.
8. Publication or provider changes that still require approval.
