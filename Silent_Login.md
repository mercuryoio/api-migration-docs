## 1. Silent Login

Silent login is a tool that makes login in Mercuryo easier for your users. 

## 2. Acceptance: 

1. You need to add to your Terms and Policy agreement to share user data with Mercuryo.
2. You need to make an agreement with Mercuryo that Mercuryo will use the data for registration and will use it to third parties.
3. You need to ask users to accept  the Mercurio term  in your interface.
4. You need to check and set up settings in partner admin panel: witch data are is allowed for silent sign-up for you. 

### 3. Partners settings

| Parameter  | Description  |  
| ------------- | -------------  |
| phone  | is users phone allowed for silent sign-up or not |
| email | is users email allowed for silent sign-up or not |
| kyc: share-token | is sharing token with SumSub allowed or not |
| kyc: photos + personal information | are users photo and peronal information allowed to pass KYC |

Common Rules: 
1. Invalid params -> error
2. Disallowed (partner settings) parameters -> disallowed parameters are ignored
3. Unacceptable parameters - unacceptable parameters are ignored  (ex. kyc:photos for US, kyc:photos without personal info…) 


## 3. Scheme

![scheme title](https://github.com/mercuryoio/api-migration-docs/blob/master/scheme.png)

## 4. API methods
There is two API methods: one for users that already have mercuryo account and one for new ones.

### 1. **Silent Login**

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

![img1](https://github.com/mercuryoio/api-migration-docs/blob/master/img/Silent%20Login.png)


### 2. **Silent SignUp**

#### **Flow:**

**1. Check partner settings:** 

Witch data  is allowed for silent sign-up for this partner. Check it in partners admin panel. Unchecked params are ignored

**2. Check contacts:**

**Possible cases:**

* No `email **AND** No `phone`, or 1 of the params is invalid -> error
* `email **OR** `phone` exists -> error
* `email` is valid/`phone` is valid  *or* `email is valid/No `phone` *or* No `email/`phone` is valid -> new user is created

**3. Check kyc:photos + Personal information**

**Possible cases:**

* Personal information without kyc:photos is ignored
* Kyc:photos without Personal information is ignored
* Kyc:photos and kyc:share-token not empty -> error

Otherwise set kyc status true without checking

**4. Check country:**

**NB:**

For US user kyc:photos is ignored

**5. Check kyc:share-token** 

**Possible cases:**
* SumSub didn’t accept kyc:share-token -> error
* kyc:photos and kyc:share-token not empty -> error

Otherwise set kyc status from SumSub

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
![img2](https://github.com/mercuryoio/api-migration-docs/blob/master/img/Silent%20Sign%20up.png)

## 5. How to

You need follow this steps:
1. Get `init_token` and `init_token_type` by using API Methods
2. Redirect user to `https://your_widget_url/?init_token=users_init_token&init_token_type=users_init_token_type`. The User needs to verify his phone\email by code that he will get on his phone\e-mail. The verification page is made on Mercuryo side

**NB:**
Mercuryos main contact type is **e-mail**
