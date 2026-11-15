# Website subscription offer handover for 15 November 2026

[Production launch checklist](INTRODUCTORY_SUBSCRIPTION_OFFER.md) ·
[FLY-133 continuation worker](https://linear.app/flyinghorsecoffee/issue/FLY-133) ·
[FLY-135 website checkout](https://linear.app/flyinghorsecoffee/issue/FLY-135) ·
[FLY-138 production rollout](https://linear.app/flyinghorsecoffee/issue/FLY-138)

## Resume here

> Resume with fulfilment of fresh paid pilot #5048.
> Firebase v0.3.2 is deployed and the private minute scheduler has run in
> production. Keep the public offer disabled. Follow the fresh order from
> checkout through fulfilment, one automatic £0.90 monthly subscription and
> one approved renewal. Confirm retries reuse the same subscription and
> Recharge queues no second renewal. Update FLY-133 and the shared checklist
> as each step is proved.

**First task.** Use #5048 for the remaining pilot. Shopify and Recharge
verified the £5.15 checkout. The automatic Firebase intake recovered on its
first retry and saved the original three-delivery schedule. PostHog recorded
one production payment milestone. Both £0 follow-ups are still queued.

The [protected production deployment](https://github.com/jamesdyorke/flying-horse-coffee-firebase-backend/actions/runs/43702651385)
published [v0.3.2](https://github.com/jamesdyorke/flying-horse-coffee-firebase-backend/releases/tag/v0.3.2).
The private job ran naturally at 10:14 UTC, returned HTTP 200 and logged
`idle`. The public catalogue was disabled and there was no pilot activation.
Leave #5043 unscheduled. The renewal for #5048 is also unscheduled.

## Status at the end of 14 November

| Area | Verified | Still open |
| --- | --- | --- |
| Staging | The accelerated scheduled worker switched a paid staging pilot. The normal staging worker also held for a delayed final fulfilment, created one £9 continuation and reused it on retries. | Carry this evidence forward. Reuse the existing staging fixture for changes to the scheduler or batching. |
| Production pilot checkout | One bundle on the existing Unlisted £1 pilot produced Shopify #5043. It cost £2.25 for merchandise and £2.90 UK shipping, £5.15 total. | Verify the current total and stock before James buys one fresh bundle. |
| Production payment intake | Firebase retained one verified enrollment for #5043. Two signed no-charge replays left one payment record and one delivered PostHog payment milestone. The private payment observer is deployed in v0.3.1. | Observe a fresh checkout reaching Recharge, Firebase and PostHog without operator replay. |
| Production fulfilment | Recharge retained the three original dates and released the two £0 follow-ups as #5044 and #5045. Shopify shows #5043–#5045 all Fulfilled. | For the new order, capture the original dates before releasing its two £0 follow-ups. |
| Production continuation | The read-only rehearsal on #5043 returned a live £0.90 merchandise quote. Recharge showed the source Expired after its one allowed prepaid charge. | Production creation, the automatic trigger, the actual queued total and expiry after one renewal remain open. No renewal was scheduled or charged. |
| Public catalogue | The disabled production draft maps 55 variants to the audited Recharge plans. | Verify Shopify prepaid selling-plan links, catalogue coverage, website presentation and the ordinary production worker before launch. |

The customer offer stays disabled during the pilot. The existing test product
can be reached by direct link. Any real checkout and any step that schedules
a future charge are separate decisions for James.

### Pull requests and evidence

- [Backend #137](https://github.com/jamesdyorke/flying-horse-coffee-firebase-backend/pull/137)
  merged the original schedule recovery. #5043's independent Recharge read
  provided its pre-update dates and Firebase retained them.
- [Backend #138](https://github.com/jamesdyorke/flying-horse-coffee-firebase-backend/pull/138)
  merged the #5043 cutoff command and passed staging deployment.
- [Backend #139](https://github.com/jamesdyorke/flying-horse-coffee-firebase-backend/pull/139)
  merged the read-only early rehearsal. It held with shipments outstanding
  and returned the £0.90 quote after all three fulfilments.
- [Backend #140](https://github.com/jamesdyorke/flying-horse-coffee-firebase-backend/pull/140)
  prepares a private early switch for #5043 only. Both required checks passed.
  It merged and passed protected staging deployment on 15 November.
- [Backend #141](https://github.com/jamesdyorke/flying-horse-coffee-firebase-backend/pull/141)
  added the private minute worker for the next purchase. Protected CI,
  staging and production deployment passed. The production idle tick is
  verified. The paid continuation and renewal are still open.
- [Shared docs #72](https://github.com/jamesdyorke/flying-horse-coffee-docs/pull/72)
  carries this handover and the current launch checklist.

## Tomorrow's execution order

### 1. Get the automatic pilot ready before checkout

- [x] Verify #140's merge and the deployed production payment observer.
  Confirm the pilot is Unlisted and the public catalogue is disabled.
- [x] Prepare the scheduler to accept a separately verified purchase of the
  exact pilot variant and plan. Resolve the new order and Recharge source
  after checkout. The operator in #140 is bound to #5043.
- [x] Deploy the pilot-only one-minute job and verify a natural idle tick in
  production while there is no activation. Verify the hold and quote for
  the fresh order during the paid pilot.
- [ ] Verify one continuation per prepaid source and one provider create
  attempt on retries or overlapping executions. Use the real clock for
  provider evidence and the approved billing date. The pilot's projected
  coverage clock can move the test forward after real fulfilment.

### 2. Verify the fresh purchase

- [x] James bought one prepaid bundle on the existing pilot. Shopify #5048
  shows £2.25 merchandise and £2.90 UK shipping, £5.15 paid.
- [x] Verify automatic Shopify and Recharge payment intake. The signed paid
  webhook reached Firebase at 10:25 UTC on 15 November. The first observer
  attempt failed verification, then the automatic retry confirmed payment.
  One Firebase enrollment and one production PostHog payment milestone
  were verified.
- [x] Retain the original three Recharge delivery dates. The two £0
  follow-ups were still queued and no renewal was scheduled.

### 3. Watch the switch in Recharge

- [ ] Verify the new order holds while its deliveries are outstanding.
  Process its two verified £0 follow-ups and confirm all three Shopify
  orders show Fulfilled. Verify the read-only rehearsal quotes £0.90.
- [ ] Once James approves the test's billing date, let the private scheduled
  worker pick up the fresh order. Show James the prepaid source and the
  one new monthly subscription in Recharge. Verify the first billing date,
  £0.90 merchandise, actual total including any shipping and one-charge cap.
  If the operator command is used instead, record that run as an operator test
  and leave the automatic switch check open.
- [ ] Observe the next scheduled check or reconcile the same operation.
  Verify the child subscription is reused and the continuation confirmation
  remains single. If the provider response is uncertain, reconcile the
  existing operation before any further action on that purchase.
- [ ] After the approved first renewal, verify one paid charge for the
  continuation, that the child expired and that Recharge has no second
  renewal queued. Record the evidence and any gaps in the Firebase and
  PostHog continuation feed. Leave #5043 unscheduled if the fresh order is
  the one selected for the paid renewal test.

## What remains before going live

### Checkout and catalogue

- [ ] Connect and audit the Shopify prepaid selling-plan links for the
  55 mapped variants. Verify the 25% prepaid price, the existing 10% monthly
  option, UK stock and shipping. Use automated checks for the entire mapping
  and unpaid checkout previews where product behaviour differs.
- [ ] Prove the existing product-page widget shows the offer before sign-in.
  Add the agreed explanation that one bundle covers three prepaid deliveries
  and then continues monthly. Verify checkout applies the price automatically
  and the regular 10% monthly option still selects the existing plan.
- [ ] Confirm the final website purchase eligibility and repeat-purchase
  behaviour. A distinct valid paid order gets its own continuation under the
  repeatable offer policy. Retries of the same order reuse its continuation.

### Production automation and launch

- [ ] Have the normal scheduler pick up eligible website purchases on a
  **5–10 minute** cadence. Verify one run handles multiple purchases due at
  once, including purchases paid in the same minute. Verify overlapping runs
  and retries create one continuation per purchase and later runs pick up
  any due orders beyond a batch limit.
- [ ] Verify the normal worker reads current refunds and cancellations,
  confirms all three fulfilments, waits for the actual coverage cutoff, and
  uses the current UK price for the correct product and plan. Early timing,
  fixed £0.90 merchandise and the one-renewal cap are pilot settings.
- [ ] Check production payment, continuation and failure monitoring. Verify
  that pausing new offer checkouts still lets the worker honour paid orders.
  Record James's website QA and rollout decision in the launch checklist.
  Enable the public offer only after his launch approval.
