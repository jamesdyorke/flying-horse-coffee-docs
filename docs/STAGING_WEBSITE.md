# Staging website

[Open Flying Horse Coffee staging](https://development-flying-horse-coffee.myshopify.com/).

This is the development Shopify store, separate from the production shop at
flyinghorsecoffee.com. Bookmark the staging homepage rather than a checkout URL:
checkout links belong to individual sessions and can expire.

## Test journey

Use staging to browse products, add an item to the cart and inspect checkout.
Before submitting a test order, confirm the checkout is for the development
store and its test payment mode is enabled. Use test payment details only.

The Recharge post-purchase offer is reached after the initial test payment,
before the final order confirmation. An eligible one-time purchase can offer
Espresso Blend as an additional item; accepting it adds the item to the test
order, while declining continues without it. Subscription purchases and some
payment methods can follow a different journey.

## Status as of 14 November 2026

- The existing staging store and checkout are accessible.
- The Recharge post-purchase offer has been tested with a real catalog product,
  including accepting and declining the offer, using test payments.
- Matching the storefront, checkout branding and approved Recharge designs to
  production is in progress. The staging homepage is the stable entry point;
  its appearance may change during this work.
- These checkout tests do not establish end-to-end synchronization with the
  staging app or backend.

Access credentials, customer records and private test evidence are intentionally
excluded from this public repository.
