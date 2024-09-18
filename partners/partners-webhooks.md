# Webhooks

## Overview
This section explains how partners can register, update, enable, and disable webhooks using the **Partner API**. Webhooks allow the partner to receive real-time notifications when certain events, such as application status changes, occur.

## 1. Registering or Updating a Webhook

- Partners can register or update a webhook by sending a **POST** request to the endpoint `/onboarding/v1/partner/webhooks`.
- The webhook allows Amaiz to notify the partner about changes in customer application statuses (such as **VERIFIED** or **DECLINED**).
- The payload includes:
    - `url`: The URL where the webhook notifications should be sent.
    - `type`: The type of webhook to register. Currently, the only supported value is `APPLICATION_STATUS`.

### Example Webhook Registration Request:

```json
{
  "url": "https://partner-company.com/api/webhooks",
  "type": "APPLICATION_STATUS"
}
```

### Payload Description:

- **url**:
    - A valid URL (up to 2048 characters) where Amaiz will send webhook notifications.
    - Special characters are allowed in the URL.

- **type**:
    - The type of webhook being registered. The only supported value is `APPLICATION_STATUS`, which triggers notifications when the status of a customer application changes (e.g., **VERIFIED**, **DECLINED**).

## 2. Webhook Update

- To update an existing webhook, partners send a **POST** request with the updated `url` or other details to the same endpoint: `/onboarding/v1/partner/webhooks`.
- This action will override the existing webhook registration for the given type (`APPLICATION_STATUS`).

### Example Webhook Update Request:

```json
{
  "url": "https://new-url-partner.com/webhooks",
  "type": "APPLICATION_STATUS"
}
```

## 3. Enabling or Disabling a Webhook

- Partners can **enable** or **disable** a registered webhook using the following **PATCH** endpoints:
    - Enable: `/onboarding/v1/partner/webhooks/enable`
    - Disable: `/onboarding/v1/partner/webhooks/disable`

### Example Webhook Enable Request:

```json
{
  "type": "APPLICATION_STATUS"
}
```

## 4. Webhook Notification Payload

Once the webhook is registered, Amaiz will trigger the webhook when the event occurs (for example, when the customer application is **VERIFIED** or **DECLINED**). The webhook notification payload looks like this:

### Example Payload for Verified Customer:

```json
{
  "id": "notif-789",
  "partnerId": "partner-123",
  "applicationId": "app-456",
  "status": "VERIFIED",
  "wallet": {
    "id": "wallet-789",
    "currencyCode": "EUR"
  }
}
```

### Example Payload for Declined Customer:

```json
{
  "id": "notif-789",
  "partnerId": "partner-123",
  "applicationId": "app-456",
  "status": "DECLINED"
}
```

### Payload Description:

- **id**:
    - Unique identifier for the webhook notification.

- **partnerId**:
    - The ID of the partner (the company) that registered the customer.

- **applicationId**:
    - The ID of the customer’s application that has been submitted by the partner.

- **status**:
    - The current status of the application. It can be either **VERIFIED** (approved by compliance) or **DECLINED** (rejected by compliance).

- **wallet**:
    - If the application is **VERIFIED**, this field contains details about the wallet created for the customer. The `wallet.id` is the 12-digit wallet ID, and `currencyCode` specifies the currency (e.g., EUR).
    - If the application is **DECLINED**, this field will be `null`.

## Summary

- Partners can register and update webhooks via the **POST** request to `/onboarding/v1/partner/webhooks`, providing a `url` and `type` (`APPLICATION_STATUS`).
- Webhooks can be enabled or disabled via the **PATCH** requests to `/onboarding/v1/partner/webhooks/enable` or `/onboarding/v1/partner/webhooks/disable`.
- When a customer application’s status changes, Amaiz will trigger the webhook and send a notification to the provided URL.
- Notifications include details such as the application status and wallet information (if applicable).

---
**UML Diagram**

![img.png](../img/partner-webhook.png)