# Table of contents

- [Crypto Deposit Flow](#crypto-deposit-flow)
- [Deposit Request](#deposit-request)
- [Multiple Use of Wallet Address](#multiple-use-of-wallet-address)
- [Callbacks](#callbacks)
- [Withdrawal Request](#withdrawal-request)
- [Cryptocurrency Details](#cryptocurrency-details)
- [Signature](#signature)
- [Callback Signature Generation](#callback-signature-generation)

# Integrated systems

## List of CRMs already integrated with our Crypto Gateway

- Utip
- FX Back Office
- Skale
- Trade Smarter
- Kenmore design
- TradeSoft
- Nullpoint
- Plugit
- Axis (Broctagon)
- Reltrix
- Datalyst
- FTT (Fair Trading Technology)
- Leverate
- Pheasanatech

## List of payment cashiers already integrated with our Crypto Gateway

- Paytiko
- Praxis
- BridgerPay
- Akurateco
- Corefy


# Crypto Deposit Flow

1. The Client requests a deposit in the broker's Client's Office.
   - **Client**: The Client initiates the deposit request.
   - **Broker CRM**: The request is handled by the broker's CRM.

2. Broker sends the request to M2P.
   - **Broker**: The broker processes the request and sends it to the M2P system via API.

3. Processing request, generating individual crypto deposit address.
   - **match2pay**: The M2P system processes the request and generates a unique crypto deposit address.

4. Showing crypto deposit address using the payment page.
   - **Broker**: The broker shows the generated crypto deposit address to the client.

5. Decision Point: Does the client have cryptocurrency?
   - **Client**: The client decides whether they have cryptocurrency to deposit.
   - If **Yes**:
     - a) Sending cryptocurrency to the individual deposit address.
       - **Client**: The client sends cryptocurrency to the generated deposit address.
       - **M2P crypto wallet**: The crypto is received in the M2P wallet.
     - b) Crypto received on the wallet.
       - **M2P**: The crypto is confirmed received in the wallet.
     - c) Converting crypto to FIAT.
       - **match2pay**: The crypto is converted to fiat currency.
     - d) The amount is booked on the broker's M2P wallet.
       - **API**: The amount is booked via the API.
   - If **No**:
     - a) Buying crypto via exchanger (e.g., Moonpay) using Credit Card or Wire Transfer.
       - **Client**: The client buys cryptocurrency using an exchanger.
       - **M2P crypto wallet**: The crypto is received in the M2P wallet.
     - b) The amount is booked on the broker's M2P wallet.
       - **API**: The amount is booked via the API.

6. Broker is booking the amount from a callback on the client's trading account.
   - **Broker**: The broker receives a callback and books the amount to the client's trading account.


# Deposit Request

This request is responsible for generating a new deposit wallet address for cryptocurrency transactions and providing a link to the payment page. It is a valuable tool for users who wish to make a deposit or payment using cryptocurrencies.

### Important Information:
- On live environment, there is a need to provide IP addresses to whitelist to be able to send requests.
- Under one paymentID there can be more than one transaction.

### BASE URL (Staging)
`https://pp-staging.fx-edge.com`
### POST /api/v2/deposit/crypto_agent
```sh
curl --location 'https://pp-staging.fx-edge.com/api/v2/deposit/crypto_agent' \
--header 'Content-Type: application/json' \
--data '{
  "amount": 10,
  "currency": "USD",
  "paymentGatewayName": "USDT TRC20",
  "paymentCurrency": "USX",
  "callbackUrl": "https://pp-staging.fx-edge.com/api/v2/callback",
  "apiToken": "70XzjXtypiJ8tkhI2Hg",
  "timestamp": 1694944511,
  "signature": "3bf00f8c39bdc0f2c3654e391c937cb0dba612490c0fbdb3df1fd8e3c7804bba8289b42c0e87474335d775251188a8ec",
  "tradingAccountLogin": "1234"
}'
```
### Response
`200`
```json
{
  "paymentId": "4b23d9c7-b896-489d-85ac-4db096951bde",
  "status": "NEW",
  "transactionAmount": null,
  "netAmount": null,
  "transactionCurrency": "USX",
  "finalCurrency": "USD",
  "processingFee": null,
  "address": "TFEMy6Tgnb3V1DTe9HNV4dvwme7zwmjgQW",
  "checkoutUrl": "https://pp-staging.fx-edge.com/crypto-payment/4b23d9c7-b896-489d-85ac-4db096951bde/paywall"
}
```

### Statuses of Deposit Requests:
- **NEW**: The transaction has been initiated (for example, the user displayed the wallet address to deposit).
- **PENDING**: The transaction is processing. This status is temporary and should change to DONE after receiving confirmation/response from the blockchain.
- **DECLINED**: The transaction has failed in the blockchain.
- **DONE**: The transaction is done. The funds are booked.

### Header Parameters:
- **Content-Type**: `string`

### Body Parameters:
- **amount**: `integer`
- **currency**: `string`
  - Symbol of currency to which paymentCurrency should be converted (not full name), e.g. USD.
- **paymentGatewayName**: `string`
  - Name of payment gateway that will be used for deposit.
- **paymentCurrency**: `string`
  - Symbol of currency that will be deposited.
- **callbackUrl**: `string`
  - Notification about changes in payment status will be sent to this URL.
- **apiToken**: `string`
  - API Token provided by the Support team.
- **timestamp**: `integer`
  - Current time in seconds.
- **signature**: `string`
  - The signature generated from the request body and API secret.
- **tradingAccountLogin**: `string`
  - Optional - Trading account ID of a user requesting a withdrawal or any other information that will help you identify the transaction.

### Responses:
#### 200 OK
**Response Attributes:**
- **paymentId**: `string`
  - Unique ID of the payment transaction in Match2Pay.
- **status**: `string`
  - Status of the transaction.
- **transactionAmount**: `integer`
- **netAmount**: `integer`
- **transactionCurrency**: `string`
  - Symbol of the deposited currency.
- **finalCurrency**: `string`
  - Symbol of the currency to which the deposited currency was converted.
- **processingFee**: `integer`
- **address**: `string`
  - Cryptocurrency wallet address created for this deposit.
- **checkoutUrl**: `string`
  - Match2Pay page URL. The client should be redirected to this URL.


# Multiple Use of Wallet Address

In order to be able to accept multiple deposits to the same wallet address, the integration should be changed so that the `payment_id` of the generated request is assigned to a particular account on your end. The same `payment_id` should always be used/displayed for a specific account within the same crypto gateways.

Within one `payment_id` there may be other deposits, which will have different hashes from the others but will still belong to the same `payment_id`.

### Summary:
- The integration on your side should assume that the `payment_id` of the request may be repeated for deposits.
- The transaction hash will be different for each deposit.
- If you receive a callback with a new transaction hash, it should be booked even if the `payment_id` is the same.
- This integration will have to be changed for each crypto payment gateway you use.

### Optional:
- You can add the ability to unlink the wallet address (`payment_id`) from the selected account so that it will be possible to generate a new wallet address in exceptional situations.


# Callbacks

### First Callback (after the client makes a deposit)
```json
{
  "depositAddress": "C9wic7ex7etARjPGQPKBHGLr2cRcCD17aZ",
  "cryptoTransactionInfo": [
    {
      "txid": "b20feab400c3cd61a9d0daec8526d739a2335fe1900415f24835001e58a837a7",
      "confirmations": 0,
      "amount": 0.10000000,
      "status": "PENDING",
      "processingFee": 0.00500000,
      "conversionRate": 0
    }
  ],
  "paymentId": "99ca8c34-5191-41d9-a1a2-666b9badf1ce",
  "status": "PENDING",
  "transactionAmount": 0.10000000,
  "netAmount": 0.09500000,
  "transactionCurrency": "BTC",
  "processingFee": 0.00500000,
  "finalAmount": 0,
  "finalCurrency": "USD",
  "conversionRate": 0
}
```

### Second Callback (after the funds are correctly booked)
```json
{
  "depositAddress": "C9wic7ex7etARjPGQPKBHGLr2cRcCD17aZ",
  "cryptoTransactionInfo": [
    {
      "txid": "b20feab400c3cd61a9d0daec8526d739a2335fe1900415f24835001e58a837a7",
      "confirmations": 2,
      "amount": 0.10000000,
      "confirmedTime": "Mar 20, 2019 7:06:38PM",
      "status": "DONE",
      "processingFee": 0.00500000,
      "conversionRate": 3198.64800
    }
  ],
  "paymentId": "99ca8c34-5191-41d9-a1a2-666b9badf1ce",
  "status": "DONE",
  "transactionAmount": 0.10000000,
  "netAmount": 0.09500000,
  "transactionCurrency": "BTC",
  "processingFee": 0.00500000,
  "finalAmount": 303.87,
  "finalCurrency": "USD",
  "conversionRate": 3198.65
}
```

### Fields
- **depositAddress**: Cryptocurrency wallet address.
- **txid**: Blockchain id of the transaction (can be used to check the transaction status in the blockchain).
- **confirmations**: Number of transaction confirmations.
- **amount**: Deposited amount.
- **status**: Transaction status.
- **processingFee**: Charged fee amount.
- **conversionRate**: Conversion rate from `transactionCurrency` to `finalCurrency`.
- **paymentId**: Unique id of the payment transaction in the Match2Pay.
- **transactionAmount**: The amount deposited by the client.
- **netAmount**: Amount after fee charge.
- **transactionCurrency**: Symbol of deposited currency (list of cryptocurrency symbols).
- **finalAmount** (optional): Amount after conversion of the net amount to the desired currency.
- **finalCurrency** (optional): Symbol of currency to which deposited currency was converted.
- **conversionRate** (optional): Conversion rate from `transactionCurrency` to `finalCurrency`.


# Withdrawal Request

A withdrawal request is a fundamental process that facilitates the transfer of cryptocurrency funds from one's wallet to an external recipient's address.

#### BASE URL (Staging)
`https://pp-staging.fx-edge.com`
### POST /api/v2/withdraw/crypto_agent
```sh
curl --location 'https://pp-staging.fx-edge.com/api/v2/withdraw/crypto_agent' \
--header 'Content-Type: application/json' \
--data '{
  "address": "TSgsSWGTLzTao1y4EKJdsABNbm2o8wvFNd",
  "amount": 2,
  "apiToken": "70XzjXtiJ8tkhI2Hg",
  "callbackUrl": "https://pp-staging.fx-edge.com/api/v2/callback",
  "currency": "USD",
  "paymentGatewayName": "USDT TRC20",
  "signature": "b40f3dbab5764e4d1440545f0a3274e5d534c41c82ef96e992b36edffd4e706e3ad63dc5c4a6a16205c856d70b177bf8",
  "timestamp": 1694944511,
  "withdrawCurrency": "USX",
  "tradingAccountLogin": ""
}'
```
### Response
`200`
```json
{
  "errorList": [],
  "finalAmount": 2,
  "paymentId": "59024850-553a-11ee-9325-1d6284d24bf8",
  "status": "NEW",
  "transactionAmount": 2,
  "netAmount": 2,
  "transactionCurrency": "USD",
  "finalCurrency": "USX",
  "processingFee": "0",
  "convertRatio": 1,
  "address": "TSgsSWGTLzTao1y4EKJdsABNbm2o8wvFNd",
  "tempTransactionId": "e80dd8d3-3589-4e97-8aeb-cd893a1d130d"
}
```
### Statuses of Withdrawal Requests
- **NEW**
  - The transaction has been initiated

- **ADMIN CONFIRMATION**
  - The transactions need to be confirmed by Support

- **PENDING**
  - The transaction is processing. This status is temporary. It should change to DONE or DECLINED after receiving confirmation/response from the blockchain

- **DECLINED**
  - The transaction has failed. This status is not final – it can be reprocessed within the same paymentID

- **DONE**
  - The transaction is done. The funds are booked

### Header Parameters
- **Content-Type** `string`

### Body Parameters
- **address** `string`
  - User cryptocurrency wallet address to withdraw

- **amount** `integer`
  - The amount the client wants to withdraw in the currency

- **apiToken** `string`
  - API token provided by Support

- **callbackUrl** `string`
  - Notification about changes in payment status will be sent to this URL

- **currency** `string`
  - Symbol of currency converted to withdrawCurrency (not full name), e.g. USD

- **paymentGatewayName** `string`
  - Name of payment gateway that will be used for withdrawal

- **signature** `string`
  - The signature generated from the request body and API secret

- **timestamp** `integer`
  - Current time in seconds

- **withdrawCurrency** `string`
  - Cryptocurrency to which currency is converted

- **tradingAccountLogin** `string`
  - Optional - Trading account ID of a user requesting a withdrawal or any other information that will help you identify transaction

### Responses
#### 200
- **Object**

### Response Attributes
- **errorList**: `array`

- **finalAmount** `number`

- **paymentId**: `string`
  - Payment Id which helps you find this transaction in the m2p dashboard

- **status**: `string`
  - Status of the transaction on the moment of sending request

- **transactionAmount** `number`

- **netAmount** `number`

- **transactionCurrency** `string`

- **finalCurrency** `string`

- **processingFee** `string`

- **convertRatio** `number`

- **address** `string`
  - Address on which funds has been sent

- **tempTransactionId** `string`

# Cryptocurrency Details

### Supported Cryptocurrencies and Gateways

| Cryptocurrency          | paymentCurrency or withdrawCurrency | paymentGatewayName   |
|-------------------------|-------------------------------------|----------------------|
| Bitcoin                 | BTC                                 | BTC                  |
| Ether                   | ETH                                 | ETH                  |
| Tether ERC20            | UST                                 | USDT ERC20           |
| USD Coin ERC20          | UCC                                 | USDC ERC20           |
| Tronix - TRX [Deposits only, min amount 1.5 TRX!] | TRX                  | TRX                  |
| Tether TRC20 [min amount 1 USDT!] | USX                          | USDT TRC20           |
| USD Coin TRC20 [Deposits only, min amount 1 USDC!] | UCX                  | USDC TRC20           |
| Binance Coin            | BNB                                 | BNB                  |
| Tether BEP20            | USB                                 | USDT BEP20           |
| USD Coin BEP20          | USB                                 | USDC BEP20           |
| MATIC                   | MAT                                 | MATIC                |
| USDT Polygon            | USP                                 | USDT POLYGON         |
| USDC Polygon [Deposits only] | UCP                           | USDC POLYGON         |
| XRP                     | XRP                                 | XRP                  |
| Dogecoin                | DOG                                 | DOGECOIN             |
| Litecoin                | LTC                                 | LTC                  |
| Solana                  | SOL                                 | SOL                  |
| USDT Solana             | USS                                 | USDT SOL             |
| USDC Solana             | UCS                                 | USDC SOL             |

### BTC Withdrawal Address Types
- **Legacy**: Starting with `1`
- **Script**: Starting with `3`
- **Segwit**: Starting with `bc1q`

### Test Environment Gateways
- Bitcoin
- USDT TRC20


# Signature

All signatures are valid for one minute after creation.

API token and API secret key are provided by Match-Trade.

### Algorithm

1. Take all the keys from your deposit or withdrawal request and sort them in A-Z order.
2. Concatenate the values according to the order of keys from the first step.
3. Append the API secret key to the concatenated string from the second step.
4. Build the SHA-384 hash of the string from the third step.

### Example

**API secret key**: MhB6KV2bVlhN6Nud8MRGKQaBTHmwOlgG2Pgj

```json
{
  "apiToken" : "b3RJ4qBRai129psUfrbGisnE5wpaMaFvovQS",
  "callbackUrl" : "http://test.com",
  "currency" : "USD",
  "paymentCurrency" : "BTC",
  "timestamp" : "1566214818"
}
```

The string from this request body is:
b3RJ4qBRai129psUfrbGisnE5wpaMaFvovQShttp://test.comUSDBTC1566214818

Then we append the secret key at the end and we receive:
b3RJ4qBRai129psUfrbGisnE5wpaMaFvovQShttp://test.comUSDBTC1566214818MhB6KV2bVlhN6Nud8MRGKQaBTHmwOlgG2Pgj

Then we use SHA-384 to generate a hash from this string and we receive our final signature value:
1e0e1de03c8f00177f689cef9e48a9cefab5deb2f572eb578e719ccf2b7f526758ae5aca12b0433d2812908e233a2325 

Please remember that the signature must have lowercase letters.

We add it to the request body in the signature field. The final request body is:
```json
{
  "apiToken" : "b3RJ4qBRai129psUfrbGisnE5wpaMaFvovQS",
  "callbackUrl" : "http://test.com",
  "currency" : "USD",
  "paymentCurrency" : "BTC",
  "signature":"1e0e1de03c8f00177f689cef9e48a9cefab5deb2f572eb578e719ccf2b7f526758ae5aca12b0433d2812908e233a2325",
  "timestamp" : "1566214818"
}
```

# Callback Signature Generation

**Important Note**: Callback signature should only be validated for status DONE.

To ensure that the callback comes from the Match2Pay system, you can optionally check the callback signature. To do this, you have to build the SHA-384 hash by adding the content of the callback in the following order: `transactionAmount` + `transactionCurrency` + `status` + `apitoken` + `apisecret`. It’s important to mention that the signature will be received in Headers.

### Example

```
{
  "depositAddress": "mhuLyXBc9CJfFu6KDpsMNaeiqxR5GZzMsH",
  "cryptoTransactionInfo": [
    {
      "txid": "55609bc1c2d3c4746e5f391a226144ed4ab369432adcef617e48d77826a92c1d",
      "confirmations": 1,
      "amount": 0.00011873,
      "confirmedTime": "May 18, 2023 1:51:47 PM",
      "status": "DONE",
      "processingFee": 0.00000119,
      "conversionRate": 27233.9
    }
  ],
  "paymentId": "4d42212c-8ac3-40c1-bc71-0770f522a9a9",
  "status": "DONE",
  "transactionAmount": 0.00011873,
  "netAmount": 0.00011754,
  "transactionCurrency": "BTC",
  "processingFee": 0.00000119,
  "finalAmount": 3.2,
  "finalCurrency": "USD",
  "conversionRate": 27233.9
} 
```

In this case, the `transactionAmount` + `transactionCurrency` + `status` string will look like this:

```
0.00011873BTCDONE + "apitoken" + "apisecret"
```

- **amount** = 0.00011873
- **transactionCurrency** = BTC
- **status** = DONE
- **apitoken** = provided by Support
- **apisecret** = provided by Support

In the case when the amount is 1 (e.g., USX):

```
"amount": 1
```

You have to add `.00000000` to the amount. The beginning of the string should look like this:

```
1.00000000USXDONE
```

`amount` should always have 8 decimal places in the string to generate the signature.

### Example

- **depositAddress**: `string`
- **cryptoTransactionInfo**: `array`
  - **child attributes**
    - **txid**: `string`
    - **confirmations**: `number`
    - **amount**: `number`
    - **confirmedTime**: `string`
    - **status**: `string`
    - **processingFee**: `number`
    - **conversionRate**: `number`
- **paymentId**: `string`
- **status**: `string`
- **transactionAmount**: `number`
- **netAmount**: `number`
- **transactionCurrency**: `string`
- **processingFee**: `number`
- **finalAmount**: `number`
- **finalCurrency**: `string`
- **conversionRate**: `number`
