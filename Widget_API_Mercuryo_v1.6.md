# Widget main info 
Widget is the most convenient way to integrate with Mercuryo. There are two ways to integrate - redirect and iframe
[FAQ](https://help.mercuryo.io/en/articles/4519473-mercuryo-widget-faq)

# How to use parameters
1. For integrate Mercuryo to your own platform use [**iframe**](https://demo-widget.mercuryo.io)

2. For rediraction to Mercuryo platform use  [**redirect**](https://widget.mercuryo.io/docs.html). In this case partner get the comissions as with iframe.

# How to get a dashboard	
[Partners admin](https://partners.mercuryo.io)  

![img1](https://github.com/mercuryoio/api-migration-docs/blob/master/img1.png)

**Dashboard** &ndash; plage with information abou transactions, also you can add widget by tap on <Add widget> button
	
**My widgets** &ndash; list of widgets

**Widget callbacks** &ndash; list of callbacks. You can send test callback from this page

**Reports** &ndash; log of Transactions, Referrals or Referrals Withdraw. You need to choose one of them to find the information

# How to set up a widget
My widgets → Create Partner Widget 

![img2](https://github.com/mercuryoio/api-migration-docs/blob/master/img2.png)

Domain &ndash; if Redirect https://domain.io, if iFrame your domain address (without “/” at the end of the address)
(1 widget = 1 domain)

**Note:** Please make sure, there are no symbols or backspace after `https://domain.io` if you choose Redirect or after `https://yourdomain.com` if you choose iFrame, otherwise the widget will not work properly and you’ll see `widget.mercuryo.io refused to connect` message.

**Backward URL** &ndash; merchant URL to which the users will return from Redirect

**Callback URL** &ndash; merchant server URL which listens to callbacks automatically when Mercuryo’s updates status of a transaction. 
 
# Check signature &ndash; signature wallet address

**Sign Key** &ndash; Callbacks signature check

When the status of a transaction changes, the partner receives a request with transaction data from Mercuryo. 

You can set-up a signature check and see the `X-Signature` HTTP header with a signature. 

You can generate a key here, for [example](https://implode.io/)

`$key = '...'

$json = '{...}'

return hash_hmac('sha256', $json, $key)`

The signature can be checked by generating a hash through HMAC algorithm sha256 of the request body (JSON request) and a key that is in the partner's dashboard in the Sign Key field.

In this case, the request body must not change.

# Webhooks	

These webhooks allow you to get current transaction status and include all the data.

1.  Set-up automatic webhooks to your server in a dashboard (`Callback URL`)  
2.  Before creating an order you should create a unique ID (max size 255 characters).
3.  Set the generated ID into the `merchantTransactionId` parameter (in let `widgetParams`) or in the URL parameter `merchant_transaction_id`.

`widget_id` &ndash; partners widget ID

`merchant_transaction_id` &ndash; generated unique ID by a partner, that was created in step 2		

**Callbacks Response Body:**
  
 Callbacks Payload example:
 
       `{
    "url": "https:\/\/webhook.site\/75f6bdf4-3b4c-463c-8a81-918523f765aa",
    "payload": {
        "data": {
            "id": "06292dddc67051001",
            "card": {
                "number": "6447"
            },
            "type": "buy",
            "user": {
                "uuid4": "938252dc-5640-4a29-a4c3-14ce2e0bafda",
                "country_code": "ru"
            },
            "amount": "0.092835600305367199",
            "status": "order_scheduled",
            "currency": "ETH",
            "created_at": "2021-04-08 07:40:13",
            "updated_at": "2021-04-08 07:40:15",
            "fiat_amount": "15000.00",
            "created_at_ts": 1617867613,
            "fiat_currency": "RUB",
            "updated_at_ts": 1617867615,
            "merchant_transaction_id": "9076"
        }`

`id` &ndash; unique ID of the current event

`uuid4` &ndash; unique user ID

`country_code` &ndash; - code of users country

`number` &ndash; last 4 numbers of card number

`amount` &ndash; crypro amount

`status` &ndash; transaction status

`currency` &ndash; crypto currency

`created_at` and `updated_at` &ndash; data of start and last update

`fiat_currency` &ndash; fiat currency

`created_at_ts` &ndash; timestamp of creation

`fiat_currency` &ndash; code of fiat currency

`updated_at_ts` &ndash; timestamp of last update

`merchant_transaction_id` &ndash; merchant id


# Transaction status types 

## BUY
Per 1 transaction there are two internal operations "buy" and "withdraw"

**Type: `buy`**

`new` &ndash; transaction initiated

`pending` &ndash; waiting for any action from the user to continue the transaction (waiting for input 3ds or descriptor)

`cancelled` &ndash; transaction cancelled (usually due to timeout of descriptor or 3ds)

`paid` &ndash; transaction completed successfully (money debited from the card)

`order_failed` &ndash; transaction was rejected by the issuer bank 

`order_scheduled` &ndash; transaction is successful, the money is held up/frozen on the card by the bank, we are waiting for the client to pass KYC. As soon as the client passes KYC - crypto is sent to the address, if the client fails KYC - transaction is canceled within 1 hours, client’s bank returns money back to card.

`descriptor_failed` &ndash; the user entered an invalid descriptor three times

**Type: `withdraw`**

`new` &ndash; transaction initiated

`pending` &ndash; transaction in progress

`failed` &ndash; not completed successfully (this is rare)

`completed` &ndash; successfully (received transaction hash)

## SELL

Per 1 transaction there are two internal operations "deposit" and "sell"

**Type:** `deposit`

`new` &ndash; new deposit

`pending` &ndash; deposit in progress

`succeeded` &ndash; deposit is done

`faild` &ndash; something gone wrong

**Type:** `sell`

`new` &ndash; transaction created

`pending` &ndash; transaction in progress

`succeeded` &ndash; successfully completed (money transferred to the card)

`failed` &ndash; not completed successfully (crypto is refunded to `refund_address`)

## API METHODS 
			
**How to get rates.**

**rate+mercuryo fees+partners fee**

Request:
`https://api.mercuryo.io/v1.6/public/rates`

[Example](https://api.mercuryo.io/v1.6/public/rates?widget_id=d9d9dab5-7127-417b-92fb-478bc90916b3)


**rate+mercuryo fee**

Request:
`https://api.mercuryo.io/v1.6/widget/rates/partner-fee-off`

[Example](https://api.mercuryo.io/v1.6/widget/rates/partner-fee-off?widget_id=d9d9dab5-7127-417b-92fb-478bc90916b3)


**clear exchange rate**

Request:
`https://api.mercuryo.io/v1.6/widget/rates/fee-off`

[Example](https://api.mercuryo.io/v1.6/widget/rates/fee-off?widget_id=d9d9dab5-7127-417b-92fb-478bc90916b3)


**How to get transaction status**

Request:
`https://api.mercuryo.io/v1.6/widget/transactions`

[Example](https://api.mercuryo.io/v1.6/widget/transactions?widget_id=d9d9dab5-7127-417b-92fb-478bc90916b3&merchant_transaction_id=1234)


**How to get final crypto *buy* or fiat *sell* amounts**

1. **buy** 

Request:
`https://api.mercuryo.io/v1.6/public/convert`

[Example](https://api.mercuryo.io/v1.6/public/convert?from=EUR&to=BTC&type=buy&amount=100&widget_id=d9d9dab5-7127-417b-92fb-478bc90916b3)


2. **sell**

Request:
`https://api.mercuryo.io/v1.6/public/convert`

[Example](https://api.mercuryo.io/v1.6/public/convert?from=BTC&to=EUR&type=sell&amount=0.1&widget_id=d9d9dab5-7127-417b-92fb-478bc90916b3)


3.
Request:
`https://api.mercuryo.io/v1.6/widget/buy/rate`

[Example](https://api.mercuryo.io/v1.6/widget/buy/rate?from=USD&to=BTC&amount=100&widget_id=d9d9dab5-7127-417b-92fb-478bc90916b3)


**How to get the list of supported fiat/cryptocurrencies**

1. `https://api.mercuryo.io/v1.6/public/currencies-buy`

2. `https://api.mercuryo.io/v1.6/public/currencies-sell`	


**How to get min/max limits**				

1. **buy** 
 
Request:
`https://api.mercuryo.io/v1.6/public/currency-limits`

[Example](https://api.mercuryo.io/v1.6/public/currency-limits?from=USD&to=BTC&widget_id=d9d9dab5-7127-417b-92fb-478bc90916b3&type=buy)


2. **sell** 

Request:
`https://api.mercuryo.io/v1.6/public/currency-limits`

[Example:](https://api.mercuryo.io/v1.6/public/currency-limits?from=USD&to=BTC&widget_id=d9d9dab5-7127-417b-92fb-478bc90916b3&type=sell)	


**How to get list of supported countries**

`https://api.mercuryo.io/v1.6/public/card-countries` 


# Signature Wallet Address

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

# TEST (to use Mercuryo’s sandbox you should provide all your test personal/server ip’s for whitelist) 

How to use parameters

1. [iframe](https://demo-widget.mercuryo.io)
2. [redirect](https://widget.mercuryo.io/docs.html)		

# SANDBOX 

[Dashboard](https://sandbox-partners.mrcr.io) 

Redirect to [Sandbox](https://sandbox-exchange.mrcr.io) 

**iframe**

1. Place `<div id="mercuryo-widget"></div>` inside `<body></body>`
 
2. Put 
`<script src="https://sandbox-widget.mrcr.io/embed.2.0.js"></script>

<script>mercuryoWidget.run({widgetId: 

da128a17-d019-41f4-b80c-a615d7e0f595 

host: document.getElementById('mercuryo-widget')})

</script>`

 in the end of the page before `</body>`
 
					
test `widget_id=60b69ef8-9287-49d7-8164-94d87d8982c4` You can find how to get your own test widget in second paragraph 

**phone** &ndash; you can use your own

**email** &ndash; you can use your own

**bank card** &ndash; you can use your own real visa/MasterCard bank cards for 3ds part (the fiat won't be charged and data won't be stored)


You can make your own test adresses in wallet. 

Here are examples of the test addresses:

test BTC address &ndash; `msBE6aCaAesegu4VzbQW3L5xWBL8vi15Q7`

test erc-20 address &ndash; `0xA14691F9f1F851bd0c20115Ec10B25FC174371DF`
				
# II How to get testnet transaction status
1. On the partner side, create a unique ID (max size 255 characters).
2. Set the generated ID in in the URL parameter `merchant_transaction_id`. 
3. Call API method
					
https://sandbox-api.mrcr.io/v1.5/widget/transactions?widget_id=60b69ef8-9287-49d7-8164-94d87d8982c4&merchant_transaction_id=1234
				
test `widget_id` example &ndash; `60b69ef8-9287-49d7-8164-94d87d8982c4` 

`merchant_transaction_id` &ndash; generated unique id by partner that was created in the step 2

# III How to get rates, limits etc


[**Rates**](https://sandbox-api.mrcr.io/v1.5/public/rates?widget_id=60b69ef8-9287-49d7-8164-94d87d8982c4)


**How to get the list of supported fiat/cryptocurrencies**	

[Buy](https://sandbox-api.mrcr.io/v1.5/public/currencies-buy)

[Sell](https://sandbox-api.mrcr.io/v1.5/public/currencies-sell)		


**How to get min/max limits**	

Request:

`https://sandbox-api.mrcr.io/v1.5/public/currency-limits`

[Example](https://sandbox-api.mrcr.io/v1.5/public/currency-limits?from=USD&to=BTC&widget_id=60b69ef8-9287-49d7-8164-94d87d8982c4)
					
**How to get list of supported countries**

Request:

`https://sandbox-api.mrcr.io/v1.5/public/card-countries` 

**How to get final crypto amount**

Request:

`https://sandbox-api.mrcr.io/v1.5/public/convert`

[Example](https://sandbox-api.mrcr.io/v1.5/public/convert?from=EUR&to=BTC&type=buy&amount=100&widget_id=60b69ef8-9287-49d7-8164-94d87d8982c4)

	
# IV Check test transaction 	

[**BTC**](https://tbtc.bitaps.com) testnet

[**ETH**](https://ropsten.etherscan.io) ropsten, metamask
