---
description: The process for installing and configuring Submarine on a Shopify Plus store.
---

# Setup and Configuration

This page walks through the steps that will be undertaken to set up and configure Submarine on a Shopify store. All new Submarine installations are currently managed by Disco Labs, so they'll be able to provide support and guidance during this process.

Typically, the below process will first be undertaken on a staging store prior for development and testing prior to a production deployment.

## Installation

### Shopify applications

Disco Labs will request collaborator access to each Shopify store Submarine is to be used on.

Once granted, the Submarine, Shopify Script Editor and Shopify Flow applications will be installed on each store \(if not already installed\).

### Payment gateway

The Submarine Payment Gateway will be installed on each store.

{% hint style="info" %}
Payment Gateways must be installed and configured by the owner of the Shopify store. Disco Labs will provide the owner with the URL to begin the installation process and the requisite configuration values.
{% endhint %}

### Checkout customisation

If not already enabled, checkout customisations will be enabled on each store \(this requires a request to the merchant's Shopify Plus account manager\).

## Configuration

### Payment processors

Disco Labs will configure API credentials for the merchant's selected payment processors within Submarine.

### Payment gateway script

The merchant's active Payment Gateway Shopify Script will be updated to include logic to show or hide the Submarine payment gateway based on the type of order going through the Shopify checkout.

## Theme customisation

### Checkout customisation

A small customisation will be applied at the payment method step to properly display the Submarine payment gateway options during checkout.

Disco Labs can implement this customisation directly or provide code examples and instructions for the merchant or third party partner to install.

### Customer purchase flow

The Shopify theme is customised to support the functionality enabled by Submarine \(eg by capturing customer subscription preferences on the product page or in the checkout\).

No specialised libraries or APIs are needed to develop these customer flows - they can be built using the Liquid, HTML, CSS and Javascript typically used for the development of Shopify themes. Special behaviours \(such as configuring subscription frequencies\) are driven through the use of standard Shopify features like [Line Item Properties](https://help.shopify.com/en/themes/customization/products/features/get-customization-information-for-products) and [Cart Attributes](https://help.shopify.com/en/themes/customization/cart/get-more-information-with-cart-attributes).

The design and implementation of these customisations is typically undertaken by the merchant's in-house development team or third party agency partner with support from Disco Labs.

### Customer account interface

The customer account section of the merchant's Shopify theme is customised to support customer-facing Submarine functionality such as managing payment methods and subscriptions.

Customer account interfaces are typically built based on the Liquid, HTML, CSS and Javascript already in use for the merchant's Shopify theme, with the addition of an integration point to Submarine's [Customer API](customer-api.md) for the retrieval and management of Submarine-specific data. The [Submarine.js](https://github.com/discolabs.com/submarine-js) client library is provided to make interacting with the Submarine Customer API as easy as possible.

The design and implementation of these customisations is typically undertaken by the merchant's in-house development team or third party agency partner with support from Disco Labs.

