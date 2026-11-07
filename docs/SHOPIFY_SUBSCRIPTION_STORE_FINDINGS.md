# Production subscription findings

> Historical document migrated from iOS commit `e28f9d98e320993edc76aa0291ce916ec7f24291` on 7 November 2026.
> Findings retain their original dates and do not establish current readiness.
> The selected integration is native app → authenticated backend → Recharge Admin REST API;
> Storefront API entitlement is not a prerequisite for that route.

Verified: **14 October 2026**. This is a dated snapshot, not a guarantee of future
configuration. Read alongside the [audit](SUBSCRIPTION_INTEGRATION_AUDIT.md) and
[implementation plan](SUBSCRIPTION_IMPLEMENTATION.md).

## Goal and scope

James wants **Shopify for everything where possible**, including subscription
management. Required native features remain coffee/quantity, frequency, shipping
address and next-date editing, plus pause/resume/cancel/skip. Establish which
features Shopify safely supports before committing to a Recharge implementation.

James authorised switching the Shopify connector from development to production
and performing read-only checks. The connector now targets production; verify
store identity at the start of future work. Switching this connection did not
change the app's environment configuration. No store settings, products, contracts,
payments or orders were changed. The connector returned no customer contract data.
A subsequent authorised browser inspection reached both production admin apps;
its findings below supersede the earlier uncertainty about whether Shopify
Subscriptions is in use.

## Initial connector evidence

| Check | Result | Limit |
| --- | --- | --- |
| Store identity | Flying Horse Coffee; `flyinghorsecoffee.myshopify.com`; public domain `flyinghorsecoffee.com` | Development is `development-flying-horse-coffee.myshopify.com` |
| Installed apps | 17 apps returned; pagination complete | Installation alone does not establish subscription ownership |
| Recharge | Recharge Subscriptions, app ID `294517`, developer Recharge | Installed and owns the returned selling-plan groups |
| Shopify Subscriptions | App title `Subscriptions`, ID `66228322305`, developer Shopify; `shopifyDeveloped: true` | Installed; no selling-plan groups attributed to it in this query |
| Selling-plan ownership | All **26** returned groups have `appId: "294517"`; pagination complete | Plan configuration is not proof of customer contract ownership |
| Gift plans | Group names include 6-month and 12-month gift subscriptions | Active customer usage, prepaid mechanics and remaining fulfilments are unverified |
| Customer contract ownership | `subscriptionContracts` returned `Access denied for subscriptionContracts field.` | This query alone could not establish ownership; later browser evidence follows below |

## Direct production admin inspection (later on 14 October)

Read-only browser inspection used James's existing authenticated admin session.
No imports, exports, setup steps, settings saves or subscription actions were run.

| Admin view | Observed result | Interpretation |
| --- | --- | --- |
| Shopify Subscriptions home | Setup guide **0/7 completed**; customer subscription management marked **Needs setup** | Installed but setup incomplete; no setup buttons were pressed |
| Shopify Subscriptions → Contracts | Empty state explaining contracts will appear after purchase; Export disabled; Import available | No contracts displayed in the app's unfiltered contract view |
| Recharge home | Correct store `flyinghorsecoffee.myshopify.com`; **51 active subscribers**, displayed reporting period **14 September–13 October 2026** | Recharge still has an active subscriber population; this is a dashboard metric, not a current per-contract census |
| Recharge → Subscriptions | Eventually loaded: **1–25 of 354** records, including Active and Cancelled; active rows have future charge dates | Direct evidence of existing subscriptions managed in Recharge; 354 is all statuses, not active subscribers |
| Recharge → Customers | Alternative page also did not yield a usable customer list | No customer details persisted in this report or exported |

**High confidence:** Recharge remains the operating subscription manager, while
Shopify Subscriptions shows no contracts. Combined with all 26 selling-plan groups
being Recharge-owned, this supports checkout migration without provider transfer.
The subscription list directly confirms active records and future charge schedules.
This is stronger evidence than the earlier connector-only check. It is still not
a complete reconciliation of every contract, and the dashboard's 51 subscribers
must not be described as 51 contracts or an exact live inventory.

Admin routes for future read-only verification:

- [Shopify contracts](https://admin.shopify.com/store/flyinghorsecoffee/apps/subscriptions-remix/app/contracts)
- [Recharge dashboard](https://flyinghorsecoffee-sp.admin.rechargeapps.com/merchant/home)
- [Recharge subscription inventory](https://flyinghorsecoffee-sp.admin.rechargeapps.com/merchant/subscriptions)

## Can everything move to Shopify?

**Potentially for ordinary recurring subscriptions; not yet demonstrated for the
whole business or the requested native app.** Treat these as separate decisions:

1. **Contract migration:** Shopify documents CSV imports from eligible Shopify
   Checkout-integrated providers. Required fields include customer/payment-method
   IDs, prices, cadence and a next billing date at least 24 hours after import.
   Source data must be transformed to Shopify's format. The email is encouraging
   eligibility evidence, not proof every payment method/contract can transfer.
   [Official import guide](https://help.shopify.com/en/manual/products/purchase-options/subscriptions/shopify-subscriptions/import-export/importing-and-exporting)
2. **Customer editing:** Shopify's documented customer experience covers
   payment/address updates, pause/resume/cancel and skipping. Its current Customer
   Account mutation list (2026-10) does not expose product/quantity/frequency or
   next-billing-date edits. Merchant editing supports products, quantity and
   frequency, but that does not grant equivalent access to our iOS client.
   Admin writes use app-owned contract scopes; adding a backend is not proof it
   can edit contracts owned by Shopify Subscriptions. Verify a supported route
   or explicitly accept reduced self-service before choosing that app.
   [Customer experience](https://help.shopify.com/en/manual/products/purchase-options/subscriptions/shopify-subscriptions/customer-experience),
   [Customer API](https://shopify.dev/docs/api/customer/latest/objects/mutation),
   [merchant editing](https://help.shopify.com/en/manual/products/purchase-options/subscriptions/shopify-subscriptions/manage-subscriptions/manage-contracts),
   [Admin scope requirement](https://shopify.dev/docs/api/admin-graphql/latest/mutations/subscriptionContractUpdate)
3. **Gifts and special cases:** configured 6/12-month gift labels require checks
   for prepaid balances, remaining deliveries and automatic expiry. Do not infer
   compatibility from Shopify's general ability to fulfil prepaid orders. Bundles
   are explicitly incompatible with Shopify Subscriptions; actual bundle usage
   here remains unknown. Verify every used gateway and recurring payment method.
   [Shopify Subscriptions considerations](https://help.shopify.com/en/manual/products/purchase-options/subscriptions/shopify-subscriptions/considerations)
4. **Safe changeover:** reconcile every active/paused subscription, existing
   prepaid fulfilment, discount and next charge; confirm how old billing stops
   and new billing begins without duplicates. Rebuild relevant selling plans,
   product widgets, customer management links, notifications and recovery rules.
   Prove representative cases in development before seeking migration approval.

The standard Shopify app is a plausible simplification if its capability limits
are acceptable. Building a custom Shopify subscription app is a separate, larger
option with its own contract ownership, billing and operational responsibilities;
it is not equivalent to installing Shopify Subscriptions. No migration is approved
or started by this read-only investigation.

## Follow-up migration constraints (14 October)

Recharge's [official Migr8 guide](https://support.getrecharge.com/hc/en-us/articles/21540011979031-Automating-customer-and-subscription-data-migration-to-Recharge-with-Migr8#shopify-1p)
provides a **Recharge to Shopify / Shopify 1P** export route enabled on request.
Its compatibility table supports standard 1:1 subscriptions and excludes prepaid
subscriptions and bundles. It describes Shopify Payments, including methods moved
into a Shopify legacy gateway, as eligible; standalone Stripe, Braintree and
Authorize.net appear unsupported for this tool. This is a tool-specific constraint,
not a claim that every alternative migration route is impossible.

A read-only active subscription detail showed **Shop Pay**, a future charge date
and a successful Shopify Checkout order. No customer names, addresses, card digits
or payment tokens are retained here. One observed payment method does not establish
the gateway distribution or migration eligibility of the remaining subscriptions.

### Evidence still required before a Shopify-only recommendation

- [ ] Complete all 354 subscription list records; reconcile status counts and
  distinguish subscribers from individual subscriptions. Do not import cancelled
  history as active billing contracts.
- [ ] For active/paused records, reconcile the payment method's actual Shopify
  payment-method ID and vault/gateway. Separate missing, expired and unsupported
  methods; a card brand or Shop Pay label alone is insufficient.
- [ ] Inspect every gift plan's billing versus delivery cadence, expiry and
  outstanding prepaid fulfilments. Gift wording alone does not establish prepaid
  behaviour. Any actual prepaid population is outside Migr8's documented Shopify
  1P support and needs a supported alternative or completion before retirement.
- [ ] Check actual bundles, discounts, queued charges and skipped prepaid orders.
- [ ] Ask Recharge to confirm eligibility and enable the Shopify formatter only
  after James authorises contacting them and any required installation/access.
  No support message, installation, token creation or export has been performed.
- [ ] Reconcile the export against Shopify's import requirements, then test in
  development. Do not use production imports or charges as validation.

Recharge's [migration-away guidance](https://support.getrecharge.com/hc/en-us/articles/12394488475671-Migrating-away-from-Recharge)
identifies subscription, queued-charge, payment-method and prepaid-order datasets
needed for reconciliation. It warns that billing continues until the old service
is stopped. Plan a coordinated cutover; do not follow its live disable/cancel steps
during this read-only investigation.

Browser limitation during this follow-up: Arc automation follows the active
window and repeatedly switched away from Recharge. The 250-row view also stayed
on loading placeholders during repeated reads. No additional
list records or gift configuration could be verified. The first 25 records and
one active detail remain the verified coverage, not a complete census. Continue
from a stable authenticated session; do not mistake a loading table for an empty
result or mark the outstanding checks complete.

## Confidence and decision

| Conclusion | Confidence | Reason |
| --- | --- | --- |
| Recharge still manages existing subscriptions | High | Active records, future charges, Recharge-owned plan groups and empty Shopify app contract view |
| Standard recurring subscriptions have a potential Shopify migration route | Moderate | Both providers document a route, but this store's complete payment and contract eligibility is unverified |
| Every gift/prepaid obligation can move using Migr8 | Not established | Prepaid is excluded by its Shopify 1P compatibility table; gift mechanics and active usage remain unknown |
| Shopify Subscriptions meets all requested native edits | Not established | Customer API gaps remain; merchant UI editing does not prove client access |

Keep Recharge operating while these gates are resolved. Do not promise a
Shopify-only cutover date until the complete inventory, capability matrix and
non-production migration/renewal evidence are available.

## What the email means

The supplied Recharge email confirms migration to **Shopify Checkout**. This is
not confirmation of migration to the separate **Shopify Subscriptions** app.
Recharge's [integration documentation](https://support.getrecharge.com/hc/en-us/articles/4403505928599-Understanding-Shopify-Checkout-Integration-technical-details-and-requirements)
distinguishes checkout/payment processing from subscription management. Its
[Avalara migration guidance](https://support.getrecharge.com/hc/en-us/articles/360038132113-Avalara)
independently states the **14 October 2026** deadline and explains that Recharge
continues managing subscriptions after migration while Shopify handles tax and
order processing. This source does not establish that this store uses Avalara.

The email therefore does not justify uninstalling Recharge, redirecting native
writes to Shopify or assuming subscriptions were transferred. Shopify-first is
the desired destination; any provider transfer is a separate scoped decision.

## Repeating the read-only checks

Use the connected Shopify Admin GraphQL tool after verifying its schema and the
selected store. These are the relevant query selections, not app credentials:

```graphql
query StoreIdentity {
  shop { name myshopifyDomain }
}
query SellingPlanOwners {
  sellingPlanGroups(first: 50, query: "app_id:ALL") {
    nodes { id name appId }
    pageInfo { hasNextPage endCursor }
  }
}
query InstalledApps {
  appInstallations(first: 50) {
    nodes { app { id title developerName shopifyDeveloped } }
    pageInfo { hasNextPage endCursor }
  }
}
query SubscriptionOwners {
  subscriptionContracts(first: 50) {
    nodes { status app { id title } }
    pageInfo { hasNextPage endCursor }
  }
}
```

Use `app_id:ALL`: the default selling-plan-group query is scoped to the calling
app. Follow every returned cursor if `hasNextPage` becomes true. The contract
query above was denied; it is recorded as a blocked check, not a successful one.
The connector did not expose a pinned API version; the app's `2026-07` version
must not be attributed to these admin checks. Store only redacted findings;
never commit access tokens, customer records or payment details.

## Next checks and decision order

- [x] Identify production and inspect installed subscription apps.
- [x] Inspect all returned selling-plan groups and map their app IDs.
- [x] Inspect both production apps: Shopify Subscriptions has an empty contract
  view; Recharge reports an active subscriber population.
- [ ] Reconcile the full contract inventory and payment-method eligibility; the
  first 25 of 354 Recharge records were inspected; connector access remains denied.
- [ ] Verify Recharge's migration/integration state, remaining payment paths,
  entitlement and permitted customer edits. These settings were not inspected.
- [ ] Resolve the native editing gaps before choosing Shopify Subscriptions.
  Actual usage was inspected (empty contract view); full feature fit is unproven.
  Merchant UI features alone do not prove this iOS client can perform them.
- [ ] Determine whether the configured gift plans have active customers and
  establish prepaid, expiry, remaining-delivery and cancellation semantics.
- [ ] Choose the smallest supported architecture: Shopify for catalogue,
  customer sign-in and checkout; subscription reads/writes through the verified
  owner. Use Recharge only where ownership and required capabilities demand it.
- [ ] If Shopify-only requires migration, separately assess payment-method
  eligibility, gifts, discounts, renewals, reconciliation, duplicate-billing
  prevention and rollback. James must approve migration and paid changes.
- [ ] Configure representative non-production plans and test customers, then
  follow containment → identity/access → reads → purchase/renewal → edits →
  release evidence in the implementation plan. No live billing experiments.

The full Shopify, Recharge and web-view checklist remains in the audit. The
13 October public Storefront counts measure products/variants, not these admin
plan-group counts; do not compare them as if they were the same measurement.

## Existing work and handoff

Continue the existing [Linear project](https://linear.app/flyinghorsecoffee/project/native-subscription-integration-3c627bfa1e7c)
and [draft audit PR #37](https://github.com/jamesdyorke/flying-horse-coffee-ios/pull/37).
Reuse FLY-21 for native editing and FLY-22 for the architecture decision; do not
create a competing integration project. This document records store evidence,
not completion of implementation issues. Only James may merge or enable auto-merge.
