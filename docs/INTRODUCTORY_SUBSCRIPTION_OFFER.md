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
  yet to record fulfillment for the first shipment. A read-only check using
  the original schedule and fresh provider reads projected the coverage end
  one week later and held while deliveries were outstanding. The deployed
  worker still needs the original schedule and authenticated delivery access.
  Wire the normal worker
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

## Production launch checklist

Start with the normal continuation test and the staging website checkout.
Those can proceed in parallel. Keep the production offer disabled and the
pilot product Draft with no sales channels while this work is in progress.

### 1. Prove the normal switch in staging

- [x] Verify the app opens Shopify with the exact prepaid plan and automatic
  £22.50 subtotal. Verify that a different signed-in buyer cannot use the
  discount.
- [x] Confirm the simulated payment, one Firebase redemption, and the final
  prepaid shipment's one-week delay in Recharge.
- [x] Verify the deployed observer handles a controlled webhook burst and
  duplicates for the existing paid order without opening incidents.
- [ ] Connect the normal worker to scoped staging Shopify and Recharge
  readers and the paid enrollment. Use the exact prepaid orders, confirmed
  Shopify deliveries, current cancellation and refund state, and a revised
  coverage schedule that preserves the original paid period.
- [ ] Set up a repeatable staging-only time control for the worker so the
  timing test can run without waiting months. Keep real provider evidence
  requirements in place. Use only verified test fulfillment records for this
  fixture and leave production on its normal clock.
- [ ] On the delayed fixture, verify zero monthly subscriptions while the
  last delivery is outstanding, zero at the original coverage end, and zero
  after delivery while revised coverage remains.
- [ ] At the revised boundary, verify one £9 monthly subscription in Recharge
  with the exact mapped plan, monthly cadence and first charge after coverage.
  Retry the same source and verify the same subscription and one Firebase
  confirmation. Check that the prepaid source has no renewal scheduled.
- [ ] Verify the worker stops on cancellation, refund, missing evidence or
  an ambiguous create response. Confirm the logs provide enough information
  to investigate and retries reuse the original incident. The existing
  accelerated test already covers a simulated monthly renewal.

### 2. Finish the website purchase flow in staging

- [ ] Connect the website offer entry to signed-in eligibility and checkout
  preparation in Firebase. Preserve one redemption per customer across
  website, app and eligible products.
- [ ] On mobile and desktop, verify the product and variant selection, three
  prepaid deliveries at 25% off, shipping, and the 10% monthly continuation.
  Use Shopify's subscription agreement at checkout.
- [ ] Verify checkout receives the exact variant, prepaid plan and discount.
  If the discount is rejected, stop the offer checkout. Verify the website
  payment and redemption with a staging test order or equivalent verified
  evidence from that website path.
- [ ] Verify ordinary one-off purchases and existing subscription options still
  work. Check an eligible buyer, a prior subscriber, an already-redeemed
  buyer, and a retry after an interrupted checkout.

### 3. Prepare production with the offer disabled

- [ ] Verify the live Shopify variant and Recharge plan mappings for each
  in-scope 350g and 1kg blend and capsule. Confirm the prepaid plan bills
  once for three monthly deliveries, and the mapped monthly plan uses the
  current applicable variant price with 10% off. Check stock, currency,
  shipping, and the treatment of existing subscriber contracts.
- [ ] Configure and deploy the production worker through protected CI. Verify
  its store, Firebase project, permissions, schedule, webhook registrations
  and separate offer controls. Confirm it can still obtain verified delivery,
  refund and cancellation evidence when the original order or charge is
  outside a provider's ordinary read window.
- [ ] Verify the live read-only checks for the signed webhook, enrollment
  correlation, current price and plan. Confirm production monitoring can
  identify a real failure, correlate retries and report recovery. Verify one
  eligible live purchase event reaches PostHog.
- [ ] Test the rollout controls. New offer checkout must be pausable while
  the worker continues to honour paid prepaid orders and existing
  subscriptions. Record who owns rollout, monitoring and recovery.

### 4. Run a controlled live website pilot

- [ ] Review the pilot product, exact plans and price before changing
  visibility. The £1 draft stays unpublished until the owner approves the
  pilot visibility and purchase steps.
- [ ] Verify the direct-link experience and check discovery through search,
  collections and recommendations, including third-party tools. Verify
  Shopify checkout enforces the designated signed-in tester. Test a copied
  link, a different account and direct cart access. Keep the live pilot
  read-only if checkout cannot enforce that restriction.
- [ ] After owner approval, submit one real pilot order using normal live
  payment settings. Verify the total, single payment and redemption, correct
  Recharge source, prepaid schedule, and monitoring events. A normal live
  renewal can only be observed when it comes due. Track its due date and
  assign someone to verify it.

### 5. Open to customers and monitor

- [ ] Get owner QA sign-off and launch approval. Confirm the final catalogue,
  product-page copy and customer entry points. Enable the approved offer
  checkout and list only the intended products or offer entry.
- [ ] Verify an eligible customer can reach the offer and check out, and that
  existing purchase paths still work. Check payment, redemption and alert
  records after launch and review the first normal continuation when due.
- [ ] If rollout needs to pause, disable new offer checkouts and remove the
  offer entry from discovery. Keep existing prepaid deliveries and monthly
  subscriptions running. Record what failed and the checks required to reopen.

[Continuation worker](https://linear.app/flyinghorsecoffee/issue/FLY-133) ·
[Website checkout](https://linear.app/flyinghorsecoffee/issue/FLY-135) ·
[Production rollout](https://linear.app/flyinghorsecoffee/issue/FLY-138)
