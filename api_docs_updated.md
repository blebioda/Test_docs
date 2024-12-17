Match-Trade Documentation

::: {#__next}
::: {.jsx-751833425 .th-publish-page-layout sidebar="false" theme="true" widget-fullscreen="false" preview="false" style="opacity:1"}
::: {#sidebarWrapper}
::: {.th-left-sidebar__mobile .sf-hidden}
:::

::: {.th-left-sidebar}
::: {.th-left-sidebar__brand}
[](https://app.theneo.io/match-trade/broker-api/prop-trading){.th-left-sidebar__brand-logo}
===========================================================================================
:::

::: {.th-left-sidebar__menu}
::: {.nav-group}
-   [[Introduction]{title="Introduction"}](https://app.theneo.io/match-trade/broker-api/introduction){.nav-item}

-   [[Authentication]{title="Authentication"}](https://app.theneo.io/match-trade/broker-api/authentication){.nav-item}

-   [[gRPC]{title="gRPC"}](https://app.theneo.io/match-trade/broker-api/grpc){.nav-item}

-   ::: {.nav-header header="false"}
    [Errors]{title="Errors"}
    :::

-   ::: {.nav-header header="false"}
    [Branches, Offers, Roles]{title="Branches, Offers, Roles"}
    :::

-   ::: {.nav-header header="false"}
    [Accounts]{title="Accounts"}
    :::

-   ::: {.nav-header header="false"}
    [Payments]{title="Payments"}
    :::

-   ::: {.nav-header header="false"}
    [Trading]{title="Trading"}
    :::

-   ::: {.nav-header .active .children-visible header="false"}
    [Prop Trading]{title="Prop Trading"}
    :::

    -   ::: {.nav-header header="false"}
        [Challenges]{title="Challenges"}
        :::

    -   ::: {.nav-header header="false"}
        [Trading Accounts]{title="Trading Accounts"}
        :::

    -   ::: {.nav-header header="false"}
        [Payments]{title="Payments"}
        :::

    -   ::: {.nav-header}
        [Evaluation Requests]{title="Evaluation Requests"}
        :::
:::
:::

::: {.th-left-sidebar__poweredBy}
:::
:::
:::

::: {#contentWrapper .th-content .false}
::: {search="false"}
::: {.th-content__top-nav}
::: {.th-content__top-nav_inner}
Search

::: {.th-content_top-menu}
::: {.th-content_top-menu-items}
:::

::: {.ThemeSwitcher_th_theme_switcher_border__dluqK}
:::

::: {.ThemeSwitcher_th_theme_switcher_wrapper__6CI2v .th-content__theme-switcher .th-content-theme-switcher-mobile}
::: {.ThemeSwitcher_th_theme_switcher__7yV2g role="button" tabindex="0"}
:::
:::

::: {.th-content_top-menu-items-trigger .sf-hidden}
:::
:::
:::

::: {.published-mobile-sidebar .sf-hidden}
:::
:::

::: {.th-content__section-list}
::: {#THContent .th-content__section-content}
::: {#66b1e5a7998527f39cd647bd .section .th-content_section-title .th-content_section-title-1}
Introduction
============
:::

::: {.section .th-content_section-item data-href="introduction"}
::: {.th-content__section}
::: {.th-content__section-left-column}
::: {theme="true"}
::: {.th-content__text}
The Broker API is a versatile API platform designed for various
integrations, enabling streamlined management and interaction with
multiple services through a single access point. This API supports REST
and gRPC protocols, providing flexibility in integration and ensuring
compatibility with diverse client systems. It facilitates secure,
efficient administrative operations and service management, making it an
essential tool for clients looking to optimize their operational
workflows.

In our system, each broker has a unique `partnerID`{.slate-code}
assigned. Every operation via this API is performed within that
particular partnerID. Since we encode your partnerID in your
Authorization token, you don\'t have to consider this parameter when
sending requests, but you will see this parameter across multiple
different endpoints. For the Sandbox environment,
`partnerID`{.slate-code} = 0 and is shared between every account in the
system. It means you will have access to test data added by other
integrations.

::: {.callout-widget data-type="info" style="width:100%;min-width:100%"}
::: {.callout-icon}
:::

<div>

Server time is GMT (UTC+0).

</div>
:::

::: {.callout-widget data-type="info" style="width:100%;min-width:100%"}
::: {.callout-icon}
:::

<div>

All time-related fields accept and return values according to the RFC
3339 standard. For example `2024-01-13T09:20:04.651Z`{.slate-code}.

</div>
:::

::: {.callout-widget data-type="info" style="width:100%;min-width:100%"}
::: {.callout-icon}
:::

<div>

The request limit is 500 requests/minute.

</div>
:::
:::
:::
:::

::: {.th-content__section-right-column}
::: {.baseUrlView_container__6p3gC .th-baseurl-module}
::: {.baseUrlView_header__2Xs4S .th-baseurl-module__header}
Base URL

::: {.editor-show-delete-action-buttons}
:::
:::

::: {.baseUrlView_body__nlK5X .th-baseurl-module__body}
Sandbox

https://broker-api-demo.match-trader.com
:::
:::
:::

::: {.th-feedback}
::: {.th-feedback__header}
[Was this section helpful?]{.th-feedback__question}

Yes

::: {.feedback-divider}
:::

No

[[]{.cds--popover-content .sf-hidden}[]{.cds--popover-caret
.sf-hidden}]{#id-4 .cds--popover}
:::
:::
:::
:::

::: {#66b1e5a7998527f39cd64a2b .section .th-content_section-title .th-content_section-title-1}
Authentication
==============
:::

::: {.section .th-content_section-item data-href="authentication"}
::: {.th-content__section-full}
::: {.th-content__text}
Our system implements a security mechanism that utilizes token-based
authorization to ensure secure resource access. The authentication
process involves using an [`Authorization`{.slate-code
style="color:var(--tw-prose-code);font-size:0.875em"}]{.slate-color
style="color:var(--tw-prose-code);font-size:0.875em"} header, following
the token scheme. This section outlines the steps required for obtaining
and using the authentication token to access protected resources.

 {#Obtaining-the-Token .slate-h2}

::: {.copy-button}
:::

Obtaining the Token

The authentication token is obtained from our Customer Relationship
Management (CRM) system. You must first authenticate yourself within the
CRM to receive a token. This token acts as a digital key, granting
access to the system\'s resources for the duration of the token\'s
validity. Tokens are valid until revoked.

 {#Using-the-Token-for-Access .slate-h2}

::: {.copy-button}
:::

Using the Token for Access

::: {.code-block-wrapper}
::: {.code-block-header}
:::

``` {.slate-CodeBlockElement .slate-code_block}
Authorization: <Your_Token_Here>
```
:::

 {#Incorporating-Authorization-in-gRPC-Requests .slate-h2}

::: {.copy-button}
:::

Incorporating Authorization in gRPC Requests

In gRPC, the traditional concept of HTTP headers is abstracted through
metadata, allowing for the transmission of key-value pairs alongside RPC
calls. To secure gRPC services with token-based authentication, the
Authorization metadata must be included with each call. This process is
similar to using the Authorization header in HTTP requests but is
adapted to fit the gRPC framework.

###  {#Adding-the-Authorization-Metadata .slate-h3}

::: {.copy-button}
:::

Adding the Authorization Metadata

To include an authentication token in a gRPC request, the token must be
added to the call\'s metadata using the \'Authorization\' key, followed
by the token value. This ensures that the server side of the gRPC
service can authenticate the request by validating the token.

###  {#Code-Example--Python .slate-h3}

::: {.copy-button}
:::

Code Example (Python)

PYTHON

::: {.code-block-wrapper}
::: {.code-block-header}
python
:::

``` {.slate-CodeBlockElement .slate-code_block .language-python tabindex="0"}
pythonCopy codeimport grpc# Create a gRPC channelchannel = grpc.insecure_channel('your_grpc_service_endpoint')# Prepare metadata with the Authorization tokenmetadata = [('Authorization', '<Your_Token_Here>')]# Create a stub (client)stub = YourServiceStub(channel)# Make a call with the Authorization metadataresponse = stub.YourRpcMethod(request, metadata=metadata)
```
:::

Replace [`<Your_Token_Here>`{.slate-code
style="color:var(--tw-prose-code);font-size:0.875em"}]{.slate-color
style="color:var(--tw-prose-code);font-size:0.875em"} with the actual
token obtained from your CRM system, and adjust
[`YourServiceStub`{.slate-code
style="color:var(--tw-prose-code);font-size:0.875em"}]{.slate-color
style="color:var(--tw-prose-code);font-size:0.875em"} and
[`YourRpcMethod`{.slate-code
style="color:var(--tw-prose-code);font-size:0.875em"}]{.slate-color
style="color:var(--tw-prose-code);font-size:0.875em"} to match your gRPC
service definition.
:::

::: {.th-feedback}
::: {.th-feedback__header}
[Was this section helpful?]{.th-feedback__question}

Yes

::: {.feedback-divider}
:::

No

[[]{.cds--popover-content .sf-hidden}[]{.cds--popover-caret
.sf-hidden}]{#id-8 .cds--popover}
:::
:::
:::
:::

::: {#66b1e5a7998527f39cd64a2d .section .th-content_section-title .th-content_section-title-1}
gRPC
====
:::

::: {.section .th-content_section-item data-href="grpc"}
::: {.th-content__section}
::: {.th-content__section-left-column}
::: {theme="true"}
::: {.th-content__text}
This gRPC model defines several services within a package designed for
the Broker API, focusing on positions, ledgers, trading events, and
related data. Below is the complete documentation for all services and
their methods.

::: {.callout-widget data-type="info" style="width:100%;min-width:100%"}
::: {.callout-icon}
:::

<div>

The connection will be dropped if no new data appears in the latest 15
minutes.

</div>
:::

###  {#1--PositionsServiceExternal .slate-h3}

::: {.copy-button}
:::

1\. PositionsServiceExternal

**getClientPositionsStream:** Streams client positions for a specified
trading account, accepts a
`PBClientPositionRequestExternal`{.slate-code} message with the
`systemUuid`{.slate-code} and `login`{.slate-code}, returns a stream of
`PBPositionStreamResponseExternal`{.slate-code} messages containing
position details such as ID, symbol, alias, volume, side, open time,
open price, stop loss/take profit, profit, net profit, swap, commission,
and current price.

###  {#2--LedgerServiceExternal .slate-h3}

::: {.copy-button}
:::

2\. LedgerServiceExternal

**getLedgersStreamByLogin:** Streams ledger entries for a specific
trading account, accepts a `PBLedgerRequestByLoginExternal`{.slate-code}
message with the `systemUuid`{.slate-code}, `login`{.slate-code}, and
`historyOperationType`{.slate-code}, returns a stream of
`PBLedgerStreamResponseExternal`{.slate-code} messages with operation
type, time, profit, comment, and associated login.

**getLedgersStreamByGroupsOrLogins:** Streams ledger entries filtered by
specified groups or account logins, accepts a
`PBLedgersRequestByGroupsOrLoginsExternal`{.slate-code} message with the
`systemUuid`{.slate-code}, optional logins, groups, and
`historyOperationType`{.slate-code}, returns a stream of
`PBLedgersStreamResponseExternal`{.slate-code} messages with grouped
ledger details.

###  {#3--GroupsServiceExternal .slate-h3}

::: {.copy-button}
:::

3\. GroupsServiceExternal

**getGroupChangesStream:** Streams updates to group configurations,
accepts a `GroupChangeStreamRequestExternal`{.slate-code} message with
the `systemUuid`{.slate-code} and monitored groups, returns a stream of
`GroupChangeStreamResponseExternal`{.slate-code} messages containing
either a `PBGroupUpdated`{.slate-code} object with updated group
parameters like margin, commission structure, and symbols, or a
`PBGroupDeleted`{.slate-code} object with the reason for group removal.

###  {#4--TradingEventsServiceExternal .slate-h3}

::: {.copy-button}
:::

4\. **TradingEventsServiceExternal**

**getTradingEventsStream:** Streams trading events for specified
accounts and groups, accepts a
`PBTradingEventsStreamRequestExternal`{.slate-code} message with account
logins and group IDs, returns a stream of
`TradingEventsStreamResponseExternal`{.slate-code} messages containing
events like margin calls, stop-outs, take profits, stop losses, or order
activations, each event includes details specific to its type.

###  {#5--OrderServiceExternal .slate-h3}

::: {.copy-button}
:::

5\. OrderServiceExternal

**getOrdersUpdateStreamByGroupsOrLogins:** Streams updates to orders for
specified accounts and groups, accepts a
`PBOrdersUpdateStreamRequestExternal`{.slate-code} message with the
`systemUuid`{.slate-code}, logins, groups, and flags for blocked/locked
orders, returns a stream of
`PBOrdersUpdateStreamResponseExternal`{.slate-code} messages containing
order details such as ID, symbol, alias, volume, type, creation time,
activation price, side, stop loss, take profit, comment, update type,
group, and login.

###  {#6--QuotationsServiceExternal .slate-h3}

::: {.copy-button}
:::

6\. QuotationsServiceExternal

**getQuotationsWithMarkupStream:** Streams real-time quotations for
specified instruments with markups applied, accepts a
`PBQuotationsWithMarkupStreamRequestExternal`{.slate-code} message with
the `systemUuid`{.slate-code}, instruments, group, and optional
throttling and statistics flags, returns a stream of
`PBQuotationsWithMarkupStreamResponseExternal`{.slate-code} messages
containing bid and ask prices, timestamps, and optional daily
statistics.

###  {#7--AccountInfoServiceExternal---coming-soon .slate-h3}

::: {.copy-button}
:::

7\. AccountInfoServiceExternal - coming soon

**getAccountInfoChangedStream:** Streams updates about account
information changes, accepts a
`PBAccountInfoChangedRequestExternal`{.slate-code} message with the
`systemUuid`{.slate-code}, groups, and flags for blocked/locked
accounts, returns a stream of
`PBAccountInfoChangedStreamResponseExternal`{.slate-code} messages
containing either `PBAccountInfoUpdated`{.slate-code} objects with
account details like login, group, leverage, status, and currency, or
`PBAccountInfoDeleted`{.slate-code} objects with the reason for account
deletion.

###  {#8--SymbolsServiceExternal---coming-soon .slate-h3}

::: {.copy-button}
:::

8\. SymbolsServiceExternal - coming soon

**getSymbolsChangedStream:** Streams updates on trading symbols, accepts
a `PBSymbolChangedRequestExternal`{.slate-code} message with the
`systemUuid`{.slate-code}, returns a stream of
`PBSymbolChangedStreamResponseExternal`{.slate-code} messages containing
either `PBSymbolUpdated`{.slate-code} objects with symbol details like
trading hours, precision, contract size, leverage, and commission, or
`PBSymbolDeleted`{.slate-code} objects indicating symbols that were
removed.

::: {.slate-code_line}
:::

::: {.code-block-wrapper style="width:100%;min-width:100%"}
::: {.code-block-header}
PROTOBUF
:::

``` {.slate-CodeBlockElement .slate-code_block .language-protobuf tabindex="0"}
 syntax = 'proto3';
import "google/protobuf/empty.proto";
option java_multiple_files = true;
option java_package = 'com.matchtrade.broker_api.grpc';

package com.matchtrade.broker_api.grpc;

service PositionsServiceExternal {
  rpc getClientPositionsStream (PBClientPositionRequestExternal) returns (stream PBPositionStreamResponseExternal) {
  }
}

service LedgerServiceExternal {
  rpc getLedgersStreamByLogin(PBLedgerRequestByLoginExternal) returns (stream PBLedgerStreamResponseExternal) {
  }

  rpc getLedgersStreamByGroupsOrLogins(PBLedgersRequestByGroupsOrLoginsExternal) returns (stream PBLedgersStreamResponseExternal) {
  }
}

service OrderServiceExternal {
  rpc getOrdersUpdateStreamByGroupsOrLogins(PBOrdersUpdateStreamRequestExternal) returns (stream PBOrdersUpdateStreamResponseExternal) {
  }
}

service QuotationsServiceExternal {
  rpc getQuotationsWithMarkupStream (PBQuotationsWithMarkupStreamRequestExternal) returns (stream PBQuotationsWithMarkupStreamResponseExternal) {
  }
}

service GroupServiceExternal {
  rpc getGroupChangesStream(GroupChangeStreamRequestExternal) returns (stream GroupChangeStreamResponseExternal) {
  }
}

service TradingEventsServiceExternal {
  rpc getTradingEventsStream (PBTradingEventsStreamRequestExternal) returns (stream TradingEventsStreamResponseExternal) {
  }
}
service AccountInfoServiceExternal {
  rpc getAccountInfoChangedStream  (PBAccountInfoChangedRequestExternal) returns (stream PBAccountInfoChangedStreamResponseExternal) {
  }
}

service SymbolsServiceExternal{
  rpc getSymbolsChangedStream(PBSymbolChangedRequestExternal) returns (stream PBSymbolChangedStreamResponseExternal) {}
}

message PBOrdersUpdateStreamRequestExternal {
  string systemUuid = 1;
  repeated int64 logins = 2;
  repeated string groups = 3;
  bool includeBlocked = 4;
  bool includeLocked = 5;
}

message PBOrdersUpdateStreamResponseExternal {
  string orderId = 1;
  string symbol = 2;
  string alias = 3;
  string volume = 4;
  PBPendingTypeExternal type = 5;
  string creationTime = 6;
  string activationPrice = 7;
  PBOrderSideExternal side = 8;
  string stopLoss = 9;
  string takeProfit = 10;
  string comment = 11;
  PBRequestUpdateTypeExternal requestUpdateType = 12;
  string groupName = 13;
  int64 login = 14;
}

enum PBPendingTypeExternal {
  UNTYPE = 0;
  LIMIT = 1;
  STOP = 2;
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

enum PBHistoryOperationTypeExternal {
  DEPOSIT = 0;
  WITHDRAWAL = 1;
  CREDIT_IN = 2;
  CREDIT_OUT = 3;
  AGENT_COMMISSION = 4;
  COMMISSIONS = 5;
  SWAPS = 6;
  OTHER = 7;
  CLOSED_POSITION = 8;
}

message PBLedgerRequestByLoginExternal {
  string systemUuid = 1;
  string login = 2;
  repeated PBHistoryOperationTypeExternal historyOperationType = 3;
}

message PBLedgersRequestByGroupsOrLoginsExternal {
  string systemUuid = 1;
  repeated string logins = 2;
  repeated string groups = 3;
  repeated PBHistoryOperationTypeExternal historyOperationType = 4;
}

message PBLedgerStreamResponseExternal {
  string id = 1;
  PBHistoryOperationTypeExternal historyOperationType = 2;
  string time = 3;
  double profit = 4;
  string comment = 5;
  string group = 6;
  int64 login = 7;
}

message PBLedgersStreamResponseExternal {
  repeated PBLedgerStreamResponseExternal financeRecordDto = 1;
}

message PBQuotationsWithMarkupStreamRequestExternal {
  string systemUuid = 1;
  repeated string instruments = 2;
  string group = 3;
  optional int32 throttlingMs = 4;
  bool includeDailyStatistics = 5;
}

message PBQuotationsWithMarkupStreamResponseExternal {
  repeated PBQuotationSimpleExternal quotations = 1;
}

message PBQuotationSimpleExternal {
  string symbol = 1;
  double bidPrice = 2;
  double askPrice = 3;
  int64 timestampInMillis = 4;
  optional PBDailyStatisticsExternal dailyStatistics = 5;
}

message GroupChangeStreamRequestExternal {
  string systemUuid = 1;
  repeated string groups = 2;
}

message GroupChangeStreamResponseExternal {
  oneof msg {
    PBGroupUpdated groupUpdated = 1;
    PBGroupDeleted groupDeleted = 2;
  }
}

message PBGroupUpdated {
  string group = 1;
  string currency = 2;
  int32 currencyPrecision = 3;
  string groupOwner = 4;
  map<string, PBGroupSymbol> symbols = 5;
  PBMarginCalculationType marginCalculationType = 6;
  bool isManager = 7;
  bool isAdmin = 8;
  bool isBrokerManager = 9;
  int64 marginCall = 10;
  int32 depositCurrencyPrecision = 11;
  int64 swapCalculationTime = 12;
  PBStopoutAndMarginCallType stopoutAndMarginCallType = 13;
  bool commissionUpfront = 14;
  bool islamicSwap = 15;
  bool demoGroup = 16;
  PBHedgeMarginCalculationType hedgeMarginCalculationType = 17;
  int64 eodMode = 18;
  int64 eomMode = 19;
  int64 eodSnapshotTime = 20;
  bool defaultRetailEnabled = 21;
  bool coverageMode = 22;
  int64 orderProcessingDelay = 23;
  double stopoutLevel = 24;
  bool hedgingEnabled = 25;
  int64 pendingMultiplierPer1000 = 26;
  int64 defaultLeverageRatioPercent = 27;
  int64 commissionPerMillion = 28;
  int64 agentCommissionPerMillion = 29;
}

message PBGroupDeleted {
  string group = 1;
  GroupDeletedReason reason = 2;

  enum GroupDeletedReason {
    GROUP_DELETED_REASON_UNKNOWN = 0;
    GROUP_DELETED_REASON_DELETED = 1;
    GROUP_DELETED_REASON_RIGHTS_REVOKED = 2;
  }
}

message PBGroupSymbol {
  string symbol = 1;
  string bidMarkup = 2;
  string askMarkup = 3;
  double leverage = 4;
  string swapBuy = 5;
  string swapSell = 6;
  int64 freezeLevel = 7;
  int64 stopsLevel = 8;
  PBSwapType swapType = 9;
  string volumeMin = 10;
  string volumeMax = 11;
  string minCommission = 12;
  bool fixedLeverage = 13;
  int64 rawLeverage = 14;
  PBCommissionType commissionType = 15;
  int64 commissionPerMillion = 16;
}

enum PBMarginCalculationType {
  UNKNOWN_MARGIN_CALCULATION_TYPE = 0;
  UNREALIZED_LOSS_ONLY = 1;
  UNREALIZED_PROFIT_LOSS = 2;
}

enum PBSwapType {
  UNKNOWN_SWAP_TYPE = 0;
  PIPS_SWAP_TYPE = 1;
  PERCENTS = 2;
  NOT_DEFINED = 3;
}

enum PBCommissionType {
  UNDEFINED = 0;
  USD_PER_MILLION = 1;
  USD_PER_CONTRACT = 2;
  USD_PER_LOT = 3;
  PERCENTAGE = 4;
  PIPS_COMMISSION_TYPE = 5;
}

enum PBStopoutAndMarginCallType {
  STOPOUT_TYPE_UNDEFINED = 0;
  STOPOUT_TYPE_MARGIN_LEVEL_PERCENTAGE = 1;
  STOPOUT_TYPE_ABSOLUTE_EQUITY_USD = 2;
  STOPOUT_TYPE_OFF = 3;
}

enum PBHedgeMarginCalculationType {
  HEDGE_MARGIN_CALCULATION_TYPE_UNSPECIFIED = 0;
  HEDGE_MARGIN_CALCULATION_TYPE_NETTING = 1;
  HEDGE_MARGIN_CALCULATION_TYPE_LARGER_LEG = 2;
}

message PBTradingEventsStreamRequestExternal {
  string systemUuid = 1;
  repeated int64 logins = 2;
  repeated string groups = 3;
  bool includeBlocked = 4;
  bool includeLocked = 5;
}

message TradingEventsStreamResponseExternal {
  repeated PBTradingEvent tradingEvents = 1;
}

message PBTradingEvent {
  oneof msg {
    PBMarginCallEvent marginCallEvent = 1;
    PBStopOutEvent stopOutEvent = 2;
    PBTakeProfitEvent takeProfitEvent = 3;
    PBStopLossEvent stopLossEvent = 4;
    PBOrderActivationEvent orderActivationEvent = 5;
  }
  int64 login = 6;
  string group = 7;
}

message PBMarginCallEvent {
  int64 login = 1;
  double marginLevel = 2;
}

message PBStopOutEvent {
  int64 login = 1;
  string orderId = 2;
  string instrument = 3;
  double volume = 4;
}

message PBTakeProfitEvent {
  int64 login = 1;
  string orderId = 2;
  string instrument = 3;
  PBOrderSideExternal side = 4;
  double volume = 5;
  double netProfit = 7;
  string walletCurrency = 8;
}

message PBStopLossEvent {
  int64 login = 1;
  string orderId = 2;
  string instrument = 3;
  PBOrderSideExternal side = 4;
  double volume = 5;
  double netProfit = 7;
  string walletCurrency = 8;
}

message PBOrderActivationEvent {
  int64 login = 1;
  string orderId = 2;
  string instrument = 3;
  PBOrderSideExternal side = 4;
  double volume = 5;
  double activationPrice = 7;
}

message PBAccountInfoChangedRequestExternal {
  string systemUuid = 1;
  repeated string groups = 2;
  bool includeBlocked = 3;
  bool includeLocked = 4;
}

message PBAccountInfoChangedStreamResponseExternal {
  oneof msg {
    PBAccountInfoUpdated  userUpdated = 1;
    PBAccountInfoDeleted userDeleted = 2;
  }
}

message PBAccountInfoUpdated {
  int64 login = 1;
  string uuid = 2;
  string group = 3;
  int32 leverage = 4;
  bool isRetail = 5;
  bool isProView = 6;
  bool isBlocked = 7;
  bool isLocked = 8;
  string country = 9;
  string name = 10;
  string address = 11;
  string email = 12;
  string phone = 13;
  string province = 14;
  string zipCode = 15;
  string city = 16;
  string remarks = 17;
  string bankAccount = 18;
  string accountCurrency = 19;
  int32 accountCurrencyPrecision = 20;
  int64 registrationDate = 21;
}

message PBAccountInfoDeleted {
  int64 login = 1;
  string group = 2;
  AccountInfoDeletedReason reason = 3;

  enum AccountInfoDeletedReason {
    ACCOUNT_INFO_DELETED_REASON_UNKNOWN = 0;
    ACCOUNT_INFO_DELETED_REASON_ACCOUNT_DELETED = 1;
    ACCOUNT_INFO_DELETED_REASON_RIGHTS_REVOKED = 2;
    ACCOUNT_INFO_DELETED_REASON_ACCOUNT_INTERNAL_ERROR = 3;
  }
}

message PBSymbolChangedRequestExternal{
  string systemUuid = 1;

}

message PBSymbolChangedStreamResponseExternal{
  oneof msg {
    PBSymbolUpdated instrumentUpdated = 1;
    PBSymbolDeleted instrumentDeleted = 2;
  }
}

message PBSymbolUpdated{
  repeated TradeHoursDto tradingHours = 1;
  int32 volumePrecision = 2;
  string volumeMin = 3;
  string volumeStep = 4;
  double commission = 5;
  string sizeOfOnePoint = 6;
  string symbol = 7;
  string alias = 8;
  string baseCurrency = 9;
  string quoteCurrency = 10;
  int32 pricePrecision = 11;
  PBSymbolType type = 12;
  string swapBuy = 13;
  string swapSell = 14;
  string volumeMax = 15;
  string contractSize = 16;
  int64 multiplier = 17;
  int64 divider = 18;
  double leverage = 19;
  string description = 20;
  PBTerminationType terminationType = 21;
  string terminationDate = 22;
  repeated string tags = 23;
}

message PBSymbolDeleted{
  string symbol = 1;
}

message TradeHoursDto {
  int32 dayNumber = 1; // 0 - Sunday
  int32 openHours = 2;
  int32 openMinutes = 3;
  int32 openSeconds = 4;
  int32 closeHours = 5;
  int32 closeMinutes = 6;
  int32 closeSeconds = 7;
}

enum PBSymbolType {
  UNKNOWN_SYMBOL_TYPE = 0;
  FOREX = 1;
  CFD = 2;
  FOREXCFD = 3;
}

enum PBTerminationType {
  EXPIRATION = 0;
  ROLLOVER = 1;
}
```
:::
:::
:::
:::

::: {.th-content__section-right-column}
::: {.baseUrlView_container__6p3gC .th-baseurl-module}
::: {.baseUrlView_header__2Xs4S .th-baseurl-module__header}
Base URL

::: {.editor-show-delete-action-buttons}
:::
:::

::: {.baseUrlView_body__nlK5X .th-baseurl-module__body}
Sandbox

https://broker-api-demo.match-trader.com
:::
:::
:::

::: {.th-feedback}
::: {.th-feedback__header}
[Was this section helpful?]{.th-feedback__question}

Yes

::: {.feedback-divider}
:::

No

[[]{.cds--popover-content .sf-hidden}[]{.cds--popover-caret
.sf-hidden}]{#id-12 .cds--popover}
:::
:::
:::
:::

::: {#66b1e5a7998527f39cd64e40 .section .th-content_section-title .th-content_section-title-1}
Errors
======
:::

::: {.section .th-content_section-item data-href="errors"}
::: {.th-content__section}
::: {.th-content__section-left-column}
::: {theme="true"}
::: {.th-content__text}
We use standard HTTP response codes to state the success or failure of
API requests. It means that codes in the range

-   <div>

    2xx indicate success and usually have response bodies,

    </div>

-   <div>

    4xx indicate errors with detailed answers to why a particular
    request failed,

    </div>

-   
:::
:::
:::

::: {.th-content__section-right-column}
::: {.codes_wrapper__9IXNM .th-general-card contenteditable="false"}
::: {.codes_header__gpSXo .th-general-card__header}
#### Status Codes

::: {.editor-show-delete-action-buttons}
:::
:::

::: {.codes_error_types_listing__vI5E3 .th-general-card__body style="padding:0px 20px"}
::: {.codes_published_code_wrapper__TUFXe}
::: {.codes_published_top_part__gCfVN}
200

::: {.codes_published_title_right__8JnIX}
The request was performed successfully. You should receive a response
with a body. 200 response bodies are listed in each endpoint\'s response
section.
:::
:::
:::

::: {.codes_published_code_wrapper__TUFXe}
::: {.codes_published_top_part__gCfVN}
204

::: {.codes_published_title_right__8JnIX}
The request was performed successfully. You should receive a response
without a body.
:::
:::
:::

::: {.codes_published_code_wrapper__TUFXe}
::: {.codes_published_top_part__gCfVN}
400

::: {.codes_published_title_right__8JnIX}
Invalid request, usually due to missing some required parameter.
:::
:::
:::

::: {.codes_published_code_wrapper__TUFXe}
::: {.codes_published_top_part__gCfVN}
401

::: {.codes_published_title_right__8JnIX}
Authorization information is missing or invalid.
:::
:::
:::

::: {.codes_published_code_wrapper__TUFXe}
::: {.codes_published_top_part__gCfVN}
403

::: {.codes_published_title_right__8JnIX}
Your API token does not have permission to perform the requested
operation.
:::
:::
:::

::: {.codes_published_code_wrapper__TUFXe}
::: {.codes_published_top_part__gCfVN}
422

::: {.codes_published_title_right__8JnIX}
The server understands the content type of the request entity, and the
syntax is correct, but it was unable to process the request.
:::
:::
:::

::: {.codes_published_code_wrapper__TUFXe}
::: {.codes_published_top_part__gCfVN}
500

::: {.codes_published_title_right__8JnIX}
Something went wrong on our end. You can contact our Product Support to
check it.
:::
:::
:::
:::
:::
:::

::: {.th-feedback}
::: {.th-feedback__header}
[Was this section helpful?]{.th-feedback__question}

Yes

::: {.feedback-divider}
:::

No

[[]{.cds--popover-content .sf-hidden}[]{.cds--popover-caret
.sf-hidden}]{#id-16 .cds--popover}
:::
:::
:::

::: {.th-show-more-button}
Show more
:::
:::

::: {#66b1e5a7998527f39cd64756 .section .th-content_section-title .th-content_section-title-1}
Branches, Offers, Roles
=======================
:::

::: {.section .th-content_section-item data-href="branches-offers-roles"}
::: {.th-content__section-full}
::: {.th-content__text}
In our system, there is a distinction between multiple layers of account
attributes. In general, the account operates within the frames of a
given: Branch, Offer(s), and Role. Each section has an explanation of a
particular layer.

Via our API you cannot edit Branches, Offers, and Roles configuration
(it can be done only by using our CRM), but it\'s important to know them
since you will need them to efficiently manage accounts created in the
system. Tha
:::

::: {.th-feedback}
::: {.th-feedback__header}
[Was this section helpful?]{.th-feedback__question}

Yes

::: {.feedback-divider}
:::

No

[[]{.cds--popover-content .sf-hidden}[]{.cds--popover-caret
.sf-hidden}]{#id-20 .cds--popover}
:::
:::
:::

::: {.th-show-more-button}
Show more
:::
:::

::: {#66b1e5a7998527f39cd644a8 .section .th-content_section-title .th-content_section-title-1}
Accounts
========
:::

::: {.section .th-content_section-item data-href="accounts"}
::: {.th-content__section-full}
::: {.th-content__text}
In the Accounts section, you will find detailed information about user
accounts within the system. With this, you can access and update account
details and create new accounts. This section serves as a central hub
for overseeing and administering user accounts within the system. You
can also find a Trading Accounts sub-section within the section with all
relevant endpoints to manage Trading Accounts.

An Account represents the user, and a Trading Accou
:::

::: {.th-feedback}
::: {.th-feedback__header}
[Was this section helpful?]{.th-feedback__question}

Yes

::: {.feedback-divider}
:::

No

[[]{.cds--popover-content .sf-hidden}[]{.cds--popover-caret
.sf-hidden}]{#id-24 .cds--popover}
:::
:::
:::

::: {.th-show-more-button}
Show more
:::
:::

::: {#66b1e5a7998527f39cd64618 .section .th-content_section-title .th-content_section-title-1}
Payments
========
:::

::: {.section .th-content_section-item data-href="payments"}
::: {.th-content__section-full}
::: {.th-content__text}
In the Payments section you can find endpoints for:

-   ::: {style="position:relative"}
    Retrieving Payment Gateways list with details about each Payment
    Gateway,
    :::

-   ::: {style="position:relative"}
    Retrieving Deposits and Withdrawals,
    :::

-   ::: {style="position:relative"}
    Making Manual payments,
    :::

-   ::: {style="position:relative"}
    Credit in and out.
    :::
:::

::: {.th-feedback}
::: {.th-feedback__header}
[Was this section helpful?]{.th-feedback__question}

Yes

::: {.feedback-divider}
:::

No

[[]{.cds--popover-content .sf-hidden}[]{.cds--popover-caret
.sf-hidden}]{#id-28 .cds--popover}
:::
:::
:::
:::

::: {#66b1e5a7998527f39cd64c50 .section .th-content_section-title .th-content_section-title-2}
Get Payment Gateways
====================
:::

::: {.section .th-content_section-item data-href="payments/get-payment-gateways"}
::: {.th-content__section}
::: {.th-content__section-left-column}
::: {theme="true"}
::: {.th-content__text}
A Payment Gateway is a resource responsible for all payment operations
in the system. All Deposits and Withdrawals are processed within the
frame of a Payment Gateway and following the Payment Gateway
configuration.

::: {.callout-widget data-type="info" style="width:100%;min-width:100%"}
::: {.callout-icon}
:::

<div>

Payment Gateways must be assigned to a Branch to be visible to users.

</div>
:::

::: {.slate-div}
:::

::: {.slate-div}
:::
:::

::: {.public_method_items_wrapper__oH8Og .method-items-wrapper}
#### Header parameters {#header-parameters .public_method_items_title___U3pR .method-items-title}

::: {.public_method_items__kHbUx .method-items}
::: {.public_method_item__F8hQf .method-item}
::: {.public_method_item_label__i2z7z .method-item-label}
Authorization[string ]{.public_method_item_label_type__C3k5k
.method-item-label-type}
:::

::: {.method_item_additional_items}
:::
:::
:::
:::

::: {.public_method_items_wrapper__oH8Og .method-items-wrapper}
#### Query parametersExpand all {#query-parametersexpand-all .public_method_items_title___U3pR .method-items-title}

::: {.public_method_items__kHbUx .method-items}
::: {.public_method_item__F8hQf .method-item}
::: {.public_method_item_label__i2z7z .method-item-label}
::: {.public_method_item_arrow__cuv6s}
:::

sort[string ]{.public_method_item_label_type__C3k5k
.method-item-label-type}
:::

::: {.public_method_item_description__1dCkh .public_ellipsis__u39gU .method-item-description}
Specifies the order in which payment gateway records should be sorted.
The default sorting is by creation date in descending order
(\"created,desc\").
:::

::: {.method_item_additional_items}
::: {.method_item_additional_items_row}
::: {.method_item_additional_items_row_key .defaultValue attr="defaultValue"}
Default value
:::

::: {.method_item_additional_items_row_value}
created,desc
:::
:::
:::
:::

::: {.public_method_item__F8hQf .method-item}
::: {.public_method_item_label__i2z7z .method-item-label}
from[string (date-time)]{.public_method_item_label_type__C3k5k
.method-item-label-type}
:::

::: {.public_method_item_description__1dCkh .method-item-description}
Start date and time for filtering payment gateways based on their
creation or update time.
:::

::: {.method_item_additional_items}
:::
:::

::: {.public_method_item__F8hQf .method-item}
::: {.public_method_item_label__i2z7z .method-item-label}
to[string (date-time)]{.public_method_item_label_type__C3k5k
.method-item-label-type}
:::

::: {.public_method_item_description__1dCkh .method-item-description}
End date and time for the same filtering criteria.
:::

::: {.method_item_additional_items}
:::
:::
:::
:::

::: {.public_method_items_wrapper__oH8Og .method-items-wrapper}
### ResponsesExpand all {#responsesexpand-all .public_method_items_title___U3pR .method-items-title .response-heading}

::: {.public_response_box_item__Vlw8p .response-box-item}
::: {.200-status-code}
::: {.public_response_box_item_key__sLhgY .key-item}
200
:::

::: {.public_response_box_item_type__phKOc .response-type-string}
Object
:::
:::

::: {.response-desc-string}
OK
:::
:::

#### Response Attributes {#response-attributes .public_method_items_title_secondary__JaJQC .method-items-title-secondary}

::: {.public_method_items__kHbUx .method-items}
::: {.public_method_item__F8hQf .method-item}
::: {.public_method_item_label__i2z7z .method-item-label}
paymentGateways[array ]{.public_method_item_label_type__C3k5k
.method-item-label-type}
:::

::: {.public_method_item_description__1dCkh .method-item-description}
An array of payment gateway objects,
:::

::: {.method_item_additional_items}
:::

Details

Show child attributes
:::
:::
:::
:::
:::

::: {.th-content__section-right-column}
::: {.th-fullscreen-widget-wrapper}
::: {.requestView_request_card_widget__M_LSO .requestView_api_explorer_inactive___ME7Y}
::: {.requestView_header__SA75F .th-snippet-box__header}
::: {.requestView_complete_url__rYfv6}
GET

/v1/payment-gateways?sort=&from=&to=
:::

::: {.requestView_language_and_overflow__KsWC_ .language-and-overflow}
::: {.CustomSelect_custom_select__irkkH}
::: {.CustomSelect_custom_select_selected__hNWtJ .requestView_dropdown__qdK83 .request-widget-language-switcher-dropdown style="padding:8px 10px;min-width:50px;color:white"}
::: {.CustomSelect_custom_select_selected_value__WPvhT}
cURL
:::
:::
:::
:::
:::

::: {.SnippetBox_snippet_box__sH7NC}
::: {.SnippetBox_code_box_section__6lhvs .requestView_snippet_content___K1DP .snippet-right-card-body}
[`1curl --location 'https://broker-api-demo.match-trader.com/v1/payment-gateways' \ 2--header 'Authorization: Provide you token here' \`{style="white-space:pre;font-size:inherit;font-family:inherit;line-height:1.66667;padding:8px"}]{style="font-size:inherit;font-family:inherit;background:rgb(44,51,79);color:rgb(255,255,255);border-radius:3px;display:flex;line-height:1.42857;overflow-x:auto;white-space:pre"}
:::
:::
:::
:::

::: {.th-fullscreen-widget-wrapper}
::: {.responseView_tabs__DiIyV .th-response-tabs-module}
::: {.th-response-tabs__header}
#### Response
:::

::: {.th-response-tabs__body}
::: {.responseView_tab_list_with_overflow__gBG6j .th-response-tabs__body-list widget-is-full-screen-mode="false"}
::: {.cds--tabs}
::: {.cds--tab--list aria-label="List of responses" role="tablist"}
200
:::
:::
:::

::: {#ccs-72-tabpanel-0 .cds--tab-content .cds--tab-content--interactive aria-labelledby="ccs-72-tab-0" role="tabpanel" tabindex="-1"}
::: {.responseView_editor_wrapper__Qgwcb}
``` {.response-code-block}
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

::: {.responseView_show_more_less_editor_wrapper__ejNx_ .th-response-box__show_more_less-button}
Show more
:::
:::
:::
:::
:::
:::
:::

::: {.th-feedback}
::: {.th-feedback__header}
[Was this section helpful?]{.th-feedback__question}

Yes

::: {.feedback-divider}
:::

No

[[]{.cds--popover-content .sf-hidden}[]{.cds--popover-caret
.sf-hidden}]{#id-33 .cds--popover}
:::
:::
:::
:::

::: {#66b1e5a7998527f39cd64a7c .section .th-content_section-title .th-content_section-title-2}
Get Deposits
============
:::

::: {.section .th-content_section-item data-href="payments/get-deposits"}
::: {.th-content__section}
::: {.th-content__section-left-column}
::: {theme="true"}
::: {.th-content__text}
With this endpoint, you can retrieve information about deposits made in
the system. You can also request deposits for a specific account using
`query`{.slate-code}.
:::

::: {.public_method_items_wrapper__oH8Og .method-items-wrapper}
#### Header parameters {#header-parameters-1 .public_method_items_title___U3pR .method-items-title}

::: {.public_method_items__kHbUx .method-items}
::: {.public_method_item__F8hQf .method-item}
::: {.public_method_item_label__i2z7z .method-item-label}
Authorization[string ]{.public_method_item_label_type__C3k5k
.method-item-label-type}
:::

::: {.method_item_additional_items}
:::
:::
:::
:::

::: {.public_method_items_wrapper__oH8Og .method-items-wrapper}
#### Query parametersExpand all {#query-parametersexpand-all-1 .public_method_items_title___U3pR .method-items-title}

::: {.public_method_items__kHbUx .method-items}
::: {.public_method_item__F8hQf .method-item}
::: {.public_method_item_label__i2z7z .method-item-label}
query[string ]{.public_method_item_label_type__C3k5k
.method-item-label-type}
:::

::: {.public_method_item_description__1dCkh .method-item-description}
Search term to filter the deposits.
:::

::: {.method_item_additional_items}
:::
:::

::: {.public_method_item__F8hQf .method-item}
::: {.public_method_item_label__i2z7z .method-item-label}
page[integer (int32)]{.public_method_item_label_type__C3k5k
.method-item-label-type}
:::

::: {.public_method_item_description__1dCkh .method-item-description}
Specifies the page number of the deposit list.
:::

::: {.method_item_additional_items}
::: {.method_item_additional_items_row}
::: {.method_item_additional_items_row_key .defaultValue attr="defaultValue"}
Default value
:::

::: {.method_item_additional_items_row_value}
0
:::
:::
:::
:::

::: {.public_method_item__F8hQf .method-item}
::: {.public_method_item_label__i2z7z .method-item-label}
size[integer (int32)]{.public_method_item_label_type__C3k5k
.method-item-label-type}
:::

::: {.public_method_item_description__1dCkh .method-item-description}
Determines the number of deposit records per page.
:::

::: {.method_item_additional_items}
::: {.method_item_additional_items_row}
::: {.method_item_additional_items_row_key .defaultValue attr="defaultValue"}
Default value
:::

::: {.method_item_additional_items_row_value}
10
:::
:::
:::
:::

::: {.public_method_item__F8hQf .method-item}
::: {.public_method_item_label__i2z7z .method-item-label}
sort[string ]{.public_method_item_label_type__C3k5k
.method-item-label-type}
:::

::: {.public_method_item_description__1dCkh .method-item-description}
Specifies the sorting order of the results, such as by creation date in
descending order (created,desc).
:::

::: {.method_item_additional_items}
::: {.method_item_additional_items_row}
::: {.method_item_additional_items_row_key .defaultValue attr="defaultValue"}
Default value
:::

::: {.method_item_additional_items_row_value}
created,desc
:::
:::
:::
:::

::: {.public_method_item__F8hQf .method-item}
::: {.public_method_item_label__i2z7z .method-item-label}
from[string (date-time)]{.public_method_item_label_type__C3k5k
.method-item-label-type}
:::

::: {.public_method_item_description__1dCkh .method-item-description}
Filters the deposits starting from this date.
:::

::: {.method_item_additional_items}
:::
:::

::: {.public_method_item__F8hQf .method-item}
::: {.public_method_item_label__i2z7z .method-item-label}
to[string (date-time)]{.public_method_item_label_type__C3k5k
.method-item-label-type}
:::

::: {.public_method_item_description__1dCkh .method-item-description}
Filters the deposits up to this date.
:::

::: {.method_item_additional_items}
:::
:::
:::
:::

::: {.public_method_items_wrapper__oH8Og .method-items-wrapper}
### ResponsesExpand all {#responsesexpand-all-1 .public_method_items_title___U3pR .method-items-title .response-heading}

::: {.public_response_box_item__Vlw8p .response-box-item}
::: {.200-status-code}
::: {.public_response_box_item_key__sLhgY .key-item}
200
:::

::: {.public_response_box_item_type__phKOc .response-type-string}
Object
:::
:::

::: {.response-desc-string}
OK
:::
:::

#### Response Attributes {#response-attributes-1 .public_method_items_title_secondary__JaJQC .method-items-title-secondary}

::: {.public_method_items__kHbUx .method-items}
::: {.public_method_item__F8hQf .method-item}
::: {.public_method_item_label__i2z7z .method-item-label}
content[array ]{.public_method_item_label_type__C3k5k
.method-item-label-type}
:::

::: {.public_method_item_description__1dCkh .method-item-description}
Contains the deposit records.
:::

::: {.method_item_additional_items}
:::

Details

Show child attributes
:::

::: {.public_method_item__F8hQf .method-item}
::: {.public_method_item_label__i2z7z .method-item-label}
totalPages[integer (int32)]{.public_method_item_label_type__C3k5k
.method-item-label-type}
:::

::: {.public_method_item_description__1dCkh .method-item-description}
Total number of pages available.
:::

::: {.method_item_additional_items}
:::
:::

::: {.public_method_item__F8hQf .method-item}
::: {.public_method_item_label__i2z7z .method-item-label}
totalElements[integer (int64)]{.public_method_item_label_type__C3k5k
.method-item-label-type}
:::

::: {.public_method_item_description__1dCkh .method-item-description}
Total number of deposit entries available.
:::

::: {.method_item_additional_items}
:::
:::

::: {.public_method_item__F8hQf .method-item}
::: {.public_method_item_label__i2z7z .method-item-label}
number[integer (int32)]{.public_method_item_label_type__C3k5k
.method-item-label-type}
:::

::: {.public_method_item_description__1dCkh .method-item-description}
Current page number.
:::

::: {.method_item_additional_items}
:::
:::

::: {.public_method_item__F8hQf .method-item}
::: {.public_method_item_label__i2z7z .method-item-label}
size[integer (int32)]{.public_method_item_label_type__C3k5k
.method-item-label-type}
:::

::: {.public_method_item_description__1dCkh .method-item-description}
Number of records per page.
:::

::: {.method_item_additional_items}
:::
:::
:::
:::
:::
:::

::: {.th-content__section-right-column}
::: {.th-fullscreen-widget-wrapper}
::: {.requestView_request_card_widget__M_LSO .requestView_api_explorer_inactive___ME7Y}
::: {.requestView_header__SA75F .th-snippet-box__header}
::: {.requestView_complete_url__rYfv6}
GET

/v1/deposits?query=&page=&size=&sort=&from=&to=
:::

::: {.requestView_language_and_overflow__KsWC_ .language-and-overflow}
::: {.CustomSelect_custom_select__irkkH}
::: {.CustomSelect_custom_select_selected__hNWtJ .requestView_dropdown__qdK83 .request-widget-language-switcher-dropdown style="padding:8px 10px;min-width:50px;color:white"}
::: {.CustomSelect_custom_select_selected_value__WPvhT}
cURL
:::
:::
:::
:::
:::

::: {.SnippetBox_snippet_box__sH7NC}
::: {.SnippetBox_code_box_section__6lhvs .requestView_snippet_content___K1DP .snippet-right-card-body}
[`1curl --location 'https://broker-api-demo.match-trader.com/v1/deposits' \ 2--header 'Authorization: Provide you token here' \`{style="white-space:pre;font-size:inherit;font-family:inherit;line-height:1.66667;padding:8px"}]{style="font-size:inherit;font-family:inherit;background:rgb(44,51,79);color:rgb(255,255,255);border-radius:3px;display:flex;line-height:1.42857;overflow-x:auto;white-space:pre"}
:::
:::
:::
:::

::: {.th-fullscreen-widget-wrapper}
::: {.responseView_tabs__DiIyV .th-response-tabs-module}
::: {.th-response-tabs__header}
#### Response
:::

::: {.th-response-tabs__body}
::: {.responseView_tab_list_with_overflow__gBG6j .th-response-tabs__body-list widget-is-full-screen-mode="false"}
::: {.cds--tabs}
::: {.cds--tab--list aria-label="List of responses" role="tablist"}
200
:::
:::
:::

::: {#ccs-73-tabpanel-0 .cds--tab-content .cds--tab-content--interactive aria-labelledby="ccs-73-tab-0" role="tabpanel" tabindex="-1"}
::: {.responseView_editor_wrapper__Qgwcb}
``` {.response-code-block}
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

::: {.responseView_show_more_less_editor_wrapper__ejNx_ .th-response-box__show_more_less-button}
Show more
:::
:::
:::
:::
:::
:::
:::

::: {.th-feedback}
::: {.th-feedback__header}
[Was this section helpful?]{.th-feedback__question}

Yes

::: {.feedback-divider}
:::

No

[[]{.cds--popover-content .sf-hidden}[]{.cds--popover-caret
.sf-hidden}]{#id-38 .cds--popover}
:::
:::
:::
:::

::: {#66b1e5a7998527f39cd64a2f .section .th-content_section-title .th-content_section-title-2}
Get Withdrawals
===============
:::

::: {.section .th-content_section-item data-href="payments/get-withdrawals"}
::: {.th-content__section}
::: {.th-content__section-left-column}
::: {theme="true"}
::: {.th-content__text}
With this endpoint, you can retrieve information about withdrawals made
in the system. system. You can also request withdrawals for a specific
account using `query`{.slate-code}.

::: {.slate-div}
:::

::: {.slate-div}
:::
:::

::: {.public_method_items_wrapper__oH8Og .method-items-wrapper}
#### Header parameters {#header-parameters-2 .public_method_items_title___U3pR .method-items-title}

::: {.public_method_items__kHbUx .method-items}
::: {.public_method_item__F8hQf .method-item}
::: {.public_method_item_label__i2z7z .method-item-label}
Authorization[string ]{.public_method_item_label_type__C3k5k
.method-item-label-type}
:::

::: {.method_item_additional_items}
:::
:::
:::
:::

::: {.public_method_items_wrapper__oH8Og .method-items-wrapper}
#### Query parametersExpand all {#query-parametersexpand-all-2 .public_method_items_title___U3pR .method-items-title}

::: {.public_method_items__kHbUx .method-items}
::: {.public_method_item__F8hQf .method-item}
::: {.public_method_item_label__i2z7z .method-item-label}
query[string ]{.public_method_item_label_type__C3k5k
.method-item-label-type}
:::

::: {.public_method_item_description__1dCkh .method-item-description}
Search term to filter the withdrawal.
:::

::: {.method_item_additional_items}
:::
:::

::: {.public_method_item__F8hQf .method-item}
::: {.public_method_item_label__i2z7z .method-item-label}
page[integer (int32)]{.public_method_item_label_type__C3k5k
.method-item-label-type}
:::

::: {.public_method_item_description__1dCkh .method-item-description}
Specifies the page number of the withdrawallist.
:::

::: {.method_item_additional_items}
::: {.method_item_additional_items_row}
::: {.method_item_additional_items_row_key .defaultValue attr="defaultValue"}
Default value
:::

::: {.method_item_additional_items_row_value}
0
:::
:::
:::
:::

::: {.public_method_item__F8hQf .method-item}
::: {.public_method_item_label__i2z7z .method-item-label}
size[integer (int32)]{.public_method_item_label_type__C3k5k
.method-item-label-type}
:::

::: {.public_method_item_description__1dCkh .method-item-description}
Determines the number of withdrawalrecords per page.
:::

::: {.method_item_additional_items}
::: {.method_item_additional_items_row}
::: {.method_item_additional_items_row_key .defaultValue attr="defaultValue"}
Default value
:::

::: {.method_item_additional_items_row_value}
10
:::
:::
:::
:::

::: {.public_method_item__F8hQf .method-item}
::: {.public_method_item_label__i2z7z .method-item-label}
sort[string ]{.public_method_item_label_type__C3k5k
.method-item-label-type}
:::

::: {.public_method_item_description__1dCkh .method-item-description}
Specifies the sorting order of the results, such as by creation date in
descending order (created,desc).
:::

::: {.method_item_additional_items}
::: {.method_item_additional_items_row}
::: {.method_item_additional_items_row_key .defaultValue attr="defaultValue"}
Default value
:::

::: {.method_item_additional_items_row_value}
created,desc
:::
:::
:::
:::

::: {.public_method_item__F8hQf .method-item}
::: {.public_method_item_label__i2z7z .method-item-label}
from[string (date-time)]{.public_method_item_label_type__C3k5k
.method-item-label-type}
:::

::: {.public_method_item_description__1dCkh .method-item-description}
Filters the withdrawals starting from this date.
:::

::: {.method_item_additional_items}
:::
:::

::: {.public_method_item__F8hQf .method-item}
::: {.public_method_item_label__i2z7z .method-item-label}
to[string (date-time)]{.public_method_item_label_type__C3k5k
.method-item-label-type}
:::

::: {.public_method_item_description__1dCkh .method-item-description}
Filters the withdrawals up to this date.
:::

::: {.method_item_additional_items}
:::
:::
:::
:::

::: {.public_method_items_wrapper__oH8Og .method-items-wrapper}
### ResponsesExpand all {#responsesexpand-all-2 .public_method_items_title___U3pR .method-items-title .response-heading}

::: {.public_response_box_item__Vlw8p .response-box-item}
::: {.200-status-code}
::: {.public_response_box_item_key__sLhgY .key-item}
200
:::

::: {.public_response_box_item_type__phKOc .response-type-string}
Object
:::
:::

::: {.response-desc-string}
OK
:::
:::

#### Response Attributes {#response-attributes-2 .public_method_items_title_secondary__JaJQC .method-items-title-secondary}

::: {.public_method_items__kHbUx .method-items}
::: {.public_method_item__F8hQf .method-item}
::: {.public_method_item_label__i2z7z .method-item-label}
content[array ]{.public_method_item_label_type__C3k5k
.method-item-label-type}
:::

::: {.public_method_item_description__1dCkh .method-item-description}
Contains the withdrawal records.
:::

::: {.method_item_additional_items}
:::

Details

Show child attributes
:::

::: {.public_method_item__F8hQf .method-item}
::: {.public_method_item_label__i2z7z .method-item-label}
totalPages[integer (int32)]{.public_method_item_label_type__C3k5k
.method-item-label-type}
:::

::: {.public_method_item_description__1dCkh .method-item-description}
Total number of pages available.
:::

::: {.method_item_additional_items}
:::
:::

::: {.public_method_item__F8hQf .method-item}
::: {.public_method_item_label__i2z7z .method-item-label}
totalElements[integer (int64)]{.public_method_item_label_type__C3k5k
.method-item-label-type}
:::

::: {.public_method_item_description__1dCkh .method-item-description}
Total number of withdrawal entries available.
:::

::: {.method_item_additional_items}
:::
:::

::: {.public_method_item__F8hQf .method-item}
::: {.public_method_item_label__i2z7z .method-item-label}
number[integer (int32)]{.public_method_item_label_type__C3k5k
.method-item-label-type}
:::

::: {.public_method_item_description__1dCkh .method-item-description}
Current page number.
:::

::: {.method_item_additional_items}
:::
:::

::: {.public_method_item__F8hQf .method-item}
::: {.public_method_item_label__i2z7z .method-item-label}
size[integer (int32)]{.public_method_item_label_type__C3k5k
.method-item-label-type}
:::

::: {.public_method_item_description__1dCkh .method-item-description}
Number of records per page.
:::

::: {.method_item_additional_items}
:::
:::
:::
:::
:::
:::

::: {.th-content__section-right-column}
::: {.th-fullscreen-widget-wrapper}
::: {.requestView_request_card_widget__M_LSO .requestView_api_explorer_inactive___ME7Y}
::: {.requestView_header__SA75F .th-snippet-box__header}
::: {.requestView_complete_url__rYfv6}
GET

/v1/withdrawals?query=&page=&size=&sort=&from=&to=
:::

::: {.requestView_language_and_overflow__KsWC_ .language-and-overflow}
::: {.CustomSelect_custom_select__irkkH}
::: {.CustomSelect_custom_select_selected__hNWtJ .requestView_dropdown__qdK83 .request-widget-language-switcher-dropdown style="padding:8px 10px;min-width:50px;color:white"}
::: {.CustomSelect_custom_select_selected_value__WPvhT}
cURL
:::
:::
:::
:::
:::

::: {.SnippetBox_snippet_box__sH7NC}
::: {.SnippetBox_code_box_section__6lhvs .requestView_snippet_content___K1DP .snippet-right-card-body}
[`1curl --location 'https://broker-api-demo.match-trader.com/v1/withdrawals' \ 2--header 'Authorization: Provide you token here' \`{style="white-space:pre;font-size:inherit;font-family:inherit;line-height:1.66667;padding:8px"}]{style="font-size:inherit;font-family:inherit;background:rgb(44,51,79);color:rgb(255,255,255);border-radius:3px;display:flex;line-height:1.42857;overflow-x:auto;white-space:pre"}
:::
:::
:::
:::

::: {.th-fullscreen-widget-wrapper}
::: {.responseView_tabs__DiIyV .th-response-tabs-module}
::: {.th-response-tabs__header}
#### Response
:::

::: {.th-response-tabs__body}
::: {.responseView_tab_list_with_overflow__gBG6j .th-response-tabs__body-list widget-is-full-screen-mode="false"}
::: {.cds--tabs}
::: {.cds--tab--list aria-label="List of responses" role="tablist"}
200
:::
:::
:::

::: {#ccs-74-tabpanel-0 .cds--tab-content .cds--tab-content--interactive aria-labelledby="ccs-74-tab-0" role="tabpanel" tabindex="-1"}
::: {.responseView_editor_wrapper__Qgwcb}
``` {.response-code-block}
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

::: {.responseView_show_more_less_editor_wrapper__ejNx_ .th-response-box__show_more_less-button}
Show more
:::
:::
:::
:::
:::
:::
:::

::: {.th-feedback}
::: {.th-feedback__header}
[Was this section helpful?]{.th-feedback__question}

Yes

::: {.feedback-divider}
:::

No

[[]{.cds--popover-content .sf-hidden}[]{.cds--popover-caret
.sf-hidden}]{#id-43 .cds--popover}
:::
:::
:::
:::

::: {#66b1e5a7998527f39cd64b60 .section .th-content_section-title .th-content_section-title-2}
Manual Deposit
==============
:::

::: {.section .th-content_section-item data-href="payments/manual-deposit"}
::: {.th-content__section}
::: {.th-content__section-left-column}
::: {theme="true"}
::: {.th-content__text}
Using this endpoint, you can deposit funds to a Trading Account.

::: {.slate-div}
:::

::: {.slate-div}
:::
:::

::: {.public_method_items_wrapper__oH8Og .method-items-wrapper}
#### Header parameters {#header-parameters-3 .public_method_items_title___U3pR .method-items-title}

::: {.public_method_items__kHbUx .method-items}
::: {.public_method_item__F8hQf .method-item}
::: {.public_method_item_label__i2z7z .method-item-label}
Authorization[string ]{.public_method_item_label_type__C3k5k
.method-item-label-type}
:::

::: {.method_item_additional_items}
:::
:::
:::
:::

::: {.public_method_items_wrapper__oH8Og .method-items-wrapper}
#### Body parameters {#body-parameters .public_method_items_title___U3pR .method-items-title}

::: {.public_method_items__kHbUx .method-items}
::: {.public_method_item__F8hQf .method-item}
::: {.public_method_item_label__i2z7z .method-item-label}
systemUuid[string ]{.public_method_item_label_type__C3k5k
.method-item-label-type}[Required]{.public_method_item_label_required__P9Klf
.method-item-label-required}
:::

::: {.public_method_item_description__1dCkh .method-item-description}
he unique system identifier; this is required.
:::
:::

::: {.public_method_item__F8hQf .method-item}
::: {.public_method_item_label__i2z7z .method-item-label}
login[string ]{.public_method_item_label_type__C3k5k
.method-item-label-type}[Required]{.public_method_item_label_required__P9Klf
.method-item-label-required}
:::

::: {.public_method_item_description__1dCkh .method-item-description}
he login identifier for the Trading Account; this is required.
:::
:::

::: {.public_method_item__F8hQf .method-item}
::: {.public_method_item_label__i2z7z .method-item-label}
paymentGatewayUuid[string (uuid)]{.public_method_item_label_type__C3k5k
.method-item-label-type}[Required]{.public_method_item_label_required__P9Klf
.method-item-label-required}
:::

::: {.public_method_item_description__1dCkh .method-item-description}
Unique identifier for the payment gateway; required for processing the
deposit.
:::

::: {.method_item_additional_items}
:::
:::

::: {.public_method_item__F8hQf .method-item}
::: {.public_method_item_label__i2z7z .method-item-label}
amount[number ]{.public_method_item_label_type__C3k5k
.method-item-label-type}[Required]{.public_method_item_label_required__P9Klf
.method-item-label-required}
:::

::: {.public_method_item_description__1dCkh .method-item-description}
The amount of money to deposit; required.
:::

::: {.method_item_additional_items}
:::
:::

::: {.public_method_item__F8hQf .method-item}
::: {.public_method_item_label__i2z7z .method-item-label}
comment[string ]{.public_method_item_label_type__C3k5k
.method-item-label-type}
:::

::: {.public_method_item_description__1dCkh .method-item-description}
A comment or note accompanying the deposit.
:::

::: {.method_item_additional_items}
:::
:::
:::
:::

::: {.public_method_items_wrapper__oH8Og .method-items-wrapper}
### ResponsesExpand all {#responsesexpand-all-3 .public_method_items_title___U3pR .method-items-title .response-heading}

::: {.public_response_box_item__Vlw8p .response-box-item}
::: {.200-status-code}
::: {.public_response_box_item_key__sLhgY .key-item}
200
:::

::: {.public_response_box_item_type__phKOc .response-type-string}
Object
:::
:::

::: {.response-desc-string}
OK
:::
:::

#### Response Attributes {#response-attributes-3 .public_method_items_title_secondary__JaJQC .method-items-title-secondary}

::: {.public_method_items__kHbUx .method-items}
::: {.public_method_item__F8hQf .method-item}
::: {.public_method_item_label__i2z7z .method-item-label}
uuid[string (uuid)]{.public_method_item_label_type__C3k5k
.method-item-label-type}[Required]{.public_method_item_label_required__P9Klf
.method-item-label-required}
:::

::: {.public_method_item_description__1dCkh .method-item-description}
Unique identifier for the deposit transaction; required.
:::

::: {.method_item_additional_items}
:::
:::

::: {.public_method_item__F8hQf .method-item}
::: {.public_method_item_label__i2z7z .method-item-label}
created[string (date-time)]{.public_method_item_label_type__C3k5k
.method-item-label-type}[Required]{.public_method_item_label_required__P9Klf
.method-item-label-required}
:::

::: {.public_method_item_description__1dCkh .method-item-description}
Timestamp when the deposit was made; required.
:::

::: {.method_item_additional_items}
:::
:::

::: {.public_method_item__F8hQf .method-item}
::: {.public_method_item_label__i2z7z .method-item-label}
status[string ]{.public_method_item_label_type__C3k5k
.method-item-label-type}[Required]{.public_method_item_label_required__P9Klf
.method-item-label-required}
:::

::: {.public_method_item_description__1dCkh .method-item-description}
Current status of the deposit,.
:::

::: {.method_item_additional_items}
::: {.method_item_additional_items_row}
:::

::: {.method_item_additional_items_row_enums_container style="flex-direction:row"}
::: {.method_item_additional_items_row_key .enumValue attr="enumValue" style="align-self:center"}
Enum values:
:::

::: {.th_enums_without_description}
::: {.th_enums_value}
`NEW`{.th_enum_value_code}`AWAITING_CONFIRMATION`{.th_enum_value_code}`PROCESSING`{.th_enum_value_code}`BOOKED`{.th_enum_value_code}`PROCESSING_PAYMENT`{.th_enum_value_code}`DONE`{.th_enum_value_code}`REJECTED`{.th_enum_value_code}`FAILED`{.th_enum_value_code}`FAILED_PAYMENT`{.th_enum_value_code}`CANCELLED_BY_USER`{.th_enum_value_code}
:::
:::
:::
:::
:::

::: {.public_method_item__F8hQf .method-item}
::: {.public_method_item_label__i2z7z .method-item-label}
transactionInfo[object ]{.public_method_item_label_type__C3k5k
.method-item-label-type}
:::

::: {.method_item_additional_items}
:::

Details

Show child attributes
:::
:::
:::
:::
:::

::: {.th-content__section-right-column}
::: {.th-fullscreen-widget-wrapper}
::: {.requestView_request_card_widget__M_LSO .requestView_api_explorer_inactive___ME7Y}
::: {.requestView_header__SA75F .th-snippet-box__header}
::: {.requestView_complete_url__rYfv6}
POST

/v1/deposits/manual
:::

::: {.requestView_language_and_overflow__KsWC_ .language-and-overflow}
::: {.CustomSelect_custom_select__irkkH}
::: {.CustomSelect_custom_select_selected__hNWtJ .requestView_dropdown__qdK83 .request-widget-language-switcher-dropdown style="padding:8px 10px;min-width:50px;color:white"}
::: {.CustomSelect_custom_select_selected_value__WPvhT}
cURL
:::
:::
:::
:::
:::

::: {.SnippetBox_snippet_box__sH7NC}
::: {.SnippetBox_code_box_section__6lhvs .requestView_snippet_content___K1DP .snippet-right-card-body}
[`1curl --location 'https://broker-api-demo.match-trader.com/v1/deposits/manual' \ 2--header 'Authorization: Provide you token here' \ 3--data '{ 4  "systemUuid": "", 5  "login": "", 6  "paymentGatewayUuid": "", 7  "amount": null, 8  "comment": "" 9}'`{style="white-space:pre;font-size:inherit;font-family:inherit;line-height:1.66667;padding:8px"}]{style="font-size:inherit;font-family:inherit;background:rgb(44,51,79);color:rgb(255,255,255);border-radius:3px;display:flex;line-height:1.42857;overflow-x:auto;white-space:pre"}
:::
:::
:::
:::

::: {.th-fullscreen-widget-wrapper}
::: {.responseView_tabs__DiIyV .th-response-tabs-module}
::: {.th-response-tabs__header}
#### Response
:::

::: {.th-response-tabs__body}
::: {.responseView_tab_list_with_overflow__gBG6j .th-response-tabs__body-list widget-is-full-screen-mode="false"}
::: {.cds--tabs}
::: {.cds--tab--list aria-label="List of responses" role="tablist"}
200
:::
:::
:::

::: {#ccs-75-tabpanel-0 .cds--tab-content .cds--tab-content--interactive aria-labelledby="ccs-75-tab-0" role="tabpanel" tabindex="-1"}
::: {.responseView_editor_wrapper__Qgwcb}
``` {.response-code-block}
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
:::
:::
:::
:::
:::
:::

::: {.th-feedback}
::: {.th-feedback__header}
[Was this section helpful?]{.th-feedback__question}

Yes

::: {.feedback-divider}
:::

No

[[]{.cds--popover-content .sf-hidden}[]{.cds--popover-caret
.sf-hidden}]{#id-48 .cds--popover}
:::
:::
:::
:::

::: {#66b1e5a7998527f39cd64a62 .section .th-content_section-title .th-content_section-title-2}
Manual Withdrawal
=================
:::

::: {.section .th-content_section-item data-href="payments/manual-withdrawal"}
::: {.th-content__section}
::: {.th-content__section-left-column}
::: {theme="true"}
::: {.th-content__text}
Using this endpoint, you can withdraw funds from a Trading Account.

::: {.slate-div}
:::

::: {.slate-div}
:::
:::

::: {.public_method_items_wrapper__oH8Og .method-items-wrapper}
#### Header parameters {#header-parameters-4 .public_method_items_title___U3pR .method-items-title}

::: {.public_method_items__kHbUx .method-items}
::: {.public_method_item__F8hQf .method-item}
::: {.public_method_item_label__i2z7z .method-item-label}
Authorization[string ]{.public_method_item_label_type__C3k5k
.method-item-label-type}
:::

::: {.method_item_additional_items}
:::
:::
:::
:::

::: {.public_method_items_wrapper__oH8Og .method-items-wrapper}
#### Body parameters {#body-parameters-1 .public_method_items_title___U3pR .method-items-title}

::: {.public_method_items__kHbUx .method-items}
::: {.public_method_item__F8hQf .method-item}
::: {.public_method_item_label__i2z7z .method-item-label}
systemUuid[string (uuid)]{.public_method_item_label_type__C3k5k
.method-item-label-type}[Required]{.public_method_item_label_required__P9Klf
.method-item-label-required}
:::

::: {.public_method_item_description__1dCkh .method-item-description}
The unique identifier for the system from which funds are withdrawn;
required.
:::

::: {.method_item_additional_items}
:::
:::

::: {.public_method_item__F8hQf .method-item}
::: {.public_method_item_label__i2z7z .method-item-label}
login[string ]{.public_method_item_label_type__C3k5k
.method-item-label-type}[Required]{.public_method_item_label_required__P9Klf
.method-item-label-required}
:::

::: {.public_method_item_description__1dCkh .method-item-description}
Login details for the Trading Account from which the withdrawal is made;
required.
:::
:::

::: {.public_method_item__F8hQf .method-item}
::: {.public_method_item_label__i2z7z .method-item-label}
paymentGatewayUuid[string (uuid)]{.public_method_item_label_type__C3k5k
.method-item-label-type}[Required]{.public_method_item_label_required__P9Klf
.method-item-label-required}
:::

::: {.public_method_item_description__1dCkh .method-item-description}
Identifier for the payment gateway used for the transaction; required.
:::

::: {.method_item_additional_items}
:::
:::

::: {.public_method_item__F8hQf .method-item}
::: {.public_method_item_label__i2z7z .method-item-label}
amount[number ]{.public_method_item_label_type__C3k5k
.method-item-label-type}[Required]{.public_method_item_label_required__P9Klf
.method-item-label-required}
:::

::: {.public_method_item_description__1dCkh .method-item-description}
The amount of money to be withdrawn; required.
:::

::: {.method_item_additional_items}
:::
:::

::: {.public_method_item__F8hQf .method-item}
::: {.public_method_item_label__i2z7z .method-item-label}
comment[string ]{.public_method_item_label_type__C3k5k
.method-item-label-type}
:::

::: {.public_method_item_description__1dCkh .method-item-description}
A comment or note accompanying the withdrawal.
:::

::: {.method_item_additional_items}
:::
:::
:::
:::

::: {.public_method_items_wrapper__oH8Og .method-items-wrapper}
### ResponsesExpand all {#responsesexpand-all-4 .public_method_items_title___U3pR .method-items-title .response-heading}

::: {.public_response_box_item__Vlw8p .response-box-item}
::: {.200-status-code}
::: {.public_response_box_item_key__sLhgY .key-item}
200
:::

::: {.public_response_box_item_type__phKOc .response-type-string}
Object
:::
:::

::: {.response-desc-string}
OK
:::
:::

#### Response Attributes {#response-attributes-4 .public_method_items_title_secondary__JaJQC .method-items-title-secondary}

::: {.public_method_items__kHbUx .method-items}
::: {.public_method_item__F8hQf .method-item}
::: {.public_method_item_label__i2z7z .method-item-label}
uuid[string (uuid)]{.public_method_item_label_type__C3k5k
.method-item-label-type}[Required]{.public_method_item_label_required__P9Klf
.method-item-label-required}
:::

::: {.public_method_item_description__1dCkh .method-item-description}
Unique identifier for the withdrawal transaction; required.
:::

::: {.method_item_additional_items}
:::
:::

::: {.public_method_item__F8hQf .method-item}
::: {.public_method_item_label__i2z7z .method-item-label}
created[string (date-time)]{.public_method_item_label_type__C3k5k
.method-item-label-type}[Required]{.public_method_item_label_required__P9Klf
.method-item-label-required}
:::

::: {.public_method_item_description__1dCkh .method-item-description}
Timestamp of when the withdrawal was initiated; required.
:::

::: {.method_item_additional_items}
:::
:::

::: {.public_method_item__F8hQf .method-item}
::: {.public_method_item_label__i2z7z .method-item-label}
status[string ]{.public_method_item_label_type__C3k5k
.method-item-label-type}[Required]{.public_method_item_label_required__P9Klf
.method-item-label-required}
:::

::: {.public_method_item_description__1dCkh .method-item-description}
The current status of the withdrawal.
:::

::: {.method_item_additional_items}
::: {.method_item_additional_items_row}
:::

::: {.method_item_additional_items_row_enums_container style="flex-direction:row"}
::: {.method_item_additional_items_row_key .enumValue attr="enumValue" style="align-self:center"}
Enum values:
:::

::: {.th_enums_without_description}
::: {.th_enums_value}
`NEW`{.th_enum_value_code}`AWAITING_CONFIRMATION`{.th_enum_value_code}`PROCESSING`{.th_enum_value_code}`BOOKED`{.th_enum_value_code}`PROCESSING_PAYMENT`{.th_enum_value_code}`DONE`{.th_enum_value_code}`REJECTED`{.th_enum_value_code}`FAILED`{.th_enum_value_code}`FAILED_PAYMENT`{.th_enum_value_code}`CANCELLED_BY_USER`{.th_enum_value_code}
:::
:::
:::
:::
:::

::: {.public_method_item__F8hQf .method-item}
::: {.public_method_item_label__i2z7z .method-item-label}
transactionInfo[object ]{.public_method_item_label_type__C3k5k
.method-item-label-type}
:::

::: {.method_item_additional_items}
:::

Details

Show child attributes
:::
:::
:::
:::
:::

::: {.th-content__section-right-column}
::: {.th-fullscreen-widget-wrapper}
::: {.requestView_request_card_widget__M_LSO .requestView_api_explorer_inactive___ME7Y}
::: {.requestView_header__SA75F .th-snippet-box__header}
::: {.requestView_complete_url__rYfv6}
POST

/v1/withdrawals/manual
:::

::: {.requestView_language_and_overflow__KsWC_ .language-and-overflow}
::: {.CustomSelect_custom_select__irkkH}
::: {.CustomSelect_custom_select_selected__hNWtJ .requestView_dropdown__qdK83 .request-widget-language-switcher-dropdown style="padding:8px 10px;min-width:50px;color:white"}
::: {.CustomSelect_custom_select_selected_value__WPvhT}
cURL
:::
:::
:::
:::
:::

::: {.SnippetBox_snippet_box__sH7NC}
::: {.SnippetBox_code_box_section__6lhvs .requestView_snippet_content___K1DP .snippet-right-card-body}
[`1curl --location 'https://broker-api-demo.match-trader.com/v1/withdrawals/manual' \ 2--header 'Authorization: Provide you token here' \ 3--data '{ 4  "systemUuid": "", 5  "login": "", 6  "paymentGatewayUuid": "", 7  "amount": null, 8  "comment": "" 9}'`{style="white-space:pre;font-size:inherit;font-family:inherit;line-height:1.66667;padding:8px"}]{style="font-size:inherit;font-family:inherit;background:rgb(44,51,79);color:rgb(255,255,255);border-radius:3px;display:flex;line-height:1.42857;overflow-x:auto;white-space:pre"}
:::
:::
:::
:::

::: {.th-fullscreen-widget-wrapper}
::: {.responseView_tabs__DiIyV .th-response-tabs-module}
::: {.th-response-tabs__header}
#### Response
:::

::: {.th-response-tabs__body}
::: {.responseView_tab_list_with_overflow__gBG6j .th-response-tabs__body-list widget-is-full-screen-mode="false"}
::: {.cds--tabs}
::: {.cds--tab--list aria-label="List of responses" role="tablist"}
200
:::
:::
:::

::: {#ccs-76-tabpanel-0 .cds--tab-content .cds--tab-content--interactive aria-labelledby="ccs-76-tab-0" role="tabpanel" tabindex="-1"}
::: {.responseView_editor_wrapper__Qgwcb}
``` {.response-code-block}
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
:::
:::
:::
:::
:::
:::

::: {.th-feedback}
::: {.th-feedback__header}
[Was this section helpful?]{.th-feedback__question}

Yes

::: {.feedback-divider}
:::

No

[[]{.cds--popover-content .sf-hidden}[]{.cds--popover-caret
.sf-hidden}]{#id-53 .cds--popover}
:::
:::
:::
:::

::: {#66b1e5a7998527f39cd64a74 .section .th-content_section-title .th-content_section-title-2}
Credit In
=========
:::

::: {.section .th-content_section-item data-href="payments/credit-in"}
::: {.th-content__section}
::: {.th-content__section-left-column}
::: {theme="true"}
::: {.th-content__text}
Using this endpoint, you can add a credit to a Trading Account.

::: {.slate-div}
:::

::: {.slate-div}
:::
:::

::: {.public_method_items_wrapper__oH8Og .method-items-wrapper}
#### Header parameters {#header-parameters-5 .public_method_items_title___U3pR .method-items-title}

::: {.public_method_items__kHbUx .method-items}
::: {.public_method_item__F8hQf .method-item}
::: {.public_method_item_label__i2z7z .method-item-label}
Authorization[string ]{.public_method_item_label_type__C3k5k
.method-item-label-type}
:::

::: {.method_item_additional_items}
:::
:::
:::
:::

::: {.public_method_items_wrapper__oH8Og .method-items-wrapper}
#### Body parameters {#body-parameters-2 .public_method_items_title___U3pR .method-items-title}

::: {.public_method_items__kHbUx .method-items}
::: {.public_method_item__F8hQf .method-item}
::: {.public_method_item_label__i2z7z .method-item-label}
systemUuid[string ]{.public_method_item_label_type__C3k5k
.method-item-label-type}[Required]{.public_method_item_label_required__P9Klf
.method-item-label-required}
:::

::: {.public_method_item_description__1dCkh .method-item-description}
The unique system identifier for the account; required.
:::
:::

::: {.public_method_item__F8hQf .method-item}
::: {.public_method_item_label__i2z7z .method-item-label}
login[string (uuid)]{.public_method_item_label_type__C3k5k
.method-item-label-type}[Required]{.public_method_item_label_required__P9Klf
.method-item-label-required}
:::

::: {.public_method_item_description__1dCkh .method-item-description}
The unique login identifier for the Trading Account to which credit is
added; required.
:::

::: {.method_item_additional_items}
:::
:::

::: {.public_method_item__F8hQf .method-item}
::: {.public_method_item_label__i2z7z .method-item-label}
amount[number ]{.public_method_item_label_type__C3k5k
.method-item-label-type}[Required]{.public_method_item_label_required__P9Klf
.method-item-label-required}
:::

::: {.public_method_item_description__1dCkh .method-item-description}
The amount of credit to be added; required.
:::

::: {.method_item_additional_items}
:::
:::

::: {.public_method_item__F8hQf .method-item}
::: {.public_method_item_label__i2z7z .method-item-label}
comment[string ]{.public_method_item_label_type__C3k5k
.method-item-label-type}
:::

::: {.public_method_item_description__1dCkh .method-item-description}
An optional comment describing the reason for the credit.
:::

::: {.method_item_additional_items}
:::
:::
:::
:::

::: {.public_method_items_wrapper__oH8Og .method-items-wrapper}
### Responses {#responses .public_method_items_title___U3pR .method-items-title .response-heading}

::: {.public_response_box_item__Vlw8p .response-box-item}
::: {.204-status-code}
::: {.public_response_box_item_key__sLhgY .key-item}
204
:::

::: {.public_response_box_item_type__phKOc .response-type-string}
Object
:::
:::

::: {.response-desc-string}
No Content
:::
:::
:::
:::
:::

::: {.th-content__section-right-column}
::: {.th-fullscreen-widget-wrapper}
::: {.requestView_request_card_widget__M_LSO .requestView_api_explorer_inactive___ME7Y}
::: {.requestView_header__SA75F .th-snippet-box__header}
::: {.requestView_complete_url__rYfv6}
POST

/v1/credit/in
:::

::: {.requestView_language_and_overflow__KsWC_ .language-and-overflow}
::: {.CustomSelect_custom_select__irkkH}
::: {.CustomSelect_custom_select_selected__hNWtJ .requestView_dropdown__qdK83 .request-widget-language-switcher-dropdown style="padding:8px 10px;min-width:50px;color:white"}
::: {.CustomSelect_custom_select_selected_value__WPvhT}
cURL
:::
:::
:::
:::
:::

::: {.SnippetBox_snippet_box__sH7NC}
::: {.SnippetBox_code_box_section__6lhvs .requestView_snippet_content___K1DP .snippet-right-card-body}
[`1curl --location 'https://broker-api-demo.match-trader.com/v1/credit/in' \ 2--header 'Authorization: Provide you token here' \ 3--data '{ 4  "systemUuid": "", 5  "login": "", 6  "amount": null, 7  "comment": "" 8}'`{style="white-space:pre;font-size:inherit;font-family:inherit;line-height:1.66667;padding:8px"}]{style="font-size:inherit;font-family:inherit;background:rgb(44,51,79);color:rgb(255,255,255);border-radius:3px;display:flex;line-height:1.42857;overflow-x:auto;white-space:pre"}
:::
:::
:::
:::

::: {.th-fullscreen-widget-wrapper}
::: {.responseView_tabs__DiIyV .th-response-tabs-module}
::: {.th-response-tabs__header}
#### Response
:::

::: {.th-response-tabs__body}
::: {.responseView_tab_list_with_overflow__gBG6j .th-response-tabs__body-list widget-is-full-screen-mode="false"}
::: {.cds--tabs}
::: {.cds--tab--list aria-label="List of responses" role="tablist"}
204
:::
:::
:::

::: {#ccs-77-tabpanel-0 .cds--tab-content .cds--tab-content--interactive aria-labelledby="ccs-77-tab-0" role="tabpanel" tabindex="-1"}
::: {.responseView_editor_wrapper__Qgwcb}
``` {.response-code-block}
No Content
```
:::
:::
:::
:::
:::
:::

::: {.th-feedback}
::: {.th-feedback__header}
[Was this section helpful?]{.th-feedback__question}

Yes

::: {.feedback-divider}
:::

No

[[]{.cds--popover-content .sf-hidden}[]{.cds--popover-caret
.sf-hidden}]{#id-58 .cds--popover}
:::
:::
:::
:::

::: {#66b1e5a7998527f39cd64cb6 .section .th-content_section-title .th-content_section-title-2}
Credit Out
==========
:::

::: {.section .th-content_section-item data-href="payments/credit-out"}
::: {.th-content__section}
::: {.th-content__section-left-column}
::: {theme="true"}
::: {.th-content__text}
Using this endpoint, you can remove a credit from a Trading Account.
:::

::: {.public_method_items_wrapper__oH8Og .method-items-wrapper}
#### Header parameters {#header-parameters-6 .public_method_items_title___U3pR .method-items-title}

::: {.public_method_items__kHbUx .method-items}
::: {.public_method_item__F8hQf .method-item}
::: {.public_method_item_label__i2z7z .method-item-label}
Authorization[string ]{.public_method_item_label_type__C3k5k
.method-item-label-type}
:::

::: {.method_item_additional_items}
:::
:::
:::
:::

::: {.public_method_items_wrapper__oH8Og .method-items-wrapper}
#### Body parameters {#body-parameters-3 .public_method_items_title___U3pR .method-items-title}

::: {.public_method_items__kHbUx .method-items}
::: {.public_method_item__F8hQf .method-item}
::: {.public_method_item_label__i2z7z .method-item-label}
systemUuid[string ]{.public_method_item_label_type__C3k5k
.method-item-label-type}[Required]{.public_method_item_label_required__P9Klf
.method-item-label-required}
:::

::: {.public_method_item_description__1dCkh .method-item-description}
The unique system identifier for the account; required.
:::
:::

::: {.public_method_item__F8hQf .method-item}
::: {.public_method_item_label__i2z7z .method-item-label}
login[string (uuid)]{.public_method_item_label_type__C3k5k
.method-item-label-type}[Required]{.public_method_item_label_required__P9Klf
.method-item-label-required}
:::

::: {.public_method_item_description__1dCkh .method-item-description}
he unique login identifier for the Trading Account from which credit is
removed; required.
:::

::: {.method_item_additional_items}
:::
:::

::: {.public_method_item__F8hQf .method-item}
::: {.public_method_item_label__i2z7z .method-item-label}
amount[number ]{.public_method_item_label_type__C3k5k
.method-item-label-type}[Required]{.public_method_item_label_required__P9Klf
.method-item-label-required}
:::

::: {.public_method_item_description__1dCkh .method-item-description}
The amount of credit to be removed; required.
:::

::: {.method_item_additional_items}
:::
:::

::: {.public_method_item__F8hQf .method-item}
::: {.public_method_item_label__i2z7z .method-item-label}
comment[string ]{.public_method_item_label_type__C3k5k
.method-item-label-type}
:::

::: {.public_method_item_description__1dCkh .method-item-description}
An optional comment describing the reason for the debit.
:::

::: {.method_item_additional_items}
:::
:::
:::
:::

::: {.public_method_items_wrapper__oH8Og .method-items-wrapper}
### Responses {#responses-1 .public_method_items_title___U3pR .method-items-title .response-heading}

::: {.public_response_box_item__Vlw8p .response-box-item}
::: {.204-status-code}
::: {.public_response_box_item_key__sLhgY .key-item}
204
:::

::: {.public_response_box_item_type__phKOc .response-type-string}
Object
:::
:::

::: {.response-desc-string}
No Content
:::
:::
:::
:::
:::

::: {.th-content__section-right-column}
::: {.th-fullscreen-widget-wrapper}
::: {.requestView_request_card_widget__M_LSO .requestView_api_explorer_inactive___ME7Y}
::: {.requestView_header__SA75F .th-snippet-box__header}
::: {.requestView_complete_url__rYfv6}
POST

/v1/credit/out
:::

::: {.requestView_language_and_overflow__KsWC_ .language-and-overflow}
::: {.CustomSelect_custom_select__irkkH}
::: {.CustomSelect_custom_select_selected__hNWtJ .requestView_dropdown__qdK83 .request-widget-language-switcher-dropdown style="padding:8px 10px;min-width:50px;color:white"}
::: {.CustomSelect_custom_select_selected_value__WPvhT}
cURL
:::
:::
:::
:::
:::

::: {.SnippetBox_snippet_box__sH7NC}
::: {.SnippetBox_code_box_section__6lhvs .requestView_snippet_content___K1DP .snippet-right-card-body}
[`1curl --location 'https://broker-api-demo.match-trader.com/v1/credit/out' \ 2--header 'Authorization: Provide you token here' \ 3--data '{ 4  "systemUuid": "", 5  "login": "", 6  "amount": null, 7  "comment": "" 8}'`{style="white-space:pre;font-size:inherit;font-family:inherit;line-height:1.66667;padding:8px"}]{style="font-size:inherit;font-family:inherit;background:rgb(44,51,79);color:rgb(255,255,255);border-radius:3px;display:flex;line-height:1.42857;overflow-x:auto;white-space:pre"}
:::
:::
:::
:::

::: {.th-fullscreen-widget-wrapper}
::: {.responseView_tabs__DiIyV .th-response-tabs-module}
::: {.th-response-tabs__header}
#### Response
:::

::: {.th-response-tabs__body}
::: {.responseView_tab_list_with_overflow__gBG6j .th-response-tabs__body-list widget-is-full-screen-mode="false"}
::: {.cds--tabs}
::: {.cds--tab--list aria-label="List of responses" role="tablist"}
204
:::
:::
:::

::: {#ccs-78-tabpanel-0 .cds--tab-content .cds--tab-content--interactive aria-labelledby="ccs-78-tab-0" role="tabpanel" tabindex="-1"}
::: {.responseView_editor_wrapper__Qgwcb}
``` {.response-code-block}
No Content
```
:::
:::
:::
:::
:::
:::

::: {.th-feedback}
::: {.th-feedback__header}
[Was this section helpful?]{.th-feedback__question}

Yes

::: {.feedback-divider}
:::

No

[[]{.cds--popover-content .sf-hidden}[]{.cds--popover-caret
.sf-hidden}]{#id-63 .cds--popover}
:::
:::
:::
:::

::: {#66b1e5a7998527f39cd64354 .section .th-content_section-title .th-content_section-title-1}
Trading
=======
:::

::: {.section .th-content_section-item data-href="trading"}
::: {.th-content__section-full}
::: {.th-content__text}
The Trading section allows accessing and managing various trading
functionalities within the system. With this section, you can place
orders and monitor trading activity seamlessly. This section provides
the necessary tools for users to efficiently execute trades and stay
informed about market conditions.
:::

::: {.th-feedback}
::: {.th-feedback__header}
[Was this section helpful?]{.th-feedback__question}

Yes

::: {.feedback-divider}
:::

No

[[]{.cds--popover-content .sf-hidden}[]{.cds--popover-caret
.sf-hidden}]{#id-67 .cds--popover}
:::
:::
:::

::: {.th-show-more-button}
Show more
:::
:::

::: {#66b20919998527f39ce6aadf .section .th-content_section-title .th-content_section-title-1}
Prop Trading {#prop-trading .highlighted-section-published}
============
:::

::: {.section .th-content_section-item data-href="prop-trading"}
::: {.th-content__section-full}
::: {.th-content__text}
:::

::: {.th-feedback}
::: {.th-feedback__header}
[Was this section helpful?]{.th-feedback__question}

Yes

::: {.feedback-divider}
:::

No

[[]{.cds--popover-content .sf-hidden}[]{.cds--popover-caret
.sf-hidden}]{#id-71 .cds--popover}
:::
:::
:::

::: {.th-show-more-button}
Show more
:::
:::
:::
:::
:::
:::
:::

::: {.Toastify}
:::
:::

::: {#quickStartModalPortal}
:::

::: {#PaymentModalPortal}
:::

::: {#seoImageModal}
:::

::: {#deleteModal}
:::

::: {#runnerDescriptionPopup}
:::

::: {#versioningPopup}
:::

::: {#customModalPortalId}
:::

::: {#additionalPropertiesPortalId}
:::

::: {#confirmationPortalId}
:::
