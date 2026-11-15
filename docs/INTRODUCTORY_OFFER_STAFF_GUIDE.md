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

## What the warehouse needs to do

Use the usual Shopify dispatch workflow. The system reads a successful
fulfilment for the correct order line. Mark the order **Fulfilled** through
the usual Shopify dispatch workflow.

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

The £1 production test uses an Unlisted product. Its release checklist
tracks whether the ordinary checkout and subscription flow works from
payment through the first monthly renewal.

Staging order #1025 verified the prepaid checkout and immediate setup of
one monthly subscription. Its first simulated renewal is due on
18 November 2026. The staging evidence is tracked in
[FLY-190](https://linear.app/flyinghorsecoffee/issue/FLY-190). The test of the
normal production path is tracked in
[FLY-192](https://linear.app/flyinghorsecoffee/issue/FLY-192).
