# FastSpring Checkout SDK — Vanilla JS Integration Guide

A step-by-step guide for integrating the FastSpring Checkout SDK into a plain HTML/JavaScript page — no build tools or frameworks required.

## What you'll have at the end

A working credit-card form on your page that accepts a real test order: the customer enters card details, clicks **Pay**, and FastSpring processes the payment end-to-end. By the end of this guide, you'll be able to take a session ID returned by your server and route the buyer through a fully styled, embedded checkout.

## Prerequisites

- A FastSpring account with a **component checkout** configured in your store.
- Your component checkout's **URL** — the path you'll pass to `FastSpring.init`. Find it in the FastSpring app under **Checkouts → Component Checkouts → [your checkout] → Implementation**.
- A **server endpoint** that creates sessions via the FastSpring [Sessions API](https://developer.fastspring.com/reference/createsession) — you'll generate a session ID on each checkout attempt and pass it to the SDK.
- For local development: your local origin (e.g. `http://localhost:5173`) added to the **allow list** for your component checkout — see the callout in [Step 2](#step-2--initialize-the-sdk).

## Overview

The integration has six steps:

1. Include the SDK
2. Initialize the SDK (`FastSpring.init`)
3. Create and mount the **Card** component
4. Create and mount the **Pay Button** component
5. *(Optional)* Create and mount the **Disclosures** component
6. Call `sdk.checkout(sessionId)` to start the checkout flow

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

Call `FastSpring.init()` once the SDK script has loaded. It returns an `sdk` instance you'll use in all subsequent steps.

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
        env: 'qa2',       // Omit for production. Use 'qa2' for QA.
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

## Step 6 — Start the Checkout

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

    <!-- Card mount target -->
    <div id="card-element"></div>

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

        // Card component
        const cardComponent = sdk.components.create('fs-card', {});
        cardComponent.mount('#card-element');

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

- **Full styling reference** — [`style-reference.md`](./style-reference.md) lists every property, type, default, and state variant for all three components.
- **[Sessions API](https://developer.fastspring.com/reference/sessions-overview)** — covers session creation, customer details, item overrides, and pricing.
- **Configure your checkout** — adjust allowed domains, currencies, and payment-method save behavior in the FastSpring app under **Checkouts → Component Checkouts → [your checkout]**.
