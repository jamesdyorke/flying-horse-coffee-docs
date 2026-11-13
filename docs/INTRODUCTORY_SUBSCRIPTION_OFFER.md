# Introductory subscription offer

This is the shared rollout plan for the website, iOS and Firebase backend.
Implementation guides and detailed test evidence live in the owning repositories.

## Offer

Eligible customers can buy three monthly deliveries upfront at 25% off.
After the prepaid deliveries and coverage are complete, the subscription
continues monthly at 10% off the current applicable price. Checkout shows the
selected product, payment and shipping totals, and the subscription terms
through Shopify's existing checkout. Eligibility and redemption apply once
per customer across the offer catalogue.

The intended catalogue includes the existing subscription coffees in 350g and
1kg and the capsules, subject to stock and confirmed plan coverage. Keep the
existing monthly plans and subscriber contracts in place.

## Verified as of 13 November 2026

- A staging purchase proved the introductory payment and redemption, one
  10%-off monthly continuation using accelerated timing, and its first paid
  renewal. The production schedule still needs its own verification.
- Production Firebase purchase telemetry is deployed. A signed Shopify sample
  reached Firebase successfully. Verify one PostHog event from a naturally
  occurring eligible live purchase.
- The five production prepaid plans are merchant-only and the offer is disabled.
  Shopify checkout and the production continuation are still pending verification.
- Backend PR #83 adds validation for mapped public 10% monthly plans and fresh
  GBP pricing. Verify Recharge creation and readback on a matching staging plan.

## Before a paid pilot

1. Verify that the website displays the offer and hands off the exact selected
   prepaid plan and discount to Shopify. If Shopify rejects the discount, stop
   the offer checkout.
2. Verify continuation using authoritative prepaid delivery and coverage records,
   including a delayed final delivery. Verify the current price for the exact
   variant, and confirm that repeated processing produces one continuation.
3. Keep the pilot restricted to one designated tester at checkout. Verify that
   a copied link or direct cart attempt cannot bypass the restriction.
4. After approval, verify the real pilot payment and redemption. Verify the
   monthly switch when coverage ends. Use staging for accelerated QA.
5. Open the offer to customers after QA. If the rollout needs to pause, stop
   new offer purchases while honouring existing prepaid orders and subscriptions.

[Production rollout](https://linear.app/flyinghorsecoffee/issue/FLY-138) ·
[Website checkout](https://linear.app/flyinghorsecoffee/issue/FLY-135) ·
[Continuation worker](https://linear.app/flyinghorsecoffee/issue/FLY-133)
