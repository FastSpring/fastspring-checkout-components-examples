# Style Properties Reference

Canonical reference for every styling property exposed by the FastSpring Checkout SDK components: `fs-card`, `fs-pay-button`, and `fs-disclosures`.

For setup and integration, see the [Vanilla JS Integration Guide](./README.md). This document is a lookup reference — code examples here are illustrative, not full integrations.

---

## How styling works

Styling is configured through a `style` object passed at component creation:

```js
sdk.components.create('fs-card', {
    style: {
        state: {
            default: { /* base styles */ },
            hover:   { /* applied on hover */ },
            focus:   { /* applied on focus */ },
            error:   { /* applied in error state */ },
        },
    },
});
```

Each state contains one or more **style categories** (`card`, `input`, `button`, etc.) that group related properties. The available categories vary per component — see the per-component sections below.

**Style precedence** (most specific wins):

1. State-specific category styles (e.g., `state.hover.card`)
2. Default category styles (`state.default.card`)
3. `globalStyles` passed to `FastSpring.init()` — applied as base defaults across all components

---

## Card (`fs-card`)

Renders the payment fields (card number, expiry, CVV) inside a secure iframe.

### Component options

Top-level options passed alongside `style`:

| Option | Type | Default | Description |
|---|---|---|---|
| `labelMode` | `'fixed'` \| `'floating'` | `'fixed'` | Label placement. `'fixed'` — labels sit above each input. `'floating'` — labels start inside the input as a placeholder and animate to the top-left corner on focus or when the field has a value. |
| `hideCardHeader` | `boolean` | `false` | When `true`, hides the panel header row (the credit card icon and "Payment" title). Useful when you provide your own payment heading above the component. |

### Available states

| State | When applied |
|---|---|
| `default` | Always (the base styling) |
| `focus` | Any input inside the card has keyboard focus (`:host(:focus-within)`) |
| `hover` | The customer hovers over the card panel |
| `error` | The card is in an error state (validation or payment failure) |

### Style categories

#### `card` — the panel

The outer container for the card form.

| Property | Type | Default | Description |
|---|---|---|---|
| `backgroundColor` | `string` | `#fff` | Panel background color |
| `borderColor` | `string` | `#DEE2E6` | Panel border color |
| `borderWidth` | `string` | `1px` | Panel border width |
| `borderRadius` | `string` | `8px` | Panel corner radius |
| `border` | `string` | — | Shorthand border (e.g. `'2px solid navy'`); overrides `borderColor` and `borderWidth` when set |
| `boxShadow` | `string` | `0 2px 6px rgba(0,0,0,0.06)` | Panel box shadow |
| `padding` | `string` | `0` | Inner padding |
| `width` | `string` | `100%` | Panel width — `'100%'` fills the container, or fixed e.g. `'500px'` |
| `maxWidth` | `string` | `680px` | Maximum panel width on wide screens. Set to `'none'` to remove the cap. When `width` is fixed, `maxWidth` matches automatically so the panel is never clipped. |
| `gap` | `string` | `0` | Gap between form rows |
| `fontSize` | `string` | `16px` | Base font size |
| `fontFamily` | `string` | `Helvetica` | Base font family |
| `color` | `string` | `#4B5563` | Base text color |
| `iconColor` | `string` | `rgb(75, 85, 99)` | Fill color of the credit card SVG icon in the panel header. Hidden when `hideCardHeader: true`. |

#### `cardTitle` — the header title

Styles the "Payment" text in the card panel header. Supports state variants — set cardTitle under state.hover, state.focus, or state.error to change the title appearance per state.

| Property | Type | Default | Description |
|---|---|---|---|
| `color` | `string` | `#4B5563` | Title text color |
| `fontSize` | `string` | `16px` | Title font size |
| `fontWeight` | `string` | `500` | Title font weight |
| `fontFamily` | `string` | `Helvetica` | Title font family |

#### `input` — all input fields

Use `input` to style all card fields at once. To target individual fields, use `cardNumber`, `expiry`, or `cvv` instead — they accept the same properties.

| Property | Type | Default | Description |
|---|---|---|---|
| `backgroundColor` | `string` | `#fff` | Input background color. Always applied as `background-color` (never the `background` shorthand) — this preserves the card-brand badge image on the card-number field. |
| `borderColor` | `string` | `#DEE2E6` | Input border color |
| `borderRadius` | `string` | `6px` | Input corner radius |
| `color` | `string` | `#4B5563` | Input text color |
| `fontSize` | `string` | `16px` | Input font size |
| `fontFamily` | `string` | `Helvetica` | Input font family |
| `height` | `string` | `48px` | Input field height |
| `padding` | `string` | `0 16px` | Input inner padding |
| `margin` | `string` | `0` | Input outer margin. Routed through `--fsc-input-margin`; the grid layout deducts this from available space so inputs never overflow the card. |
| `outline` | `string` | — | Focus ring (e.g. `'1px solid #bfdbfe'`) |
| `errorBorderColor` | `string` | `#e53935` | Border color in error state |
| `'::placeholder'` | `object` | — | Placeholder styles — supports `color`, `fontSize`, `fontFamily`, `fontWeight` |

**Placeholder styling example:**

```js
input: {
    '::placeholder': {
        color: '#7D8A9B',
        fontSize: '14px',
    },
}
```

#### `cardNumber`, `expiry`, `cvv` — per-field overrides

Each accepts the same properties as `input` (above) but applies only to the named field. Useful when you want one field styled differently from the others.

#### `cvvIcon` — CVV graphic

A small graphic to the right of the CVV input — a card silhouette with a circle showing where to find the CVV code.

| Property | Type | Default | Description |
|---|---|---|---|
| `height` | `string` | `25px` | Icon height; width scales proportionally |
| `outlineColor` | `string` | `#B9B9B9` | The grey structural parts: card-shaped rectangle on the left and circle border on the right. Think "frame." |
| `accentColor` | `string` | `#008AFF` | The colored digit strokes inside the circle. Think "content." |
| `backgroundColor` | `string` | `transparent` | Background fill behind the icon — useful to match the input background |

#### `changeCard` — "Use a different card" link

| Property | Type | Default | Description |
|---|---|---|---|
| `color` | `string` | `#6b7280` | Link text color |
| `fontSize` | `string` | `14px` | Link font size |
| `fontFamily` | `string` | `Helvetica` | Link font family |

#### `inlineError` — inline validation error messages

| Property | Type | Default | Description |
|---|---|---|---|
| `color` | `string` | `#EB1431` | Error message text color |
| `fontSize` | `string` | `12px` | Error message font size |
| `fontWeight` | `string` | `400` | Error message font weight |
| `backgroundColor` | `string` | `transparent` | Error message background |

### State variants — what's stylable per state

| State | Categories that accept overrides in this state |
|---|---|
| `default` | All categories above |
| `focus` | `card.borderColor`, `input.borderColor` |
| `hover` | `card.backgroundColor`, `card.borderColor`, `cardTitle.*`, `changeCard.color` |
| `error` | `card.backgroundColor`, `card.borderColor`, `input.borderColor`, `input.color` |

---

## Pay Button (`fs-pay-button`)

Submits the payment when clicked. Styles apply directly as CSS on the underlying `<button>` element — any valid CSS property in camelCase is accepted.

### Available states

| State | Selector | When applied |
|---|---|---|
| `default` | `button` | Always (the base styling) |
| `hover` | `button:hover` | Mouse hover |
| `active` | `button:active` | Mouse down / tap |
| `disabled` | `button:disabled` | Session not yet loaded, or payment in progress |
| `error` | `button` (host `[error]`) | Payment error |
| `valid` | `button` (host `[valid]`) | Payment valid |

### Style categories

#### `button` — the button element

| Property | Type | Default | Description |
|---|---|---|---|
| `backgroundColor` | `string` | `#2563eb` | Button background color |
| `color` | `string` | `#ffffff` | Button label color |
| `border` | `string` | `solid 0px` | Shorthand border (e.g. `'2px solid red'`) |
| `borderColor` | `string` | `#2563eb` | Button border color |
| `borderRadius` | `string` | `8px` | Button corner radius |
| `padding` | `string` | `16px 24px` | Button inner padding |
| `margin` | `string` | `0px` | Button outer margin |
| `width` | `string` | `400px` | Button width |
| `height` | `string` | `54px` | Button height |
| `fontFamily` | `string` | `Helvetica` | Button font family |
| `fontSize` | `string` | `16px` | Button font size |
| `fontWeight` | `string` | `400` | Button font weight |
| `cursor` | `string` | `pointer` | Button cursor style |
| `opacity` | `string` | `1` | Button opacity |

#### `text` — the button label

Styles applied to the `<span>` inside the button.

| Property | Type | Description |
|---|---|---|
| `color` | `string` | Text color |
| `fontSize` | `string` | Text font size |
| `fontWeight` | `string` | Text font weight |
| `fontFamily` | `string` | Text font family |

#### `spinner` — the loading spinner

Shown while a payment is in flight. The spinner is a circular SVG arc filled with a linear gradient. The four named properties below map to CSS custom properties on `:host`; any other valid CSS property in camelCase is applied directly to the `<svg>` element.

| Property | Type | Default | Description |
|---|---|---|---|
| `colorStart` | `string` | `#F5F5F5` | Gradient start color (top of the arc) |
| `colorMid` | `string` | `#C8C8C8` | Gradient mid-point color |
| `colorEnd` | `string` | `#888888` | Gradient end color (bottom of the arc) |
| `size` | `string` | `2rem` | Width and height of the spinner SVG |

#### `iofDisclaimer` — Brazilian IOF fees disclaimer

Rendered only when the session's `complianceMessages` array contains `"SHOW_IOF_MESSAGE"`.

| Property | Type | Description |
|---|---|---|
| `fontSize` | `string` | Font size of the disclaimer text |
| `color` | `string` | Text color of the disclaimer |

#### `gdprNotice` — GDPR data-privacy notice

Rendered for customers in countries where this notice is shown, including EU member states, the UK, Norway, Switzerland, and Japan. Includes auto-populated links to Terms of Service and Privacy Policy.

| Property | Type | Description |
|---|---|---|
| `fontSize` | `string` | Notice font size |
| `color` | `string` | Notice text color |
| `linkColor` | `string` | Terms / Privacy Policy link color |
| `linkHoverColor` | `string` | Link color on hover |

---

## Disclosures (`fs-disclosures`)

Displays the FastSpring reseller disclosures ("Sold and fulfilled by FastSpring, an authorized reseller.") plus Privacy Policy and Terms of Sale links sourced automatically from session data. Uses the same `style.state.default` / `state.hover` structure as the other components.

> The Privacy Policy and Terms of Sale links render only when both URLs are present in the session data. No extra configuration needed — they populate automatically.

### Available states

| State | When applied |
|---|---|
| `default` | Always |
| `hover` | The customer hovers over a link |

### Style categories

#### `container` — the disclosures container

| Property | Type | Default | Description |
|---|---|---|---|
| `backgroundColor` | `string` | `transparent` | Container background color |
| `color` | `string` | `#cccccc` | Reseller disclosure text color |
| `fontFamily` | `string` | `Helvetica` | Font family |
| `fontSize` | `string` | `12px` | Font size |
| `padding` | `string` | `12px 16px` | Inner padding of the container |
| `rowGap` | `string` | `6px` | Gap between the reseller row and the links row |

#### `link` — Privacy Policy / Terms of Sale links

Set under both `state.default.link` and `state.hover.link` to control link styling at rest and on hover.

| Property | Type | Default | Description |
|---|---|---|---|
| `color` | `string` | `#004499` | Link color |
