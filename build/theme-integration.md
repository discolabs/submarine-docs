---
description: Documentation for integrating Submarine into a Shopify theme.
---

# Theme Integration

Submarine is not at all prescriptive about how you build your customer-facing experiences, whether it's for presales, subscriptions or saved payment methods. It's up to you to decide on the flow that makes sense for your customers and to build that into your theme.

As described in the [Theme customisation](https://docs.getsubmarine.com/build/setup-and-configuration#theme-customisation) section of the setup and configuration page, there are three areas of Submarine theme integration:

* Checkout customisation;
* Customer purchase flow;
* Customer account interface.

The checkout customisation and customer account interface elements are described further on the Setup and Configuration page, and in the documentation for the [Submarine.js](https://github.com/discolabs/submarine-js) Javascript library.

This page focuses on the line item properties and cart attributes you can set at any point during the customer purchase flow to drive special behaviours.

## Subscription behaviours

Submarine uses a combination of cart-level attributes, line item-level properties, and configuration to determine the subscription behaviour for a particular order.

### Cart-level attributes

#### Subscription frequency \(required\)

You _must_ define a subscription frequency as a cart-level attribute to trigger Submarine's subscription behaviour.

The name of the frequency cart-level attribute is `_subscription_frequency`.

Possible values for the cart-level attribute are a magnitude and a unit, separated by an underscore - for example, `7_days`, `3_months`, or `1_year`.

#### Other attributes

Any other cart-level attributes will be persisted on future subscription orders if they are whitelisted by the Submarine configuration.

### Line item-level attributes

#### Subscription flag \(required, depending on configuration\)

Submarine offers three subscription line item configuration modes, depending on merchant requirements:

* If a subscription frequency is present at the cart level, include all line items in the resulting subscription;
* If a subscription frequency is present at the cart level, only include line items with a line item property of `_subscription_line_item` set to `true` in the resulting subscription;
* If a subscription frequency is present at the cart level, include all line items other than those with a line item property of `_subscription_line_item` set to `false` in the resulting subscription.

These options allow you to easily create subscriptions with "mixed carts" - that is, some subscription items and some one-off items.

#### Line item subscription frequency \(optional\)

If you'd like to override the cart-level subscription frequency for an individual line item, you can do that with a line item-level property.

The name of the frequency line item-level property is `_subscription_frequency`.

The possible values are the same as for the cart-level frequency attribute.

{% hint style="info" %}
Submarine will group line items by their frequency when creating subscriptions - so if your initial order has multiple subscription frequencies defined on different line items, multiple subscriptions \(one per frequency group\) will be created.
{% endhint %}


