# FastSpring Checkout SDK — Vanilla JS Integration Guide

A step-by-step guide for integrating the FastSpring Checkout SDK into a plain HTML/JavaScript page — no build tools or frameworks required.

> [!NOTE]
> **New to Component Checkouts?** Before running these examples, create and configure a Component Checkout in the FastSpring app. Set your allowed domains, currencies, and payment settings. [Get started in the FastSpring docs →](https://developer.fastspring.com/docs/set-up-component-checkouts)

**Jump to:** [Steps](#in-this-guide) · [Complete example](#complete-example) · [Troubleshooting](#troubleshooting) · [Next steps](#next-steps)

## What you will have at the end

A working credit-card form on your page that accepts an order: the customer enters card details, clicks **Pay**, and FastSpring processes the payment end-to-end. By the end of this guide, you will be able to take a session ID returned by your server and route the buyer through a fully styled, embedded checkout.

## Prerequisites

- A FastSpring account with a **component checkout** configured in your store.
- Your component checkout's **URL** — the path you will pass to `FastSpring.init`. Find it in the FastSpring app under **Checkouts → Component Checkouts → [your checkout] → Implementation**.
- A **server endpoint** that creates sessions with the FastSpring [Sessions API](https://developer.fastspring.com/reference/createsession). You will create a session on each checkout attempt and pass its ID to the SDK.
- For local development: your local origin (for example, `http://localhost:5173`) added to the **allow list** for your component checkout. See the callout in [Step 2](#step-2-initialize-the-sdk).

## In this guide

The integration has ten steps. Select a step to jump to it.

| Step | What you do | Component type | Mount target | Optional? |
| --- | --- | --- | --- | --- |
| [1. Include the SDK](#step-1-include-the-sdk) | Add the SDK with npm or a script tag | — | — | |
| [2. Initialize the SDK](#step-2-initialize-the-sdk) | Call `FastSpring.init()` and add callbacks | — | — | |
| [3. Add the Card](#step-3-add-the-card-component) | Collect card number, expiry, and CVV | `fs-card` | `#card-element` | |
| [4. Add the Pay Button](#step-4-add-the-pay-button-component) | Submit the payment | `fs-pay-button` | `#pay-button-element` | |
| [5. Add Disclosures](#step-5-add-the-disclosures-component) | Show the reseller disclosure and policy links | `fs-disclosures` | `#disclosures-container` | |
| [6. Add the Coupon field](#step-6-add-the-coupon-component) | Let the buyer apply or remove a coupon code | `fs-coupon` | `#coupon-element` | |
| [7. Add the Email field](#step-7-add-the-email-component) | Collect the buyer's email address | `fs-email` | `#email-element` | |
| [8. Add Apple Pay](#step-8-add-apple-pay-optional) | Show Apple's Apple Pay button | `fs-apple-pay` | `#apple-pay-element` | Optional |
| [9. Add Google Pay](#step-9-add-google-pay-optional) | Show the Google Pay button | `fs-google-pay` | `#google-pay-element` | Optional |
| [10. Start the checkout](#step-10-start-the-checkout) | Call `sdk.checkout()` with a session ID | — | — | |

For every property you can style on a component, see the [Style Properties Reference](./style-reference.md).

---

## Step 1: Include the SDK

Choose the installation method that fits your project.

### Option A — Package manager

```bash
npm install @fastspring/checkout-sdk
```

Then import it in your JavaScript or TypeScript:

```js
import { FastSpring } from '@fastspring/checkout-sdk';
```

### Option B — Script tag (no build tools required)

Add the SDK script tag to your HTML, just before the closing `</head>` tag.

```html
<script src="https://cdn.onfastspring.com/checkout-sdk/latest/fastspring-sdk.js"></script>
```

<details>
<summary>TypeScript and IntelliSense (optional)</summary>

Download `fastspring-sdk.d.ts` from the same CDN path and place it next to your HTML file. VS Code picks it up automatically and provides autocomplete.

</details>

**Next:** [Step 2: Initialize the SDK →](#step-2-initialize-the-sdk)

---

## Step 2: Initialize the SDK

Call `FastSpring.init()` once, after the SDK has loaded. It returns an `sdk` instance that you use in all later steps. If you installed the package with npm, use the code inside the `<script>` tags in your own module and leave the tags out.

> [!IMPORTANT]
> **Local development — allow your origin first.**
>
> Component checkouts restrict which domains may embed them. Before testing locally, add your local origin (for example, `http://localhost:5173`) to the **allow list** for your component checkout. In the FastSpring app, go to **Checkouts → Component Checkouts → [your checkout] → Implementation → Step 1. Add your domains to the allow list**, enter your origin, and click **Save**.
>
> Without this, the browser's `frame-ancestors` policy blocks the component checkout, and components don't render.

Pass your settings and callbacks in a single `FastSpring.init()` call. Only `checkoutUrl` is required; use the URL from your checkout's **Implementation** tab.

- `debug` shows a dialog with the order details and raw JSON after every payment success or failure, in addition to your callbacks. Use it while you build.
- `globalStyles` sets base styles for every component. Styles you set on a component take priority.
- Callbacks tell your page what happens during checkout. Each one, with its full payload, is described in the [Callbacks reference](https://developer.fastspring.com/reference/checkout-components-callbacks).

```html
<script>
  const sdk = FastSpring.init({
    // Required
    checkoutUrl: 'https://<your-store>.onfastspring.com/<your-checkout>',

    // Optional
    debug: true,
    globalStyles: {
      color: '#4B5563',
      fontFamily: 'Helvetica',
      fontSize: '14px',
    },

    // Callbacks
    /**
     * Fired when the session data has been fetched and the checkout is ready.
     * Use this to hide your own loading indicator.
     */
    onSessionLoaded: (data) => {
      console.log('Session loaded:', data);
    },

    /**
     * Fired each time the session is reloaded after a change made in the
     * browser — postal code, coupon apply/remove, country/currency change.
     * Not fired for the initial load (see onSessionLoaded) or when the
     * order completes (see onOrderCompleted). Changes made server-side
     * through the Session API do not fire it.
     * `data` is the full session, same shape as onSessionLoaded — it does
     * not say which fields changed, so re-render the values you display.
     */
    onSessionUpdated: (data) => {
      console.log('Session updated:', data);
    },

    /**
     * Fired when the buyer clicks any payment button — the Pay Button, the
     * saved-card button, or a wallet button. Fires on the click itself,
     * before validation and before the order is created, even if the
     * payment then stops. Reporting only: the payment carries on
     * regardless, and the return value is ignored.
     * `data.paymentMethodType` — for example 'card', 'applepay', 'googlepay'
     * `data.oneClick` — true when a saved payment method was used
     * `data.session` — the current session, or null if not loaded yet
     */
    onPayClicked: (data) => {
      console.log('Pay clicked:', data.paymentMethodType, data.oneClick);
    },

    /**
     * Fired after a successful payment and order completion.
     * `data` contains the order confirmation details.
     */
    onOrderCompleted: (data) => {
      console.log('Order completed!', data);
    },

    /**
     * Fired when the payment attempt is rejected or encounters an error.
     * Use this to display an error message to the customer.
     */
    onPaymentFailed: (error) => {
      console.error('Payment failed:', error);
    },
  });
</script>
```

**Next:** [Step 3: Add the Card →](#step-3-add-the-card-component)

---

## Step 3: Add the Card component

The Card component renders the payment fields (card number, expiry, CVV) inside a secure iframe.

**1. Add a mount target in your HTML:**

```html
<div id="card-element"></div>
```

**2. Create and mount the component:**

```js
const cardComponent = sdk.components.create('fs-card', {});
cardComponent.mount('#card-element');
```

<details open>
<summary>Customize the appearance</summary>

```js
const cardComponent = sdk.components.create('fs-card', {
  labelMode: 'fixed', // 'floating' (default) | 'fixed'
  hideCardHeader: false, // hide the built-in "Payment" title row
  style: {
    state: {
      default: {
        card: {
          backgroundColor: '#fff',
          borderRadius: '8px',
          border: '2px solid navy',
        },
        input: {
          borderRadius: '6px',
          height: '48px',
        },
      },
      focus: {
        input: {
          borderColor: '#4d90fe',
        },
      },
    },
  },
});

cardComponent.mount('#card-element');
```

For the full list of styling categories and properties — including `cardTitle`, `cvvIcon`, `changeCard`, `inlineError`, and per-state overrides — see the [Style Properties Reference](./style-reference.md).

</details>

**Next:** [Step 4: Add the Pay Button →](#step-4-add-the-pay-button-component)

---

## Step 4: Add the Pay Button component

The Pay Button submits the payment when clicked.

**1. Add a mount target in your HTML:**

```html
<div id="pay-button-element"></div>
```

**2. Create and mount the component:**

```js
const payButtonComponent = sdk.components.create('fs-pay-button', {});
payButtonComponent.mount('#pay-button-element');
```

> [!NOTE]
> Pass an options object to `sdk.components.create()`, even an empty `{}`. In SDK 1.4.0, creating the Pay Button without one throws an error. See [Troubleshooting](#troubleshooting).

<details open>
<summary>Customize the appearance</summary>

```js
const payButtonComponent = sdk.components.create('fs-pay-button', {
  style: {
    state: {
      default: {
        button: {
          backgroundColor: '#2563EB',
          color: '#ffffff',
          borderRadius: '8px',
          width: '400px',
          height: '54px',
        },
      },
      hover: {
        button: {
          backgroundColor: '#1E4FC0',
        },
      },
    },
  },
});

payButtonComponent.mount('#pay-button-element');
```

For the full list of states (`hover`, `active`, `disabled`, `error`, `valid`) and styling categories (`text`, `spinner`, `iofDisclaimer`, `gdprNotice`), see the [Style Properties Reference](./style-reference.md).

</details>

**Next:** [Step 5: Add Disclosures →](#step-5-add-the-disclosures-component)

---

## Step 5: Add the Disclosures component

The Disclosures component shows the FastSpring reseller disclosure — *"Sold and fulfilled by FastSpring, an authorized reseller."* — along with Privacy Policy and Terms of Sale links sourced automatically from session data. It renders inside its own iframe.

**1. Add a mount target in your HTML:**

```html
<div id="disclosures-container"></div>
```

**2. Create and mount the component:**

```js
const disclosuresComponent = sdk.components.create('fs-disclosures', {});
disclosuresComponent.mount('#disclosures-container');
```

The Privacy Policy and Terms of Sale links populate automatically when both URLs are present in session data. No extra configuration is needed.

<details open>
<summary>Customize the appearance</summary>

```js
const disclosuresComponent = sdk.components.create('fs-disclosures', {
  style: {
    state: {
      default: {
        container: {
          color: '#666666',
          fontFamily: 'Helvetica',
          fontSize: '12px',
        },
        link: {
          color: '#0066cc',
        },
      },
    },
  },
});

disclosuresComponent.mount('#disclosures-container');
```

</details>

**Next:** [Step 6: Add the Coupon field →](#step-6-add-the-coupon-component)

---

## Step 6: Add the Coupon component

The Coupon component lets the buyer apply or remove a coupon code during checkout, inside its own iframe. Applying or clearing a code updates the session through the SDK. Components that show session-derived values, such as totals, refresh automatically.

**1. Add a mount target in your HTML:**

```html
<div id="coupon-element"></div>
```

**2. Create and mount the component:**

```js
const couponComponent = sdk.components.create('fs-coupon', {});
couponComponent.mount('#coupon-element');
```

If the session already has a coupon applied, the component renders its applied state on load. The buyer can clear the existing code or replace it.

**Next:** [Step 7: Add the Email field →](#step-7-add-the-email-component)

---

## Step 7: Add the Email component

The Email component collects the buyer's **email address** (which maps to `customer.billToContact.email` in the session) inside a secure iframe. It renders no section header, so write your own heading above it on the page. It's typically shown at the top of the form, so mount its target above the card.

**1. Add a mount target in your HTML:**

```html
<div id="email-element"></div>
```

**2. Create and mount the component:**

```js
const emailComponent = sdk.components.create('fs-email', {});
emailComponent.mount('#email-element');
```

`fields.email` controls visibility:

| Value | Behavior |
| --- | --- |
| `'auto'` (default) | Shows an editable, required field. Pre-filled if the session already has an email. |
| `'readonly'` | Shows the field pre-filled and locked. |
| `'never'` | Hides the field. |

`'readonly'` and `'never'` fall back to `'auto'` when the session has no email, because an order can't be fulfilled without one.

<details open>
<summary>Customize the appearance and behavior</summary>

```js
const emailComponent = sdk.components.create('fs-email', {
  labelMode: 'floating', // 'floating' (default) | 'fixed'
  fields: {
    email: 'auto', // 'auto' (default) | 'readonly' | 'never'
  },
  style: {
    state: {
      default: {
        email: {
          maxWidth: '520px',
        },
        input: {
          borderRadius: '6px',
          height: '48px',
        },
      },
      focus: {
        input: {
          borderColor: '#4d90fe',
        },
      },
      error: {
        input: {
          borderColor: '#EB1431',
        },
      },
    },
  },
});

emailComponent.mount('#email-element');
```

For the full list of styling categories (`email`, `label`, `input`, `inlineError`) and per-state overrides, see the [Style Properties Reference](./style-reference.md).

</details>

**Next:** [Step 8: Add Apple Pay (optional) →](#step-8-add-apple-pay-optional)

---

## Step 8: Add Apple Pay (optional)

> [!NOTE]
> This step is optional. To skip it, continue to [Step 9](#step-9-add-google-pay-optional) or go straight to [Step 10](#step-10-start-the-checkout).

The Apple Pay component renders Apple's official Apple Pay button inside a secure iframe. It uses a two-step flow: clicking the button opens a FastSpring-hosted window where the buyer confirms with Face ID or Touch ID.

The button appears only when **both** of these are true. Otherwise the component renders nothing, no error is shown, and other payment methods take over.

- The buyer's browser supports Apple Pay (Safari on macOS or iOS).
- Apple Pay is enabled as a payment method for your store.

**1. Add a mount target in your HTML:**

```html
<div id="apple-pay-element"></div>
```

**2. Create and mount the component:**

```js
const applePayComponent = sdk.components.create('fs-apple-pay', {});
applePayComponent.mount('#apple-pay-element');
```

While the Apple Pay window is open, the SDK dims your page with a gray overlay to prevent a double checkout. The overlay is removed automatically when the window closes, is abandoned, or the payment completes. Clicking the overlay closes the Apple Pay window and restores the checkout.

A successful Apple Pay payment fires the same `onOrderCompleted` callback as a card payment. Declines are reported through `onPaymentFailed`.

<details open>
<summary>Customize the appearance</summary>

Apple draws the button, so the stylable surface is small: `variant`, `frame`, and `button.height` / `button.borderRadius`.

```js
const applePayComponent = sdk.components.create('fs-apple-pay', {
  variant: 'black', // 'black' (default) | 'white' — use white on dark checkouts
  frame: {
    width: '100%',
    maxWidth: '680px',
  },
  style: {
    state: {
      default: {
        button: {
          height: '48px',
          borderRadius: '4px',
        },
      },
    },
  },
});

applePayComponent.mount('#apple-pay-element');
```

See the [Style Properties Reference](./style-reference.md).

</details>

**Next:** [Step 9: Add Google Pay (optional) →](#step-9-add-google-pay-optional)

---

## Step 9: Add Google Pay (optional)

> [!NOTE]
> This step is optional. To skip it, continue to [Step 10](#step-10-start-the-checkout).

The Google Pay component (`fs-google-pay`) renders a branded Google Pay button inside a secure iframe. Clicking it opens a FastSpring-hosted popup where the buyer completes the Google Pay flow.

Like Apple Pay, the button appears only when the payment method is available for the current session and browser. If Google Pay is unavailable, the component renders nothing and the rest of the checkout continues normally.

**1. Add a mount target in your HTML:**

```html
<div id="google-pay-element"></div>
```

**2. Create and mount the component:**

```js
const googlePayComponent = sdk.components.create('fs-google-pay', {});
googlePayComponent.mount('#google-pay-element');
```

<details open>
<summary>Customize the appearance</summary>

`fs-google-pay` supports component width through `frame.width` and `frame.maxWidth`, and button height and shape overrides through `style.state.default.button.height` and `style.state.default.button.borderRadius`. Only the `default` style state is supported; Google controls the button's colors and interaction states. Use `variant` to choose the branded black or white button.

```js
const googlePayComponent = sdk.components.create('fs-google-pay', {
  variant: 'black', // 'black' (default) | 'white'
  frame: {
    width: '100%',
    maxWidth: '680px',
  },
  style: {
    state: {
      default: {
        button: {
          height: '48px',
          borderRadius: '8px',
        },
      },
    },
  },
});

googlePayComponent.mount('#google-pay-element');
```

See the [Style Properties Reference](./style-reference.md#google-pay-fs-google-pay).

</details>

**Next:** [Step 10: Start the checkout →](#step-10-start-the-checkout)

---

## Step 10: Start the checkout

Call `sdk.checkout()` with a valid **Session ID** to load session data into the mounted components. The Card and Pay Button become visible once the session is confirmed open.

> [!NOTE]
> **Getting a Session ID.** Create a session server-side with the FastSpring [Sessions API](https://developer.fastspring.com/reference/createsession). The API returns an `id` field. That's the Session ID to pass here.

```js
sdk.checkout('<SESSION_ID>', {
  onSuccess: () => {
    console.log('Session loaded — checkout is ready');
  },
  onError: (error) => {
    console.error('Session load failed:', error.message);
  },
});
```

---

## Complete example

The finished page, with every component from the steps above. Replace the checkout URL and the session ID.

```html
<!DOCTYPE html>
<html>
<head>
  <title>FastSpring Checkout</title>
  <!-- Include SDK -->
  <script src="https://cdn.onfastspring.com/checkout-sdk/latest/fastspring-sdk.js"></script>
</head>
<body>
  <!-- Email mount target -->
  <div id="email-element"></div>

  <!-- Apple Pay mount target -->
  <div id="apple-pay-element"></div>

  <!-- Google Pay mount target -->
  <div id="google-pay-element"></div>

  <!-- Card mount target -->
  <div id="card-element"></div>

  <!-- Coupon mount target -->
  <div id="coupon-element"></div>

  <!-- Pay button mount target -->
  <div id="pay-button-element"></div>

  <!-- Disclosures mount target -->
  <div id="disclosures-container"></div>

  <script>
    // Initialize SDK
    const sdk = FastSpring.init({
      checkoutUrl: 'https://<your-store>.onfastspring.com/<your-checkout>',
      onSessionLoaded: (data) => {
        console.log('Session loaded:', data);
      },
      onSessionUpdated: (data) => {
        console.log('Session updated:', data);
      },
      onPayClicked: (data) => {
        console.log('Pay clicked:', data);
      },
      onOrderCompleted: (data) => {
        console.log('Order completed!', data);
      },
      onPaymentFailed: (error) => {
        console.error('Payment failed:', error);
      },
    });

    // Email component
    const emailComponent = sdk.components.create('fs-email', {});
    emailComponent.mount('#email-element');

    // Apple Pay component (renders only in eligible browsers)
    const applePayComponent = sdk.components.create('fs-apple-pay', {});
    applePayComponent.mount('#apple-pay-element');

    // Google Pay component (renders only when available for the session)
    const googlePayComponent = sdk.components.create('fs-google-pay', {
      variant: 'black',
      frame: {
        width: '100%',
        maxWidth: '680px',
      },
      style: {
        state: {
          default: {
            button: {
              height: '48px',
              borderRadius: '8px',
            },
          },
        },
      },
    });
    googlePayComponent.mount('#google-pay-element');

    // Card component
    const cardComponent = sdk.components.create('fs-card', {});
    cardComponent.mount('#card-element');

    // Coupon component
    const couponComponent = sdk.components.create('fs-coupon', {});
    couponComponent.mount('#coupon-element');

    // Pay button component
    const payButtonComponent = sdk.components.create('fs-pay-button', {});
    payButtonComponent.mount('#pay-button-element');

    // Disclosures component
    const disclosuresComponent = sdk.components.create('fs-disclosures', {});
    disclosuresComponent.mount('#disclosures-container');

    // Start checkout (replace <SESSION_ID> with the Session ID from your server)
    sdk.checkout('<SESSION_ID>', {
      onSuccess: () => {
        console.log('Session loaded');
      },
      onError: (error) => {
        console.error('Session load failed:', error.message);
      },
    });
  </script>
</body>
</html>
```

---

## Troubleshooting

<details>
<summary><strong>The card form doesn't render, and the browser console shows a <code>frame-ancestors</code> or CSP error.</strong></summary>

Your origin isn't on the allow list for your component checkout. Add it in the FastSpring app under **Checkouts → Component Checkouts → [your checkout] → Implementation → Step 1**, then refresh.

</details>

<details>
<summary><strong><code>sdk.checkout()</code> calls <code>onError</code> with <code>HTTP 404</code>.</strong></summary>

FastSpring has no session with that ID. The `message` looks like `HTTP 404: `, and the text after the code can be empty. Check that the ID is the `id` your server received from the Sessions API, that it belongs to this checkout, and that the session hasn't expired. If it has, create a new session right before you call `sdk.checkout()`.

</details>

<details>
<summary><strong><code>sdk.checkout()</code> succeeds, but the components stay hidden.</strong></summary>

The components render only while the session is open. A session that has already been paid is not open, so its checkout components don't show. Create a new session for the next checkout.

</details>

<details>
<summary><strong>Components don't appear after <code>.mount()</code>.</strong></summary>

The mount target isn't in the DOM yet. Make sure your `<div id="card-element">` (and the others) appear in the HTML *before* the script that calls `.mount()`, or wrap your code in a `DOMContentLoaded` listener.

</details>

<details>
<summary><strong>The Pay Button doesn't become active.</strong></summary>

The Pay Button works with a loaded session. Confirm that `sdk.checkout()` was called with a valid session ID and that `onSuccess` fired without an error.

</details>

<details>
<summary><strong>Creating the Pay Button throws <code>Cannot read properties of undefined (reading 'style')</code>.</strong></summary>

In SDK 1.4.0, `sdk.components.create('fs-pay-button')` throws when you don't pass an options object. Pass an empty object instead: `sdk.components.create('fs-pay-button', {})`. Passing `{}` to every component is a safe habit.

</details>

<details>
<summary><strong>The SDK script returns 404 or fails to load.</strong></summary>

Check that the script URL exactly matches the one in [Step 1](#step-1-include-the-sdk). The `latest` path always serves the current release, so if you copied a version-specific URL from somewhere else, switch to the `latest` URL.

</details>

---

## Next steps

- **[Callbacks reference](https://developer.fastspring.com/reference/checkout-components-callbacks)** — every callback, when it fires, and its full payload.
- **Full styling reference** — [`style-reference.md`](./style-reference.md) lists every property, type, default, and state variant for every component.
- **[Sessions API](https://developer.fastspring.com/reference/sessions-overview)** — covers session creation, customer details, item overrides, and pricing.
- **Configure your checkout** — adjust allowed domains, currencies, and payment-method save behavior in the FastSpring app under **Checkouts → Component Checkouts → [your checkout]**.
