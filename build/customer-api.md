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

{% code title="accounts.liquid" %}
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
{% endcode %}

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
  "data": [
    {
      "id": "75199384",
      "type": "customer_payment_method",
      "attributes": {
        "status": "active",
        "payment_data": {
          "brand": "Visa",
          "last4": "4242",
          "exp_year": 2022,
          "exp_month": 1,
          "processor": "stripe"
        },
        "payment_method_type": "credit-card",
        "authorized_payment_method_id": 825022
      }
    },
    {
      "id": "75199212",
      "type": "customer_payment_method",
      "attributes": {
        "status": "active",
        "payment_data": {
          "email": "customer@example.com",
          "processor": "braintree"
        },
        "payment_method_type": "paypal",
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
Create a new payment method for the current customer. Body parameters should be passed as a JSON object wrapped in a `payment_method` namespace.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-path-parameters %}
{% api-method-parameter name="customer\_id" type="integer" required=true %}
ID of the currently logged in customer.
{% endapi-method-parameter %}
{% endapi-method-path-parameters %}

{% api-method-body-parameters %}
{% api-method-parameter required=true name="payment\_token" type="string" %}
A payment token retrieved from a payment processor's JS SDK.
{% endapi-method-parameter %}

{% api-method-parameter name="payment\_method\_type" type="string" required=true %}
The type of payment method. One of `credit-card`, `paypal` or `sepa`.
{% endapi-method-parameter %}

{% api-method-parameter name="payment\_processor" type="string" required=true %}
The payment processor. Either `stripe` or `braintree`.
{% endapi-method-parameter %}

{% api-method-parameter name="status" type="string" required=true %}
Status of the new method. Should be `active`.
{% endapi-method-parameter %}
{% endapi-method-body-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```javascript
{  
  "data": {  
    "id": "5208432",
    "type": "customer_payment_method",
    "attributes": {  
      "status": "active",
      "payment_data": {
        "brand": "Visa",
        "last4": "4242",
        "exp_month": 1,
        "exp_year": 2022,
        "processor": "stripe"
      },
      "payment_method_type": "credit-card",
      "authorized_payment_method_id": 9012424
    }
  }
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

{% api-method method="delete" host="https://submarine.discolabs.com" path="/api/v1/customers/{{ customer\_id }}/payment\_methods/{{ id }}.json" %}
{% api-method-summary %}
Remove existing payment method
{% endapi-method-summary %}

{% api-method-description %}
Remove \(disable\) an existing payment method belonging to the current customer.
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

```javascript
{  
  "data": {  
    "id": "5208432",
    "type": "customer_payment_method",
    "attributes": {  
      "status": "disabled",
      "payment_data": {  
        "brand": "Visa",
        "last4": "1111",
        "exp_year": 2020,
        "exp_month": 1,
        "processor": "stripe"
      },
      "payment_method_type": "credit-card",
      "authorized_payment_method_id": 9012424
    }
  }
}
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

```javascript
{
  "data": [
    {
      "id": "63594867",
      "type": "subscription",
      "attributes": {
        "status": "active",
        "created_at": "2019-05-01T05:14:27.441Z",
        "cancelled_at": null,
        "paused_at": null,
        "note": "",
        "customer_name": "Jane Doe",
        "customer_email": "jane@example.com",
        "billing_address": {
          "zip": "3000",
          "city": "Melbourne",
          "name": "Jane Doe",
          "phone": null,
          "company": null,
          "country": "Australia",
          "address1": "100 Main Street",
          "address2": "",
          "latitude": null,
          "province": "Victoria",
          "last_name": "Doe",
          "longitude": null,
          "first_name": "Jane",
          "country_code": "AU",
          "province_code": "VIC"
        },
        "frequency": "42_days",
        "frequency_human": "Every 6 weeks",
        "line_items": {
          "data": [
            {
              "id": "40850",
              "type": "subscription_line_item",
              "attributes": {
                "product_id": 1506703278149,
                "variant_id": 13587185303621,
                "quantity": 5,
                "price": "8.90",
                "properties": null,
                "title": "Beauty Berry Porridge"
              }
            },
            {
              "id": "40851",
              "type": "subscription_line_item",
              "attributes": {
                "product_id": 1506738864197,
                "variant_id": 13587544539205,
                "quantity": 1,
                "price": "15.90",
                "properties": [
                  {
                    "name": "_applied_subscription_discount",
                    "value": "135"
                  }
                ],
                "title": "Kids Blendies"
              }
            }
          ]
        },
        "shipping_method": {
          "data": {
            "id": "84573",
            "type": "subscription_shipping_method",
            "attributes": {
              "note": null,
              "shipping_rates": [
                {
                  "id": 851467108421,
                  "code": "Standard Shipping (3-6 days)",
                  "phone": null,
                  "price": "0.00",
                  "title": "Standard Shipping (3-6 days)",
                  "source": "shopify",
                  "discounted_price": "0.00"
                }
              ],
              "shipping_address": {
                "first_name": "Jane",
                "last_name": "Doe",
                "address1": "100 Main Street",
                "address2": "",
                "city": "Melbourne",
                "zip": "3000",
                "province": "Victoria",
                "province_code": "VIC",
                "country": "Australia"
              }
            }
          }
        },
        "payment_method": {
          "data": {
            "id": "349580",
            "type": "customer_payment_method",
            "attributes": {
              "status": "active",
              "payment_data": {
                "brand": "Visa",
                "last4": "4242",
                "exp_year": 2024,
                "exp_month": 4,
                "processor": "stripe"
              },
              "payment_method_type": "credit-card",
              "authorized_payment_method_id": 235252
            }
          }
        },
        "next_scheduled_order": {
          "data": {
            "id": "12521",
            "type": "subscription_order",
            "attributes": {
              "status": "scheduled",
              "shipping_rate": {},
              "scheduled_at": "2019-05-18T00:00:00.000Z",
              "processed_at": null,
              "skipped_at": null,
              "cancelled_at": null,
              "order_id": null,
              "sequential_id": 2,
              "shopify_url": "#",
              "order_line_items": {
                "data": [
                  {
                    "id": "35625",
                    "type": "subscription_order_line_item",
                    "attributes": {
                      "subscription_order_id": 12521,
                      "product_id": 1506703278149,
                      "variant_id": 13587185303621,
                      "quantity": 5,
                      "price": "8.90",
                      "properties": []
                    }
                  },
                  {
                    "id": "35624",
                    "type": "subscription_order_line_item",
                    "attributes": {
                      "subscription_order_id": 12521,
                      "product_id": 1506738864197,
                      "variant_id": 13587544539205,
                      "quantity": 1,
                      "price": "15.90",
                      "properties": [
                        {
                          "name": "_applied_subscription_discount",
                          "value": "135"
                        }
                      ]
                    }
                  }
                ]
              }
            }
          }
        }
      }
    }
  ]
}
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

{% api-method-body-parameters %}
{% api-method-parameter name="subscription" type="object" required=true %}
An object containing the updates to apply.
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

{% api-method method="post" host="" path="/api/v1/customers/{{ customer\_id }}/subscriptions/bulk\_update.json" %}
{% api-method-summary %}
Bulk update existing subscriptions
{% endapi-method-summary %}

{% api-method-description %}
Update multiple existing subscriptions for the current customer in a single API call. Currently, only the payment method linked to the subscription can be updated in bulk.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-path-parameters %}
{% api-method-parameter name="customer\_id" type="integer" required=true %}
ID of the currently logged in customer.
{% endapi-method-parameter %}
{% endapi-method-path-parameters %}

{% api-method-body-parameters %}
{% api-method-parameter name="bulk\_update" type="object" required=true %}
An object containing details of the bulk update to be performed. The `bulk_update` object has two attributes: `subscription_ids`, a list of the subscriptions that should be updated, and `subscription`, which is an object containing the updates to apply.
{% endapi-method-parameter %}
{% endapi-method-body-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```
{
  "data": {
    "id": "816b5f28-0a79-42a0-a69e-5ae37d06821c",
    "type": "bulk_update_subscriptions_result",
    "attributes": {
      "successes": {
        "data": [
          ...a list of updated subscription objects...
        ]
      },
      "failures": {
        "data": [
          ...a list of subscription objects that failed to update...
        ]
      }
    }
  }  
}        
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

{% api-method method="delete" host="https://submarine.discolabs.com" path="/api/v1/customers/{{ customer\_id }}/subscriptions/{{ id }}.json" %}
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

