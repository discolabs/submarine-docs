---
description: Answers to frequently asked technical questions regarding Submarine.
---

# Technical FAQ

## How are refunds handled?

Because Submarine is integrated into Shopify as a payment gateway, you can generally manage refunds in the same way as any payment being processed through a "regular" Shopify gateway.

That means that you can create refunds through the refund interface in the Shopify Admin, or via the Shopify Refund API, and Submarine will take care of passing the refund along to the corresponding payment provider.

There is one thing to be conscious of, which is that there's a slight difference in the refund handling of orders created by customers directly via the Shopify checkout and those created through the API by Submarine \(eg subscription or upsell order\).

When refunding orders placed through the checkout, Shopify will pass the refund request to Submarine _first_, and only applies the refund in the event that the upstream payment processor successfully processes it.

When refunding orders created by Submarine, Shopify will apply the refund _before_ passing the refund request to Submarine, at which point we'll try to apply the refund via the processor. In almost all cases, this will be fine, but in the exceptional case that a refund fails processor-side after it's been applied in Shopify, the merchant will be notified and manual intervention will be required.

