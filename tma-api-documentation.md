# Transfer Management API Documentation

## Overview

This API enables clients to manage and track their transfers and transaction history without a user interface. It allows for the automation of transfer processes and efficient monitoring of transactions.

### API Request Authorization
- **Requirement**: Include the `X-API-ID` and `X-API-KEY` in the HTTP header for each API call.
    - `X-API-ID`: The user's API ID, provided after onboarding.
    - `X-API-KEY`: The user's API Key, provided after onboarding.
- **Invalid Access**: If the provided `X-API-ID` or `X-API-KEY` is incorrect, the system denies the request.

## Endpoints

### 1. Internal Transfers

#### Execute Transfer
- **Path**: `/tma/v1/transfer/internal/send`
- **Method**: POST
- **Description**: Executes an internal transfer between wallets.

**Request Example**:
  ```json
  {
      "amount": "10",
      "description": "internal",
      "destWalletSerial": "315912227851",
      "srcWalletSerial": "252959811664"
  }
  ```
#### `srcWalletSerial`
- **Type:** string
- **Description:** Serial number of the source wallet.

#### `destWalletSerial`
- **Type:** string
- **Description:** Serial number of the destination wallet.

#### `amount`
- **Type:** string
- **Description:** Amount to transfer, expressed as a string.

#### `description`
- **Type:** string
- **Description:** Description of the transfer.

**Response Example**:
  ```json
  {
      "data": {
        "id": "0b54c786-986b-4d96-8bb8-30400a764bb0",
        "type": "Self transfer",
        "createdAt": "2023-11-23T12:04:59.055Z",
        "updatedAt": "2023-11-23T12:04:58.941Z",
        "status": "processed",
        "direction": "OUT",
        "amount": "10.00",
        "commission": "0.00",
        "currency": "EUR",
        "description": "Outgoing transfer to wallet 315912227851",
        "fromWalletSerial": "252959811664",
        "toWalletSerial": "315912227851"
      },
      "status": "OK",
      "date": "2023-11-23T12:04:59.381324639Z"
}
  ```
#### `status`
- **Type:** string
- **Description:** Status of the transfer (e.g., "OK").

#### `date`
- **Type:** string
- **Description:** Date and time of the response.

#### `data`
- **Type:** object
- **Description:** Detailed information about the transfer.
    - `id`: Unique identifier for the transfer.
    - `type`: Type of the transfer, such as "Self transfer".
    - `createdAt`: Timestamp of when the transfer was created.
    - `updatedAt`: Timestamp of the last update to the transfer.
    - `status`: Status of the transfer, such as "processed".
    - `direction`: Direction of the transfer, such as "IN" or "OUT".
    - `amount`: Amount of the transfer.
    - `commission`: Commission amount for the transfer.
    - `currency`: Currency of the transfer, such as "EUR".
    - `description`: Description of the transfer.
    - `fromWalletSerial`: Source wallet serial number.
    - `toWalletSerial`: Destination wallet serial number.
  

#### Calculate Transfer
- **Path**: `/tma/v1/transfer/internal/calculate`
- **Method**: POST
- **Description**: Calculates the commission for an internal transfer.

**Request Example**:
  ```json
  {
      "amount": "1000",
      "description": "internal",
      "destWalletSerial": "568521240916",
      "srcWalletSerial": "252959811664"
  }
  ```
#### `srcWalletSerial`
- **Type:** string
- **Description:** Serial number of the source wallet.

#### `destWalletSerial`
- **Type:** string
- **Description:** Serial number of the destination wallet.

#### `amount`
- **Type:** string
- **Description:** Amount to transfer, expressed as a string.

**Response Example**:
  ```json
  {
      "data": {
        "amount": "1000.00",
        "destAmount": "1000.00",
        "commission": "12.00"
      },
      "status": "OK",
      "date": "2023-11-23T12:13:02.603036352Z"
}
  ```
#### `status`
- **Type:** string
- **Description:** Status of the calculation (e.g., "OK").

#### `date`
- **Type:** string
- **Description:** Date and time of the response.

#### `data`
- **Type:** object
- **Description:** Result of the calculation.
    - `amount`: Requested transfer amount.
    - `destAmount`: Amount to be received after commission deduction.
    - `commission`: Commission amount.

### 2. External Transfers

#### Execute External Transfer
- **Path**: `/tma/v1/transfer/external/send`
- **Method**: POST
- **Description**: Executes an external transfer from a wallet.

**Request Example**:
  ```json
  {
      "walletSerial": "252959811664",
      "transferType": "SEPA",
      "amount": "100",
      "payerData": {
        "paymentDetails": "test details",
        "isBusiness": "false",
        "beneficiaryName": "EPAM",
        "country": "CY",
        "iban": "CY89375238572039523454",
        "bic": "MMMMMM32455",
        "city": "Larnaca",
        "postalCode": "3035",
        "street1": "Sefery 7"
      }
  }
  ```
#### `walletSerial`
- **Type:** string
- **Description:** Serial number of the wallet initiating the transfer.

#### `transferType`
- **Type:** string
- **Description:** Type of the transfer, e.g., "international."

#### `amount`
- **Type:** string
- **Description:** Amount to transfer, expressed as a string.

#### `payerData`
- **Type:** object
- **Description:** Contains payer's information. The specific fields in payerData can be constructed using the `/tma/v1/transfer/external/payer-fields` endpoint, which provides a list of mandatory and optional fields, along with validation rules. Each field in payerData should adhere to these specifications.
    
**Response Example**:
  ```json
  {
      "data": {
        "id": "86428f00-72dd-493a-89af-d6542abea249",
        "type": "Outgoing SEPA",
        "createdAt": "2023-11-23T12:00:10.373Z",
        "updatedAt": "2023-11-23T12:00:10.099Z",
        "status": "pending",
        "direction": "OUT",
        "amount": "100.00",
        "commission": "11.00",
        "currency": "EUR",
        "description": "Outgoing transfer to EPAM",
        "walletSerial": "252959811664",
        "payerData": {
          "country": "CY",
          "city": "Larnaca",
          "isBusiness": "false",
          "beneficiaryName": "EPAM",
          "iban": "CY89375238572039523454",
          "postalCode": "3035",
          "street1": "Sefery 7",
          "paymentDetails": "test details",
          "bic": "MMMMMM32455"
        }
      },
      "status": "OK",
      "date": "2023-11-23T12:00:11.392124507Z"
 }
  ```
#### `status`
- **Type:** string
- **Description:** Status of the request (e.g., "success").

#### `date`
- **Type:** string
- **Description:** Date and time of the response.

#### `data`
- **Type:** object
- **Description:** Detailed information about the executed transfer.
    - `id`: Unique identifier for the transfer.
    - `type`: Type of the transfer ("external").
    - `status`: Status of the transfer, such as "completed".
    - `createdAt`: Timestamp of when the transfer was created.
    - `direction`: Direction of the transfer (e.g., "outgoing").
    - `amount`: Amount of the transfer.
    - `commission`: Commission or fees applied to the transfer.
    - `currency`: Currency of the transfer, such as "EUR".
    - `description`: Description of the transfer.
    - `walletSerial`: Serial number of the sender wallet.
    - `payerData`: Payer information as provided in the request.

    
#### Get Payer Fields for External Transfer
- **Path**: `/tma/v1/transfer/external/payer-fields`
- **Method**: POST
- **Description**: Retrieves the payer fields required for an external transfer.

**Request Example**:
```json
{
  "walletSerial": "252959811664",
  "transferType": "SEPA"
}
```

**Response Example**:
  ```json
  {
      "data": [
        {
          "name": "paymentDetails",
          "optional": false,
          "constraints": [
            {
              "regex": "^[a-zA-Z0-9'\\-\\s\\.]*$",
              "errorMessageKey": "validator.general.invalid",
              "type": "Regex"
            },
            {
              "to": 140,
              "errorMessageKey": "validator.general.length",
              "type": "Length"
            }
          ]
        },
        {
          "name": "isBusiness",
          "optional": false,
          "constraints": []
        },
        {
          "name": "beneficiaryName",
          "optional": false,
          "constraints": [
            {
              "regex": "^[a-zA-Z0-9'\\-\\s\\.]*$",
              "errorMessageKey": "validator.general.invalid",
              "type": "Regex"
            },
            {
              "to": 50,
              "errorMessageKey": "validator.general.length",
              "type": "Length"
            }
          ]
        },
        {
          "name": "country",
          "optional": false,
          "constraints": [
            {
              "options": [
                {
                  "label": "Austria",
                  "value": "AT"
                },
                {
                  "label": "United Kingdom",
                  "value": "GB"
                }
              ],
              "errorMessageKey": "validator.general.select_invalid",
              "type": "Select"
            }
          ]
        },
        {
          "name": "iban",
          "optional": false,
          "constraints": [
            {
              "supportedCountries": [
                "DE", "NO", "BE", "FI", "PT"
              ],
              "regex": "^[A-Z]{2,2}[0-9]{2,2}[a-zA-Z0-9]{12,30}$",
              "errorMessageKey": "validator.general.invalid",
              "type": "IbanCode"
            }
          ]
        },
        {
          "name": "bic",
          "optional": false,
          "constraints": [
            {
              "regex": "^([A-Z]{6}[A-Z2-9][A-NP-Z1-9])(X{3}|[A-WY-Z0-9][A-Z0-9]{2})?$",
              "errorMessageKey": "validator.general.invalid",
              "type": "BicCode"
            }
          ]
        },
        {
          "name": "city",
          "optional": true,
          "constraints": [
            {
              "to": 100,
              "errorMessageKey": "validator.general.length",
              "type": "Length"
            }
          ]
        },
        {
          "name": "postalCode",
          "optional": true,
          "constraints": [
            {
              "regex": "^[a-zA-Z0-9'\\-\\s\\.]*$",
              "errorMessageKey": "validator.general.invalid",
              "type": "Regex"
            },
            {
              "to": 50,
              "errorMessageKey": "validator.general.length",
              "type": "Length"
            }
          ]
        },
        {
          "name": "street1",
          "optional": true,
          "constraints": [
            {
              "to": 500,
              "errorMessageKey": "validator.general.length",
              "type": "Length"
            }
          ]
        }
      ],
      "status": "OK",
      "date": "2023-11-23T12:00:37.658799348Z"
}
  ```

#### Calculate External Transfer
- **Path**: `/tma/v1/transfer/external/calculate`
- **Method**: POST
- **Description**: Calculates the details for an external transfer.

**Request Example**:
  ```json
  {
      "amount": "100",
      "walletSerial": "252959811664",
      "transferType": "SEPA"
  }
  ```
#### `walletSerial`
- **Type:** string
- **Description:** Serial number of the wallet from which the transfer will be made.

#### `transferType`
- **Type:** string
- **Description:** Type of the external transfer, e.g., "international."

#### `amount`
- **Type:** string
- **Description:** The amount intended to transfer, expressed as a string.

**Response Example**:
  ```json
  {
      "data": {
        "amount": "100.00",
        "destAmount": "100.00",
        "commission": "11.00"
      },
      "status": "OK",
      "date": "2023-11-23T12:1538.168319562Z"
 }
  ```

#### `status`
- **Type:** string
- **Description:** Status of the calculation (e.g., "success").

#### `date`
- **Type:** string
- **Description:** Date and time of the response.

#### `data`
- **Type:** object
- **Description:** Detailed information about the calculated result.
    - `amount`: The original amount requested for transfer.
    - `destAmount`: The amount that will be received after deduction of fees and conversion (if applicable).
    - `commission`: The commission or fee amount for the transfer.


### 3. Wallets

#### Get All Wallets
- **Path**: `/tma/v1/wallets`
- **Method**: GET
- **Description**: Retrieves a list of all user wallets.

#### Get Transactions for a Wallet
- **Path**: `/tma/v1/wallets/{serial}/transactions`
- **Method**: GET
- **Description**: Retrieves transactions for a specific wallet, identified by its serial.

### Pagination Support
Endpoints returning multiple resources support pagination. Clients should use the pageable JSON parameter to specify pagination options.