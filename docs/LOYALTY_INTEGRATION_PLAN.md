# Flying Horse Rewards: integration and migration plan

Date: 7 November 2026. Status: custom Firebase selected; backend foundation in progress, deployment pending. Discovery: [FLY-118](https://linear.app/flyinghorsecoffee/issue/FLY-118).

This document records James's requested experience, public provider research and
delivery gates. It does not claim that a vendor integration, migration, feature
flag, app screen or deployment already exists. Vendor enquiries are prepared in
[the discovery checklist](LOYALTY_PROVIDER_DISCOVERY.md); none has been sent by
this documentation work. Prices are dated advertised findings, not contract quotes.

## Recommendation and agreed scope

Build a **Rewards** tab inside Flying Horse, with **Your stamp card** and **Your
rewards**. Offer a mobile customer website for people without an iPhone, using the
same account and balance. **James selected a focused custom Firebase backend on
7 November 2026**, starting with free coffee. Magic Stamp is now an export/migration
dependency, not an SDK prerequisite. Loopy is not the selected backend. The earlier
provider comparison below is retained as dated research, not the delivery order.

Use the existing Shopify customer-account sign-in for customers and staff. Firebase
stores private staff roles and café permissions keyed to verified Shopify identity;
there is no second Firebase login. Staff access can be revoked without removing the
shopping account. Initial implementation is [FLY-149](https://linear.app/flyinghorsecoffee/issue/FLY-149).

- One programme shared across participating cafés; café purchases only at launch.
- Staff use named accounts and a camera-scanning website on a phone or tablet.
- Initially issue free-coffee rewards and allow audited manager adjustments/grants.
  Keep versioned benefits extensible; configurable other items and money off are later work.
- Approved migrations preserve existing progress and outstanding rewards, with
  an optional additional, configurable switch-over reward.
- Target recurring provider cost broadly comparable to Magic Stamp's total bill.
- Ship the feature disabled; release separately through PostHog with app-version
  targeting. It need not be available in the first app release.
- Shopify purchases, subscriptions, referrals, birthdays, broad targeting and
  automatic till integration are outside the first release.

## Historical provider comparison and decision gates

| Option | Evidence as of 7 November 2026 | Blockers or conditions |
| --- | --- | --- |
| Magic Stamp: preferred continuity | Its product uses a customer app and physical stamp. Advertised GBP Basic is £29/site/month; Growth is £39/site/month and includes portal access; optional custom card design is £250. | No supported public SDK/API for embedding the existing programme was found. Private partner access remains unknown. Need written permission, technical access, account-linking and safe mutation proof. |
| Loopy Loyalty: first alternative | Public API supports digital stamp cards and custom integrations. Monthly tiers list $25 for one location/three staff users, $69 for three locations/ten users, and $95 for ten locations/50 users. | Wallet-centred product: verify native rendering rights, relevant API entitlement, reward types, staff access limits, migration and timeout recovery. Do not infer all API features are included in a listed tier. |
| Square Loyalty: conditional | Documents third-party mobile integrations and UK availability. | Prefer only if till discovery establishes a useful Square fit. No till replacement is proposed. Account enrolment and programme constraints must be assessed against the customer journey. |
| Voucherify: capability benchmark | API-first loyalty/promotions with custom UI support. Business pricing advertises €600/$650 per month. | Likely above the intended budget; further consideration requires a clear operational benefit. |
| Custom Firebase service | Fits the existing backend direction and allows the requested UI and workflows. | Flying Horse owns security, ledger correctness, staff operations, support, reconciliation and maintenance. Hosting alone is not total cost. |

Sources: [Magic Stamp pricing](https://magicstamp.com/en-US/pricing),
[Loopy integration guide](https://docs.loopyloyalty.com/en/articles/3898154-how-to-use-the-loopy-loyalty-api),
[Loopy API](https://developer.loopyloyalty.com/),
[Loopy pricing](https://loopyloyalty.com/pricing/),
[Square API](https://developer.squareup.com/docs/loyalty-api/overview),
[Voucherify loyalty](https://www.voucherify.io/loyalty-software),
[Voucherify pricing](https://www.voucherify.io/pricing).

These findings establish candidates, not contractual entitlement or working
integrations. No public Magic Stamp documentation is not proof that an integration
is impossible. A link to its app or a separate white-label app does not satisfy
the embedded requirement. Do not use undocumented endpoints or reverse-engineer
the physical stamping protocol.

The original plan proposed vendor proofs before selecting a service. James later
chose custom because the first release needs a narrow ledger, staff confirmation
and manual management, rather than an external loyalty platform. Those vendor
proof gates no longer block engineering. Export availability and old-card
retirement remain relevant to migration. Preserve vendor costs as historical
benchmarks; no paid vendor commitment has been made.

## Customer and staff journeys

### Collect stamps

1. The customer signs into Flying Horse and opens Rewards.
2. The card shows progress, the promised completion reward, earning rules and locations.
3. **Collect stamps** opens a short-lived QR code or typed fallback code.
4. Authenticated staff scan it, verify the purchase and confirm eligible quantity.
5. The server records the award and returns the confirmed outcome; the card refreshes.

The challenge does not contain an editable balance or grant permission to award
stamps. Scanning is not payment proof. Until POS integration exists, staff are
responsible for checking the sale. Multiple eligible items may earn multiple
stamps according to the confirmed programme rules.

### Use a reward

Card completion issues the configured reward; further purchases contribute to
the next card. Unredeemed rewards remain separately visible.

1. The customer sees the reward name, benefit, terms, locations and expiry.
2. **Use reward** opens a short-lived redemption QR code.
3. Staff scan it and see the exact benefit, restrictions and eligibility result.
4. Staff confirm redemption after checking the sale qualifies.
5. The server consumes the reward once and both screens show confirmation.

Opening, closing or expiring the QR must not consume the reward. Two cafés trying
to redeem it concurrently must yield exactly one success. Stamping and redeeming
are distinct challenge purposes. An expired challenge can be regenerated if the
underlying reward is still valid.

Staff apply the free item or discount using normal till controls. Do not claim
the sale has been discounted automatically. If recording redemption succeeds but
the till adjustment fails, a manager resolves it through an audited reversal;
do not ask staff to retry redemption blindly.

### Later extension: configurable offers

This is the planned extension, not the initial free-coffee backend acceptance.
[FLY-159](https://linear.app/flyinghorsecoffee/issue/FLY-159) owns additional benefits.

Managers can create, preview, schedule and retire versioned offers with:

- Customer-facing title, description and terms.
- A named eligible free item, including size/add-on restrictions, or fixed GBP
  money off with minimum spend. Store money in integer minor units.
- Participating locations, issuance window and an explicit reward expiry rule.
- Trigger: card completion or approved migration.
- Per-member limit, campaign issuance cap and combination policy.

Default to one reward per sale, no cash change for money off, and no reward
stacking. Cashiers verify sale restrictions until the till is integrated.
Campaign retirement or cap exhaustion stops new promises; it must not invalidate
issued rewards or change what an active card promised. Reserve capacity for
accepted active-card promises, or reject that campaign configuration if the
selected provider cannot honour it. A migration bonus that is unavailable must
be disclosed before the customer agrees to transfer.

Changes create new offer/rule versions. Existing issued rewards retain a snapshot
of benefit, terms and expiry; an in-progress card retains its promised completion
reward. Changing the stamp target must not silently revalue existing progress.
Switch-over bonuses are additional to imported balances, not an exchange for them.

### Staff and customer access

Cashiers can award/redeem at authorised locations. Managers handle migration,
corrections and campaigns. Administrators invite/revoke staff and assign locations.
Use named Shopify accounts, not a shared cashier password; support session expiry
and prompt revocation through server-owned roles. Verify the staff website
Shopify OAuth callback/session handling before deployment.

Start with a responsive staff website, not a second native app. Test camera
permission, lighting and connectivity on the actual café hardware. Typed fallback
codes must be short-lived, rate-limited and subject to the same authorisation.

The customer website uses the same verified member identity as iOS. Its sign-in
must be supported by the Shopify customer-account configuration; validate web
callback/session handling rather than reusing an iOS callback URI.

## Feature flags and version-aware tabs

Required by James on 7 November 2026: Rewards can ship dormant and be enabled later
in PostHog only for suitable app versions. This is a launch prerequisite, not a
post-launch enhancement. No live flag was created or enabled by this document.

### Customer rollout flag

Use the boolean key `rewards_enabled`, default false. Add a typed `FHFeatureFlag`
case through the existing `FeatureFlagService` boundary; no scattered raw keys or
direct SDK calls in screens. The inspected app already has PostHog and typed flag
support, but `AppTab.activeTabs` is currently static.

PostHog supports semantic-version release conditions. Configure the iOS rule as
the correct environment/app namespace AND current device app version greater
than or equal to the first verified Rewards-capable release, with an optional
pilot cohort or percentage. The release version is not yet known: keep the flag
off until that build passes QA. Every alternative matching condition must retain
the version/environment restriction; a broad pilot rule must not bypass it.

Supply current-device evaluation properties before loading flags, including
environment, namespace, version and build. The app already records `fhc_app_version`
and `fhc_app_build` as analytics context; event properties alone do not establish
that flag evaluation receives them. Verify that path explicitly. Do not rely on a
person's last reported version, because one account may use old and new devices.
Use semantic comparison, never string ordering: `1.10.0` must sort after `1.9.0`.

Use separate platform conditions for the customer website, with its deployed
capability revision; do not require an iOS version property on web. A version flag
cannot add code to an old binary. Only builds containing Rewards can expose it.

### Dynamic tab behaviour

- Off/unresolved: Shop, Locations, Account; no Rewards promotion or active deep link.
- On: Shop, Rewards, Locations, Account.
- Build a single resolved tab list, used by rendering, selection, analytics,
  deep-link routing and background preparation. Never index a dynamic tab bar
  through the old static list.
- Preserve existing navigation stacks and selected tab by identity when the
  list changes. If Rewards disappears while selected, dismiss its challenge
  safely and select Shop; preserve unrelated cart/account/navigation state.
- Resolve on launch, foreground and identity changes. Clear the previous
  account's eligibility on sign-out/switch and refresh before exposing Rewards.
- Start hidden until eligibility is confirmed for the current identity/session.
  Do not show a stale true value belonging to another identity or environment.
  Validate SDK freshness/error semantics; extend the service's result state if
  the existing boolean/cache-only interface cannot distinguish these cases.
- While foregrounded, refresh on a bounded interval (initial target 60 seconds)
  and before creating a challenge. Network failure must not silently grant new
  access. Document that a remote toggle is not instantaneous on offline devices.

### Backend protection and emergency pause

The client flag controls presentation, not authorisation. The backend verifies
identity, role, location and reward state independently. Gate new challenges and
transactions server-side with the selected rollout policy and a server-controlled
`rewards_operations_enabled` emergency switch, default off until rollout.
Staff must not become eligible merely by passing a customer flag.

Check the operation gate when committing, including previously issued challenges.
Use a bounded freshness policy (initial maximum 60 seconds); unavailable or stale
gate evaluation rejects new mutations safely. Return the original confirmed
result for a duplicate committed operation even when new operations are paused.
Keep authenticated status lookup and support/reconciliation available.

Turning off `rewards_enabled` hides customer entry points. A full operational
shutdown also disables the server switch; document both steps in the runbook.
Neither switch deletes balances, cancels earned rewards or reissues migration
bonuses. Pause migration/bonus issuance with operations. Local debug overrides
must never bypass production server controls. Use descriptive PostHog tags such
as `loyalty`, `ios` and `backend`, never ticket IDs.

Sources: [PostHog release conditions and semver](https://posthog.com/docs/feature-flags/creating-feature-flags),
[iOS SDK usage](https://posthog.com/docs/libraries/ios/usage).
Confirm the installed SDK and connected project behaviour during implementation.

## Architecture and public interfaces

Reuse UIKit navigation with SwiftUI screen content. Add domain models and services
through the existing module boundaries, not provider types in presentation code.
The inspected iOS app uses Shopify customer-account OAuth. The backend must verify
the session and derive the Shopify customer identity server-side, then map it to
an internal loyalty member. Never trust a client-supplied customer ID or assume
Firebase Authentication already represents customer login.

Expose authenticated capabilities for programme/balance/rewards/activity reads,
challenge creation, staff preview/award/redemption, operation-status lookup and
manager migration/correction/campaign management. Return stable operation IDs,
confirmed or pending state and safe errors; do not expose provider secrets.

Firestore is the authoritative balance and reward ledger. All mutations pass
through the authenticated backend; there is no second vendor ledger to synchronise.
Keep any legacy Magic Stamp import evidence private and reconcile cutover changes.

Every mutation needs an idempotency key bound to actor, member and request content,
plus an audit entry. Reusing a key with different content is rejected. An uncertain
provider response remains pending and is reconciled before another attempt.
Serialising local requests alone cannot prevent duplicates after provider timeouts.
For custom, atomically update balance, ledger, challenge consumption, reward
issuance/caps and operation result. Corrections are compensating entries with
reasons, not history rewrites. Staff permissions and campaign limits are enforced
server-side. Keep staging and production stores, identities and operations isolated.

## Migration, cutover and recovery

### Preferred: verified export and import

Obtain programme versions, stable member/card identifiers, stamp balances,
outstanding rewards and expiry dates. Dry-run the mapping, reconcile counts and
value, and securely prove customer ownership before linking. Do not match by name
alone or accept an unverified email claim. Handle aliases, changed addresses,
duplicate cards and existing destination balances through manager review.

Coordinate the snapshot and cutover: stop old earning/redemption or obtain a
reconciled final delta so changes after the export are not lost or duplicated.
Import progress and outstanding rewards separately; do not convert everything
into repeated purchase stamps. Use repeat-safe batch/row identifiers and retain
private reconciliation evidence. Agree expiry treatment without shortening an
existing promise silently.

### Fallback: controlled manual transfer

1. Customer signs in and shows staff their live Magic Stamp card, not only a screenshot.
2. Staff record the available stable old card identifier, current progress and
   outstanding rewards, with the minimum private evidence required.
3. Check both source card and destination member for a previous transfer across
   all locations and both automated/manual routes.
4. Confirm one audited migration operation, preserving progress and rewards.
5. Issue the configured switch-over bonus once, when eligible and available.

Do not manually press the stamp button enough times to recreate a balance: that
could trigger unintended rewards and distort activity. A migrated member may
already have new stamps; preview and reconcile before importing rather than
overwriting or blindly adding. Corrections cannot reset the bonus eligibility.

Without stable source identifiers or a retirement mechanism, robust prevention
of duplicate transfers and old-card reuse is impossible. Route unverifiable
claims to managers with an explicit goodwill limit; record this residual risk.
The exact limit and transition dates require operational approval before launch.
Retire/flag transferred cards where supported and coordinate all cafés. Avoid
indefinite dual earning/redemption. Do not cancel the provider before exports and
reconciliation are secured. Explain deadlines and support routes to customers.

### Outages and rollback

Cached balances may be shown with freshness information, but v1 does not confirm
offline stamping or redemption. Use receipts and manager review for missed stamps.
Record discretionary outage gifts separately. Resolve uncertain operations before
retrying or reversing them. Never roll back to an old ledger snapshot after real
rewards have been used; reconcile deltas and preserve the transaction history.

Store customer evidence and exports privately, with restricted access and agreed
retention/deletion handling. Keep raw exports, credentials, contact details, QR
tokens and operational evidence out of this public repository and analytics.
Verify provider data-processing terms and exit/export rights before commitment;
do not assume existing marketing consent transfers to a new channel.

## Delivery gates, effort and acceptance

| Stage | Deliverable | Exit evidence |
| --- | --- | --- |
| Discovery | Actual contract/cost, café count, active members, programme rules, outstanding rewards, till/hardware inventory | Named operational owner confirms missing inputs; no invented stamp target or release version |
| Custom backend proof | Single Shopify identity, private staff roles, transactional Firestore ledger | Permission, concurrency, retry, reward and default-off evidence |
| Feature foundation | Typed flags, dynamic tabs, backend gate, verified identity and provider interface | Flag/version tests and no exposed unfinished Rewards tab |
| Customer/staff delivery | iOS Rewards, mobile web card, staff scanning, manager offers and corrections | End-to-end staging tests on intended devices |
| Migration rehearsal | Dry-run import, manual fallback and switch-over bonus | Totals reconcile; duplicate and partial failure tests pass |
| Pilot | Staff accounts then invited customers at one café | James's hands-on QA, no unexplained balance discrepancies, counter workflow accepted |
| Rollout | Staff training, customer communication, version/cohort rollout and cutover | Operational sign-off, monitoring/support ready; staged expansion |

## Linear delivery project

[Flying Horse Rewards](https://linear.app/flyinghorsecoffee/project/flying-horse-rewards-78da0101d925)
is separate from core app launch and subscription delivery. It can launch later.

| Milestone | Tickets |
| --- | --- |
| Programme and backend foundation | [FLY-118 research](https://linear.app/flyinghorsecoffee/issue/FLY-118), [FLY-149 backend](https://linear.app/flyinghorsecoffee/issue/FLY-149), [FLY-150 rules/operating policy](https://linear.app/flyinghorsecoffee/issue/FLY-150) |
| Customer and café interfaces | [FLY-151 staff scanner](https://linear.app/flyinghorsecoffee/issue/FLY-151), [FLY-152 native Rewards](https://linear.app/flyinghorsecoffee/issue/FLY-152), [FLY-153 flags/tabs](https://linear.app/flyinghorsecoffee/issue/FLY-153), [FLY-154 mobile web](https://linear.app/flyinghorsecoffee/issue/FLY-154) |
| Migration and staging assurance | [FLY-155 transfers](https://linear.app/flyinghorsecoffee/issue/FLY-155), [FLY-156 staging/monitoring](https://linear.app/flyinghorsecoffee/issue/FLY-156), [FLY-157 end-to-end QA](https://linear.app/flyinghorsecoffee/issue/FLY-157) |
| Café pilot and release | [FLY-161 production configuration](https://linear.app/flyinghorsecoffee/issue/FLY-161), [FLY-158 rollout](https://linear.app/flyinghorsecoffee/issue/FLY-158) |
| Later additional rewards | [FLY-159 benefit types](https://linear.app/flyinghorsecoffee/issue/FLY-159); not a free-coffee launch blocker |

Backend work proceeds before migration access is resolved. The interfaces depend
on its contract; native Rewards also depends on flagged tab composition. QA depends
on the customer/staff journeys, migration rehearsal and staging proof. Pilot depends
on approved programme rules and James's end-to-end QA. No launch date is committed.
Backend implementation/setup guidance lives in its owning repository, not here.

### Acceptance matrix

- Card completion and multiple-item purchases issue the promised reward exactly once.
- Free-coffee rewards show correct terms and expiry; later additional benefits
  require their own currency/restriction/compatibility acceptance.
- Opening, abandoning or expiring QR codes preserves valid underlying rewards.
- Repeated scans, double taps and concurrent café redemption cannot duplicate value.
- Connection loss after commit resolves to the original result without another award.
- Revoked/wrong-location staff and cross-member requests are rejected.
- Multiple devices and web/iOS share one member balance without leaking sessions.
- Offer edits, campaign retirement/caps and rule changes preserve accepted promises.
- Imports preserve progress/rewards; repeat imports/manual transfers do not duplicate
  them or the switch-over bonus where stable source identity exists.
- Missing/false/error flag states hide Rewards; eligible versions show it; older
  versions stay hidden. Test semantic versions such as 1.9.0 versus 1.10.0.
- One member on old/new devices receives device-appropriate evaluation. Sign-out,
  account switching, foreground refresh and web targeting do not reuse stale eligibility.
- Dynamic tab changes preserve unrelated navigation and correctly attribute tab
  analytics; disabling while Rewards is selected safely returns to Shop.
- Deep links and cached QR codes cannot bypass disabled backend operations;
  completed operation status remains recoverable during a pause.
- Camera denial, typed fallback, accessibility, small screens and weak connectivity
  work on actual café hardware; staging cannot affect production.

Instrument confirmed awards/issuance/redemption, migrations, safe failures,
pending outcomes, reconciliation and recovery with opaque correlation. Alert on
unresolved operations and discrepancies through approved destinations. Analytics
failure must not undo a business transaction. Follow owning repositories'
observability contracts and record what was actually verified.

Use existing CI for checks and snapshots; no local snapshot run is authorised.
Keep PRs open for James. Implemented journeys require QA before Done; passing tests
does not replace his hands-on validation. Production rollout and migration are
separate operational steps, not implied by publishing this plan.
