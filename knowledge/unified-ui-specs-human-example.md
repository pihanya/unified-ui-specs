# 🧭 UUISS-Design-Screen-001 — “Dual-Mode Architecture Specification” — v1.0.0

**👥 Owners:** @product @design @dev • **Status:** Implemented
**🎨 Figma:** figma-screen-dual-mode-choose • **📌 Jira:** UUISS-1
**🔗 Links:** PRD-001, Personas P01/P02 • **🚀 Release:** MVP
**🧩 Platforms:** Android ≥13, iOS ≥16, Web ≥Chrome 120

## 🎯 Goal & Context

Provide a clear separation of clinical and wellness experiences so users can select the appropriate mode immediately after onboarding.
**📈 KPI targets:** Mode Select Rate ≥85%; Retention ≥25%; PHQ-9 Completion ratio = 1.0.

## 🧱 Composition

- **Regions:** `header`, `clinical_card`, `wellness_card`, `info_section`, `footer`
- **Focus order:** header → clinical_card → wellness_card → info_section → footer.settings → footer.help
- **Components:**
  - `header`: AppTitle (center), AvatarButton (right, optional)
  - `clinical_card`: ModeCard (key `clinical`)
  - `wellness_card`: ModeCard (key `wellness`)
  - `info_section`: InfoLink (optional)
  - `footer`: FooterAction (keys `settings`, `help`)
- **Design system references:** Card, ButtonPrimary, Icon, Typography/Heading, Typography/Body

## 🎨 Design Tokens

```json
{
  "source_type": "external",
  "store": "tokens.json"
}
```

## 🔀 States & Variants

- **States:** default, focus, pressed, disabled, loading, error, success
- **Variants:** light, dark

## ⚙️ Behavior Rules

- **Success state:** Show `icon/Check` at top right with slide transition (300 ms, ease-in-out).
- **Pressed state:** Apply scale 0.98 with opacity 0.8 feedback.

## 🖱️ Interactions

- `tap_card` → condition `mode.available==true` → actions `highlightCard`, `hapticLight` → navigation `route:/mode/{clinical|wellness}` → animation `slide-right-300ms`
- `swipe_cards` → condition `platform in {tablet,desktop}` → action `showNextCard` → navigation `none` → animation `fade-200ms`
- `long_press` → condition `always` → action `openModal:modeDetails` → navigation `modal:/modeDetails` → animation `scale-up-250ms`
- `keyboard_enter` → condition `focusVisible==true` → action `selectMode` → navigation `route:/mode/{clinical|wellness}` → animation `focusRingPulse-200ms`
- `tap_info` → condition `always` → action `openModal:modeDifferences` → navigation `modal:/modeDifferences` → animation `fade-in-200ms`
- `tap_footer` → condition `always` → action `ripple` → navigation `route:/settings|/help` → animation `scale-150ms`

## ♿ Accessibility

- **Touch targets:** iOS ≥44 pt, Android ≥48 dp, Web ≥24 px
- **Contrast:** Ratio ≥4.5:1 (AA)
- **Focus indicators:** Required and visible on all interactive elements
- **ARIA roles:** button, navigation; live region `polite`
- **Alternatives:** Gesture, keyboard, and voice command parity ensured

## 🌍 Internationalization

- 🗝️ **ICU keys:** app_title, mode_selection_title, mode_info, settings, help, profile, mode_clinical_title, mode_clinical_description, mode_wellness_title, mode_wellness_description, common_start, state_loading, state_error, state_ready, state_disabled, state_active, cd_app_logo, cd_profile_icon, cd_help_icon, cd_info_icon, snackbar_mode_selected_clinical, snackbar_mode_selected_wellness
- ✂️ **Constraints:** Mode descriptions ≤120 characters; allow ≥200 % text expansion
- 🔁 **RTL support:** true with mirrored layout validation
- 💬 **Tone:** Supportive, empathetic
- 🕒 **Formatting:** Locale-specific date/time/currency rules; ICU patterns in use

## 📊 Analytics

- `screen_view` — properties: source, previous_mode — owner @analytics — PII: none
- `mode_selected` — properties: selected_mode, selection_time — owner @product — PII: none
- `info_viewed` — properties: mode, view_duration — owner @design — PII: none
- `mode_switched` — properties: from_mode, to_mode — owner @dev — PII: none

## ⚠️ Edge Cases

- `no_history` → show onboarding nudge → outcome `fallback`
- `offline` → use last cached mode → outcome `fallback`
- `4xx_5xx` → retry with backoff, fallback to local data → outcome `retry`
- `partial_data` → render skeletons then hydrate → outcome `fallback`
- `multi_tap` → de-duplicate taps → outcome `silent_ignore`

## ✅ Acceptance (BDD)

**Scenario AC-001 — Successful clinical selection**

```txt
Given user is on Mode Selection
When user taps Clinical
Then app routes to Clinical Chat
And event mode_selected is logged
```

Traceability: TC-001

**Scenario AC-002 — Offline selection**

```txt
Given device is offline
When user selects a mode
Then choice is cached
And synced when network is available
```

Traceability: TC-002

**Scenario AC-003 — Contract validation**

```txt
Given invalid mode in API response
When processed
Then error state shown
And retry allowed
```

Traceability: TC-003

## 🔌 Data Contracts

- GET `/api/user/preferences` — schema `UserPreferences` — timeout 5000 ms — retry `3 retries` — offline cache ✅
- POST `/api/user/mode-selection` — schema `ModeSelection` — timeout 5000 ms — retry `3 retries` — offline cache ✅
- GET `/api/content/mode-descriptions` — schema `ModeDescriptions` — timeout 5000 ms — retry `3 retries` — offline cache ❌

## 🧭 Traceability

- 👤 **Personas:** P01 (Clinical user), P02 (Wellness user)
- 📄 **PRD:** PRD-001 (Dual-Mode Requirements)
- 📌 **Requirements:** REQ-001 (Architecture Separation)
- 🧪 **Test cases:** TC-001, TC-002, TC-003 (linked to REQ-001)
- 🎯 **KPIs:** KPI-001 (Mode Select Rate ≥85 %), KPI-002 (Retention +25 %)

## 📝 Notes

- 📚 Cites UTS UI Standard in artifact card bibliography.
- 🎨 Uses external token store (`tokens.json`) to align with organization-wide design tokens.
