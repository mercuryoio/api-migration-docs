# Widget main info 
https://help.mercuryo.io/en/articles/4519473-mercuryo-widget-faq

# How to use parameters
**iframe** https://demo-widget.mercuryo.io

**redirect** https://widget.mercuryo.io/docs.html	

# How to get a dashboard	
https://partners.mercuryo.io

![img1](https://github.com/mercuryoio/api-migration-docs/blob/master/img1.png)

# How to set up a widget
My widgets → Create Partner Widget 

![img2](https://github.com/mercuryoio/api-migration-docs/blob/master/img2.png)

Domain &ndash; if Redirect https://exchange.mercuryo.io, if iFrame your domain address (without “/” at the end of the address)
(1 widget = 1 domain)

**Note:** Please make sure, there are no symbols or backspace after `https://exchange.mercuryo.io/` if you choose Redirect or after `https://yourdomain.com/` if you choose iFrame, otherwise the widget will not work properly and you’ll see `widget.mercuryo.io refused to connect` message.

Backward URL &ndash; merchant URL to which the users will return from Redirect

Callback URL &ndash; merchant server URL which listens to callbacks automatically when Mercuryo’s updates status of a transaction. 
 
# Check signature &ndash; signature wallet address

Sign Key &ndash; Callbacks signature check

When the status of a transaction changes, the partner receives a request with transaction data from us. 

You can set-up a signature check and see the X-Signature HTTP header with a signature. 

The signature can be checked by generating a hash through HMAC algorithm sha256 of the request body (JSON request) and a key that is in the partner's dashboard in the Sign Key field.

In this case, the request body must not change.

You can generate a key here, for example, https://implode.io/

`$key = '...'`;

`$json = '{...}'`;

`return hash_hmac('sha256', $json, $key)`;

# Webhooks	

These webhooks allow you to get current transaction status and include all the data.

1.  Set-up automatic webhooks to your server in a dashboard (Callback URL) 
2.  Before creating an order you should create a unique ID (max size 255 characters).
3.  Set the generated ID into the merchantTransactionId parameter (in let widgetParams) or in the URL parameter `merchant_transaction_id`.

`widget_id` &ndash; partners widget ID
`merchant_transaction_id` &ndash; generated unique ID by a partner, that was created in step 2		

**Callbacks Response Body:**

`
  
        "data": {
            "id": "04ecdb45d433f9266",
            "tx": {
                "id": null,
                "address": "3M2SzgMhtkSeLnCKyUnQrvhcxNvtfDmgCo"
            },
            "card": {
                "number": "4247"
            },
            "type": "buy",
            "user": {
                 "uuid4": "64e12501-f2fd-4e49-8984-4bef332525f5"
                "country_code": "gb"
            },
            "amount": "0.01140",
            "status": "paid",
            "currency": "BTC",
            "created_at": "2020-08-11 09:11:33",
            "updated_at": "2020-08-11 09:11:50",
            "fiat_amount": "120.00",
            "created_at_ts": 1597137093,
            "fiat_currency": "EUR",
            "updated_at_ts": 1597137110,
            "merchant_transaction_id": "9046"

`

        `"data": {
            "id": "04ecdb563c2bb0492", 
            "tx": {
                "id": "b68ca7b07c046ef85eb71285e910a29f8da539263c2b869f3515c7c0220811d4",
                "address": "3M2SzgMhtkSeLnCKyUnQrvhcxNvtfDmgCo"
            },
            "type": "withdraw",
             "user": {
                 "uuid4": "64e12501-f2fd-4e49-8984-4bef332525f5"
                "country_code": "gb"
            },
            "amount": "0.01140",
            "status": "completed",
            "currency": "BTC",
            "created_at": "2020-08-11 09:11:50",
            "updated_at": "2020-08-11 09:20:12",
            "fiat_amount": "132.27",
            "created_at_ts": 1597137110,
            "fiat_currency": "EUR",
            "updated_at_ts": 1597137612,
            "merchant_transaction_id": "9046"`

`id` &ndash; unique ID of the current event

`tx.id` &ndash; blockchain’s hash of the transaction

`tx.address` &ndash; crypto wallet address 

`user` &ndash; unique user ID


# Transaction status types 

## BUY
Per 1 transaction there are two internal operations "buy" and "withdraw"

**Type: `buy`**

`new` transaction initiated

`pending` waiting for any action from the user to continue the transaction (waiting for input 3ds or descriptor)

`cancelled` transaction cancelled (usually due to timeout of descriptor or 3ds)

`paid` transaction completed successfully (money debited from the card)

`order_failed` transaction was rejected by the issuer bank 

`order_scheduled` transaction is successful, the money is held up/frozen on the card by the bank, we are waiting for the client to pass KYC. As soon as the client passes KYC - crypto is sent to the address, if the client fails KYC - transaction is canceled within 1 hours, client’s bank returns money back to card.

`descriptor_failed` the user entered an invalid descriptor three times

**Type: `withdraw`**

`new` transaction initiated

`pending` transaction in progress

`failed` not completed successfully (this is rare)

`completed` successfully (received transaction hash)

## SELL

`new` transaction created

`pending` transaction in progress

`succeeded` successfully completed (money transferred to the card)

`failed` not completed successfully (crypto is refunded to refund_address)

## API METHODS 
			
**How to get rates.**

**rate+mercuryo fees+partners fee**

Request:
https://api.mercuryo.io/v1.6/public/rates

Example:
https://api.mercuryo.io/v1.6/public/rates?widget_id=d9d9dab5-7127-417b-92fb-478bc90916b3 


**rate+mercuryo fee**

Request:
https://api.mercuryo.io/v1.6/widget/rates/partner-fee-off

Example:
https://api.mercuryo.io/v1.6/widget/rates/partner-fee-off?widget_id=d9d9dab5-7127-417b-92fb-478bc90916b3


**clear exchange rate**

Request:
https://api.mercuryo.io/v1.6/widget/rates/fee-off

Example:
https://api.mercuryo.io/v1.6/widget/rates/fee-off?widget_id=d9d9dab5-7127-417b-92fb-478bc90916b3


**How to get transaction status**

Request:
https://api.mercuryo.io/v1.6/widget/transactions

Example:
https://api.mercuryo.io/v1.6/widget/transactions?widget_id=d9d9dab5-7127-417b-92fb-478bc90916b3&merchant_transaction_id=1234


**How to get final crypto *buy* or fiat *sell* amounts**

1. **buy** 

Request:
https://api.mercuryo.io/v1.6/public/convert

Example:
https://api.mercuryo.io/v1.6/public/convert?from=EUR&to=BTC&type=buy&amount=100&widget_id=d9d9dab5-7127-417b-92fb-478bc90916b3 


2. **sell**

Request:
https://api.mercuryo.io/v1.6/public/convert

Example:
https://api.mercuryo.io/v1.6/public/convert?from=BTC&to=EUR&type=sell&amount=0.1&widget_id=d9d9dab5-7127-417b-92fb-478bc90916b3


3.
Request:
https://api.mercuryo.io/v1.6/widget/buy/rate

Example:
https://api.mercuryo.io/v1.6/widget/buy/rate?from=USD&to=BTC&amount=100&widget_id=d9d9dab5-7127-417b-92fb-478bc90916b3


**How to get the list of supported fiat/cryptocurrencies**

https://api.mercuryo.io/v1.6/public/currencies-buy

https://api.mercuryo.io/v1.6/public/currencies-sell	


**How to get min/max limits**				

1. **buy** 
 
Request:
https://api.mercuryo.io/v1.6/public/currency-limits

Example:
https://api.mercuryo.io/v1.6/public/currency-limits?from=USD&to=BTC&widget_id=d9d9dab5-7127-417b-92fb-478bc90916b3&type=buy


2. **sell** 

Request:
https://api.mercuryo.io/v1.6/public/currency-limits

Example:
https://api.mercuryo.io/v1.6/public/currency-limits?from=USD&to=BTC&widget_id=d9d9dab5-7127-417b-92fb-478bc90916b3&type=sell	


**How to get list of supported countries**

https://api.mercuryo.io/v1.6/public/card-countries 


# Signature Wallet Address

To protect against forgery of the crypto-wallet address, you have to use a signature.

On the dashboard, in the widget’s setting, there is a section where you can enable signature verification. 

The partner sets Secret (automatically generated or manually) and sets the “Check signature“ checkbox.


![img3](https://github.com/mercuryoio/api-migration-docs/blob/master/img3.png)

When the checkbox is on, the signature and address parameters must be appended to the widget’s URL (the partner generates on its side and substitutes).

Signature is calculated using the following algorithm:

signature = sha512(address+secret), where + concatenation operation.

If the signature is invalid, the widget will not be displayed.

Signature generation example 

https://abunchofutils.com/u/computing/sha512-hash-calculator

![img4](https://github.com/mercuryoio/api-migration-docs/blob/master/img4.png)

# TESTNET (to use Mercuryo’s sandbox you should provide all your test personal/server ip’s for whitelist) 

How to use parameters

1. iframe https://demo-widget.mercuryo.io
2. redirect https://widget.mercuryo.io/docs.html		

# SANDBOX 

dashboard https://sandbox-partners.mrcr.io (if applicable)

redirect  https://sandbox-exchange.mrcr.io 

**iframe**

1. Place `<div id="mercuryo-widget"></div>` inside `<body></body>`
 
2. Put `<script src="https://sandbox-widget.mrcr.io/embed.2.0.js"></script>`

`<script>mercuryoWidget.run({widgetId: 'da128a17-d019-41f4-b80c-a615d7e0f595', host: document.getElementById('mercuryo-widget')})`;`</script>`

 in the end of the page before `</body>`
 
					
testnet `widget_id=60b69ef8-9287-49d7-8164-94d87d8982c4`

phone &ndash; you can use your own

email &ndash; you can use your own

bank card &ndash; you can use your own real visa/MasterCard bank cards for 3ds part (the fiat won't be charged and data won't be stored)


an examples of the testnet addresses

testnet BTC address &ndash; `msBE6aCaAesegu4VzbQW3L5xWBL8vi15Q7`

testnet erc-20 address &ndash; `0xA14691F9f1F851bd0c20115Ec10B25FC174371DF`


you can also get your own testnet crypto address (see III)

				
# II How to get testnet transaction status
1. On the partner side, create a unique ID (max size 255 characters).
2. Set the generated ID in in the URL parameter merchant_transaction_id. 3 Call API method
					
https://sandbox-api.mrcr.io/v1.5/widget/transactions?widget_id=60b69ef8-9287-49d7-8164-94d87d8982c4&merchant_transaction_id=1234
				
*testnet widget_id &ndash; `60b69ef8-9287-49d7-8164-94d87d8982c4` *merchant_transaction_id - generated unique id by partner that was created in the step 2
					
Response example (see 1st page)

**How to get rates.**

https://sandbox-api.mrcr.io/v1.5/public/rates?widget_id=60b69ef8-9287-49d7-8164-94d87d8982c4


**How to get the list of supported fiat/cryptocurrencies**	

https://sandbox-api.mrcr.io/v1.5/public/currencies-buy

https://sandbox-api.mrcr.io/v1.5/public/currencies-sell		


**How to get min/max limits**	

https://sandbox-api.mrcr.io/v1.5/public/currency-limits?from=USD&to=BTC&widget_id=60b69ef8-9287-49d7-8164-94d87d8982c4
					
**How to get list of supported countries**

https://sandbox-api.mrcr.io/v1.5/public/card-countries 


**How to get final crypto amount** 

https://sandbox-api.mrcr.io/v1.5/public/convert?from=EUR&to=BTC&type=buy&amount=100&widget_id=60b69ef8-9287-49d7-8164-94d87d8982c4

	
# III Check testnet transaction (if applicable)	

**BTC** &ndash; https://tbtc.bitaps.com/ (testnet)

**ETH** &ndash; https://ropsten.etherscan.io/ (ropsten) (metamask) 

