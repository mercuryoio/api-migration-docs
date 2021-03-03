# Switching to 1.6
As part of optimization Mercuryo is switching to api version **1.6**. After the update the methods configured for the api **1.5** version will continue supported.

You need to change the version number from **1.5** to **1.6** to update:

https://api.mercuryo.io/v1.5 -> https://api.mercuryo.io/v1.6/

Most of the responses remain unchanged.

# Updated responses

The main change is that response now contains information about commission
1. `fee` &ndash; commission
2. `subtotal` &ndash; amount without commission
3. `total` &ndash; total amount with commission
4. `rate` &ndash; exchange rate of selected crypto to selected fiat

# /widget/buy/rate

Request:

https://api.mercuryo.io/v1.6/widget/buy/rate?from={fiat_currency}&to={crypto_currency}&amount={from_amount}&widget_id={widget_id}

Request example:

https://api.mercuryo.io/v1.6/widget/sell/rate?from=BTC&to=USD&amount=0.003&widget_id=d9d9dab5-7127-417b-92fb-478bc90916b3

Example:

![buy/rate](https://github.com/IgnatBatuev/draft1.6api/blob/main/buy_comparev3.png)
# /widget/sell/rate

Request:

https://api.mercuryo.io/v1.6/widget/sell/rate?from={crypto_currency}&to={fiat_currency}&amount={from_amount}3&widget_id={widget_id}

Request example:

https://api.mercuryo.io/v1.6/widget/sell/rate?from=BTC&to=USD&amount=0.003&widget_id=d9d9dab5-7127-417b-92fb-478bc90916b3

Example:

![buy/rate](https://github.com/IgnatBatuev/draft1.6api/blob/main/sell__comparev3.png)
# public / convert

Request:

https://api.mercuryo.io/v1.6/public/convert?from={fiat_currency}&to={crypto_currency}&type={type}&amount={from_amount}&widget_id={widget_id}

Request example:

https://api.mercuryo.io/v1.6/public/convert?from=EUR&to=BTC&type=buy&amount=100&widget_id=d9d9dab5-7127-417b-92fb-478bc90916b3

Added fields in response body
1. `fee_currency` &ndash; type of selected fiat

Example:

![public\convert](https://github.com/IgnatBatuev/draft1.6api/blob/main/conver_comparev3.png)
