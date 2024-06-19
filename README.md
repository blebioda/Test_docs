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

Via our API, you cannot edit Branches, Offers, and Roles configuration (it can be done only by using our CRM), but it's important to know them since you will need them to efficiently manage accounts created in the system. That's why we share GET endpoints, where you can check Branches, Offers, and Roles available in your setup.

## Get Branches

Branch represents the geographical or organizational division where the user operates. On the broker's end, it is usually connected to a specific area of responsibility or jurisdiction within the company's structure. This does not have to be fixed and the division can be freely shaped, depending on the business needs. The only requirement is that every account must have a branch assigned, even if there is one branch instance in the system.

### GET /v1/branches?from=&to=&sort=
```sh
curl --location 'https://broker-api-demo.match-trader.com/v1/branches' \
--header 'Authorization: Provide your token here' \
--header 'Content-Type: application/json'
```

### Response
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

#### Header Parameters

- **Authorization**: `string`

#### Body Parameters

- **accountUuid**: `string (uuid)`
  - **REQUIRED**
- **newPassword**: `string**
  - **REQUIRED**

#### Responses

##### 204 No Content
