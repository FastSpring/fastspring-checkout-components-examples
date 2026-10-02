# Style Properties Reference

Canonical reference for every styling property exposed by the FastSpring Checkout SDK components: `fs-card`, `fs-email`, `fs-apple-pay`, `fs-coupon`, `fs-pay-button`, and `fs-disclosures`.

For setup and integration, see the [Vanilla JS Integration Guide](https://github.com/FastSpring/fastspring-checkout-components-examples/blob/main/examples/vanilla/basic/README.md). This document is a lookup reference — code examples here are illustrative, not full integrations.

---

## How styling works

Each component takes an options object with a `style` key. Inside `style`, organize overrides by **state** (`default`, `hover`, `focus`, `error`, …) and then by **style category** (`card`, `input`, `button`, …):

```js
sdk.components.create('fs-card', {
  style: {
    state: {
      default: {
        card: {
          backgroundColor: '#ffffff',
          borderRadius: '12px',
        },
        input: {
          borderColor: '#cbd5e1',
        },
      },
      focus: {
        input: {
          borderColor: '#2563eb',
        },
      },
      error: {
        input: {
          borderColor: '#dc2626',
        },
      },
    },
  },
});
```

**Precedence**, most specific wins:

1. State-specific category styles (e.g., `state.hover.card.backgroundColor`)
2. Default-state category styles (`state.default.card.backgroundColor`)
3. `globalStyles` passed to `FastSpring.init()` — applied as base defaults across every component

Anything you don't set falls back to the SDK default listed below.

---

## Card (`fs-card`)

Renders the card-number, expiry, and CVV inputs inside a secure iframe.

### Component options

Top-level options passed alongside `style`:

| Option | Type | Default | Description |
| --- | --- | --- | --- |
| `labelMode` | `'fixed'` \| `'floating'` | `'floating'` | Label placement. `'floating'` — labels start inside the input as a placeholder and animate to the top-left corner on focus or when the field has a value. `'fixed'` — labels sit above each input. |
| `hideCardHeader` | `boolean` | `false` | Hide the panel header row (credit-card icon + "Card" title) when you have your own heading. |

### `card` — the panel

The outer container for the card form.

| Property | Default | Description |
| --- | --- | --- |
| `backgroundColor` | `#ffffff` | Panel background |
| `borderColor` | `#DEE2E6` | Panel border color |
| `borderWidth` | `1px` | Panel border width |
| `borderRadius` | `8px` | Panel corner radius |
| `border` | — | Shorthand (e.g., `'2px solid navy'`); overrides `borderColor` + `borderWidth` |
| `boxShadow` | `0 2px 6px rgba(0,0,0,0.06)` | Panel shadow |
| `padding` | `0` | Inner padding |
| `width` | `100%` | Panel width — `'100%'` fills container, or a fixed value like `'500px'` |
| `maxWidth` | `680px` | Caps panel width on wide screens. Set to `'none'` to remove the cap. |
| `gap` | `0` | Gap between form rows |
| `fontSize` | `16px` | Base font size |
| `fontFamily` | `'Helvetica Neue', Helvetica, Arial, sans-serif` | Base font family |
| `color` | `#4B5563` | Base text color |
| `iconColor` | `rgb(75, 85, 99)` | Header credit-card icon color. Hidden when `hideCardHeader: true`. |

### `cardTitle` — the "Card" header

| Property | Default | Description |
| --- | --- | --- |
| `color` | `#4B5563` | Title text color |
| `fontSize` | `16px` | Title size |
| `fontWeight` | `550` | Title weight |
| `fontFamily` | `'Helvetica Neue', Helvetica, Arial, sans-serif` | Title family |

### `input` — all input fields

Use `input` to style every card field at once. Use `cardNumber`, `expiry`, or `cvv` for per-field overrides — they accept the same properties.

> **Heads-up:** depending on your session config, the SDK may also render a **Zip Code** field and a **"Save Payment Details"** checkbox alongside the card inputs. These don't have dedicated style categories yet — see [Session-driven fields](#session-driven-fields) before designing a theme.

| Property | Default | Description |
| --- | --- | --- |
| `backgroundColor` | `#ffffff` | Input background. Always applied as `background-color` (not the shorthand) to preserve card-brand badges. |
| `borderColor` | `#DEE2E6` | Border color |
| `borderRadius` | `4px` | Corner radius |
| `color` | `#4B5563` | Text color |
| `fontSize` | `16px` | Font size |
| `fontFamily` | `'Helvetica Neue', Helvetica, Arial, sans-serif` | Font family |
| `fontWeight` | `400` | Font weight |
| `height` | `48px` | Field height |
| `padding` | `0 16px` | Inner padding. In floating label mode the label follows the horizontal padding for `px`, `rem` and `%` values; font-relative units (`em`, `ex`, `ch`) are not supported for label alignment. |
| `margin` | `0` | Outer margin |
| `outline` | — | Focus-ring shorthand (e.g., `'1px solid #bfdbfe'`) |
| `outlineColor` | `rgba(0, 138, 255, 0.2)` | Focus-ring color (flat alternative to `outline`) |
| `placeholderColor` | `#7D8A9B` | Placeholder text color (flat alternative to `'::placeholder'.color`) |
| `'::placeholder'` | — | Nested placeholder styles — `color`, `fontSize`, `fontFamily`, `fontWeight` |

**Placeholder example:**

```js
input: {
  '::placeholder': {
    color: '#94a3b8',
    fontSize: '14px',
  },
}
```

### `cvvIcon` — CVV graphic

A small card-silhouette graphic next to the CVV input.

| Property | Default | Description |
| --- | --- | --- |
| `height` | `25px` | Icon height; width scales proportionally |
| `outlineColor` | `#ABB5BE` | The grey structural parts (card rectangle, circle border) |
| `accentColor` | `#008AFF` | The colored digit strokes inside the circle |
| `backgroundColor` | `transparent` | Background fill behind the icon |

### `changeCard` — "Use a different card" link

| Property | Default | Description |
| --- | --- | --- |
| `color` | `#6b7280` | Link color |
| `fontSize` | `14px` | Link size |
| `fontFamily` | `'Helvetica Neue', Helvetica, Arial, sans-serif` | Link family |

### `inlineError` — validation error messages

| Property | Default | Description |
| --- | --- | --- |
| `color` | `#EB1431` | Error text color |
| `fontSize` | `12px` | Error font size |
| `fontWeight` | `400` | Error font weight |
| `fontFamily` | `'Helvetica Neue', Helvetica, Arial, sans-serif` | Error font family (follows the component font) |
| `backgroundColor` | `transparent` | Error message background |

### Card states

Out of the box, the Card visually changes for **focus**, **error**, and **active**. Other states (`hover`) accept overrides but apply no SDK-default differentiation — set them to add your own hover styling.

| State | What changes by default |
| --- | --- |
| `focus` | Focused input gets a blue border (`#2563EB`) and a soft blue glow |
| `error` | Card border, input border, and input text all turn red (`#EB1431`) |
| `active` | Card border becomes blue (`#008aff`); title font weight bumps to `600` |
| `hover` | No visual change unless you override |

To style a state, set the same category/property under that state key:

```js
style: {
  state: {
    focus: {
      input: {
        borderColor: '#2563eb',
      },
    },
    hover: {
      card: {
        backgroundColor: '#fafafa',
      },
    },
  },
}
```

### Example — full Card customization

```js
sdk.components.create('fs-card', {
  labelMode: 'floating',
  style: {
    state: {
      default: {
        card: {
          backgroundColor: '#ffffff',
          border: '1px solid #e5e7eb',
          borderRadius: '12px',
          padding: '24px',
          fontFamily: 'Inter, sans-serif',
        },
        cardTitle: {
          color: '#0f172a',
          fontSize: '18px',
          fontWeight: '600',
        },
        input: {
          backgroundColor: '#f9fafb',
          borderColor: '#e5e7eb',
          height: '48px',
          placeholderColor: '#9ca3af',
        },
        inlineError: {
          color: '#dc2626',
          fontSize: '13px',
        },
      },
      focus: {
        input: {
          borderColor: '#2563eb',
        },
      },
      error: {
        input: {
          borderColor: '#dc2626',
        },
      },
    },
  },
}).mount('#card-element');
```

---

## Email (`fs-email`)

Collects the buyer's email address inside a secure iframe. Maps to `customer.billToContact.email` in the session. The component renders no section header — write your own heading above it on the page.

### Component options

Top-level options passed alongside `style`:

| Option | Type | Default | Description |
| --- | --- | --- | --- |
| `labelMode` | `'floating'` \| `'fixed'` | `'floating'` | Label placement. `'floating'` — label starts inside the input as a placeholder and animates to the top on focus or when the field has a value. `'fixed'` — label sits above the input. |
| `fields` | `{ email: 'auto' \| 'readonly' \| 'never' }` | `{ email: 'auto' }` | Email field visibility. `'auto'` — shown, editable, required (pre-filled if the session already has an email). `'readonly'` — shown, pre-filled, locked. `'never'` — hidden. `'readonly'` and `'never'` fall back to `'auto'` when the session has no email, since an order can't be fulfilled without one. |

### `email` — component width

There is no panel around the field — background, border, and shadow come from your page. Text color and font are set per part (`label`, `input`, `inlineError`) or once for every component via `globalStyles`.

| Property | Default | Description |
| --- | --- | --- |
| `maxWidth` | `680px` | Caps component width on wide screens. Set to `'none'` to remove the cap. |

### `label` — the field label

| Property | Default | Description |
| --- | --- | --- |
| `color` | `#4B5563` | Label text color |
| `fontSize` | `14px` | Label size |
| `fontWeight` | `400` | Label weight |
| `fontFamily` | `'Helvetica Neue', Helvetica, Arial, sans-serif` | Label family |
| `backgroundColor` | `transparent` | Label background |

### `input` — the email field

| Property | Default | Description |
| --- | --- | --- |
| `backgroundColor` | `#ffffff` | Input background |
| `borderColor` | `#DEE2E6` | Border color |
| `borderRadius` | `4px` | Corner radius |
| `color` | `#4B5563` | Text color |
| `fontSize` | `16px` | Font size |
| `fontFamily` | `'Helvetica Neue', Helvetica, Arial, sans-serif` | Font family |
| `fontWeight` | `400` | Font weight |
| `height` | `48px` | Field height |
| `padding` | `0 16px` | Inner padding |
| `margin` | `0` | Outer margin |
| `outline` | — | Focus-ring shorthand (e.g., `'1px solid #bfdbfe'`) |
| `outlineColor` | `rgba(0, 138, 255, 0.2)` | Focus-ring color (flat alternative to `outline`) |
| `errorBorderColor` | `#EB1431` | Border color in the error state |
| `placeholderColor` | `#7D8A9B` | Placeholder text color (flat alternative to `'::placeholder'.color`) |
| `'::placeholder'` | — | Nested placeholder styles — `color`, `fontSize`, `fontFamily`, `fontWeight` |

**Placeholder example:**

```js
input: {
  '::placeholder': {
    color: '#94a3b8',
    fontSize: '14px',
  },
}
```

### `inlineError` — validation error messages

| Property | Default | Description |
| --- | --- | --- |
| `color` | `#EB1431` | Error text color |
| `fontSize` | `12px` | Error font size |
| `fontWeight` | `400` | Error font weight |
| `fontFamily` | `'Helvetica Neue', Helvetica, Arial, sans-serif` | Error font family (follows the component font) |
| `backgroundColor` | `transparent` | Error message background |

### Email states

Out of the box, the Email form changes for **focus** and **error**.

| State | What changes by default | Overridable |
| --- | --- | --- |
| `focus` | Focused input gets a blue border (`#2563EB`) and a soft blue glow | `input`: `borderColor`, `backgroundColor`, `outline` |
| `error` | Input border, input text, and the floating label turn red (`#EB1431`) | `input`: `borderColor`, `backgroundColor`, `color` · `label`: `color` (tints the floating label; defaults to following `input.errorBorderColor`) · `inlineError`: all properties |
| `hover` | No visual change | — (overrides under `hover` are not applied) |

Combinations not listed are ignored. `inlineError` styling is also accepted under `default`; the `error` value wins when both are set.

To style a state, set the same category/property under that state key:

```js
style: {
  state: {
    focus: {
      input: {
        borderColor: '#2563eb',
      },
    },
    error: {
      input: {
        borderColor: '#dc2626',
      },
    },
  },
}
```

### Example — full Email customization

```js
sdk.components.create('fs-email', {
  labelMode: 'floating',
  fields: { email: 'auto' },
  style: {
    state: {
      default: {
        email: {
          maxWidth: '520px',
        },
        label: {
          color: '#374151',
          fontSize: '14px',
          fontFamily: 'Inter, sans-serif',
        },
        input: {
          fontFamily: 'Inter, sans-serif',
          backgroundColor: '#f9fafb',
          borderColor: '#e5e7eb',
          height: '48px',
          placeholderColor: '#9ca3af',
        },
        inlineError: {
          color: '#dc2626',
          fontSize: '13px',
        },
      },
      focus: {
        input: {
          borderColor: '#2563eb',
        },
      },
      error: {
        input: {
          borderColor: '#dc2626',
        },
      },
    },
  },
}).mount('#email-element');
```

---

## Apple Pay (`fs-apple-pay`)

Renders Apple's official Apple Pay button for the two-step flow. The button only appears in eligible browsers (Safari on macOS/iOS) on stores with Apple Pay enabled — elsewhere the component renders nothing and takes no space.

The button itself is drawn by Apple: its colors, label, and interaction states are governed by Apple's Human Interface Guidelines. The stylable surface is the `variant` option, the `frame` option, and two `button` properties. This component takes nothing from `globalStyles`.

### Component options

Top-level options passed alongside `style`:

| Option | Type | Default | Description |
| --- | --- | --- | --- |
| `variant` | `'black'` \| `'white'` | `'black'` | Apple button appearance. `'black'` suits light checkouts; `'white'` suits dark ones. |
| `frame` | `{ width?, maxWidth? }` | see below | The component's own box: width and cap. A layout option, so it lives beside `style`, not inside it. |

### `frame` — the component's size

| Property | Default | Description |
| --- | --- | --- |
| `width` | `100%` | Component width. Fills the container you mount it into. |
| `maxWidth` | `680px` | Caps the component width on wide screens. Every component uses the same value. `'none'` removes the cap. |

### `button` — the Apple Pay button

Maps onto Apple's public CSS custom-property surface for the real `<apple-pay-button>`. Width comes from `frame`; the button always fills it.

| Property | Default | Description |
| --- | --- | --- |
| `height` | `48px` | Button height. Apple permits a minimum of 30px. |
| `borderRadius` | `4px` | Corner radius. Apple permits 0 to 50% of the height. |

### Apple Pay states

Only the `default` state is wired. Hover/press feedback on the button is rendered natively by Apple and cannot be overridden; overrides under other state keys are ignored.

### Example — full Apple Pay customization

```js
sdk.components.create('fs-apple-pay', {
  variant: 'white',
  frame: { width: '100%', maxWidth: '480px' },
  style: {
    state: {
      default: {
        button: { height: '56px', borderRadius: '10px' },
      },
    },
  },
}).mount('#apple-pay-element');
```

---

## Pay Button (`fs-pay-button`)

Submits payment when clicked. Styles apply as CSS on the underlying `<button>` element — any valid CSS property in camelCase works.

### `button` — the button element

| Property | Default | Description |
| --- | --- | --- |
| `backgroundColor` | `#2563EB` | Button background |
| `color` | `#ffffff` | Label color |
| `border` | `solid 0px` | Shorthand border |
| `borderColor` | `#2563EB` | Border color — same as the background, so it shows only once you set a `borderWidth` or `border` |
| `borderRadius` | `8px` | Corner radius |
| `padding` | `28px` | Inner padding |
| `margin` | `0` | Outer margin |
| `width` | `400px` | Button width |
| `height` | `54px` | Button height |
| `fontFamily` | `'Helvetica Neue', Helvetica, Arial, sans-serif` | Font family |
| `fontSize` | `16px` | Font size |
| `fontWeight` | `400` | Font weight |
| `cursor` | `pointer` | Cursor style |
| `opacity` | `1` | Opacity |

### `text` — the button label

Styles applied to the `<span>` inside the button. By default, text properties inherit from the button.

| Property | Default | Description |
| --- | --- | --- |
| `color` | inherits from `button.color` | Text color |
| `fontSize` | inherits from `button.fontSize` | Text size |
| `fontWeight` | inherits from `button.fontWeight` | Text weight |
| `fontFamily` | inherits from `button.fontFamily` | Text family |

### `spinner` — loading spinner

Circular SVG arc shown while a payment is in flight.

| Property | Default | Description |
| --- | --- | --- |
| `colorStart` | `#E7E7E7` | Gradient start color (top of arc) |
| `colorMid` | `#ABB5BE` | Gradient mid-point |
| `colorEnd` | `#7D8A9B` | Gradient end color (bottom of arc) |
| `size` | `2rem` | Spinner width and height |

### `iofDisclaimer` — Brazilian IOF fees notice

Rendered only when the session's `complianceMessages` array contains `"SHOW_IOF_MESSAGE"`.

| Property | Default | Description |
| --- | --- | --- |
| `fontSize` | `.75rem` | Disclaimer font size |
| `color` | `#64748B` | Disclaimer text color |

### `gdprNotice` — GDPR data-privacy notice

Rendered for customers in GDPR-applicable countries (EU member states, UK, Norway, Switzerland, Japan). Includes auto-populated links to Terms of Service and Privacy Policy.

| Property | Default | Description |
| --- | --- | --- |
| `fontSize` | `.75rem` | Notice font size |
| `color` | `#64748B` | Notice text color |
| `linkColor` | `#2563eb` | Terms / Privacy Policy link color |
| `linkHoverColor` | `#2563EB` | Link color on hover |

### Pay Button states

| State | When applied | What changes by default |
| --- | --- | --- |
| `default` | Always | Base styles above |
| `hover` | `button:hover` | Background and border color shift to `#1E4FC0`. Other properties unchanged — override for full hover differentiation. |
| `active` | `button:active` (mouse down / tap) | No visual change unless overridden |
| `disabled` | `button:disabled` (session not loaded / payment in progress) | Opacity drops to `0.5`, cursor becomes `not-allowed` |
| `error` | Host has `[error]` attribute | No visual change unless overridden |
| `valid` | Host has `[valid]` attribute | No visual change unless overridden |
| `focus` | Button has keyboard focus | Outline `2px solid #2563EB` with `2px` offset — the same blue a focused input border uses |

### Example — full Pay Button customization

```js
sdk.components.create('fs-pay-button', {
  style: {
    state: {
      default: {
        button: {
          backgroundColor: '#16a34a',
          borderColor: '#15803d',
          borderRadius: '8px',
          width: '100%',
          fontWeight: '600',
        },
        text: {
          fontFamily: 'Inter, sans-serif',
        },
        spinner: {
          colorStart: '#dcfce7',
          colorMid:   '#86efac',
          colorEnd:   '#15803d',
          size: '1.5rem',
        },
      },
      hover: {
        button: {
          backgroundColor: '#15803d',
        },
      },
      active: {
        button: {
          backgroundColor: '#166534',
        },
      },
    },
  },
}).mount('#pay-button-element');
```

---

## Disclosures (`fs-disclosures`)

Displays the FastSpring reseller disclosure — *"Sold and fulfilled by FastSpring, an authorized reseller."* — plus Privacy Policy and Terms of Sale links sourced automatically from session data.

The Privacy Policy and Terms of Sale links populate automatically when both URLs are present in the session data. No extra configuration needed.

### `container` — the disclosure container

| Property | Default | Description |
| --- | --- | --- |
| `backgroundColor` | `transparent` | Container background |
| `color` | `#ABB5BE` | Reseller disclosure text color |
| `fontFamily` | `'Helvetica Neue', Helvetica, Arial, sans-serif` | Font family |
| `fontSize` | `12px` | Font size |
| `padding` | `12px 16px` | Inner padding |
| `rowGap` | `6px` | Gap between the reseller row and the links row |

### `link` — Privacy Policy / Terms of Sale links

| Property | Default | Description |
| --- | --- | --- |
| `color` | `#2563eb` | Link color |

### Disclosures states

| State | When applied | What changes by default |
| --- | --- | --- |
| `default` | Always | Base styles above |
| `hover` | Customer hovers over a link | No visual change unless overridden |

### Example — Disclosures customization

```js
sdk.components.create('fs-disclosures', {
  style: {
    state: {
      default: {
        container: {
          color: '#64748b',
          fontFamily: 'Inter, sans-serif',
          fontSize: '12px',
        },
        link: {
          color: '#2563eb',
        },
      },
      hover: {
        link: {
          color: '#1d4ed8',
        },
      },
    },
  },
}).mount('#disclosures-container');
```

---

## Coupon (`fs-coupon`)

Lets buyers apply, view, and remove a coupon code. In `collapsed` mode (the default) the input is hidden behind a "Have a coupon code?" toggle link; in `expanded` mode the input is always visible. Once a code is applied, the field is replaced by the applied `chip`.

### Component options

| Option | Type | Default | Description |
| --- | --- | --- | --- |
| `presentation` | `'collapsed' \| 'expanded'` | `'collapsed'` | `collapsed` hides the input behind a toggle link; `expanded` shows it inline. |
| `labelMode` | `'floating' \| 'fixed'` | `'floating'` | `floating` animates the label inside the field; `fixed` renders a static label above it (and shows a real placeholder). |

### `input` — the coupon code field

| Property | Default | Description |
| --- | --- | --- |
| `backgroundColor` | `#fff` | Input background (`background` is accepted as a legacy alias) |
| `color` | `#4b5563` | Typed text color |
| `borderColor` | `#DEE2E6` | Default border |
| `borderRadius` | `4px` | Corner radius |
| `fontSize` | `16px` | Input text size |
| `fontFamily` | `'Helvetica Neue', Helvetica, Arial, sans-serif` | Input font |
| `height` | `48px` | Input height |
| `padding` | `22px 16px 8px 16px` | Inner padding (top-heavy to seat the floating label) |
| `placeholderColor` | `transparent` | Placeholder color. Transparent by default in floating mode (the label acts as the placeholder); set a visible color when using `labelMode: 'fixed'`. |

`state.focus.input.borderColor` sets the focused border (default `#2563EB`).

`state.error.input` styles the field while the backend has rejected the code: `borderColor` and `color` (both default `#EB1431`), plus `backgroundColor` (default `#fff`) and `fontFamily` (default `'Helvetica Neue', Helvetica, Arial, sans-serif`), the resting values above. These are the same four properties `state.error.input` takes on `fs-card` and `fs-email`.

### `button` — the Apply button

| Property | Default | Description |
| --- | --- | --- |
| `background` | `#008AFF` | Button background |
| `color` | `#fff` | Label color |
| `fontWeight` | `400` | Label weight |
| `borderRadius` | `4px` | Corner radius |

`state.hover.button.background` (default `#2563EB`) and `state.disabled.button.background` (default `#84C6FF`) style the other states.

### `chip` — the applied-coupon display

The `{code} applied!` text plus an `×` remove button, shown once a coupon is applied.

| Property | Default | Description |
| --- | --- | --- |
| `background` | `transparent` | Chip background |
| `color` | `#27B2A8` | Applied text color |
| `borderRadius` | `0` | Chip corner radius |
| `padding` | `4px` | Chip padding |
| `gap` | `8px` | Gap between text and the `×` button |
| `fontFamily` | `'Helvetica Neue', Helvetica, Arial, sans-serif` | Chip font |
| `fontSize` | `12px` | Chip text size |
| `fontWeight` | `400` | Chip text weight |
| `iconSize` | `16px` | `×` icon size |
| `iconColor` | `#7D8A9B` | `×` icon color |

`state.hover.chip.iconColor` (default `#4b5563`) styles the `×` on hover.

### `inlineError` — validation error messages

Shown 4px below the input when the backend rejects a code. Same group name and properties as `fs-card` and `fs-email`, so one error style block ports across all three.

| Property | Default | Description |
| --- | --- | --- |
| `color` | `#EB1431` | Error text color |
| `fontSize` | `12px` | Error font size |
| `fontWeight` | `400` | Error font weight |
| `fontFamily` | `'Helvetica Neue', Helvetica, Arial, sans-serif` | Error font family (follows the component font) |
| `backgroundColor` | `transparent` | Error message background |

`error` is the deprecated name for this group. It still applies, logs one console warning per component, and loses to `inlineError` when both are set. Note that `inlineError.color` no longer drives the input's error border; use `state.error.input.borderColor` for that.

### `toggle` — collapsed-mode link

The "Have a coupon code?" link, shown only in `presentation: 'collapsed'`.

| Property | Default | Description |
| --- | --- | --- |
| `color` | `#008AFF` | Link color |

`state.hover.toggle.color` (default `#2563EB`) styles it on hover.

### Coupon states

| State | When applied | What changes by default |
| --- | --- | --- |
| `default` | Always | Base styles above |
| `focus` | Input focused | Border turns `#2563EB`; floating label lifts and turns blue |
| `hover` | Hover on Apply button, toggle link, or chip `×` | Button/toggle → `#2563EB`, chip icon → `#4b5563` |
| `disabled` | During the apply/clear round-trip | Apply button → `#84C6FF` |
| `error` | Backend rejected the code | Input border and typed text turn `#EB1431`; the `inlineError` message appears 4px below the field |

Under `error`, `input` accepts `borderColor`, `color`, `backgroundColor` and `fontFamily`, and `inlineError` accepts all its properties. `inlineError` styling is also accepted under `default`; the `error` value wins when both are set.

### Example — full Coupon customization

```js
sdk.components.create('fs-coupon', {
  presentation: 'expanded',
  style: {
    state: {
      default: {
        input:  { background: '#0b1220', color: '#f1f5f9', borderColor: '#283449', borderRadius: '10px', placeholderColor: '#64748b' },
        button: { background: '#6366f1', color: '#ffffff', fontWeight: '600', borderRadius: '10px' },
        chip:   { color: '#a5b4fc', iconColor: '#64748b' },
        inlineError: { color: '#fca5a5', backgroundColor: '#1f1a2e' },
        toggle: { color: '#818cf8' },
      },
      focus: { input: { borderColor: '#6366f1' } },
      error: { input: { borderColor: '#f87171', color: '#fecaca' } },
      hover: { button: { background: '#4f46e5' }, toggle: { color: '#a5b4fc' } },
      disabled: { button: { background: '#3730a3' } },
    },
  },
}).mount('#coupon-element');
```

---

## Global styles

Use `globalStyles` on `FastSpring.init()` to set defaults across every mounted component:

```js
const sdk = FastSpring.init({
  checkoutUrl: 'https://<your-store>.onfastspring.com/<your-checkout>',
  globalStyles: {
    fontFamily: 'Inter, sans-serif',
    color: '#0f172a',
    fontSize: '14px',
  },
});
```

Component-level styles always win over global styles. Anything you don't set in either place falls back to the SDK defaults documented above.

---

## A few patterns

**Match the components to your brand color.** Set `state.default.button.backgroundColor` on Pay Button and `state.focus.input.borderColor` on Card to your brand color — the two highest-impact overrides.

**Dark mode.** Override `card.backgroundColor` and `input.backgroundColor` to dark values, and bump `color` / `input.color` to a light tone. Don't forget `input.borderColor` and `card.borderColor` — they need to lift off the dark surface.

**Visible hover on the Pay Button.** The SDK doesn't apply a hover-state color shift by default. Add one:

```js
hover: {
  button: {
    backgroundColor: '#1e40af',
  },
}
```

**Custom focus ring.** Set `state.focus.input.borderColor` and (optionally) `state.default.input.outlineColor` for the ring.

**When changing the Card's base background, override the same property on hover/focus/active.** The SDK ships with light-mode state defaults (`#ffffff` on the card panel for hover and active, `#DEE2E6` on the border for hover). If you only set `state.default.card.backgroundColor` to a non-white value, the SDK's defaults will leak through on interaction and the card will appear to "flip" to white when the user hovers or focuses an input. Always pair a non-default base background with matching state overrides:

```js
state: {
  default: {
    card: {
      backgroundColor: '#1e293b',
      borderColor: '#334155',
    },
  },
  hover: {
    card: {
      backgroundColor: '#1e293b',
      borderColor: '#334155',
    },
  },
  focus: {
    card: {
      backgroundColor: '#1e293b',
      borderColor: '#334155',
    },
  },
  active: {
    card: {
      backgroundColor: '#1e293b',
      borderColor: '#475569',
    },
  },
}
```

---

## Session-driven fields

Before you commit to a theme, one thing to know: depending on your session configuration, the SDK may render additional fields beyond the style categories documented above. The two most common are a **Zip Code** input (when the session requires billing address) and a **"Save Payment Details"** checkbox (when the session permits saved payment methods).

These fields inherit base styling from `card`, `input`, and the global font/color settings, so they'll look broadly cohesive with whatever you've styled. They do **not** currently have dedicated style categories in the SDK style API — overriding `input` won't change their internal label color, background fill, or border treatment. The mismatch is most visible when your theme strays from the SDK's default light look (e.g., a dark or borderless preset).

If you need pixel-precise control over the Zip Code field or the Save Payment checkbox, contact FastSpring support — additional style hooks may be added in future SDK releases.

When testing or building presets, expect these fields to appear in the rendered output when:

- The session was created with billing address requirements → Zip Code field appears below the CVV row
- The session permits saving the payment method → "Save Payment Details" checkbox appears below the input fields

---

## Presets

Three drop-in starting points. Pick the one closest to your site's look, paste it in, then tweak the brand-relevant values (typically the Pay Button background and the focus accent). Each preset styles all three components cohesively.

### Preset 1 — Polished Light

Clean white card, modern sans-serif, blue Pay Button. The safe, professional baseline most B2B and SaaS sites land on.

```js
const sdk = FastSpring.init({
  checkoutUrl: 'https://<your-store>.onfastspring.com/<your-checkout>',
  globalStyles: {
    fontFamily: 'Inter, ui-sans-serif, system-ui, sans-serif',
    color: '#0f172a',
    fontSize: '14px',
  },
  onSessionLoaded:  (data)  => console.log('Session loaded:', data),
  onOrderCompleted: (data)  => console.log('Order completed!', data),
  onPaymentFailed:  (error) => console.error('Payment failed:', error),
});

sdk.components.create('fs-card', {
  labelMode: 'floating',
  style: {
    state: {
      default: {
        card: {
          backgroundColor: '#ffffff',
          border: '1px solid #e5e7eb',
          borderRadius: '12px',
          padding: '24px',
          boxShadow: '0 1px 2px rgba(0,0,0,0.04)',
          color: '#0f172a',
          iconColor: '#475569',
        },
        cardTitle: {
          color: '#0f172a',
          fontSize: '16px',
          fontWeight: '600',
        },
        input: {
          backgroundColor: '#ffffff',
          borderColor: '#e2e8f0',
          borderRadius: '8px',
          color: '#0f172a',
          height: '48px',
          placeholderColor: '#94a3b8',
        },
        inlineError: {
          color: '#dc2626',
          fontSize: '13px',
        },
      },
      focus: {
        input: {
          borderColor: '#2563eb',
        },
      },
      error: {
        input: {
          borderColor: '#dc2626',
        },
      },
    },
  },
}).mount('#card-element');

sdk.components.create('fs-pay-button', {
  style: {
    state: {
      default: {
        button: {
          backgroundColor: '#2563eb',
          borderColor: '#2563eb',
          color: '#ffffff',
          borderRadius: '8px',
          width: '100%',
          height: '52px',
          fontSize: '15px',
          fontWeight: '600',
        },
      },
      hover: {
        button: {
          backgroundColor: '#1d4ed8',
        },
      },
      active: {
        button: {
          backgroundColor: '#1e40af',
        },
      },
      disabled: {
        button: {
          backgroundColor: '#94a3b8',
        },
      },
    },
  },
}).mount('#pay-button-element');

sdk.components.create('fs-disclosures', {
  style: {
    state: {
      default: {
        container: {
          color: '#64748b',
          fontFamily: 'Inter, sans-serif',
          fontSize: '12px',
          padding: '12px 0',
        },
        link: {
          color: '#2563eb',
        },
      },
      hover: {
        link: {
          color: '#1d4ed8',
        },
      },
    },
  },
}).mount('#disclosures-container');
```

### Preset 2 — Dark

Dark card surface, light text, muted borders that still register against the background. Pairs well with dashboards, developer tools, and modern apps.

```js
const sdk = FastSpring.init({
  checkoutUrl: 'https://<your-store>.onfastspring.com/<your-checkout>',
  globalStyles: {
    fontFamily: 'Inter, ui-sans-serif, system-ui, sans-serif',
    color: '#f1f5f9',
    fontSize: '14px',
  },
  onSessionLoaded:  (data)  => console.log('Session loaded:', data),
  onOrderCompleted: (data)  => console.log('Order completed!', data),
  onPaymentFailed:  (error) => console.error('Payment failed:', error),
});

sdk.components.create('fs-card', {
  labelMode: 'floating',
  style: {
    state: {
      default: {
        card: {
          backgroundColor: '#1e293b',
          border: '1px solid #334155',
          borderRadius: '12px',
          padding: '24px',
          boxShadow: '0 1px 2px rgba(0,0,0,0.3)',
          color: '#f1f5f9',
          iconColor: '#94a3b8',
        },
        cardTitle: {
          color: '#f1f5f9',
          fontSize: '16px',
          fontWeight: '600',
        },
        input: {
          backgroundColor: '#0f172a',
          borderColor: '#334155',
          borderRadius: '8px',
          color: '#f1f5f9',
          height: '48px',
          placeholderColor: '#64748b',
        },
        inlineError: {
          color: '#f87171',
          fontSize: '13px',
        },
      },
      hover: {
        card: {
          backgroundColor: '#1e293b',
          borderColor: '#334155',
        },
      },
      focus: {
        card: {
          backgroundColor: '#1e293b',
          borderColor: '#334155',
        },
        input: {
          borderColor: '#38bdf8',
        },
      },
      active: {
        card: {
          backgroundColor: '#1e293b',
          borderColor: '#475569',
        },
      },
      error: {
        card: {
          backgroundColor: '#1e293b',
          borderColor: '#f87171',
        },
        input: {
          borderColor: '#f87171',
          color: '#fecaca',
        },
      },
    },
  },
}).mount('#card-element');

sdk.components.create('fs-pay-button', {
  style: {
    state: {
      default: {
        button: {
          backgroundColor: '#0ea5e9',
          borderColor: '#0ea5e9',
          color: '#0f172a',
          borderRadius: '8px',
          width: '100%',
          height: '52px',
          fontSize: '15px',
          fontWeight: '600',
        },
      },
      hover: {
        button: {
          backgroundColor: '#0284c7',
        },
      },
      active: {
        button: {
          backgroundColor: '#0369a1',
        },
      },
      disabled: {
        button: {
          backgroundColor: '#475569',
          color: '#cbd5e1',
        },
      },
    },
  },
}).mount('#pay-button-element');

sdk.components.create('fs-disclosures', {
  style: {
    state: {
      default: {
        container: {
          color: '#94a3b8',
          fontFamily: 'Inter, sans-serif',
          fontSize: '12px',
          padding: '12px 0',
        },
        link: {
          color: '#38bdf8',
        },
      },
      hover: {
        link: {
          color: '#0ea5e9',
        },
      },
    },
  },
}).mount('#disclosures-container');
```

### Preset 3 — Minimal

No card panel border, transparent surfaces, just the inputs and a flat button. The checkout reads as part of the host page rather than as a dropped-in widget. Good for landing pages and one-page checkouts that already have their own visual frame.

```js
const sdk = FastSpring.init({
  checkoutUrl: 'https://<your-store>.onfastspring.com/<your-checkout>',
  globalStyles: {
    fontFamily: 'system-ui, -apple-system, BlinkMacSystemFont, sans-serif',
    color: '#111827',
    fontSize: '14px',
  },
  onSessionLoaded:  (data)  => console.log('Session loaded:', data),
  onOrderCompleted: (data)  => console.log('Order completed!', data),
  onPaymentFailed:  (error) => console.error('Payment failed:', error),
});

sdk.components.create('fs-card', {
  labelMode: 'floating',
  hideCardHeader: true,
  style: {
    state: {
      default: {
        card: {
          backgroundColor: 'transparent',
          border: 'none',
          boxShadow: 'none',
          padding: '0',
          maxWidth: 'none',
        },
        input: {
          backgroundColor: 'transparent',
          borderColor: '#d1d5db',
          borderRadius: '0',
          color: '#111827',
          height: '44px',
          padding: '0 4px',
          placeholderColor: '#9ca3af',
        },
        inlineError: {
          color: '#b91c1c',
          fontSize: '12px',
        },
      },
      hover: {
        card: {
          backgroundColor: 'transparent',
          borderColor: 'transparent',
        },
      },
      focus: {
        card: {
          backgroundColor: 'transparent',
          borderColor: 'transparent',
        },
        input: {
          borderColor: '#111827',
        },
      },
      active: {
        card: {
          backgroundColor: 'transparent',
          borderColor: 'transparent',
        },
      },
      error: {
        card: {
          backgroundColor: 'transparent',
          borderColor: 'transparent',
        },
        input: {
          borderColor: '#b91c1c',
        },
      },
    },
  },
}).mount('#card-element');

sdk.components.create('fs-pay-button', {
  style: {
    state: {
      default: {
        button: {
          backgroundColor: '#111827',
          borderColor: '#111827',
          color: '#ffffff',
          border: 'none',
          borderRadius: '0',
          width: '100%',
          height: '48px',
          fontSize: '14px',
          fontWeight: '500',
        },
      },
      hover: {
        button: {
          backgroundColor: '#1f2937',
        },
      },
      active: {
        button: {
          backgroundColor: '#374151',
        },
      },
      disabled: {
        button: {
          backgroundColor: '#9ca3af',
        },
      },
    },
  },
}).mount('#pay-button-element');

sdk.components.create('fs-disclosures', {
  style: {
    state: {
      default: {
        container: {
          color: '#6b7280',
          fontSize: '11px',
          padding: '8px 0',
        },
        link: {
          color: '#111827',
        },
      },
      hover: {
        link: {
          color: '#374151',
        },
      },
    },
  },
}).mount('#disclosures-container');
```

---

## See also

- [Sessions API](https://developer.fastspring.com/reference/sessions-overview) — server-side session creation
