---
description: How Submarine is structured and how it integrates with a Shopify Plus store.
---

# Platform Architecture

Submarine consists of several different components working together. This page provides an overview of each component and their role and responsibilities within the platform.

## Platform components

### Application

The Submarine Shopify application is installed into a merchant's store just like any other third-party Shopify app. The application is the source of truth for information about customer payment methods and subscriptions.

The app includes a merchant-facing interface inside the Shopify admin that allows for the admin management of customer payment methods and customer subscriptions.

The app provides the [Theme API](../build/theme-api.md), the [Admin API](../build/admin-api.md), and the [Shopify Flow Integration](../build/shopify-flow-integration.md).

### Payment gateway

In order to integrate tightly with the existing Shopify checkout, Submarine provides its own payment gateway that is installed into the merchant's Shopify store.

Logic is applied to direct all customer checkouts requiring tokenised payment functionality through the Submarine payment gateway, which in turn supports each of the payment methods configured by the merchant.

### Theme integration

While Submarine does not strictly require any theme customisations to function, in order to take full advantage of its functionality changes to the merchant's theme will be required. These changes will likely include:

* Customisation of the Shopify checkout at the payment method step, which displays the payment methods available through Submarine directly inside the Shopify checkout;
* Adding customer-facing interfaces during product selection or checkout that capture a customer's subscription preferences, and stores those preferences against the order in a format that can be understood by Submarine;
* Building out customer account interfaces to allow the management of stored payment methods and customer subscriptions.

In keeping with its philosophy of giving developers full control over the Submarine implementation, Submarine does not make any automated changes to a merchant's theme code or add any Javascript to the storefront. However, we do provide libraries that can be easily pulled into custom development workflows to make this integration easier.

### Script Editor integration

If a merchant requires only some orders to be processed through Submarine \(as is usually the case\) then a Payment Gateway Shopify Script needs to be added to the store that will detect this condition and show or hide payment gateways as appropriate.

