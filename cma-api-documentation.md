# Card Management API Documentation

## Overview
The Card Management API allows clients to interact with card-related services, including webhooks for card events, managing card details, transactions, security information, and balances. It's designed to offer a comprehensive suite of functionalities for digital banking platforms that need to integrate card management features.

### API Request Authorization
- **Requirement**: Include the `X-API-ID` and `X-API-KEY` in the HTTP header for each API call.
    - `X-API-ID`: The user's API ID, provided after onboarding.
    - `X-API-KEY`: The user's API Key, provided after onboarding.
- **Invalid Access**: If the provided `X-API-ID` or `X-API-KEY` is incorrect, the system denies the request.

## Endpoints

### 1. Webhooks

#### Retrieve Webhook Information
- **Path:** `/cma/v1/webhooks`
- **Method:** GET
- **Description:** Fetches the configuration details of the existing webhook.

#### Configure Webhook
- **Path:** `/cma/v1/webhooks`
- **Method:** POST
- **Description:** Sets or updates a webhook's configuration parameters.

### 2. Card Management

#### List All Cards
- **Path:** `/cma/v1/cards`
- **Method:** GET
- **Description:** Retrieves a list of all registered cards with pagination support.

#### Issue New Card
- **Path:** `/cma/v1/cards`
- **Method:** POST
- **Description:** Initiates the creation of a new card.

#### Withdraw Funds
- **Path:** `/cma/v1/cards/{cardId}/withdraw`
- **Method:** POST
- **Description:** Executes a withdrawal transaction from the specified card.

#### Top-up Card
- **Path:** `/cma/v1/cards/{cardId}/topup`
- **Method:** POST
- **Description:** Performs a top-up operation to add funds to the specified card.

#### Access Card Details
- **Path:** `/cma/v1/cards/{cardId}`
- **Method:** GET
- **Description:** Obtains detailed information for a specific card.

#### Modify Card Status
- **Path:** `/cma/v1/cards/{cardId}`
- **Method:** PATCH
- **Description:** Alters the status of a card (e.g., active, frozen, blocked).

#### View Card Transactions
- **Path:** `/cma/v1/cards/{cardId}/transactions`
- **Method:** GET
- **Description:** Lists the transaction history for a particular card.

#### Retrieve Card Security Information
- **Path:** `/cma/v1/cards/{cardId}/security`
- **Method:** GET
- **Description:** Fetches security-related details for a card, such as PIN or CVV information.

#### Check Card Balance
- **Path:** `/cma/v1/cards/{cardId}/balance`
- **Method:** GET
- **Description:** Displays the current balance of the specified card.

### Pagination Support
Endpoints returning multiple resources support pagination. Clients should use the pageable JSON parameter to specify pagination options.
