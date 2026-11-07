# Subscription integration audit

> Historical document migrated from iOS commit `e28f9d98e320993edc76aa0291ce916ec7f24291` on 7 November 2026.
> Findings retain their original dates and do not establish current readiness.
> The selected integration is native app → authenticated backend → Recharge Admin REST API;
> Storefront API entitlement is not a prerequisite for that route.

Research date: 13 October 2026. Code baseline: `6542a6756afb06be294db0aa43db1908f4eff9c6`
(`origin/development`, fetched before creating `feature/subscription-integration-audit`).

Status: repository and public-documentation audit complete; production app and
selling-plan ownership checks completed on 14 October. Connector contract access
was denied; direct admin later showed an empty Shopify Subscriptions contract view
and 51 active subscribers in Recharge's reporting period;
entitlement, detailed settings and live integration checks remain unverified. This change is
documentation only. The native feature is not implemented or release-approved.
See the [implementation specification](SUBSCRIPTION_IMPLEMENTATION.md).

Second review: 14 October 2026, branch commit `872dff6`. This review inspected
the existing implementation, backend configuration and current public provider
documentation. Findings below are static code findings, not runtime reproductions.
That code review did not verify private admin settings or customer records. Later
read-only production checks are recorded in the
[production findings](SHOPIFY_SUBSCRIPTION_STORE_FINDINGS.md). Purchases and renewals
remain unverified; public Storefront counts below remain evidence from 13 October.

## Recommendation

Keep Shopify checkout and pursue **Shopify for everything where possible**, as
James requested. Verify ownership and native API capabilities before choosing the
management provider. Production has both Recharge and Shopify Subscriptions
installed; all 26 returned selling-plan groups belong to Recharge. Direct admin
inspection subsequently showed Shopify Subscriptions has no displayed
contracts and incomplete setup; Recharge reports 51 active subscribers for
14 September–13 October. Recharge remains the operating manager with high
confidence, although a full per-contract reconciliation is outstanding.
See the [dated evidence and next checks](SHOPIFY_SUBSCRIPTION_STORE_FINDINGS.md).

The Recharge email confirms Shopify Checkout migration, not a transfer to Shopify
Subscriptions. Recharge's [integration documentation][migration] explains the
distinction. Its [migration deadline guidance][migration-deadline] independently
confirms 14 October 2026 and continued Recharge subscription management after
migration. Do not remove Recharge or redirect writes based on the email alone.

Required native editing includes coffee, quantity, frequency, shipping address
and next date, plus pause/resume/cancel/skip. Six- and twelve-month gift plan names
are configured; active gift usage and prepaid/bundle semantics need verification.
Recharge remains a conditional integration candidate where the verified owner
and required capabilities demand it; Shopify-only may require a separate migration.

## What already exists

| Area | Repository evidence | Implication |
| --- | --- | --- |
| Checkout | `ShopifyClient.checkoutURL` builds a Storefront cart; `RemoteCheckoutService` and Checkout Sheet Kit present it | Shopify checkout already exists; no checkout-provider replacement is needed |
| Purchase options | Product query selects the first variant; `ProductModel` and `CheckoutRequest` have no selling plan | Subscription purchasing needs explicit plan discovery and cart-line `sellingPlanId` |
| Management | `RemoteSubscriptionService` sends pause/activate/cancel/skip to Customer Account API; client pins `2026-07` | Current production write path still needs reconciliation with Recharge |
| Completeness | Subscription query caps contracts at 20, lines at 10 and upcoming cycles at 6 without pagination | Do not reuse these limits as guarantees of complete customer data |
| Backend | Sibling Firebase repository contains deployment configuration, no configured services | A middleware design would be new work, not an existing endpoint |

Earlier work is real:

- `feature/manage-subscriptions` is recorded in merge `3c5a8c3` on 19 August.
- `feature/recharge-api-setup` exists locally and in fetched remote refs; its local
  tip is `4cd4ea3` dated 29 August. It adds `FHRecharge`, a portal service and a
  website fallback; it does not implement full native editing.
- Its handoff and the current `TODO.md` record a 19 August mismatch between a
  Shopify pause and Recharge's billing schedule. This is historical reported
  evidence, not a test repeated in this audit or proof of post-migration behaviour.
- Its production Storefront token is empty. Empty historical configuration does
  not establish the store's current plan or entitlement. Its source layout
  predates `Modules/`; port individual ideas rather than merging it wholesale.
- Its cookie-sharing assumption and hand-written token-exchange request require
  verification. Never reproduce its raw error-body logging in authentication code.

## Capability comparison

“Documented” below means a public API exists, not that this store permits it.

| Customer action | Recharge candidate | Shopify Subscriptions candidate |
| --- | --- | --- |
| Buy a subscription | Shopify selling plans and checkout | Same checkout mechanism |
| List/detail | Customer-scoped subscription API | Customer Account contracts |
| Pause/resume | Pause-related scheduling parameters and activation exist; semantics must be tested | Customer API pause/activate exists |
| Cancel | Cancellation API | Customer API cancel exists |
| Skip | Existing-charge or future-subscription-charge APIs | Customer API billing-cycle skip exists |
| Change coffee/quantity | Subscription update API, subject to product/portal rules | No customer mutation established by this audit |
| Change frequency | Subscription update API; schedule side effects apply | No customer mutation established by this audit |
| Change shipping address | Address and subscription-address APIs | Delivery-options/select-delivery-method API exists |
| Choose next date | Subscription charge-date API; charge date is not promised arrival date | No equivalent customer mutation established by this audit |
| Payment details | Provider-hosted secure payment update | Shopify-hosted payment management |

References: [Recharge subscription methods][rc-methods], [update request fields][rc-update],
[charge-date behaviour][rc-date], [pause parameter][rc-pause],
[Shopify Customer contracts][customer-contract], [cycle skip][customer-skip],
[delivery selection][customer-delivery], [Shopify customer experience][customer-experience].

The Shopify merchant interface can edit products, quantities and frequencies;
that does not establish access for this iOS client. The broader Admin API uses
app-owned contract permissions. Headless/Hydrogen's
`customer_write_subscription_contracts` can access contracts across providers,
but does not add missing mutations or prove the owning provider processes those
changes. See [merchant editing][merchant-edit], [Admin contract editing][admin-edit]
and [scope definitions][scopes].

Moving to Shopify Subscriptions is a separate migration: Shopify supports imports
from eligible checkout-integrated providers using a specifically formatted CSV.
This does not guarantee this store's payment methods, discounts or special plans
are transferable. A later migration would need reconciliation, duplicate-billing
prevention and a rollback plan before retiring Recharge. [Import guidance][import]

Recharge's [Shopify 1P Migr8 compatibility table](https://support.getrecharge.com/hc/en-us/articles/21540011979031-Automating-customer-and-subscription-data-migration-to-Recharge-with-Migr8#shopify-1p)
supports standard subscriptions but excludes prepaid subscriptions and bundles.
Payment eligibility depends on the actual gateway/vault, including the distinction
between a Shopify legacy gateway and a standalone processor. One active detail
showed Shop Pay; the rest of the gateway distribution and gift obligations remain
unverified. See the [follow-up evidence and confidence assessment](SHOPIFY_SUBSCRIPTION_STORE_FINDINGS.md).
Resolve these gates before committing to a provider or migration date.

## Authentication and entitlement findings

- Recharge currently documents **Plus or Custom** for Storefront API/JS SDK
  availability. This supersedes the old handoff's speculative Pro/Standard advice.
  API-token visibility also depends on staff permissions and owner acceptance of
  API terms; a missing button alone cannot diagnose a plan restriction.
  [Recharge API access][rc-access]
- `loginWithShopifyCustomerAccount` is explicitly documented for headless clients
  using Shopify Customer Account access tokens. The separate Customer Account UI
  extension backend requirement must not be applied to the iOS app by analogy.
  Storefront sessions enforce customer scope and portal permissions and expire
  after roughly one hour. [Authentication guide][rc-auth]
- If Recharge is required, candidate route: Shopify sign-in → Recharge customer session →
  customer-scoped subscription operations. Verify native HTTP compatibility and
  exact request/response contracts before porting the JS SDK behaviour to Swift.
- An Admin API key belongs only on a backend. If Storefront access is unavailable,
  first confirm Admin API entitlement and supported use for this store; do not
  assume it is an unrestricted substitute. The older [custom portal guide][portal]
  describes middleware and labels its platform as Custom, so it is not evidence
  that every example applies unchanged to this migrated Shopify store.

## Store evidence and questions for the owner

### Verified public Storefront access

On 13 October, read-only GraphQL queries against each configured store's
`/api/2026-07/graphql.json`, using the app's existing public Storefront credentials,
returned the following. Product pagination completed; none of the queried nested
variant/plan/allocation connections reported additional pages.

| Environment | Visible products | Products with selling-plan groups | Subscription-only products | Variants with selling-plan allocations |
| --- | --- | --- | --- | --- |
| Production | 84 | 19 | 2 | 227 |
| Development | 86 | 0 | 0 | 0 |

This verifies access to published product purchase options, not all products in
Shopify admin or which provider owns each plan. No carts, orders, customer data
or subscription mutations were involved. The current app cannot explicitly select
these plans. Development needs representative subscription plans before end-to-end
purchase tests; two production products require a plan rather than one-time buying.

### Remaining private configuration

Authenticated production app and selling-plan checks succeeded on 14 October;
connector customer contract access was denied. Later browser inspection reached
both apps and established the operational owner; see the [production findings](SHOPIFY_SUBSCRIPTION_STORE_FINDINGS.md)
for exact evidence and limitations. Detailed settings below remain unchecked.
Record dated, redacted evidence; never publish secrets or customer records.

| Needed evidence | Where / who | Current result |
| --- | --- | --- |
| Recharge plan, negotiated API entitlement, subscription fees and add-ons | Settings → Billing & plan; owner | Not verified |
| Storefront token availability and scope names; Admin access separately | Tools & apps → API tokens; owner permissions | Not verified; no token creation requested |
| Shopify Checkout integration state and residual legacy payment gateways | Recharge integration/payment settings; migration confirmation | Email says migration complete; admin not verified |
| Customer Account channel permissions and callback registration | Shopify Headless channel, both environments | IDs exist in code; admin not verified |
| Enabled edits, allowed frequencies, swaps, pause options and cancellation rules | Recharge Customer Portal settings | Not verified |
| Prepaid/gifts, bundles, discounts, credits, minimum commitments | Owner and product/plan settings | Gift-labelled 6/12-month plans present; active usage and semantics unknown |
| Shipping countries, cutoffs, delivery versus charge dates, inventory policy | Owner and delivery configuration | Not verified |
| Failed-payment recovery, notifications, retention offers, external integrations | Owner and operational settings | Not verified |
| Non-production Recharge store and designated test customers | Owner/developer | Not verified |

The live public Storefront result above is independent of these pending checks.

Ask the boss: which of these features must survive any provider change, what is
the actual monthly/transaction cost, and what budget is acceptable for API access?
Prepare these questions for James to send; this audit sends no external messages.

## Second-review findings and confidence

| Priority | Evidence in the current app | Required plan improvement |
| --- | --- | --- |
| P1 | `Modules/FHNetworking/Sources/Subscriptions/RemoteSubscriptionService.swift` still sends writes to Shopify despite the historical Recharge mismatch | Make containment the first deliverable: disable unverified writes in the service and UI, with a tested recovery destination. Never report billing success from Shopify alone |
| P1 | `CustomerTokenProvider.refresh` saves a late refresh or clears the session on rejection without checking whether the original session is still current | Add session generation checks, cancellation and stale-result rejection across Shopify, Recharge and customer screens; test logout and account-switch races |
| P1 | `ProductDetailViewModel.prepareCheckout` reads quantity before an await and records `preparedQuantity` afterwards | Capture one immutable purchase selection before starting; accept the result only for that selection and session |
| P1 | `LiveRemoteCheckoutService.customerAccessToken` silently falls back to guest checkout on any token error | Give signed-in subscription buyers reauthentication or an explicit guest choice; verify how the resulting subscription becomes associated with an account |
| P2 | `CustomerAuthWebViewController` uses callback string-prefix matching and only logs navigation failures | Validate exact callback components, provide retry/cancel states and test browser logout/account switching; evaluate system web authentication separately from checkout |
| P1 | `ShopifyCheckoutView` discards typed failure/recoverability information; `CheckoutViewModel` retries with a fresh cart | Preserve failure categories and reconcile uncertain payment outcomes before offering another purchase; cover termination, redirects and delayed provider creation |
| P2 | `CustomerAccountClient.execute` logs response-body fragments; checkout paths log raw provider messages | Apply allowlisted telemetry to existing code as well as new Recharge code; avoid customer data and token-bearing URLs in PostHog logs |

Paths without a directory above refer to their named types in `Modules/FHShopify`,
`Modules/FHNetworking` or `FlyingHorseCoffee/Sources/Features`. These are implementation requirements,
not fixes delivered by this documentation branch.

| Outcome | Engineering confidence | Evidence still needed |
| --- | --- | --- |
| Subscription purchasing through Shopify | High | Correct plan/variant/cart inputs and store configuration, demonstrated end to end |
| Native subscription list/detail | Medium–high | Verified owner, customer-scoped authentication and complete provider reads |
| Coffee, quantity and frequency edits | Medium | Store entitlement, permitted edits and pricing/schedule validation |
| Pause, skip and next-date changes | Medium | Actual billing outcomes, including shared charges and scheduling side effects |
| Gifts, prepaid plans and bundles | Undetermined | Gift plans configured; verify active usage and each supported model |
| Production readiness today | Low | Private configuration and complete purchase/renewal evidence are missing |

These are judgments, not measured probabilities or a promise that every existing
subscription can be edited natively. The implementation specification defines
the evidence required to raise confidence.

## James's Shopify and Recharge checklist

Record each result separately for development and production, with verification
date and redacted evidence. Leave unknown items unchecked. Do not paste tokens,
customer details or payment information into this document. Settings navigation
may vary by account; verify the setting rather than relying on a menu label.
This checklist is not authorization for agents to change paid plans, production
billing, credentials or provider ownership.

### Shopify

- [x] **Production app/plan inventory (14 October):** both Recharge and Shopify
  Subscriptions installed; all 26 returned selling-plan groups owned by Recharge.
- [x] **Operational owner (direct admin):** Shopify Subscriptions contract view
  is empty; Recharge reports 51 active subscribers for its displayed period.
- [ ] **Full migration feasibility:** reconcile every contract and payment method,
  resolve native editing gaps and verify gift/prepaid behaviour. Recharge lists
  354 records across statuses; only the first page was inspected. Shopify-only
  requires a separately approved migration.
- [ ] **Customer accounts / Headless or app client configuration:** record the
  actual client type, store, client ID, enabled customer scopes and exact sign-in
  and logout redirect settings. Confirm the development and production clients
  match their app configuration. Agree callback changes with the app developer
  before changing them in admin.
- [ ] **Token lifecycle:** confirm the current shop-configured client returns a
  refresh token. Shopify distinguishes these clients from apps using their own
  `customer_authentication` client, which do not receive refresh tokens. The app
  currently requires one; changing client type requires app changes and tests.
- [ ] **Products and sales-channel publication:** verify intended coffee variants,
  grind/size choices, availability and selling-plan allocations are visible to
  the app's Storefront client, including subscription-only products.
- [ ] **Development catalogue:** configure representative subscription plans and
  products, including one-time and subscription-only cases, and agree designated
  test customers and provider-supported test payment methods. The 13 October
  audit found no development selling plans.
- [ ] **Storefront permissions:** verify the product, selling-plan and inventory
  access the app needs; do not broaden permissions merely to hide an error.
- [ ] **Checkout, payments and delivery:** list the offered payment methods,
  shipping countries/rates, discounts and tax behaviour. Test those combinations
  with subscriptions, including address changes and renewal payments.
- [ ] **Customer association:** verify a signed-in purchase appears in the correct
  customer's Shopify orders and Recharge subscriptions. Define the recovery
  experience for guest checkout or a different email entered at checkout.
- [ ] **Customer communications and privacy:** identify active checkout/account
  notifications and pixels; agree consent handling with the native app and avoid
  duplicate messages from Shopify and Recharge.

### Recharge

- [ ] **Billing & plan:** confirm Plus/Custom Storefront API entitlement or a
  documented exception, actual recurring/transaction costs and any add-ons.
  Recharge Plus is separate from Shopify Plus. James approves any paid change.
- [ ] **API access:** confirm owner/staff permissions, API terms and the required
  customer read/write Storefront scopes. Record whether PII is obfuscated.
  Assess Admin access separately only if middleware is needed; never put an
  Admin token into the app or this checklist.
- [ ] **Shopify Checkout integration:** verify migration completion, provider
  ownership of existing subscriptions and any remaining legacy payment paths.
  Do not interpret checkout migration as migration to Shopify Subscriptions.
- [ ] **Customer Portal permissions:** decide and enable intended product swaps,
  quantity/frequency edits, address/date changes, skips, pauses and cancellation.
  Record allowed products/intervals, commitments, reasons and retention rules.
  The Storefront API honours these portal permissions.
- [ ] **Scheduling rules:** document pause duration/resume semantics, billing and
  delivery intervals, cutoffs/time zone, shared-charge behaviour, and what edits
  do to skipped or manually moved orders.
- [ ] **Migration payment reconciliation:** map each active/paused subscription
  to its Shopify payment-method ID and actual gateway; identify missing, expired
  or unsupported methods. A Shop Pay label alone is insufficient.
- [ ] **Gift obligations:** inspect billing versus delivery intervals, fixed-term
  expiry, queued orders and remaining paid deliveries for every used gift plan.
  Migr8's Shopify 1P route excludes prepaid/bundles; establish a supported
  alternative or completion strategy before retiring Recharge.
- [ ] **Special subscriptions:** confirm use or non-use of prepaid plans, gifts,
  bundles, credits and discounts; supply redacted representative cases for each
  supported flow. Decide whether unsupported cases use the portal at launch.
- [ ] **Test environment:** confirm a supported non-production Recharge setup
  linked to the intended development store and test customers. Establish how to
  simulate renewals and payment failures without charging real customers.
- [ ] **Payment recovery and notifications:** verify secure payment-update links,
  failed-payment recovery rules and ownership of renewal/failure/cancellation
  messages; check external integrations affected by subscription edits.

### Website / web-view handoff and final evidence

- [ ] Verify customer portal and secure payment-update destinations on a phone,
  including login, cancellation, expiry, logout and account switching. Do not
  assume native tokens or cookies sign in every web view.
- [ ] If adopting system web authentication, agree a provider-supported callback
  and any required app/domain association configuration before changing URLs.
- [ ] Verify return-to-app behaviour after hosted payment updates and external
  payment authentication; refresh authoritative state after returning.
- [ ] Complete the implementation acceptance matrix and record the intended
  renewal or absence of renewal after edits. An HTTP success is insufficient.

Relevant current references: [Shopify client authentication][customer-auth],
[authenticated checkout][checkout-auth], [Recharge API access][rc-access],
[Recharge authentication and portal permissions][rc-auth],
[Checkout Kit consent integration][checkout-privacy].

## Additional app scope

Launch requirements beyond basic provider wiring:

- Refresh before edit confirmation and detect concurrent portal/device changes;
  prevent silently overwriting a newer schedule or price.
- Provide failed-payment recovery and a clear next-shipment summary with coffee,
  grind/size, quantity, charge date and estimated delivery labelled separately.
- Provide support, privacy preferences and a defined account-deletion process;
  keep account deletion distinct from subscription cancellation and explain any
  required separate action.
- Test large text, VoiceOver, keyboard entry, error focus and save announcements.
- Complete or remove the Search and Map placeholder tabs before presenting the
  app as a complete release. These are separate app tasks, not Recharge blockers.
- Define safe failure metrics, rate-limit behaviour, API-version maintenance and
  a tested switch that disables native writes independently of checkout.

## Decision and release gates

0. Contain the existing unverified subscription write path. A missing or disabled
   integration must not expose the old Shopify mutations as a fallback.
1. Establish contract ownership and Shopify-first capability fit, then confirm
   API entitlement, allowed edits and test environment. No subscription
   provider migration is justified while those answers are unknown.
2. Prove customer-session authentication, account isolation and each native
   operation on designated test subscriptions. Record billing outcomes, not just
   successful HTTP responses.
3. Resolve pause semantics and special-plan behaviour before promising full native
   coverage. Keep unsupported operations visibly unavailable; never fall back to
   Shopify writes when Recharge owns billing.
4. Complete the implementation specification's acceptance matrix before production
   rollout. A portal may be an explicit recovery route, but does not satisfy the
   requested native editing requirement.

[migration]: https://support.getrecharge.com/hc/en-us/articles/4403505928599-Understanding-Shopify-Checkout-Integration-technical-details-and-requirements
[rc-methods]: https://storefront.rechargepayments.com/client/docs/methods/api/subscription/
[rc-update]: https://storefront.rechargepayments.com/client/docs/types/interfaces/UpdateSubscriptionRequest/
[rc-date]: https://storefront.rechargepayments.com/client/docs/types/functions/updateSubscriptionChargeDate/
[rc-pause]: https://storefront.rechargepayments.com/client/docs/types/interfaces/UpdateSubscriptionChargeDateParams/
[customer-contract]: https://shopify.dev/docs/api/customer/2026-07/objects/SubscriptionContract
[customer-skip]: https://shopify.dev/docs/api/customer/latest/mutations/subscriptionBillingCycleSkip
[customer-delivery]: https://shopify.dev/docs/api/customer/latest/mutations/subscriptionContractSelectDeliveryMethod
[customer-experience]: https://help.shopify.com/en/manual/products/purchase-options/subscriptions/shopify-subscriptions/customer-experience
[merchant-edit]: https://help.shopify.com/en/manual/products/purchase-options/subscriptions/shopify-subscriptions/manage-subscriptions/manage-contracts
[admin-edit]: https://shopify.dev/docs/api/admin-graphql/latest/mutations/subscriptionContractUpdate
[scopes]: https://shopify.dev/docs/api/usage/access-scopes
[import]: https://help.shopify.com/en/manual/products/purchase-options/subscriptions/shopify-subscriptions/import-export/importing-and-exporting
[rc-access]: https://support.getrecharge.com/hc/en-us/articles/360008829993-Recharge-API
[rc-auth]: https://storefront.rechargepayments.com/client/docs/methods/api/auth/
[portal]: https://docs.getrecharge.com/docs/build-a-custom-customer-portal-with-the-recharge-api
[customer-auth]: https://shopify.dev/docs/api/customer/latest
[checkout-auth]: https://shopify.dev/docs/storefronts/mobile/checkout-kit/authenticate-checkouts?extension=swift
[checkout-privacy]: https://shopify.dev/docs/storefronts/mobile/checkout-kit/privacy-compliance

[migration-deadline]: https://support.getrecharge.com/hc/en-us/articles/360038132113-Avalara


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
