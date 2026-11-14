# Resume the website offer on 15 November 2026

[Production launch checklist](INTRODUCTORY_SUBSCRIPTION_OFFER.md) ·
[Continuation worker](https://linear.app/flyinghorsecoffee/issue/FLY-133) ·
[Website checkout](https://linear.app/flyinghorsecoffee/issue/FLY-135) ·
[Production rollout](https://linear.app/flyinghorsecoffee/issue/FLY-138)

## Where we stopped

The public offer is disabled. The existing £1 pilot is Unlisted and available
by direct link. Shopify order #5043 paid £5.15, including £2.90 UK shipping.
Recharge released its two £0 follow-ups as #5044 and #5045. All three orders
show Fulfilled in Shopify. Firebase retained the original prepaid schedule
and one payment. PostHog recorded one payment milestone.

The read-only production rehearsal returned a £0.90 merchandise quote for
the monthly continuation. No continuation was created and no renewal was
scheduled. The original plan shows Expired because its one prepaid charge
completed. That is the expected state of the source contract.

The private payment intake is deployed. The production continuation scheduler
is still missing. The #5043 early switch is an operator command. A successful
run will prove the production switch can create the right subscription.
Automatic switching remains a separate launch requirement.

## Tomorrow's pilot

- [ ] Check [backend #140](https://github.com/jamesdyorke/flying-horse-coffee-firebase-backend/pull/140)
  has merged with required CI. Both required checks passed on 14 November.
  It provides the early switch for #5043 only. Confirm the protected production
  payment intake is healthy, the public offer is disabled and the test product
  is still Unlisted.
- [ ] Before checkout, prepare the private rehearsal and early switch to
  accept a separately verified purchase of this exact pilot. After checkout,
  bind the new verified order and Recharge source. The #5043 switch is
  fixed to the first order. If tomorrow's paid test is intended to prove
  automatic switching, deploy and verify a private, pilot-scoped scheduler
  before purchasing.
- [ ] James opens the existing pilot in a fresh checkout, selects the prepaid
  plan and adds **one** bundle. It covers three deliveries. Check £2.25 before
  shipping, the UK shipping amount and stock. The last pilot total was £5.15.
  James submits the checkout when happy with the total. This is a separate
  real production purchase.
- [ ] Watch the new paid order reach Recharge and Firebase automatically.
  Verify one paid Recharge charge, one Firebase receipt and enrollment, and
  one production PostHog payment event. Verify the three original scheduled
  dates and retain them as soon as all Recharge rows are available, before
  processing the remaining shipments. An operator recovery can restore
  independently verified dates, with that recovery recorded separately from
  the proof of automatic intake.
- [ ] Prove the new order holds while shipments are outstanding. Process only
  its two verified £0 prepaid follow-ups, then verify all three Shopify
  orders show Fulfilled. Run the read-only rehearsal and check that it returns
  the £0.90 quote. Keep the original and fresh orders as separate records.
- [ ] Use the fresh order for the visible switch and approve its billing
  date. Observe the pilot scheduler if deployed, or run the private early
  switch once and record this as operator-driven evidence. In Recharge,
  show James the original prepaid plan and the one
  new monthly subscription. Verify £0.90 merchandise, the queued total
  including shipping, the first billing date and the one-charge cap.
- [ ] Recheck the same operation to prove retries reuse the subscription.
  After the authorised renewal, verify one paid renewal, the child contract
  expired and no second charge is queued. Record what production observed
  in Firebase, Recharge and PostHog.

## Before opening the public offer

- [ ] Finish connecting and auditing the production Shopify selling-plan links
  for the 55 mapped variants. Verify the public 25% prepaid price and the
  existing 10% monthly option, plus UK stock and shipping. Use automated
  mapping checks and an unpaid checkout preview for product-specific
  differences.
- [ ] Prove the existing product-page widget shows both options. Add the
  agreed explanation that one bundle covers three prepaid deliveries and
  then switches to monthly. Verify the offer appears before sign-in and
  checkout applies the price automatically.
- [ ] Wire and verify the private production scheduler. It must pick up
  ordinary paid website orders without an operator command. Verify the
  production catalogue worker checks current refunds and cancellations, uses actual fulfilment
  and coverage, reads the current price and creates one continuation per
  distinct purchase. The pilot's early timing, fixed £0.90 price and
  one-renewal cap apply only to the pilot.
- [ ] Verify production payment and continuation monitoring, plus failure
  and recovery signals. Record owner QA and a rollback check in the launch
  checklist. Publish the offer only after James approves the rollout.
