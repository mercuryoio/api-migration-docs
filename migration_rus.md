# Переход на 1.6
В рамках оптимизации Mercuryo переходит на версию api **1.6**. На данный момент поддерживается обратная совместимость запросов версии api **1.5**.

Для обновления версии api требуется изменить номер версии c **1.5** на **1.6**:

https://api.mercuryo.io/v1.5 -> https://api.mercuryo.io/v1.6

Большинство ответов остается без изменений.

# Обновленные ответы

Основное изменение &ndash; ответ дополнился информацией о комиссии:
1. `fee` &ndash; комиссии
2. `subtotal` &ndash; суммы без комиссии
3. `total` &ndash; итоговые суммы с комиссией
4. `rate` &ndash; курс криптовалюты на момент запроса

# /widget/buy/rate
Запрос:
https://api.mercuryo.io/v1.6/widget/buy/rate?from={fiat_currency}&to={crypto_currency}&amount={from_amount}&widget_id={widget_id}

Пример запроса:

https://api.mercuryo.io/v1.6/widget/buy/rate?from=USD&to=BTC&amount=150&widget_id=d9d9dab5-7127-417b-92fb-478bc90916b3

Пример:

![buy/rate](https://github.com/IgnatBatuev/draft1.6api/blob/main/buy_comparev3.png)
# /widget/sell/rate
Запрос:

https://api.mercuryo.io/v1.6/widget/sell/rate?from={crypto_currency}&to={fiat_currency}&amount={from_amount}3&widget_id={widget_id}

Пример запрос:

https://api.mercuryo.io/v1.6/widget/sell/rate?from=BTC&to=USD&amount=0.003&widget_id=d9d9dab5-7127-417b-92fb-478bc90916b3


Пример:

![buy/rate](https://github.com/IgnatBatuev/draft1.6api/blob/main/sell__comparev3.png)
# /public/convert
Запрос:

https://api.mercuryo.io/v1.6/public/convert?from={fiat_currency}&to={crypto_currency}&type={type}&amount={from_amount}&widget_id={widget_id}

Пример запроса:

https://api.mercuryo.io/v1.6/public/convert?from=EUR&to=BTC&type=buy&amount=100&widget_id=d9d9dab5-7127-417b-92fb-478bc90916b3

Добавленные объекты в теле ответа
1. `fee_currency` &ndash; тип выбранного фиата

Пример:

![public/convert](https://github.com/IgnatBatuev/draft1.6api/blob/main/conver_comparev3.png)
