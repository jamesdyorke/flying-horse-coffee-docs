# Introductory subscription offer

The first release covers the website and its Firebase backend. iOS is a
follow-up. Keep existing iOS evidence for that release.
Implementation guides and detailed test evidence live in the owning repositories.

## Offer

Customers can buy three monthly deliveries upfront at 25% off.
After the prepaid deliveries and coverage are complete, the subscription
continues monthly at 10% off the current applicable price. Checkout shows the
selected product, payment and shipping totals, and the subscription terms
through Shopify's existing checkout. If the current Shopify and Recharge
setup cannot enforce one redemption per customer, the website will allow
repeat purchases. Every distinct paid prepaid order can start its own
monthly subscription. Retries of one order must reuse its continuation.

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
On 14 November, Shopify reported Draft and no publication timestamp, and
both storefront product URLs returned 404. Recharge now has both
Merchant Portal-only pilot plans. The read-only audit verified the saved
25% prepaid and 10% monthly terms, Draft state, no Online Store publication
and zero Shopify selling-plan groups. Shopify checkout and the complete Shopify
sales-channel list remain unverified. Backend PR #116 added the repeatable read-only pilot
audit. Both required checks passed before merge, and the protected staging
deployment succeeded for the merge commit. [Pilot audit PR](https://github.com/jamesdyorke/flying-horse-coffee-firebase-backend/pull/116)

Recharge documents storefront checkout as the supported
subscription path, so a draft invoice cannot substitute for the pilot's
Recharge and website checkout proof.
[Recharge checkout troubleshooting](https://support.getrecharge.com/hc/en-us/articles/12075818832663-Customer-missing-from-Recharge)

Keep the pilot product unpublished until the checkout restriction and
production continuation have been verified and the paid pilot is approved.

## Production launch checklist

Carry forward the completed staging proof. Prepare the Grow checkout rule,
isolated production pilot and production backend in parallel. Keep the
production offer disabled and the pilot product Draft with no sales channels.

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
   plus a monthly plan with 10% off. The catalogue audit passed. Production
   order, charge and subscription reads now return HTTP 200. The dedicated
   credential also has subscription write permission for the future worker.
   The production worker remains disabled.
4. Carry forward the existing staging proof for code-free prepaid pricing,
   payment and redemption, and the delayed, duplicate-safe £9 continuation.
   Build the customer-facing entry in the existing subscription widget for an
   unpublished production theme preview. Keep the standalone staging page
   available for targeted checks if a new integration changes a proven path.
5. Prepare the production continuation worker behind disabled offer controls.
   Verify current pricing, fulfillment and revised coverage, durable payment
   evidence, cancellation and refund holds, and one continuation on retries.
6. Prepare production deployment and monitoring. Verify project, store,
   credential scopes, webhooks and permissions. Register and verify the
   production Shopify web sign-in client before enabling checkout. Exercise the ability to
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
- [x] Verify with simulated provider evidence that cancellation or refund
  after the initial quote stops the create request. Backend PR #108 passed
  CI and merged. Retries retain the same claim and incident correlation.
- [ ] Verify the worker stops on cancellation, refund, missing evidence or
  an ambiguous create response. Confirm the logs provide enough information
  to investigate and retries reuse the original incident. The existing
  accelerated test already covers a simulated monthly renewal.

The 14 November read-only check of the original paid staging fixture confirmed
one payment and redemption, one linked monthly continuation, one paid renewal
and the next renewal queued. All three milestones were delivered. Acceleration
and repair were disabled. The check made no provider or Firestore writes and
sent no alerts.

### 2. Finish the website purchase flow in staging

- [x] Verify the staging browser transport after backend PR #103 deployed.
  Preflight allows the exact development storefront with Authorization and
  Content-Type. A foreign origin gets a mismatched origin and cannot read
  the response. Anonymous checkout preparation returns HTTP 401.
- [ ] Verify browser sign-in via the Shopify Customer Account API and the
  authenticated Firebase checkout handoff. Browser identity is tracked in
  [FLY-172](https://linear.app/flyinghorsecoffee/issue/FLY-172).

- [x] Configure a dedicated staging Customer Account API web client with
  the exact HTTPS storefront origin and callback. The normal storefront
  account session is available, and the offer still needs a customer token
  Firebase can verify. Leave the mobile client configuration in place.
  In the development store's Headless channel, select a separate Public
  (web app) client. Register these values:

  - Public web Client ID: `9a4225e4-67c4-4dfc-bc2d-67e7a2b7f029`
  - JavaScript origin: `https://development-flying-horse-coffee.myshopify.com`
  - HTTPS callback: `https://development-flying-horse-coffee.myshopify.com/products/staging-public-continuation-test`

  Backend PR #106 merged and deployed successfully to staging. It supplies
  the sign-in helper. James reported completing the Shopify client setup.
  On 14 November, Shopify accepted the public Client ID in Chrome and
  redirected to the exact staging callback. After the theme asset was
  installed, the button completed token exchange with an existing test login.
- [x] Prepare a fresh eligible staging buyer and a verified, single-use
  discount bound to that buyer and the fixture. The fresh customer had no
  offer record. The active, unused £7.50 discount was verified in Shopify
  and configured in staging Firebase. The fresh buyer signed in and
  Firebase prepared the purchase successfully.
  Reuse that buyer and code for unpaid checkout previews while the discount
  is active. Refresh the staging fixture verification if its 30-minute window
  expires. Shopify counts a use when an order is submitted. For repeat paid
  staging tests, a multi-use code alone is insufficient because Firebase also
  records redemption per customer. Use fresh eligible buyers for paid tests.
- [x] Implement and test the browser cart handoff against fixtures. Backend
  PR #105 applies the discount automatically and returns a checkout link
  only for the exact staging prepaid plan at a £22.50 GBP subtotal.
  CI and the staging deployment passed. A separate unpaid request using
  the same cart query verified the exact prepaid plan and a £30 subtotal
  against the real staging Storefront API.
- [x] Wire the staging checkout button to Shopify sign-in and cart preparation.
  Backend PR #107 passed CI, merged and deployed to staging. The staging
  theme asset and Custom Liquid mount are installed on the development
  product template. Backend PR #109 accepts Shopify’s cart handoff URL.
- [x] Verify the unpaid checkout from the staging product button. On 14
  November, after the rebuilt Horizon asset was saved, the staging buyer
  signed in through Shopify and the button took them directly to checkout.
  The order summary showed three prepaid monthly deliveries at £30.00
  before discount, £7.50 automatically applied and a £22.50 subtotal before
  shipping. No code was entered and no order was submitted.
- [ ] On mobile and desktop, verify that signed-out shoppers can see and
  select the introductory option on eligible product pages. Show the three
  prepaid deliveries at 25% off, shipping and the 10% monthly continuation.
- [ ] Preserve the selected coffee, variant and prepaid plan through normal
  Shopify checkout. Verify that the plan applies the 25% price without a
  code and that ordinary monthly and one-off purchases retain their prices.
- [ ] For the repeatable route, verify two distinct purchases from the same
  account and replay both. Each paid order must produce one payment record
  and one continuation. Reuse the existing staged lifecycle evidence.


### 3. Prepare production with the offer disabled

Prioritise the 25% price on the Recharge prepaid plan and its Shopify link.
Verify the 25% prepaid price and existing 10% monthly option before
accepting payment. In parallel, verify deployment
and configuration of the disabled production continuation worker. Reuse the
recorded staging lifecycle results for the pilot decision.

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
- [x] Duplicate the current production theme for the unpublished preview.
  On 14 November, the copy was verified unpublished and both product
  templates matched the live copies, including the existing Recharge
  Subscription Widget app block.
- [ ] Prepare the 25% price on the Recharge prepaid plans. Check the
  exact variant, three monthly deliveries paid upfront, one prepaid charge,
  and no change to the 10% monthly plan. Keep the plans merchant-only for
  preparation. Selecting Storefront widget creates a Shopify selling plan
  and can expose it in the live widget. This is a launch step.
  [Recharge plan channels](https://support.getrecharge.com/hc/en-us/articles/33352577070999-How-to-create-a-Product-Subscription-Plan)
- [ ] Verify the existing subscription widget shows the prepaid option to
  signed-out shoppers and passes the chosen plan to Shopify checkout at
  the 25% price. A one-redemption gate is optional if Shopify and Recharge
  cannot enforce it on the current plans.
  [Website checkout](https://linear.app/flyinghorsecoffee/issue/FLY-135)
- [x] Verify the Recharge plans, terms and coverage for all 55 variants with
  the dedicated production read-only credential.
- [ ] Prove shipping, fulfillment and treatment of existing subscriber
  contracts. Keep the capsule variant out of checkout while inventory is
  blocked.
- [x] Store the exact prepaid plan ID per variant in the disabled production
  Firestore catalogue and verify it with the updated read-only Recharge audit.
- [x] Verify the unregistered production Shopify reader can fetch the
  current UK price on one variant from each catalogue product. Backend
  PR #102 merged. Provider-linked fulfillment verification remains open.
- [x] Verify the dedicated production Shopify credential exchanges for a
  token with the expected read-only scopes and returns a current UK price
  through the unregistered worker adapter. Backend PR #104 merged after
  both required checks and its staging deployment passed. Runtime secret
  binding remains part of production deployment.
- [ ] Set up production website sign-in in the live Shopify store’s Headless
  channel. James registers a separate Public (web app) Customer Account API
  client with the final production storefront origin and exact HTTPS callback.
  Keep the existing mobile client intact. Verify sign-in and Firebase
  customer identity through the approved preview before enabling offer
  checkout. [Website identity](https://linear.app/flyinghorsecoffee/issue/FLY-172)
- [ ] Configure and deploy the production worker through protected CI. Verify
  its store, Firebase project, permissions, schedule, webhook registrations
  and separate offer controls. The 14 November read-only production inventory
  showed the two Shopify paid-order functions. The production continuation
  runtime was absent. The Shopify payment reader and Recharge evidence join
  passed protected staging deployment in backend PRs #117 and #118. A
  matching production pilot payment remains open. Backend PR #119 added
  durable enrollment keyed by verified prepaid source, with transactional
  checks against duplicate Shopify orders and Recharge charges. It passed
  protected CI and staging deployment. Backend PR #120 added the read-only
  original schedule reader for the paid checkout and two queued £0 orders.
  Its fixture tests and protected staging deployment passed. A real pilot
  still needs to verify the Recharge response shape. Backend PR #121 added
  the fulfillment observation adapter and passed protected staging deployment.
  It joins retained Recharge order IDs to Shopify fulfillment and rechecks
  payment, refund and cancellation evidence before a continuation. Backend
  #122 added the disabled pilot-only writer and passed protected staging
  deployment. Backend #123 composed the private worker. Its synthetic
  end-to-end test held until revised coverage, quoted £0.90, then found one
  monthly continuation after a lost response and on retry. The pilot
  adapters have no production runtime registration. A live pilot and the
  wider customer catalogue worker remain separate.
  [Private pilot worker](https://github.com/jamesdyorke/flying-horse-coffee-firebase-backend/pull/123)
  [Original schedule reader](https://github.com/jamesdyorke/flying-horse-coffee-firebase-backend/pull/120)
  [Recharge evidence join](https://github.com/jamesdyorke/flying-horse-coffee-firebase-backend/pull/118)
  · [Paid enrollment](https://github.com/jamesdyorke/flying-horse-coffee-firebase-backend/pull/119) Confirm it can still obtain verified delivery,
  refund and cancellation evidence when the original order or charge is
  outside a provider's ordinary read window.
- [ ] Verify live enrollment correlation, current price and plan.
  Confirm production monitoring can
  identify a real failure, correlate retries and report recovery. Verify one
  eligible live purchase event reaches PostHog.
- [ ] Test the rollout controls. New offer checkout must be pausable while
  the worker continues to honour paid prepaid orders and existing
  subscriptions. Record who owns rollout, monitoring and recovery.

#### Production schedule evidence

- [ ] From the approved pilot, retain the original provider schedule alongside
  the verified paid enrollment before any reschedule. Compare its exact order
  IDs and provider dates with the refreshed schedule and Shopify fulfillment
  evidence. Verify that a delay extends coverage and that the first monthly
  charge falls after that boundary in Recharge’s configured processing window.
  Include a month-end and a daylight-saving boundary in the schedule tests.

Recharge documents Eastern Time as its default charge processing window,
including prepaid shipments, and allows stores to change the processing time.
Specific charge days and cutoffs can also shift the prepaid schedule. Confirm
the production settings and actual provider dates before applying the staging
UTC fixture rule to live enrollments.
[Processing time](https://support.getrecharge.com/hc/en-us/articles/13002255447703-Adjust-your-order-processing-time)
· [Charge-day schedules](https://support.getrecharge.com/hc/en-us/articles/360006293074-Charging-on-a-specific-day-of-the-week-or-month)

#### Code-free staging pricing

Unpaid Shopify carts using the same development variant showed £22.50 for
the new three-delivery prepaid plan (Recharge 23732091, Shopify 693083275612)
and £9.00 for the existing monthly plan. Both carts had zero discount codes.
The 25% adjustment is built into the prepaid plan. Before validation was
activated, a guest could receive a prepaid checkout URL. With the staging
development preview's exact-plan validation active, Shopify returned
`VALIDATION_CUSTOM` and no checkout URL for a guest prepaid cart. A guest
monthly cart still returned £9.00 and a checkout URL.

The staging preview uses a manual eligibility tag for the Website Test account
and limits the introductory plan to one unit per cart. An existing prepaid
checkout link showed an eligibility error and disabled payment for a guest.
After signing in as Website Test on the same checkout, the error cleared and
the three-delivery plan displayed a £22.50 merchandise subtotal without a code.
The test stopped before payment.

#### Production checkout

On 14 November 2026, the production Shopify Admin API confirmed Grow.
The custom eligibility rule ran in the development store. A native
one-redemption control for the same Recharge prepaid plan remains
unverified on production. James approved allowing repeat purchases if
the current Shopify and Recharge setup cannot enforce the limit. Use
Recharge plan pricing for the 25% and the existing Shopify checkout.
Scope production payment records to a verified paid checkout and reconcile
continuations by Recharge source subscription. Keep existing staging
redemption evidence as historical proof.

The production audit found zero Shopify links across the 55 Recharge prepaid
plans. The existing 10% monthly plans are already linked. Connecting the
prepaid plans to the storefront can expose them on the live widget, so
leave that step for launch approval.

#### Recharge plan audit — 14 November 2026

The live Recharge API audit verified 55 prepaid plans, one per Shopify
variant, and five product-wide monthly plans. All 55 mapped variants have
exactly one active prepaid plan. The five previously recorded prepaid IDs
are representative variant plans. The standalone audit in backend #114
assumed one prepaid ID per product and reported a coverage mismatch. A
complete product-filtered API read verified the full set.

| Product | Variants | Recharge prepaid IDs | Recharge monthly ID |
| --- | ---: | --- | --- |
| Espresso Blend | 14 | 23714198–23714211 | 2508960 |
| Espresso Single Origin | 14 | 23714222–23714235 | 2508979 |
| Filter Single Origin | 12 | 23714236–23714247 | 2509003 |
| Caffeine Free | 14 | 23714250–23714263 | 2509027 |
| All Day Brewing Capsules | 1 | 23714265 | 17212604 |

All prepaid plans ship monthly, charge every three months and expire after
one charge. They have zero built-in discount, Merchant portal visibility
only and no Shopify selling-plan links. The monthly plans charge and ship
monthly at 10% off and match the five expected Shopify selling-plan IDs.
Shopify’s complete variant-level read returned only those monthly plans.
Website checkout still needs the prepaid selling-plan links and proof of
the 25% discount. The capsule variant remains inventory-blocked.

The updated audit passed against all 60 exact provider records. The
production Firestore draft now stores the exact prepaid plan ID for all 55
variants. Readback confirmed the mapping and the disabled offer state.
[Backend audit update](https://github.com/jamesdyorke/flying-horse-coffee-firebase-backend/pull/115)

Production Firebase currently has only the existing Shopify paid-order receiver
and publisher active. Cloud Scheduler API is disabled. Protected deployment of
the continuation runtime and its scheduler remains open.
The current deployment scripts reject staging offer modes in production. Add a
production-specific runtime registration and verify its default-off controls in
CI before provisioning its scheduler. A normal production release of the
current code will not register this worker.

Backend PR #110 starts production integration with an unregistered quote-only
preview. Its tests hold until the revised coverage boundary, select the mapped
monthly plan and quote 10% off the current UK variant price without creating a
continuation. Backend PR #111 merged and its protected staging deployment
passed. The Recharge reader only fetches store identity and the mapped monthly
plan. The production preview remains unregistered. Backend PR #113 verified
that the preview logs diagnostics without writing Firestore telemetry.

The production Shopify reader returned current UK prices for one variant of
each bag size across the four bagged coffees and the capsule variant. The
preview calculation gave £15.26/£32.36 for Espresso Blend, £17.06/£34.16 for
both Single Origins and £17.96/£35.96 for Caffeine Free (350g/1kg). The
capsule result was £5.39 for audit only. These are sample calculations
before shipping. Production enrollment evidence and runtime registration
remain open.

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

[15 November handoff and fresh pilot purchase](INTRODUCTORY_OFFER_RESUME_2026-11-15.md)

- [x] Verify the unlisted £1 pilot via direct link. One bundle covers three
  prepaid deliveries and uses the linked 25% Recharge plan. The broader offer
  remains disabled.
- [x] Complete one paid website pilot. Shopify #5043 charged £2.25 for the
  bundle plus £2.90 UK shipping, £5.15 total. Recharge confirmed one checkout
  charge and two £0 shipments queued for 14 December 2026 and 14 January 2027.
- [x] Record the payment in Firebase. The private production observer verified
  the exact Shopify order and Recharge charge. An identical retry returned
  duplicate. [Backend PR #126](https://github.com/jamesdyorke/flying-horse-coffee-firebase-backend/pull/126)
- [x] Verify the production payment milestone. One production
  `offer_payment_confirmed` event arrived, and the Slack destination completed
  for `#backend-alerts`. [PostHog event](https://eu.posthog.com/project/238446/events/550c2740-70ac-54ee-bbaa-a9ff6573c35f/2026-11-14T20%3A43%3A56.139Z)
- [x] Deploy Firebase backend v0.3.0 to production with pilot intake and
  the public offer disabled. The corrected staging deployment for #128
  and the exact v0.3.0 staging candidate both succeeded. Production #131
  merged and deployed successfully. The release tag matches the merge
  commit, only the existing paid-order functions are active, and the
  production catalogue still reads `status: draft` and `enabled: false`.
  [Production deployment](https://github.com/jamesdyorke/flying-horse-coffee-firebase-backend/actions/runs/43643929938)
  · [Release](https://github.com/jamesdyorke/flying-horse-coffee-firebase-backend/releases/tag/v0.3.0)
- [x] Deploy v0.3.1 with private production pilot intake enabled and the
  public offer disabled. #132 scoped Firebase's retry acknowledgement to
  the new observer. #133 prepared v0.3.1 and #134 promoted the passing
  staging candidate. The Eventarc service account has invoker access on
  the private observer only.
  [Production deployment](https://github.com/jamesdyorke/flying-horse-coffee-firebase-backend/actions/runs/43646593585)
- [x] Prove payment deduplication for already-paid #5043 on private
  production intake. Two signed no-charge replays returned HTTP 200 and
  created one receipt. Eventarc initially received HTTP 403. A service-only
  invoker grant to its existing identity let redelivery reach the observer,
  which logged two duplicate enrollment outcomes. The original payment and
  outbox delivery timestamps remained unchanged. Firestore counted one
  enrollment and PostHog counted one production payment milestone.
  The automatic receipt handles schedule validation separately from payment.
  [PostHog event](https://eu.posthog.com/project/238446/events/550c2740-70ac-54ee-bbaa-a9ff6573c35f/2026-11-14T20%3A43%3A56.139Z)
- [x] Verify and retain the original prepaid schedule for #5043. The Recharge
  read taken on 14 November at 20:33 UTC, before the queued records were
  updated at 20:41 UTC, matches the linked live records. The original dates
  are 14 November, 14 December and 14 January at 05:00 UTC. Firebase retained
  the dates on the existing enrollment. The payment count remained one and
  the existing PostHog event stayed delivered with one attempt.
- [x] Verify the production pilot price and hold without creating anything.
  The live preflight verified the private £0.90 monthly plan. The read-only
  worker returned `prepaid_deliveries_outstanding`. The nominal cutoff is
  14 February 2027 at 05:00 UTC and can move if fulfilments are delayed.
  The public offer was disabled at verification.
- [x] Verify the private #5043 cutoff command in read-only mode against
  production. The original Recharge source is expired with no renewal queued.
  The operator held for outstanding deliveries and verified that the public
  catalogue is still disabled. The helper and local cutoff, price-cap and
  duplicate-write tests are in [backend PR #138](https://github.com/jamesdyorke/flying-horse-coffee-firebase-backend/pull/138).
  The schedule-recovery change merged in
  [backend PR #137](https://github.com/jamesdyorke/flying-horse-coffee-firebase-backend/pull/137).
- [x] Process both £0 prepaid shipments and verify all three Shopify
  fulfilments. Recharge preserved the original dates and created only the
  two £0 Shopify follow-ups, #5044 and #5045. Shopify shows #5043–#5045 all
  fulfilled and the original payment remains the only charge.
- [x] Run the early rehearsal against live #5043 with all three fulfilments.
  It returned `quote_ready` at £0.90 merchandise. The public offer remained
  disabled. #138 merged and deployed to staging. The read-only rehearsal
  merged in [backend #139](https://github.com/jamesdyorke/flying-horse-coffee-firebase-backend/pull/139).
- [ ] Review the separately invoked early switch in
  [backend #140](https://github.com/jamesdyorke/flying-horse-coffee-firebase-backend/pull/140)
  and choose an approved early billing date. Only then run the
  private switch for #5043. Verify one Recharge continuation, the one-charge
  cap and the queued total including any shipping before billing. No renewal
  was scheduled during preparation.
- [ ] After the approved renewal, verify the pilot subscription expired
  and Recharge has no second charge queued.
- [ ] Wire automatic production website enrollment and complete alert parity.
  The milestone route is enabled. Production failure and recovery routes are
  saved disabled pending an end-to-end Firebase incident feed. Staging's two
  hourly insight alerts are disabled. Recheck the production health signal
  before enabling corresponding hourly alerts.

#### Website production readiness compared with staging

| Area | Staging evidence | Production gap |
| --- | --- | --- |
| Storefront and checkout | The fixture proved the prepaid price and the regular monthly option. | The unlisted pilot paid successfully through a cart built with the exact selling plan. Verify the normal product widget selects that plan and maps the approved catalogue variants before exposing the public offer. |
| Payment and lifecycle events | Deployed offer handlers consume signed order, payment, refund and subscription events and retry receipts. | Production v0.3.1 wires the private website pilot payment observer alongside the regular purchase feed. Prove it accepts a fresh checkout without a signed replay. Wire ordinary catalogue enrollment and current refund/cancellation checks with durable retries and exact plan mapping. |
| Prepaid coverage | The original schedule was retained. The staging worker held for a delayed delivery and created one continuation on retry. | The original pilot dates are retained and the read-only production worker held for outstanding deliveries. Wire the production worker with the coverage gate and live price check for automatic processing. |
| Renewal | Staging recorded a paid renewal and exercised duplicate-safe receipt handling. | The private operator was verified read-only in production. Live creation at the verified cutoff, observation of the £0.90 renewal and confirmation of the one-charge cap remain open. |
| Alerts | Firebase publishes operational events and runs a scheduled health monitor. | The private observer delivered the pilot payment milestone. Automatic production failure, recovery and heartbeat feeds remain open. |

A fresh paid pilot is a separate order. Keep both Firebase enrollments and
verify each order's schedule and continuation independently. If a refund
is requested, reconcile Shopify's refund and the Recharge contract before
further processing of that order.
[Recharge refund guide](https://support.getrecharge.com/hc/en-us/articles/1500000489002-Issuing-a-refund-through-the-Shopify-Checkout-Integration)

#### Verify catalogue mappings and the shared checkout

- [x] Map all 55 eligible variants to their exact Recharge prepaid plans
  and the five product-wide monthly plans. The disabled production
  catalogue retains the variant mappings.
- [ ] Verify each Recharge prepaid plan has the correct Shopify
  selling-plan link and 25% price when the approved plans are connected.
  Audit all variants against the catalogue, including their fulfilment
  keys, UK stock and shipping configuration. Keep incomplete variants
  outside the rollout.
- [ ] Prove the shared widget and checkout flow once. Show the offer
  before sign-in, add one three-delivery bundle on the correct plan,
  verify 25% off without code entry, and confirm the regular 10% monthly
  option still selects its existing plan. Use an unpaid preview only
  where a product has different widget, pricing or shipping behaviour.
- [ ] Use automated checks across the approved catalogue to verify
  plan matching, captured-price validation and the 10% continuation
  quote from the current UK price. Use one paid pilot to verify the
  full Shopify → Recharge → Firebase → PostHog path. The £1 price and
  one-renewal cap apply only to the pilot.
- [ ] Verify the shared worker checks current refund and cancellation
  state before continuation, ignores unrelated purchases and creates
  one enrollment and continuation per paid purchase even on retries.

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
