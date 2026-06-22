---
title: Avatar
summary: Usage, guidelines, and accessibility for mdc-avatar — a non-interactive avatar that represents a person or group as a photo, initials, icon, or counter, with optional presence and typing indicators.
tier: 3
component: avatar
---

## Overview

An avatar is a small image or icon used to visually represent a user, profile, or identity. `mdc-avatar` is the non-interactive variant: it renders as a photo, initials, icon, or counter and stays out of the tab order by default. An optional presence indicator and typing loading state can overlay the artwork when the represented user is active or composing a message.

### When to use

- When a person or group needs a visual identity inline within content — for example in lists, cards, message rows, or participant strips.
- When the avatar is informational or decorative and does not need to be clicked or keyboard-focused.
- When a presence badge or typing indicator should appear alongside a single user's avatar artwork.

### When not to use

- When the avatar opens a profile popover, menu, or other interactive surface. Use `mdc-avatarbutton` instead.
- When representing multiple users with a numeric count and no individual identity. Use the `counter` display mode on `mdc-avatar` or `mdc-avatarbutton`, but do not pair counter avatars with presence or typing states.
- When a standalone status badge is needed without avatar artwork. Use `mdc-presence` only in combination with an avatar, or `mdc-badge` for counts and non-status decorations.

## Guidelines

### Developer usage

Import and use the component via its React wrapper or directly as a custom element:

```tsx
import "@momentum-design/components/dist/components/avatar/index.js"; // custom element registration
// or via React wrapper
import { Avatar } from '@momentum-design/components/dist/react';
```

Photo avatar with presence:

```html
<mdc-avatar src="/profile.jpg" initials="AB" presence="active" size="32"></mdc-avatar>
```

Initials avatar:

```html
<mdc-avatar initials="AB" size="48"></mdc-avatar>
```

Counter avatar (multiple participants):

```html
<mdc-avatar counter="12" size="32"></mdc-avatar>
```

### Content guidance

- **Photo** — use when a genuine or professional image is available; while the photo loads, provide `initials` as an instant placeholder when possible.
- **Initials** — use when no photo is uploaded; the component uppercases and truncates to the first two characters automatically.
- **Icon** — use for role or status cues (for example admin, moderator, or verified account icons) via `icon-name`. When no photo, initials, icon, or counter is supplied, the avatar defaults to the `user-regular` icon.
- **Counter** — use when the avatar represents multiple users; values above 99 display as `99+`, and negative values display as `0`.
- Do not rely on the avatar image alone to convey identity — pair it with adjacent visible text (name, group name) whenever possible.

### Property/Attribute details

When multiple display attributes are provided, the avatar picks what to render in this priority order:

1. **`src`** (photo) — highest priority. While loading, `initials` show as an instant placeholder when provided; otherwise the default icon is shown. On load error, the placeholder remains visible.
2. **`icon-name`** — used when `src` is not provided. If both `icon-name` and `initials` are provided (without `src`), the icon wins and `initials` are ignored, which means users may briefly see nothing while the icon library loads even though initials would have rendered instantly.
3. **`initials`** — shown when neither `src` nor `icon-name` is provided. Renders instantly.
4. **`counter`** — shown only when none of the above are provided.

Other supported attributes:

- **`size`**: Avatar diameter in px — `24` (2X-Small), `32` (X-Small, default), `48` (Small), `64` (Midsize), `72` (Large), `88` (X-Large), `124` (2X-Large). Invalid values fall back to `32`. Match size to surrounding content density — smaller sizes for compact rows, larger sizes when the avatar is a focal identity marker.
- **`presence`**: Activity status passed through to the nested `mdc-presence` badge. Accepts `active`, `away`, `away-calling`, `busy`, `dnd`, `meeting`, `on-call`, `on-device`, `on-mobile`, `pause`, `pto`, `presenting`, `quiet`, `scheduled`. Hidden when a counter is rendered or while typing.
- **`is-typing`**: When `true`, overlays a three-dot typing loading indicator on top of the current content. Hidden when the avatar is in counter mode.

### Limitations

- Counter avatars do not show a presence indicator or typing state — these are suppressed by design when `counter` is set.
- The avatar is non-interactive and non-focusable; it cannot open popovers or menus on its own.
- Display priority is fixed — supplying `src` always wins over initials, icon, or counter even when another mode was intended.

## Accessibility

### Built-in features

The avatar is treated as decorative by default — it is hidden from assistive technologies so screen readers do not announce it when it is purely visual context alongside a visible name or label.

#### Internal ARIA managed by the component

| Element       | Attribute     | Value                                                                                        |
| ------------- | ------------- | -------------------------------------------------------------------------------------------- |
| Host          | `aria-hidden` | `true` by default; consumers may set `aria-hidden="false"` when the avatar conveys meaning   |
| Photo (`img`) | `aria-hidden` | `true` (the host carries the accessible exposure when overridden)                            |

### Implementation requirements

#### General

- Prefer pairing the avatar with adjacent visible text (name, group name) so the avatar can stay decorative and the text carries the accessible name.
- Do not rely on presence color or icon alone to convey availability — expose status in surrounding text or an accessible label when the information is critical.

#### Labelling

- Override `aria-hidden="false"` only when the avatar is the sole source of identity information (for example when no name is shown next to it). Provide an `aria-label` on the host in that case.
- When `presence` is set, the presence badge is decorative; communicate availability status in adjacent text or through the parent row's accessible name when screen-reader users need the state announced.
