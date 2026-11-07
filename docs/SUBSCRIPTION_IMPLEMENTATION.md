# Native subscriptions: implementation specification

> Historical document migrated from iOS commit `e28f9d98e320993edc76aa0291ce916ec7f24291` on 7 November 2026.
> Findings retain their original dates and do not establish current readiness.
> The selected integration is native app → authenticated backend → Recharge Admin REST API;
> Storefront API entitlement is not a prerequisite for that route.

Companion to the [subscription audit](SUBSCRIPTION_INTEGRATION_AUDIT.md), updated
after the 14 October 2026 second review. See its owner checklist for Shopify,
Recharge and website configuration checks and its confidence assessment.
This is a gated specification, not a claim that credentials, business rules or
live API behaviour have been verified. The store evidence table in the audit
must be completed before committing to a transport or production rollout.
The [14 October production findings](SHOPIFY_SUBSCRIPTION_STORE_FINDINGS.md) confirm
Recharge owns the returned plan groups. Later direct admin inspection showed an
empty Shopify Subscriptions contract view and an active Recharge subscriber base;
full per-contract reconciliation remains outstanding. Shopify-first
is the goal; Recharge-specific work below is conditional on verified ownership
and the required capabilities. Installed Shopify Subscriptions alone proves neither.

## Revised delivery order

Each phase has an evidence gate. Documentation completion does not complete any
implementation or live-verification phase below.

| Phase | Deliverable | Exit evidence |
| --- | --- | --- |
| 0. Contain existing writes | Disable unverified Shopify subscription writes at both service and UI boundaries; provide a verified recovery route or clear unavailable state | Missing/disabled provider configuration cannot trigger old mutations; checkout remains independently available |
| 1. Prove identity and access | Establish contract owner and Shopify-first capability fit; complete entitlement/test-environment checks; fix session races and web authentication; prove the selected customer authentication route | Logout/account-switch/expired-token tests pass; customer A cannot access B; actual client type and renewal strategy recorded |
| 2. Verify authoritative reads | Provider-tagged identities, complete pagination, capabilities, schedules and empty/unsupported states | Designated test subscriptions match their verified owner; no false empty state from a permission or transport error |
| 3. Prove purchase and renewal | Variant/plan selection, immutable cart intent, explicit guest policy and checkout recovery | One complete representative purchase and supported simulated renewal match the expected customer, price and schedule; every offered plan is tested before release |
| 4. Add native edits incrementally | Implement only verified permitted operations, with consequences, concurrency checks and refreshed state | Each enabled edit passes its acceptance cases and actual billing/schedule checks, including shared charges |
| 5. Prove recovery and release readiness | Failed payments, interrupted operations, telemetry, accessibility and independent write-disable switch | Full acceptance matrix and supported business cases pass; operations/support ownership is recorded |

Provider discovery can proceed alongside phase 0, but native writes must not be
enabled before their gates pass. James alone merges PRs and approves production
enablement, paid changes or provider migration.

## Architecture and authentication

Preserve Shopify Customer Account sign-in, product discovery and Checkout Sheet
Kit. Use Recharge for authoritative subscription state and writes when it owns
the subscription. Never infer ownership from an ID's numeric suffix or from a
missing token, and never silently substitute Shopify mutations.

If Recharge is required, candidate transport contingent on verified Storefront entitlement:

1. Obtain the current customer's valid Shopify Customer Account access token
   through `customerTokenProvider`.
2. Exchange it using Recharge's documented `loginWithShopifyCustomerAccount`
   behaviour and a publishable Storefront token for the selected store. Verify
   the native request shape against current official SDK behaviour; the old
   branch's URL/header/body is reference material, not an approved wire contract.
3. Require a customer-bound session before reading personal data or writing.
   A non-customer session must produce an empty/new-customer experience, never
   broader access. Retain the session in memory, coalesce concurrent refreshes,
   and clear it on logout, account switch or environment change.
4. Reauthenticate expired sessions. Do not automatically replay a write with an
   ambiguous outcome: refetch first, then offer retry if still required. Do not
   log credentials, portal tokens, addresses or raw authentication responses.

Harden the existing Shopify session before relying on it for Recharge:

- Associate requests and refreshes with a session generation. Clear local state
  promptly on logout, cancel outstanding work, and reject late results from an
  old generation. Cancellation alone is insufficient: a late successful refresh
  must not restore the old account, and a late failure must not clear a new one.
- Apply the generation check to sign-in completion, customer screen loads,
  Recharge sessions, checkout preparation and mutation responses. Clear personal
  screen state and cached purchase URLs when the account changes.
- Verify the registered client type. Shop-configured Headless/Hydrogen clients
  receive refresh tokens; apps using their own `customer_authentication` client
  do not. Preserve the existing client or explicitly implement and test the
  matching renewal flow; the current decoder requires a refresh token.
- Validate exact callback scheme, host, port and path against the registered URI
  and retain OAuth state validation. Do not use string-prefix matching. Provide
  visible retry/cancel states for navigation failures and cancellation handling
  that completes each authentication attempt once.
- Evaluate `ASWebAuthenticationSession` for sign-in, separately from Checkout
  Sheet Kit. Coordinate provider-supported callbacks and any required domain/app
  association setup; verify browser logout and account selection. A native token
  does not prove a portal or checkout web view shares an authenticated session.

References: [Shopify client authentication](https://shopify.dev/docs/api/customer/latest)
and [Apple web authentication](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession).

If Storefront entitlement is absent, stop the direct-client route. Assess an
explicit Firebase middleware implementation only after Admin access is confirmed.
That backend must resolve customer identity through Shopify using a verified
customer access token, map the returned Shopify customer ID to Recharge, and
check subscription/address/charge ownership on every operation. A supplied email
or customer ID is not authentication. Admin secrets stay in server-side secret
storage. Rate limits, operation deduplication and portal-policy enforcement then
become backend responsibilities. Do not deploy a generic Recharge proxy.

## Shopify-only migration decision gate

Before selecting Shopify Subscriptions, complete the
[production reconciliation checklist](SHOPIFY_SUBSCRIPTION_STORE_FINDINGS.md).
Recharge's documented Shopify 1P formatter excludes prepaid subscriptions and
bundles. Classify gift plans by actual billing/delivery semantics, and map every
active payment method to a valid Shopify payment-method ID and supported gateway.
Retain cancelled history separately from the active migration population. Resolve
native customer editing gaps before approving the provider choice. Do not install
migration tooling, import contracts, disable billing or contact providers as part
of read-only discovery.

## Module and interface changes for the feature PR

- Only if the provider decision requires Recharge, add `Modules/FHRecharge` with
  provider DTOs, configuration, transport and a session dependency; register it in `Modules/Package.swift`, `Project.swift` and
  the module test plan. Reuse the current networking/dependency conventions rather
  than importing the old branch's parallel HTTP stack.
- Keep domain models in `FHDomain` and mapping in `FHNetworking`. Extend the
  subscription model with a provider-tagged identity, structured line items,
  permitted operations, billing/delivery intervals, address summary and schedule.
  Keep Shopify contract IDs, Recharge subscription IDs, charge IDs, address IDs
  and selling-plan IDs distinct. Do not parse labels to perform edits.
- Extend the subscription service with typed product/quantity/frequency/address/
  date edits and a refreshable schedule. Existing pause/resume/cancel/skip methods
  must delegate to the configured, verified owner. Return the refreshed provider
  state after writes; represent unavailable capabilities and partial failures.
- Fetch all pages needed for subscriptions, selectable plans and upcoming orders.
  Do not retain the current assumption that six cycles always includes the next
  unskipped order. Preserve provider dates and distinguish charge date, dispatch
  date and estimated arrival in UI copy.

## Subscription purchasing

Use live Storefront product-detail purchase options for the selected variant,
including selling-plan allocations, price adjustments and `requiresSellingPlan`.
Do not derive a recurring price from the ordinary product price. Keep this detail
data outside the existing product-list SQLite record for the first implementation;
refresh it before checkout instead of persisting stale eligibility.

Extend `CheckoutRequest` and `RemoteCheckoutService.checkoutURL` with the selected
variant and optional `sellingPlanID`; pass it through to Shopify's cart line.
An absent ID means one-time purchase only when the product permits it. Require an
explicit plan for subscription-only products; a loading/error state must never
silently permit a one-time purchase. Show frequency, quantity and recurring price
before purchase. Leave checkout taxes/shipping to Shopify.

Key checkout preloading by variant, quantity, selling plan and customer session.
Invalidate it when any selection changes; discard obsolete asynchronous results.
Capture that immutable selection before any await, and associate the result with
the captured values, not the current UI values after the request completes. Keep
authenticated checkout URL creation close to presentation; preserve the current
avoidance of long-lived authenticated preloads because those URLs expire.

For a signed-in subscription buyer, token failure must offer reauthentication or
an explicit guest choice instead of silently continuing anonymously. Preserve
one-time and anonymous purchases where supported, and test how guest purchases
are subsequently associated with a customer. Never infer ownership from email
alone in the native management API.

Preserve typed checkout errors and recoverability through the module boundary.
Distinguish a failure before payment from an uncertain payment outcome. Reconcile
the existing attempt before encouraging another purchase; safely retain enough
non-secret attempt context to recover after termination or external redirects.
When rebuilding a cart is appropriate, preserve the same selected purchase
option and revalidate its eligibility. After checkout, refresh subscriptions
with bounded retries for provider propagation, then show a pending/retry state
rather than creating a second subscription.

Reference: [Shopify selling plans and allocations](https://shopify.dev/docs/storefronts/headless/building-with-the-storefront-api/products-collections/subscriptions).
See also [authenticated checkout URL lifetime](https://shopify.dev/docs/storefronts/mobile/checkout-kit/authenticate-checkouts?extension=swift).

## Native editing behaviour

Follow existing UIKit routing and shared SwiftUI components. Add native forms to
the existing subscription detail flow; a hosted management portal does not count
as completion. Payment-card updates may use the provider's secure hosted flow.

| Operation | Required behaviour |
| --- | --- |
| Product / quantity | Present only permitted variants; validate positive quantity and allowed swap rules. Obtain provider pricing before confirmation; never accept a client-chosen recurring price |
| Frequency | Offer allowed plans/intervals, show schedule impact, apply aligned billing/delivery fields, then refetch dates and skips |
| Address | Identify whether the address is shared; change only the selected subscription unless the customer explicitly chooses a shared update. Verify ownership, shipping eligibility and resulting costs |
| Next date | Show the precise billing or dispatch date being changed; reject unsupported/past dates. Refetch charges because regrouping may replace charge IDs |
| Skip | Distinguish existing queued charge from a future schedule entry; select the intended subscription(s), not every item in a shared charge |
| Pause / resume | Prove the selected provider's pause duration, resume date and status semantics on the test store. Do not disguise cancellation as pause or promise an indefinite pause through date deferral |
| Cancel | Confirm consequences, respect configured reasons/commitments, then verify provider status and future schedule |

Disable duplicate submissions while a write is pending. Honour provider/portal
permissions and surface a useful error for a removed plan, unavailable swap or
changed schedule. On a multi-step failure, refresh and explain what succeeded;
do not report a complete save or retry every step blindly.

Refresh before presenting final edit confirmation. Compare the relevant provider
state with the version used to build the form; if another device or the website
changed it, show the updated consequences and require reconfirmation. Use provider
conditional-update/version support where available; otherwise document the
remaining race and reconcile after submission rather than claiming atomicity.
Enforce duplicate-submission protection in the service/view model as well as by
disabling buttons. Replace current unconditional pause/cancel promises with copy
matching verified provider semantics and any already-processing order.

Changing frequency may remove skips and manually adjusted charges. An edit must
show that impact before submission and verify the final schedule afterwards.
Do not silently recreate old skips without checking the customer's intended dates.
Prepaid fulfilments, gifts and bundles require their own validated flows where
present. Gift-labelled plans are already configured, but active usage and prepaid
semantics remain unverified; they cannot be treated as simple recurring coffee.
Reference: [Recharge subscription methods](https://storefront.rechargepayments.com/client/docs/methods/api/subscription/).

## Acceptance and delivery

No live mutations were run during this audit. Use designated non-production
customers and provider-supported test payments for the following feature checks.
The live public Storefront audit found no selling plans in development; configure
representative test plans and establish the selected provider's test environment before
running purchase scenarios. Public product queries passed in both environments.

| Test | Acceptance evidence |
| --- | --- |
| Authentication | Existing/new customer, expired token, missing scope and revoked session behave correctly; switching accounts clears prior state |
| Session races | Logout during refresh, account B login while A refreshes, and late success/failure cannot restore A, clear B or publish A's screen data; test late sign-in completion too |
| Web authentication | Exact callback validation rejects lookalike paths; offline/TLS failure offers recovery; cancel, logout and account selection finish correctly |
| Isolation | Customer A cannot read/edit B's subscription, address or charge; dev credentials cannot reach production data |
| Purchase | One-time and each offered plan preserve variant/quantity/price/frequency through cart, checkout and subscription created with the verified owner |
| Preload / failures | Selection changes cannot reuse the wrong cart; sold-out/deleted plan, network failure and retry preserve purchase intent |
| Preload race | Change quantity while cart creation is suspended; the completed cart remains bound to its original selection and cannot be reused for the new quantity or session |
| Checkout identity | Signed-in token failure offers reauthentication/explicit guest choice; account switching and guest purchases have verified customer association |
| Payment recovery | Termination, external payment redirects, recoverable failures and uncertain outcomes do not encourage duplicate purchases; delayed creation shows pending/retry |
| Core actions | Pause/resume/cancel/skip match provider state and the next scheduled charge, including shared-charge cases |
| Editing | Coffee, quantity, frequency, address and date changes preserve applicable discounts and intended delivery/billing policy |
| Schedule regression | Skip then change frequency; manual date then change frequency; same-address date merge; partial failure between edits |
| Concurrent edits | Website/second-device changes invalidate stale confirmations; repeat taps and retries do not apply an operation twice |
| Completeness | Pagination, empty account, all fetched cycles skipped, unknown statuses and provider propagation delay are handled |
| Billing | A provider-supported simulated renewal confirms the intended charge or absence of charge; a Shopify response alone is insufficient |
| Failed payments | Customer can understand the failure, reach secure payment update, return and see refreshed authoritative status |
| Accessibility | Large text, VoiceOver, keyboard entry, error focus and save announcements work in purchase and edit flows |
| Operational controls | Rate limits produce bounded backoff, writes are not blindly replayed, telemetry contains no raw customer payloads, and disabling native writes leaves checkout usable |

## Operational and wider app readiness

Apply telemetry redaction to existing Customer Account and checkout code, not
only new Recharge code. Remove raw response-body fragments, provider messages
and token-bearing URLs from logs/analytics; use allowlisted error codes, operation
names and safe correlation IDs. Track failures and pending outcomes separately
from confirmed success. Review native and checkout consent handling together
using [Shopify's consent integration](https://shopify.dev/docs/storefronts/mobile/checkout-kit/privacy-compliance),
based on actual data practices rather than treating one permission as universal
consent.

Record ownership for API-version upgrades, rate-limit handling, customer support,
failed-payment recovery and provider notifications. Avoid duplicate or conflicting
messages across Shopify, Recharge and the app. Ensure the write-disable control
defaults to unavailable for unverified capabilities; generic feature-flag
fail-open conventions must not reactivate the old subscription write path.

Track these separate app tasks alongside the integration: complete or remove
Search/Map placeholders; provide support and privacy preferences; define account
deletion separately from subscription cancellation; and show a clear shipment
summary including coffee, grind/size, quantity, charge date and estimated delivery.
Special-plan support remains gated by confirmed business usage and dedicated tests.

## Verification record and PR delivery

Use unit tests for mappings, session handling, cart inputs and ambiguous write
outcomes; integration tests for provider behaviour; UI tests for editing and
confirmation. Record test account aliases, API versions and results without PII.

Deliver the audit as a documentation PR. Keep it draft while store verification
remains outstanding. Subsequent feature PRs must follow the revised phased order
above, beginning with containment of unverified writes. No production enablement
until required store behaviours pass; a disabled/unconfigured integration must
not activate the old Shopify write path. Track operation failures without PII and
retain a way to disable native writes independently of checkout. James alone
merges PRs. Provider migration, paid plan changes and production billing changes
remain outside this audit.


## Shared account management backend — Settings handoff

The Settings implementation adds native personal-details, address-management and
account-deletion-request screens. All new account changes use shell backend
requests through `accountManagementService`; no live backend origin is configured.
This UI work does not implement erasure, change billing, or prove provider access.

Implement the [account management backend contract](https://github.com/jamesdyorke/flying-horse-coffee-ios/blob/main/docs/ACCOUNT_MANAGEMENT_BACKEND.md)
alongside the shared customer authentication/session foundation. It covers name
updates, paginated address reads and CRUD/default selection, and deletion-request
submission/status. Resolve identity from a verified Shopify Customer Account token;
check ownership on every operation. Keep environments isolated, secrets server-side,
field errors structured, mutation retries idempotent, and late customer-session
results invalidated. Reconcile ambiguous outcomes before replaying writes.

Deletion needs a persistent request lifecycle and operational owner. Reconcile
Shopify, Firebase/app data, analytics identity and the verified subscription
provider; define required retention before completion. Account deletion does not
imply Recharge cancellation or stopped renewal. Explicitly handle active billing,
shared charges, prepaid/gift/bundle records and any required customer action under
this plan's existing capability and billing-verification gates.

Acceptance additions: cross-customer authorization, pagination, country-specific
address validation, concurrent duplicate requests, lost-response/relaunch recovery,
logout/account-switch races, environment isolation, request status persistence, and
verification that account-address edits leave subscription deliveries unchanged.
Keep live account writes unavailable until backend integration is verified. These
requirements remain open implementation work; documentation does not complete them.
