# Understanding Webhooks in the Card Management API

## Purpose of Webhooks
Webhooks in the Card Management API play a critical role in the external authorization process for card transactions. Their primary purpose is to facilitate real-time decision-making for approving or declining card authorizations. When a card transaction occurs, the API sends a webhook notification to an external system, which then evaluates the transaction based on predefined criteria or rules.

## Setting Up Webhooks
To effectively use webhooks with the Card Management API, follow these steps:
1. **Configure the Webhook URL**: Configure the Webhook URL: Utilize the endpoint **/cma/v1/webhooks** to specify the URL where you want the webhook notifications to be delivered.
2. **Receive Notifications**: Once configured, the API will send HTTP POST requests with JSON payloads to your specified URL whenever the subscribed events occur.

## Webhook Payload Structure

When the Card Management API triggers a webhook event, it sends a JSON payload to the configured webhook URL. The payload contains detailed information about the transaction or event.

```json
{
  "id": "string",
  "card": {
    "serial": "string"
  },
  "amount": number,
  "created": long,
  "currency": "string",
  "merchant": {
    "id": "string",
    "name": "string",
    "mccCode": "string",
    "country": "string",
    "posTerminalId": "string"
  },
  "transactionAmount": number,
  "transactionCurrency": "string",
  "transactionType": "string",
  "authorizationType": "string"
} 
```

#### `id`
- **Type:** string
- **Description:** Unique identifier for the transaction or event.

#### `card`
- **Type:** object
    - `serial`
        - **Type:** string
        - **Description:** Serial number of the card involved in the transaction.

#### `amount`
- **Type:** number
- **Description:** The amount involved in the transaction.

#### `created`
- **Type:** long
- **Description:** Timestamp of when the transaction was created, in milliseconds since Unix epoch.

#### `currency`
- **Type:** string
- **Description:** Currency of the transaction.

#### `merchant`
- **Type:** object
    - `id`
        - **Type:** string
        - **Description:** Unique identifier for the merchant.
    - `name`
        - **Type:** string
        - **Description:** Name of the merchant, including location details.
    - `mccCode`
        - **Type:** string
        - **Description:** Merchant Category Code.
    - `country`
        - **Type:** string
        - **Description:** Country where the merchant is located.
    - `posTerminalId`
        - **Type:** string
        - **Description:** ID of the Point of Sale terminal used.

#### `transactionAmount`
- **Type:** number
- **Description:** Actual transaction amount.

#### `transactionCurrency`
- **Type:** string
- **Description:** Currency used for the transaction.

#### `transactionType`
- **Type:** string
- **Description:** Type of transaction (e.g., "POS").

#### `authorizationType`
- **Type:** string
- **Description:** Type of authorization for the transaction (e.g., "NormalAuthorize").


Expected Response Format:
```json
{
  "status": "APPROVE"
}
```
```json
{
  "status": "DECLINE"
}
```

Upon receiving the payload, the client's webhook endpoint should process the transaction details and respond with an authorization decision. The response must be a JSON object with a status field, indicating either APPROVE or DECLINE.
A transaction is approved if the HTTP response contains `"status": "APPROVE"`. It is declined if the response contains `"status": "DECLINE", if the response status code is not 200, or if there are connection issues.


## Security Considerations
When implementing webhooks, consider the following security measures:
- **Validate Incoming Requests**: Ensure that the incoming webhook requests are indeed from the trusted API source. This can typically be done using secret tokens or signature headers.
- **Use HTTPS**: Always use HTTPS for your webhook URLs to secure the data in transit against eavesdropping or man-in-the-middle attacks.
- **Retry Mechanism**: Implement a retry mechanism for handling scenarios where webhook delivery fails, ensuring that no critical information is missed due to temporary issues.

## Best Practices
Adhering to best practices ensures reliable and efficient handling of webhook notifications:
- **Responsive Endpoint**: Make sure that your webhook endpoint is capable of handling requests and responding promptly to avoid timeouts.
- **Logging**: Maintain logs of webhook activity. This is crucial for debugging and auditing purposes.
- **Duplicate Handling**: Sometimes, webhooks might be called more than once for the same event. Design your system to gracefully handle duplicate webhook calls.
