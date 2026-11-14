# Staging website

[Open Flying Horse Coffee staging](https://development-flying-horse-coffee.myshopify.com/).

This is the development Shopify store, separate from the production shop at
flyinghorsecoffee.com. Bookmark the staging homepage rather than a checkout URL:
checkout links belong to individual sessions and can expire.

## Test journey

Use staging to browse products, add an item to the cart and inspect checkout.
Before submitting a test order, confirm the checkout is for the development
store and its test payment mode is enabled. Use test payment details only.

The Recharge post-purchase offer can appear after the initial test payment,
before the final order confirmation. An eligible one-time purchase can offer
Espresso Blend as an additional item; accepting it adds the item to the test
order, while declining continues without it. Subscription purchases and some
payment methods can follow a different journey.

## Status as of 14 November 2026

- The existing staging store and checkout are accessible.
- Earlier staging tests verified accepting and declining a Recharge
  post-purchase offer with a real catalog product and test payments. Two later
  fresh checkouts in an isolated browser session displayed the offer directly
  with the redesigned theme and checkout branding unchanged; declining both
  completed the expected test orders. Acceptance was not repeated in this run.
- Intermittent empty post-purchase screens remain unresolved. The regular test
  session retained a theme-preview parameter; the successful isolated session
  did not. This is a diagnostic difference, not proof of the cause, because
  browser storage and login state also differed. No theme or branding rollback
  was performed during this comparison. Start a new checkout from the staging
  homepage rather than reuse an old checkout or theme-preview link.
- The staging default theme is **Flying Horse | Production match | Staging**,
  copied from the production theme with staging assets and navigation. The
  previous **Horizon** theme remains available in staging for rollback.
- Checkout uses the production horse logo, logo size and black button/link
  colours. The branded Recharge overlay, cart and product widget are enabled
  in staging. Cart styling has passed desktop and phone-width review; upgrading
  a cart item to a subscription and removing it have been tested.
- Staging retains its existing catalog and subscription test plans. In
  particular, Espresso Blend currently offers a 25% subscription test discount;
  production's 10% subscription messaging remains in the copied theme. This is
  a design and checkout test environment, not a complete catalog/pricing mirror.
- External booking and gift-card purchase actions in the copied footer lead
  to a staging information page. Other staging content, including footer
  navigation and product specifications, can differ from production.
- These checkout tests do not establish end-to-end synchronization with the
  staging app or backend.

Access credentials, customer records and private test evidence are intentionally
excluded from this public repository.
