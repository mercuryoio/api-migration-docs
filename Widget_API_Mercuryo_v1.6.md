# Widget main info 
Widget is the most convenient way to integrate with Mercuryo. There are two ways to integrate - redirect and iframe
[FAQ](https://help.mercuryo.io/en/articles/4519473-mercuryo-widget-faq)

1. [How to start](/draw.md#1-how-to-start)

   1.1. [Step 1. Get parameters](//draw.md#11-step-1-.-get-parameters)
 
   1.2. [Step 2. Get a dashboard](//draw.md#12-step-2-.--get-a-dashboard)
 
   1.3. [Step 3. Set up a widget](//draw.md#13-step-3-.-set-up-a-widget)
 
   1.4. [Step 4. Check signature wallet address](//draw.md#14-step-4-.-check-signature-wallet-address)
2. [Webhooks](/draw.md#2-webhooks)
3. [Transaction status types ](/draw.md#3-transaction-status-types)

   3.1 [buy](//draw.md#31-buy)

   3.2 [sell](//draw.md#32-sell)
4. [API METHODS](/draw.md#4-api-methods)
5. [Signature Wallet Address](/draw.md#5-signature-wallet-address)
6. [Test](/draw.md#6-test)

   6.1. [SANDBOX](//draw.md#61-sandbox)

   6.2. [Get testnet transaction status](//62-get-testnet-transaction-status)
 
   6.3. [Get rates, limits etc](//draw.md#63-get-rates-limits-etc)

       

   6.4. [Check test transaction ](//draw.md#64-check-test-transaction)
	
***

#### 1. How to start

#### 1.1. Step 1. Get parameters

1. For integrate Mercuryo to your own platform use [**iframe**](https://demo-widget.mercuryo.io)

2. For redirection to Mercuryo platform use  [**redirect**](https://widget.mercuryo.io/docs.html). In this case partner get the comissions as with iframe.

#### 1.2. Step 2. Get a dashboard	
[Partners admin](https://partners.mercuryo.io)  

![img1](https://github.com/mercuryoio/api-migration-docs/blob/master/img1.png)

| Section  | Description  | 
| ------------- | -------------  |
| Dashboard | page with information abou transactions, also you can add widget by tap on <Add widget> button |
| My widgets | list of widgets |
| Widget callbacks | list of callbacks. You can send test callback from this page |
| Reports | log of Transactions, Referrals or Referrals Withdraw. You need to choose one of them to find the information|

#### 1.3 Step 3. Set up a widget
**My widgets → Create Partner Widget**

![img2](https://github.com/mercuryoio/api-migration-docs/blob/master/img2.png)

**Domain** &ndash; if Redirect `https://domain.io`, if iFrame your domain address (without “/” at the end of the address)
(1 widget = 1 domain)
**Note:** Please make sure, there are no symbols or backspace after `https://domain.io` if you choose Redirect or after `https://yourdomain.com` if you choose iFrame, otherwise the widget will not work properly and you’ll see `widget.mercuryo.io refused to connect` message.
**Backward URL** &ndash; merchant URL to which the users will return from Redirect
**Callback URL** &ndash; merchant server URL which listens to callbacks automatically when Mercuryo’s updates status of a transaction. 
 
#### 1.4. Step 4. Check signature wallet address

**Sign Key** &ndash; Callbacks signature check

When the status of a transaction changes, the partner receives a request with transaction data from Mercuryo. 

You can set-up a signature check and see the `X-Signature` HTTP header with a signature. 

You can generate a key here, for [example](https://implode.io/)

``` 
$key = '...';

$json = '{...}';

return hash_hmac('sha256', $json, $key) 
```

The signature can be checked by generating a hash through HMAC algorithm sha256 of the request body (JSON request) and a key that is in the partner's dashboard in the Sign Key field.

In this case, the request body must not change.

***
***

### 2. Webhooks	

These webhooks allow you to get current transaction status and include all the data.

1.  Set-up automatic webhooks to your server in a dashboard (`Callback URL`)  
2.  Before creating an order you should create a unique ID (max size 255 characters).
3.  Set the generated ID into the `merchantTransactionId` parameter (in let `widgetParams`) or in the URL parameter `merchant_transaction_id`.

`widget_id` &ndash; partners widget ID

`merchant_transaction_id` &ndash; generated unique ID by a partner, that was created in step 2	

#### 2.1. Callbacks signature check

**Sign Key** &ndash; Callbacks signature check

When the status of a transaction changes, the partner receives a request with transaction data from Mercuryo. 

You can set-up a signature check and see the `X-Signature` HTTP header with a signature. 

You can generate a key here, for [example](https://implode.io/)

``` 
$key = '...';

$json = '{...}';

return hash_hmac('sha256', $json, $key) 
```

The signature can be checked by generating a hash through HMAC algorithm sha256 of the request body (JSON request) and a key that is in the partner's dashboard in the Sign Key field.

In this case, the request body must not change.
		

#### 2.2. Callbacks Response Body
  
 Callbacks Payload example:
 
       ```JS
       {
    "payload": {
        "data": {
	"tx": {
	"id": "blockchain_transaction_id",
	"address": "blockchain_address"
	},
	"type": "withdraw",
	"user":{
	"uuid4":"mercuryo_user_uuid4,
	"country_code":"nz"
	},
	"amount": "0.02833",
	"status": "pending",
	"currency": "ETH",
	"created_at": "2021-03-09 20:05:20",
	"updated_at": "2021-03-09 20:05:20",
	"fiat_amount": "47.86",
	"created_at_ts": 1615320320,
	"fiat_currency": "USD",
	"updated_at_ts": 1615320320,
	"id":"mercuryo_id",
	"merchant_transaction_id":"merchant_transaction_id"
	}
       }
	```
| Parameter  | Description  | 
| ------------- | -------------  |
| `id` | unique ID of the current event |
| `uuid4` | unique user ID |
| `country_code` | code of users country |
| `number` | last 4 numbers of card number |
| `amount` | crypro amount |
| `status` | transaction status |
| `currency` | crypto currency |
| `created_at` and `updated_at` | data of start and last update |
|`fiat_currency` | fiat currency |
| `created_at_ts` | timestamp of creation |
| `fiat_currency` | code of fiat currency |
| `updated_at_ts` | timestamp of last update |
| `merchant_transaction_id` | merchant id |

***

### 3. Transaction status types 

#### 3.1. BUY
Per 1 transaction there are two internal operations "buy" and "withdraw"

**Type: `buy`**

| Status  | Description  | 
| ------------- | -------------  |
| `new` | transaction initiated |
| `pending` | waiting for any action from the user to continue the transaction (waiting for input 3ds or descriptor) |
| `cancelled` | transaction cancelled (usually due to timeout of descriptor or 3ds) |
| `paid` | transaction completed successfully (money debited from the card) |
| `order_failed` | transaction was rejected by the issuer bank |
| `order_scheduled` | transaction is successful, the money is held up/frozen on the card by the bank, we are waiting for the client to pass KYC. As soon as the client passes KYC - crypto is sent to the address, if the client fails KYC - transaction is canceled within 1 hours, client’s bank returns money back to card.|
|`descriptor_failed` | the user entered an invalid descriptor three times |

**Type: `withdraw`**

| Status  | Description  | 
| ------------- | -------------  |
| `new` | transaction initiated |
| `pending` | transaction in progress |
| `failed` | not completed successfully (this is rare) |
| `completed` | successfully (received transaction hash) |

#### 3.2. SELL

Per 1 transaction there are two internal operations "deposit" and "sell"

**Type:** `deposit`

| Status  | Description  | 
| ------------- | -------------  |
| `new` | new deposit |
| `pending`| deposit in progress |
| `succeeded` | deposit is done |
| `faild` | something gone wrong |

**Type:** `sell`

| Status  | Description  | 
| ------------- | -------------  |
|`new` | transaction created |
| `pending` | transaction in progress |
| `succeeded` | successfully completed (money transferred to the card) |
| `failed` | not completed successfully (crypto is refunded to `refund_address`) |

***

### 4. API METHODS 

1. [Get rates](/draw.md####1-get-rates)

   1.1. [rate+mercuryo fees+partners fee](//draw.md#####11-rate+mercuryo-fees+partners-fee)
 
   1.2. [rate+mercuryo fee](//draw.md#####12-rate+mercuryo-fee)
 
   1.3. [clear exchange rate](//draw.md#####13-clear-exchange-rate)
 
2. [Get transaction status](/draw.md####2-get-transaction-status)
3. [Get final crypto *buy* or fiat *sell* amounts ](/draw.md####3-get-final-crypto-buy-or-fiat-sell-amounts)

   3.1 [buy](//draw.md#####31-buy)

   3.2 [sell](//draw.md#####32-sell)
4. [Get the list of supported fiat or crypto currencies](/draw.md####4-get-the-list-of-supported-fiat-or-crypto-currencies)
5. [Get min/max limits](/draw.md####5-get-min/max-limits)

   5.1 [buy](//draw.md#####51-buy)

   5.2 [sell](//draw.md#####52-sell)
  
6. [Get list of supported countries](/draw.md####6-get-list-of-supported-countries)

			
#### 1. Get rates

##### 1.1 rate+mercuryo fees+partners fee

Request:
`GET https://api.mercuryo.io/v1.6/public/rates?widget_id=your_widget_id`

| Params | Description  | 
| ------------- | -------------  |
| widget_id | your widget id |

Response example:
```js
"status": 200,
    "data": {
        "buy": {
            "ALGO": {
                "ARS": "124.98297107",
                "BRL": "7.42299956",
                "CHF": "1.22559999",
                "EUR": "1.10750000",
                "GBP": "0.95730000",
                "IDR": "19428.79347193",
                "ILS": "4.36429999",
                "JPY": "144.72785374",
                "KES": "133.79984881",
                "KRW": "1497.005988",
                "MXN": "26.62299785",
                "NGN": "549.28757402",
                "NOK": "11.10899917",
                "PLN": "5.07299996",
                "RUB": "102.12793771",
                "SEK": "11.27299889",
                "TRY": "10.80869932",
                "UAH": "37.75198981",
                "USD": "1.33240000",
                "ZAR": "19.03699822"
            }, ...
```


##### 1.2 rate+mercuryo fee

Request:
`GET https://api.mercuryo.io/v1.6/widget/rates/partner-fee-off?widget_id=your_widget_id`

| Params | Description  | 
| ------------- | -------------  |
| widget_id | your widget id |

Response example:

```js
{"status": 200,
    "data": {
        "buy": {
            "BTC": {
                "EUR": "45999.85280047",
                "RUB": "4271678.76975651",
                "USD": "55253.19777882",
                "JPY": "6071645.41590771",
                "TRY": "449074.90569426",
                "GBP": "40221.37846708",
                "UAH": "1576044.12923561"
            }, ... 
```

##### 1.3 clear exchange rate

Request:
`GET https://api.mercuryo.io/v1.6/widget/rates/fee-off?widget_id=your_widget_id`

| Params | Description  | 
| ------------- | -------------  |
| widget_id | your widget id |

Response example:

```js
"status": 200,
    "data": {
        "buy": {
            "BTC": {
                "EUR": "45999.85280047",
                "RUB": "4240882.10347752",
                "USD": "55261.44188154",
                "JPY": "6006006.00600600",
                "TRY": "448933.78226711",
                "GBP": "39758.42779273",
                "UAH": "1563232.76535876"
            },
```
***

#### 2. Get transaction status

Request:
`GET https://api.mercuryo.io/v1.6/widget/transactions?widget_id=your_widget_id&merchant_transaction_id=your_id`

| Params | Description  | 
| ------------- | -------------  |
| widget_id | your widget id |
| merchant_transaction_id | current merchant transaction id |

Response example:

*** 

#### 3. Get final crypto *buy* or fiat *sell* amounts

##### 3.1. buy

Request:
`GET https://api.mercuryo.io/v1.6/public/convert?from=fiat&to=crypto&type=buy&amount=fiat_ammount&widget_id=your_widget_id`

| Params | Description  | 
| ------------- | -------------  |
| from | your fiat |
| to | your crypto |
| type | transaction type |
| ammount | fiat ammount |
| widget_id | your widget id |

Response example:

```js
{
    "status": 200,
    "data": {
        "type": "buy",
        "currency": "BTC",
        "amount": "0.00215648",
        "fiat_currency": "EUR",
        "fiat_amount": "100.00",
        "rate": "46371.86",
        "fee": "12.00",
        "fee_currency": "EUR"
    }
}
```

##### 3.2. sell

Request:
`GET https://api.mercuryo.io/v1.6/public/convert?from=crypto&to=fiat&type=sell&amount=crypto_ammount&widget_id=your_widget_id`

| Params | Description  | 
| ------------- | -------------  |
| from | your fiat |
| to | your crypto |
| type | transaction type |
| ammount | cpyrto ammount |
| widget_id | your widget id |

Response example:

```js
{
    "status": 200,
    "data": {
        "type": "sell",
        "currency": "BTC",
        "amount": "0.10000000",
        "fiat_currency": "EUR",
        "fiat_amount": "4624.90",
        "rate": "46248.89",
        "fee": "132.53",
        "fee_currency": "EUR"
    }
}
```

##### 3.3. buy rate
Request:
`GET https://api.mercuryo.io/v1.6/widget/buy/rate?from=fiat&to=crypto&amount=fiat_ammount&widget_id=your_widget_id`

| Params | Description  | 
| ------------- | -------------  |
| from | your fiat |
| to | your crypto |
| ammount | fiat ammount |
| widget_id | your widget id |

Response example:

```js
{
    "status": 200,
    "data": {
        "buy_token": "59d9bdb370427daed4deffdf2e94becdb7edc0848045cbbd65db08cf7b94068beyJ0IjoxNjE4OTgyNTU0LCJjIjoiQlRDIiwiYSI6IjAuMDAyNDM5MzAiLCJmYyI6IlVTRCIsImZhIjoiMTUwLjAwIiwiZiI6IjE0LjcyIiwic2YiOiIxNC43MjAwMDAwMDAwIiwidGYiOiIwIiwiciI6IjU1NDU4LjUwIiwiY2lkIjoiNjUwMWNjNGUxZDg3NDFlY2U2NmU4MGVkYmQyYjc2NzQiLCJ3IjoiMjU4NjVERjEtMzYyRC00MTFBLUI5QTEtRjBBQ0FDRjdFNzIwIn0=",
        "currency": "BTC",
        "amount": "0.00243930",
        "fiat_amount": "150.00",
        "fiat_currency": "USD",
        "rate": "55458.50",
        "fee": {
            "BTC": "0.00026543",
            "USD": "14.72"
        },
        "subtotal": {
            "BTC": "0.00243930",
            "USD": "135.28"
        },
        "total": {
            "BTC": "0.00270473",
            "USD": "150.00"
        },
        "kyc_limit_exceeded": false
    }
}
```
***

#### 4. Get the list of supported fiat or crypto currencies

1. **Buy**
Request:
`GET https://api.mercuryo.io/v1.6/public/currencies-buy`

Pesponse example:
```
{
    "status": 200,
    "data": [
        "EUR",
        "USD",
        "RUB",
        "JPY",
        "TRY",
        "GBP",
        "UAH",
        "GHS",
        "TZS",
        "UGX",
        "BTC",
        "ETH",
        "BAT",
        "USDT",
        "ALGO",
        "TRX",
        "OKB",
        "BCH",
        "DAI",
        "EGLD"
    ]
}
```
2. **Sell**
Request:
`GET https://api.mercuryo.io/v1.6/public/currencies-sell`	

Response example:
```js
{
    "status": 200,
    "data": [
        "EUR",
        "USD",
        "RUB",
        "BTC",
        "ETH",
        "BAT",
        "USDT",
        "ALGO",
        "TRX",
        "OKB",
        "BCH",
        "DAI",
        "EGLD"
    ]
}
```
***

#### 5. Get min/max limits				

##### 5.1 buy 
 
Request:
`GET https://api.mercuryo.io/v1.6/public/currency-limits?from=fiat&to=crypto&widget_id=your_widget_id&type=buy`

| Params | Description  | 
| ------------- | -------------  |
| from | fiat |
| to | crypto |
| widget_id | your widget id |
| type | transaction type |

Response example:
```js
{
    "status": 200,
    "code": 0,
    "message": "",
    "data": {
        "BTC": {
            "max": "0.08317062",
            "min": "0.002"
        },
        "USD": {
            "max": "4856.4",
            "min": "123.08"
        }
    }
}
```

##### 5.2. sell 

Request:
`GET https://api.mercuryo.io/v1.6/public/currency-limits?from=fiat&to=crypto&widget_id=your_widget_id&type=sell`

| Parameters | Description  | 
| ------------- | -------------  |
| from | fiat |
| to | crypto |
| widget_id | your widget id |
| type | transaction type |

Response example:
```js
{
    "status": 200,
    "code": 0,
    "message": "",
    "data": {
        "BTC": {
            "max": "0.05399633",
            "min": "0.002"
        },
        "USD": {
            "max": "3124.79",
            "min": "111.19"
        }
    }
}
```
***
#### 6. Get list of supported countries

Request:
`GET https://api.mercuryo.io/v1.6/public/card-countries` 

Response example:
```js
{
    "status": 200,
    "data": [
        "alb",
        "asm",
        "and",
	...
```

***
***
### 5. Signature Wallet Address

To protect against forgery of the crypto-wallet address, you have to use a signature.

On the dashboard, in the widget’s setting, there is a section where you can enable signature verification. 

The partner sets Secret (automatically generated or manually) and sets the “Check signature“ checkbox.


![img3](https://github.com/mercuryoio/api-migration-docs/blob/master/img3.png)

When the checkbox is on, the signature and address parameters must be appended to the widget’s URL (the partner generates on its side and substitutes).

Signature is calculated using the following algorithm:

signature = sha512(address+secret), where + concatenation operation.

If the signature is invalid, the widget will not be displayed.

Signature generation [example](https://abunchofutils.com/u/computing/sha512-hash-calculator)

![img4](https://github.com/mercuryoio/api-migration-docs/blob/master/img4.png)

***

### 6. TEST

You should provide all your test personal/server ip’s for whitelist to use Mercuryo’s sandbox. Contact your Mercuryo manager for it

How to use parameters

[**iframe**](https://demo-widget.mercuryo.io)

**Getting started**

1. Place `<div id="mercuryo-widget"></div>` inside `<body></body>`
2. Put `<script src="https://widget.mercuryo.io/embed.2.0.js"></script>` in the end of the page before `</body>`
Make a call:
`mercuryoWidget.run({ widgetId: 'a8c1dead-ed5f-4740-b9ce-c4ea7721c93b', host: document.getElementById('mercuryo-widget') });`
`widgetId` and `host` are minimal required parameters. Below is a complete list of parameters:

| Parameters | Description  | 
| ------------- | -------------  |
| widgetId | widget Id (required) |
| host | DOM element, container for the widget (required) |
| width | width of widget’s iframe in CSS-compatible format |
| height | height of widget’s iframe in CSS-compatible format |
| type | fixed type of the operation. Possible values are 'buy' or 'sell'|
| amount | amount of cryptocurrency |
| fixAmount | prevent user from changing amount of cryptocurrency. Boolean |
| currency | cryptocurrency ticker |
| currencies | array of available cryptocurrencies |
| fixCurrency | prevent user from changing cryptocurrency. Boolean |
| fiatAmount | amount of fiat currency |
| fixFiatAmount | prevent user from changing amount of fiat currency. Boolean |
| fiatCurrency | fiat currency ticker |
| fixFiatCurrency | prevent user from changing fiat currency. Boolean |
| fiatCurrencies | array of available fiat currencies |
| fiatCurrencyDefault | default fiat currency value |
| ratesFeeOff | display currency rates without fees. Boolean |
| address | wallet address for the predefined currency or BTC by default |
| addressMap | JS object of wallets. Example: {BTC: '…', ETH: '…'} |
| hideAddress | do not show wallet address in user interface. Boolean |
| refundAddress | wallet address for the predefined currency or BTC by default. It is used for refund in sell |
| refundAddressMap | JS object of wallets for refund in sell. Example: `{BTC: '…', ETH: '…'}` |
| signature | wallet address signature |
| countryCode – country code of user’s citizenship in ISO 3166-1 alpha-2 format. Example: 'ru' |
| phone | phone number |
| firstName | first name |
| lastName | last name |
| birthdate | date of birth in dd.mm.yyyy or dd/mm/yyyy format |
| email | e-mail adress |
| returnUrl | merchant’s URL to return to after transaction |
| merchantTransactionId | merchant’s transaction id |
| refCode | code for referral program |
| face | photo of user’s face in base64 format |
| passport | photo of user’s passport in base64 format |
| idCardFront | photo of front side of user’s ID card in base64 format |
| idCardBack | photo of back side of user’s ID card in base64 format |
| lang | default language for user interface in ISO 639-1 format. Example: 'ru' |
| theme | name of a custom CSS theme |
| shareToken | SumSub share token |

Callbacks:
1. onStatusChange - triggered each time the status of the purchase changes.

Example: `onStatusChange: data` => `console.log(data)`

Example of the returned data:`{ amount: "0.01336", currency: "BTC", fiat_amount: "100", fiat_currency: "EUR", id: "03b22d25d523a5285", status: "paid" }`

2 . onSellTransferEnabled - triggered during sell crypto scenario when user chooses sell flow variant and gets qr-code for money transferring.

Example: `onSellTransferEnabled: data` => `console.log(data)`

Example of the returned data:`{ amount: "0.01336", currency: "BTC", address: "04d3911f3b6de0843", id: "03b22d25d523a5285", flow_id: "payout" }`

***

[**redirect**](https://widget.mercuryo.io/docs.html)		

| Parameter | Description | Example |
| ------------- | -------------  | -------------  |
| `widget_id` | Widget Id (required) | `67710925-8b40-4767-846e-3b88db69f04d` |
| `type`	| Type of the operation	 | `buy` or `sell` |
| `amount` | Amount of cryptocurrency| `1.2` |
| `fix_amount` | Prevent user from changing amount of cryptocurrency | `true` |
| `currency` | Cryptocurrency ticker | `BTC` |
| `fix_currency` | Prevent user from changing cryptocurrency | `true` |
| `fiat_amount` | Amount of fiat currency	| `100` |
| `fix_fiat_amount` | Prevent user from changing amount of fiat currency	| `true` |
| `fiat_currency`	| Fiat currency ticker	| `EUR` |
| `fix_fiat_currency`	| Prevent user from changing fiat currency	| `true` |
| `currencies`	| Array of available crypto currencies	| `BTC`,`ETH` |
| `fiat_currencies`	| Array of available fiat currencies	| `EUR`,`USD` |
| `fiat_currency_default`	| Default fiat currency value	| `RUB` |
| `rates_fee_off`	| Display currency rates without fees	| `true` |
| `address`	| Wallet address for the predefined currency or BTC by default |	`a8c1dead-ed5f-4740-b9ce-c4ea7721c93b` |
| `address_map`	| Wallet address for the predefined currency or BTC by default	| `%7B%22BTC%22%3A%22a8c1dead-ed5f-4740-b9ce-c4ea7721c93b%22%7D (urlencoded JSON {"BTC":"a8c1dead-ed5f-4740-b9ce-c4ea7721c93b"})` |
| `hide_address`	| Do not show wallet address in user interface |	`true` |
| `refund_address`	| Wallet address for the predefined currency or BTC by default. It is used for refund in sell	| `a8c1dead-ed5f-4740-b9ce-c4ea7721c93b` |
| `refund_address_map`	| JSON object of wallets for refund in sell	| `%7B%22BTC%22%3A%22a8c1dead-ed5f-4740-b9ce-c4ea7721c93b%22%7D (urlencoded JSON {"BTC":"a8c1dead-ed5f-4740-b9ce-c4ea7721c93b"})` |
| `signature`	| Wallet address signature	| `cf23df2207d99a74fbe169e3eba035e633b65d94` |
| `country_code`	| Country code of user’s citizenship in ISO 3166-1 alpha-2 format |	`ru` |
| `phone` |	Phone number	| `79998887766` |
| `first_name` |	First name |	`Ivan` |
| `last_name` | Last name	 | `Ivanov` |
| `birthdate`	| Date of birth in dd.mm.yyyy or dd/mm/yyyy format |	`01/01/1980` |
| `email`	 | Email |	example@examplemail.com |
| `return_url`	| Merchant’s URL to return to after transaction |	`https://example.com` |
| `merchant_transaction_id` |	Merchant’s transaction id |	`d523a528503b22d25` |
| `ref_code` |	Code for referral program |	`Yr89v` |
| `lang`	 |Default language for user interface in ISO 639-1 format |	`ru` |
| `theme` |	Name of a custom CSS theme |	`mercuryo` |
| `share_token` |	SumSub share token |	`{token}` |

*** 

#### 6.1. Mercuryo SANDBOX 

[Dashboard](https://sandbox-partners.mrcr.io) 

**Redirect**
 You can open widgetto [Sandbox](https://sandbox-exchange.mrcr.io) 

**iframe**

1. Place `<div id="mercuryo-widget"></div>` inside `<body></body>`
 
2. Put to widget HTML
```
<script src="https://sandbox-widget.mrcr.io/embed.2.0.js"></script>

<script>mercuryoWidget.run({widgetId: 

da128a17-d019-41f4-b80c-a615d7e0f595 

host: document.getElementById('mercuryo-widget')})

</script>
```

 in the end of the page before `</body>`
 
					
test `widget_id=60b69ef8-9287-49d7-8164-94d87d8982c4` You can find how to get your own test widget in second paragraph 
| Parameter name  | Description  | 
| ------------- | -------------  |
| phone | Phine number. You can use your own |
| email | E-mail adress. You can use your own |
| bank card | Bank card number. You can use your own real visa/MasterCard bank cards for 3ds part (the fiat won't be charged and data won't be stored) |


You can make your own test adresses in wallet. 

Here are examples of the test addresses:

test BTC address &ndash; `msBE6aCaAesegu4VzbQW3L5xWBL8vi15Q7`

test erc-20 address &ndash; `0xA14691F9f1F851bd0c20115Ec10B25FC174371DF`
				

#### 6.2. Check test transaction 	

[**BTC**](https://tbtc.bitaps.com) testnet

[**ETH**](https://ropsten.etherscan.io) ropsten, metamask

