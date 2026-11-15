# Three-delivery coffee offer staff guide

This is the planned process for the website offer. The public offer is
currently off. Use this guide for warehouse, customer support and launch
briefing. The [launch checklist](INTRODUCTORY_SUBSCRIPTION_OFFER.md) tracks
what has passed and what still needs testing.

## What the customer buys

The customer pays upfront for three monthly deliveries at 25% off. One
bundle covers all three. Shopify shows the full checkout total, including
shipping. When the prepaid period ends and all three orders are fulfilled,
the subscription is set up to continue monthly at 10% off the applicable
price at that time.

## What happens after checkout

| When | What happens automatically | What staff do |
| --- | --- | --- |
| At purchase | Shopify takes the prepaid payment and creates the first order. Recharge schedules the remaining two shipments. | Pick, pack and dispatch the first order as usual. Check that Shopify shows the order as **Fulfilled** once it’s dispatched. |
| About one month later | Recharge creates a Shopify order for delivery two. The merchandise is already paid for. | Dispatch the second order and check it shows as **Fulfilled**. |
| About two months after purchase | Recharge creates the Shopify order for delivery three. The merchandise is already paid for. | Dispatch the third order and check it shows as **Fulfilled**. |
| After the prepaid period ends | The system checks the three Shopify fulfilments and the Recharge schedule. Once both checks pass, it sets up the 10%-off monthly subscription. Recharge handles billing on its scheduled date. | Refer payment or subscription questions to whoever manages Recharge. |

For example, for a bundle bought on the 15th, the usual shipment dates are
the 15th of that month and the 15th of each of the next two months. The
prepaid period ordinarily ends around the 15th of the fourth month. Use
the actual Recharge dates if they move. Dispatching the third order still
leaves its prepaid month to run.

## What the warehouse needs to do

Use the usual Shopify dispatch workflow. The system reads a successful
fulfilment for the correct order line. **Fulfilled** is enough. A separate
carrier **Delivered** status, a tracking scan and a confirmation in
Recharge are unnecessary for the switch.

Recharge schedules all three prepaid shipments at purchase. If an earlier
Shopify order is still **Unfulfilled**, the next prepaid order can still
appear on its scheduled date. Check any outstanding order through the usual
dispatch workflow and record fulfilment once it has shipped. The switch to
the paid monthly subscription waits until **all three** orders show fulfilled
and the prepaid period has ended.
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
| All three orders show **Fulfilled**, but the monthly subscription hasn’t appeared | Check whether the prepaid period has ended, including any schedule changes. If it has ended, send the original Shopify order number and the three fulfilment numbers to the person who manages Recharge. They can check for changes, refunds or a record that needs review. |
| The customer requests a pause, cancellation or refund | Route it through the usual support process so the subscription and payment records can be checked together before the next shipment or charge. |

The internal pilot uses an approved early test date and a one-renewal cap.
Customer subscriptions use their ordinary Recharge schedule. The launch
checklist records the live pilot proof and the remaining checks for the
public offer.

## Planned update before launch

The revised offer sets up the 10%-off monthly subscription when the prepaid
payment is verified. The first monthly payment is scheduled for the end of the
original prepaid period. Its starting merchandise price is set at purchase.
Warehouse staff still dispatch and mark all three prepaid orders **Fulfilled**
in Shopify. An outstanding fulfilment leaves the scheduled billing date
in place, so raise missed shipments through the usual dispatch process.

If the prepaid order is cancelled or prepaid merchandise is refunded before
the first monthly payment, check in Recharge that the linked subscription was
cancelled and its first charge was removed. Escalate to the Recharge operator
if it is still queued or appears to have been paid. This revised process is
awaiting staging verification. [FLY-190](https://linear.app/flyinghorsecoffee/issue/FLY-190)
