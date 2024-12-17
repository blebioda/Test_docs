## Introduction

The Broker API is a versatile API platform designed for various integrations, enabling streamlined management and interaction with multiple services through a single access point. This API supports REST and gRPC protocols, providing flexibility in integration and ensuring compatibility with diverse client systems. It facilitates secure, efficient administrative operations and service management, making it an essential tool for clients looking to optimize their operational workflows.

In our system, each broker has a unique `partnerID` assigned. Every operation via this API is performed within that particular partnerID. Since we encode your partnerID in your Authorization token, you don't have to consider this parameter when sending requests, but you will see this parameter across multiple different endpoints. For the Sandbox environment, `partnerID` = 0 and is shared between every account in the system. It means you will have access to test data added by other integrations.

Sandbox

https://broker-api-demo.match-trader.com

## Authentication

Our system implements a security mechanism that utilizes token-based authorization to ensure secure resource access. The authentication process involves using an `**Authorization**` header, following the token scheme. This section outlines the steps required for obtaining and using the authentication token to access protected resources.

## Obtaining the Token

The authentication token is obtained from our Customer Relationship Management (CRM) system. You must first authenticate yourself within the CRM to receive a token. This token acts as a digital key, granting access to the system's resources for the duration of the token's validity. Tokens are valid until revoked.

## Using the Token for Access

```
<p>Authorization: &lt;Your_Token_Here&gt;</p>
```

In gRPC, the traditional concept of HTTP headers is abstracted through metadata, allowing for the transmission of key-value pairs alongside RPC calls. To secure gRPC services with token-based authentication, the Authorization metadata must be included with each call. This process is similar to using the Authorization header in HTTP requests but is adapted to fit the gRPC framework.

### Adding the Authorization Metadata

To include an authentication token in a gRPC request, the token must be added to the call's metadata using the 'Authorization' key, followed by the token value. This ensures that the server side of the gRPC service can authenticate the request by validating the token.

### Code Example (Python)

PYTHON

```python
pythonCopy codeimport grpc

# Create a gRPC channel

channel = grpc.insecure_channel('your_grpc_service_endpoint')

# Prepare metadata with the Authorization token

metadata = [('Authorization', '<Your_Token_Here>')]

# Create a stub (client)

stub = YourServiceStub(channel)

# Make a call with the Authorization metadata

response = stub.YourRpcMethod(request, metadata=metadata)
```

Replace `**<Your_Token_Here>**` with the actual token obtained from your CRM system, and adjust `**YourServiceStub**` and `**YourRpcMethod**` to match your gRPC service definition.

## gRPC

This gRPC model defines several services within a package designed for the Broker API, focusing on positions, ledgers, trading events, and related data. Below is the complete documentation for all services and their methods.

### 1\. PositionsServiceExternal

**getClientPositionsStream:** Streams client positions for a specified trading account, accepts a `PBClientPositionRequestExternal` message with the `systemUuid` and `login`, returns a stream of `PBPositionStreamResponseExternal` messages containing position details such as ID, symbol, alias, volume, side, open time, open price, stop loss/take profit, profit, net profit, swap, commission, and current price.

### 2\. LedgerServiceExternal

**getLedgersStreamByLogin:** Streams ledger entries for a specific trading account, accepts a `PBLedgerRequestByLoginExternal` message with the `systemUuid`, `login`, and `historyOperationType`, returns a stream of `PBLedgerStreamResponseExternal` messages with operation type, time, profit, comment, and associated login.

**getLedgersStreamByGroupsOrLogins:** Streams ledger entries filtered by specified groups or account logins, accepts a `PBLedgersRequestByGroupsOrLoginsExternal` message with the `systemUuid`, optional logins, groups, and `historyOperationType`, returns a stream of `PBLedgersStreamResponseExternal` messages with grouped ledger details.

### 3\. GroupsServiceExternal

**getGroupChangesStream:** Streams updates to group configurations, accepts a `GroupChangeStreamRequestExternal` message with the `systemUuid` and monitored groups, returns a stream of `GroupChangeStreamResponseExternal` messages containing either a `PBGroupUpdated` object with updated group parameters like margin, commission structure, and symbols, or a `PBGroupDeleted` object with the reason for group removal.

### 4\. **TradingEventsServiceExternal**

**getTradingEventsStream:** Streams trading events for specified accounts and groups, accepts a `PBTradingEventsStreamRequestExternal` message with account logins and group IDs, returns a stream of `TradingEventsStreamResponseExternal` messages containing events like margin calls, stop-outs, take profits, stop losses, or order activations, each event includes details specific to its type.

### 5\. OrderServiceExternal

**getOrdersUpdateStreamByGroupsOrLogins:** Streams updates to orders for specified accounts and groups, accepts a `PBOrdersUpdateStreamRequestExternal` message with the `systemUuid`, logins, groups, and flags for blocked/locked orders, returns a stream of `PBOrdersUpdateStreamResponseExternal` messages containing order details such as ID, symbol, alias, volume, type, creation time, activation price, side, stop loss, take profit, comment, update type, group, and login.

### 6\. QuotationsServiceExternal

**getQuotationsWithMarkupStream:** Streams real-time quotations for specified instruments with markups applied, accepts a `PBQuotationsWithMarkupStreamRequestExternal` message with the `systemUuid`, instruments, group, and optional throttling and statistics flags, returns a stream of `PBQuotationsWithMarkupStreamResponseExternal` messages containing bid and ask prices, timestamps, and optional daily statistics.

### 7\. AccountInfoServiceExternal - coming soon

**getAccountInfoChangedStream:** Streams updates about account information changes, accepts a `PBAccountInfoChangedRequestExternal` message with the `systemUuid`, groups, and flags for blocked/locked accounts, returns a stream of `PBAccountInfoChangedStreamResponseExternal` messages containing either `PBAccountInfoUpdated` objects with account details like login, group, leverage, status, and currency, or `PBAccountInfoDeleted` objects with the reason for account deletion.

### 8\. SymbolsServiceExternal - coming soon

**getSymbolsChangedStream:** Streams updates on trading symbols, accepts a `PBSymbolChangedRequestExternal` message with the `systemUuid`, returns a stream of `PBSymbolChangedStreamResponseExternal` messages containing either `PBSymbolUpdated` objects with symbol details like trading hours, precision, contract size, leverage, and commission, or `PBSymbolDeleted` objects indicating symbols that were removed.

```protobuf
syntax = 'proto3'; import "google/protobuf/empty.proto"; option java_multiple_files = true; option java_package = 'com.matchtrade.broker_api.grpc'; package com.matchtrade.broker_api.grpc; service PositionsServiceExternal { rpc getClientPositionsStream (PBClientPositionRequestExternal) returns (stream PBPositionStreamResponseExternal) { } } service LedgerServiceExternal { rpc getLedgersStreamByLogin(PBLedgerRequestByLoginExternal) returns (stream PBLedgerStreamResponseExternal) { } rpc getLedgersStreamByGroupsOrLogins(PBLedgersRequestByGroupsOrLoginsExternal) returns (stream PBLedgersStreamResponseExternal) { } } service OrderServiceExternal { rpc getOrdersUpdateStreamByGroupsOrLogins(PBOrdersUpdateStreamRequestExternal) returns (stream PBOrdersUpdateStreamResponseExternal) { } } service QuotationsServiceExternal { rpc getQuotationsWithMarkupStream (PBQuotationsWithMarkupStreamRequestExternal) returns (stream PBQuotationsWithMarkupStreamResponseExternal) { } } service GroupServiceExternal { rpc getGroupChangesStream(GroupChangeStreamRequestExternal) returns (stream GroupChangeStreamResponseExternal) { } } service TradingEventsServiceExternal { rpc getTradingEventsStream (PBTradingEventsStreamRequestExternal) returns (stream TradingEventsStreamResponseExternal) { } } service AccountInfoServiceExternal { rpc getAccountInfoChangedStream (PBAccountInfoChangedRequestExternal) returns (stream PBAccountInfoChangedStreamResponseExternal) { } } service SymbolsServiceExternal{ rpc getSymbolsChangedStream(PBSymbolChangedRequestExternal) returns (stream PBSymbolChangedStreamResponseExternal) {} } message PBOrdersUpdateStreamRequestExternal { string systemUuid = 1; repeated int64 logins = 2; repeated string groups = 3; bool includeBlocked = 4; bool includeLocked = 5; } message PBOrdersUpdateStreamResponseExternal { string orderId = 1; string symbol = 2; string alias = 3; string volume = 4; PBPendingTypeExternal type = 5; string creationTime = 6; string activationPrice = 7; PBOrderSideExternal side = 8; string stopLoss = 9; string takeProfit = 10; string comment = 11; PBRequestUpdateTypeExternal requestUpdateType = 12; string groupName = 13; int64 login = 14; } enum PBPendingTypeExternal { UNTYPE = 0; LIMIT = 1; STOP = 2; } message PBDailyStatisticsExternal { double change = 1; double high = 2; double low = 3; } message PBClientPositionRequestExternal { string systemUuid = 1; string login = 2; } message PBPositionStreamResponseExternal { repeated PBPositionStateExternal positions = 1; } message PBPositionStateExternal { PBRequestUpdateTypeExternal request_update_type = 1; PBPositionExternal position = 2; } enum PBRequestUpdateTypeExternal { NEW = 0; UPDATE = 1; CLOSED = 2; PROFIT_CHANGE = 3; PARTIALLY_CLOSED = 4; } message PBPositionExternal { string id = 1; string symbol = 2; string alias = 15; double volume = 3; PBOrderSideExternal side = 4; string open_time = 5; double open_price = 6; double stop_loss = 7; double take_profit = 8; double swap = 9; double profit = 10; double net_profit = 11; double current_price = 12; double commission = 13; repeated PBPartialExternal partials = 14; } message PBPartialExternal { string id = 1; double volume = 3; double old_partial_volume = 4; string open_time = 6; double open_price = 7; double swap = 8; double profit = 9; double net_profit = 10; double commission = 12; } enum PBOrderSideExternal { BUY = 0; SELL = 1; } enum PBHistoryOperationTypeExternal { DEPOSIT = 0; WITHDRAWAL = 1; CREDIT_IN = 2; CREDIT_OUT = 3; AGENT_COMMISSION = 4; COMMISSIONS = 5; SWAPS = 6; OTHER = 7; CLOSED_POSITION = 8; } message PBLedgerRequestByLoginExternal { string systemUuid = 1; string login = 2; repeated PBHistoryOperationTypeExternal historyOperationType = 3; } message PBLedgersRequestByGroupsOrLoginsExternal { string systemUuid = 1; repeated string logins = 2; repeated string groups = 3; repeated PBHistoryOperationTypeExternal historyOperationType = 4; } message PBLedgerStreamResponseExternal { string id = 1; PBHistoryOperationTypeExternal historyOperationType = 2; string time = 3; double profit = 4; string comment = 5; string group = 6; int64 login = 7; } message PBLedgersStreamResponseExternal { repeated PBLedgerStreamResponseExternal financeRecordDto = 1; } message PBQuotationsWithMarkupStreamRequestExternal { string systemUuid = 1; repeated string instruments = 2; string group = 3; optional int32 throttlingMs = 4; bool includeDailyStatistics = 5; } message PBQuotationsWithMarkupStreamResponseExternal { repeated PBQuotationSimpleExternal quotations = 1; } message PBQuotationSimpleExternal { string symbol = 1; double bidPrice = 2; double askPrice = 3; int64 timestampInMillis = 4; optional PBDailyStatisticsExternal dailyStatistics = 5; } message GroupChangeStreamRequestExternal { string systemUuid = 1; repeated string groups = 2; } message GroupChangeStreamResponseExternal { oneof msg { PBGroupUpdated groupUpdated = 1; PBGroupDeleted groupDeleted = 2; } } message PBGroupUpdated { string group = 1; string currency = 2; int32 currencyPrecision = 3; string groupOwner = 4; map<string, PBGroupSymbol> symbols = 5; PBMarginCalculationType marginCalculationType = 6; bool isManager = 7; bool isAdmin = 8; bool isBrokerManager = 9; int64 marginCall = 10; int32 depositCurrencyPrecision = 11; int64 swapCalculationTime = 12; PBStopoutAndMarginCallType stopoutAndMarginCallType = 13; bool commissionUpfront = 14; bool islamicSwap = 15; bool demoGroup = 16; PBHedgeMarginCalculationType hedgeMarginCalculationType = 17; int64 eodMode = 18; int64 eomMode = 19; int64 eodSnapshotTime = 20; bool defaultRetailEnabled = 21; bool coverageMode = 22; int64 orderProcessingDelay = 23; double stopoutLevel = 24; bool hedgingEnabled = 25; int64 pendingMultiplierPer1000 = 26; int64 defaultLeverageRatioPercent = 27; int64 commissionPerMillion = 28; int64 agentCommissionPerMillion = 29; } message PBGroupDeleted { string group = 1; GroupDeletedReason reason = 2; enum GroupDeletedReason { GROUP_DELETED_REASON_UNKNOWN = 0; GROUP_DELETED_REASON_DELETED = 1; GROUP_DELETED_REASON_RIGHTS_REVOKED = 2; } } message PBGroupSymbol { string symbol = 1; string bidMarkup = 2; string askMarkup = 3; double leverage = 4; string swapBuy = 5; string swapSell = 6; int64 freezeLevel = 7; int64 stopsLevel = 8; PBSwapType swapType = 9; string volumeMin = 10; string volumeMax = 11; string minCommission = 12; bool fixedLeverage = 13; int64 rawLeverage = 14; PBCommissionType commissionType = 15; int64 commissionPerMillion = 16; } enum PBMarginCalculationType { UNKNOWN_MARGIN_CALCULATION_TYPE = 0; UNREALIZED_LOSS_ONLY = 1; UNREALIZED_PROFIT_LOSS = 2; } enum PBSwapType { UNKNOWN_SWAP_TYPE = 0; PIPS_SWAP_TYPE = 1; PERCENTS = 2; NOT_DEFINED = 3; } enum PBCommissionType { UNDEFINED = 0; USD_PER_MILLION = 1; USD_PER_CONTRACT = 2; USD_PER_LOT = 3; PERCENTAGE = 4; PIPS_COMMISSION_TYPE = 5; } enum PBStopoutAndMarginCallType { STOPOUT_TYPE_UNDEFINED = 0; STOPOUT_TYPE_MARGIN_LEVEL_PERCENTAGE = 1; STOPOUT_TYPE_ABSOLUTE_EQUITY_USD = 2; STOPOUT_TYPE_OFF = 3; } enum PBHedgeMarginCalculationType { HEDGE_MARGIN_CALCULATION_TYPE_UNSPECIFIED = 0; HEDGE_MARGIN_CALCULATION_TYPE_NETTING = 1; HEDGE_MARGIN_CALCULATION_TYPE_LARGER_LEG = 2; } message PBTradingEventsStreamRequestExternal { string systemUuid = 1; repeated int64 logins = 2; repeated string groups = 3; bool includeBlocked = 4; bool includeLocked = 5; } message TradingEventsStreamResponseExternal { repeated PBTradingEvent tradingEvents = 1; } message PBTradingEvent { oneof msg { PBMarginCallEvent marginCallEvent = 1; PBStopOutEvent stopOutEvent = 2; PBTakeProfitEvent takeProfitEvent = 3; PBStopLossEvent stopLossEvent = 4; PBOrderActivationEvent orderActivationEvent = 5; } int64 login = 6; string group = 7; } message PBMarginCallEvent { int64 login = 1; double marginLevel = 2; } message PBStopOutEvent { int64 login = 1; string orderId = 2; string instrument = 3; double volume = 4; } message PBTakeProfitEvent { int64 login = 1; string orderId = 2; string instrument = 3; PBOrderSideExternal side = 4; double volume = 5; double netProfit = 7; string walletCurrency = 8; } message PBStopLossEvent { int64 login = 1; string orderId = 2; string instrument = 3; PBOrderSideExternal side = 4; double volume = 5; double netProfit = 7; string walletCurrency = 8; } message PBOrderActivationEvent { int64 login = 1; string orderId = 2; string instrument = 3; PBOrderSideExternal side = 4; double volume = 5; double activationPrice = 7; } message PBAccountInfoChangedRequestExternal { string systemUuid = 1; repeated string groups = 2; bool includeBlocked = 3; bool includeLocked = 4; } message PBAccountInfoChangedStreamResponseExternal { oneof msg { PBAccountInfoUpdated userUpdated = 1; PBAccountInfoDeleted userDeleted = 2; } } message PBAccountInfoUpdated { int64 login = 1; string uuid = 2; string group = 3; int32 leverage = 4; bool isRetail = 5; bool isProView = 6; bool isBlocked = 7; bool isLocked = 8; string country = 9; string name = 10; string address = 11; string email = 12; string phone = 13; string province = 14; string zipCode = 15; string city = 16; string remarks = 17; string bankAccount = 18; string accountCurrency = 19; int32 accountCurrencyPrecision = 20; int64 registrationDate = 21; } message PBAccountInfoDeleted { int64 login = 1; string group = 2; AccountInfoDeletedReason reason = 3; enum AccountInfoDeletedReason { ACCOUNT_INFO_DELETED_REASON_UNKNOWN = 0; ACCOUNT_INFO_DELETED_REASON_ACCOUNT_DELETED = 1; ACCOUNT_INFO_DELETED_REASON_RIGHTS_REVOKED = 2; ACCOUNT_INFO_DELETED_REASON_ACCOUNT_INTERNAL_ERROR = 3; } } message PBSymbolChangedRequestExternal{ string systemUuid = 1; } message PBSymbolChangedStreamResponseExternal{ oneof msg { PBSymbolUpdated instrumentUpdated = 1; PBSymbolDeleted instrumentDeleted = 2; } } message PBSymbolUpdated{ repeated TradeHoursDto tradingHours = 1; int32 volumePrecision = 2; string volumeMin = 3; string volumeStep = 4; double commission = 5; string sizeOfOnePoint = 6; string symbol = 7; string alias = 8; string baseCurrency = 9; string quoteCurrency = 10; int32 pricePrecision = 11; PBSymbolType type = 12; string swapBuy = 13; string swapSell = 14; string volumeMax = 15; string contractSize = 16; int64 multiplier = 17; int64 divider = 18; double leverage = 19; string description = 20; PBTerminationType terminationType = 21; string terminationDate = 22; repeated string tags = 23; } message PBSymbolDeleted{ string symbol = 1; } message TradeHoursDto { int32 dayNumber = 1; // 0 - Sunday int32 openHours = 2; int32 openMinutes = 3; int32 openSeconds = 4; int32 closeHours = 5; int32 closeMinutes = 6; int32 closeSeconds = 7; } enum PBSymbolType { UNKNOWN_SYMBOL_TYPE = 0; FOREX = 1; CFD = 2; FOREXCFD = 3; } enum PBTerminationType { EXPIRATION = 0; ROLLOVER = 1; }
```

Sandbox

https://broker-api-demo.match-trader.com

## Errors

We use standard HTTP response codes to state the success or failure of API requests. It means that codes in the range

-   2xx indicate success and usually have response bodies,
    
-   4xx indicate errors with detailed answers to why a particular request failed,
    
-   5xx indicate errors on our servers.
    

200

The request was performed successfully. You should receive a response with a body. 200 response bodies are listed in each endpoint's response section.

204

The request was performed successfully. You should receive a response without a body.

400

Invalid request, usually due to missing some required parameter.

401

Authorization information is missing or invalid.

403

Your API token does not have permission to perform the requested operation.

422

The server understands the content type of the request entity, and the syntax is correct, but it was unable to process the request.

500

Something went wrong on our end. You can contact our Product Support to check it.

## Examples

In this section, you can find examples of error responses for each status code. Successful responses are available in the relevant sections of this documentation.

### Responses

#### Response Attributes

#### Response Attributes

#### Response Attributes

#### Response Attributes

## Branches, Offers, Roles

In our system, there is a distinction between multiple layers of account attributes. In general, the account operates within the frames of a given: Branch, Offer(s), and Role. Each section has an explanation of a particular layer.

Via our API you cannot edit Branches, Offers, and Roles configuration (it can be done only by using our CRM), but it's important to know them since you will need them to efficiently manage accounts created in the system. That's why we share GET endpoints, where you can check Branches, Offers, and Roles available in your setup.

## Get Branches

**Branch** represents the geographical or organizational division where the user operates. On the broker's end, it is usually connected to a specific area of responsibility or jurisdiction within the company's structure. This does not have to be fixed and the division can be freely shaped, depending on the business needs. The only requirement is that every account must have a branch assigned, even if there is one branch instance in the system.

#### Header parameters

#### Query parameters

sortstring

Default value

created,desc

### Responses

#### Response Attributes

branchesarray

Hide child attributes

Unique identifier for the Branch.

Unique ID of a particular Broker in the system.

Time at which Branch was created.

Time of the last update of the Branch.

mailingConfigurationobject

Mailing Configuration is used for connecting email providers to send emails to users within a particular branch.

Hide child attributes

Login used to send emails.

Password used to send emails.

Mailer configuration host.

Mailer configuration port.

Whether the configuration is using TSL encryption technology.

Whether the configuration is using SSL encryption technology.

tfaRequiredForClientsboolean

Whether Two Factor Authentication is required for clients to operate.

Real/Live server name for MetaTrader 4 related purposes.

Demo server name for MetaTrader 4 related purposes.

Real/Live server name for MetaTrader 4 related purposes.

Demo server name for MetaTrader 4 related purposes.

Whether Sumsub is being used in that particular Branch to verify users.

## Get Offers

**Offer** is associated with the user's _trading_ account. It can be either Demo or Real and have different leverage, currency, or other trading-related configurations. In regards to Branch, users can be in one branch but have multiple trading accounts within different offers.

#### Header parameters

#### Query parameters

sortstring

Default value

created,desc

### Responses

#### Response Attributes

offersarray

Hide child attributes

Unique identifier for the Offer.

Unique ID of a particular Broker in the system.

createdstring (date-time)

Time at which the Offer was created.

updatedstring (date-time)

Time of the last update of the Offer.

The currency of the Offer. All trading accounts within a particular Offer will have that currency.

Whether the Offer is a demo. If false, the Offer is real/live.

Whether the Offer is not visible when creating a new trading account. It can be also used for displaying Offers in the registration process.

Offer's description you want to show to users.

Parameter used for PAMM system connection purposes.

Amount of funds that will be automatically deposited to a trading account after finishing the registration process.Amount of funds that will be automatically deposited to a trading account after finishing the registration process.

Leverage for trading accounts created in a particular Offer.

verificationRequiredboolean

Whether to start operating, users have to be verified.

tradingAccountAutoCreationboolean

Whether trading accounts will be created automatically or you need to be accepted manually in the CRM.

Group name of a particular group in the trading system. It is used to connect Offers to the trading system and create new trading accounts in that group.

The branch in the Offer is used only once - for the registration process. After selecting the Offer to create a trading account, a user will have that particular Branch assigned to their newly created account.

Hide child attributes

Unique identifier for the Branch.

Unique ID of a particular Broker in the system.

createdstring (date-time)

Time at which the Branch was created.

Time of the last update of a Branch.

Trading system to which the Offer is connected and in which it operates.

Hide child attributes

Unique identifier for the System.

Unique ID of a particular Broker in the system.

createdstring (date-time)

Time at which the System was created.

Whether the System is demo or real/live. If false, the System is real/live.

Whether the System is enabled in the CRM.

The System's trading engine it is connected to.

## Get Roles

**Role** is connected directly to the user's account. Roles with access to the Match-Trader trading platform are: User, IB, and Sub IB. Other roles are reserved for users with access to the CRM and have different responsibilities and permissions to manage accounts.

#### Header parameters

#### Query parameters

sortstring

Default value

created,desc

### Responses

#### Response Attributes

rolesarray

Hide child attributes

Unique identifier for the Role.

Unique ID of a particular Broker in the system.

createdstring (date-time)

Time at which the Role was created.

updatedstring (date-time)

Time of the last update of the Offer.

## Accounts

In the Accounts section, you will find detailed information about user accounts within the system. With this, you can access and update account details and create new accounts. This section serves as a central hub for overseeing and administering user accounts within the system. You can also find a Trading Accounts sub-section within the section with all relevant endpoints to manage Trading Accounts.

An Account represents the user, and a Trading Account is needed to perform trading actions. Under one Account, there might be more than one Trading Account. Some actions are performed only on Accounts, while others are on Trading Accounts.

In our system, there is a distinction between Leads and Clients. They are both types of user accounts. The difference between Lead and Client is conversion (first deposit) - the user is a Lead by default and becomes a Client after the first successful deposit.

## Get Accounts

With the Get Accounts endpoint, you can retrieve detailed information about all accounts within the system.

Since the response is paged, please make sure you are making a valid request. The default request's response (without providing a proper query param) is set to 10 records.

#### Header parameters

#### Query parameters

You can use it to ask for a specific: email, first name, last name, account manager name, account manager surname, country, phone and lead status.

Page number you want to receive the data frp,

Page size - how many records you want to receive in the response.

Either descending or ascending. You can sort by: created, updated, email, phone, first name, last name, country, state, city, postCode and address.

Default value

created,desc

Starting date and time for created.

Ending date and time for created.

### Responses

#### Response Attributes

contentarray

Hide child attributes

Unique identifier for the Account.

createdstring (date-time)

Time at which the Account was created.

updatedstring (date-time)

Time of the last update of the Account.

User's verification status for KYC purposes.

Enum values:

`NEW``REJECTED``VERIFIED``BLOCKED``PENDING_VERIFICATION``UNVERIFIED`

Account type.

Enum values:

`RETAIL``PROFESSIONAL``EXPERIENCED`

personalDetailsobject

Hide child attributes

passportobject

Hide child attributes

User's passport/ID number.

User's passport/ID issuer country.

taxIdentificationNumberstring

User's tax identification number.

contactDetailsobject

Hide child attributes

toContactobject

Hide child attributes

toContactDatestring (date-time)

The date you want to contact the particular user.

When true, it means this user is marked as contacted in the CRM.

accountConfigurationobject

Hide child attributes

Unique ID of a particular Broker in the system.

Unique identifier for the Branch of a particular Account.

Unique identifier for the Role of a particular Account.

Details of Account Manager assigned to the particular Account.

Hide child attributes

Unique identifier for the Account Manager.

Account Manager's email address.

ibParentTradingAccountUuidstring (uuid)

If an Account is in the IB structure, this field will return the value of an IB Account above that particular account in the IB structure.

Only applicable for CRM Users.

Hide child attributes

accountTypeContactboolean

When true, it means that a particular Account was created as a Contact. It means the user can register with a given email and update data you already have.

addressDetailsobject

Hide child attributes

User's address (for example street, building, and flat numbers).

bankingDetailsobject

Hide child attributes

User's bank's SWIFT code.

User's bank account (for example: bank number).

User's bank account name.

leadDetailsobject

Hide child attributes

Unique identifier for the Lead Status of a particular Account.

User's lead source/source of origin (from where the user appeared in the system).

providerUuidstring (uuid)

Unique identifier for the Lead Provider of a particular Account.

becomeActiveClientTimestring (date-time)

The time when the user became Client (and stopped being Lead).

totalPagesinteger (int32)

Total number of pages based on the requested size.

totalElementsinteger (int64)

Total number of Accounts in the system (based on the provided query params).

Number of records included in one page.

## Get Account by Email

With the Get Account by Email endpoint, you can retrieve detailed information about a particular Account in the system using the user's email.

#### Header parameters

#### Path parameters

### Responses

#### Response Attributes

Unique identifier for the user

createdstring (date-time)

The creation date and time of the user account

updatedstring (date-time)

The last updated date and time of the user account

The email address of the user

The status of the user's email verification

Enum values:

`NEW``REJECTED``VERIFIED``BLOCKED``PENDING_VERIFICATION``UNVERIFIED`

The type of user, with possible values being RETAIL, PROFESSIONAL, and EXPERIENCED

Enum values:

`RETAIL``PROFESSIONAL``EXPERIENCED`

personalDetailsobject

Hide child attributes

Preferred language of the user

Marital status of the user

An object containing passport details such as number and country of issuance

Hide child attributes

taxIdentificationNumberstring

User's tax identification number.

contactDetailsobject

Hide child attributes

An object related to contacting details

Hide child attributes

toContactDatestring (date-time)

The date scheduled to contact the user

A boolean indicating whether the user has already been contacted

accountConfigurationobject

Object with details about account configuration

Hide child attributes

Identifier for the partner

An object containing information about the user's account manager

Hide child attributes

UUID of the account manager.

Email address of the account manager

Name of the account manager

ibParentTradingAccountUuidstring (uuid)

UUID of the IB parent trading account

An object detailing CRM user scope

Hide child attributes

accountTypeContactboolean

addressDetailsobject

Hide child attributes

The country where the user resides

The state where the user resides

The city where the user resides

Postal code of the user’s address

Detailed street address of the user

bankingDetailsobject

Hide child attributes

The user's bank account number

Name of the bank where the user's account is held

The name associated with the bank account

leadDetailsobject

Hide child attributes

UUID representing the status of the lead

The source from which the lead originated, e.g., a marketing campaign, referral, etc.

providerUuidstring (uuid)

UUID of the provider that referred or generated the lead

becomeActiveClientTimestring (date-time)

The date and time when the lead became an active client

## Get Account by UUID

With the Get Account by UUID endpoint, you can retrieve detailed information about a particular Account in the system using the Account's UUID.

With the Get Account by Email endpoint, you can retrieve detailed information about a particular Account in the system using the user's email.

#### Header parameters

#### Path parameters

accountUuidstring (uuid)Required

Unique identifier for a user account, used to fetch specific account data.

### Responses

#### Response Attributes

Unique identifier for the user

createdstring (date-time)

The creation date and time of the user account

updatedstring (date-time)

The last updated date and time of the user account

The email address of the user

The status of the user's email verification

Enum values:

`NEW``REJECTED``VERIFIED``BLOCKED``PENDING_VERIFICATION``UNVERIFIED`

The type of user, with possible values being RETAIL, PROFESSIONAL, and EXPERIENCED

Enum values:

`RETAIL``PROFESSIONAL``EXPERIENCED`

personalDetailsobject

Hide child attributes

Preferred language of the user

Marital status of the user

An object containing passport details such as number and country of issuance

Hide child attributes

taxIdentificationNumberstring

User's tax identification number.

contactDetailsobject

Hide child attributes

An object related to contacting details

Hide child attributes

toContactDatestring (date-time)

The date scheduled to contact the user

A boolean indicating whether the user has already been contacted

accountConfigurationobject

n object with details about account configuration

Hide child attributes

Identifier for the partner

An object containing information about the user's account manager

Hide child attributes

UUID of the account manager.

Email address of the account manager

Name of the account manager

ibParentTradingAccountUuidstring (uuid)

UUID of the IB parent trading account

An object detailing CRM user scope

Hide child attributes

accountTypeContactboolean

addressDetailsobject

Hide child attributes

The country where the user resides

The state where the user resides

The city where the user resides

Postal code of the user’s address

Detailed street address of the user

bankingDetailsobject

Hide child attributes

The user's bank account number

Name of the bank where the user's account is held

The name associated with the bank account

leadDetailsobject

Hide child attributes

UUID representing the status of the lead

The source from which the lead originated, e.g., a marketing campaign, referral, etc.

providerUuidstring (uuid)

UUID of the provider that referred or generated the lead

becomeActiveClientTimestring (date-time)

The date and time when the lead became an active client

## Get Account's Timeline Events

With Get Account's Timeline Events endpoint, you can retrieve detailed information about events associated with a specific account. Users can access a timeline of events, including details, creators, and creation timestamps, providing valuable insights into account activities.

#### Header parameters

#### Query parameters

A string parameter specifying the type of activity to filter by.

Enum values:

`VOIP_CALL``NOTE``TASK_CALL``TASK``LEAD_ASSIGNMENT``FAILED_DEPOSIT``SUCCESSFUL_DEPOSIT``WITHDRAWAL_TO_CONFIRM``STOP_LOSS``TAKE_PROFIT`

###### ... 4 other enums

Filters activities starting from this date and time

Filters activities up to this date and time.

Specifies the sorting order of the results. The default value is created,desc, which sorts the activities by creation date in descending order

Default value

created,desc

#### Path parameters

accountUuidstring (uuid)Required

Unique identifier for a user account, used to fetch specific account data.

### Responses

#### Response Attributes

timelineEventsarray

Hide child attributes

This field takes different values depending on the type of the Event. However, each time it contains detailed information about the Event.

Different Event types available in the system.

Enum values:

`VOIP_CALL``NOTE``TASK_CALL``TASK``LEAD_ASSIGNMENT``FAILED_DEPOSIT``SUCCESSFUL_DEPOSIT``WITHDRAWAL_TO_CONFIRM``STOP_LOSS``TAKE_PROFIT``ORDER_ACTIVATION``STOP_OUT``MARGIN_CALL``LOGIN`

Indicates the creator of the Event.

createdstring (date-time)

Time at which the Event was created.

## Create Account

In this section, you can find the endpoint for creating new Accounts in the system.

#### Header parameters

#### Body parameters

The email address for account registration.

The secure password for the new account

Identifier for a specific promotional offer

Type of client (RETAIL, PROFESSIONAL, EXPERIENCED).

Enum values:

`RETAIL``PROFESSIONAL``EXPERIENCED`

createAsDepositedAccountboolean

Flag to set the account as initially deposited.

personalDetailsobject Required

Hide child attributes

Account holder’s firstname

Account holder’s lastname

Account holder's birthdate.

Includes passport number and issuing country.

Hide child attributes

taxIdentificationNumberstring

ax ID for financial records

contactDetailsobject

Hide child attributes

toContactobject

Hide child attributes

toContactDatestring (date-time)

The specific date and time when the account holder prefers to be contacted.

alreadyContactedboolean Required

ndicates whether the account holder has been previously contacted. This is a required field.

accountConfigurationobject

Hide child attributes

A unique identifier for the partner associated with the account.

UUID of the branch where the account is managed.

UUID identifying the specific role associated with the account.

accountManagerUuidstring (uuid)

UUID of the account manager assigned to handle the account.

ibParentTradingAccountUuidstring (uuid)

UUID of the parent trading account, linking any sub-accounts or related trading activities.

crmUserScopeobject

Hide child attributes

A list of branch identifiers defining the scope of operation for the CRM user. The array allows unique items only.

Defines pools of managers within the CRM system, restricted to unique entries.

accountTypeContactboolean

Detailed address components for the account holder’s residence or operational base.

Hide child attributes

bankingDetailsobject

Hide child attributes

The address of the bank where the account holder’s account is located.

The SWIFT code of the bank for international transactions.

The name of the bank where the account is maintained.

The name associated with the bank account, typically the account holder's name.

leadDetailsobject

Hide child attributes

UUID indicating the current status of the lead in the CRM system.

The origin or source from where the lead was generated.

providerUuidstring (uuid)

UUID of the provider or entity that referred or generated the lead.

becomeActiveClientTimestring (date-time)

The date and time when the lead is expected to become an active client.

### Responses

#### Response Attributes

uuidstring (uuid)Required

Required. A unique identifier for the account.

temporary token used for authentication or verification purposes.

createdstring (date-time)Required

Required. The date and time the account was created.

updatedstring (date-time)Required

Required. The date and time the account was last updated.

Required. The email address associated with the account.

verificationStatusstring Required

Required. The current status of the account's verification.

Enum values:

`NEW``REJECTED``VERIFIED``BLOCKED``PENDING_VERIFICATION``UNVERIFIED`

Type of account based on client classification. Possible values: RETAIL, PROFESSIONAL, EXPERIENCED.

Enum values:

`RETAIL``PROFESSIONAL``EXPERIENCED`

personalDetailsobject Required

Contains personal information about the account holder

Hide child attributes

Preferred language of the user

Marital status of the user

passportobject

Hide child attributes

taxIdentificationNumberstring

User's tax identification number.

contactDetailsobject Required

Hide child attributes

toContactobject

Hide child attributes

toContactDatestring (date-time)

alreadyContactedboolean Required

accountConfigurationobject Required

n object with details about account configuration

Hide child attributes

partnerIdinteger (int64)Required

Identifier for the partner

branchUuidstring (uuid)Required

UUID of the branch

An object containing information about the user's account manager

Hide child attributes

UUID of the account manager.

Email address of the account manager

Name of the account manager

ibParentTradingAccountUuidstring (uuid)

UUID of the IB parent trading account

An object detailing CRM user scope

Hide child attributes

accountTypeContactboolean Required

addressDetailsobject Required

Hide child attributes

The country where the user resides

The state where the user resides

The city where the user resides

Postal code of the user’s address

Detailed street address of the user

bankingDetailsobject Required

Hide child attributes

The user's bank account number

Name of the bank where the user's account is held

The name associated with the bank account

leadDetailsobject Required

Hide child attributes

UUID representing the status of the lead

The source from which the lead originated, e.g., a marketing campaign, referral, etc.

providerUuidstring (uuid)

UUID of the provider that referred or generated the lead

becomeActiveClientTimestring (date-time)

The date and time when the lead became an active client

## Update Account Info

With this endpoint, you can update and modify Account details.

#### Header parameters

#### Path parameters

accountUuidstring (uuid)Required

Unique identifier for a user account, used to fetch specific account data.

#### Body parameters

The status of the user's email verification

Enum values:

`NEW``REJECTED``VERIFIED``BLOCKED``PENDING_VERIFICATION``UNVERIFIED`

The type of account, with possible values being RETAIL, PROFESSIONAL, and EXPERIENCED

Enum values:

`RETAIL``PROFESSIONAL``EXPERIENCED`

personalDetailsobject

Hide child attributes

Preferred language of the user

Marital status of the user

An object containing passport details such as number and country of issuance

Hide child attributes

taxIdentificationNumberstring

User's tax identification number.

contactDetailsobject

Hide child attributes

An object related to contacting details

Hide child attributes

toContactDatestring (date-time)

The date scheduled to contact the user

alreadyContactedboolean Required

A boolean indicating whether the user has already been contacted

accountConfigurationobject

Object with details about account configuration

Hide child attributes

Identifier for the branch managing the account.

Identifier for the account's assigned role, defining permissions.

ibParentTradingAccountUuidstring (uuid)

Identifier for the parent IB trading account, linking related accounts.

accountManagerUuidstring (uuid)

Identifier for the account manager in charge.

accountTypeContactboolean

Flags if contact details are required for the account.

addressDetailsobject

Hide child attributes

The country where the user resides

The state where the user resides

The city where the user resides

Postal code of the user’s address

Detailed street address of the user

bankingDetailsobject

Hide child attributes

The user's bank account number

Name of the bank where the user's account is held

The name associated with the bank account

leadDetailsobject

Hide child attributes

leadStatusUuidstring (uuid)

Unique identifier representing the current status of a lead within the CRM system, tracking the lead's progression through the sales pipeline.

### Responses

#### Response Attributes

uuidstring (uuid)Required

Unique identifier for the account.

createdstring (date-time)Required

Timestamps for when the account was created

updatedstring (date-time)Required

Timestamps for when the account was last updated.

Email address associated with the account.

verificationStatusstring Required

Status of the account's verification

Enum values:

`NEW``REJECTED``VERIFIED``BLOCKED``PENDING_VERIFICATION``UNVERIFIED`

Account type, options are RETAIL, PROFESSIONAL, EXPERIENCED.

Enum values:

`RETAIL``PROFESSIONAL``EXPERIENCED`

personalDetailsobject Required

Hide child attributes

First name of the account holder.

Last name of the account holder.

Birthdate of the account holder.

Citizenship of the account holder.

Preferred language of the account holder.

Marital status of the account holder.

Passport information

Hide child attributes

taxIdentificationNumberstring

contactDetailsobject Required

Contact information

Hide child attributes

Phone number of the account holder.

Preferences about contacting the account holder

Hide child attributes

toContactDatestring (date-time)

Preferred date and time to contact.

alreadyContactedboolean Required

Required. Indicates if the account holder has been contacted previously.

accountConfigurationobject Required

Configuration details for the account

Hide child attributes

partnerIdinteger (int64)Required

Partner identifier associated with the account.

branchUuidstring (uuid)Required

Branch identifier where the account is managed.

Role identifier associated with the account.

Manager details

Hide child attributes

Unique identifier for the account manager.

Email address of the account manager.

Name of the account manager.

ibParentTradingAccountUuidstring (uuid)

Identifier for a parent IB trading account linked to the account.

CRM user's scope

Hide child attributes

accountTypeContactboolean Required

addressDetailsobject Required

Hide child attributes

bankingDetailsobject Required

Provides essential financial information for transactions

Hide child attributes

Physical address of the bank.

SWIFT code for international banking.

Name associated with the bank account.

leadDetailsobject Required

Details for tracking and managing leads

Hide child attributes

Unique ID for the lead's status

providerUuidstring (uuid)

ID of the lead provider.

becomeActiveClientTimestring (date-time)

Expected time for lead conversion to active client.

## Change Account's Password

Using this endpoint, you can change the Account's password.

#### Header parameters

#### Body parameters

accountUuidstring (uuid)Required

newPasswordstring Required

### Responses

## Add Note

This endpoint allows you to add a note to a specific Account, enabling you to store additional information or comments related to the account.

#### Header parameters

#### Body parameters

accountUuidstring (uuid)Required

A unique identifier for the account to which the note is attached.

The type of the note, which can be either "NOTE" (a regular note) or "CALL" (a note regarding a call).

The content of the note or details related to the call.

### Responses

#### Response Attributes

## Add Task

With this endpoint, you can create a task for a specific Account, facilitating task management and follow-up actions.

#### Header parameters

#### Body parameters

accountUuidstring (uuid)Required

A unique identifier for the account to which the task is assigned.

The type of task, which can be either "task" (a general task) or "call assignment" (a task related to a call assignment).

The start date of the task

The current status of the task, which can be "to do", "in progress", or "done".

accountManagerUuidstring Required

A unique identifier for the account manager responsible for the task.

A comment providing additional information or remarks about the task.

### Responses

#### Response Attributes

## Trading Accounts

## Get Trading Accounts

With the Get Accounts endpoint, you can retrieve detailed information about all Trading Accounts within the system.

#### Header parameters

#### Query parameters

Filters accounts by login, email, name and surname of the trader and by the name of the account manager if exist.

Page number to access a subset of account records.

Number of account records per page. The size limit is 1000

Sorting order of the account records,

Default value

created,desc

Filter accounts from this starting date and time.

Filter accounts up to this ending date and time.

### Responses

#### Response Attributes

contentarray

Hide child attributes

Unique identifier for the trading account.

Login ID associated with the trading account.

createdstring (date-time)

Date and time the account was established.

accountInfoobject

Hide child attributes

uuidstring (uuid)Required

Unique identifier for the account info.

Email address linked to the account.

Identifier for the associated offer.

Identifier for the system managing the account.

financeInfoobject

Hide child attributes

Trading Account's Balance.

Trading Account's Equity.

Trading Account's Profit.

Trading Account's Net Profit.

Trading Account's Margin.

Trading Account's Free Margin.

Trading Account's Margin Level.

Trading Account's Credit.

Trading Account's Currency.

currencyPrecisioninteger (int32)

Trading Account's Currency precision.

totalPagesinteger (int32)

Total number of result pages.

totalElementsinteger (int64)

Total number of accounts.

Number of records per page.

## Get Trading Account by login

With the Get Trading Account by login endpoint, you can retrieve detailed information about a particular Trading Account in the system using the Trading Account's login.

#### Header parameters

#### Query parameters

systemUuidstring Required

Provided System UUID.

### Responses

#### Response Attributes

Unique identifier for the trading account.

Login ID associated with the trading account.

createdstring (date-time)

Date and time the account was established.

accountInfoobject

Hide child attributes

Unique identifier for the account info.

Email address linked to the account.

Identifier for the associated offer.

Identifier for the system managing the account.

financeInfoobject

Hide child attributes

Trading Account's Balance.

Trading Account's Equity.

Trading Account's Profit.

Trading Account's Net Profit.

Trading Account's Margin.

Trading Account's Free Margin.

Trading Account's Margin Level.

Trading Account's Credit.

Trading Account's Currency.

currencyPrecisioninteger (int32)

Trading Account's Currency precision.

## Create New Trading Account

In this section, you can find the endpoint for creating new Trading Accounts in the system.

#### Header parameters

#### Path parameters

accountUuidstring (uuid)Required

Required. A unique identifier for the account under which the trading account will be created.

#### Body parameters

offerUuidstring (uuid)Required

Required. Identifier for the specific offer that applies to the new trading account.

commissionUuidstring (uuid)

Identifier for the commission structure that will be applied to the trading account.

### Responses

#### Response Attributes

uuidstring (uuid)Required

Required. The unique identifier assigned to the newly created trading account.

createdstring (date-time)Required

Required. Timestamp indicating when the trading account was created.

updatedstring (date-time)Required

Required. Timestamp indicating the last update to the trading account.

Required. The login ID associated with the trading account.

offerUuidstring (uuid)Required

Required. The offer identifier linked to the trading account upon creation.

The status of the trading account upon creation.

commissionUuidstring (uuid)

he commission structure identifier associated with the trading account.

## Update Trading Account

With the Get Trading Account by login endpoint, you can retrieve detailed information about a particular Trading Account in the system using the Trading Account's login.

#### Header parameters

#### Query parameters

systemUuidstring Required

Provided System UUID.

#### Body parameters

Required. Identifier for the specific offer that applies to the new trading account.

Identifier for the commission structure that will be applied to the trading account.

### Responses

## Change Leverage

In this section, you can find the endpoint to change the leverage of the trading account.

#### Header parameters

Authorizationstring Required

#### Query parameters

systemUuidstring Required

Identifier for the system managing the account.

Login ID associated with the trading account.

#### Body parameters

leverageinteger (uuid)Required

Leverage you would like to set for trading account

### Responses

## Payments

In the Payments section you can find endpoints for:

-   Retrieving Payment Gateways list with details about each Payment Gateway,
    
-   Retrieving Deposits and Withdrawals,
    
-   Making Manual payments,
    
-   Credit in and out.
    

## Get Payment Gateways

A Payment Gateway is a resource responsible for all payment operations in the system. All Deposits and Withdrawals are processed within the frame of a Payment Gateway and following the Payment Gateway configuration.

#### Header parameters

#### Query parameters

Specifies the order in which payment gateway records should be sorted. The default sorting is by creation date in descending order ("created,desc").

Default value

created,desc

Start date and time for filtering payment gateways based on their creation or update time.

End date and time for the same filtering criteria.

### Responses

#### Response Attributes

An array of payment gateway objects,

Hide child attributes

Unique identifier for the payment gateway.

Name of the payment gateway.

The method used by the payment gateway (e.g., credit card, bank transfer).

supportedOperationTypesobject

Types of operations supported by the gateway

Hide child attributes

Indicates whether the gateway supports deposits.

Indicates whether the gateway supports withdrawals.

Configuration related to payment settings

Hide child attributes

The currency used by the payment gateway.

Fee configuration for the payment operations:

Hide child attributes

General fee for processing payments.

Specific fee for deposit operations.

Specific fee for withdrawal operations.

Settings related to the processing of transactions

Hide child attributes

Indicates whether deposits are processed automatically.

Indicates whether withdrawals are processed automatically.

verificationRequiredboolean

Specifies if verification is required for processing transactions.

## Get Deposits

With this endpoint, you can retrieve information about deposits made in the system. You can also request deposits for a specific account using `query`.

#### Header parameters

#### Query parameters

Search term to filter the deposits.

Specifies the page number of the deposit list.

Determines the number of deposit records per page.

Specifies the sorting order of the results, such as by creation date in descending order (created,desc).

Default value

created,desc

Filters the deposits starting from this date.

Filters the deposits up to this date.

### Responses

#### Response Attributes

Contains the deposit records.

Hide child attributes

Unique identifier for the deposit.

Identifier of the partner associated with the deposit.

createdstring (date-time)

Date and time when the deposit was created.

Information about the account associated with the deposit, including accountUuid and email.

Hide child attributes

Unique identifier for the account.

Email address associated with the account.

Personal information of the individual associated with the account.

Hide child attributes

First name of the account holder.

Last name of the account holder.

Details about the manager overseeing the account.

Hide child attributes

Unique identifier for the account manager.

Email address of the account manager.

Full name of the account manager.

Information about the trading account linked to the deposit.

Hide child attributes

Unique identifier for the trading account.

Login details for the trading account.

Identifier for the offer associated with the trading account.

Details regarding the leads related to the account.

Hide child attributes

Unique identifier representing the status of the lead.

Source from where the lead was generated.

providerUuidstring (uuid)

Identifier for the provider related to the lead.

becomeActiveClientTimestring (date-time)

Timestamp when the lead became an active client.

Details about payment requests associated with the deposit.

Hide child attributes

Financial specifics of the payment.

Hide child attributes

Current status of the payment.

Total amount of the payment.

Net amount received after deductions.

Currency in which the payment was made.

paymentGatewayDetailsobject

Information about the payment gateway used.

Hide child attributes

Unique identifier for the payment gateway.

Name of the payment gateway.

Additional details related to the payment.

Hide child attributes

Wallet address used for the payment.

Reference number for the payment.

Unique identifier for the payment transaction.

totalPagesinteger (int32)

Total number of pages available.

totalElementsinteger (int64)

Total number of deposit entries available.

Number of records per page.

## Get Withdrawals

With this endpoint, you can retrieve information about withdrawals made in the system. system. You can also request withdrawals for a specific account using `query`.

#### Header parameters

#### Query parameters

Search term to filter the withdrawal.

Specifies the page number of the withdrawallist.

Determines the number of withdrawalrecords per page.

Specifies the sorting order of the results, such as by creation date in descending order (created,desc).

Default value

created,desc

Filters the withdrawals starting from this date.

Filters the withdrawals up to this date.

### Responses

#### Response Attributes

Contains the withdrawal records.

Hide child attributes

Unique identifier for the withdrawal.

Identifier of the partner associated with the withdrawal.

createdstring (date-time)

Date and time when the withdrawal was created.

Information about the account associated with the withdrawal, including accountUuid and email.

Hide child attributes

Unique identifier for the account.

Email address associated with the account.

Personal information of the individual associated with the account.

Hide child attributes

First name of the account holder.

Last name of the account holder.

accountManagerobject

Hide child attributes

Unique identifier for the account manager.

Email address of the account manager.

Full name of the account manager.

Information about the trading account linked to the withdrawal.

Hide child attributes

Unique identifier for the trading account.

Login details for the trading account.

Identifier for the offer associated with the trading account.

Details regarding the leads related to the account.

Hide child attributes

Unique identifier representing the status of the lead.

Source from where the lead was generated.

providerUuidstring (uuid)

Identifier for the provider related to the lead.

becomeActiveClientTimestring (date-time)

Timestamp when the lead became an active client.

Details about payment requests associated with the withdrawal.

Hide child attributes

This object outlines the financial specifics of the withdrawal.

Hide child attributes

Current status of the withdrawal.

Gross amount of the withdrawal before deductions.

Net amount received after any fees or deductions.

Currency type of the withdrawal (e.g., USD, BTC).

paymentGatewayDetailsobject

Information about the payment gateway used.

Hide child attributes

Unique identifier for the payment gateway.

Name of the payment gateway.

This object includes supplementary details about the withdrawal.

Hide child attributes

The digital wallet address for the withdrawal destination.

A reference number for transaction identification.

Unique identifier for the transaction.

totalPagesinteger (int32)

Total number of pages available.

totalElementsinteger (int64)

Total number of withdrawal entries available.

Number of records per page.

## Manual Deposit

Using this endpoint, you can deposit funds to a Trading Account.

#### Header parameters

#### Body parameters

systemUuidstring Required

he unique system identifier; this is required.

he login identifier for the Trading Account; this is required.

paymentGatewayUuidstring (uuid)Required

Unique identifier for the payment gateway; required for processing the deposit.

The amount of money to deposit; required.

A comment or note accompanying the deposit.

### Responses

#### Response Attributes

uuidstring (uuid)Required

Unique identifier for the deposit transaction; required.

createdstring (date-time)Required

Timestamp when the deposit was made; required.

Current status of the deposit,.

Enum values:

`NEW``AWAITING_CONFIRMATION``PROCESSING``BOOKED``PROCESSING_PAYMENT``DONE``REJECTED``FAILED``FAILED_PAYMENT``CANCELLED_BY_USER`

transactionInfoobject

Hide child attributes

Net amount after processing fees or other deductions.

Currency in which the deposit was made.

## Manual Withdrawal

Using this endpoint, you can withdraw funds from a Trading Account.

#### Header parameters

#### Body parameters

systemUuidstring (uuid)Required

The unique identifier for the system from which funds are withdrawn; required.

Login details for the Trading Account from which the withdrawal is made; required.

paymentGatewayUuidstring (uuid)Required

Identifier for the payment gateway used for the transaction; required.

The amount of money to be withdrawn; required.

A comment or note accompanying the withdrawal.

### Responses

#### Response Attributes

uuidstring (uuid)Required

Unique identifier for the withdrawal transaction; required.

createdstring (date-time)Required

Timestamp of when the withdrawal was initiated; required.

The current status of the withdrawal.

Enum values:

`NEW``AWAITING_CONFIRMATION``PROCESSING``BOOKED``PROCESSING_PAYMENT``DONE``REJECTED``FAILED``FAILED_PAYMENT``CANCELLED_BY_USER`

transactionInfoobject

Hide child attributes

## Credit In

Using this endpoint, you can add a credit to a Trading Account.

#### Header parameters

#### Body parameters

systemUuidstring Required

The unique system identifier for the account; required.

loginstring (uuid)Required

The unique login identifier for the Trading Account to which credit is added; required.

The amount of credit to be added; required.

An optional comment describing the reason for the credit.

### Responses

## Credit Out

Using this endpoint, you can remove a credit from a Trading Account.

#### Header parameters

#### Body parameters

systemUuidstring Required

The unique system identifier for the account; required.

loginstring (uuid)Required

he unique login identifier for the Trading Account from which credit is removed; required.

The amount of credit to be removed; required.

An optional comment describing the reason for the debit.

### Responses

## Trading

The Trading section allows accessing and managing various trading functionalities within the system. With this section, you can place orders and monitor trading activity seamlessly. This section provides the necessary tools for users to efficiently execute trades and stay informed about market conditions.

## Get Symbols

Using this endpoint, you can retrieve information about various symbols (instruments) within the system, including details such as symbol names, currency, trading hours, and more.

#### Header parameters

#### Query parameters

systemUuidstring (uuid)Required

The unique system identifier; required to specify which system's symbols to retrieve.

Specifies the group or category of symbols to be fetched; required.

### Responses

#### Response Attributes

effectiveInstrumentsarray

An array of objects, each representing a trading instrument

Show child attributes

## Open Position

Using this endpoint, you can open a Position for a specific Trading Account.

#### Header parameters

#### Body parameters

systemUuidstring Required

The unique identifier for the system where the account is located; required to specify where the position is to be opened.

The login identifier for the Trading Account on which the position is to be opened; required.

instrumentstring Required

The trading instrument (symbol) for which the position is to be opened; required.

Specifies whether the position is a 'BUY' or 'SELL'; required.

The amount of the instrument to be traded in the position (lots); required.

The price at which a stop-loss order is set to limit potential losses; optional.

The price at which a take-profit order is set to realize profits; optional.

### Responses

#### Response Attributes

The outcome of the request to open a position

Enum values:

`OK``REJECTED``PARTIAL_SUCCESS`

A unique identifier for the order associated with the opened position

## Create Pending Order

Using this endpoint, you can open a Position for a specific Trading Account.

#### Header parameters

#### Body parameters

systemUuidstring Required

The unique identifier for the system where the account is located; required to specify where the position is to be opened.

The login identifier for the Trading Account on which the position is to be opened; required.

instrumentstring Required

The trading instrument (symbol) for which the position is to be opened; required.

Specifies whether the position is a 'BUY' or 'SELL'; required.

The amount of the instrument to be traded in the position (lots); required.

The price at which a stop-loss order is set to limit potential losses; optional.

The price at which a take-profit order is set to realize profits; optional.

The price at which a pending order becomes active and is triggered.

Specifies the type of order: "STOP" for an order that is executed immediately at the current market price, or "LIMIT" for a pending order that is filled only when the price reaches a specified level.

### Responses

#### Response Attributes

The outcome of the request to open a position

Enum values:

`OK``REJECTED``PARTIAL_SUCCESS`

A unique identifier for the order associated with the opened position

## Cancel Pending Order

Using this endpoint, you can open a Position for a specific Trading Account.

#### Header parameters

#### Body parameters

systemUuidstring Required

The unique identifier for the system where the account is located; required to specify where the position is to be opened.

The login identifier for the Trading Account on which the position is to be opened; required.

instrumentstring Required

The trading instrument (symbol) for which the position is to be opened; required.

The unique identifier for the pending order that is to be canceled; required. This ID is used to specify which order should be canceled from the system.

### Responses

#### Response Attributes

The outcome of the request to open a position

Enum values:

`OK``REJECTED``PARTIAL_SUCCESS`

A unique identifier for the order associated with the opened position

## Edit Position

You can edit (Stop Loss and Take Profit values) an already opened Position for a specific Trading Account using this endpoint.

#### Header parameters

#### Body parameters

systemUuidstring Required

The unique identifier for the system where the trading account is located; required.

The login identifier for the Trading Account in which the position exists; required.

The unique identifier of the position to be edited; required.

The new stop loss price to limit potential losses; optional, only include if modification is needed.

The new take profit price to capture desired profits; optional, only include if modification is needed.

### Responses

#### Response Attributes

Indicates the result of the request to edit the position

Enum values:

`OK``REJECTED``PARTIAL_SUCCESS`

A unique identifier for the order associated with the edited position

## Close Position

You can close a Position for a specific Trading Account using this endpoint. It is also possible to close multiple positions with one request.

#### Header parameters

#### Body parameters

systemUuidstring Required

The unique identifier for the system where the trading account is located; required to specify the system context.

The login identifier for the Trading Account from which positions are to be closed; required.

closePositionsarray Required

Hide child attributes

positionIdstring Required

The unique identifier of the trading position to be closed.

instrumentstring Required

The trading instrument of the position.

### Responses

#### Response Attributes

The outcome of the request to close positions

Enum values:

`OK``REJECTED``PARTIAL_SUCCESS`

A unique identifier for the transaction or order associated with the closure of the positions

## Close Position (Partially)

This endpoint allows you to partially close a Position for a specific Trading Account, reducing the position size while keeping the rest open.

#### Header parameters

#### Body parameters

systemUuidstring Required

The unique identifier for the system where the trading account is located; required to specify the system context.

The login identifier for the Trading Account from which positions are to be closed.

positionIdstring Required

The unique identifier of the Position to be partially closed.

instrumentstring Required

The financial instrument for the Position (e.g., EUR/USD).

The amount in LOTs of the Position to be closed.

### Responses

#### Response Attributes

The outcome of the request to close positions

Enum values:

`OK``REJECTED``PARTIAL_SUCCESS`

A unique identifier for the transaction or order associated with the closure of the positions

## Trading Data

The Trading Data section includes endpoints to retrieve Open and Closed Positions, Active Orders, and Ledgers. There is also a “Candles” sub-section, where you can find an endpoint to retrieve Candles data for a specific instrument.

## Get Open Positions

With this endpoint, you can retrieve all open positions for a specific trading account.

#### Header parameters

#### Query parameters

systemUuidstring Required

The unique system identifier for the account.

The login identifier for the Trading Account.

### Responses

#### Response Attributes

An array of objects, each detailing a trading instrument.

Hide child attributes

The unique identifier of the position.

The trading instrument of the position.

An alternative name or alias for the trading instrument.

The amount of the instrument that is currently open.

Indicates whether the position is a 'BUY' or 'SELL'.

openTimestring (date-time)

The timestamp when the position was opened.

The price at which the position was opened.

The set stop loss value to limit potential losses.

The set take profit value to secure profits.

The swap fee applied to the position.

The current profit or loss of the position.

The net profit after accounting for fees and costs.

The current market price of the trading instrument.

The commission fees associated with the position.

positionsarray

Hide child attributes

The unique identifier of the position.

The trading instrument of the position.

An alternative name or alias for the trading instrument.

The amount of the instrument that is currently open.

This attribute indicates the volume of the instrument in the position before any recent adjustments or partial closures.

Indicates whether the position is a 'BUY' or 'SELL'.

openTimestring (date-time)

The timestamp when the position was opened.

The price at which the position was opened.

The swap fee applied to the position.

The current profit or loss of the position.

The net profit after accounting for fees and costs.

The current market price of the trading instrument.

The commission fees associated with the position.

## Get Closed Positions

With this endpoint, you can retrieve all closed positions for a specific trading account.

#### Header parameters

#### Query parameters

systemUuidstring (date-time)Required

The unique identifier for the system associated with the trading account; required.

loginstring (date-time)Required

The login identifier for the Trading Account; required.

Start date for the query range to fetch closed positions; optional.

End date for the query range to fetch closed positions; optional.

### Responses

#### Response Attributes

closedPositionsarray

Hide child attributes

Unique identifier of the closed position.

openTimestring (date-time)

Timestamp when the position was opened.

Price at which the position was opened.

Trading instrument of the position.

Alternative name or alias for the trading instrument.

Volume of the instrument traded in the position.

Stop loss value set for the position.

Take profit value set for the position.

Timestamp when the position was closed.

Price at which the position was closed.

Commission fees associated with the position.

Swap fees incurred by the position.

Profit or loss generated by the position.

Indicates whether the position was a 'BUY' or 'SELL'.

Net profit or loss after accounting for fees and costs.

Identifier for the order that closed the position.

## Get Active Orders

With this endpoint, you can retrieve all Active Orders for a specific Trading Account.

#### Header parameters

#### Query parameters

systemUuidstring Required

The unique system identifier for the account; required to specify the system from which to retrieve active orders.

The login identifier for the Trading Account; required to identify which account's orders are to be fetched.

### Responses

#### Response Attributes

An array of objects, each detailing an active order.

Hide child attributes

Unique identifier for the order.

Trading instrument of the order.

An alternative name or alias for the trading instrument.

Volume of the instrument ordered.

Type of order, with possible values being 'LIMIT', 'STOP', or 'UNTYPE'.

creationTimestring (date-time)

Timestamp marking when the order was created.

Price at which the order becomes active.

Specifies whether the order is to 'BUY' or 'SELL'.

The stop loss price set for the order to limit potential losses.

The take profit price set for the order to secure profits.

A comment or note associated with the order.

## Get Ledgers

With this endpoint, you can retrieve all Ledgers for a specific Trading Account.

#### Header parameters

#### Query parameters

systemUuidstring (date-time)Required

The unique system identifier for the account; required to specify from which system the ledgers are retrieved.

loginstring (date-time)Required

The login identifier for the Trading Account; required to identify the account whose ledger entries are to be fetched.

typesarray Required

Enum values:

`DEPOSIT``WITHDRAWAL``CREDIT_IN``CREDIT_OUT``AGENT_COMMISSION``COMMISSIONS``SWAPS`

Start date for filtering the ledger entries; optional.

End date for filtering the ledger entries; optional.

The maximum number of ledger entries to return in the response; optional.

### Responses

#### Response Attributes

An array of objects, each representing a ledger entry.

Hide child attributes

Unique identifier for the ledger entry.

The type of transaction recorded in the ledger.

Enum values:

`DEPOSIT``WITHDRAWAL``CREDIT_IN``CREDIT_OUT``AGENT_COMMISSION``COMMISSIONS``SWAPS``OTHER`

Timestamp when the transaction was recorded.

Profit or loss amount associated with the transaction.

A comment or description provided for the transaction.

## Retrieve Orders History by Login List or Groups

With this endpoint, you can retrieve all open positions for a specific trading account.

#### Header parameters

#### Body parameters

systemUuidstring Required

statusesarray Required

Enum values:

`FILLED``CANCELLED``REJECTED``ADDED``ACTIVE_ORDER_PRICE_EDITED`

### Responses

#### Response Attributes

historicalOrdersarray

Hide child attributes

## Retrieve Ledgers by Login List or Groups

With this endpoint, you can retrieve all Ledgers for a specific Trading Account.

#### Header parameters

#### Body parameters

systemUuidstring Required

### Responses

#### Response Attributes

operationsarray

Hide child attributes

## Candles

## Get Candles

Get Candles endpoint allows you to retrieve candle data for a specific symbol and time interval. You will access information on the opening, high, low, and closing prices of candles within the specified time frame.

#### Header parameters

#### Query parameters

systemUuidstring (uuid)Required

The unique system identifier; required to specify which trading system's symbol data to retrieve.

The trading symbol for which candle data is requested; required.

The time interval for the candle data.

fromstring (date-time)Required

The start datetime from which to retrieve candle data; required.

tostring (date-time)Required

The end datetime up to which candle data should be retrieved; required.

### Responses

#### Response Attributes

The symbol for which the data is provided.

The time interval of the candle data, reflecting the same values as specified in the query.

An array of objects, each representing a candle.

Hide child attributes

Timestamp representing the start time of the candle.

Opening price of the candle.

Highest price reached during the candle's interval.

Lowest price during the candle's interval.

Closing price of the candle.

## Prop Trading

## Challenges

In the challenges section, you will find endpoints that allow you to perform various operations on challenges:

-   Get challenges
    
-   Create challenge
    
-   Update challenge
    

Sandbox

https://broker-api-demo.match-trader.com

## Get Challenges

This endpoint retrieves a list of all challenges available on the platform with their details

#### Header parameters

### Responses

#### Response Attributes

branchobject

Hide child attributes

systemobject

Hide child attributes

operationobject

Hide child attributes

challengeStatisticsEnabledboolean

phasesarray

Hide child attributes

profitSplitPercentagesobject

Hide child attributes

minimumTradingPeriodnumber

maxDailyLossPercentagenumber

profitTargetPercentagenumber

maxDailyLossCalculationTypestring

autoEvaluationEnabledboolean

lockAccountOnWithdrawalRequestedboolean

## Create Challenge

This endpoint allows administrators to create a new trading challenge on the platform. Each challenge sets specific objectives, rewards, and conditions for traders to meet within a defined timeframe. This endpoint is essential for managing and introducing new challenges to engage traders and enhance their trading skills.

#### Header parameters

#### Body parameters

challengeStatisticsEnabledboolean

phasesarray

Hide child attributes

profitSplitPercentagesobject

Hide child attributes

minimumTradingPeriodnumber

maxDailyLossPercentagenumber

profitTargetPercentagenumber

maxDailyLossCalculationTypestring

autoEvaluationEnabledboolean

lockAccountOnWithdrawalRequestedboolean

### Responses

#### Response Attributes

challengeStatisticsEnabledboolean

phasesarray

Hide child attributes

profitSplitPercentagesobject

Hide child attributes

minimumTradingPeriodnumber

maxDailyLossPercentagenumber

profitTargetPercentagenumber

maxDailyLossCalculationTypestring

autoEvaluationEnabledboolean

lockAccountOnWithdrawalRequestedboolean

## Update Challenge

This endpoint allows administrators to update an existing trading challenge on the platform. This endpoint is essential for managing and adjusting challenges to ensure they remain relevant and engaging for traders.

#### Header parameters

#### Body parameters

challengeStatisticsEnabledboolean

phasesarray

Hide child attributes

profitSplitPercentagesobject

Hide child attributes

minimumTradingPeriodnumber

maxDailyLossPercentagenumber

profitTargetPercentagenumber

maxDailyLossCalculationTypestring

autoEvaluationEnabledboolean

lockAccountOnWithdrawalRequestedboolean

### Responses

#### Response Attributes

challengeStatisticsEnabledboolean

phasesarray

Hide child attributes

profitSplitPercentagesobject

Hide child attributes

minimumTradingPeriodnumber

maxDailyLossPercentagenumber

profitTargetPercentagenumber

maxDailyLossCalculationTypestring

autoEvaluationEnabledboolean

lockAccountOnWithdrawalRequestedboolean

## Trading Accounts

In the Trading Accounts section you can find endpoints for:

-   Get trading accounts
    
-   Create trading account
    
-   Update trading account
    
-   Force snapshot
    

## Get Trading Accounts

With the Get Accounts endpoint, you can retrieve detailed information about all Prop Trading Accounts within the system.

#### Header parameters

#### Query parameters

Filter only by the following fields: login, name, email, phaseName, challengeName

Default value : created,desc

### Responses

#### Response Attributes

contentarray

Hide child attributes

## Create Trading Account

In this section, you can find the endpoint for creating new Prop Trading Accounts in the system.

#### Header parameters

#### Query parameters

If true, the account is immediately active after creation

The phase of the challenege in which the account is to be created. The first phase starts with phaseStep = 1

#### Body parameters

challengeIdstring Required

accountUuidstring Required

### Responses

#### Response Attributes

challengeDetailsobject

Hide child attributes

endOfDayBalanceSnapshotnumber

isReadyForEvaluationboolean

challengeTargetsobject

Hide child attributes

maxDailyLossEquityLevelnumber

profitTargetEquityLevelnumber

## Update Trading Account

With this endpoint, you can update and modify Prop Trading Account details.

#### Header parameters

#### Body parameters

challengeIdstring Required

phaseStep is counted from 1, first phase = 1 second phase = 2 etc.

The system automatically determines the appropriate status based on the account’s current phase

Enum values:

`ACTIVE`

Refer to two states: ACTIVE\_FUNDED – when the account is active and is in the funded phase. ACTIVE\_PARTICIPATING\_IN\_CHALLENGE – when the account is active and participating in a challenge

`INACTIVE`

Corresponds to: LOCKED\_FAILED\_CHALLENGE, which indicates the account has failed a challenge and is locked

### Responses

## Force Snapshot

With the Force Snapshots endpoint, you can reset account max daily loss to not wait to original daily snapshot until the original daily snapshot entire max daily loss is available again.

#### Header parameters

#### Path parameters

#### Body parameters

### Responses

## Payments

In the Payments section you can find endpoints for:

-   Manual Withdrawal
    
-   Get max amount to withdrawal
    
-   Get amount to deposit
    

## Manual Withdrawal

Using this endpoint, you can withdraw funds from a Trading Account. It's important to use this withdrawal endpoint for prop trading accounts to automatically calculate new max daily loss.

#### Header parameters

#### Path parameters

#### Body parameters

### Responses

#### Response Attributes

transactionInfoobject

Hide child attributes

## Get Max Amount To Withdraw (copy)

This endpoint retrieves the maximum amount that the user is eligible to withdraw from their challenge. This takes into consideration factors such as account balance, initial balance, and profit split. This endpoint is essential to understand current withdrawal capacity.

#### Header parameters

#### Path parameters

### Responses

#### Response Attributes

The total profit made by the trader may be withdrawn. Does not include the profit split

profitSplitobject

Hide child attributes

## Get Amount For Deposit

This endpoint retrieves the required amount that the authenticated user should deposit into their trading account to activate the challenge.

#### Header parameters

#### Path parameters

### Responses

## Evaluation Requests

In the Evaluation Requests section you can find endpoints for:

-   Get evaluation requests
    
-   Confirm evaluation
    
-   Reject evaluation
    

## Get Evaluation Requests

With the Get Evaluation Requests endpoint, you can retrieve information about all Prop Trading Accounts which are currently waiting for the evaluation to the next phase.

#### Header parameters

#### Query parameters

Default value: requestCreated,desc

### Responses

#### Response Attributes

contentarray

Hide child attributes

## Confirm Evaluation

With the Confirm Evaluation endpoint, you can confirm pending request to move to next phase.

#### Header parameters

#### Path parameters

### Responses

## Reject Evaluation

With the Reject Evaluation endpoint, you can confirm pending request to move to next phase.

#### Header parameters

#### Path parameters

### Responses
