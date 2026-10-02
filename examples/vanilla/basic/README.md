# FastSpring Checkout SDK — Vanilla JS Integration Guide

A step-by-step guide for integrating the FastSpring Checkout SDK into a plain HTML/JavaScript page — no build tools or frameworks required.

> **New to Component Checkouts?** Before running these examples, you will need to create and configure a Component Checkout in the FastSpring app — set your allowed domains, currencies, and payment settings.
> [Get started in the FastSpring docs →](https://developer.fastspring.com/docs/set-up-component-checkouts)

## What you will have at the end

A working credit-card form on your page that accepts a live order: the customer enters card details, clicks **Pay**, and FastSpring processes the payment end-to-end. By the end of this guide, you will be able to take a session ID returned by your server and route the buyer through a fully styled, embedded checkout.

## Prerequisites

- A FastSpring account with a **component checkout** configured in your store.
- Your component checkout's **URL** — the path you will pass to `FastSpring.init`. Find it in the FastSpring app under **Checkouts → Component Checkouts → [your checkout] → Implementation**.
- A **server endpoint** that creates sessions via the FastSpring [Sessions API](https://developer.fastspring.com/reference/createsession) — you will generate a session ID on each checkout attempt and pass it to the SDK.
- For local development: your local origin (e.g. `http://localhost:5173`) added to the **allow list** for your component checkout — see the callout in [Step 2](#step-2--initialize-the-sdk).

## Overview

The integration has nine steps:

1. Include the SDK
2. Initialize the SDK (`FastSpring.init`)
3. Create and mount the **Card** component
4. Create and mount the **Pay Button** component
5. Create and mount the **Disclosures** component
6. Create and mount the **Coupon** component
7. Create and mount the **Email** component (email address)
8. Create and mount the **Apple Pay** component
9. Call `sdk.checkout(sessionId)` to start the checkout flow

---

## Step 1 — Include the SDK

Choose the installation method that fits your project.

### Option A — Package manager

```bash
npm install @fastspring/checkout-sdk
```

Then import in your JavaScript or TypeScript:

```js
import { FastSpring } from '@fastspring/checkout-sdk';
```

### Option B — Script tag (no build tools required)

Add the SDK script tag in your HTML, just before the closing `</head>` tag.

**Include script from CDN:**

```html
<script src="https://cdn.onfastspring.com/checkout-sdk/latest/fastspring-sdk.js"></script>
```


> **TypeScript / IntelliSense (optional)**
>
> Download `fastspring-sdk.d.ts` from the same CDN path and place it next to your HTML file. VS Code will pick it up automatically and provide autocomplete.

---

## Step 2 — Initialize the SDK

Call `FastSpring.init()` once the SDK script has loaded. It returns an `sdk` instance you will use in all subsequent steps.

> **Local development — allow your origin first**
>
> Component checkouts restrict which domains may embed them. Before testing locally, add your local origin (e.g., `http://localhost:5173`) to the **allow list** for your component checkout. In the FastSpring app, go to **Checkouts → Component Checkouts → [your checkout] → Implementation → Step 1. Add your domains to the allow list**, enter your origin, and click **Save**.
>
> Without this, the component checkout is blocked by the browser's `frame-ancestors` policy, and components won't render.

```html
<script>
    const sdk = FastSpring.init({

        // ── Required ──────────────────────────────────────────────────────
        // Must point to a component checkout. Find this URL in your component
        // checkout's Implementation tab in the FastSpring app.
        checkoutUrl: 'https://<your-store>.onfastspring.com/<your-checkout>',

        // ── Optional ──────────────────────────────────────────────────────
        debug: true,      // Shows built-in success/failure dialogs during testing.

        globalStyles: {   // Applied to all components as base defaults.
            color: '#4B5563',
            fontFamily: 'Helvetica',
            fontSize: '14px',
        },

        // ── Callbacks ─────────────────────────────────────────────────────

        /**
         * Fired when the session data has been fetched and the checkout is ready.
         * Use this to hide your own loading indicator.
         */
        onSessionLoaded: (data) => {
            console.log('Session loaded:', data);
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

---

## Step 3 — Create and mount the Card component

The Card component renders the payment fields (card number, expiry, CVV) inside a secure iframe.

**HTML — add a mount target:**

```html
<div id="card-element"></div>
```

**JS — create and mount:**

```js
const cardComponent = sdk.components.create('fs-card', {
    labelMode: 'fixed',       // 'fixed' (default) | 'floating'
    hideCardHeader: false,    // hide the built-in "Payment" title row

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

---

## Step 4 — Create and mount the Pay Button component

The Pay Button submits the payment when clicked.

**HTML — add a mount target:**

```html
<div id="pay-button-element"></div>
```

**JS — create and mount:**

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

---

## Step 5 — Create and mount the Disclosures component

The Disclosures component shows the FastSpring reseller disclosure — *"Sold and fulfilled by FastSpring, an authorized reseller."* — along with Privacy Policy and Terms of Sale links sourced automatically from session data. It renders inside its own iframe.

**HTML — add a mount target:**

```html
<div id="disclosures-container"></div>
```

**JS — create and mount:**

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

The Privacy Policy and Terms of Sale links populate automatically when both URLs are present in session data — no extra configuration needed.

---

## Step 6 — Create and mount the Coupon component

The Coupon component lets the buyer apply or remove a coupon code during checkout, inside its own iframe. Applying or clearing a code updates the session via the SDK; components that show session-derived values (such as totals) refresh automatically.

**HTML — add a mount target:**

```html
<div id="coupon-element"></div>
```

**JS — create and mount:**

```js
const couponComponent = sdk.components.create('fs-coupon', {});

couponComponent.mount('#coupon-element');
```

If the session already has a coupon applied, the component renders its applied state on load — the buyer can clear the existing code or replace it.

---

## Step 7 — Create and mount the Email component

The Email component collects the buyer's **email address** (which maps to `customer.billToContact.email` in the session) inside a secure iframe. It renders no section header, so write your own heading above it on the page. It's typically shown at the top of the form, so mount its target above the card.

**HTML — add a mount target:**

```html
<div id="email-element"></div>
```

**JS — create and mount:**

```js
const emailComponent = sdk.components.create('fs-email', {
    labelMode: 'floating',      // 'floating' (default) | 'fixed'
    fields: { email: 'auto' },  // 'auto' (default) | 'readonly' | 'never'

    style: {
        state: {
            default: {
                email: { maxWidth: '520px' },
                input: { borderRadius: '6px', height: '48px' },
            },
            focus: {
                input: { borderColor: '#4d90fe' },
            },
            error: {
                input: { borderColor: '#EB1431' },
            },
        },
    },
});

emailComponent.mount('#email-element');
```

`fields.email` controls visibility: `'auto'` shows an editable, required field (pre-filled if the session already has an email); `'readonly'` shows it pre-filled and locked; `'never'` hides it. `'readonly'`/`'never'` fall back to `'auto'` when the session has no email, since an order can't be fulfilled without one.

For the full list of styling categories (`email`, `label`, `input`, `inlineError`) and per-state overrides, see the [Style Properties Reference](./style-reference.md).

---

## Step 8 — Create and mount the Apple Pay component

The Apple Pay component renders Apple's official Apple Pay button inside a secure iframe (the two-step flow: clicking it opens a FastSpring-hosted window where the buyer confirms with Face ID / Touch ID).

The button only appears when **both** hold — otherwise the component renders nothing and other payment methods take over (no error is shown):

- the buyer's browser supports Apple Pay (Safari on macOS/iOS), and
- Apple Pay is enabled as a payment method for your store.

**HTML — add a mount target:**

```html
<div id="apple-pay-element"></div>
```

**JS — create and mount:**

```js
const applePayComponent = sdk.components.create('fs-apple-pay', {
    variant: 'black',            // 'black' (default) | 'white' — use white on dark checkouts
    frame: { width: '100%', maxWidth: '680px' },

    style: {
        state: {
            default: {
                button: { height: '48px', borderRadius: '4px' },
            },
        },
    },
});

applePayComponent.mount('#apple-pay-element');
```

While the Apple Pay window is open, the SDK dims your page with a gray overlay to prevent double-checkout; it is removed automatically when the window closes, is abandoned, or the payment completes. Clicking the overlay closes the Apple Pay window and restores the checkout.

A successful Apple Pay payment fires the same `onOrderCompleted` callback as a card payment; declines are reported through `onPaymentFailed`.

The button is drawn by Apple, so the stylable surface is small: `variant`, `frame`, and `button.height` / `button.borderRadius`. See the [Style Properties Reference](./style-reference.md).

---

## Step 9 — Start the Checkout

Call `sdk.checkout()` with a valid **Session ID** to load session data into the mounted components. The Card and Pay Button become visible once the session is confirmed open.

> **Getting a Session ID**
>
> Create a session server-side using the FastSpring [Sessions API](https://developer.fastspring.com/reference/createsession). The API returns an `id` field — that's the Session ID to pass here.

```js
sdk.checkout('<SESSION_ID>', {
    onSuccess: () => {
        console.log('Session loaded — checkout is ready');
    },
    onError: (error) => {
        console.error('Session load failed:', error);
    },
});
```

---

## Complete example

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
            onSessionLoaded:  (data)  => console.log('Session loaded:', data),
            onOrderCompleted: (data)  => console.log('Order completed!', data),
            onPaymentFailed:  (error) => console.error('Payment failed:', error),
        });

        // Email component (email)
        const emailComponent = sdk.components.create('fs-email', {});
        emailComponent.mount('#email-element');

        // Apple Pay component (renders only in eligible browsers)
        const applePayComponent = sdk.components.create('fs-apple-pay', {});
        applePayComponent.mount('#apple-pay-element');

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
            onSuccess: () => console.log('Session loaded'),
            onError:   (err) => console.error('Session load failed:', err),
        });
    </script>

</body>
</html>
```

---

## Troubleshooting

**The card form doesn't render — browser console shows a `frame-ancestors` or CSP error.**
Your local origin isn't on the allow list for your component checkout. Add it via **Checkouts → Component Checkouts → [your checkout] → Implementation → Step 1** in the FastSpring app, then refresh.

**`sdk.checkout()` calls `onError` with a 404 or session-not-found.**
The session ID is invalid or expired. Verify your server is creating the session right before calling `sdk.checkout()`, and that the session hasn't already been used or timed out.

**Components don't appear after `.mount()`.**
The mount target element isn't in the DOM yet. Ensure your `<div id="card-element">` (and others) are present in the HTML *before* the script that calls `.mount()`, or wrap your code in a `DOMContentLoaded` listener.

**The Pay Button stays disabled.**
The button activates after the session loads. Confirm `sdk.checkout()` was called with a valid session ID and that `onSuccess` fired without an error.

**The SDK script returns 404 or fails to load.**
Verify the CDN URL and version. The current supported version is shown in Step 1; if a newer version has been released, update the URL accordingly.

---

## Next steps

- **Full styling reference** — [`style-reference.md`](./style-reference.md) lists every property, type, default, and state variant for every component.
- **[Sessions API](https://developer.fastspring.com/reference/sessions-overview)** — covers session creation, customer details, item overrides, and pricing.
- **Configure your checkout** — adjust allowed domains, currencies, and payment-method save behavior in the FastSpring app under **Checkouts → Component Checkouts → [your checkout]**.