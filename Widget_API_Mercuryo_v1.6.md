# Widget main info 
Widget is the most convenient way to integrate with Mercuryo.

1. [How to start](/Widget_API_Mercuryo_v1.6.md#1-how-to-start)

   1.1. [Get parameters](/Widget_API_Mercuryo_v1.6.md/#11-get-parameters)
 
   1.2. [Get a dashboard](/Widget_API_Mercuryo_v1.6.md/#12-get-a-dashboard)
 
   1.3. [Set up a widget](/Widget_API_Mercuryo_v1.6.md/#13-set-up-a-widget)
   
   1.4. [Widget general questions](/Widget_API_Mercuryo_v1.6.md/#14-widget-general-questions)
 
2. [Webhooks](/Widget_API_Mercuryo_v1.6.md#2-webhooks)

   2.1. [Callbacks signature check](/Widget_API_Mercuryo_v1.6.md#21-callbacks-signature-check)
   
   2.2. [Callbacks Response Body](/Widget_API_Mercuryo_v1.6.md#22-callbacks-response-body)
   
   2.3. [Function onSellTransferEnabled](/Widget_API_Mercuryo_v1.6.md#23-function-onselltransferenabled)

   2.4. [Test callbacks](/Widget_API_Mercuryo_v1.6.md#24-test-callbacks)


3. [Transaction status types ](/Widget_API_Mercuryo_v1.6.md#3-transaction-status-types)

   3.1 [buy](/Widget_API_Mercuryo_v1.6.md/#31-buy)

   3.2 [sell](/Widget_API_Mercuryo_v1.6.md/#32-sell)
   
4. [API METHODS](/Widget_API_Mercuryo_v1.6.md/#4-api-methods)
5. [Signature Wallet Address](/Widget_API_Mercuryo_v1.6.md/#5-signature-wallet-address)
6. [Test](/Widget_API_Mercuryo_v1.6.md/#6-test)

   6.1. [Mercuryo SANDBOX](/Widget_API_Mercuryo_v1.6.md/#61-mercuryo-sandbox)
 
   6.2. [Check test transaction](/Widget_API_Mercuryo_v1.6.md/#62-check-test-transaction)

7. [Errors](/Widget_API_Mercuryo_v1.6.md/#7-errors)
8. [Sumsub shared token](/Widget_API_Mercuryo_v1.6.md/#8-sumsub-shared-token)
9. [Silent login](/Widget_API_Mercuryo_v1.6.md/#9-silent-login)
9. [Design](/Widget_API_Mercuryo_v1.6.md/#10-design)
	
***
***

#### 1. How to start

***

> **_NOTE:_** if you want to add widget to your mobile application use Mercuryo wrappers for [iOs](https://github.com/mercuryoio/Mercuryo-Widget-Wrapper-iOS) and for [Android](https://github.com/mercuryoio/Mercuryo-Widget-Wrapper-Android)

***

#### 1.1. Get parameters

For redirection to Mercuryo platform use  [**redirect**](https://widget.mercuryo.io/docs.html). In this case partners get commissions

[Example of filled parameters](https://github.com/mercuryoio/api-migration-docs/blob/master/imgwidget.png)

![imgwidget](https://github.com/mercuryoio/api-migration-docs/blob/master/imgwidget.png)


#### 1.2. Get a dashboard	
[Partners admin](https://partners.mercuryo.io)  

![img1](https://github.com/mercuryoio/api-migration-docs/blob/master/new_dashboard_1.jpg)

| Section  | Description  | 
| ------------- | -------------  |
| Dashboard | page with transaction information, also you can add widget by tapping on the <Add widget> button |
| Profile | you can set up your commission and change password |	
| My widgets | list of widgets |
| Widget callbacks | list of callbacks; you can send test callback from this page |
| Reports | log of Transactions, Referrals or Referrals Withdraw; choose one of them to find the information |

#### 1.3 Set up a widget
**My widgets → Create Partner Widget**

![img2](https://github.com/mercuryoio/api-migration-docs/blob/master/new_dashboard_setupawidget.jpg)

| Section  | Description  | 
| ------------- | -------------  |
| Name | your widget name |
| Domain |   `https://domain.io` 
 **Note:** Note: Please make sure there are no symbols or backspace after `https://domain.io` The widget will not work properly and you’ll see `widget.mercuryo.io refused to connect` message.|
| Alias | transfer your widget id to your widget alias |
| Backward URL | merchant URL where the users will return to from Redirect |
| Callback URL | merchant server URL which listens to callbacks automatically when Mercuryo updates transaction status | 
| Sign Key | Callbacks signature check |
| Check signature  checkbox | enable or disable signature verification |
| Secret Key | your secret key for your signature verification |
| Is Active checkbox | it shows your is widget  active or not |

	
#### 1.4 Widget general questions
	
1. [Mercuryo widget FAQ. Currencies, countries, limits, fees.](https://help.mercuryo.io/en/articles/4519473-mercuryo-widget-faq)
	
2. [How to buy crypto with Mercuryo widget.](https://help.mercuryo.io/en/articles/4359840-how-to-buy-crypto-with-mercuryo-widget)

***
***

### 2. Webhooks	

These webhooks allow you to get current transaction status and include all the data.

1.  Set up automatic webhooks to your server in a dashboard (`Callback URL`).  This is obligatory.
2.  Before creating an order you should create a unique ID (max size 255 characters).
3.  Set the generated ID into the `merchantTransactionId` parameter (in let `widgetParams`) or in the URL parameter `merchant_transaction_id`.

`widget_id` &ndash; partners widget ID

`merchant_transaction_id` &ndash; generated unique transaction ID by a partner. Must be unique for every transaction

#### 2.1. Callbacks signature check

**Sign Key** &ndash; Callbacks signature check

When the status of a transaction changes, the partner receives a request with transaction data from Mercuryo. 

You can set up a signature check and see the `X-Signature` HTTP header with a signature. 

The signature can be checked by generating a hash through HMAC algorithm sha256 of the request body (string request) and a key that is in the partner's dashboard in the Sign Key field.

You can use [this](https://www.freeformatter.com/)

Cryptography & Security -> HMAC Generator -> Fill the **Copy-paste the string here** field -> Fill the **Secret Key** field -> Select **SHA256** as message digest algorithm

![img5](https://github.com/mercuryoio/api-migration-docs/blob/master/img5.png)

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
| `fiat_ammount` | amount of fiat|
| `created_at_ts` | timestamp of creation |
| `fiat_currency` | code of fiat currency |
| `updated_at_ts` | timestamp of last update |
| `merchant_transaction_id` | merchant transaction id |


#### 2.3. Function onSellTransferEnabled

When the User wants to sell crypto he needs to choose what to do if the crypto rate will change more than 5% during the selling. There are two options
1. `payout`
2. `refund`
	
When he has made his choice and get the blockchain address this callback is called
	
Parameters:

| Parameter  | Description  | 
| ------------- | -------------  |
| `address` | blockchain address |
| `amount` | amount of crypto |
| `currency` | cryptocurrency |
| `id` | blockchain transaction id |
| `flow_id` | `payout` or `refund` based on users choice |
	
#### 2.4. Test callbacks

If you want to check the processing of the webhook, you can send a test callback.

Follow this steps:

1. Go to **Widget Callbacks**
2. Select the type of transaction on the **Transaction Type** field
3. Select the status of the transaction on the **Transaction Status** field
4. Select your widget on the **Widget Id** field
5. Click on the **Send test callback** button

![testcallbackimg](https://github.com/mercuryoio/api-migration-docs/blob/master/new_dashboard_testcallback.jpg)


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
| `order_scheduled` | order is in schedule |
| `order_failed` | transaction was rejected by the issuer bank |
| `descriptor_failed` | the user entered an invalid descriptor three times |

**Type: `withdraw`**

| Status  | Description  | 
| ------------- | -------------  |
| `new` | transaction initiated |
| `pending` | transaction in progress |
| `failed` | completed unsuccessfully (this is rare) |
| `order_scheduled` | the transaction is successful, the money is held off/frozen on the card by the bank, Mercuryo is waiting for the client to pass KYC. As soon as the client passes KYC crypto will be sent to the address, if the client fails KYC transaction will be canceled within 1 hour abd client’s bank will return money to the card.|
| `completed` | successfully completed (received transaction hash) |

#### 3.2. SELL

There are two internal operations "deposit" and "sell" per 1 transaction

**Type:** `deposit`

| Status  | Description  | 
| ------------- | -------------  |
| `new` | new deposit |
| `pending`| deposit in progress |
| `completed` | deposit is done |
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

1. [Get rates mercuryo fees partners fee](/Widget_API_Mercuryo_v1.6.md/#1-api-methods/#41-get-rates-mercuryo-fees-partners-fee) 
2. [Get transactions status](/Widget_API_Mercuryo_v1.6.md/#42-get-transactions-status)
3. [Get final crypto *buy* or fiat *sell* amounts ](/Widget_API_Mercuryo_v1.6.md/#43-get-final-crypto-buy-or-fiat-sell-amounts)

   3.1 [buy](/Widget_API_Mercuryo_v1.6.md/#431-buy)

   3.2 [sell](/Widget_API_Mercuryo_v1.6.md/#432-sell)
4. [Get the list of supported fiat or crypto currencies](/Widget_API_Mercuryo_v1.6.md/#44-get-the-list-of-supported-fiat-or-crypto-currencies)
5. [Get min or max limits](/Widget_API_Mercuryo_v1.6.md/#45-get-min-or-max-limits)

     5.1 [buy](/Widget_API_Mercuryo_v1.6.md/#451-buy)

     5.2 [sell](/Widget_API_Mercuryo_v1.6.md/#452-sell)
6. [Get list of supported countries](/Widget_API_Mercuryo_v1.6.md/#46-get-list-of-supported-countries)
7. [Get the users country by IP](/Widget_API_Mercuryo_v1.6.md/#47-get-the-users-country-by-ip)
8. [Get blockchain fee](/Widget_API_Mercuryo_v1.6.md/#48-get-fee-min)

****
**Attention:**
	
1. Recommended RTS 1 request per 10 second.
	
2. Mercuryo also needs to whitelist your production IP’s to make sure that Mercuryo receive requests right from you (Please connect your Mercuryo manager and send these IP’s).
	
****	
#### 4.1. Get rates mercuryo fees partners fee

Request:
`GET https://api.mercuryo.io/v1.6/public/rates?widget_id=your_widget_id`

| Parameter | Description  | 
| ------------- | -------------  |
| `widget_id` | your widget id |

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
***

#### 4.2. Get transactions status

**By transaction ID**	
	
Request:
`GET https://api.mercuryo.io/v1.6/sdk-partner/transactions?widget_id=your_widget_id&date_start=date&date_end=date&merchant_transaction_id=your_merchant_transaction_id`

| Parameter | Description  | 
| ------------- | -------------  |
| `widget_id` | your widget id |
| `date start` | date of the start of the period which you want get the status |
| `date end` | date of the end of the period which you want get the status |
| `merchant_transaction_id` | current merchant transaction id |
	
**By widget id**

Request:	
`GET https://api.mercuryo.io/v1.6/sdk-partner/transactions?widget_id=your_widget_id&date_start=date&date_end=date`
	
| Parameter | Description  | 
| ------------- | -------------  |
| `widget_id` | your widget id |
| `date start` | date of the start of the period which you want get the status |
| `date end` | date of the end of the period which you want get the status |

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
                "widget_id": "67710925-8b40-4767-846e-3b88db69f04d",
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
**Also you can sort transactions by their status:**
	
| Parameter |
| ------------- |
| `paid` |
| `cancelled` |
| `failed` |
| `order_scheduled` |
| `descriptor_failed` |

Request:	
`GET https://api.mercuryo.io/v1.6/sdk-partner/transactions?widget_id=your_widget_id&date_start=date&date_end=date&status=transaction_status`
	
You can use more then one status for search:

Request example:	
`GET https://api.mrcr.io/v1.6/sdk-partner/transactions?widget_id=your_widget_id&date_start=date&date_end=date&status=paid,cancelled,failed`
	
**You get the last 50 transaction as a response by default. You need to use parameter offset if you want to see previous transactions:**

| Parameter | Description |
| ------------- | ------------- |
| `offset` | the numerical value of the shift  | 
| `limit` | limit of rows `max: 50`, `default: 50` |
	
Request example:	
`GET https://api.mercuryo.io/v1.6/sdk-partner/transactions?widget_id=your_widget_id&date_start=date&date_end=date&offset=10&limit=20`
	
Transactions from number 11 to number 31 will be shown instead of from 1 to 50 as default. 


*** 

#### 4.3. Get final crypto *buy* or fiat *sell* amounts

##### 4.3.1. buy

Request:
`GET https://api.mercuryo.io/v1.6/widget/buy/rate?from=fiat&to=crypto&amount=fiat_ammount&widget_id=your_widget_id`

Example:
`GET https://api.mercuryo.io/v1.6/widget/buy/rate?from=EUR&to=USDT&amount=500&network=TRON&widget_id=67710925-8b40-4767-846e-3b88db69f04d`


| Parameter | Description  | 
| ------------- | -------------  |
| `from` | your fiat |
| `to` | your crypto |
| `type` | transaction type |
| `amount` | fiat amount |
| `network` | cryptocurrency network |
| `widget_id` | your widget id |

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
	Or
`GET https://api.mercuryo.io/v1.6/public/convert?from=crypto&to=fiat&type=sell&amount=crypto_ammount&widget_id=your_widget_id`
	
Example:
`GET https://api.mercuryo.io/v1.6/public/convert?from=BTC&to=EUR&type=sell&amount=0.1&widget_id=67710925-8b40-4767-846e-3b88db69f04d`
	Or
`GET https://api.mercuryo.io/v1.6/public/convert?from=BTC&to=EUR&type=sell&amount=0.3&widget_id=67710925-8b40-4767-846e-3b88db69f04d`

| Parameter | Description  | 
| ------------- | -------------  |
| `from` | crypto type |
| `to` | fiat type |
| `type` | transaction type |
| `amount` | cpyrto amount |
| `widget_id` | your widget id |

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
`GET https://api.mercuryo.io/v1.6/widget/buy/rate?from=USD&to=BTC&amount=100&widget_id=67710925-8b40-4767-846e-3b88db69f04d`


| Parameter | Description  | 
| ------------- | -------------  |
| `from` | your fiat |
| `to` | your crypto |
| `amount` | fiat amount |
| `widget_id` | your widget id |

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

#### 4.4. Get the list of supported fiat or crypto currencies

	
**Main method**
Request:
`GET https://api.mercuryo.io/v1.6/lib/currencies`	
	
Response example:
```js
	{
	    "status": 200,
    "data": {
        "fiat": [
            "EUR",
            "RUB",
            "USD",
            "IDR",
            "JPY",
            "TRY",
            "GBP",
            "UAH",
            "NGN",
            "KES",
            "ZAR",
            "GHS",
            "TZS",
            "UGX",
            "BRL"
        ],
        "crypto": [
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
        ], ...
}
```	
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

| Parameter | Description  | 
| ------------- | -------------  |
| `from` | fiat |
| `to` | crypto |
| `widget_id` | your widget id |
| `type` | transaction type |

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

| Parameter | Description  | 
| ------------- | -------------  |
| `from` | fiat |
| `to` | crypto |
| `widget_id` | your widget id |
| `type` | transaction type |

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

| Parameter | Description  | 
| ------------- | -------------  |
| `ip` | users ip |

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
	
#### 4.8. Get blockchain fee
	
If you want to get blockchain fee mannualy use this method
	
Request: 
`GET https://api.mercuryo.io/v1.6/widget/blockchain-fee?widget_id=`

| Parameter | Description  | 
| ------------- | -------------  |
| `widget_id` | your widget id |
	
Response example:
```js
{
    {
    "status": 200,
    "data": {
        "sell": {
            "EUR": {
                "blockchain_fee": "4",
                "currency": "EUR"
            },
            "USD": {
                "blockchain_fee": "4",
                "currency": "USD"
            }
        } ,
	 "buy": {
            "ALGO": {
                "blockchain_fee": "0",
                "currency": "EUR"
            },
            "BAT": {
                "blockchain_fee": "13",
                "currency": "EUR"
            },
            "BCH": {
                "blockchain_fee": "0",
                "currency": "EUR"
            }...
}
```
	
***
***
### 5. Signature Wallet Address

To protect a crypto-wallet address against forgery, you have to use a signature.
	
You need to use `address` as a parameter like this: https://sandbox-exchange.mrcr.io/?widget_id=your_widget_id&address=blockchain_adress`

On the dashboard, in the widget’s setting, there is a section where you can enable signature verification. 

The partner sets Secret (automatically generated or manually) and checks the “Check signature“ box.


![img3](https://github.com/mercuryoio/api-migration-docs/blob/master/new_dashboard_secret.jpg)
	
If you pass parameter `address` so parameter `signature` is obligatory too.

Signature is calculated using the following algorithm:

signature = sha512(address+secret), without space between `address` and `secret`. Sign key isn't required.
	
The Validation is on the step `Pay with card`. If the signature is invalid, the widget is displayed, but the User cannot complete the operation. In this case the error `Signature is invalid` will be shown to the User.

For signature generation you can use [this](https://www.freeformatter.com/)

Cryptography & Security -> SHA-512 Generator -> Fill the **Copy-paste the string here** field

![img6](https://github.com/mercuryoio/api-migration-docs/blob/master/new_dashboard_sha512.jpg)
	
**Example link:**
	
`https://sandbox-exchange.mrcr.io/?widget_id=your_widget_id&address=blockchain_adress&signature=user_generated_signature`

*There are no autofilled parameters `address`, `address_map` and `hide_address` for Mercuryos own widget.*
***
***

### 6. TEST

You should provide all your test personal/server IPs for whitelist to use Mercuryo’s sandbox. Contact your Mercuryo manager for it

Test Addresses:
1. `https://sandbox-partners.mrcr.io` &ndash; test URL
2. `https://sandbox-exchange.mrcr.io`  &ndash; test widget with redirection. You must use this as `Domain`
Example:
`https://sandbox-exchange.mrcr.io/?widget_id=your_widget_id`

How to use parameters

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
	
Callbacks:
1. onStatusChange &ndash; triggered each time the status of the purchase changes.

Example: `onStatusChange: data` => `console.log(data)`

Example of the returned data:`{ amount: "0.01336", currency: "BTC", fiat_amount: "100", fiat_currency: "EUR", id: "03b22d25d523a5285", status: "paid" }`

2 . onSellTransferEnabled &ndash; triggered during sell crypto scenario when user chooses sell flow variant and gets qr-code for money transferring.

Example: `onSellTransferEnabled: data` => `console.log(data)`

Example of the returned data:`{ amount: "0.01336", currency: "BTC", address: "04d3911f3b6de0843", id: "03b22d25d523a5285", flow_id: "payout" }`

*** 

#### 6.1. Mercuryo SANDBOX 

[Dashboard](https://sandbox-partners.mrcr.io) 

**Redirect**
 You can open widget to [Sandbox](https://sandbox-exchange.mrcr.io) 

You can use sandbox API:
1. Add sandbox before `api`
2. Change `mercuryo` -> `mrcr`

It must be like **sandbox**-api.**mrcr**.io/v1.6

Example test redirect widget:
	
`https://sandbox-exchange.mrcr.io/?widget_id=625376b4-cf28-43b8-b836-550cd558c431`

`test widget_id` = `625376b4-cf28-43b8-b836-550cd558c431`

Please use this `widget_id` when you try API methods in sandbox
	
Example:
`https://sandbox-api.mrcr.io/v1.6/public/currencies-buy`

You can make your own test addresses in the wallet. 

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
| 5xx | server errors. Mercuryo team has already seen them on monitor and is already chasing the problem. This is normally resolved as quickly as possible, because it might affect all Mercuryo partners. So if you see that error was detected 3 days ago or even half day ago — 100% it’s fixed |

**Most common cases of 4xx**

| Error code  | Description  | 
| ------------- | -------------  |
| 400 | tor ip blocked, anti-fraud |
| 401 | unauthorized, client token authentication timeout |
| 403 | transaction lock failed. Bank was unable to charge/hold the amount &ndash; insufficient funds on card, incorrect 3ds code entered |

### 8. Sumsub shared token.

Share Token allows Mercuryo to check partner databases for user identity and use it to pass over Mercuryo's KYC procedure. In this way, users don't have to verify their identities manually on Mercuryo side and undergo KYC twice.

1. [**What is shared token**](https://developers.sumsub.com/api-reference/#sharing-applicants-between-partner-services)

2. [**How it works**](https://youtu.be/DpGd8wy07RM)
	
	
### 9. Silent login

#### 9.1. Silent Login

Silent login is a tool that makes login in Mercuryo easier for your users. 
Contact Your account manager to agree on what user data is allowed to silent sign-up for your users. 


#### 9.2. Acceptance: 

1. You need to add to your Terms and Policy an agreement of sharing data with Mercuryo.
2. You need to make an agreement with Mercuryo aabout Mercuryo using data for registration and third parties.
3. You need to ask users to accept Mercuryo term in your interface by publishing them on Your site.

#### 9.3. API methods
There is two API methods: one for users that already have mercuryo account and one for new ones.

##### 9.3.1. **Silent Login**

For users already registered in Mercuryo use:

Request: `POST https://api.mercuryo.io/v1.6/sdk-partner/login`

Note: Please use one and only one of available params

| Header  | Type  |  
| ------------- | -------------  |
| Sdk-Partner-Token | your partner key |
| **Parameter**  | **Description**  | 
| ------------- | -------------  |
| `phone` | users phone number |
| `user_uuid4` | id you got after using sign-up |
| `email` | users email |

Responses:

| Case  | Code  |  Response  |
| ------------- | -------------  |-------------  |
| user is register by you | 200 | `init_token` and `init_type_token` and `user_uuid` |
| user is register by other partner | 200 | `init_token` and `init_type_token` and `user_uuid` |
| invalid phone | 400 |  invalid phone |
| user is blocked by reasons: `LOCK_REASON_TOO_MANY_LOGIN_FAILURES`, `LOCK_REASON_FRAUD`, `LOCK_REASON_REFUND`, `LOCK_REASON_TOO_MANY_REQUESTS`, `LOCK_REASON_SANCTION_LIST`   | 403 | silent login forbidden |
| user is deleted `LOCK_REASON_DELETED` | 403 | silent login forbidden |
| invalid `sdk-partner-token` | 401 | wrong partner |
| user is not registered | 404 | user not found |

![img1](https://github.com/mercuryoio/api-migration-docs/blob/master/img/Silent%20Login.png)


##### 9.3.2. **Silent SignUp**

For users that have no Mercuryo account use:

Request: `POST https://api.mercuryo.io/v1.6/sdk-partner/sign-up`

Note:
Email is obligatory in Mercuryo, that's why if you don't pass valid email - we will have to ask user during first login.
All Mercuryo users have to go through 'Know your Customer' procedure. You can save your users from extra steps by passing valis SumSub share_token. 


| Header  | Type  |  
| ------------- | -------------  |
| Sdk-Partner-Token | obligatory |
| **Parameter**  | **Type**  | 
| ------------- | ------------- |
| **User data:**   |
| accept | obligatory | 
| phone | optional |  
| email | optional |
| share-token | optional |


Example of Response body:

```
{
  “status”: 200,
  “data”: {
    “uuid”: “7f6f384b-3388-479f-a26e-3a6e290f5bfe”,
    “init_token”: “0835f4b0c56de3586",
    “init_token_type”: “sdk_partner_authorization”
  }
}
 ```

| Case  | Code  |  Response  |
| ------------- | -------------  |-------------  |
| user is register by partner parameters | 200 | init_token and init_token_type |
| user is already registered | 400 | user already registered in system |
| user can not be registered  | 400 |  user \ can not be registered  |
	
**Errors**

| Case  | HTTP Code  |  Response code |
| ------------- | -------------  |-------------  |
| Some internal error | 403 | 403023 |
| 'country_code' invalid | 400 | 400005 |
| 'phone' is invalid | 400 | 400010 |
| 'accept' field is invalid | 400 | 400006 |
| 'email' field is invalid | 400 | 400037 |
| 'language_code' is invalid | 400 | 400038 |
| 'document' is invalid | 400 | 400039 |
| 'birthday' is invalid | 400 | 400040 |
| 'first_name' is invalid | 400 | 400041 |
| 'last_name' is invalid | 400 | 400042 |
| 'token' is invalid | 400 | 400043 |

Response example:

```js
{
"status": 200,
"data": {
"init_token": "06b931d9b6b696442",
"init_token_type": "sdk_partner_authorization"
}
}
```
![img2](https://github.com/mercuryoio/api-migration-docs/blob/master/img/Silent%20Sign%20up.png)

#### 9.5. How to

You need follow this steps:
1. Get `init_token` and `init_token_type` by using API Methods
2. Redirect user to `https://your_widget_url/?init_token=users_init_token&init_token_type=users_init_token_type`. The User needs to verify his phone\email by code that he will get on his phone\e-mail. The verification page is made on Mercuryo side
Example of redirection link: `https://exchange.mercuryo.io/?init_token=081b8291864c77282&init_token_type=sdk_partner`

**NB:**
Mercuryos main contact type is **e-mail**

#### 9.6 Init token

`init_token` expires after first time it was passed to widget or after 1 hour.



### 10. Design.

To customise Mercuryo widget follow the instruction in figma [here](https://www.figma.com/file/NDsbB4owdvvD7T3EU9Ju4I/Customization?node-id=3%3A19).	

To get Mercuryo fonts download this archive: [Mercuryo_fonts](https://github.com/mercuryoio/api-migration-docs/blob/master/design/Mercuryo_fonts.zip)

To get Mercuryo logos download this archive: [Mercuryo_logos](https://github.com/mercuryoio/api-migration-docs/blob/master/design/Mercuryo_logos.zip)
