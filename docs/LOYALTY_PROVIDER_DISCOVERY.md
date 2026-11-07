# Loyalty provider enquiries and proof checklist

Prepared 7 November 2026 for [FLY-118](https://linear.app/flyinghorsecoffee/issue/FLY-118).
Companion to the [Rewards plan](LOYALTY_INTEGRATION_PLAN.md).
Status: custom Firebase selected on 7 November 2026. No enquiry sent or sandbox
created. The embedding enquiry drafts and provider proof below are historical
research, not prerequisites to custom implementation.
Do not append private vendor correspondence, account details or customer exports
to this public file. Record a sanitised conclusion and keep raw evidence private.

## Current Magic Stamp migration enquiry draft

Subject: Export and transfer of Flying Horse Coffee loyalty balances

We are building a Flying Horse customer app and would like to understand how we
can preserve customers' existing Magic Stamp progress and outstanding rewards.
Can you provide a supported export containing stable member/card identifiers,
current stamps, unredeemed rewards, terms and expiry dates? How can we securely
verify ownership when linking a customer to their exported card?

Please explain whether cards can be marked transferred or retired individually,
and how to reconcile changes between an export and cutover. Please also confirm
export/termination terms and support arrangements. This is an information request,
not authorisation to change our live programme or cancel our account.

## Historical Magic Stamp embedding enquiry draft

Subject: Embedding our existing Flying Horse Coffee loyalty programme

We already use Magic Stamp at Flying Horse Coffee and are developing our own
customer iPhone app, plus a mobile website. We would prefer to retain Magic Stamp
while displaying customers' stamp cards and rewards inside those experiences.

Do you offer a supported SDK, backend API or partner integration for this? We need
customers to link their existing card securely, see progress and outstanding
rewards, collect stamps and redeem rewards without opening a separate customer app.

Please confirm:

1. Supported integration and native-display licensing, documentation, sandbox
   access and any implementation/recurring fees beyond our existing subscription.
2. Account-linking and ownership verification, including changed email addresses,
   without collecting a customer's Magic Stamp password.
3. Card/balance/reward/activity reads, expiry data, update events and reconciliation.
4. Whether our existing stamping devices work with an embedded experience, or
   whether authenticated staff can scan customer QR codes through our staff website.
5. Configurable free-item and fixed money-off rewards, card-rule versioning,
   campaign limits, and an additional one-time migration/welcome reward.
6. Atomic redemption, duplicate-request protection and lookup after an uncertain
   timeout, including concurrent redemptions at different locations.
7. Export of stable card identifiers, current balances and unredeemed rewards;
   whether individual cards can be marked transferred or retired if we migrate.
8. Staff permissions/audit, service limits, support, data-processing and exit terms.

Could you provide access to a supported test environment for an end-to-end proof?
We are evaluating options, not requesting changes to our live programme.

## Historical Loopy Loyalty enquiry draft

Subject: Native Flying Horse app integration and stamp-programme migration

We are assessing your API for a café programme. We need a native stamp card and
rewards inside our existing iPhone app, plus a mobile web card and staff scanner.
Apple/Google Wallet can be an optional addition; it cannot be the only customer UI.

Please confirm native-display rights and the plan/API access required for card
reads, stamping, redemption, activity and export. Can customers use our own account
and interface without installing or adding a separate wallet card?

We also need free-item and fixed money-off rewards, immutable issued reward terms,
versioned card promises, and an extra once-only bonus after an approved transfer
from another provider. Please explain which capabilities are native and which
must be implemented by us.

For reliability, please provide documented idempotency, transaction lookup,
concurrent redemption guarantees, event authentication and reconciliation options.
How should we recover if an award/redemption succeeds but the response is lost?

Please describe initial balance/reward imports, stable external member identifiers,
data export/exit terms, staff/location limits and API rate limits. We would like a
sandbox proof and a quote covering the required API and staff capabilities before
committing to any paid service.

## Operational inputs to obtain privately

| Input | Owner/source | Current status |
| --- | --- | --- |
| Existing plan, bill and contract terms | James / Magic Stamp account owner | Not verified |
| Café count, staff count, devices and till provider | Café operations | Not verified |
| Stamp target, eligible items, multiple-item rules and expiry | Current programme settings | Not verified; preserve current promises |
| Members, balances, outstanding rewards, source identifiers | Authorised Magic Stamp export/inspection | Not obtained |
| Migration window, goodwill limit and exception owner | James / operations | Required before cutover |
| First eligible app version and pilot identities | Release owner | Not chosen; keep flag off |
| Shopify web sign-in/session setup and production support owner | Engineering / operations | Required before deployment; Shopify is the sole identity provider |

## Historical provider proof: use synthetic members only

| Test | Pass evidence |
| --- | --- |
| Embedded experience | Native and mobile-web screens read a supported card without opening a vendor app |
| Ownership/linking | Verified account links to the intended member; a claimed email/customer ID alone cannot hijack a card |
| Award and complete | Confirmed stamp changes progress and creates exactly the promised completion reward |
| Dynamic benefit | A free pastry and a fixed GBP discount can each be displayed and redeemed with their terms |
| Existing promises | Editing/retiring an offer does not alter an issued reward or active card promise |
| Retry after lost response | Same operation is recovered, not re-applied; changed payload with same key is rejected |
| Concurrent redemption | Only one of two simultaneous attempts succeeds |
| Challenge lifecycle | Expiry/abandonment loses no reward; collect/redeem purposes cannot be substituted |
| Events/reconciliation | Duplicate/out-of-order delivery and missed events converge to authoritative state |
| Migration | Opening progress and outstanding rewards import without extra completion/bonus rewards; rerun is safe |
| Exit | Required data exports in a usable format before cancellation |
| Flags and pause | Ineligible versions stay hidden; backend pause rejects new work but returns previous confirmed results |

Record each result as **not tested**, **passed** or **failed**, with date, API/SDK
version, safe evidence reference and limitation. All rows are currently not tested.
No provider has passed these gates. Use private synthetic evidence for payloads;
publish only sanitised conclusions here.

## Historical decision record template

Record the chosen provider, supported integration surface, plan/quote, failed or
pending gates, mitigations, exit strategy and owner sign-off. Reconcile the actual
cost against the current Magic Stamp bill. If moving to the next candidate, record
the concrete reason; unanswered questions are not a confirmed technical rejection.
If custom is selected, record why viable external options fail the required fit
and who owns ongoing support before implementation begins.
