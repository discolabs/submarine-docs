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

#### Subscription frequency \(conditionally optional\)

A subscription frequency must be defined on an order to trigger Submarine's subscription behaviour. It can either be defined as a cart-level attribute or as a line-item property. 

The name of the frequency cart-level attribute is `_subscription_frequency`. When defined at the cart level, the subscription will apply to all subscription items in the cart.

Possible values for the cart-level attribute are a magnitude and a unit, separated by an underscore - for example, `7_days`, `3_months`, or `1_year`.

#### Opt-in for all line items \(optional\)

When all items in a cart should be part of a subscription, it can sometimes be more convenient to flag that once, at the cart level, rather than individually for each line item.

The `_include_all_items_in_subscription` cart-level attribute serves this purpose. When present, there is no need to include any`_subscription_line_item` line-item proiperties - instead, all of the carts items will be automatically included in the subscription.

#### Push back date of first subscription order \(optional\)

Submarine's default behaviour is to start the subscription from the time of the original Shopify order. So, if the frequency is set to every one week and the order was placed on January 2nd, the first subscription order would be generated on the 9th, the second on the 16th, and so on.

Sometimes, merchants may want to delay the start of the subscription, which can be done via the `_submarine_initial_scheduled_at` cart-level attribute. This should be set to ISO 8601-formatted time that the first subscription order should be generated on, e.g. `2020-10-22T12:00:00Z`.

#### Other attributes

Any other cart-level attributes will be persisted on future subscription orders if they are whitelisted by the Submarine configuration.

### Line item-level attributes

#### Line item subscription frequency \(conditionally optional\)

If you'd like to define subscription frequencies at the line-item level, you can do that with a line-item property.

The name of the frequency line-item property is `_subscription_frequency`.

The possible values are the same as for the cart-level frequency attribute.

#### Subscription flag \(required, depending on configuration\)

Submarine offers two subscription line item configuration modes, depending on merchant requirements:

* If a subscription frequency is present at the cart level, include all line items in the resulting subscription;
* If a subscription frequency is present at the cart or item level, only include line items with a line-item property of `_subscription_line_item` set to `true` in the resulting subscription.

These options allow you to easily create subscriptions with "mixed carts" - that is, some subscription items and some one-off items.

{% hint style="info" %}
Submarine can be configured to either group line items by their frequency when creating subscriptions, or create single-item subscriptions for each subscription item in the cart.
{% endhint %}



