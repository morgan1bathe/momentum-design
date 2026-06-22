---
title: Presence
summary: Usage, guidelines, and accessibility for mdc-presence — a small status badge that overlays an avatar to show a user's real-time availability or activity state.
tier: 3
component: presence
---

## Overview

A presence badge is a small colored icon overlaid on or beside an avatar to communicate a user's real-time status or availability — for example active, away, in a meeting, on a call, or do-not-disturb. `mdc-presence` is sized to pair with `mdc-avatar` and `mdc-avatarbutton`; the avatar component renders it automatically when the `presence` attribute is set.

### When to use

- When an `mdc-avatar` or `mdc-avatarbutton` needs to show the represented user's current availability or activity alongside their identity artwork.
- When the application has a meaningful, up-to-date status signal sourced from user settings or system state (online, in a meeting, presenting, on PTO, etc.).

### When not to use

- When the avatar is in counter mode (representing multiple users) — presence is suppressed on counter avatars.
- When the avatar is showing a typing indicator — presence is hidden while `is-typing` is active.
- When a count, notification dot, or non-status decoration is needed. Use `mdc-badge` instead.
- When an arbitrary icon at a custom size is needed outside the avatar size set. Use `mdc-icon` instead.

## Guidelines

### Developer usage

Import and use the component via its React wrapper or directly as a custom element:

```tsx
import "@momentum-design/components/dist/components/presence/index.js"; // custom element registration
// or via React wrapper
import { Presence } from '@momentum-design/components/dist/react';
```

Standalone usage (unusual — prefer setting `presence` on `mdc-avatar`):

```html
<mdc-presence type="active" size="32"></mdc-presence>
```

Typical usage through avatar:

```html
<mdc-avatar src="/profile.jpg" presence="meeting" size="48"></mdc-avatar>
```

### Content guidance

- Resolve conflicting status signals to a single `type` in application logic before passing it to the component — the badge displays one state at a time.
- Keep presence state in sync with the underlying availability service; stale presence erodes trust in the indicator.
- Do not use presence color or icon alone to convey critical status — pair with text or an accessible label when the information is essential.

### Property/Attribute details

- **`type`**: The presence state — `active`, `away`, `away-calling`, `busy`, `dnd`, `meeting`, `on-call`, `on-device`, `on-mobile`, `pause`, `pto`, `presenting`, `quiet`, `scheduled`. Default `active`. Unknown values fall back to `active`. When multiple signals apply, resolve to a single type in application logic using the precedence order below (highest first) before passing it to the component.

| Precedence | `type` value     | Color token           | Set by   | Description                            |
| ---------- | ---------------- | --------------------- | -------- | -------------------------------------- |
| 1          | `pto`            | indicator/locked      | Manual   | Out of office / PTO                    |
| 2          | `dnd`            | indicator/attention   | Manual   | Do not disturb                         |
| 3          | `busy`           | indicator/unstable    | Manual   | Busy                                   |
| 4          | `quiet`          | indicator/locked      | Manual   | Quiet hours / away from desk           |
| 5          | `presenting`     | indicator/attention   | System   | Presenting or sharing screen           |
| 6          | `meeting`        | indicator/unstable    | System   | In a meeting                           |
| 7          | `on-call`        | indicator/unstable    | System   | On a phone call                        |
| 8          | `pause`          | indicator/locked      | System   | On call but placed on hold             |
| 9          | `away-calling`   | indicator/locked      | Manual   | Away (Calling service users)           |
| 10         | `on-device`      | indicator/locked      | System   | Active on a device                     |
| 11         | `on-mobile`      | indicator/locked      | System   | Active on a mobile device              |
| 12         | `scheduled`      | indicator/unstable    | System   | Has a currently scheduled meeting      |
| 13         | `active`         | indicator/stable      | System   | Online and available                   |
| 14         | `away`           | indicator/locked      | System   | Idle / away                            |

Presence icon colors are limited to the indicator tokens above for each type. Do not override badge colors to convey different meanings.

- **`size`**: Overall badge size in px, matched to the parent avatar — `24`, `32` (default), `48`, `64`, `72`, `88`, `124`. Invalid values fall back to `32`.
- Presence icons have a **minimum rendered size of 14px** to meet accessibility standards. For avatar sizes `24`, `32`, and `48`, the icon renders at 14px; larger avatar sizes scale the icon proportionally.

### Limitations

- The component displays one presence type at a time — precedence resolution is the consumer's responsibility.
- The badge has no built-in accessible name; status must be communicated through surrounding context or parent labeling.
- Counter avatars and typing states suppress the presence badge when rendered through `mdc-avatar` or `mdc-avatarbutton`.

## Accessibility

### Built-in features

The badge renders a presentational icon with a type-specific background color. The component does not set a role or accessible name — it is decorative unless the consumer exposes the status elsewhere.

Presence icons inside avatars meet the **14px minimum size** requirement for visibility.

#### Internal ARIA managed by the component

| Element | Attribute | Value                     |
| ------- | --------- | ------------------------- |
| Host    | `role`    | None set by the component |

### Implementation requirements

#### General

- Do not rely on presence color or icon alone to convey availability — pair with adjacent text, a tooltip, or an accessible label on the parent row when the status is critical.
- When presence is the only indicator of availability in a list row, include the status in the row's accessible name (for example through `aria-label` on the list item or a visually hidden status string).

#### Labelling

- When using `mdc-presence` standalone, provide context through a parent element's accessible name or adjacent visible text that states the user's status.
- When presence is rendered through `mdc-avatar`, override `aria-hidden="false"` on the avatar and supply an `aria-label` that includes both identity and status when no visible text is present (for example `aria-label="Jane Doe, in a meeting"`).
