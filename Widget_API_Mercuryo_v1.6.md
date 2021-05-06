# Widget main info 
Widget is the most convenient way to integrate with Mercuryo. There are two ways to integrate - redirect and iframe
[FAQ](https://help.mercuryo.io/en/articles/4519473-mercuryo-widget-faq)

1. [How to start](/Widget_API_Mercuryo_v1.6.md#1-how-to-start)

   1.1. [Get parameters](/Widget_API_Mercuryo_v1.6.md/#11-get-parameters)
 
   1.2. [Get a dashboard](/Widget_API_Mercuryo_v1.6.md/#12-get-a-dashboard)
 
   1.3. [Set up a widget](/Widget_API_Mercuryo_v1.6.md/#13-set-up-a-widget)
 
2. [Webhooks](/Widget_API_Mercuryo_v1.6.md#2-webhooks)

   2.1. [Callbacks signature check](/Widget_API_Mercuryo_v1.6.md#21-callbaks-signature-chek)
   
   2.2. [Callbacks Response Body](/Widget_API_Mercuryo_v1.6.md#22-callbaks-response-body)

   2.3. [Test callbacks](/Widget_API_Mercuryo_v1.6.md#23-test-callbaks)


3. [Transaction status types ](/Widget_API_Mercuryo_v1.6.md#3-transaction-status-types)

   3.1 [buy](/Widget_API_Mercuryo_v1.6.md/#31-buy)

   3.2 [sell](/Widget_API_Mercuryo_v1.6.md/#32-sell)
   
4. [API METHODS](/Widget_API_Mercuryo_v1.6.md/#4-api-methods)
5. [Signature Wallet Address](/Widget_API_Mercuryo_v1.6.md/#5-signature-wallet-address)
6. [Test](/Widget_API_Mercuryo_v1.6.md/#6-test)

   6.1. [Mercuryo SANDBOX](/Widget_API_Mercuryo_v1.6.md/#61-mercuryo-sandbox)
 
   6.2. [Check test transaction](/Widget_API_Mercuryo_v1.6.md/#62-check-test-transaction)

7. [Errors](/Widget_API_Mercuryo_v1.6.md/#7-errors)
	
***
***

#### 1. How to start

#### 1.1. Get parameters

1. To integrate Mercuryo to your own platform use [**iframe**](https://demo-widget.mercuryo.io)

2. For redirection to Mercuryo platform use  [**redirect**](https://widget.mercuryo.io/docs.html). In this case partners get comissions as with iframe.

#### 1.2. Get a dashboard	
[Partners admin](https://partners.mercuryo.io)  

![img1](https://github.com/mercuryoio/api-migration-docs/blob/master/img1.png)

| Section  | Description  | 
| ------------- | -------------  |
| Dashboard | page with transaction information, also you can add widget by tapping on the <Add widget> button |
| Profile | you can set up your comission and change password |	
| My widgets | list of widgets |
| Widget callbacks | list of callbacks; you can send test callback from this page |
| Reports | log of Transactions, Referrals or Referrals Withdraw; choose one of them to find the information |

#### 1.3 Set up a widget
**My widgets → Create Partner Widget**

![img2](https://github.com/mercuryoio/api-migration-docs/blob/master/img2.png)

| Section  | Description  | 
| ------------- | -------------  |
| Name | your widget name |
| Domain |  if Redirect `https://domain.io`, if iFrame your domain address (without “/” at the end of the address) 
 **Note:** Note: Please make sure there are no symbols or backspace after `https://domain.io` if you choose Redirect, or after `https://yourdomain.com` if you choose iFrame; otherwise the widget will not work properly and you’ll see `widget.mercuryo.io refused to connect` message.|
| Alias | transfer your widget id to your widget alias |
| Backward URL | merchant URL where the users will return to from Redirect |
| Callback URL | merchant server URL which listens to callbacks automatically when Mercuryo updates transaction status | 
| Sign Key | Callbacks signature check |
| Check signature  checkbox | enable or disable signature verification |
| Secret Key | your secret key for your signature verification |
| Is Active checkbox | it shows your is widget  active or not |

***
***

### 2. Webhooks	

These webhooks allow you to get current transaction status and include all the data.

1.  Set up automatic webhooks to your server in a dashboard (`Callback URL`).  
2.  Before creating an order you should create a unique ID (max size 255 characters).
3.  Set the generated ID into the `merchantTransactionId` parameter (in let `widgetParams`) or in the URL parameter `merchant_transaction_id`.

`widget_id` &ndash; partners widget ID

`merchant_transaction_id` &ndash; generated unique transaction ID by a partner	

#### 2.1. Callbacks signature check

**Sign Key** &ndash; Callbacks signature check

When the status of a transaction changes, the partner receives a request with transaction data from Mercuryo. 

You can set up a signature check and see the `X-Signature` HTTP header with a signature. 

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
	"id": "event_id",
        "card": {
              "number": "1111"
            },
	"tx": {
	    "id": "blockchain_transaction_id",
	    "address": "blockchain_address"
	    },
	"type": "withdraw",
	"user": {
	    "uuid4": "mercuryo_user_uuid4",
	    "country_code": "nz"
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
	"merchant_transaction_id": "merchant_transaction_id"
	}
       }
```
| Parameter  | Description  | 
| ------------- | -------------  |
| `id` | unique ID of the current event |
| `number` | last 4 numbers of a card  |
| `id` | blockchain transaction id |
| `type` | transaction type |
| `uuid4` | unique user ID |
| `country_code` | code of user's country |
| `amount` | amount of crypto |
| `status` | transaction status |
| `currency` | cryptocurrency |
| `created_at` | date of start |
| `updated_at` | date of last update |
| `fiat_currency` | fiat currency |
| `created_at_ts` | timestamp of creation |
| `fiat_currency` | code of fiat currency |
| `updated_at_ts` | timestamp of last update |
| `merchant_transaction_id` | merchant transaction id |


#### 2.3. Test callbacks

If you want to check the processing of the webhook, you can send a test callback.

Follow this steps:

1. Go to **Widget Callbacks**
2. Select the type of transaction on the **Transaction Type** field
3. Select the status of the transaction on the **Transaction Status** field
4. Select your widget on the **Widget Id** field
5. Click on the **Send test callback** button

[testcallbackimg](https://github.com/mercuryoio/api-migration-docs/blob/master/testcallbackimg.png)


***
***

### 3. Transaction status types 

#### 3.1. BUY
There are two internal operations "buy" and "withdraw" per 1 transaction

**Type: `buy`**

| Status  | Description  | 
| ------------- | -------------  |
| `new` | transaction initiated |
| `pending` | waiting for any action from the user to continue the transaction (waiting for 3ds input or descriptor) |
| `cancelled` | transaction cancelled (usually due to timeout of descriptor or 3ds) |
| `paid` | transaction completed successfully (money debited from the card) |
| `order_failed` | transaction was rejected by the issuer bank |
| `order_scheduled` | transaction is successful, the money is held off/frozen on the card by the bank, we are waiting for the client to pass KYC. As soon as the client passes KYC crypto will be sent to the address, if the client fails KYC transaction will be canceled within 1 hour abd client’s bank will return money to the card.|
|`descriptor_failed` | the user entered an invalid descriptor three times |

**Type: `withdraw`**

| Status  | Description  | 
| ------------- | -------------  |
| `new` | transaction initiated |
| `pending` | transaction in progress |
| `failed` | completed unsuccessfully (this is rare) |
| `completed` | successfully completed (received transaction hash) |

#### 3.2. SELL

There are two internal operations "deposit" and "sell" per 1 transaction

**Type:** `deposit`

| Status  | Description  | 
| ------------- | -------------  |
| `new` | new deposit |
| `pending`| deposit in progress |
| `succeeded` | deposit is done |
| `failed` | something went wrong |

**Type:** `sell`

| Status  | Description  | 
| ------------- | -------------  |
|`new` | transaction created |
| `pending` | transaction in progress |
| `succeeded` | successfully completed (money transferred to the card) |
| `failed` | not completed successfully (crypto is refunded to `refund_address`) |

***

### 4. API METHODS 

1. [Get rates](/Widget_API_Mercuryo_v1.6.md/#1-api-methods/#41-get-rates)

   1.1. [rate mercuryo fees partners fee](/Widget_API_Mercuryo_v1.6.md/#411-rate-mercuryo-fees-partners-fee)
 
   1.2. [rate mercuryo fee](/Widget_API_Mercuryo_v1.6.md/#412-rate-mercuryo-fee)
 
   1.3. [clear exchange rate](/Widget_API_Mercuryo_v1.6.md/#413-clear-exchange-rate)
2. [Get transaction status](/Widget_API_Mercuryo_v1.6.md/#42-get-transaction-status)
3. [Get final crypto *buy* or fiat *sell* amounts ](/Widget_API_Mercuryo_v1.6.md/#43-get-final-crypto-buy-or-fiat-sell-amounts)

   3.1 [buy](/Widget_API_Mercuryo_v1.6.md/#431-buy)

   3.2 [sell](/Widget_API_Mercuryo_v1.6.md/#432-sell)
4. [Get the list of supported fiat or cryptocurrencies](/Widget_API_Mercuryo_v1.6.md/#44-get-the-list-of-supported-fiat-or-cryptocurrencies)
5. [Get min or max limits](/Widget_API_Mercuryo_v1.6.md/#45-get-min-or-max-limits)

     5.1 [buy](/Widget_API_Mercuryo_v1.6.md/#451-buy)

     5.2 [sell](/Widget_API_Mercuryo_v1.6.md/#452-sell)
6. [Get list of supported countries](/Widget_API_Mercuryo_v1.6.md/#46-get-list-of-supported-countries)
7. [Get the users country by IP](/Widget_API_Mercuryo_v1.6.md/#46-get-the-users-country-by-ip)

****

#### 4.1. Get rates

##### 4.1.1 rate mercuryo fees partners fee

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


##### 4.1.2 rate mercuryo fee

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

##### 4.1.3 clear exchange rate

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

#### 4.2. Get transaction status

Request:
`GET https://api.mercuryo.io/v1.6/widget/transactions?widget_id=your_widget_id&merchant_transaction_id=your_id`

| Params | Description  | 
| ------------- | -------------  |
| widget_id | your widget id |
| merchant_transaction_id | current merchant transaction id |

Response example:

```js
 "status": 200,
    "total": 18,
    "next": null,
    "prev": null,
    "data": [
        {
            "buy": {
                "id": "058dc74257a417582",
                "transaction_id": "058dc74303d2d0459",
                "widget_id": "25865df1-362d-411a-b9a1-f0acacf7e720",
                "merchant_transaction_id": "qq1122ww",
                "fiat_currency": "EUR",
                "fiat_amount": "27.77",
                "currency": "BTC",
                "amount": "0.00300",
                "status": "order_failed",
                "created_at": "2020-12-11 10:41:07",
                "updated_at": "2020-12-11 10:41:14",
                "email": "74111111175@tt.st"
            }
        },
	...
```
*** 

#### 4.3. Get final crypto *buy* or fiat *sell* amounts

##### 4.3.1. buy

Request:
`GET https://api.mercuryo.io/v1.6/public/convert?from=fiat&to=crypto&type=buy&amount=fiat_ammount&widget_id=your_widget_id`

Example:
`GET https://api.mercuryo.io/v1.6/public/convert?from=EUR&to=BTC&type=buy&amount=100&widget_id=d9d9dab5-7127-417b-92fb-478bc90916b3`


| Params | Description  | 
| ------------- | -------------  |
| from | your fiat |
| to | your crypto |
| type | transaction type |
| amount | fiat amount |
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

##### 4.3.2. sell

Request:
`GET https://api.mercuryo.io/v1.6/public/convert?from=crypto&to=fiat&type=sell&amount=crypto_ammount&widget_id=your_widget_id`

Example:
`GET https://api.mercuryo.io/v1.6/public/convert?from=BTC&to=EUR&type=sell&amount=0.1&widget_id=d9d9dab5-7127-417b-92fb-478bc90916b3`

| Params | Description  | 
| ------------- | -------------  |
| from | your fiat |
| to | your crypto |
| type | transaction type |
| amount | cpyrto amount |
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

##### 4.3.3. buy rate
Request:
`GET https://api.mercuryo.io/v1.6/widget/buy/rate?from=fiat&to=crypto&amount=fiat_ammount&widget_id=your_widget_id`

Example:
`GET https://api.mercuryo.io/v1.6/widget/buy/rate?from=USD&to=BTC&amount=100&widget_id=d9d9dab5-7127-417b-92fb-478bc90916b3`


| Params | Description  | 
| ------------- | -------------  |
| from | your fiat |
| to | your crypto |
| amount | fiat amount |
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

#### 4.4. Get the list of supported fiat or cryptocurrencies

1. **Buy**
Request:
`GET https://api.mercuryo.io/v1.6/public/currencies-buy`

Response example:
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

#### 4.5. Get min or max limits				

##### 4.5.1 buy 
 
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

##### 4.5.2. sell 

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
#### 4.6. Get list of supported countries

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
#### 4.7. Get the users country by IP

Request:
`GET https://api.mercuryo.io/v1.6/public/data-by-ip?ip=user_ip` 

| Parameters | Description  | 
| ------------- | -------------  |
| ip | users ip |

Response example:
```js
{
    "status": 200,
    "data": {
        "country": {
            "code": "ru",
            "phone_prefix": 7,
            "enabled": true
        }
    }
}
```

***
***
### 5. Signature Wallet Address

To protect crypto-wallet address against forgery, you have to use a signature.

On the dashboard, in the widget’s setting, there is a section where you can enable signature verification. 

The partner sets Secret (automatically generated or manually) and checks the “Check signature“ box.


![img3](https://github.com/mercuryoio/api-migration-docs/blob/master/img3.png)

When the checkbox is on, the signature and address parameters must be appended to the widget’s URL (the partner generates on their side and substitutes).

Signature is calculated using the following algorithm:

signature = sha512(address+secret), where + concatenation operation.

If the signature is invalid, the widget will not be displayed.

For signature generation you can use [this](https://www.freeformatter.com/)

Cryptography & Security -> HMAC Generator -> Fill the **Copy-paste the string here** field -> Fill the **Secret Key** field -> Select **SHA256** as message digest algorithm

![img5](https://github.com/mercuryoio/api-migration-docs/blob/master/img5.png)

**OR**

Cryptography & Security -> SHA-256 Generator -> Fill the **Copy-paste the string here** field

![img6](https://github.com/mercuryoio/api-migration-docs/blob/master/img6.png)

***
***

### 6. TEST

You should provide all your test personal/server IPs for whitelist to use Mercuryo’s sandbox. Contact your Mercuryo manager for it

Test Adresses:
1. `https://sandbox-partners.mrcr.io` &ndash; test URL. You must use this as ` Domain` 
2. `https://sandbox-exchange.mrcr.io`  &ndash; test widget with redirection
Example:
`https://sandbox-exchange.mrcr.io/?widget_id=your_widget_id`

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
| `widgetId` | widget Id (required) |
| `host` | DOM element, container for the widget (required) |
| `width` | width of widget’s iframe in CSS-compatible format |
| `height` | height of widget’s iframe in CSS-compatible format |
| `type` | fixed type of the operation. Possible values are 'buy' or 'sell' |
| `amount` | amount of cryptocurrency |
| `fixAmount` | prevent user from changing amount of cryptocurrency. Boolean |
| `currency` | cryptocurrency ticker |
| `currencies` | array of available cryptocurrencies |
| `fixCurrency` | prevent user from changing cryptocurrency. Boolean |
| `fiatAmount` | amount of fiat currency |
| `fixFiatAmount` | prevent user from changing amount of fiat currency. Boolean |
| `fiatCurrency` | fiat currency ticker |
| `fixFiatCurrency` | prevent user from changing fiat currency. Boolean |
| `fiatCurrencies` | array of available fiat currencies |
| `fiatCurrencyDefault` | default fiat currency value |
| `ratesFeeOff` | display currency rates without fees. Boolean |
| `address` | wallet address for the predefined currency or BTC by default. It is used for sell refunds |
| `addressMap` | JS object of wallets. Example: {BTC: '…', ETH: '…'} |
| `hideAddress` | do not show wallet address in user interface. Boolean |
| `refundAddress` | wallet address for the predefined currency or BTC by default. It is used for sell refunds |
| `refundAddressMap` | JS object of wallets for sell refunds. Example: `{BTC: '…', ETH: '…'}` |
| `signature` | wallet address signature |
| `countryCode` | country code of user’s citizenship in ISO 3166-1 alpha-2 format. Example: 'ru' |
| `phone` | phone number |
| `firstName` | first name |
| `lastName` | last name |
| `birthdate` | date of birth in dd.mm.yyyy or dd/mm/yyyy format |
| `email` | e-mail adress |
| `returnUrl` | merchant’s URL to return to after transaction |
| `merchantTransactionId` | merchant’s transaction id |
| `refCode` | code for referral program |
| `face` | photo of user’s face in base64 format |
| `passport` | photo of user’s passport in base64 format |
| `idCardFront` | photo of front side of user’s ID card in base64 format |
| `idCardBack` | photo of back side of user’s ID card in base64 format |
| `lang` | default language for user interface in ISO 639-1 format. Example: 'ru' |
| `theme` | name of a custom CSS theme |
| `shareToken` | SumSub share token |

Callbacks:
1. onStatusChange &ndash; triggered each time the status of the purchase changes.

Example: `onStatusChange: data` => `console.log(data)`

Example of the returned data:`{ amount: "0.01336", currency: "BTC", fiat_amount: "100", fiat_currency: "EUR", id: "03b22d25d523a5285", status: "paid" }`

2 . onSellTransferEnabled &ndash; triggered during sell crypto scenario when user chooses sell flow variant and gets qr-code for money transferring.

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
| `refund_address`	| Wallet address for the predefined currency or BTC by default. It is used for sell refunds | `a8c1dead-ed5f-4740-b9ce-c4ea7721c93b` |
| `refund_address_map`	| JSON object of wallets for sell refunds	| `%7B%22BTC%22%3A%22a8c1dead-ed5f-4740-b9ce-c4ea7721c93b%22%7D (urlencoded JSON {"BTC":"a8c1dead-ed5f-4740-b9ce-c4ea7721c93b"})` |
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
 You can open widget to [Sandbox](https://sandbox-exchange.mrcr.io) 

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

 at the end of the page `</body>`
 
					
test `widget_id=60b69ef8-9287-49d7-8164-94d87d8982c4` You can find how to get your own test widget in second paragraph 
| Parameter name  | Description  | 
| ------------- | -------------  |
| phone | Phone number. You can use your own |
| email | E-mail adress. You can use your own |
| bank card | Bank card number. You can use your own real visa/MasterCard bank cards for 3ds part (the fiat won't be charged and data won't be stored) |

You can use sadbox API:
1. Add sadbox befor api
2. Change mercuryo -> mrcr

It must be like **sandbox**-api.**mrcr**.io/v1.6
Example:
`https://sandbox-api.mrcr.io/v1.6/public/currencies-buy`

You can make your own test adresses in wallet. 

Here are examples of the test addresses:

test BTC address &ndash; `msBE6aCaAesegu4VzbQW3L5xWBL8vi15Q7`

test erc-20 address &ndash; `0xA14691F9f1F851bd0c20115Ec10B25FC174371DF`
				

#### 6.2. Check test transaction 	

[**BTC**](https://tbtc.bitaps.com) testnet

[**ETH**](https://ropsten.etherscan.io) ropsten, metamask

### 7. Errors

| Error code  | Description  | 
| ------------- | -------------  |
| 2xx | all is okay. The request was successfully received, understood, and accepted |
| 4xx | most of them are a part of our normal flow, so all of those codes are coming from the backend and processed normally by our frontend. Don’t care much if you see any of those in your logs, as they don’t indicate any serious problem related with service |
| 5xx | server errors. Mercuryo team have already seen them in monitor and already chasing the problem, this is normally resolved as quickly as possible, because it might affects all Mercuryo partners. So if you see that error was detected 3 days ago or even half day ago — 100% it’s fixed |

**Most common cases of 4xx**

| Error code  | Description  | 
| ------------- | -------------  |
| 400 | tor ip blocked, anti-fraud |
| 401 | unauthorized, client token authentication timeout |
| 403 | transaction lock failed. Bank was unable to charge/hold the amount &ndash; insufficient funds on card, incorrect 3ds code entered |

