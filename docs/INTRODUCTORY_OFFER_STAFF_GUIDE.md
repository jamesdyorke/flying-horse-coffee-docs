# Three-delivery coffee offer staff guide

This is the planned process for the website offer. The public offer is
currently off. Use this guide for warehouse, customer support and launch
briefing. The [launch checklist](INTRODUCTORY_SUBSCRIPTION_OFFER.md) tracks
what has passed and what still needs testing.

## What the customer buys

The customer pays upfront for three monthly deliveries at 25% off. One
bundle covers all three. Shopify shows the full checkout total, including
shipping. Once the prepaid payment is verified, the system sets up the
monthly subscription and schedules its first payment for the end of the
original prepaid period. The starting monthly coffee price is 10% off the
price at purchase. Recharge handles monthly billing and shipping charges.

## What happens after checkout

| When | What happens automatically | What staff do |
| --- | --- | --- |
| At purchase | Shopify takes the prepaid payment and creates the first order. Recharge schedules the remaining two shipments. After payment is verified, the monthly subscription appears with a future billing date. | Pick, pack and dispatch the first order as usual. Check that Shopify shows the order as **Fulfilled** once it’s dispatched. |
| About one month later | Recharge creates a Shopify order for delivery two. The merchandise is already paid for. | Dispatch the second order and check it shows as **Fulfilled**. |
| About two months after purchase | Recharge creates the Shopify order for delivery three. The merchandise is already paid for. | Dispatch the third order and check it shows as **Fulfilled**. |
| On the scheduled first monthly billing date | Recharge takes the first monthly payment and creates the monthly order. | Refer payment or subscription questions to whoever manages Recharge. |

For example, for a bundle bought on the 15th, the usual shipment dates are
the 15th of that month and the 15th of each of the next two months. The
first monthly payment ordinarily falls on the 15th of the fourth month.
Check the recorded date in Recharge. A late shipment leaves that date in
place.

## What staff and customers see

| When | Shopify Orders | Recharge |
| --- | --- | --- |
| First checkout | One **Paid** order for one bundle. The coffee subtotal covers all three deliveries at 25% off. Shipping is shown separately. Pack **one delivery**. | One prepaid payment and the three-shipment schedule. The next two prepaid shipments are queued at **£0**. Once the payment and schedule are verified, a separate **Active** monthly subscription shows the first billing date and monthly coffee price. |
| Second and third prepaid shipments | A separate **£0** order appears each time Recharge processes the shipment. Pack one delivery from each order and mark it **Fulfilled** after dispatch. | The corresponding shipment moves out of the upcoming queue and links to its Shopify order. An **Expired** label on the prepaid billing plan can appear after its one upfront payment. Check the shipment queue for what is still due. |
| First monthly payment | A new paid monthly order appears when payment succeeds. It covers one delivery. | The active monthly subscription records the charge and shows the next monthly billing date. If payment fails, check the charge status and follow the normal Recharge payment support process. |

For coffee normally priced at £10, Shopify shows **£22.50** for the prepaid
bundle before shipping. The separate subscription starts at **£9 per month**
for coffee, with shipping shown separately. A purchased bundle and its
monthly subscription are linked but each has its own record.

The customer sees the bundle price, shipping and monthly terms before
paying. They receive the Shopify order confirmation. Any Recharge activation
email for the bundle should explain the single upfront payment and the
separate monthly subscription. Check a real confirmation at launch so staff
know exactly what customers receive. For any question about the next charge,
use the billing date on the active monthly subscription.

PostHog purchase, subscription setup and prepaid shipment alerts can carry
the original Shopify order number and a shared bundle reference. A shipment
alert means Recharge processed that shipment into Shopify. Use **Fulfilled**
in Shopify to confirm dispatch. Use the Recharge charge and matching Shopify
order to verify the first monthly payment while production first-payment
alerting is being verified.

## What the warehouse needs to do

Pick, pack and dispatch each Shopify order as usual. Mark the order
**Fulfilled** after dispatch. Staff can use Shopify fulfilment as their
dispatch record without a carrier-delivered update.

Recharge schedules all three prepaid shipments at purchase. If an earlier
Shopify order is still **Unfulfilled**, the next prepaid order can still
appear on its scheduled date. Check any outstanding order through the usual
dispatch workflow and record fulfilment once it has shipped. Raise late or
missing shipments through the usual dispatch process. The first monthly
billing date stays on the original schedule.
[Recharge prepaid shipment schedule](https://support.getrecharge.com/hc/en-us/articles/360008830433-Changing-the-product-or-swapping-variants-for-an-existing-prepaid-subscription)

A production sample on 15 November 2026 found 18 recent subscription orders
with the exact successful single-item fulfilment the worker expects. One
recent subscription order was still unfulfilled. None of the 25 fulfilled
orders in the wider sample had a carrier-delivered status. The previous
pilot's three orders also passed the same fulfilment check, including the
two prepaid follow-ups.

## If something looks wrong

| What you see | What to check |
| --- | --- |
| A prepaid shipment order is missing in Shopify | Check the next shipment date and order status in Recharge. Send the original Shopify order number and the missing shipment month to the person who manages Recharge. |
| The parcel was dispatched but Shopify still shows **Unfulfilled** | Check the dispatch record and complete or correct the normal Shopify fulfilment for that order. |
| The monthly subscription or its first billing date is missing after payment | Send the original Shopify order number to the person who manages Recharge. They can check the payment, the prepaid schedule and the linked monthly subscription. |
| A prepaid order is cancelled or prepaid merchandise is refunded before first monthly billing | Check in Recharge that the linked monthly subscription is cancelled and the queued charge is gone. Escalate to the Recharge operator if it is still queued or has already been paid. |
| The customer requests a pause or another subscription change | Route it through the usual support process so payment, shipment and subscription records can be checked together. |

## First live order

James will enable the offer and place one order on an eligible coffee.
Before payment, check the correct grind and size, one three-delivery bundle,
the 25%-off subtotal, shipping, and the first monthly payment terms.

After payment, match Shopify’s original order to Recharge. Confirm the one
prepaid payment, the two upcoming £0 shipments, and exactly one active
10%-off subscription with its first payment three months after purchase.
Check the customer email and the purchase and subscription setup alerts.
Capture the original order number for staff follow-up. During a supervised
test, processing a £0 shipment early is a manual test step. On ordinary
orders Recharge processes those shipments automatically on the scheduled
dates. Check that the first monthly billing date stays in place.

Staging order #1027 verified the three prepaid orders and a manually
processed first monthly payment reaching Firebase. Scheduled first-payment
alerting and the first public production purchase still need verification.
See [FLY-190](https://linear.app/flyinghorsecoffee/issue/FLY-190) and
[FLY-192](https://linear.app/flyinghorsecoffee/issue/FLY-192).
