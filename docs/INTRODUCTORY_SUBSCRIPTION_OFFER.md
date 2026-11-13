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
  fresh GBP pricing. The delayed staging fixture verified Recharge creation
  and retry readback on the matching plan.
- The normal worker requires verified Shopify fulfillment and revised prepaid
  coverage. A staging read confirmed that Recharge can show all three orders
  processed while Shopify still has no fulfillment records. Those deliveries
  stay pending under the normal policy.
- The Firestore emulator ran the normal worker and Recharge adapter with
  a simulated provider. When the final delivery arrived after the original
  coverage end, the worker sent zero create requests until revised coverage
  ended. Concurrent workers, a lost response and retries then produced one
  £9 monthly subscription request, one simulated subscription and one
  milestone. The staging provider result is recorded in section 1.
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
  The paid fixture’s original schedule is now retained in staging, and the
  guarded manual worker has passed CI and deployed. The Shopify connector
  verified read access for an operator, including the test checkout order.
  A dedicated staging Shopify app now grants read-only product and order
  access, including older orders. Its credential is in staging Secret
  Manager. A manual read-only worker preflight used that credential and
  held for outstanding deliveries.
- Recharge confirmed that the final prepaid shipment was delayed by seven
  days and that the earlier shipment kept its original date. The source had
  no renewal scheduled and no monthly continuation existed. Shopify had
  yet to record fulfillment for the first shipment, and the two later
  prepaid orders were still queued. The seven-day delay moved the coverage
  end from 13 to 20 February 2027 (UTC). The deployed manual worker uses the
  real clock. The earlier accelerated fixture proved checkout and renewal.
  The delayed fixture later passed provider creation and retry readback
  using the private staging test clock.

## Separate production pilot draft

A separate £1 product was created in production Shopify on 13 November 2026
for a future controlled pilot. It remains Draft with no sales channels selected.
The public product URL returned a page-not-found response. No selling plan
was attached and no order or payment was created. The existing subscription
plans and the disabled offer are unchanged.

Keep the pilot product unpublished until the checkout restriction and
production continuation have been verified and the paid pilot is approved.

## Production launch checklist

The delayed staging purchase passed the normal worker switch in section 1.
Next, finish the staging website checkout and prepare production runtime
and catalogue mappings in parallel. Keep the production
offer disabled and the pilot product Draft with no sales channels.

### Execution order

Use the checkboxes below to record verified results. If a step depends on
access, CI, or owner input, record that dependency and take the next available
step. The production offer stays disabled and the pilot stays unpublished
through preparation.

1. Confirm the fulfillment fix reached staging. The protected deployment
   passed. Preserve the existing £9 subscription and its retry evidence.
2. Audit the live Shopify catalogue read-only. Match the eligible blends and
   capsules to exact variant IDs, current UK prices, stock and shipping.
3. Verify Recharge mappings for those variants using dedicated production
   read access. Confirm three monthly deliveries under one prepaid payment,
   plus a monthly plan with 10% off. The dedicated Recharge credential is
   currently outstanding. Continue with steps 4 and 5 while that is resolved.
4. Wire the staging website offer to Firebase eligibility and checkout
   preparation. Verify the signed-in mobile and desktop journeys, discount
   rejection, repeat eligibility and existing purchase paths.
5. Prepare the production continuation worker behind disabled offer controls.
   Verify current pricing, fulfillment and revised coverage, durable payment
   evidence, cancellation and refund holds, and one continuation on retries.
6. Prepare production deployment and monitoring. Verify project, store,
   credential scopes, webhooks and permissions. Exercise the ability to
   pause new checkouts while servicing existing subscriptions. Check
   failure/recovery correlation and live purchase telemetry.
7. Review the unpublished pilot and prove the tester restriction before
   opening access. Keep the pilot read-only if the restriction cannot be
   enforced. Prepare the owner QA and rollout handoff.
8. After owner approval, run the controlled paid pilot. Verify one payment,
   redemption and correct prepaid schedule. Record who will check the
   normal continuation and renewal when due.
9. After launch approval, open the offer to customers, verify normal purchases
   and monitoring, and use the checkout pause if the launch needs to stop.

### 1. Prove the normal switch in staging

- [x] Verify the app opens Shopify with the exact prepaid plan and automatic
  £22.50 subtotal. Verify that a different signed-in buyer cannot use the
  discount.
- [x] Confirm the simulated £27.49 Shopify payment (£22.50 plus £4.99
  shipping) and one Firebase redemption.
- [x] Confirm the final prepaid shipment's one-week delay in Recharge moves
  the retained coverage end from 13 to 20 February 2027 (UTC).
- [x] Verify the deployed observer handles a controlled webhook burst and
  duplicates for the existing paid order without opening incidents.
- [x] Retain the original schedule for the paid staging fixture. Two
  read-only runs used that baseline and fresh provider reads and held
  continuation while deliveries were outstanding.
- [x] Connect the stored payment and schedule to the normal worker in quote
  mode. A read-only staging run held for outstanding deliveries even when
  Recharge charge lookups were disabled.
- [x] Connect the manual staging worker to the guarded Recharge writer.
  Backend CI and the protected staging deployment passed. The later
  fulfillment-based provider test is recorded below.
- [x] Provision scoped staging Shopify delivery and catalogue access. The
  credential is stored in staging Secret Manager.
- [x] Use that credential in a read-only preflight. It read the paid
  enrollment and current £10 catalogue price, then held continuation for
  outstanding deliveries using the retained schedule and fresh provider reads.
- [x] Verify the normal worker's delay and retry logic in the Firestore
  emulator. The simulated provider received one £9 create request after
  revised coverage ended, and concurrent execution and a lost response
  produced one confirmation.
- [x] Run the private staging operator command with scoped Shopify token
  exchange and a fixed clock for the delayed fixture. This uses real staging
  Firestore, Recharge and Shopify reads. Backend PR #100 contains the command.
- [x] Confirm the worker holds at both the original and revised coverage ends
  while deliveries are outstanding. Both live staging runs left the
  continuation operation and success milestone absent.
- [x] Process the two remaining £0 prepaid staging shipments in Recharge.
  Both generated linked Shopify staging orders. The worker still held at the
  original and revised coverage ends, with zero continuation operations and
  zero success milestones.
- [x] Verify all three linked orders are Fulfilled in Shopify staging.
  Successful fulfillment of the exact paid line is sufficient. The worker
  does not depend on carrier delivery status or a manual Delivered update.
- [x] With all three orders fulfilled, run the staging test clock just before
  the revised coverage end on 20 February 2027 at 05:00 UTC. The worker
  held for prepaid coverage and recorded no continuation or success milestone.
- [x] At the revised cutoff, verify one £9 monthly Recharge subscription
  with the mapped terms and its first charge scheduled for 21 February.
  Two retries verified the same subscription and one durable Firebase
  confirmation. The worker verified the prepaid source had no renewal
  scheduled before creation. No charge was processed.
- [ ] Verify the worker stops on cancellation, refund, missing evidence or
  an ambiguous create response. Confirm the logs provide enough information
  to investigate and retries reuse the original incident. The existing
  accelerated test already covers a simulated monthly renewal.

### 2. Finish the website purchase flow in staging

- [x] Verify the staging browser transport after backend PR #103 deployed.
  Preflight allows the exact development storefront with Authorization and
  Content-Type. A foreign origin gets a mismatched origin and cannot read
  the response. Anonymous checkout preparation returns HTTP 401.
- [ ] Verify browser sign-in via the Shopify Customer Account API and the
  authenticated Firebase checkout handoff. Browser identity is tracked in
  [FLY-172](https://linear.app/flyinghorsecoffee/issue/FLY-172).

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

- [x] Provision a separate read-only Shopify app for production. Verified the
  production credential can read products and orders older than 60 days.
  Worker integration remains part of the protected deployment check below.
- [x] Verify that a signed production Shopify sample reaches Firebase purchase
  telemetry. A naturally occurring eligible purchase still needs PostHog
  verification.
- [x] Audit the live Shopify variant IDs, UK prices and inventory policy for
  the five products linked from the subscriptions page. The 13 November
  2026 read-only audit returned 55 variants across five active products.
  The 54 bagged variants had no inventory block. All Day Brewing Capsules
  had no sellable stock and Shopify blocks overselling for that variant.
- [x] Verify Shopify selling-plan terms on all five catalogue products. The
  product-level plan groups returned monthly billing and delivery at 10%
  off. No three-delivery prepaid plan appeared in those groups.
- [ ] Verify each Recharge prepaid and monthly plan mapping and the shipping
  and fulfillment routes. Confirm one payment for three monthly deliveries
  and the current applicable variant price with 10% off. Leave the capsule
  variant out of checkout while its inventory remains blocked. Check the
  treatment of existing subscriber contracts.
- [x] Verify the unregistered production Shopify reader can fetch the
  current UK price on one variant from each catalogue product. Backend
  PR #102 merged. Provider-linked fulfillment verification remains open.
- [ ] Configure and deploy the production worker through protected CI. Verify
  its store, Firebase project, permissions, schedule, webhook registrations
  and separate offer controls. Confirm it can still obtain verified delivery,
  refund and cancellation evidence when the original order or charge is
  outside a provider's ordinary read window.
- [ ] Verify live enrollment correlation, current price and plan.
  Confirm production monitoring can
  identify a real failure, correlate retries and report recovery. Verify one
  eligible live purchase event reaches PostHog.
- [ ] Test the rollout controls. New offer checkout must be pausable while
  the worker continues to honour paid prepaid orders and existing
  subscriptions. Record who owns rollout, monitoring and recovery.

#### Shopify catalogue audit — 13 November 2026

These are Shopify UK catalogue prices before discounts and shipping. They
are evidence for the mapping review. The worker must read current pricing
at execution.

| Product | 350g options and price | 1kg options and price | Other |
| --- | --- | --- | --- |
| Espresso Blend | 7 at £16.95 | 7 at £35.95 | |
| Espresso Single Origin | 7 at £18.95 | 7 at £37.95 | |
| Filter Single Origin | 6 at £18.95 | 6 at £37.95 | |
| Caffeine Free | 7 at £19.95 | 7 at £39.95 | |
| All Day Brewing Capsules | | | 1 at £5.99, inventory blocked |

[Live subscription catalogue](https://flyinghorsecoffee.com/pages/subscriptions)

### 4. Run a controlled live website pilot

- [x] Create the £1 pilot product as a Draft with no sales channels. Confirm
  the production product URL returns page not found.
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
