## 1. Silent Login

Silent login is a tool that makes login in Mercuryo easier for your users

## 2. Acceptance: 

1. You need to add to your Terms and Policy agreement to share user data with Mercuryo.
2. You need to make an agreement with Mercuryo that Mercuryo will use the data for registration and will use it to third parties.
3. You need to ask users to accept  the Mercurio term  in your interface.

## 3. Scheme

![scheme title](https://github.com/mercuryoio/api-migration-docs/blob/master/scheme.png)

## 4. API methods
There is two API methods: one for users that already have mercuryo account and one for new ones.

1. **Silent Login**

For already registered in Mercuryo users you need to use this API method:

Request: `POST https://api.mercuryo.io/v1.6/sdk-partner/login`


| Header  | Type  |  
| ------------- | -------------  |
| Sdk-Partner-Token | your partner key |
| **Parameter**  | **Description**  | 
| ------------- | -------------  |
| `phone` | users phone number |

Responses:

| Case  | Code  |  Response  |
| ------------- | -------------  |-------------  |
| user is register by you | 200 | `init_token` and `init_token_type` |
| user is register by other partner | 200 | `init_token` and `init_token_type` |
| invalid phone | 400 |  invalid phone |
| user is blocked by reasons: `LOCK_REASON_TOO_MANY_LOGIN_FAILURES`, `LOCK_REASON_FRAUD`, `LOCK_REASON_REFUND`, `LOCK_REASON_TOO_MANY_REQUESTS`, `LOCK_REASON_SANCTION_LIST`   | 403 | silent login forbidden |
| user is deleted `LOCK_REASON_DELETED` | 403 | silent login forbidden |
| invalid `sdk-partner-token` | 401 | wrong partner |
| user is not registered | 404 | user not found |


2. **Silent SignUp**

For users that have not Mercuryo account you need to use this API method:

Request: `POST https://api.mercuryo.io/v1.6/sdk-partner/sign-up`


| Header  | Type  |  
| ------------- | -------------  |
| Sdk-Partner-Token | obligatory |
| **Parameter**  | **Type**  | 
| ------------- | ------------- |
| **User data:**   |
| accept | obligatory | 
| phone | obligatory |  
| email | optional |

Example of Request body:

```js
{
  "accept": "true",
  "phone": "+4915207829731",
  "email": "email@google.com",
  }
 ```

| Case  | Code  |  Response  |
| ------------- | -------------  |-------------  |
| user is register by partner parameters | 200 | init_token and init_token_type |
| user is already registered | 400 | user already registered in system |
| user can not be registered  | 400 |  user \ can not be registered  |

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
## 5. How to

You need follow this steps:
1. Get `init_token` and `init_token_type` by using API Methods
2. Redirect user to `https://your_widget_url/?init_token=users_init_token&init_token_type=users_init_token_type`. 
