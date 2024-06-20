# Match-Trade API Documentation

## Table of Contents
- [Introduction](#introduction)
- [Authentication](#authentication)
- [gRPC](#grpc)
- [Errors](#errors)
- [Examples](#examples)

### Branches, Offers, Roles
- [Get Branches](#get-branches)
- [Get Offers](#get-offers)
- [Get Roles](#get-roles)

### Accounts
- [Get Accounts](#get-accounts)
- [Get Account by Email](#get-account-by-email)
- [Get Account by UUID](#get-account-by-uuid)
- [Get Account's Timeline Events](#get-accounts-timeline-events)
- [Create Account](#create-account)
- [Update Account Info](#update-account-info)
- [Change Account's Password](#change-accounts-password)

### Trading Accounts
- [Get Trading Accounts](#get-trading-accounts)
- [Get Trading Account by Login](#get-trading-account-by-login)
- [Create New Trading Account](#create-new-trading-account)

### Payments
- [Get Payment Gateways](#get-payment-gateways)
- [Get Deposits](#get-deposits)
- [Get Withdrawals](#get-withdrawals)
- [Manual Deposit](#manual-deposit)
- [Manual Withdrawal](#manual-withdrawal)
- [Credit In](#credit-in)
- [Credit Out](#credit-out)

### Trading
- [Get Symbols](#get-symbols)
- [Open Position](#open-position)
- [Edit Position](#edit-position)
- [Close Position](#close-position)

### Trading Data
- [Get Open Positions](#get-open-positions)
- [Get Closed Positions](#get-closed-positions)
- [Get Active Orders](#get-active-orders)
- [Get Ledgers](#get-ledgers)

### Candles
- [Get Candles](#get-candles)

# Introduction

The Broker API is a versatile API platform designed for various integrations, enabling streamlined management and interaction with multiple services through a single access point. This API supports REST and gRPC protocols, providing flexibility in integration and ensuring compatibility with diverse client systems. It facilitates secure, efficient administrative operations and service management, making it an essential tool for clients looking to optimize their operational workflows.

In our system, each broker has a unique `partnerID` assigned. Every operation via this API is performed within that particular `partnerID`. Since we encode your `partnerID` in your Authorization token, you don't have to consider this parameter when sending requests, but you will see this parameter across multiple different endpoints. For the Sandbox environment, `partnerID = 0` and is shared between every account in the system. It means you will have access to test data added by other integrations.

Server time is GMT (UTC+0).

All time-related fields accept and return values according to the RFC 3339 standard. For example `2024-01-13T09:20:04.651Z`.

The request limit is 500 requests/minute.

## Authentication

Our system implements a security mechanism that utilizes token-based authorization to ensure secure resource access. The authentication process involves using an Authorization header, following the token scheme. This section outlines the steps required for obtaining and using the authentication token to access protected resources.

### Obtaining the Token

The authentication token is obtained from our Customer Relationship Management (CRM) system. You must first authenticate yourself within the CRM to receive a token. This token acts as a digital key, granting access to the system's resources for the duration of the token's validity. Tokens are valid until revoked.

### Using the Token for Access

Once the token is obtained, it must be included in the request to access secured resources within our system. The token is added to the HTTP request's header as follows:



### Incorporating Authorization in gRPC Requests

In gRPC, the traditional concept of HTTP headers is abstracted through metadata, allowing for the transmission of key-value pairs alongside RPC calls. To secure gRPC services with token-based authentication, the Authorization metadata must be included with each call. This process is similar to using the Authorization header in HTTP requests but is adapted to fit the gRPC framework.

#### Adding the Authorization Metadata

To include an authentication token in a gRPC request, the token must be added to the call's metadata using the `Authorization` key, followed by the token value. This ensures that the server side of the gRPC service can authenticate the request by validating the token.

#### Code Example (Python)

Below is a generic example of how to add the Authorization metadata in a gRPC client application. The implementation may vary depending on the programming language and gRPC library used.

```python
import grpc

# Create a gRPC channel
channel = grpc.insecure_channel('your_grpc_service_endpoint')

# Prepare metadata with the Authorization token
metadata = [('Authorization', '<Your_Token_Here>')]

# Create a stub (client)
stub = YourServiceStub(channel)

# Make a call with the Authorization metadata
response = stub.YourRpcMethod(request, metadata=metadata)
```

Replace <Your_Token_Here> with the actual token obtained from your CRM system, and adjust YourServiceStub and YourRpcMethod to match your gRPC service definition.

## gRPC

This gRPC model defines several services within a package designed for the Broker API, focusing on positions and ledgers in a financial or trading context. Below is a brief description of each service and its methods for documentation purposes.

The connection will be dropped if no new data appears in the latest 15 minutes.

### 1. PositionsServiceExternal

**getClientPositionsStream**: This method streams the positions of a client in real-time. It takes a `PBClientPositionRequestExternal` message, which includes the trading account UUID, and returns a stream of `PBPositionStreamResponseExternal` messages. These messages contain information about each position, such as the position state (new, update, closed, etc.), the position details (symbol, volume, open time, etc.), and partial position updates.

### 2. LedgerServiceExternal

**getLedgersStream**: This method provides a stream of ledger entries for a trading account. It accepts a `PBLedgerRequestExternal` message, including the trading account UUID and a list of history operation types (e.g., deposit, withdrawal, commission). The response is a stream of `PBLedgerStreamResponseExternal` messages, detailing each ledger entry with information such as the operation type, time, profit, and an optional comment.

```proto
syntax = "proto3";
import "google/protobuf/empty.proto";

option java_multiple_files = true;
option java_package = "com.matchtrade.mtr.grpc.admin_gateway";

package com.matchtrade.mtr.grpc.admin_gateway;

service PositionsServiceExternal {
  rpc getClientPositionsStream (PBClientPositionRequestExternal) returns (stream PBPositionStreamResponseExternal) {
  }
}

service LedgerServiceExternal {
  rpc getLedgersStream(PBLedgerRequestExternal) returns (stream PBLedgerStreamResponseExternal) {
  }
}

message PBDailyStatisticsExternal {
  double change = 1;
  double high = 2;
  double low = 3;
}

message PBClientPositionRequestExternal {
  string systemUuid = 1;
  string login = 2;
}

message PBPositionStreamResponseExternal {
  repeated PBPositionStateExternal positions = 1;
}

message PBPositionStateExternal {
  PBRequestUpdateTypeExternal request_update_type = 1;
  PBPositionExternal position = 2;
}

enum PBRequestUpdateTypeExternal {
  NEW = 0;
  UPDATE = 1;
  CLOSED = 2;
  PROFIT_CHANGE = 3;
  PARTIALLY_CLOSED = 4;
}

message PBPositionExternal {
  string id = 1;
  string symbol = 2;
  string alias = 15;
  double volume = 3;
  PBOrderSideExternal side = 4;
  string open_time = 5;
  double open_price = 6;
  double stop_loss = 7;
  double take_profit = 8;
  double swap = 9;
  double profit = 10;
  double net_profit = 11;
  double current_price = 12;
  double commission = 13;
  repeated PBPartialExternal partials = 14;
}

message PBPartialExternal {
  string id = 1;
  double volume = 3;
  double old_partial_volume = 4;
  string open_time = 6;
  double open_price = 7;
  double swap = 8;
  double profit = 9;
  double net_profit = 10;
  double commission = 12;
}

enum PBOrderSideExternal {
  BUY = 0;
  SELL = 1;
}

message PBLedgerRequestExternal {
  string systemUuid = 1;
  string login = 2;
  repeated PBHistoryOperationTypeExternal historyOperationType = 3;
}

enum PBHistoryOperationTypeExternal {
  DEPOSIT = 0;
  WITHDRAWAL = 1;
  CREDIT_IN = 2;
  CREDIT_OUT = 3;
  AGENT_COMMISSION = 4;
  COMMISSIONS = 5;
  SWAPS = 6;
  OTHER = 7;
}

message PBLedgerStreamResponseExternal {
  string id = 1;
  PBHistoryOperationTypeExternal historyOperationType = 2;
  string time = 3;
  double profit = 4;
  string comment = 5;
}
```

## Errors

We use standard HTTP response codes to state the success or failure of API requests. It means that codes in the range:

- 2xx indicate success and usually have response bodies,
- 4xx indicate errors with detailed answers to why a particular request failed,
- 5xx indicate errors on our servers.

### Status Codes

| Code | Description |
|------|-------------|
| **200 OK** | The request was performed successfully. You should receive a response with a body. 200 response bodies are listed in each endpoint's response section. |
| **204 No Content** | The request was performed successfully. You should receive a response without a body. |
| **400 Bad Request** | Invalid request, usually due to missing some required parameter. |
| **401 Unauthorized** | Authorization information is missing or invalid. |
| **403 Forbidden** | Your API token does not have permission to perform the requested operation. |
| **422 Unprocessable Entity** | The server understands the content type of the request entity, and the syntax is correct, but it was unable to process the request. |
| **500 Internal Server Error** | Something went wrong on our end. You can contact our Product Support to check it. |


## Examples

In this section, you can find examples of error responses for each status code. Successful responses are available in the relevant sections of this documentation.

### Responses

#### 400 Bad Request

**Response Attributes:**

| Attribute  | Type   | Description         |
|------------|--------|---------------------|
| status     | number | HTTP status code    |
| title      | string | Error title         |
| detail     | string | Error detail        |
| path       | string | Request path        |
| timestamp  | string | Error timestamp     |

#### Example Response:
```json
{
  "status": 400,
  "title": "Bad Request",
  "detail": "Required parameter missing",
  "path": "/api/v1/resource",
  "timestamp": "2023-06-19T12:34:56Z"
}
```

#### 401 Unauthorized

**Response Attributes:**

| Attribute  | Type   | Description         |
|------------|--------|---------------------|
| status     | number | HTTP status code    |
| title      | string | Error title         |
| detail     | string | Error detail        |
| path       | string | Request path        |
| timestamp  | string | Error timestamp     |

#### Example Response:
```json
{
  "status": 401,
  "title": "Unauthorized",
  "detail": "Authentication failed",
  "path": "/api/v1/resource",
  "timestamp": "2023-06-19T12:34:56Z"
}
```

#### 403 Forbidden

**Response Attributes:**

| Attribute  | Type   | Description         |
|------------|--------|---------------------|
| status     | number | HTTP status code    |
| title      | string | Error title         |
| detail     | string | Error detail        |
| path       | string | Request path        |
| timestamp  | string | Error timestamp     |

#### Example Response:
```json
{
  "status": 403,
  "title": "Forbidden",
  "detail": "Access denied",
  "path": "/api/v1/resource",
  "timestamp": "2023-06-19T12:34:56Z"
}
```

#### 500 Internal Server Error

**Response Attributes:**

| Attribute  | Type   | Description         |
|------------|--------|---------------------|
| status     | number | HTTP status code    |
| title      | string | Error title         |
| detail     | string | Error detail        |
| path       | string | Request path        |
| timestamp  | string | Error timestamp     |

#### Example Response:
```json
{
  "status": 500,
  "title": "Internal Server Error",
  "detail": "An unexpected error occurred",
  "path": "/api/v1/resource",
  "timestamp": "2023-06-19T12:34:56Z"
}
```
## Branches, Offers, Roles

In our system, there is a distinction between multiple layers of account attributes. In general, the account operates within the frames of a given: Branch, Offer(s), and Role. Each section has an explanation of a particular layer.

Via our API, you cannot edit Branches, Offers, and Roles configuration (it can be done only by using our CRM), but it's important to know them since you will need them to efficiently manage accounts created in the system. That's why we share  endpoints, where you can check Branches, Offers, and Roles available in your setup.

##  Branches

Branch represents the geographical or organizational division where the user operates. On the broker's end, it is usually connected to a specific area of responsibility or jurisdiction within the company's structure. This does not have to be fixed and the division can be freely shaped, depending on the business needs. The only requirement is that every account must have a branch assigned, even if there is one branch instance in the system.

#### GET /v1/branches?from=&to=&sort=
```sh
curl --location 'https://broker-api-demo.match-trader.com/v1/branches' \
--header 'Authorization: Provide your token here' \
--header 'Content-Type: application/json'
```

#### Response
```json
{
  "branches": [
    {
      "uuid": "d3318c30-8c90-4018-ac25-fe2d8444d77e",
      "partnerId": "0",
      "created": "2019-12-31T10:38:15.908Z",
      "updated": "2022-12-06T09:44:36.840Z",
      "name": "Default",
      "mailingConfiguration": {
        "emailUser": "",
        "emailPassword": "",
        "emailHost": "",
        "emailPort": null,
        "emailTls": false,
        "emailSsl": false
      },
      "tfaRequiredForClients": false,
      "mt4RealServerName": "",
      "mt4DemoServerName": "",
      "mt5RealServerName": "",
      "mt5DemoServerName": "",
      "useSumSubKyc": false,
      "paymentGatewayUuids": [
        ""
      ]
    }
  ]
}
```

### Header Parameters

- **Authorization**: `string`

### Query Parameters

- **from**: `string (date-time)`
- **to**: `string (date-time)`
- **sort**: `string`
  - **Default value**: `created,desc`

### Responses

#### 200 OK

**Response Attributes:**

- **branches**: `array`
  - **child attributes**:
    - **uuid**: `string`
      - Unique identifier for the Branch.
    - **partnerId**: `string`
      - Unique ID of a particular Broker in the system.
    - **created**: `string`
      - Time at which Branch was created.
    - **updated**: `string`
      - Time of the last update of the Branch.
    - **name**: `string`
      - The Branch's name.
    - **mailingConfiguration**: `object`
      - Mailing Configuration is used for connecting email providers to send emails to users within a particular branch.
      - **child attributes**:
        - **emailUser**: `string`
          - Login used to send emails.
        - **emailPassword**: `string`
          - Password used to send emails.
        - **emailHost**: `string`
          - Mailer configuration host.
        - **emailPort**: `string`
          - Mailer configuration port.
        - **emailTls**: `boolean`
          - Whether the configuration is using TSL encryption technology.
        - **emailSsl**: `boolean`
          - Whether the configuration is using SSL encryption technology.
    - **tfaRequiredForClients**: `boolean`
      - Whether Two Factor Authentication is required for clients to operate.
    - **mt4RealServerName**: `string`
      - Real/Live server name for MetaTrader 4 related purposes.
    - **mt4DemoServerName**: `string`
      - Demo server name for MetaTrader 4 related purposes.
    - **mt5RealServerName**: `string`
      - Real/Live server name for MetaTrader 4 related purposes.
    - **mt5DemoServerName**: `string`
      - Demo server name for MetaTrader 4 related purposes.
    - **useSumSubKyc**: `boolean`
      - Whether Sumsub is being used in that particular Branch to verify users.
    - **paymentGatewayUuids**: `array`

## Get Offers

Offer is associated with the user's trading account. It can be either Demo or Real and have different leverage, currency, or other trading-related configurations. In regards to Branch, users can be in one branch but have multiple trading accounts within different offers.


### GET /v1/offers?sort=&from=&to=
```sh
curl --location 'https://broker-api-demo.match-trader.com/v1/offers' \
--header 'Authorization: Provide you token here' \
--header 'Content-Type: application/json'
```
### Response
```json
{
  "offers": [
    {
      "uuid": "f6cbaca3-cc96-4275-a784-12659032b544",
      "partnerId": null,
      "created": "2023-03-23T10:09:17.683Z",
      "updated": "2024-03-26T10:52:35.647434Z",
      "name": "QFX Demo",
      "currency": "USD",
      "demo": true,
      "hidden": true,
      "description": "",
      "moneyManager": null,
      "initialDeposit": 10000,
      "leverage": 100,
      "verificationRequired": false,
      "tradingAccountAutoCreation": true,
      "groupName": "testUSD",
      "branch": {
        "uuid": "d3318c30-8c90-4018-ac25-fe2d8444d77e",
        "partnerId": null,
        "created": "2019-12-31T10:38:15.908Z",
        "name": "Default"
      },
      "system": {
        "uuid": "8e9ed851-1e5e-479b-aa19-bade6a67d1d5",
        "partnerId": null,
        "created": "2023-01-20T13:08:11.215Z",
        "name": "QFX Demo",
        "demo": false,
        "active": true,
        "systemType": "QFX"
      }
    }
  ]
}
```

### Header Parameters

- **Authorization**: `string`

### Query Parameters

- **sort**: `string`
  - **Default value**: `created,desc`
- **from**: `string (date-time)`
- **to**: `string (date-time)`

### Responses

#### 200 OK

**Response Attributes:**

- **offers**: `array`
  - **child attributes**:
    - **uuid**: `string (uuid)`
      - Unique identifier for the Offer.
    - **partnerId**: `integer (int64)`
      - Unique ID of a particular Broker in the system.
    - **created**: `string (date-time)`
      - Time at which the Offer was created.
    - **updated**: `string (date-time)`
      - Time of the last update of the Offer.
    - **name**: `string`
      - The Offer's name.
    - **currency**: `string`
      - The currency of the Offer. All trading accounts within a particular Offer will have that currency.
    - **demo**: `boolean`
      - Whether the Offer is a demo. If false, the Offer is real/live.
    - **hidden**: `boolean`
      - Whether the Offer is not visible when creating a new trading account. It can also be used for displaying Offers in the registration process.
    - **description**: `string`
      - Offer's description you want to show to users.
    - **moneyManager**: `string`
      - Parameter used for PAMM system connection purposes.
    - **initialDeposit**: `number`
      - Amount of funds that will be automatically deposited to a trading account after finishing the registration process.
    - **leverage**: `integer (int32)`
      - Leverage for trading accounts created in a particular Offer.
    - **verificationRequired**: `boolean`
      - Whether to start operating, users have to be verified.
    - **tradingAccountAutoCreation**: `boolean`
      - Whether trading accounts will be created automatically or you need to be accepted manually in the CRM.
    - **groupName**: `string`
      - Group name of a particular group in the trading system. It is used to connect Offers to the trading system and create new trading accounts in that group.
    - **branch**: `object`
      - The branch in the Offer is used only once - for the registration process. After selecting the Offer to create a trading account, a user will have that particular Branch assigned to their newly created account.
      - **child attributes**:
        - **uuid**: `string (uuid)`
          - Unique identifier for the Branch.
        - **partnerId**: `integer (int64)`
          - Unique ID of a particular Broker in the system.
        - **created**: `string (date-time)`
          - Time at which the Branch was created.
        - **name**: `string`
          - Name of the Branch.
    - **system**: `object`
      - Trading system to which the Offer is connected and in which it operates.
      - **child attributes**:
        - **uuid**: `string (uuid)`
          - Unique identifier for the System.
        - **partnerId**: `integer (int64)`
          - Unique ID of a particular Broker in the system.
        - **created**: `string (date-time)`
          - Time at which the System was created.
        - **name**: `string`
          - The System's name.
        - **demo**: `boolean`
          - Whether the System is demo or real/live. If false, the System is real/live.
        - **active**: `boolean`
          - Whether the System is enabled in the CRM.
        - **systemType**: `string`
          - The System's trading engine it is connected to.
          - **Enum values**:
            - MT4
            - MT5
            - QFX


## Get Roles

Role is connected directly to the user's account. Roles with access to the Match-Trader trading platform are: User, IB, and Sub IB. Other roles are reserved for users with access to the CRM and have different responsibilities and permissions to manage accounts.

### GET /v1/roles?from=&to=&sort=
```sh
curl --location 'https://broker-api-demo.match-trader.com/v1/roles' \
--header 'Authorization: Provide you token here' \
--header 'Content-Type: application/json'
```
### Response
`200`
```json
{
  "roles": [
    {
      "uuid": "82ce6e5b-3e88-4952-90ea-f850f290d857",
      "partnerId": null,
      "created": "2024-01-16T10:55:36.461115Z",
      "updated": "2024-01-16T10:55:36.461150Z",
      "name": "CRM integrations"
    }
  ]
}
```

### Header Parameters

- **Authorization**: `string`

### Query Parameters

- **sort**: `string`
  - **Default value**: `created,desc`
- **from**: `string (date-time)`
- **to**: `string (date-time)`

### Responses

#### 200 OK

**Response Attributes:**

- **roles**: `array`
  - **child attributes**:
    - **uuid**: `string (uuid)`
      - Unique identifier for the Role.
    - **partnerId**: `integer (int64)`
      - Unique ID of a particular Broker in the system.
    - **created**: `string (date-time)`
      - Time at which the Role was created.
    - **updated**: `string (date-time)`
      - Time of the last update of the Role.
    - **name**: `string`
      - The Role's name.


## Accounts

In the Accounts section, you will find detailed information about user accounts within the system. With this, you can access and update account details and create new accounts. This section serves as a central hub for overseeing and administering user accounts within the system. You can also find a Trading Accounts sub-section within the section with all relevant endpoints to manage Trading Accounts.

An Account represents the user, and a Trading Account is needed to perform trading actions. Under one Account, there might be more than one Trading Account. Some actions are performed only on Accounts, while others are on Trading Accounts.

In our system, there is a distinction between Leads and Clients. They are both types of user accounts. The difference between Lead and Client is conversion (first deposit) - the user is a Lead by default and becomes a Client after the first successful deposit.

### Get Accounts

With the Get Accounts endpoint, you can retrieve detailed information about all accounts within the system.

Since the response is paged, please make sure you are making a valid request. The default request's response (without providing a proper query param) is set to 10 records.

### GET /v1/accounts?query=&page=&size=&sort=&from=&to=&accountType=ALL
```sh
curl --location 'https://broker-api-demo.match-trader.com/v1/accounts?accountType=ALL' \
--header 'Authorization: Provide you token here' \
--header 'Content-Type: application/json'
```

### Response 
`200`
```json
{
  "content": [
    {
      "uuid": "0044eb05-6964-40e1-a920-c6a3e8258569",
      "created": "2024-04-05T13:37:54.014Z",
      "updated": "2024-04-05T13:37:54.014Z",
      "email": "integration-demo@match-trade.com",
      "verificationStatus": "NEW",
      "type": "RETAIL",
      "personalDetails": {
        "firstname": "Demo",
        "lastname": "Integration Account",
        "dateOfBirth": "2000-01-01",
        "citizenship": "US",
        "language": "en",
        "maritalStatus": "Married",
        "passport": {
          "number": "123-456-789",
          "country": "USA"
        },
        "taxIdentificationNumber": "Sample TIN"
      },
      "contactDetails": {
        "phoneNumber": "+1111111111",
        "faxNumber": "+1111111111",
        "toContact": {
          "toContactDate": null,
          "alreadyContacted": false
        }
      },
      "accountConfiguration": {
        "partnerId": null,
        "branchUuid": "d3318c30-8c90-4018-ac25-fe2d8444d77e",
        "roleUuid": "1e7c3cc4-0780-4dcf-850e-b9b444bcc561",
        "accountManager": {
          "uuid": "21913fc9-1851-4ab4-a862-5230ad1f53ae",
          "email": "conversion@roles.test",
          "name": null
        },
        "ibParentTradingAccountUuid": "44edfdc6-bb7e-464a-844d-d82ffefb9e24",
        "crmUserScope": {
          "branchScope": [
            ""
          ],
          "managerPools": [
            ""
          ]
        },
        "accountTypeContact": false
      },
      "addressDetails": {
        "country": "US",
        "state": "New York",
        "city": "New York",
        "postCode": "NY 10005",
        "address": "11 Wall St"
      },
      "bankingDetails": {
        "bankAddress": "Sample Bank Address",
        "bankSwiftCode": "Sample SWIFT Code",
        "bankAccount": "Sample Bank Account",
        "bankName": "Sample Bank Name",
        "accountName": "Sample Account Name"
      },
      "leadDetails": {
        "statusUuid": "77092554-9ab3-44ab-a5c9-49fcc7c459fa",
        "source": "Client Referral",
        "providerUuid": null,
        "becomeActiveClientTime": "2024-04-05T13:37:53.953Z"
      }
    },
    "test"
  ],
  "totalPages": 1,
  "totalElements": null,
  "number": null,
  "size": 1
}
```

#### Header Parameters

- **Authorization**: `string`

#### Query Parameters

- **query**: `string`
  - You can use it to ask for a specific: email, first name, last name, account manager name, account manager surname, country, phone, and lead status.
- **page**: `integer (int32)`
  - Page number you want to receive the data from.
  - **Default value**: `0`
- **size**: `integer (int32)`
  - Page size - how many records you want to receive in the response.
  - **Default value**: `10`
- **sort**: `string`
  - Either descending or ascending. You can sort by: created, updated, email, phone, first name, last name, country, state, city, postCode, and address.
  - **Default value**: `created,desc`
- **from**: `string (date-time)`
  - Starting date and time for created.
- **to**: `string (date-time)`
  - Ending date and time for created.
- **accountType**: `string`
  - **Default value**: `ALL`
  - **Enum values**: CLIENT, LEAD, ALL

#### Responses

##### 200 OK

**Response Attributes:**

- **content**: `array`
  - **child attributes**:
    - **uuid**: `string (uuid)`
      - Unique identifier for the Account.
    - **created**: `string (date-time)`
      - Time at which the Account was created.
    - **updated**: `string (date-time)`
      - Time of the last update of the Account.
    - **email**: `string`
      - User's email address.
    - **verificationStatus**: `string`
      - User's verification status for KYC purposes.
      - **Enum values**: NEW, REJECTED, VERIFIED, BLOCKED, PENDING_VERIFICATION, UNVERIFIED
    - **type**: `string`
      - Account type.
      - **Enum values**: RETAIL, PROFESSIONAL, EXPERIENCED
    - **personalDetails**: `object`
      - **child attributes**:
        - **firstname**: `string`
          - User's first name.
        - **lastname**: `string`
          - User's last name.
        - **dateOfBirth**: `string (date)`
          - User's date of birth.
        - **citizenship**: `string`
          - User's citizenship.
        - **language**: `string`
          - User's language.
        - **maritalStatus**: `string`
          - User's marital status.
        - **passport**: `object`
          - **child attributes**:
            - **number**: `string`
              - User's passport/ID number.
            - **country**: `string`
              - User's passport/ID issuer country.
        - **taxIdentificationNumber**: `string`
          - User's tax identification number.
    - **contactDetails**: `object`
      - **child attributes**:
        - **phoneNumber**: `string`
          - User's phone number.
        - **faxNumber**: `string`
          - User's fax number.
    - **toContact**: `object`
      - **child attributes**:
        - **toContactDate**: `string (date-time)`
          - The date you want to contact the particular user.
        - **alreadyContacted**: `boolean`
          - When true, it means this user is marked as contacted in the CRM.
    - **accountConfiguration**: `object`
      - **child attributes**:
        - **partnerId**: `integer (int64)`
          - Unique ID of a particular Broker in the system.
        - **branchUuid**: `string (uuid)`
          - Unique identifier for the Branch of a particular Account.
        - **roleUuid**: `string (uuid)`
          - Unique identifier for the Role of a particular Account.
    - **accountManager**: `object`
      - Details of Account Manager assigned to the particular Account.
      - **child attributes**:
        - **uuid**: `string (uuid)`
          - Unique identifier for the Account Manager.
        - **email**: `string`
          - Account Manager's email address.
        - **name**: `string`
          - Account Manager's name.
    - **ibParentTradingAccountUuid**: `string (uuid)`
      - If an Account is in the IB structure, this field will return the value of an IB Account above that particular account in the IB structure.
    - **crmUserScope**: `object`
      - Only applicable for CRM Users.
      - **child attributes**:
        - **branchScope**: `array`
          - Allow unique items: true
        - **managerPools**: `array`
          - Allow unique items: true
        - **accountTypeContact**: `boolean`
    - **addressDetails**: `object`
      - **child attributes**:
        - **country**: `string`
          - User's country.
        - **state**: `string`
          - User's state.
        - **city**: `string`
          - User's city.
        - **postCode**: `string`
          - User's postal/zip code.
        - **address**: `string`
          - User's address (for example street, building, and flat numbers).
    - **bankingDetails**: `object`
      - **child attributes**:
        - **bankAddress**: `string`
          - User's bank's address.
        - **bankSwiftCode**: `string`
          - User's bank's SWIFT code.
        - **bankAccount**: `string`
          - User's bank account (for example: bank number).
        - **bankName**: `string`
          - User's bank's name.
        - **accountName**: `string`
          - User's bank account name.
    - **leadDetails**: `object`
      - **child attributes**:
        - **statusUuid**: `string (uuid)`
          - Unique identifier for the Lead Status of a particular Account.
        - **source**: `string`
          - User's lead source/source of origin (from where the user appeared in the system).
        - **providerUuid**: `string (uuid)`
          - Unique identifier for the Lead Provider of a particular Account.
        - **becomeActiveClientTime**: `string (date-time)`
          - The time when the user became Client (and stopped being Lead).
    - **totalPages**: `integer (int32)`
      - Total number of pages based on the requested size.
    - **totalElements**: `integer (int64)`
      - Total number of Accounts in the system (based on the provided query params).
    - **number**: `integer (int32)`
      - Page number.
    - **size**: `integer (int32)`
      - Number of records included in one page.

### Get Account by Email

With the Get Account by Email endpoint, you can retrieve detailed information about a particular Account in the system using the user's email.

### GET /v1/accounts/by-email/{email}
```sh
curl --location --globoff 'https://broker-api-demo.match-trader.com/v1/accounts/by-email/{email}' \
--header 'Authorization: Provide you token here' \
--header 'Content-Type: application/json'
```

### Response
`200`
```json
{
  "uuid": "",
  "created": "",
  "updated": "",
  "email": "",
  "verificationStatus": "NEW",
  "type": "RETAIL",
  "personalDetails": {
    "firstname": "",
    "lastname": "",
    "dateOfBirth": "",
    "citizenship": "",
    "language": "",
    "maritalStatus": "",
    "passport": {
      "number": "",
      "country": ""
    },
    "taxIdentificationNumber": ""
  },
  "contactDetails": {
    "phoneNumber": "",
    "faxNumber": "",
    "toContact": {
      "toContactDate": "",
      "alreadyContacted": false
    }
  },
  "accountConfiguration": {
    "partnerId": null,
    "branchUuid": "",
    "roleUuid": "",
    "accountManager": {
      "uuid": "",
      "email": "",
      "name": ""
    },
    "ibParentTradingAccountUuid": "",
    "crmUserScope": {
      "branchScope": [
        ""
      ],
      "managerPools": [
        ""
      ]
    },
    "accountTypeContact": false
  },
  "addressDetails": {
    "country": "",
    "state": "",
    "city": "",
    "postCode": "",
    "address": ""
  },
  "bankingDetails": {
    "bankAddress": "",
    "bankSwiftCode": "",
    "bankAccount": "",
    "bankName": "",
    "accountName": ""
  },
  "leadDetails": {
    "statusUuid": "",
    "source": "",
    "providerUuid": "",
    "becomeActiveClientTime": ""
  }
}
```

#### Header Parameters

- **Authorization**: `string`

#### Path Parameters

- **email**: `string`
  - **REQUIRED**

#### Responses

##### 200 OK

**Response Attributes:**

- **uuid**: `string (uuid)`
  - Unique identifier for the user.
- **created**: `string (date-time)`
  - The creation date and time of the user account.
- **updated**: `string (date-time)`
  - The last updated date and time of the user account.
- **email**: `string`
  - The email address of the user.
- **verificationStatus**: `string`
  - The status of the user's email verification.
  - **Enum values**: NEW, REJECTED, VERIFIED, BLOCKED, PENDING_VERIFICATION, UNVERIFIED
- **type**: `string`
  - The type of user, with possible values being RETAIL, PROFESSIONAL, and EXPERIENCED.
  - **Enum values**: RETAIL, PROFESSIONAL, EXPERIENCED
- **personalDetails**: `object`
  - **child attributes**:
    - **firstname**: `string`
      - User’s first name.
    - **lastname**: `string`
      - User’s last name.
    - **dateOfBirth**: `string (date)`
      - User’s date of birth.
    - **citizenship**: `string`
      - Citizenship of the user.
    - **language**: `string`
      - Preferred language of the user.
    - **maritalStatus**: `string`
      - Marital status of the user.
    - **passport**: `object`
      - An object containing passport details such as number and country of issuance.
      - **child attributes**:
        - **number**: `string`
          - Passport number.
        - **country**: `string`
          - Country of issuance.
    - **taxIdentificationNumber**: `string`
      - User's tax identification number.
- **contactDetails**: `object`
  - **child attributes**:
    - **phoneNumber**: `string`
      - User’s phone number.
    - **faxNumber**: `string`
      - User’s fax number.
- **toContact**: `object`
  - An object related to contacting details.
  - **child attributes**:
    - **toContactDate**: `string (date-time)`
      - The date scheduled to contact the user.
    - **alreadyContacted**: `boolean`
      - A boolean indicating whether the user has already been contacted.
- **accountConfiguration**: `object`
  - Object with details about account configuration.
  - **child attributes**:
    - **partnerId**: `integer (int64)`
      - Identifier for the partner.
    - **branchUuid**: `string (uuid)`
      - UUID of the branch.
    - **roleUuid**: `string (uuid)`
      - UUID of the user's role.
- **accountManager**: `object`
  - An object containing information about the user's account manager.
  - **child attributes**:
    - **ibParentTradingAccountUuid**: `string (uuid)`
      - UUID of the IB parent trading account.
- **crmUserScope**: `object`
  - An object detailing CRM user scope.
  - **child attributes**:
    - **branchScope**: `array`
      - Allow unique items: true
    - **managerPools**: `array`
      - Allow unique items: true
    - **accountTypeContact**: `boolean`
- **addressDetails**: `object`
  - **child attributes**:
    - **country**: `string`
      - The country where the user resides.
    - **state**: `string`
      - The state where the user resides.
    - **city**: `string`
      - The city where the user resides.
    - **postCode**: `string`
      - Postal code of the user’s address.
    - **address**: `string`
      - Detailed street address of the user.
- **bankingDetails**: `object`
  - **child attributes**:
    - **bankAddress**: `string`
      - The address of the bank.
    - **bankSwiftCode**: `string`
      - SWIFT code of the bank.
    - **bankAccount**: `string`
      - The user's bank account number.
    - **bankName**: `string`
      - Name of the bank where the user's account is held.
    - **accountName**: `string`
      - The name associated with the bank account.
- **leadDetails**: `object`
  - **child attributes**:
    - **statusUuid**: `string (uuid)`
      - UUID representing the status of the lead.
    - **source**: `string`
      - The source from which the lead originated, e.g., a marketing campaign, referral, etc.
    - **providerUuid**: `string (uuid)`
      - UUID of the provider that referred or generated the lead.
    - **becomeActiveClientTime**: `string (date-time)`
      - The date and time when the lead became an active client.

### Get Account by UUID

With the Get Account by UUID endpoint, you can retrieve detailed information about a particular Account in the system using the Account's UUID.

### GET /v1/accounts/by-uuid/{accountUuid}
```sh
curl --location --globoff 'https://broker-api-demo.match-trader.com/v1/accounts/by-uuid/{accountUuid}' \
--header 'Authorization: Provide you token here' \
--header 'Content-Type: application/json'
```
### Response
`200`
```json
{
  "uuid": "",
  "created": "",
  "updated": "",
  "email": "",
  "verificationStatus": "NEW",
  "type": "RETAIL",
  "personalDetails": {
    "firstname": "",
    "lastname": "",
    "dateOfBirth": "",
    "citizenship": "",
    "language": "",
    "maritalStatus": "",
    "passport": {
      "number": "",
      "country": ""
    },
    "taxIdentificationNumber": ""
  },
  "contactDetails": {
    "phoneNumber": "",
    "faxNumber": "",
    "toContact": {
      "toContactDate": "",
      "alreadyContacted": false
    }
  },
  "accountConfiguration": {
    "partnerId": null,
    "branchUuid": "",
    "roleUuid": "",
    "accountManager": {
      "uuid": "",
      "email": "",
      "name": ""
    },
    "ibParentTradingAccountUuid": "",
    "crmUserScope": {
      "branchScope": [
        ""
      ],
      "managerPools": [
        ""
      ]
    },
    "accountTypeContact": false
  },
  "addressDetails": {
    "country": "",
    "state": "",
    "city": "",
    "postCode": "",
    "address": ""
  },
  "bankingDetails": {
    "bankAddress": "",
    "bankSwiftCode": "",
    "bankAccount": "",
    "bankName": "",
    "accountName": ""
  },
  "leadDetails": {
    "statusUuid": "",
    "source": "",
    "providerUuid": "",
    "becomeActiveClientTime": ""
  }
}
```

#### Header Parameters

- **Authorization**: `string`

#### Path Parameters

- **accountUuid**: `string (uuid)`
  - **REQUIRED**
  - Unique identifier for a user account, used to fetch specific account data.

#### Responses

##### 200 OK

**Response Attributes:**

- **uuid**: `string (uuid)`
  - Unique identifier for the user.
- **created**: `string (date-time)`
  - The creation date and time of the user account.
- **updated**: `string (date-time)`
  - The last updated date and time of the user account.
- **email**: `string`
  - The email address of the user.
- **verificationStatus**: `string`
  - The status of the user's email verification.
  - **Enum values**: NEW, REJECTED, VERIFIED, BLOCKED, PENDING_VERIFICATION, UNVERIFIED
- **type**: `string`
  - The type of user, with possible values being RETAIL, PROFESSIONAL, and EXPERIENCED.
  - **Enum values**: RETAIL, PROFESSIONAL, EXPERIENCED
- **personalDetails**: `object`
  - **child attributes**:
    - **firstname**: `string`
      - User’s first name.
    - **lastname**: `string`
      - User’s last name.
    - **dateOfBirth**: `string (date)`
      - User’s date of birth.
    - **citizenship**: `string`
      - Citizenship of the user.
    - **language**: `string`
      - Preferred language of the user.
    - **maritalStatus**: `string`
      - Marital status of the user.
    - **passport**: `object`
      - An object containing passport details such as number and country of issuance.
      - **child attributes**:
        - **number**: `string`
          - Passport number.
        - **country**: `string`
          - Country of issuance.
    - **taxIdentificationNumber**: `string`
      - User's tax identification number.
- **contactDetails**: `object`
  - **child attributes**:
    - **phoneNumber**: `string`
      - User’s phone number.
    - **faxNumber**: `string`
      - User’s fax number.
- **toContact**: `object`
  - An object related to contacting details.
  - **child attributes**:
    - **toContactDate**: `string (date-time)`
      - The date scheduled to contact the user.
    - **alreadyContacted**: `boolean`
      - A boolean indicating whether the user has already been contacted.
- **accountConfiguration**: `object`
  - Object with details about account configuration.
  - **child attributes**:
    - **partnerId**: `integer (int64)`
      - Identifier for the partner.
    - **branchUuid**: `string (uuid)`
      - UUID of the branch.
    - **roleUuid**: `string (uuid)`
      - UUID of the user's role.
- **accountManager**: `object`
  - An object containing information about the user's account manager.
  - **child attributes**:
    - **uuid**: `string (uuid)`
      - UUID of the account manager.
    - **email**: `string`
      - Email address of the account manager.
    - **name**: `string`
      - Name of the account manager.
- **ibParentTradingAccountUuid**: `string (uuid)`
  - UUID of the IB parent trading account.
- **crmUserScope**: `object`
  - An object detailing CRM user scope.
  - **child attributes**:
    - **branchScope**: `array`
      - Allow unique items: true
    - **managerPools**: `array`
      - Allow unique items: true
    - **accountTypeContact**: `boolean`
- **addressDetails**: `object`
  - **child attributes**:
    - **country**: `string`
      - The country where the user resides.
    - **state**: `string`
      - The state where the user resides.
    - **city**: `string`
      - The city where the user resides.
    - **postCode**: `string`
      - Postal code of the user’s address.
    - **address**: `string`
      - Detailed street address of the user.
- **bankingDetails**: `object`
  - **child attributes**:
    - **bankAddress**: `string`
      - The address of the bank.
    - **bankSwiftCode**: `string`
      - SWIFT code of the bank.
    - **bankAccount**: `string`
      - The user's bank account number.
    - **bankName**: `string`
      - Name of the bank where the user's account is held.
    - **accountName**: `string`
      - The name associated with the bank account.
- **leadDetails**: `object`
  - **child attributes**:
    - **statusUuid**: `string (uuid)`
      - UUID representing the status of the lead.
    - **source**: `string`
      - The source from which the lead originated, e.g., a marketing campaign, referral, etc.
    - **providerUuid**: `string (uuid)`
      - UUID of the provider that referred or generated the lead.
    - **becomeActiveClientTime**: `string (date-time)`
      - The date and time when the lead became an active client.

### Get Account's Timeline Events

With Get Account's Timeline Events endpoint, you can retrieve detailed information about events associated with a specific account. Users can access a timeline of events, including details, creators, and creation timestamps, providing valuable insights into account activities.

### GET /v1/accounts/{accountUuid}/timeline-events?type=VOIP_CALL&from=&to=&sort=
```sh
curl --location --globoff 'https://broker-api-demo.match-trader.com/v1/accounts/{accountUuid}/timeline-events?type=VOIP_CALL' \
--header 'Authorization: Provide you token here' \
--header 'Content-Type: application/json'
```

### Response
`200`
```json
{
  "timelineEvents": [
    {
      "details": "New login from IP address 83.15.168.234",
      "event": "LOGIN",
      "createdBy": null,
      "created": "2024-03-26T12:04:44.504Z"
    }
  ]
}
```

#### Header Parameters

- **Authorization**: `string`

#### Query Parameters

- **type**: `string`
  - A string parameter specifying the type of activity to filter by.
  - **Enum values**: VOIP_CALL, NOTE, TASK_CALL, TASK, LEAD_ASSIGNMENT, FAILED_DEPOSIT, SUCCESSFUL_DEPOSIT, WITHDRAWAL_TO_CONFIRM, STOP_LOSS, TAKE_PROFIT, ORDER_ACTIVATION, STOP_OUT, MARGIN_CALL, LOGIN
- **from**: `string (date-time)`
  - Filters activities starting from this date and time.
- **to**: `string (date-time)`
  - Filters activities up to this date and time.
- **sort**: `string`
  - Specifies the sorting order of the results. The default value is created,desc, which sorts the activities by creation date in descending order.
  - **Default value**: `created,desc`

#### Path Parameters

- **accountUuid**: `string (uuid)`
  - **REQUIRED**
  - Unique identifier for a user account, used to fetch specific account data.

#### Responses

##### 200 OK

**Response Attributes:**

- **timelineEvents**: `array`
  - **child attributes**:
    - **details**: `string`
      - This field takes different values depending on the type of the Event. However, each time it contains detailed information about the Event.
    - **event**: `string`
      - Different Event types available in the system.
      - **Enum values**: VOIP_CALL, NOTE, TASK_CALL, TASK, LEAD_ASSIGNMENT, FAILED_DEPOSIT, SUCCESSFUL_DEPOSIT, WITHDRAWAL_TO_CONFIRM, STOP_LOSS, TAKE_PROFIT, ORDER_ACTIVATION, STOP_OUT, MARGIN_CALL, LOGIN
    - **createdBy**: `string`
      - Indicates the creator of the Event.
    - **created**: `string (date-time)`
      - Time at which the Event was created.

### Create Account

In this section, you can find the endpoint for creating new Accounts in the system.

### POST /v1/accounts
```sh
curl --location 'https://broker-api-demo.match-trader.com/v1/accounts' \
--header 'Authorization: Provide you token here' \
--header 'Content-Type: application/json' \
--data '{
  "email": "",
  "password": "",
  "offerUuid": "",
  "clientType": "RETAIL",
  "createAsDepositedAccount": false,
  "personalDetails": {
    "firstname": "",
    "lastname": "",
    "dateOfBirth": "",
    "citizenship": "",
    "language": "",
    "maritalStatus": "",
    "passport": {
      "number": "",
      "country": ""
    },
    "taxIdentificationNumber": ""
  },
  "contactDetails": {
    "phoneNumber": "",
    "faxNumber": "",
    "toContact": {
      "toContactDate": "",
      "alreadyContacted": false
    }
  },
  "accountConfiguration": {
    "partnerId": null,
    "branchUuid": "",
    "roleUuid": "",
    "accountManagerUuid": "",
    "ibParentTradingAccountUuid": "",
    "crmUserScope": {
      "branchScope": [
        ""
      ],
      "managerPools": [
        ""
      ]
    },
    "accountTypeContact": false
  },
  "addressDetails": {
    "country": "",
    "state": "",
    "city": "",
    "postCode": "",
    "address": ""
  },
  "bankingDetails": {
    "bankAddress": "",
    "bankSwiftCode": "",
    "bankAccount": "",
    "bankName": "",
    "accountName": ""
  },
  "leadDetails": {
    "statusUuid": "",
    "source": "",
    "providerUuid": "",
    "becomeActiveClientTime": ""
  }
}'
```
### Response
`200`
```json
{
  "uuid": "",
  "oneTimeToken": "",
  "created": "",
  "updated": "",
  "email": "",
  "verificationStatus": "NEW",
  "type": "RETAIL",
  "personalDetails": {
    "firstname": "",
    "lastname": "",
    "dateOfBirth": "",
    "citizenship": "",
    "language": "",
    "maritalStatus": "",
    "passport": {
      "number": "",
      "country": ""
    },
    "taxIdentificationNumber": ""
  },
  "contactDetails": {
    "phoneNumber": "",
    "faxNumber": "",
    "toContact": {
      "toContactDate": "",
      "alreadyContacted": false
    }
  },
  "accountConfiguration": {
    "partnerId": null,
    "branchUuid": "",
    "roleUuid": "",
    "accountManager": {
      "uuid": "",
      "email": "",
      "name": ""
    },
    "ibParentTradingAccountUuid": "",
    "crmUserScope": {
      "branchScope": [
        ""
      ],
      "managerPools": [
        ""
      ]
    },
    "accountTypeContact": false
  },
  "addressDetails": {
    "country": "",
    "state": "",
    "city": "",
    "postCode": "",
    "address": ""
  },
  "bankingDetails": {
    "bankAddress": "",
    "bankSwiftCode": "",
    "bankAccount": "",
    "bankName": "",
    "accountName": ""
  },
  "leadDetails": {
    "statusUuid": "",
    "source": "",
    "providerUuid": "",
    "becomeActiveClientTime": ""
  }
}
```

#### Header Parameters

- **Authorization**: `string`

#### Body Parameters

- **email**: `string`
  - **REQUIRED**
  - The email address for account registration.
- **password**: `string`
  - **REQUIRED**
  - The secure password for the new account.
- **offerUuid**: `string (uuid)`
  - Identifier for a specific promotional offer.
- **clientType**: `string`
  - Type of client (RETAIL, PROFESSIONAL, EXPERIENCED).
  - **Enum values**: RETAIL, PROFESSIONAL, EXPERIENCED
- **createAsDepositedAccount**: `boolean`
  - Flag to set the account as initially deposited.
- **personalDetails**: `object`
  - **REQUIRED**
  - **child attributes**:
    - **firstname**: `string`
      - **REQUIRED**
      - Account holder’s first name.
    - **lastname**: `string`
      - **REQUIRED**
      - Account holder’s last name.
    - **dateOfBirth**: `string (date)`
      - Account holder's birthdate.
    - **citizenship**: `string`
      - Demographic details.
    - **language**: `string`
      - Demographic details.
    - **maritalStatus**: `string`
      - Demographic details.
    - **passport**: `object`
      - Includes passport number and issuing country.
      - **child attributes**:
        - **number**: `string`
          - Number of the passport.
        - **country**: `string`
          - Country of the passport.
    - **taxIdentificationNumber**: `string`
      - Tax ID for financial records.
- **contactDetails**: `object`
  - **child attributes**:
    - **phoneNumber**: `string`
      - Contact numbers.
    - **faxNumber**: `string`
      - User's fax number.
- **toContact**: `object`
  - **child attributes**:
    - **toContactDate**: `string (date-time)`
      - The specific date and time when the account holder prefers to be contacted.
    - **alreadyContacted**: `boolean`
      - **REQUIRED**
      - Indicates whether the account holder has been previously contacted. This is a required field.
- **accountConfiguration**: `object`
  - **child attributes**:
    - **partnerId**: `integer (int64)`
      - A unique identifier for the partner associated with the account.
    - **branchUuid**: `string (uuid)`
      - UUID of the branch where the account is managed.
    - **roleUuid**: `string (uuid)`
      - UUID identifying the specific role associated with the account.
    - **accountManagerUuid**: `string (uuid)`
      - UUID of the account manager assigned to handle the account.
    - **ibParentTradingAccountUuid**: `string (uuid)`
      - UUID of the parent trading account, linking any sub-accounts or related trading activities.
    - **crmUserScope**: `object`
      - **child attributes**:
        - **branchScope**: `array`
          - A list of branch identifiers defining the scope of operation for the CRM user. The array allows unique items only.
          - **Allow unique items**: true
        - **managerPools**: `array`
          - Defines pools of managers within the CRM system, restricted to unique entries.
          - **Allow unique items**: true
        - **accountTypeContact**: `boolean`
- **addressDetails**: `object`
  - Detailed address components for the account holder’s residence or operational base.
  - **child attributes**:
    - **country**: `string`
    - **state**: `string`
    - **city**: `string`
    - **postCode**: `string`
    - **address**: `string`
- **bankingDetails**: `object`
  - **child attributes**:
    - **bankAddress**: `string`
      - The address of the bank where the account holder’s account is located.
    - **bankSwiftCode**: `string`
      - The SWIFT code of the bank for international transactions.
    - **bankAccount**: `string`
      - The bank account number.
    - **bankName**: `string`
      - The name of the bank where the account is maintained.
    - **accountName**: `string`
      - The name associated with the bank account, typically the account holder's name.
- **leadDetails**: `object`
  - **child attributes**:
    - **statusUuid**: `string (uuid)`
      - UUID indicating the current status of the lead in the CRM system.
    - **source**: `string`
      - The origin or source from where the lead was generated.
    - **providerUuid**: `string (uuid)`
      - UUID of the provider or entity that referred or generated the lead.
    - **becomeActiveClientTime**: `string (date-time)`
      - The date and time when the lead is expected to become an active client.

#### Responses

##### 200 OK

**Response Attributes:**

- **uuid**: `string (uuid)`
  - **REQUIRED**
  - Required. A unique identifier for the account.
- **oneTimeToken**: `string`
  - Temporary token used for authentication or verification purposes.
- **created**: `string (date-time)`
  - **REQUIRED**
  - Required. The date and time the account was created.
- **updated**: `string (date-time)`
  - **REQUIRED**
  - Required. The date and time the account was last updated.
- **email**: `string`
  - **REQUIRED**
  - Required. The email address associated with the account.
- **verificationStatus**: `string`
  - **REQUIRED**
  - Required. The current status of the account's verification.
  - **Enum values**: NEW, REJECTED, VERIFIED, BLOCKED, PENDING_VERIFICATION, UNVERIFIED
- **type**: `string`
  - Type of account based on client classification. Possible values: RETAIL, PROFESSIONAL, EXPERIENCED.
  - **Enum values**: RETAIL, PROFESSIONAL, EXPERIENCED
- **personalDetails**: `object`
  - **REQUIRED**
  - Contains personal information about the account holder.
  - **child attributes**:
    - **firstname**: `string`
      - User’s first name.
    - **lastname**: `string`
      - User’s last name.
    - **dateOfBirth**: `string (date)`
      - User’s date of birth.
    - **citizenship**: `string`
      - Citizenship of the user.
    - **language**: `string`
      - Preferred language of the user.
    - **maritalStatus**: `string`
      - Marital status of the user.
    - **passport**: `object`
      - **child attributes**:
        - **number**: `string`
          - Passport number.
        - **country**: `string`
          - Country of issuance.
    - **taxIdentificationNumber**: `string`
- **contactDetails**: `object`
  - **REQUIRED**
  - **child attributes**:
    - **phoneNumber**: `string`
      - User’s phone number.
    - **faxNumber**: `string`
      - User’s fax number.
- **toContact**: `object`
  - **child attributes**:
    - **toContactDate**: `string (date-time)`
    - **alreadyContacted**: `boolean`
- **accountConfiguration**: `object`
  - **REQUIRED**
  - Object with details about account configuration.
  - **child attributes**:
    - **partnerId**: `integer (int64)`
      - **REQUIRED**
      - Identifier for the partner.
    - **branchUuid**: `string (uuid)`
      - **REQUIRED**
      - Branch identifier where the account is managed.
    - **roleUuid**: `string (uuid)`
      - Role identifier associated with the account.
- **accountManager**: `object`
  - Manager details.
  - **child attributes**:
    - **uuid**: `string (uuid)`
      - Unique identifier for the account manager.
    - **email**: `string`
      - Email address of the account manager.
    - **name**: `string`
      - Name of the account manager.
- **ibParentTradingAccountUuid**: `string (uuid)`
  - Identifier for a parent IB trading account linked to the account.
- **crmUserScope**: `object`
  - CRM user's scope.
  - **child attributes**:
    - **branchScope**: `array`
      - Allow unique items: true
    - **managerPools**: `array`
      - Allow unique items: true
    - **accountTypeContact**: `boolean`
- **addressDetails**: `object`
  - **REQUIRED**
  - **child attributes**:
    - **country**: `string`
      - Country location.
    - **state**: `string`
      - State or region.
    - **city**: `string`
      - City or locality.
    - **postCode**: `string`
      - Postal code.
    - **address**: `string`
      - Street address.
- **bankingDetails**: `object`
  - **REQUIRED**
  - Provides essential financial information for transactions.
  - **child attributes**:
    - **bankAddress**: `string`
      - Physical address of the bank.
    - **bankSwiftCode**: `string`
      - SWIFT code for international banking.
    - **bankAccount**: `string`
      - Bank account number.
    - **bankName**: `string`
      - Name of the bank.
    - **accountName**: `string`
      - Name associated with the bank account.
- **leadDetails**: `object`
  - **REQUIRED**
  - Details for tracking and managing leads.
  - **child attributes**:
    - **statusUuid**: `string (uuid)`
      - Unique ID for the lead's status.
    - **source**: `string`
      - Origin of the lead.
    - **providerUuid**: `string (uuid)`
      - ID of the lead provider.
    - **becomeActiveClientTime**: `string (date-time)`
      - Expected time for lead conversion to active client.

### Update Account Info

With this endpoint, you can update and modify Account details.

### PATCH /v1/accounts/{accountUuid}
```sh
curl --location --globoff --request PATCH 'https://broker-api-demo.match-trader.com/v1/accounts/{accountUuid}' \
--header 'Authorization: Provide you token here' \
--header 'Content-Type: application/json' \
--data '{
  "verificationStatus": "NEW",
  "clientType": "RETAIL",
  "personalDetails": {
    "firstname": "",
    "lastname": "",
    "dateOfBirth": "",
    "citizenship": "",
    "language": "",
    "maritalStatus": "",
    "passport": {
      "number": "",
      "country": ""
    },
    "taxIdentificationNumber": ""
  },
  "contactDetails": {
    "phoneNumber": "",
    "faxNumber": "",
    "toContact": {
      "toContactDate": "",
      "alreadyContacted": false
    }
  },
  "accountConfiguration": {
    "branchUuid": "",
    "roleUuid": "",
    "ibParentTradingAccountUuid": "",
    "accountManagerUuid": "",
    "accountTypeContact": false
  },
  "addressDetails": {
    "country": "",
    "state": "",
    "city": "",
    "postCode": "",
    "address": ""
  },
  "bankingDetails": {
    "bankAddress": "",
    "bankSwiftCode": "",
    "bankAccount": "",
    "bankName": "",
    "accountName": ""
  },
  "leadDetails": {
    "leadStatusUuid": ""
  }
}'
```

###
`200`
```json
{
  "uuid": "",
  "created": "",
  "updated": "",
  "email": "",
  "verificationStatus": "NEW",
  "type": "RETAIL",
  "personalDetails": {
    "firstname": "",
    "lastname": "",
    "dateOfBirth": "",
    "citizenship": "",
    "language": "",
    "maritalStatus": "",
    "passport": {
      "number": "",
      "country": ""
    },
    "taxIdentificationNumber": ""
  },
  "contactDetails": {
    "phoneNumber": "",
    "faxNumber": "",
    "toContact": {
      "toContactDate": "",
      "alreadyContacted": false
    }
  },
  "accountConfiguration": {
    "partnerId": null,
    "branchUuid": "",
    "roleUuid": "",
    "accountManager": {
      "uuid": "",
      "email": "",
      "name": ""
    },
    "ibParentTradingAccountUuid": "",
    "crmUserScope": {
      "branchScope": [
        ""
      ],
      "managerPools": [
        ""
      ]
    },
    "accountTypeContact": false
  },
  "addressDetails": {
    "country": "",
    "state": "",
    "city": "",
    "postCode": "",
    "address": ""
  },
  "bankingDetails": {
    "bankAddress": "",
    "bankSwiftCode": "",
    "bankAccount": "",
    "bankName": "",
    "accountName": ""
  },
  "leadDetails": {
    "statusUuid": "",
    "source": "",
    "providerUuid": "",
    "becomeActiveClientTime": ""
  }
}
```


#### Header Parameters

- **Authorization**: `string`

#### Path Parameters

- **accountUuid**: `string (uuid)`
  - **REQUIRED**
  - Unique identifier for a user account, used to fetch specific account data.

#### Body Parameters

- **verificationStatus**: `string`
  - The status of the user's email verification.
  - **Enum values**: NEW, REJECTED, VERIFIED, BLOCKED, PENDING_VERIFICATION, UNVERIFIED
- **clientType**: `string`
  - The type of account, with possible values being RETAIL, PROFESSIONAL, and EXPERIENCED.
  - **Enum values**: RETAIL, PROFESSIONAL, EXPERIENCED
- **personalDetails**: `object`
  - **child attributes**:
    - **firstname**: `string`
      - User’s first name.
    - **lastname**: `string`
      - User’s last name.
    - **dateOfBirth**: `string (date)`
      - User’s date of birth.
    - **citizenship**: `string`
      - Citizenship of the user.
    - **language**: `string`
      - Preferred language of the user.
    - **maritalStatus**: `string`
      - Marital status of the user.
    - **passport**: `object`
      - An object containing passport details such as number and country of issuance.
      - **child attributes**:
        - **number**: `string`
          - Passport number.
        - **country**: `string`
          - Country of issuance.
    - **taxIdentificationNumber**: `string`
      - User's tax identification number.
- **contactDetails**: `object`
  - **child attributes**:
    - **phoneNumber**: `string`
      - User’s phone number.
    - **faxNumber**: `string`
      - User’s fax number.
- **toContact**: `object`
  - An object related to contacting details.
  - **child attributes**:
    - **toContactDate**: `string (date-time)`
      - The date scheduled to contact the user.
    - **alreadyContacted**: `boolean`
      - **REQUIRED**
      - A boolean indicating whether the user has already been contacted.
- **accountConfiguration**: `object`
  - Object with details about account configuration.
  - **child attributes**:
    - **branchUuid**: `string (uuid)`
      - Identifier for the branch managing the account.
    - **roleUuid**: `string (uuid)`
      - Identifier for the account's assigned role, defining permissions.
    - **ibParentTradingAccountUuid**: `string (uuid)`
      - Identifier for the parent IB trading account, linking related accounts.
    - **accountManagerUuid**: `string (uuid)`
      - Identifier for the account manager in charge.
    - **accountTypeContact**: `boolean`
      - Flags if contact details are required for the account.
- **addressDetails**: `object`
  - **child attributes**:
    - **country**: `string`
      - The country where the user resides.
    - **state**: `string`
      - The state where the user resides.
    - **city**: `string`
      - The city where the user resides.
    - **postCode**: `string`
      - Postal code of the user’s address.
    - **address**: `string`
      - Detailed street address of the user.
- **bankingDetails**: `object`
  - **child attributes**:
    - **bankAddress****: `string`
      - The address of the bank.
    - **bankSwiftCode**: `string`
      - SWIFT code of the bank.
    - **bankAccount**: `string`
      - The user's bank account number.
    - **bankName**: `string`
      - Name of the bank where the user's account is held.
    - **accountName**: `string`
      - The name associated with the bank account.
- **leadDetails**: `object**
  - **child attributes**:
    - **leadStatusUuid**: `string (uuid)`
      - Unique identifier representing the current status of a lead within the CRM system, tracking the lead's progression through the sales pipeline.

#### Responses

##### 200 OK

**Response Attributes:**

- **uuid**: `string (uuid)`
  - **REQUIRED**
  - Unique identifier for the account.
- **created**: `string (date-time)`
  - **REQUIRED**
  - Timestamps for when the account was created.
- **updated**: `string (date-time)`
  - **REQUIRED**
  - Timestamps for when the account was last updated.
- **email**: `string**
  - **REQUIRED**
  - Email address associated with the account.
- **verificationStatus**: `string`
  - **REQUIRED**
  - Status of the account's verification.
  - **Enum values**: NEW, REJECTED, VERIFIED, BLOCKED, PENDING_VERIFICATION, UNVERIFIED
- **type**: `string**
  - Account type, options are RETAIL, PROFESSIONAL, EXPERIENCED.
  - **Enum values**: RETAIL, PROFESSIONAL, EXPERIENCED
- **personalDetails**: `object**
  - **REQUIRED**
  - **child attributes**:
    - **firstname**: `string**
      - First name of the account holder.
    - **lastname**: `string**
      - Last name of the account holder.
    - **dateOfBirth**: `string (date)**
      - Birthdate of the account holder.
    - **citizenship**: `string**
      - Citizenship of the account holder.
    - **language**: `string**
      - Preferred language of the account holder.
    - **maritalStatus**: `string**
      - Marital status of the account holder.
    - **passport**: `object**
      - Passport information.
      - **child attributes**:
        - **number**: `string**
          - Passport number.
        - **country**: `string**
          - Country of issuance.
    - **taxIdentificationNumber**: `string**
- **contactDetails**: `object**
  - **REQUIRED**
  - Contact information.
  - **child attributes**:
    - **phoneNumber**: `string**
      - Phone number of the account holder.
    - **faxNumber**: `string**
      - Fax number.
- **toContact**: `object**
  - Preferences about contacting the account holder.
  - **child attributes**:
    - **toContactDate**: `string (date-time)**
      - Preferred date and time to contact.
    - **alreadyContacted**: `boolean**
      - **REQUIRED**
      - Required. Indicates if the account holder has been contacted previously.
- **accountConfiguration**: `object**
  - **REQUIRED**
  - Configuration details for the account.
  - **child attributes**:
    - **partnerId**: `integer (int64)**
      - **REQUIRED**
      - Partner identifier associated with the account.
    - **branchUuid**: `string (uuid)**
      - **REQUIRED**
      - Branch identifier where the account is managed.
    - **roleUuid**: `string (uuid)**
      - Role identifier associated with the account.
- **accountManager**: `object**
  - Manager details.
  - **child attributes**:
    - **uuid**: `string (uuid)**
      - Unique identifier for the account manager.
    - **email**: `string**
      - Email address of the account manager.
    - **name**: `string**
      - Name of the account manager.
- **ibParentTradingAccountUuid**: `string (uuid)**
  - Identifier for a parent IB trading account linked to the account.
- **crmUserScope**: `object**
  - CRM user's scope.
  - **child attributes**:
    - **branchScope**: `array**
      - Allow unique items: true
    - **managerPools**: `array**
      - Allow unique items: true
    - **accountTypeContact**: `boolean**
- **addressDetails**: `object**
  - **REQUIRED**
  - **child attributes**:
    - **country**: `string**
      - Country location.
    - **state**: `string**
      - State or region.
    - **city**: `string**
      - City or locality.
    - **postCode**: `string**
      - Postal code.
    - **address**: `string**
      - Street address.
- **bankingDetails**: `object**
  - **REQUIRED**
  - Provides essential financial information for transactions.
  - **child attributes**:
    - **bankAddress**: `string**
      - Physical address of the bank.
    - **bankSwiftCode**: `string**
      - SWIFT code for international banking.
    - **bankAccount**: `string**
      - Bank account number.
    - **bankName**: `string**
      - Name of the bank.
    - **accountName**: `string**
      - Name associated with the bank account.
- **leadDetails**: `object**
  - **REQUIRED**
  - Details for tracking and managing leads.
  - **child attributes**:
    - **statusUuid**: `string (uuid)**
      - Unique ID for the lead's status.
    - **source**: `string**
      - Origin of the lead.
    - **providerUuid**: `string (uuid)**
      - ID of the lead provider.
    - **becomeActiveClientTime**: `string (date-time)**
      - Expected time for lead conversion to active client.

### Change Account's Password

Using this endpoint, you can change the Account's password.

### POST /v1/change-password
```sh
curl --location 'https://broker-api-demo.match-trader.com/v1/change-password' \
--header 'Authorization: Provide you token here' \
--header 'Content-Type: application/json' \
--data '{
  "accountUuid": "",
  "newPassword": ""
}'
```

### Response
`204`
```json
No content
```

#### Header Parameters

- **Authorization**: `string`

#### Body Parameters

- **accountUuid**: `string (uuid)`
  - **REQUIRED**
- **newPassword**: `string**
  - **REQUIRED**

#### Responses

##### 204 No Content


## Trading Accounts

### Get Trading Accounts

With the Get Accounts endpoint, you can retrieve detailed information about all Trading Accounts within the system.

### GET /v1/trading-accounts?query=&page=&size=&sort=&from=&to=
```sh
curl --location 'https://broker-api-demo.match-trader.com/v1/trading-accounts' \
--header 'Authorization: Provide you token here' \
--header 'Content-Type: application/json'
```

### Response
`200`
```json
{
  "content": [
    {
      "uuid": "",
      "login": "",
      "created": "",
      "accountInfo": {
        "uuid": "",
        "email": ""
      },
      "offerUuid": "",
      "systemUuid": "",
      "financeInfo": {
        "balance": null,
        "equity": null,
        "profit": null,
        "netProfit": null,
        "margin": null,
        "freeMargin": null,
        "marginLevel": null,
        "credit": null,
        "currency": "",
        "currencyPrecision": null
      }
    }
  ],
  "totalPages": null,
  "totalElements": null,
  "number": null,
  "size": null
}
```

#### Header Parameters

- **Authorization**: `string`

#### Query Parameters

- **query**: `string`
  - Filter accounts based on specific criteria.
- **page**: `integer (int32)`
  - Page number to access a subset of account records.
  - **Default value**: `0`
- **size**: `integer (int32)`
  - Number of account records per page.
  - **Default value**: `10`
- **sort**: `string`
  - Sorting order of the account records.
  - **Default value**: `created,desc`
- **from**: `string (date-time)`
  - Filter accounts from this starting date and time.
- **to**: `string (date-time)`
  - Filter accounts up to this ending date and time.

#### Responses

##### 200 OK

**Response Attributes:**

- **content**: `array`
  - **child attributes**:
    - **uuid**: `string (uuid)`
      - Unique identifier for the trading account.
    - **login**: `string`
      - Login ID associated with the trading account.
    - **created**: `string (date-time)`
      - Date and time the account was established.
    - **accountInfo**: `object`
      - **child attributes**:
        - **uuid**: `string (uuid)`
          - **REQUIRED**
          - Unique identifier for the account info.
        - **email**: `string`
          - **REQUIRED**
          - Email address linked to the account.
        - **offerUuid**: `string (uuid)`
          - Identifier for the associated offer.
        - **systemUuid**: `string (uuid)`
          - Identifier for the system managing the account.
    - **financeInfo**: `object`
      - **child attributes**:
        - **balance**: `number`
          - Trading Account's Balance.
        - **equity**: `number`
          - Trading Account's Equity.
        - **profit**: `number`
          - Trading Account's Profit.
        - **netProfit**: `number`
          - Trading Account's Net Profit.
        - **margin**: `number`
          - Trading Account's Margin.
        - **freeMargin**: `number`
          - Trading Account's Free Margin.
        - **marginLevel**: `number`
          - Trading Account's Margin Level.
        - **credit**: `number`
          - Trading Account's Credit.
        - **currency**: `string`
          - Trading Account's Currency.
        - **currencyPrecision**: `integer (int32)`
          - Trading Account's Currency precision.
- **totalPages**: `integer (int32)`
  - Total number of result pages.
- **totalElements**: `integer (int64)`
  - Total number of accounts.
- **number**: `integer (int32)`
  - Current page number.
- **size**: `integer (int32)`
  - Number of records per page.

### Get Trading Account by login

With the Get Trading Account by login endpoint, you can retrieve detailed information about a particular Trading Account in the system using the Trading Account's login.

### GET /v1/trading-account?systemUuid=ea1fe126-f0e1-4682-8a7d-d7277ebc6e18&login=
```sh
curl --location 'https://broker-api-demo.match-trader.com/v1/trading-account?systemUuid=ea1fe126-f0e1-4682-8a7d-d7277ebc6e18&login=' \
--header 'Authorization: Provide you token here' \
--header 'Content-Type: application/json'
```

### Response
`200`
```json
{
  "uuid": "",
  "login": "",
  "created": "",
  "accountInfo": {
    "uuid": "",
    "email": ""
  },
  "offerUuid": "",
  "systemUuid": "",
  "financeInfo": {
    "balance": null,
    "equity": null,
    "profit": null,
    "netProfit": null,
    "margin": null,
    "freeMargin": null,
    "marginLevel": null,
    "credit": null,
    "currency": "",
    "currencyPrecision": null
  }
}
```

#### Header Parameters

- **Authorization**: `string`

#### Query Parameters

- **systemUuid**: `string`
  - **REQUIRED**
  - Provided System UUID.
- **login**: `string`
  - **REQUIRED**
  - Trading Account login.

#### Responses

##### 200 OK

**Response Attributes:**

- **uuid**: `string (uuid)`
  - Unique identifier for the trading account.
- **login**: `string`
  - Login ID associated with the trading account.
- **created**: `string (date-time)`
  - Date and time the account was established.
- **accountInfo**: `object`
  - **child attributes**:
    - **uuid**: `string (uuid)`
      - Unique identifier for the account info.
    - **email**: `string`
      - Email address linked to the account.
    - **offerUuid**: `string (uuid)`
      - Identifier for the associated offer.
    - **systemUuid**: `string (uuid)`
      - Identifier for the system managing the account.
- **financeInfo**: `object`
  - **child attributes**:
    - **balance**: `number`
      - Trading Account's Balance.
    - **equity**: `number`
      - Trading Account's Equity.
    - **profit**: `number`
      - Trading Account's Profit.
    - **netProfit**: `number`
      - Trading Account's Net Profit.
    - **margin**: `number`
      - Trading Account's Margin.
    - **freeMargin**: `number`
      - Trading Account's Free Margin.
    - **marginLevel**: `number`
      - Trading Account's Margin Level.
    - **credit**: `number`
      - Trading Account's Credit.
    - **currency**: `string`
      - Trading Account's Currency.
    - **currencyPrecision**: `integer (int32)`
      - Trading Account's Currency precision.

### Create New Trading Account

In this section, you can find the endpoint for creating new Trading Accounts in the system.

### POST /v1/accounts/{accountUuid}/trading-accounts
```sh
curl --location --globoff 'https://broker-api-demo.match-trader.com/v1/accounts/{accountUuid}/trading-accounts' \
--header 'Authorization: Provide you token here' \
--header 'Content-Type: application/json' \
--data '{
  "offerUuid": "",
  "commissionUuid": ""
}'
```

### Response
`200`
```json
{
  "uuid": "",
  "created": "",
  "updated": "",
  "login": "",
  "offerUuid": "",
  "status": "",
  "commissionUuid": ""
}
```

#### Header Parameters

- **Authorization**: `string`

#### Path Parameters

- **accountUuid**: `string (uuid)`
  - **REQUIRED**
  - A unique identifier for the account under which the trading account will be created.

#### Body Parameters

- **offerUuid**: `string (uuid)`
  - **REQUIRED**
  - Identifier for the specific offer that applies to the new trading account.
- **commissionUuid**: `string (uuid)`
  - Identifier for the commission structure that will be applied to the trading account.

#### Responses

##### 200 OK

**Response Attributes:**

- **uuid**: `string (uuid)`
  - **REQUIRED**
  - The unique identifier assigned to the newly created trading account.
- **created**: `string (date-time)`
  - **REQUIRED**
  - Timestamp indicating when the trading account was created.
- **updated**: `string (date-time)`
  - **REQUIRED**
  - Timestamp indicating the last update to the trading account.
- **login**: `string`
  - **REQUIRED**
  - The login ID associated with the trading account.
- **offerUuid**: `string (uuid)`
  - **REQUIRED**
  - The offer identifier linked to the trading account upon creation.
- **status**: `string`
  - The status of the trading account upon creation.
- **commissionUuid**: `string (uuid)`
  - The commission structure identifier associated with the trading account.


## Payments

In the Payments section you can find endpoints for:

- Retrieving Payment Gateways list with details about each Payment Gateway
- Retrieving Deposits and Withdrawals
- Making Manual payments
- Credit in and out

### Get Payment Gateways

A Payment Gateway is a resource responsible for all payment operations in the system. All Deposits and Withdrawals are processed within the frame of a Payment Gateway and following the Payment Gateway configuration.

`Payment Gateways must be assigned to a Branch to be visible to users.`

### GET /v1/payment-gateways?sort=&from=&to=
```sh
curl --location 'https://broker-api-demo.match-trader.com/v1/payment-gateways' \
--header 'Authorization: Provide you token here' \
--header 'Content-Type: application/json'
```

### Response 
`200`
```json
{
  "paymentGateways": [
    {
      "uuid": "",
      "name": "",
      "method": "",
      "supportedOperationTypes": {
        "deposits": false,
        "withdrawals": false
      },
      "paymentSettings": {
        "currency": "",
        "feesConfig": {
          "processingFee": null,
          "depositFee": null,
          "withdrawalFee": null
        }
      },
      "processingSettings": {
        "autoDeposits": false,
        "autoWithdrawals": false,
        "verificationRequired": false
      }
    }
  ]
}
```

#### Header Parameters

- **Authorization**: `string`

#### Query Parameters

- **sort**: `string`
  - Specifies the order in which payment gateway records should be sorted. The default sorting is by creation date in descending order ("created,desc").
  - **Default value**: `created,desc`
- **from**: `string (date-time)`
  - Start date and time for filtering payment gateways based on their creation or update time.
- **to**: `string (date-time)`
  - End date and time for the same filtering criteria.

#### Responses

##### 200 OK

**Response Attributes:**

- **paymentGateways**: `array`
  - An array of payment gateway objects,
  - **child attributes**:
    - **uuid**: `string (uuid)`
      - Unique identifier for the payment gateway.
    - **name**: `string`
      - Name of the payment gateway.
    - **method**: `string`
      - The method used by the payment gateway (e.g., credit card, bank transfer).
    - **supportedOperationTypes**: `object`
      - Types of operations supported by the gateway
      - **child attributes**:
        - **deposits**: `boolean`
          - Indicates whether the gateway supports deposits.
        - **withdrawals**: `boolean`
          - Indicates whether the gateway supports withdrawals.
    - **paymentSettings**: `object`
      - Configuration related to payment settings
      - **child attributes**:
        - **currency**: `string`
          - The currency used by the payment gateway.
        - **feesConfig**: `object`
          - Fee configuration for the payment operations:
          - **child attributes**:
            - **processingFee**: `number`
              - General fee for processing payments.
            - **depositFee**: `number`
              - Specific fee for deposit operations.
            - **withdrawalFee**: `number`
              - Specific fee for withdrawal operations.
        - **processingSettings**: `object`
          - Settings related to the processing of transactions
          - **child attributes**:
            - **autoDeposits**: `boolean`
              - Indicates whether deposits are processed automatically.
            - **autoWithdrawals**: `boolean`
              - Indicates whether withdrawals are processed automatically.
            - **verificationRequired**: `boolean`
              - Specifies if verification is required for processing transactions.

### Get Deposits

With this endpoint, you can retrieve information about deposits made in the system. You can also request deposits for a specific account using query.

### GET /v1/deposits?query=&page=&size=&sort=&from=&to=
```sh
curl --location 'https://broker-api-demo.match-trader.com/v1/deposits' \
--header 'Authorization: Provide you token here' \
--header 'Content-Type: application/json'
```

### Response
`200`
```json
{
  "content": [
    {
      "uuid": "",
      "partnerId": null,
      "created": "",
      "accountInfo": {
        "accountUuid": "",
        "email": "",
        "personalDetails": {
          "firstname": "",
          "lastname": ""
        },
        "accountManager": {
          "uuid": "",
          "email": "",
          "name": ""
        },
        "tradingAccount": {
          "uuid": "",
          "login": "",
          "offerUuid": ""
        },
        "leadDetails": {
          "statusUuid": "",
          "source": "",
          "providerUuid": "",
          "becomeActiveClientTime": ""
        }
      },
      "paymentRequestInfo": {
        "financialDetails": {
          "status": "",
          "amount": null,
          "netAmount": null,
          "currency": ""
        },
        "paymentGatewayDetails": {
          "uuid": "",
          "name": ""
        },
        "additionalInfo": {
          "walletAddress": "",
          "reference": "",
          "paymentId": ""
        }
      }
    }
  ],
  "totalPages": null,
  "totalElements": null,
  "number": null,
  "size": null
}
```

#### Header Parameters

- **Authorization**: `string`

#### Query Parameters

- **query**: `string`
  - Search term to filter the deposits.
- **page**: `integer (int32)`
  - Specifies the page number of the deposit list.
  - **Default value**: `0`
- **size**: `integer (int32)`
  - Determines the number of deposit records per page.
  - **Default value**: `10`
- **sort**: `string`
  - Specifies the sorting order of the results, such as by creation date in descending order (created,desc).
  - **Default value**: `created,desc`
- **from**: `string (date-time)`
  - Filters the deposits starting from this date.
- **to**: `string (date-time)`
  - Filters the deposits up to this date.

#### Responses

##### 200 OK

**Response Attributes:**

- **content**: `array`
  - Contains the deposit records.
  - **child attributes**:
    - **uuid**: `string (uuid)`
      - Unique identifier for the deposit.
    - **partnerId**: `integer (int64)`
      - Identifier of the partner associated with the deposit.
    - **created**: `string (date-time)`
      - Date and time when the deposit was created.
    - **accountInfo**: `object`
      - Information about the account associated with the deposit, including accountUuid and email.
      - **child attributes**:
        - **accountUuid**: `string (uuid)`
          - Unique identifier for the account.
        - **email**: `string`
          - Email address associated with the account.
    - **personalDetails**: `object`
      - Personal information of the individual associated with the account.
      - **child attributes**:
        - **firstname**: `string`
          - First name of the account holder.
        - **lastname**: `string`
          - Last name of the account holder.
    - **accountManager**: `object`
      - Details about the manager overseeing the account.
      - **child attributes**:
        - **uuid**: `string (uuid)`
          - Unique identifier for the account manager.
        - **email**: `string`
          - Email address of the account manager.
        - **name**: `string`
          - Full name of the account manager.
    - **tradingAccount**: `object`
      - Information about the trading account linked to the deposit.
      - **child attributes**:
        - **uuid**: `string (uuid)`
          - Unique identifier for the trading account.
        - **login**: `string`
          - Login details for the trading account.
        - **offerUuid**: `string (uuid)`
          - Identifier for the offer associated with the trading account.
    - **leadDetails**: `object`
      - Details regarding the leads related to the account.
      - **child attributes**:
        - **statusUuid**: `string (uuid)`
          - Unique identifier representing the status of the lead.
        - **source**: `string`
          - Source from where the lead was generated.
        - **providerUuid**: `string (uuid)`
          - Identifier for the provider related to the lead.
        - **becomeActiveClientTime**: `string (date-time)`
          - Timestamp when the lead became an active client.
    - **paymentRequestInfo**: `object`
      - Details about payment requests associated with the deposit.
      - **child attributes**:
        - **financialDetails**: `object`
          - Financial specifics of the payment.
          - **child attributes**:
            - **status**: `string`
              - Current status of the payment.
            - **amount**: `number`
              - Total amount of the payment.
            - **netAmount**: `number`
              - Net amount received after deductions.
            - **currency**: `string`
              - Currency in which the payment was made.
        - **paymentGatewayDetails**: `object`
          - Information about the payment gateway used.
          - **child attributes**:
            - **uuid**: `string (uuid)`
              - Unique identifier for the payment gateway.
            - **name**: `string`
              - Name of the payment gateway.
        - **additionalInfo**: `object`
          - Additional details related to the payment.
          - **child attributes**:
            - **walletAddress**: `string`
              - Wallet address used for the payment.
            - **reference**: `string`
              - Reference number for the payment.
            - **paymentId**: `string (uuid)`
              - Unique identifier for the payment transaction.
- **totalPages**: `integer (int32)`
  - Total number of pages available.
- **totalElements**: `integer (int64)`
  - Total number of deposit entries available.
- **number**: `integer (int32)`
  - Current page number.
- **size**: `integer (int32)`
  - Number of records per page.

### Get Withdrawals

With this endpoint, you can retrieve information about withdrawals made in the system. You can also request withdrawals for a specific account using query.

#### Header Parameters

- **Authorization**: `string`

#### Query Parameters

- **query**: `string`
  - Search term to filter the withdrawal.
- **page**: `integer (int32)`
  - Specifies the page number of the withdrawal list.
  - **Default value**: `0`
- **size**: `integer (int32)`
  - Determines the number of withdrawal records per page.
  - **Default value**: `10`
- **sort**: `string`
  - Specifies the sorting order of the results, such as by creation date in descending order (created,desc).
  - **Default value**: `created,desc`
- **from**: `string (date-time)`
  - Filters the withdrawals starting from this date.
- **to**: `string (date-time)`
  - Filters the withdrawals up to this date.

#### Responses

##### 200 OK

**Response Attributes:**

- **content**: `array`
  - Contains the withdrawal records.
  - **child attributes**:
    - **uuid**: `string (uuid)`
      - Unique identifier for the withdrawal.
    - **partnerId**: `integer (int64)`
      - Identifier of the partner associated with the withdrawal.
    - **created**: `string (date-time)`
      - Date and time when the withdrawal was created.
    - **accountInfo**: `object`
      - Information about the account associated with the withdrawal, including accountUuid and email.
      - **child attributes**:
        - **accountUuid**: `string (uuid)`
          - Unique identifier for the account.
        - **email**: `string`
          - Email address associated with the account.
    - **personalDetails**: `object`
      - Personal information of the individual associated with the account.
      - **child attributes**:
        - **firstname**: `string`
          - First name of the account holder.
        - **lastname**: `string`
          - Last name of the account holder.
    - **accountManager**: `object`
      - Details about the manager overseeing the account.
      - **child attributes**:
        - **uuid**: `string (uuid)`
          - Unique identifier for the account manager.
        - **email**: `string`
          - Email address of the account manager.
        - **name**: `string`
          - Full name of the account manager.
    - **tradingAccount**: `object`
      - Information about the trading account linked to the withdrawal.
      - **child attributes**:
        - **uuid**: `string (uuid)`
          - Unique identifier for the trading account.
        - **login**: `string`
          - Login details for the trading account.
        - **offerUuid**: `string (uuid)`
          - Identifier for the offer associated with the trading account.
    - **leadDetails**: `object`
      - Details regarding the leads related to the account.
      - **child attributes**:
        - **statusUuid**: `string (uuid)`
          - Unique identifier representing the status of the lead.
        - **source**: `string`
          - Source from where the lead was generated.
        - **providerUuid**: `string (uuid)`
          - Identifier for the provider related to the lead.
        - **becomeActiveClientTime**: `string (date-time)`
          - Timestamp when the lead became an active client.
    - **paymentRequestInfo**: `object`
      - Details about payment requests associated with the withdrawal.
      - **child attributes**:
        - **financialDetails**: `object`
          - This object outlines the financial specifics of the withdrawal.
          - **child attributes**:
            - **status**: `string`
              - Current status of the withdrawal.
            - **amount**: `number`
              - Gross amount of the withdrawal before deductions.
            - **netAmount**: `number`
              - Net amount received after any fees or deductions.
            - **currency**: `string`
              - Currency type of the withdrawal (e.g., USD, BTC).
        - **paymentGatewayDetails**: `object`
          - Information about the payment gateway used.
          - **child attributes**:
            - **uuid**: `string (uuid)`
              - Unique identifier for the payment gateway.
            - **name**: `string`
              - Name of the payment gateway.
        - **additionalInfo**: `object`
          - This object includes supplementary details about the withdrawal.
          - **child attributes**:
            - **walletAddress**: `string`
              - The digital wallet address for the withdrawal destination.
            - **reference**: `string`
              - A reference number for transaction identification.
            - **paymentId**: `string (uuid)`
              - Unique identifier for the transaction.
- **totalPages**: `integer (int32)`
  - Total number of pages available.
- **totalElements**: `integer (int64)`
  - Total number of withdrawal entries available.
- **number**: `integer (int32)`
  - Current page number.
- **size**: `integer (int32)`
  - Number of records per page.

### Manual Deposit

Using this endpoint, you can deposit funds to a Trading Account.

### POST /v1/deposits/manual?systemUuid=&login=
```sh
curl --location 'https://broker-api-demo.match-trader.com/v1/deposits/manual' \
--header 'Authorization: Provide you token here' \
--header 'Content-Type: application/json' \
--data '{
  "systemUuid": "",
  "login": "",
  "paymentGatewayUuid": "",
  "amount": null,
  "comment": ""
}'
```

### Response
`200`
```json
{
  "uuid": "",
  "created": "",
  "status": "NEW",
  "transactionInfo": {
    "amount": null,
    "netAmount": null,
    "currency": ""
  }
}
```
#### Header Parameters

- **Authorization**: `string`

#### Query Parameters

- **systemUuid**: `string`
  - The unique identifier for the system where the deposit is to be made.
- **login**: `string`
  - Login details associated with the Trading Account.

#### Body Parameters

- **systemUuid**: `string`
  - **REQUIRED**
  - The unique system identifier; this is required.
- **login**: `string`
  - **REQUIRED**
  - The login identifier for the Trading Account; this is required.
- **paymentGatewayUuid**: `string (uuid)`
  - **REQUIRED**
  - Unique identifier for the payment gateway; required for processing the deposit.
- **amount**: `number`
  - **REQUIRED**
  - The amount of money to deposit; required.
- **comment**: `string`
  - A comment or note accompanying the deposit.

#### Responses

##### 200 OK

**Response Attributes:**

- **uuid**: `string (uuid)`
  - **REQUIRED**
  - Unique identifier for the deposit transaction; required.
- **created**: `string (date-time)`
  - **REQUIRED**
  - Timestamp when the deposit was made; required.
- **status**: `string`
  - **REQUIRED**
  - Current status of the deposit.
  - **Enum values**: NEW, AWAITING_CONFIRMATION, PROCESSING, BOOKED, PROCESSING_PAYMENT, DONE, REJECTED, FAILED, FAILED_PAYMENT, CANCELLED_BY_USER
- **transactionInfo**: `object`
  - **child attributes**:
    - **amount**: `number`
      - Gross amount deposited.
    - **netAmount**: `number`
      - Net amount after processing fees or other deductions.
    - **currency**: `string`
      - Currency in which the deposit was made.

### Manual Withdrawal

Using this endpoint, you can withdraw funds from a Trading Account.

### POST /v1/withdrawals/manual
```sh
curl --location 'https://broker-api-demo.match-trader.com/v1/withdrawals/manual' \
--header 'Authorization: Provide you token here' \
--header 'Content-Type: application/json' \
--data '{
  "systemUuid": "",
  "login": "",
  "paymentGatewayUuid": "",
  "amount": null,
  "comment": ""
}'
```

### Response
`200`
```json
{
  "uuid": "",
  "created": "",
  "status": "NEW",
  "transactionInfo": {
    "amount": null,
    "netAmount": null,
    "currency": ""
  }
}
```

#### Header Parameters

- **Authorization**: `string`

#### Body Parameters

- **systemUuid**: `string (uuid)`
  - **REQUIRED**
  - The unique identifier for the system from which funds are withdrawn; required.
- **login**: `string`
  - **REQUIRED**
  - Login details for the Trading Account from which the withdrawal is made; required.
- **paymentGatewayUuid**: `string (uuid)`
  - **REQUIRED**
  - Identifier for the payment gateway used for the transaction; required.
- **amount**: `number`
  - **REQUIRED**
  - The amount of money to be withdrawn; required.
- **comment**: `string`
  - A comment or note accompanying the withdrawal.

#### Responses

##### 200 OK

**Response Attributes:**

- **uuid**: `string (uuid)`
  - **REQUIRED**
  - Unique identifier for the withdrawal transaction; required.
- **created**: `string (date-time)`
  - **REQUIRED**
  - Timestamp of when the withdrawal was initiated; required.
- **status**: `string`
  - **REQUIRED**
  - The current status of the withdrawal.
  - **Enum values**: NEW, AWAITING_CONFIRMATION, PROCESSING, BOOKED, PROCESSING_PAYMENT, DONE, REJECTED, FAILED, FAILED_PAYMENT, CANCELLED_BY_USER
- **transactionInfo**: `object`
  - **child attributes**:
    - **amount**: `number`
    - **netAmount**: `number`
    - **currency**: `string`

### Credit In

Using this endpoint, you can add a credit to a Trading Account.

### POST /v1/credit/in
```sh
curl --location 'https://broker-api-demo.match-trader.com/v1/credit/in' \
--header 'Authorization: Provide you token here' \
--header 'Content-Type: application/json' \
--data '{
  "systemUuid": "",
  "login": "",
  "amount": null,
  "comment": ""
}'
```

### Response
`204`
`No content`

#### Header Parameters

- **Authorization**: `string`

#### Body Parameters

- **systemUuid**: `string`
  - **REQUIRED**
  - The unique system identifier for the account; required.
- **login**: `string (uuid)`
  - **REQUIRED**
  - The unique login identifier for the Trading Account to which credit is added; required.
- **amount**: `number`
  - **REQUIRED**
  - The amount of credit to be added; required.
- **comment**: `string`
  - An optional comment describing the reason for the credit.

#### Responses

##### 204 No Content

### Credit Out

Using this endpoint, you can remove a credit from a Trading Account.

### POST /v1/credit/out
```sh
curl --location 'https://broker-api-demo.match-trader.com/v1/credit/out' \
--header 'Authorization: Provide you token here' \
--header 'Content-Type: application/json' \
--data '{
  "systemUuid": "",
  "login": "",
  "amount": null,
  "comment": ""
}'
```

### Response
`204`
`No Content`

#### Header Parameters

- **Authorization**: `string`

#### Body Parameters

- **systemUuid**: `string`
  - **REQUIRED**
  - The unique system identifier for the account; required.
- **login**: `string (uuid)`
  - **REQUIRED**
  - The unique login identifier for the Trading Account from which credit is removed; required.
- **amount**: `number`
  - **REQUIRED**
  - The amount of credit to be removed; required.
- **comment**: `string`
  - An optional comment describing the reason for the debit.

#### Responses

##### 204 No Content


## Trading

The Trading section allows accessing and managing various trading functionalities within the system. With this section, you can place orders and monitor trading activity seamlessly. This section provides the necessary tools for users to efficiently execute trades and stay informed about market conditions.

### Get Symbols

Using this endpoint, you can retrieve information about various symbols (instruments) within the system, including details such as symbol names, currency, trading hours, and more.

### GET /v1/symbols?systemUuid=&group=
```sh
curl --location 'https://broker-api-demo.match-trader.com/v1/symbols?systemUuid=&group=' \
--header 'Authorization: Provide you token here' \
--header 'Content-Type: application/json'
```

### Response
`200`
```json
{
  "effectiveInstruments": [
    {
      "isSessionOpen": false,
      "symbol": "",
      "alias": "",
      "baseCurrency": "",
      "quoteCurrency": "",
      "type": "FOREX",
      "pricePrecision": null,
      "volumePrecision": null,
      "swapBuy": null,
      "swapSell": null,
      "swapType": "PIPS",
      "volumeMin": null,
      "volumeStep": null,
      "volumeMax": null,
      "commissionMin": null,
      "contractSize": null,
      "sizeOfOnePoint": null,
      "multiplier": null,
      "divider": null,
      "leverage": null,
      "bidMarkup": null,
      "askMarkup": null,
      "freezeLevel": null,
      "stopsLevel": null,
      "description": "",
      "tags": [
        ""
      ],
      "tradingHours": [
        {
          "dayNumber": null,
          "openHours": null,
          "openMinutes": null,
          "openSeconds": null,
          "closeHours": null,
          "closeMinutes": null,
          "closeSeconds": null
        }
      ],
      "isFixedLeverage": false,
      "terminationType": "EXPIRATION",
      "terminationDate": "",
      "sessionOpen": false
    }
  ]
}
```

#### Header Parameters

- **Authorization**: `string`

#### Query Parameters

- **systemUuid**: `string (uuid)`
  - **REQUIRED**
  - The unique system identifier; required to specify which system's symbols to retrieve.
- **group**: `string`
  - **REQUIRED**
  - Specifies the group or category of symbols to be fetched; required.

#### Responses

##### 200 OK

**Response Attributes:**

- **effectiveInstruments**: `array`
  - An array of objects, each representing a trading instrument.
  - **child attributes**:
    - **isSessionOpen**: `boolean`
      - Indicates if the trading session for the symbol is currently open.
    - **symbol**: `string`
      - The unique identifier of the symbol.
    - **alias**: `string`
      - An alternative name or alias for the symbol.
    - **baseCurrency**: `string`
      - The base currency of the trading instrument.
    - **quoteCurrency**: `string`
      - The currency against which the instrument is valued.
    - **type**: `string`
      - The type of the instrument, such as FOREX, CFD.
      - **Enum values**: FOREX, CFD
    - **pricePrecision**: `integer (int32)`
      - The number of decimal places used in the symbol's price.
    - **volumePrecision**: `integer (int32)`
      - The precision of volume measurements for the instrument.
    - **swapBuy**: `number`
      - The swap rate applied to overnight buy positions.
    - **swapSell**: `number`
      - The swap rate applied to overnight sell positions.
    - **swapType**: `string`
      - Indicates the type of swap calculation (PIPS, PERCENTS, NOT_DEFINED, UNKNOWN).
      - **Enum values**: PIPS, PERCENTS, NOT_DEFINED, UNKNOWN
    - **volumeMin**: `number`
      - The minimum trade volume for the instrument.
    - **volumeStep**: `number`
      - The step increment for trade volume adjustments.
    - **volumeMax**: `number`
      - The maximum trade volume for the instrument.
    - **commissionMin**: `number`
      - Minimum commission for transactions with this symbol.
    - **contractSize**: `number`
      - The size of a single contract for the symbol.
    - **sizeOfOnePoint**: `number`
      - Value change of one point movement in price.
    - **multiplier**: `integer (int64)`
      - A multiplier applied to the base values for this symbol.
    - **divider**: `integer (int64)`
      - A divider applied for calculating precision or other metrics.
    - **leverage**: `number (double)`
      - The leverage available for the symbol.
    - **bidMarkup**: `number`
      - Additional markup applied to the bid price.
    - **askMarkup**: `number`
      - Additional markup applied to the ask price.
    - **freezeLevel**: `integer (int32)`
      - The price level within which orders cannot be placed or modified near the current price.
    - **stopsLevel**: `integer (int32)`
      - Minimum distance for stop loss and take profit orders from the current price.
    - **description**: `string`
      - Descriptive text about the symbol.
    - **tags**: `array`
      - Tags or labels associated with the symbol.
    - **tradingHours**: `array`
      - Specific trading hours for the symbol.
      - **child attributes**:
        - **dayNumber**: `integer (int32)`
          - An integer representing the day of the week.
        - **openHours**: `integer (int32)`
          - The hour at which trading for the symbol opens on the specified day.
        - **openMinutes**: `integer (int32)`
          - The minute within the hour at which trading starts.
        - **openSeconds**: `integer (int32)`
          - The second within the minute marking the exact moment trading begins.
        - **closeHours**: `integer (int32)`
          - The hour at which trading for the symbol closes on the specified day.
        - **closeMinutes**: `integer (int32)`
          - The minute within the hour at which trading ends.
        - **closeSeconds**: `integer (int32)`
          - The second within the minute marking the exact moment trading stops.
    - **isFixedLeverage**: `boolean`
      - Indicates if leverage for the symbol is fixed.
    - **terminationType**: `string`
      - Specifies how the symbol's trading is terminated (EXPIRATION, ROLLOVER).
      - **Enum values**: EXPIRATION, ROLLOVER
    - **terminationDate**: `string (date-time)`
      - Date when the symbol is set to expire.
    - **sessionOpen**: `boolean`
      - Reflects whether the session for the symbol is open.

### Open Position

Using this endpoint, you can open a Position for a specific Trading Account.

### POST /v1/trading-accounts/positions/open
```sh
curl --location 'https://broker-api-demo.match-trader.com/v1/trading-accounts/positions/open' \
--header 'Authorization: Provide you token here' \
--header 'Content-Type: application/json' \
--data '{
  "systemUuid": "",
  "login": "",
  "instrument": "",
  "orderSide": "BUY",
  "volume": null,
  "slPrice": null,
  "tpPrice": null
}'
```

### Response
`200`
```json
{
  "status": "OK",
  "orderId": ""
}
```

#### Header Parameters

- **Authorization**: `string`

#### Body Parameters

- **systemUuid**: `string`
  - **REQUIRED**
  - The unique identifier for the system where the account is located; required to specify where the position is to be opened.
- **login**: `string`
  - **REQUIRED**
  - The login identifier for the Trading Account on which the position is to be opened; required.
- **instrument**: `string`
  - **REQUIRED**
  - The trading instrument (symbol) for which the position is to be opened; required.
- **orderSide**: `string`
  - **REQUIRED**
  - Specifies whether the position is a 'BUY' or 'SELL'; required.
  - **Enum values**: BUY, SELL
- **volume**: `number`
  - **REQUIRED**
  - The amount of the instrument to be traded in the position; required.
- **slPrice**: `number`
  - The price at which a stop-loss order is set to limit potential losses; optional.
- **tpPrice**: `number`
  - The price at which a take-profit order is set to realize profits; optional.

#### Responses

##### 200 OK

**Response Attributes:**

- **status**: `string`
  - **REQUIRED**
  - The outcome of the request to open a position.
  - **Enum values**: OK, REJECTED, PARTIAL_SUCCESS
- **orderId**: `string`
  - A unique identifier for the order associated with the opened position.

### Edit Position

You can edit (Stop Loss and Take Profit values) an already opened Position for a specific Trading Account using this endpoint.

### POST /v1/trading-accounts/positions/edit
```sh
curl --location 'https://broker-api-demo.match-trader.com/v1/trading-accounts/positions/edit' \
--header 'Authorization: Provide you token here' \
--header 'Content-Type: application/json' \
--data '{
  "systemUuid": "",
  "login": "",
  "id": "",
  "slPrice": null,
  "tpPrice": null
}'
```

### Response
`200`
```json
{
  "status": "OK",
  "orderId": ""
}
```

#### Header Parameters

- **Authorization**: `string`

#### Body Parameters

- **systemUuid**: `string`
  - **REQUIRED**
  - The unique identifier for the system where the trading account is located; required.
- **login**: `string`
  - **REQUIRED**
  - The login identifier for the Trading Account in which the position exists; required.
- **id**: `string`
  - **REQUIRED**
  - The unique identifier of the position to be edited; required.
- **slPrice**: `number`
  - The new stop loss price to limit potential losses; optional, only include if modification is needed.
- **tpPrice**: `number`
  - The new take profit price to capture desired profits; optional, only include if modification is needed.

#### Responses

##### 200 OK

**Response Attributes:**

- **status**: `string`
  - Indicates the result of the request to edit the position.
  - **Enum values**: OK, REJECTED, PARTIAL_SUCCESS
- **orderId**: `string`
  - A unique identifier for the order associated with the edited position.

### Close Position

You can close a Position for a specific Trading Account using this endpoint. It is also possible to close multiple positions with one request.

### POST /v1/trading-accounts/positions/close
```sh
curl --location 'https://broker-api-demo.match-trader.com/v1/trading-accounts/positions/close' \
--header 'Authorization: Provide you token here' \
--header 'Content-Type: application/json' \
--data '{
  "systemUuid": "",
  "login": "",
  "closePositions": [
    {
      "positionId": "",
      "instrument": ""
    }
  ]
}'
```

### Response
`200`
```json
{
  "status": "OK",
  "orderId": ""
}
```

#### Header Parameters

- **Authorization**: `string`

#### Body Parameters

- **systemUuid**: `string`
  - **REQUIRED**
  - The unique identifier for the system where the trading account is located; required to specify the system context.
- **login**: `string`
  - **REQUIRED**
  - The login identifier for the Trading Account from which positions are to be closed; required.
- **closePositions**: `array`
  - **REQUIRED**
  - **child attributes**:
    - **positionId**: `string`
      - **REQUIRED**
      - The unique identifier of the trading position to be closed.
    - **instrument**: `string`
      - **REQUIRED**
      - The trading instrument of the position.

#### Responses

##### 200 OK

**Response Attributes:**

- **status**: `string`
  - The outcome of the request to close positions.
  - **Enum values**: OK, REJECTED, PARTIAL_SUCCESS
- **orderId**: `string`
  - A unique identifier for the transaction or order associated with the closure of the positions.


## Trading Data

The Trading Data section includes endpoints to retrieve Open and Closed Positions, Active Orders, and Ledgers. There is also a “Candles” sub-section, where you can find an endpoint to retrieve Candles data for a specific instrument.

### Get Open Positions

With this endpoint, you can retrieve all open positions for a specific trading account.

### GET /v1/trading-accounts/trading-data/open-positions?systemUuid=&login= 
```sh
curl --location 'https://broker-api-demo.match-trader.com/v1/trading-accounts/trading-data/open-positions?systemUuid=&login=' \
--header 'Authorization: Provide you token here' \
--header 'Content-Type: application/json'
```

### Response
`200`
```json
{
  "positions": [
    {
      "id": "",
      "symbol": "",
      "alias": "",
      "volume": null,
      "side": "BUY",
      "openTime": "",
      "openPrice": null,
      "stopLoss": null,
      "takeProfit": null,
      "swap": null,
      "profit": null,
      "netProfit": null,
      "currentPrice": null,
      "commission": null,
      "positions": [
        {
          "id": "",
          "symbol": "",
          "alias": "",
          "volume": null,
          "oldPartialVolume": null,
          "side": "BUY",
          "openTime": "",
          "openPrice": null,
          "swap": null,
          "profit": null,
          "netProfit": null,
          "currentPrice": null,
          "commission": null
        }
      ]
    }
  ]
}
```

#### Header Parameters

- **Authorization**: `string`

#### Query Parameters

- **systemUuid**: `string`
  - **REQUIRED**
  - The unique system identifier for the account.
- **login**: `string`
  - **REQUIRED**
  - The login identifier for the Trading Account.

#### Responses

##### 200 OK

**Response Attributes:**

- **positions**: `array`
  - An array of objects, each detailing a trading instrument.
  - **child attributes**:
    - **id**: `string`
      - The unique identifier of the position.
    - **symbol**: `string`
      - The trading instrument of the position.
    - **alias**: `string`
      - An alternative name or alias for the trading instrument.
    - **volume**: `number`
      - The amount of the instrument that is currently open.
    - **side**: `string`
      - Indicates whether the position is a 'BUY' or 'SELL'.
      - **Enum values**: BUY, SELL
    - **openTime**: `string (date-time)`
      - The timestamp when the position was opened.
    - **openPrice**: `number`
      - The price at which the position was opened.
    - **stopLoss**: `number`
      - The set stop loss value to limit potential losses.
    - **takeProfit**: `number`
      - The set take profit value to secure profits.
    - **swap**: `number`
      - The swap fee applied to the position.
    - **profit**: `number`
      - The current profit or loss of the position.
    - **netProfit**: `number`
      - The net profit after accounting for fees and costs.
    - **currentPrice**: `number`
      - The current market price of the trading instrument.
    - **commission**: `number`
      - The commission fees associated with the position.
    - **positions**: `array`
      - **child attributes**:
        - **id**: `string`
          - The unique identifier of the position.
        - **symbol**: `string`
          - The trading instrument of the position.
        - **alias**: `string`
          - An alternative name or alias for the trading instrument.
        - **volume**: `number`
          - The amount of the instrument that is currently open.
        - **oldPartialVolume**: `number`
          - The volume of the instrument in the position before any recent adjustments or partial closures.
        - **side**: `string`
          - Indicates whether the position is a 'BUY' or 'SELL'.
          - **Enum values**: BUY, SELL
        - **openTime**: `string (date-time)`
          - The timestamp when the position was opened.
        - **openPrice**: `number`
          - The price at which the position was opened.
        - **swap**: `number`
          - The swap fee applied to the position.
        - **profit**: `number`
          - The current profit or loss of the position.
        - **netProfit**: `number`
          - The net profit after accounting for fees and costs.
        - **currentPrice**: `number`
          - The current market price of the trading instrument.
        - **commission**: `number`
          - The commission fees associated with the position.

### Get Closed Positions

With this endpoint, you can retrieve all closed positions for a specific trading account.

### GET /v1/trading-accounts/trading-data/closed-positions?systemUuid=&login=&from=&to= 
```sh
curl --location 'https://broker-api-demo.match-trader.com/v1/trading-accounts/trading-data/closed-positions?systemUuid=&login=' \
--header 'Authorization: Provide you token here' \
--header 'Content-Type: application/json'
```

### Response
`200`
```json
{
  "closedPositions": [
    {
      "id": "",
      "openTime": "",
      "openPrice": null,
      "symbol": "",
      "alias": "",
      "volume": null,
      "stopLoss": null,
      "takeProfit": null,
      "time": "",
      "closePrice": null,
      "commission": null,
      "swap": null,
      "profit": null,
      "side": "BUY",
      "netProfit": null,
      "closingOrderID": ""
    }
  ]
}
```

#### Header Parameters

- **Authorization**: `string`

#### Query Parameters

- **systemUuid**: `string`
  - **REQUIRED**
  - The unique identifier for the system associated with the trading account; required.
- **login**: `string`
  - **REQUIRED**
  - The login identifier for the Trading Account; required.
- **from**: `string`
  - Start date for the query range to fetch closed positions; optional.
- **to**: `string`
  - End date for the query range to fetch closed positions; optional.

#### Responses

##### 200 OK

**Response Attributes:**

- **closedPositions**: `array`
  - **child attributes**:
    - **id**: `string`
      - Unique identifier of the closed position.
    - **openTime**: `string (date-time)`
      - Timestamp when the position was opened.
    - **openPrice**: `number`
      - Price at which the position was opened.
    - **symbol**: `string`
      - Trading instrument of the position.
    - **alias**: `string`
      - Alternative name or alias for the trading instrument.
    - **volume**: `number`
      - Volume of the instrument traded in the position.
    - **stopLoss**: `number`
      - Stop loss value set for the position.
    - **takeProfit**: `number`
      - Take profit value set for the position.
    - **time**: `string (date-time)`
      - Timestamp when the position was closed.
    - **closePrice**: `number`
      - Price at which the position was closed.
    - **commission**: `number`
      - Commission fees associated with the position.
    - **swap**: `number`
      - Swap fees incurred by the position.
    - **profit**: `number`
      - Profit or loss generated by the position.
    - **side**: `string`
      - Indicates whether the position was a 'BUY' or 'SELL'.
      - **Enum values**: BUY, SELL
    - **netProfit**: `number`
      - Net profit or loss after accounting for fees and costs.
    - **closingOrderID**: `string`
      - Identifier for the order that closed the position.

### Get Active Orders

With this endpoint, you can retrieve all Active Orders for a specific Trading Account.

### GET /v1/trading-accounts/trading-data/active-orders?systemUuid=&login= 
```sh
curl --location 'https://broker-api-demo.match-trader.com/v1/trading-accounts/trading-data/active-orders?systemUuid=&login=' \
--header 'Authorization: Provide you token here' \
--header 'Content-Type: application/json'
```

### Response
`200`
```json
{
  "orders": [
    {
      "id": "",
      "symbol": "",
      "alias": "",
      "volume": null,
      "type": "LIMIT",
      "creationTime": "",
      "activationPrice": null,
      "side": "BUY",
      "stopLoss": null,
      "takeProfit": null,
      "comment": ""
    }
  ]
}
```

#### Header Parameters

- **Authorization**: `string`

#### Query Parameters

- **systemUuid**: `string`
  - **REQUIRED**
  - The unique system identifier for the account; required to specify the system from which to retrieve active orders.
- **login**: `string`
  - **REQUIRED**
  - The login identifier for the Trading Account; required to identify which account's orders are to be fetched.

#### Responses

##### 200 OK

**Response Attributes:**

- **orders**: `array`
  - An array of objects, each detailing an active order.
  - **child attributes**:
    - **id**: `string`
      - Unique identifier for the order.
    - **symbol**: `string`
      - Trading instrument of the order.
    - **alias**: `string`
      - An alternative name or alias for the trading instrument.
    - **volume**: `number`
      - Volume of the instrument ordered.
    - **type**: `string`
      - Type of order, with possible values being 'LIMIT', 'STOP', or 'UNTYPE'.
      - **Enum values**: LIMIT, STOP, UNTYPE
    - **creationTime**: `string (date-time)`
      - Timestamp marking when the order was created.
    - **activationPrice**: `number`
      - Price at which the order becomes active.
    - **side**: `string`
      - Specifies whether the order is to 'BUY' or 'SELL'.
      - **Enum values**: BUY, SELL
    - **stopLoss**: `number`
      - The stop loss price set for the order to limit potential losses.
    - **takeProfit**: `number`
      - The take profit price set for the order to secure profits.
    - **comment**: `string`
      - A comment or note associated with the order.

### Get Ledgers

With this endpoint, you can retrieve all Ledgers for a specific Trading Account.

### GET /v1/trading-accounts/trading-data/ledgers?systemUuid=&login=&types=&from=&to=&limit= 
```sh
curl --location --globoff 'https://broker-api-demo.match-trader.com/v1/trading-accounts/trading-data/ledgers?systemUuid=&login=&types[]=' \
--header 'Authorization: Provide you token here' \
--header 'Content-Type: application/json'
```

### Response
`200`
```json
{
  "operations": [
    {
      "id": "",
      "type": "DEPOSIT",
      "time": "",
      "profit": null,
      "comment": ""
    }
  ]
}
```

#### Header Parameters

- **Authorization**: `string`

#### Query Parameters

- **systemUuid**: `string (date-time)`
  - **REQUIRED**
  - The unique system identifier for the account; required to specify from which system the ledgers are retrieved.
- **login**: `string (date-time)`
  - **REQUIRED**
  - The login identifier for the Trading Account; required to identify the account whose ledger entries are to be fetched.
- **types**: `array`
  - **REQUIRED**
  - **Enum values**: DEPOSIT, WITHDRAWAL, CREDIT_IN, CREDIT_OUT, AGENT_COMMISSION, COMMISSIONS, SWAPS
- **from**: `string`
  - Start date for filtering the ledger entries; optional.
- **to**: `string`
  - End date for filtering the ledger entries; optional.
- **limit**: `integer`
  - The maximum number of ledger entries to return in the response; optional.

#### Responses

##### 200 OK

**Response Attributes:**

- **operations**: `array`
  - An array of objects, each representing a ledger entry.
  - **child attributes**:
    - **id**: `string`
      - Unique identifier for the ledger entry.
    - **type**: `string`
      - The type of transaction recorded in the ledger.
      - **Enum values**: DEPOSIT, WITHDRAWAL, CREDIT_IN, CREDIT_OUT, AGENT_COMMISSION, COMMISSIONS, SWAPS, OTHER
    - **time**: `string (date-time)`
      - Timestamp when the transaction was recorded.
    - **profit**: `number`
      - Profit or loss amount associated with the transaction.
    - **comment**: `string`
      - A comment or description provided for the transaction.

### Get Candles

Get Candles endpoint allows you to retrieve candle data for a specific symbol and time interval. You will access information on the opening, high, low, and closing prices of candles within the specified time frame.

### GET /v1/candles?systemUuid=&symbol=&interval=M5&from=&to= 
```sh
curl --location 'https://broker-api-demo.match-trader.com/v1/candles?systemUuid=&symbol=&interval=M5&from=&to=' \
--header 'Authorization: Provide you token here' \
--header 'Content-Type: application/json'
```

### Response
`200`
```json
{
  "symbol": "",
  "interval": "M1",
  "candles": [
    {
      "time": null,
      "open": null,
      "high": null,
      "low": null,
      "close": null
    }
  ]
}
```

#### Header Parameters

- **Authorization**: `string`

#### Query Parameters

- **systemUuid**: `string (uuid)`
  - **REQUIRED**
  - The unique system identifier; required to specify which trading system's symbol data to retrieve.
- **symbol**: `string`
  - **REQUIRED**
  - The trading symbol for which candle data is requested; required.
- **interval**: `string`
  - **REQUIRED**
  - The time interval for the candle data.
  - **Enum values**: M1, M5, M15, M30, H1, H4, D1, W1, MN1
- **from**: `string (date-time)`
  - **REQUIRED**
  - The start datetime from which to retrieve candle data; required.
- **to**: `string (date-time)`
  - **REQUIRED**
  - The end datetime up to which candle data should be retrieved; required.

#### Responses

##### 200 OK

**Response Attributes:**

- **symbol**: `string`
  - The symbol for which the data is provided.
- **interval**: `string`
  - The time interval of the candle data, reflecting the same values as specified in the query.
  - **Enum values**: M1, M5, M15, M30, H1, H4, D1, W1, MN1
- **candles**: `array`
  - An array of objects, each representing a candle.
  - **child attributes**:
    - **time**: `integer (int64)`
      - Timestamp representing the start time of the candle.
    - **open**: `number (double)`
      - Opening price of the candle.
    - **high**: `number (double)`
      - Highest price reached during the candle's interval.
    - **low**: `number (double)`
      - Lowest price during the candle's interval.
    - **close**: `number (double)`
      - Closing price of the candle.

