# Style Properties Reference

Canonical reference for every styling property exposed by the FastSpring Checkout SDK components: `fs-card`, `fs-pay-button`, and `fs-disclosures`.

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
| `labelMode` | `'fixed'` \| `'floating'` | `'floating'` | Label placement. `'floating'` — labels start inside the input as a placeholder and animates to the top-left corner on focus or when the field has a value. `'fixed'` — labels sit above each input. |
| `hideCardHeader` | `boolean` | `false` | Hide the panel header row (credit-card icon + "Payment" title) when you have your own heading. |

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
| `fontFamily` | system font stack | Base font family |
| `color` | `#4B5563` | Base text color |
| `iconColor` | `rgb(75, 85, 99)` | Header credit-card icon color. Hidden when `hideCardHeader: true`. |

### `cardTitle` — the "Payment" header

| Property | Default | Description |
| --- | --- | --- |
| `color` | `#4B5563` | Title text color |
| `fontSize` | `16px` | Title size |
| `fontWeight` | `500` | Title weight |
| `fontFamily` | `Helvetica` | Title family |

### `input` — all input fields

Use `input` to style every card field at once. Use `cardNumber`, `expiry`, or `cvv` for per-field overrides — they accept the same properties.

> **Heads-up:** depending on your session config, the SDK may also render a **Zip Code** field and a **"Save Payment Details"** checkbox alongside the card inputs. These don't have dedicated style categories yet — see [Session-driven fields](#session-driven-fields) before designing a theme.

| Property | Default | Description |
| --- | --- | --- |
| `backgroundColor` | `#ffffff` | Input background. Always applied as `background-color` (not the shorthand) to preserve card-brand badges. |
| `borderColor` | `#DEE2E6` | Border color |
| `borderRadius` | `6px` | Corner radius |
| `color` | `#4B5563` | Text color |
| `fontSize` | `16px` | Font size |
| `fontFamily` | `Helvetica` | Font family |
| `fontWeight` | `400` | Font weight |
| `height` | `48px` | Field height |
| `padding` | `0 16px` | Inner padding |
| `margin` | `0` | Outer margin |
| `outline` | — | Focus-ring shorthand (e.g., `'1px solid #bfdbfe'`) |
| `outlineColor` | `#bfdbfe` | Focus-ring color (flat alternative to `outline`) |
| `errorBorderColor` | `#e53935` | Border color in error state |
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
| `outlineColor` | `#B9B9B9` | The grey structural parts (card rectangle, circle border) |
| `accentColor` | `#008AFF` | The colored digit strokes inside the circle |
| `backgroundColor` | `transparent` | Background fill behind the icon |

### `changeCard` — "Use a different card" link

| Property | Default | Description |
| --- | --- | --- |
| `color` | `#6b7280` | Link color |
| `fontSize` | `14px` | Link size |
| `fontFamily` | `Helvetica` | Link family |

### `inlineError` — validation error messages

| Property | Default | Description |
| --- | --- | --- |
| `color` | `#EB1431` | Error text color |
| `fontSize` | `12px` | Error font size |
| `fontWeight` | `400` | Error font weight |
| `backgroundColor` | `transparent` | Error message background |

### Card states

Out of the box, the Card visually changes for **focus**, **error**, and **active**. Other states (`hover`) accept overrides but apply no SDK-default differentiation — set them to add your own hover styling.

| State | What changes by default |
| --- | --- |
| `focus` | Focused input gets a blue border (`#4d90fe`) and a soft blue glow |
| `error` | Card border, input border, and input text all turn red (`#e53935`) |
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

## Pay Button (`fs-pay-button`)

Submits payment when clicked. Styles apply as CSS on the underlying `<button>` element — any valid CSS property in camelCase works.

### `button` — the button element

| Property | Default | Description |
| --- | --- | --- |
| `backgroundColor` | `#2563EB` | Button background |
| `color` | `#ffffff` | Label color |
| `border` | `solid 0px` | Shorthand border |
| `borderColor` | `#008aff` | Border color |
| `borderRadius` | `8px` | Corner radius |
| `padding` | `28px` | Inner padding |
| `margin` | `0` | Outer margin |
| `width` | `400px` | Button width |
| `height` | `54px` | Button height |
| `fontFamily` | `Helvetica` | Font family |
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
| `colorStart` | `#F5F5F5` | Gradient start color (top of arc) |
| `colorMid` | `#C8C8C8` | Gradient mid-point |
| `colorEnd` | `#888888` | Gradient end color (bottom of arc) |
| `size` | `2rem` | Spinner width and height |

### `iofDisclaimer` — Brazilian IOF fees notice

Rendered only when the session's `complianceMessages` array contains `"SHOW_IOF_MESSAGE"`.

| Property | Default | Description |
| --- | --- | --- |
| `fontSize` | `.75rem` | Disclaimer font size |
| `color` | `#6b7280` | Disclaimer text color |

### `gdprNotice` — GDPR data-privacy notice

Rendered for customers in GDPR-applicable countries (EU member states, UK, Norway, Switzerland, Japan). Includes auto-populated links to Terms of Service and Privacy Policy.

| Property | Default | Description |
| --- | --- | --- |
| `fontSize` | `.75rem` | Notice font size |
| `color` | `#6b7280` | Notice text color |
| `linkColor` | `#2563eb` | Terms / Privacy Policy link color |
| `linkHoverColor` | `#1d4ed8` | Link color on hover |

### Pay Button states

| State | When applied | What changes by default |
| --- | --- | --- |
| `default` | Always | Base styles above |
| `hover` | `button:hover` | Border color shifts to `#2563EB`. Other properties unchanged — override for full hover differentiation. |
| `active` | `button:active` (mouse down / tap) | No visual change unless overridden |
| `disabled` | `button:disabled` (session not loaded / payment in progress) | Opacity drops to `0.5`, cursor becomes `not-allowed` |
| `error` | Host has `[error]` attribute | No visual change unless overridden |
| `valid` | Host has `[valid]` attribute | No visual change unless overridden |
| `focus` | Button has keyboard focus | Outline `2px solid #3b82f6` with `2px` offset |

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
| `color` | `#cccccc` | Reseller disclosure text color |
| `fontFamily` | `Helvetica` | Font family |
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
