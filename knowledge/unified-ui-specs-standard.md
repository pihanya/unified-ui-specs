# 📘 Unified UI Specification Standard

Grounded in:

* **ISO 9241-110:2020** (interaction principles)
* **ISO 9241-210:2019** (human-centred design)
* **WCAG 2.2 AA** + Apple HIG + Material Design accessibility baselines
* **W3C DTCG Design Tokens** as the exchange format
* **IFML (OMG)** for optional flow diagrams

This standard provides structure for **screen and flow specifications**. It maps directly to the JSON schema provided.

---

## 1. Standard Structure

Each screen design must include:

1. **Artifact Card**

   * ID: `YMTL-<Area>-<Screen>-<nnn>`
   * Version: Semantic (`vMAJOR.MINOR.PATCH`)
   * Owners: `@product`, `@design`, `@dev`
   * Status: Draft → Ready → Approved → Implemented
   * Links: Figma, Jira, PRD, Personas, Roadmap release, REQ/TC IDs
   * Platforms: iOS ≥16 / Android ≥13 / Web ≥Chrome 120

2. **Purpose & Principles**

   * Goal: User and business value
   * KPIs: Quantified, machine-verifiable (e.g. `SelectionTime p90 ≤2000ms`)
   * Journey Context: Where the screen fits
   * HCD Alignment: ISO 9241-110 principles

3. **Composition & Hierarchy**

   * Regions: IDs, required/optional, components, focus order
   * Design System references only (no ad-hoc widgets)

4. **Design Tokens**

   * Defined in W3C DTCG format
   * Inline or external reference

5. **States & Variants**

   * States: default, hover, pressed, disabled, error, loading, success
   * Variants: light/dark themes

6. **Behavior Rules**

   * Conditions for enabling/disabling components

7. **Interactions**

   * Event → Condition → UI Action → Navigation → Analytics

8. **Accessibility**

   * Touch targets: iOS ≥44pt / Android ≥48dp / Web ≥24px
   * Contrast: min 4.5:1 (AA)
   * Focus indicators mandatory
   * Gesture and keyboard alternatives required

9. **Internationalization (i18n)**

   * ICU keys required
   * RTL support mandatory
   * Tone guidelines documented

10. **Analytics Plan**

    * Event naming `object_action`
    * Minimal properties, no PII unless flagged

11. **Edge Cases**

    * Empty, timeout, retries, offline

12. **Acceptance Criteria (BDD)**

    * Gherkin scenarios with traceability to REQ and TC

13. **Data Contracts**

    * API endpoints, schema refs, retry policy, cache strategy

14. **Traceability**

    * Mapping to Personas, PRD, REQ, TC, KPIs

---

## 2. Example Screen Spec

### YMTL-Mode-001 — “Mode Selection Screen” — v1.0.0

**Owners:** @product @design @dev • **Status:** Approved
**Figma:** [Figma Link](https://www.figma.com/file/abc123/mode-selection) • **Jira:** [APP-45](https://jira.company.com/browse/APP-45)
**Links:** PRD-001, REQ-101/102, TC-201/202 • **Personas:** Commuter, Casual User
**Release:** R1-2025-Q1
**Platforms:** iOS ≥16 / Android ≥13 / Web ≥Chrome 120

---

### Goal & Context

Enable users to quickly choose **Travel Mode** or **Explore Mode** immediately after onboarding.
**KPI:** Mode Selection Time `p90 ≤2000ms` • Error Rate ≤1%.

---

### Composition

* **Header:** Title text (`screen_title`)
* **Body:** Mode cards (`travel_mode`, `explore_mode`)
* **Footer:** Primary button (`continue_button`)

**Focus Order:** Header → Mode Cards → Continue Button
**Design System Components:** Card, Button, Typography/Title

---

### Design Tokens

```json
{
  "color/primary": {"$value":"#0066FF", "$type":"color", "$description":"Primary brand color"},
  "space/md": {"$value":"16px", "$type":"space"},
  "radius/lg": {"$value":"12px", "$type":"radius"}
}
```

---

### States & Variants

* **States:** default, hover, focus, pressed, disabled, error
* **Variants:** light, dark

---

### Behavior Rules

* **Continue Button:** Enabled only when a mode is selected

---

### Interactions

* **mode_selected** → highlight card → analytics: `mode_selected`
* **continue_pressed** (if mode selected) → route `/home` → analytics: `continue_pressed`

---

### Accessibility

* **Touch Targets:** iOS ≥44pt / Android ≥48dp / Web ≥24px
* **Contrast:** ≥4.5:1 (AA)
* **ARIA Roles:** button, navigation
* **Alternatives:** gesture, keyboard, voice commands

---

### Internationalization

* **ICU Keys:** `screen_title`, `travel_mode_label`, `explore_mode_label`, `continue_button_label`
* **RTL Support:** true
* **Tone:** Supportive, clear
* **Formatting:** Locale-driven date/time/currency

---

### Analytics

* **mode_selected{mode}** — owner: @product • PII: none
* **continue_pressed{mode}** — owner: @analytics • PII: none

---

### Edge Cases

* **No selection + Continue pressed:** Button remains disabled (silent_ignore)
* **Network timeout on Continue:** Retry dialog, exponential backoff (retry)

---

### Acceptance (BDD)

**Scenario:** Successful mode selection

```
Given User is on Mode Selection Screen
When User taps Travel Mode
Then Travel card is highlighted and Continue button is enabled
```

---

### Data Contracts

* **POST /api/user/mode**
* Request: `ModeSelectionRequest` JSON schema
* Timeout: 5000ms • Retry: exponential_backoff • Cache: none

---

### Traceability

* **Personas:** Commuter, Casual User
* **PRD:** PRD-001
* **REQ:** REQ-101 (mode selection), REQ-102 (button enable rule)
* **TC:** TC-201, TC-202
* **KPI:** KPI-001 (SelectionTime ≤2000ms), KPI-002 (Error rate ≤1%)

---

## 3. Markdown Template (Reusable)

````markdown
# YMTL-<Area>-<Screen>-<nnn> — “<Screen Title>” — vX.Y.Z
**Owners:** @product @design @dev • **Status:** <Draft|Ready|Approved|Implemented>  
**Figma:** <link> • **Jira:** <link>  
**Links:** PRD <id>, REQ-xxx, TC-xxx, KPI-xxx • **Personas:** <list>  
**Release:** <MVP/Alpha/Beta/Prod>  
**Platforms:** iOS ≥16 / Android ≥13 / Web ≥Chrome 120  

## Goal & Context
<User value, business metric, KPIs>

## Composition
- Regions/components list  
- Focus order  
- Design System components  

## Design Tokens
```json
{ "token/name": { "$value":"...", "$type":"...", "$description":"..." } }
````

## States & Variants

* States: …
* Variants: …

## Behavior Rules

<Component → Condition → State>

## Interactions

<Event → Condition → UI Action → Navigation → Analytics>

## Accessibility

* Touch targets, contrast, focus, aria, gesture/keyboard/voice alternatives

## Internationalization

* ICU keys, RTL, tone, formatting rules

## Analytics

* Events, owners, PII flags

## Edge Cases

* Case → Expected behavior → Expected outcome

## Acceptance (BDD)

```
Given …
When …
Then …
```

## Data Contracts

* Endpoint, method, schema ref, retry/cache rules

## Traceability

* Personas, PRD, REQ, TC, KPI links
