---
description: Documentation for Submarine's Shopify Flow integration.
---

# Shopify Flow Integration

Rather than building functionality like email notifications, webhook triggers, or custom app-to-app integrations into the platform, Submarine instead offers a comprehensive [Shopify Flow](https://apps.shopify.com/flow) integration. This gives merchants the flexibility to connect Submarine to their existing systems and use Flow to build custom workflow logic - out of the box.

For example, a merchant already managing customer email templates in Klaviyo can connect the **Upcoming Subscription Order** trigger from Submarine to the **Event** action in Klaviyo, and deliver an email notification to the customer ahead of their next subscription order being placed. This helps keep customer data and custom email template management in the one place, rather than siloing it inside Submarine.

Going the other way, a merchant using Loyalty Lion may want to reward loyal customers with credit on their next subscription order. To do this, they could connect the **Customer moved into “Loyal” segment** trigger from Loyalty Lion to the **Adjust subscription credit** action \(beta\) in Submarine.

## Flow triggers

### Payment action failed

A payment action \(for example, authorisation or capture\) failed to complete successfully.

#### Properties

| Name | Type | Description |
| :--- | :--- | :--- |
| Customer email | email | The email address of the customer. |
| Payment amount | number | The amount of the attempted payment \(in cents\). |
| Action URL | url | A URL the customer can follow to take action to address the payment issue. |
| Error message | string | Any error message returned from the payment processor. |

### Upcoming subscription order

A subscription order is upcoming for a customer.

#### Properties

| Name | Type | Description |
| :--- | :--- | :--- |
| Customer email | email | The email address of the customer. |
| Scheduled at | string | The date and time the order is scheduled to be placed \(in ISO8601 format\). |
| Total price | number | The total price of the upcoming order \(in cents\). |
| Action URL | url | A URL the customer can follow to review their order. |

### Customer first subscription

A customer created their first subscription.

#### Properties

| Name | Type | Description |
| :--- | :--- | :--- |
| Customer email | email | The email address of the customer. |

### Upcoming card expiry

A stored credit card is expiring soon.

#### Properties

| Name | Type | Description |
| :--- | :--- | :--- |
| Customer | customer |  |
| Type of card | string | The brand of credit card \(e.g. "visa"\). |
| Last four digits | string | Last four digits of the credit card. |
| Expiry date | string | The expiry date of the credit card \(in ISO8601 format\). |
| Action URL | url | A URL the customer can follow to update their payment details. |

### Items removed from subscription \(beta\)

One or more items have been removed from a subscription, independently of any customer activity \(e.g. a subscription product has been unpublished or deleted\).

| Name | Type | Description |
| :--- | :--- | :--- |
| Customer | customer |  |
| Reason | string | The reason for the removal. |
| Total price | number | The revised price of the subscription order \(in cents\). |
| Variant IDs | string | A comma-separated list of Shopify variant IDs that were removed. |
| Action URL | url | A URL the customer can follow to review the subscription. |

### Subscription cancelled \(beta\)

A subscription has been cancelled by Submarine, independently of any customer activity \(e.g. all subscription products have been unpublished or deleted\).

| Name | Type | Description |
| :--- | :--- | :--- |
| Customer | customer |  |
| Reason | string | The reason for the cancellation. |
| Action URL | url | A URL the customer can follow to review the subscription. |

### Subscription status changed \(beta\)

The status of a subscription has changed, by the customer, the merchant or Submarine.

| Name | Type | Description |
| :--- | :--- | :--- |
| Customer | customer |  |
| Subscription ID | string | The Submarine subscription ID. |
| Reason | string | The reason for the change in status. |
| Previous status | string | The previous status of the subscription, e.g. 'active'. |
| Updated status | string | The updated status of the subscription, e.g. 'paused'. |
| Active subscription count | number | The current number of active subscriptions the customer has. |
| Action URL | url | A URL the customer can follow to review the subscription. |

## Flow actions

### Update customer subscription status

Set the status of all of a customer's active subscriptions.

#### Properties

| Name | Type | Description |
| :--- | :--- | :--- |
| customer\_email | email | The email address of the customer. |
| subscription\_status | short\_text | Can be either "paused" or "cancelled". |

### Adjust subscription credit \(beta\)

Adjust the amount of subscription credit for a customer. Cannot be adjusted below zero.

#### Properties

| Name | Type | Description |
| :--- | :--- | :--- |
| customer\_email | email | The email address of the customer. |
| adjustment\_value | number | The amount to adjust the customer's subscription credit by \(in cents\). Positive values add to the customer's credit balance, negative values deduct from the customer's credit balance. An adjustment that would result in a negative balance will be applied but the balance will be set to zero. |

