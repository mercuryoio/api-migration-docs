## 1. Silent Login

Silent login is a tool that makes login in Mercuryo easier for your users. 
Contact Your account manager to agree on what user data is allowed to silent sign-up for your users. 

## 2. How to

You need follow this steps:
1. Get `init_token` and `init_token_type` by using API Methods
2. Redirect user to `https://your_widget_url/?init_token=users_init_token&init_token_type=users_init_token_type`. The User needs to verify his phone\email by code that he will get on his phone\e-mail. The verification page is made on Mercuryo side
Example of redirection link: `https://exchange.mercuryo.io/?init_token=081b8291864c77282&init_token_type=sdk_partner`

**NB:**
Mercuryos main contact type is **e-mail**

## 3. Acceptance: 

1. You need to add to your Terms and Policy agreement to share user data with Mercuryo.
2. You need to make an agreement with Mercuryo that Mercuryo will use the data for registration and will use it to third parties.
3. You need to ask users to accept  the Mercuryo terms in your interface.

## 4. Partners settings

| Parameter  | Description  |  
| ------------- | -------------  |
| phone  | is users phone allowed for silent sign-up or not |
| email | is users email allowed for silent sign-up or not |
| kyc: share_token | is sharing token with SumSub allowed or not |
| kyc: photos + personal information | are users photo and personal information allowed to pass KYC |

So you can transfer Users first_name, last_name, birthday, share_token, document (type and files) if it is allowed 

**Personal info**

| Parameter  | Description  |  
| ------------- | -------------  |
| first_name | Users first name |
| last_name | Users last name |
| birthday | Users birthday |
| country | get it by users phone |

**User's document files** 
document.files must contain an array with filename as key and file content as value. For current document type requirements for list of files differs.
1. `id_card` Count of files must be 3. File names must be `face.`, `side-1.`, `side-2.`;
2. `passport` Count of files must be 2. File names must be `face.`, `side-1.`;
3. `driver_license` Count of files must be 3. File names must be `face.`, `side-1.`, `side-2.`.
Extensions in file names must correspond `.jpg`, `.png`. Files must be encrypted base64.


Common Rules: 
1. Invalid params -> error
2. Disallowed (partner settings) parameters -> disallowed parameters are ignored
3. Unacceptable parameters - unacceptable parameters are ignored  (ex. kyc:photos for US, kyc:photos without personal info)
4. Send docs is allowed only with personal data

## 5. API methods
There is two API methods: one for users that already have mercuryo account and one for new ones.

### 5.1. **Silent Login**

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
| user is register by you | 200 | `init_token` and `init_token_type` |
| user is register by other partner | 200 | `init_token` and `init_token_type` |
| invalid phone | 400 |  invalid phone |
| user is blocked by reasons: `LOCK_REASON_TOO_MANY_LOGIN_FAILURES`, `LOCK_REASON_FRAUD`, `LOCK_REASON_REFUND`, `LOCK_REASON_TOO_MANY_REQUESTS`, `LOCK_REASON_SANCTION_LIST`   | 403 | silent login forbidden |
| user is deleted `LOCK_REASON_DELETED` | 403 | silent login forbidden |
| invalid `sdk-partner-token` | 401 | wrong partner |
| user is not registered | 404 | user not found |

![img1](https://github.com/mercuryoio/api-migration-docs/blob/master/img/Silent%20Login.png)


### 5.2. **Silent SignUp**

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
| share_token | optional |
| country_code | optional |
| first_name | optional |
| last_name | optional |
| birthday | optional |
| document[files] | optional |

Example of Response body:

```
{
  “status”: 200,
  “data”: {
    “user_uuid4”: “7f6f384b-3388-479f-a26e-3a6e290f5bfe”,
    “init_token”: “0835f4b0c56de3586",
    “init_token_type”: “sdk_partner_authorization”
  }
}
 ```

| Case  | Code  |  Response  |
| ------------- | -------------  |-------------  |
| user is register by partner parameters | 200 | `init_token` and `init_token_type` |
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

## 6 Init token

`init_token` expires after first time it was passed to widget or after 1 hour.
