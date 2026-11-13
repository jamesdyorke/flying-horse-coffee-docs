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
  renewal. A read-only recheck confirmed the same result without creating a new
  order or changing the subscription. The production schedule still needs its
  own verification.
- Production Firebase purchase telemetry is deployed. A signed Shopify sample
  reached Firebase successfully. Verify one PostHog event from a naturally
  occurring eligible live purchase.
- The five production prepaid plans are merchant-only and the offer is disabled.
  Shopify checkout and the production continuation are still pending verification.
- Backend PR #83 is merged. It validates mapped public 10% monthly plans and
  fresh GBP pricing. Verify Recharge creation and readback on a matching
  staging plan.
- The normal worker requires verified delivery evidence and revised prepaid
  coverage. A staging read confirmed that Recharge can show all three orders
  processed while Shopify still has no fulfillment records. Those deliveries
  stay pending under the normal policy.
- The Firestore emulator ran the normal worker and Recharge adapter with
  a simulated provider. When the final delivery arrived after the original
  coverage end, the worker sent zero create requests until revised coverage
  ended. Concurrent workers, a lost response and retries then produced one
  £9 monthly subscription request, one simulated subscription and one
  milestone. The provider test now has a paid staging source and still needs
  verified delivery and coverage records.
- A separate £10 product is now configured in the development store with an
  development product page, a public 10% monthly plan, and a three-delivery prepaid
  plan that expires after one charge. Both Recharge plans have linked Shopify
  selling-plan IDs. The prepaid plan has no built-in discount.
- The fixture is Active on the development Online Store and Headless channels
  for app checkout testing. Shopify's Storefront API returned the £9 monthly
  price and accepted the exact prepaid selling plan in an unpaid cart with a
  £30 subtotal.
- Backend PR #87 merged and deployed the second fixture to Firebase staging.
  Live checks rejected an unauthenticated request and a mismatched selling
  plan. Shopify rejected an invalid discount in the unpaid development cart.
  The unpaid staging cart returned £22.50 before shipping for the exact
  prepaid plan. Signed-in hosted checkout confirmed £22.50 plus £4.99 shipping
  after manual discount entry. It rejected the same discount for a different
  account. The iOS checkout and Firebase offer-details updates are available
  in staging. The app withheld checkout for an ineligible account. After
  switching to the eligible account, the app opened Shopify checkout with
  the exact prepaid plan and the £7.50 discount already applied. The
  merchandise subtotal was £22.50 before shipping. No order was submitted.
  A separate hosted staging checkout then confirmed a simulated payment at
  £22.50 plus £4.99 shipping. Firebase confirmed the paid enrollment and
  redemption.
- The staging observer processed four signed test webhook receipts for the
  existing paid order after the rate-limit fix deployed. Four duplicate
  deliveries were recognized at ingress. All originals completed and no
  failure or recovery alerts were generated for the burst.
- The staging Recharge adapter for the public monthly plan passed local
  request and readback tests. It sends the quoted amount and checks that the
  provider returns exactly one matching subscription with the expected
  cadence and charge date. The adapter has merged. A follow-up staging
  component now matches each prepaid Recharge order to Shopify fulfillment
  and refund evidence. A read-only preflight combines that evidence with
  verified payment, a supplied coverage schedule and current pricing. Local
  tests held for an outstanding third delivery and quoted £9 at the boundary.
  A read-only staging check verified the actual £10 Shopify variant price and
  matching Recharge plan, yielding a £9 quote with a synthetic coverage
  schedule. The Shopify read transport passed CI and deployed to staging.
  The worker still needs Shopify Admin access and an authoritative coverage
  source before the provider test can run.
- Recharge confirmed that the final prepaid shipment was delayed by seven
  days and that the earlier shipment kept its original date. The source had
  no renewal scheduled and no monthly continuation existed. Shopify had
  yet to record fulfillment for the first shipment. Wire the normal worker
  to verified deliveries and revised coverage, then verify zero creates
  before that boundary and one £9 monthly subscription after it, including
  on retries. The earlier accelerated fixture proved checkout and renewal.

## Separate production pilot draft

A separate £1 product was created in production Shopify on 13 November 2026
for a future controlled pilot. It remains Draft with no sales channels selected.
The public product URL returned a page-not-found response. No selling plan
was attached and no order or payment was created. The existing subscription
plans and the disabled offer are unchanged.

Keep the pilot product unpublished until the checkout restriction and
production continuation have been verified and the paid pilot is approved.

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
