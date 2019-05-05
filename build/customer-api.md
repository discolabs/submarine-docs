---
description: Documentation for the Submarine Customer API.
---

# Customer API

The Customer API is used to build bespoke interfaces to allow customers to manage their stored payment methods and subscriptions.

It's designed to be called from within the context of a customer logged in to their Shopify account, either from a browser or other client \(such as a mobile app\).

The API follows the [JSON API Specification](https://jsonapi.org/), and provide endpoints for retrieving, creating and updating stored payment methods, subscriptions, and individual subscription orders.

A Javascript client library \(Submarine.js\) is provided by Disco Labs to simplify interaction with the Customer API. This library can be imported into an existing theme development workflow, or included via a `<script>` tag.

{% hint style="info" %}
Instructions and code examples for using the Submarine.js client library are available [on Github](https://github.com/discolabs/submarine-js).
{% endhint %}

## Authentication

Because the API is returning sensitive customer information \(a list of their stored payment methods, saved subscriptions, and the contents of those subscriptions\), authentication in required to retrieve or update any customer information.

Requests to the API are authenticated by providing three parameters in the querystring of all HTTP requests:

* `shop` - the Shopify domain of the current store, eg `example.myshopify.com`;
* `timestamp` - a UNIX timestamp of when the request was generated;
* `signature` - a SHA256 HMAC signature generated from the ID of the logged in customer, the `timestamp` value, and a secret key made available to your theme via a shop-level metafield `shop.metafields.submarine.customer_api_secret`.

For Shopify themes, these values should be generated within your Liquid templates and passed to the Javascript code that will be making calls to the Customer API.

For other clients, such as mobile apps, these values should be generated within your application code before making calls.

### Example generation

{% code-tabs %}
{% code-tabs-item title="accounts.liquid" %}
```markup
{% assign api_timestamp = 'now' | date: '%s' %}
{% assign api_data = customer.id | append: ':' | append: api_timestamp %}
{% assign api_signature = api_data | hmac_sha256: shop.metafields.submarine.customer_api_secret %}

<script>
  window.submarine = new Submarine({
    environment: "production",
    authentication: {
      shop: "{{ shop.permanent_domain }}",
      timestamp: "{{ api_timestamp }}",
      signature: "{{ api_signature }}"
    }
  });
</script>
```
{% endcode-tabs-item %}
{% endcode-tabs %}

### Example request

An example authenticated API request to fetch the list of payment methods for the current customer \(with an ID of `82500043234`\) may therefore look like the following:

```text
GET https://submarine.discolabs.com/api/v1/customers/82500043234/payment_methods?shop=example.myshopify.com&timestamp=1556957501&signature=4400f4c50dc953d000a735beb5becd6460141980d3d0d5a207b47ec6b3124f5e
```

{% hint style="info" %}
For brevity, we omit these authentication parameters from the endpoint documentation below, but note that they are required for all requests.
{% endhint %}

## Payment method endpoints

{% api-method method="get" host="https://submarine.discolabs.com" path="/api/v1/customers/{{ customer\_id }}/payment\_methods.json" %}
{% api-method-summary %}
List payment methods
{% endapi-method-summary %}

{% api-method-description %}
Fetch a list of the current customer's active payment methods.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-path-parameters %}
{% api-method-parameter name="customer\_id" type="integer" required=true %}
ID of the currently logged in customer.
{% endapi-method-parameter %}
{% endapi-method-path-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}
Successfully retrieved a list of payment methods.
{% endapi-method-response-example-description %}

```javascript
{
  "data":[
    {
      "id":"75199384",
      "type":"customer_payment_method",
      "attributes":{
        "status":"active",
        "payment_data":{
          "brand":"Visa",
          "last4":"4242",
          "exp_year":2022,
          "exp_month":1,
          "processor":"stripe"
        },
        "payment_method_type":"credit-card",
        "authorized_payment_method_id": 825022
      }
    },
    {
      "id":"75199212",
      "type":"customer_payment_method",
      "attributes":{
        "status":"active",
        "payment_data":{
          "email":"customer@example.com",
          "processor":"braintree"
        },
        "payment_method_type":"paypal",
        "authorized_payment_method_id": 221099
      }
    }
  ]
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

{% api-method method="post" host="https://submarine.discolabs.com" path="/api/v1/customers/{{ customer\_id }}/payment\_methods.json" %}
{% api-method-summary %}
Create new payment method
{% endapi-method-summary %}

{% api-method-description %}
Create a new payment method for the current customer.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-path-parameters %}
{% api-method-parameter name="customer\_id" type="integer" required=true %}
ID of the currently logged in customer.
{% endapi-method-parameter %}
{% endapi-method-path-parameters %}

{% api-method-body-parameters %}
{% api-method-parameter name="payment\_method" type="object" required=true %}
Details of the payment method to create.
{% endapi-method-parameter %}
{% endapi-method-body-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```text

```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

{% api-method method="patch" host="https://submarine.discolabs.com" path="/api/v1/customers/{{ customer\_id }}/payment\_methods/{{ id }}.json" %}
{% api-method-summary %}
Update existing payment method
{% endapi-method-summary %}

{% api-method-description %}
Update an existing payment method belonging to the current customer.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-path-parameters %}
{% api-method-parameter name="customer\_id" type="integer" required=true %}
ID of the currently logged in customer.
{% endapi-method-parameter %}

{% api-method-parameter name="id" type="integer" required=true %}
ID of the payment method to update.
{% endapi-method-parameter %}
{% endapi-method-path-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```text

```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

{% api-method method="delete" host="https://submarine.discolabs.com" path="/api/v1/{{ customer\_id }}/payment\_methods/{{ id }}.json" %}
{% api-method-summary %}
Delete existing payment method
{% endapi-method-summary %}

{% api-method-description %}
Remove an existing payment method belonging to the current customer.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-path-parameters %}
{% api-method-parameter name="customer\_id" type="integer" required=true %}
ID of the currently logged in customer.
{% endapi-method-parameter %}

{% api-method-parameter name="id" type="integer" required=true %}
ID of the payment method to remove.
{% endapi-method-parameter %}
{% endapi-method-path-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```text

```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

## Subscription endpoints

{% api-method method="get" host="https://submarine.discolabs.com" path="/api/v1/customers/{{ customer\_id }}/subscriptions.json" %}
{% api-method-summary %}
List subscriptions
{% endapi-method-summary %}

{% api-method-description %}
Fetch a list of the current customer's active subscriptions
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-path-parameters %}
{% api-method-parameter name="customer\_id" type="integer" required=true %}
ID of the currently logged in customer.
{% endapi-method-parameter %}
{% endapi-method-path-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```text

```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

{% api-method method="post" host="https://submarine.discolabs.com" path="/api/v1/customers/{{ customer\_id }}/subscriptions.json" %}
{% api-method-summary %}
Create new subscription
{% endapi-method-summary %}

{% api-method-description %}
Create a new subscription for the current customer.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-path-parameters %}
{% api-method-parameter name="customer\_id" type="integer" required=true %}
ID of the currently logged in customer.
{% endapi-method-parameter %}
{% endapi-method-path-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```text

```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

{% api-method method="patch" host="https://submarine.discolabs.com" path="/api/v1/customers/{{ customer\_id }}/subscriptions/{{ id }}.json" %}
{% api-method-summary %}
Update existing subscription
{% endapi-method-summary %}

{% api-method-description %}
Update an existing subscription belonging to the current customer.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-path-parameters %}
{% api-method-parameter name="customer\_id" type="integer" required=true %}
ID of the currently logged in customer.
{% endapi-method-parameter %}

{% api-method-parameter name="id" type="integer" required=true %}
ID of the subscription to update.
{% endapi-method-parameter %}
{% endapi-method-path-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```text

```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

{% api-method method="delete" host="https://submarine.discolabs.com" path="/api/v1/{{ customer\_id }}/subscriptions/{{ id }}.json" %}
{% api-method-summary %}
Delete existing subscription
{% endapi-method-summary %}

{% api-method-description %}
Remove an existing subscription belonging to the current customer.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-path-parameters %}
{% api-method-parameter name="customer\_id" type="integer" required=true %}
ID of the currently logged in customer.
{% endapi-method-parameter %}

{% api-method-parameter name="id" type="integer" required=true %}
ID of the subscription to remove.
{% endapi-method-parameter %}
{% endapi-method-path-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```text

```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

## Subscription order endpoints

{% api-method method="get" host="https://submarine.discolabs.com" path="/api/v1/customers/{{ customer\_id }}/subscriptions/{{ subscription\_id }}/subscription\_orders.json" %}
{% api-method-summary %}
List subscription orders
{% endapi-method-summary %}

{% api-method-description %}
Fetch a list of the subscription orders for the given subscription.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-path-parameters %}
{% api-method-parameter name="customer\_id" type="integer" required=true %}
ID of the currently logged in customer.
{% endapi-method-parameter %}

{% api-method-parameter name="subscription\_id" type="integer" required=true %}
ID of the subscription to retrieve orders for.
{% endapi-method-parameter %}
{% endapi-method-path-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```text

```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

{% api-method method="patch" host="https://submarine.discolabs.com" path="/api/v1/customers/{{ customer\_id }}/subscriptions/{{ subscription\_id }}/subscription\_orders/{{ id }}.json" %}
{% api-method-summary %}
Update existing subscription order
{% endapi-method-summary %}

{% api-method-description %}
Update an existing subscription order.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-path-parameters %}
{% api-method-parameter name="customer\_id" type="integer" required=true %}
ID of the currently logged in customer.
{% endapi-method-parameter %}

{% api-method-parameter name="subscription\_id" type="integer" required=true %}
ID of the subscription the subscription order to be updated belongs to.
{% endapi-method-parameter %}

{% api-method-parameter name="id" type="integer" required=true %}
ID of the subscription order to update.
{% endapi-method-parameter %}
{% endapi-method-path-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```text

```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

