### Blockchain-Ads Backend API DOCUMENTATION
---
#### Quick and Simple doc
---

This repository contains the documentation and additional files for the entirety of the Firebase Backend API. It is a quick description for Blockchain-Ad API endpoints defined in the ./functions/routes/API-routes.js file.
This documentation is in its infancy, and would grow as the project grows also. Nonetheless, it gives a good idea of how requests to each endpoints should be fashioned, the expected action(get, post, patch delete, etc) and response and data type following each request.

The endpoints are broken into 4 major sections:
[ STATUS ACTION || ACCOUNT ACTION || PAYMENT ACTION || CAMPAIGN ACTION ]


##### SUMMARY
- **BASE URL**: https://us-central1-web3-marketing-hub.cloudfunctions.net/api
- **HOST**: Google Firebase (GCP)
- **DB**: Firestore
- **AUTH**: Firebase Auth
- **LANGUAGE**: JS on NodeJS
- **Version**: 1.0.0 |as at| 25.11.2023

**Into the Docs:**
##### [STATUS ACTION]
---

##### (1)
**ENDPOINT:** ```/APIavailability``` </br>
**ACTION:** GET </br>
**DETAIL:** This endpoint lets the developer get a response from the API server, to know if it is alive and working fine. It responds with a single line of text to give it's availaility status. No response shows that the server is either dead or it's crashed. </br>

###### USAGE EXAMPLE(Javascript)
```javascript

var requestOptions = {
  method: 'GET',
  redirect: 'follow'
};

fetch("https://us-central1-web3-marketing-hub.cloudfunctions.net/api/APIavailability", requestOptions)
  .then(response => response.text())
  .then(result => console.log(result))
  .catch(error => console.log('error', error));

```

###### RESPONSE DATA(TEXT)
- 200 `Blockchain-Ad API Server is up and running.`
- 4xx `<Errors from incomplete request body>`
- 5xx `<try/catch error response>`

---


##### [ACCOUNT ACTIONS]
---
##### (1)
**ENDPOINT:** ```/newSignUp``` </br>
**ACTION:** POST </br>
**DETAIL:** This endpoint is for creating a new user account via the form fields collected. Once the form is filled, the frontend application sends a post request with that information to the endpoint, a new account is created, a success status us sent, and the user can then be prompted to login </br>

**REQUEST DATA(JSON):** All data in body is required for a valid request.
```
{
  organization:    (String): "This is the name for the organization from the form input",
  email:           (String): "The email address for the new user from the form input",
  password:        (String): "The user's account password from the form",
  invited:         (Boolean): "This endpoint resolves to true or false from the url parameter. If the user is invited, it is true, if the user is not invited it stays false",
  inviteOrgID:     (String): "If the Invited parameter is true, then the inviteOrgID has to contain an ID, else, the inviteORGID can be set to null",
}
```

###### USAGE EXAMPLE(Javascript)
```javascript

var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  "organization": "<organization-Name>",
  "email": "<user-email>",
  "password": "<user-password>",
  "invited": "<true|false>",
  "inviteOrgID":"<organizationID>"
});

var requestOptions = {
  method: 'POST',
  headers: myHeaders,
  body: raw,
  redirect: 'follow'
};

fetch("https://us-central1-web3-marketing-hub.cloudfunctions.net/api/createUser", requestOptions)
  .then(response => response.text())
  .then(result => console.log(result))
  .catch(error => console.log('error', error));

```

###### RESPONSE DATA(JSON)
- 200 `{res_sts: true, res_msg: User Sign-Up successful}`
- 4xx `{res_sts: false, res_msg: <Errors from incomplete request body>}`
- 5xx `{res_sts: false, res_msg: error.message}`

---

##### (2)
**ENDPOINT:** ```/completeSignUp``` </br>
**ACTION:** POST </br>
**DETAIL:** This endpoint is used in conjuction with the one click signUp like Google Sign Up and or Facebook SignUp. The logic for how this work is different, please follow me. You would need to directly use the SDK from google to setup the login process independent of this API. You can find a follow through process to that [here](https://youtu.be/vuLTzi17k14?si=2qbfGJfYgoSY86aC) and the official docs [here](https://firebase.google.com/docs/auth/web/google-signin). Once you complete that, you realize that google puts the user infomation in the browser context. At this point, the user account is half complete, to complete this, you would pull the personal information from the user document in the browser and then call this API with the required documents and complete the account signUp. You can do this without user interaction after the user signUP and when that is complete, you can redirect the user to the dashboard, logged in.</br>

**REQUEST DATA(JSON):** All data in body is required for a valid request.
```
{
  email:      (String): "This email data can be pulled from the browser context as earlier stated, it is the user's email address",
  uuid:       (String): "This is the unique ID created by Google when the account was created, it is also in the browser context, as UID",
  photoURL:   (String | URL): "The user's image on the Google shared from Google to be used as the user's avatar",
  name:       (String): "This is the name for the user, from Google",
  invited:    (boolean): "This is a boolean for handling invites, this adds a bit of complexity. To understand how to use this we might need to talk. It is used know if the user creating an account was invited or not. Invite links have queries in the url that you can read and  use in this section. If a user is invited via link, this field resolves to true, else it resolves to false",
  inviteOrgID:(String): "If the invited is true, then the organization ID they are been invited should be in here, it'll be sent in the link to be handled,
}
```

###### USAGE EXAMPLE(Javascript)
```javascript

var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");

var raw = {
  "email":"<user-email>",
  "uuid":"<user-unique-ID>", 
  "photoURL": "<profile-image-url>",
  "name": "<user name>",
  "invited": "<true|false>",
  "inviteOrgID":"<organizationID>",
  };

var requestOptions = {
  method: 'POST',
  headers: myHeaders,
  body: raw,
  redirect: 'follow'
};

fetch("https://us-central1-web3-marketing-hub.cloudfunctions.net/api/completeSIgnUp", requestOptions)
  .then(response => response.text())
  .then(result => console.log(result))
  .catch(error => console.log('error', error));

```

###### RESPONSE DATA(JSON)
- 200 `{res_sts: true, res_msg: Information update successfull}`
- 4xx `{res_sts: false, res_msg: <Errors from incomplete request body>}`
- 5xx `{res_sts: false, res_msg: error.message}`

---

##### (3)
**ENDPOINT:** ```/loadUserProfile``` </br>
**ACTION:** POST </br>
**DETAIL:** This endpoint is for getting user information during the login process. Once a user logs in, either via the form or via one click sign In, You use the google AUTH SDK for that, you can read the documentation [here](https://firebase.google.com/docs/auth/web/password-auth), once you get a successful response from the call to Google, you then call this endpoint, to pull all the user's information from the database and render it on the dashboard page.</br>

**REQUEST DATA(JSON):** All data in body is required for a valid request.
```
{
  uuid:    (String): "All that is required is the uuid to identify the user in the DB, the Firebase SDK returns that, so you can call the endpoint with the response from the SDK",
}
```

###### USAGE EXAMPLE(Javascript)
```javascript

var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  "uuid": "<uuid>"
});

var requestOptions = {
  method: 'POST',
  headers: myHeaders,
  body: raw,
  redirect: 'follow'
};

fetch("https://us-central1-web3-marketing-hub.cloudfunctions.net/api/loadUserProfile", requestOptions)
  .then(response => response.text())
  .then(result => console.log(result))
  .catch(error => console.log('error', error));

```

###### RESPONSE DATA(JSON)
- 200 `{res_sts: true, res_msg: User Sign-Up successful}`
- 4xx `{res_sts: false, res_msg: <Errors from incomplete request body>}`
- 5xx `{res_sts: false, res_msg: error.message}`

---

##### (4)
**ENDPOINT:** ```/switchOrganization``` </br>
**ACTION:** POST </br>
**DETAIL:** This endpoint is used for switching the user's organization while logged in. To switch the organization, they make a call to the endpoint with the organization's ID they want to switch to and also their own ID to ensure they are logged in. </br>

**REQUEST DATA(JSON):** All data in body is required for a valid request.
```
{
  uuid:          (String): "All that is required is the uuid to identify the user in the DB, the Firebase SDK returns that, so you can call the endpoint with the response from the SDK",
  organizationID:(String): ID for the organization. This is loaded in when a user is logged in, you can then make it the value of the drop down for the organization selection. When a user select to switch an organization, it picks up that organization id. 
}
```

###### USAGE EXAMPLE(Javascript)
```javascript

var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  "uuid": "<User unique ID>",
  "organizationID": "<organization-ID>",
});

var requestOptions = {
  method: 'POST',
  headers: myHeaders,
  body: raw,
  redirect: 'follow'
};

fetch("https://us-central1-web3-marketing-hub.cloudfunctions.net/api/loadUserProfile", requestOptions)
  .then(response => response.text())
  .then(result => console.log(result))
  .catch(error => console.log('error', error));

```

###### RESPONSE DATA(TEXT)
- 200 `Blockchain-Ad API Server is up and running.`
- 4xx `<Errors from incomplete request body>`
- 5xx `<try/catch error response>`

---

##### (5)
**ENDPOINT:** ```/createNewOrganization``` </br>
**ACTION:** POST </br>
**DETAIL:** This endpoint is used for creating a new Organization and adding that individual as the admin and member of the organization</br>

**REQUEST DATA(JSON):** All data in body is required for a valid request.
```
{
  uuid:            (String): "All that is required is the uuid to identify the user in the DB, the Firebase SDK returns that, so you can call the endpoint with the response from the SDK",
  organizationName:(String): Name for the organization during creation. 
}
```

###### USAGE EXAMPLE(Javascript)
```javascript

var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  "uuid": "<User unique ID>",
  "organizationName": "<organization-Name>",
});

var requestOptions = {
  method: 'POST',
  headers: myHeaders,
  body: raw,
  redirect: 'follow'
};

fetch("https://us-central1-web3-marketing-hub.cloudfunctions.net/api/loadUserProfile", requestOptions)
  .then(response => response.text())
  .then(result => console.log(result))
  .catch(error => console.log('error', error));

```

###### RESPONSE DATA(TEXT)
- 200 `Blockchain-Ad API Server is up and running.`
- 4xx `<Errors from incomplete request body>`
- 5xx `<try/catch error response>`

---


##### (6)
**ENDPOINT:** ```/updateUserInfo``` </br>
**ACTION:** POST </br>
**DETAIL:** This endpoint is used for the user to update their information. It can update the user's information on the profile page.</br>

**REQUEST DATA(JSON):** All data in the main body is required for a valid request, but data in the info body is optional, but there should at least be one.
```
{
  uuid:            (String): "All that is required is the uuid to identify the user in the DB, the Firebase SDK returns that, so you can call the endpoint with the response from the SDK",
  info:            (object):{
                              "name": "The user's new name for update",
                              "email": "The user's new email for the update",
                            }
}
```

###### USAGE EXAMPLE(Javascript)
```javascript

var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  "uuid": "<User unique ID>",
  "info": "{"name": "<user-name>", "email":"user-email", "phone_number":"<user-number>"}"
});

var requestOptions = {
  method: 'POST',
  headers: myHeaders,
  body: raw,
  redirect: 'follow'
};

fetch("https://us-central1-web3-marketing-hub.cloudfunctions.net/api/loadUserProfile", requestOptions)
  .then(response => response.text())
  .then(result => console.log(result))
  .catch(error => console.log('error', error));

```

###### RESPONSE DATA(TEXT)
- 200 `Blockchain-Ad API Server is up and running.`
- 4xx `<Errors from incomplete request body>`
- 5xx `<try/catch error response>`

---

##### (7)
**ENDPOINT:** ```/inviteUser``` </br>
**ACTION:** POST </br>
**DETAIL:** This endpoint is used for the only Admins/creators of an organization to invite others into the organization</br>

**REQUEST DATA(JSON):** All data in the  body is required for a valid request.
```
{
  uuid:             (String): "All that is required is the uuid to identify the user in the DB, the Firebase SDK returns that, so you can call the endpoint with the response from the SDK",
  organizationID:   (String): "ID for the organization the new user is being invited into",
  senderName:       (String): "This is the name of the sender, picked up from the user object returned in the SDK",
  inviteeEmail:     (String): "The email for the user being invited into the organization"
}
```

###### USAGE EXAMPLE(Javascript)
```javascript

var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  "uuid": "<User unique ID>",
  "organizationID": "<organization-ID>",
  "senderName": "<sender-name>",
  "inviteeEmail": "<The invited email>"
});

var requestOptions = {
  method: 'POST',
  headers: myHeaders,
  body: raw,
  redirect: 'follow'
};

fetch("https://us-central1-web3-marketing-hub.cloudfunctions.net/api/loadUserProfile", requestOptions)
  .then(response => response.text())
  .then(result => console.log(result))
  .catch(error => console.log('error', error));

```

###### RESPONSE DATA(TEXT)
- 200 `Blockchain-Ad API Server is up and running.`
- 4xx `<Errors from incomplete request body>`
- 5xx `<try/catch error response>`

---

##### [PAYMENT ACTIONS]
---
##### (1)
**ENDPOINT:** ```/getAllCurrencies```</br>
**ACTION:** GET</br>
**DETAIL:** This is a method to obtain detailed information about all cryptocurrencies available for payments. The frontend can then use the response as a dropdown menu when a user needs to pay, to let them choose which cryptocurrency to pay with.</br>

###### USAGE EXAMPLE(Javascript)
```javascript

var requestOptions = {
  method: 'GET',
  redirect: 'follow'
};

fetch("https://us-central1-web3-marketing-hub.cloudfunctions.net/api/getAllCurrencies", requestOptions)
  .then(response => response.text())
  .then(result => console.log(result))
  .catch(error => console.log('error', error));

```

###### RESPONSE DATA(JSON)
- 200 (Response Example)
```json
{
    [
        {
            "id": 169,
            "code": "1INCH",
            "name": "1inch Network",
            "enable": true,
            "wallet_regex": "^(0x)[0-9A-Fa-f]{40}$",
            "priority": 168,
            "extra_id_exists": false,
            "extra_id_regex": null,
            "logo_url": "/images/coins/1inch.svg",
            "track": true,
            "cg_id": "1inch",
            "is_maxlimit": false,
            "network": "eth",
            "smart_contract": "0x111111111117dc0aa78b770fa6a738034120c302",
            "network_precision": "18",
            "explorer_link_hash": null,
            "precision": 8,
            "ticker": "1inch",
            "is_defi": false,
            "is_popular": false,
            "is_stable": false,
            "available_for_to_conversion": true
        },
        {
            "id": 172,
            "code": "1INCHBSC",
            "name": "1Inch Network (BSC)",
            "enable": true,
            "wallet_regex": "^(0x)[0-9A-Fa-f]{40}$",
            "priority": 171,
            "extra_id_exists": false,
            "extra_id_regex": null,
            "logo_url": "/images/coins/1inchbsc.svg",
            "track": true,
            "cg_id": "1inch",
            "is_maxlimit": false,
            "network": "bsc",
            "smart_contract": "0x111111111117dc0aa78b770fa6a738034120c302",
            "network_precision": "18",
            "explorer_link_hash": null,
            "precision": 8,
            "ticker": "1inch",
            "is_defi": false,
            "is_popular": false,
            "is_stable": false,
            "available_for_to_conversion": true
        },
        {
            "id": 121,
            "code": "AAVE",
            "name": "Aave",
            "enable": true,
            "wallet_regex": "^(0x)[0-9A-Fa-f]{40}$",
            "priority": 127,
            "extra_id_exists": false,
            "extra_id_regex": null,
            "logo_url": "/images/coins/aave.svg",
            "track": true,
            "cg_id": "aave",
            "is_maxlimit": false,
            "network": "eth",
            "smart_contract": "0x7Fc66500c84A76Ad7e9c93437bFc5Ac33E2DDaE9",
            "network_precision": "18",
            "explorer_link_hash": "https://eth-explorer.nownodes.io/tx/:hash",
            "precision": 8,
            "ticker": "aave",
            "is_defi": false,
            "is_popular": false,
            "is_stable": false,
            "available_for_to_conversion": true
        },
        {
            "id": 21,
            "code": "ADA",
            "name": "Cardano",
            "enable": true,
            "wallet_regex": "^([1-9A-HJ-NP-Za-km-z]{59,104})|([0-9A-Za-z]{58,104})$",
            "priority": 21,
            "extra_id_exists": false,
            "extra_id_regex": null,
            "logo_url": "/images/coins/ada.svg",
            "track": false,
            "cg_id": "cardano",
            "is_maxlimit": false,
            "network": "ada",
            "smart_contract": null,
            "network_precision": "6",
            "explorer_link_hash": null,
            "precision": 8,
            "ticker": "ada",
            "is_defi": false,
            "is_popular": false,
            "is_stable": false,
            "available_for_to_conversion": true
        },
        {
            "id": 48,
            "code": "AE",
            "name": "AE",
            "enable": true,
            "wallet_regex": "^ak_[A-Za-z0-9]{49,52}$",
            "priority": 15,
            "extra_id_exists": false,
            "extra_id_regex": null,
            "logo_url": "/images/coins/ae.svg",
            "track": true,
            "cg_id": "aeternity",
            "is_maxlimit": false,
            "network": null,
            "smart_contract": null,
            "network_precision": null,
            "explorer_link_hash": null,
            "precision": 8,
            "ticker": null,
            "is_defi": false,
            "is_popular": false,
            "is_stable": false,
            "available_for_to_conversion": true
        }
    ]
}
```
- 4xx `<Errors from incomplete request body>`
- 5xx `<try/catch error response>`

---

---
##### (2)
**ENDPOINT:** ```/paymentOnSite```</br>
**ACTION:** POST</br>
**DETAIL:** This endpoint help users create and make payment. With this method, the customer will be able to complete the payment without leaving the website.</br>
**REQUEST DATA(JSON):** All data in body is required for a valid request.
```
{
  price_amount:        (number): "This is the fiat equivalent of the price to be paid in crypto in USD",
  pay_currency:        (string): "The crypto currency in which the pay_amount is specified (btc, eth, etc)",
  uuid:                (String): "The unique ID for the user making the payment",
}
```

###### USAGE EXAMPLE(Javascript)
```javascript

var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  "price_amount": 400,
  "pay_currency": "ltc",
  "uuid": "<uuid>"
});

var requestOptions = {
  method: 'POST',
  headers: myHeaders,
  body: raw,
  redirect: 'follow'
};

fetch("https://us-central1-web3-marketing-hub.cloudfunctions.net/api/paymentOnSite", requestOptions)
  .then(response => response.text())
  .then(result => console.log(result))
  .catch(error => console.log('error', error));
```

###### RESPONSE DATA(JSON)
- 200 (Response Example)
```json
{
    "payment_id": "6440115313",
    "payment_status": "waiting",
    "pay_address": "MS3qL8Fe9BWxjp9qKK6Lrm7jkthLPTGQsj",
    "price_amount": 400,
    "price_currency": "usd",
    "pay_amount": 5.58529026,
    "amount_received": 1.6942116,
    "pay_currency": "ltc",
    "order_id": null,
    "order_description": "Payment for Blockchain-Ads Platform",
    "payin_extra_id": null,
    "ipn_callback_url": "https://nowpayments.io",
    "created_at": "2023-11-25T18:31:06.986Z",
    "updated_at": "2023-11-25T18:31:06.986Z",
    "purchase_id": "6382908424",
    "smart_contract": null,
    "network": "ltc",
    "network_precision": null,
    "time_limit": null,
    "burning_percent": null,
    "expiration_estimate_date": "2023-11-25T18:51:06.986Z",
    "is_fixed_rate": false,
    "is_fee_paid_by_user": false,
    "valid_until": "2023-12-02T18:31:06.986Z",
    "type": "crypto2crypto"
}
```
- 4xx `<Errors from incomplete request body>`
- 5xx `<try/catch error response>`
---

---
##### (3)
**ENDPOINT:** ```/paymentWithInvoice```</br>
**ACTION:** POST</br>
**DETAIL:** This endpoint creates a payment link. With this method, the customer is required to follow the generated url to complete their payment.</br>
**REQUEST DATA(JSON):** All data in body is required for a valid request.
```
{
  price_amount:        (number): This is the fiat equivalent of the price to be paid in crypto in USD,
  uuid:              (String): "The unique ID for the user making the payment",
}
```

###### USAGE EXAMPLE(Javascript)
```javascript

var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  "price_amount": 1000
  "uuid": "<uuid>"
});

var requestOptions = {
  method: 'POST',
  headers: myHeaders,
  body: raw,
  redirect: 'follow'
};

fetch("https://us-central1-web3-marketing-hub.cloudfunctions.net/api/paymentWithInvoice", requestOptions)
  .then(response => response.text())
  .then(result => console.log(result))
  .catch(error => console.log('error', error));
```

###### RESPONSE DATA(JSON)
- 200 (Response Example)
```json
{
    "id": "5612340952",
    "token_id": "5724521151",
    "order_id": null,
    "order_description": "Payment for Blockchain-Ads Platform",
    "price_amount": "1000",
    "price_currency": "USD",
    "pay_currency": null,
    "ipn_callback_url": "https://nowpayments.io",
    "invoice_url": "https://nowpayments.io/payment/?iid=5612340952",
    "success_url": "https://nowpayments.io",
    "cancel_url": "https://nowpayments.io",
    "partially_paid_url": null,
    "payout_currency": null,
    "created_at": "2023-11-25T18:31:35.725Z",
    "updated_at": "2023-11-25T18:31:35.725Z",
    "is_fixed_rate": false,
    "is_fee_paid_by_user": false
}
```
- 4xx `<Errors from incomplete request body>`
- 5xx `<try/catch error response>`
---

---
##### (3)
**ENDPOINT:** ```/checkPaymentStatus```</br>
**ACTION:** GET</br>
**DETAIL:** This endpoint is used to get information about a payment.</br>
**REQUEST DATA(JSON):** All data in body is required for a valid request.
```
{
  paymentId:        (string): Unique ID corresponding to a valid user's payment transaction,
}
```

###### USAGE EXAMPLE(Javascript)
```javascript

var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  "paymentId": 6366009964
});

var requestOptions = {
  method: 'GET',
  headers: myHeaders,
  body: raw,
  redirect: 'follow'
};

fetch("https://us-central1-web3-marketing-hub.cloudfunctions.net/api/checkPaymentStatus", requestOptions)
  .then(response => response.text())
  .then(result => console.log(result))
  .catch(error => console.log('error', error));
```

###### RESPONSE DATA(JSON)
- 200 (Response Example)
```json

{
    "payment_id": 6366009964,
    "invoice_id": null,
    "payment_status": "waiting",
    "pay_address": "MWy3P5s2GSUWnsYr33bukhWFb1UxQAnq7V",
    "payin_extra_id": null,
    "price_amount": 400,
    "price_currency": "usd",
    "pay_amount": 5.72554718,
    "actually_paid": 0,
    "pay_currency": "ltc",
    "order_id": null,
    "order_description": "Payment for Blockchain-Ads Platform",
    "purchase_id": 5158196419,
    "outcome_amount": 1.6153341,
    "outcome_currency": "bnbbsc",
    "payout_hash": null,
    "payin_hash": null,
    "created_at": "2023-11-19T10:15:56.853Z",
    "updated_at": "2023-11-25T20:51:46.957Z",
    "burning_percent": "null",
    "type": "crypto2crypto"
}

```
- 4xx `<Errors from incomplete request body>`
- 5xx `<try/catch error response>`

---

##### [CAMPAIGN ACTIONS]
---

##### (1)
**ENDPOINT:** ```/createNewCampaign``` </br>
**ACTION:** POST </br>
**DETAIL:** This endpoint creates a new campaign for user. It does validity check on organization balance before allowing a campaign get created. The minimum price allowed for a  campaign is USD 300.</br>

**REQUEST DATA(JSON):** All data in body is required for a valid request.
```
{
  uuid:                (String): "The unique ID for the user making the payment",
  campaignInfo:        (Object): "This is an object that contains the information required for creating a valid campaign":
                      {
                        campaignName:(String): Name of the Campaign being created,
                        targetURL:(String): The url that ad clicker would be sent to,
                        startDate:(String): Day for the advert to start showing in dd/mm/yyyy format,
                        budget:(Number): The budget amount for campaign,
                        maxCPC:(Number): This is the price a user is willing to pay for a click on an advert shown,
                        timezone:(Number): The timezone for the advert to be shown,
                        smartContractAddress:(String): The smart contract address of the organization being advertised, 
                        chainTargeting:(String): The blockchain network being targeted,
                        operatingSystem:(Array): The operating system option wanted by the advertiser for their ads to be shown on. Example: ["mobile", "desktop"],
                        deviceType:(Array): The type of devices the advert should be shown to. Example:[ "ios", "android", "window", "linux", "mac_os"]
                      }
  creatives:          (Array): "This is an array that contains creative objects, the creative  objects are the advert images and title used in the campaign, it can contain multiple creative objects":
                      [
                        {
                          image:(String) "base 64 image string, that conforms to the propellerAds standard"
                          title:(String): The title of the creative being created,
                          status:(Number): This defaults to 1,
                        }
                      ]
}
```

###### USAGE EXAMPLE(Javascript)
```javascript

var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  "uuid": "3uzjwJi3vceUgqKD3HFaL8IQI443",
  "campaignInfo": {
    "campaignName": "API Test Campaign",
    "targetURL": "https://abc.xyz",
    "startDate": "",
    "budget": "500",
    "maxCPC": "0.25",
    "timezone": 3,
    "smartContractAddress": "0x0000000000000000000000000000000000001000",
    "chainTargeting": "BSCSCAN",
    "operatingSystem": [],
    "deviceType": []
  },
  "creatives": [
    {
      "image": "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAewAAAFIAQMAAACoaV/bAAAAAXNSR0IB2cksfwAAAAlwSFlzAAALEwAACxMBAJqcGAAAAANQTFRF////p8QbyAAAACtJREFUeJztwYEAAAAAw6D5U1/gCFUBAAAAAAAAAAAAAAAAAAAAAAAAAHwDULgAAVNxxnoAAAAASUVORK5CYII=",
      "title": "API creative title push",
      "status": 1
    }
  ]
});

var requestOptions = {
  method: 'POST',
  headers: myHeaders,
  body: raw,
  redirect: 'follow'
};

fetch("https://us-central1-web3-marketing-hub.cloudfunctions.net/api/createNewCampaign", requestOptions)
  .then(response => response.text())
  .then(result => console.log(result))
  .catch(error => console.log('error', error));

```

###### RESPONSE DATA(JSON)
- 200 `{res_sts: true, res_msg: Campaign created successfully}`
- 4xx `{res_sts: false, res_msg: <Errors from incomplete request body>}`
- 5xx `{res_sts: false, res_msg: error.message}`

---

##### (2)
**ENDPOINT:** ```/viewCampaignInfo``` </br>
**ACTION:** POST </br>
**DETAIL:** This endpoint retrieves info about a campaign with its ID.</br>

**REQUEST DATA(JSON):** All data in body is required for a valid request.
```
{
  uuid:                (String): "The unique ID for the user making the payment",
  campaignId:          (Number): "The ID for the campaign to be viewed",
}
```

###### USAGE EXAMPLE(Javascript)
```javascript

var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  "uuid": "3uzjwJi3vceUgqKD3HFaL8IQI443",
  "campaignId": 7736830
});

var requestOptions = {
  method: 'POST',
  headers: myHeaders,
  body: raw,
  redirect: 'follow'
};

fetch("https://us-central1-web3-marketing-hub.cloudfunctions.net/api/viewCampaignInfo", requestOptions)
  .then(response => response.text())
  .then(result => console.log(result))
  .catch(error => console.log('error', error));
```

###### RESPONSE DATA(JSON)
- 200 `{res_sts: true, res_msg: Campaign data retrieved successfully, data}`
- 4xx `{res_sts: false, res_msg: <Errors from incomplete request body>}`
- 5xx `{res_sts: false, res_msg: error.message}`

---

##### (3)
**ENDPOINT:** ```/updateCampaignInfo``` </br>
**ACTION:** POST </br>
**DETAIL:** This endpoint updates a campaign info/data via its ID, with the new info/data.</br>

**REQUEST DATA(JSON):** All data in body is required for a valid request.
```
{
  uuid:                (String): "The unique ID for the user making the payment",
  campaignId:          (Number): "The ID for the campaign to be viewed",
  creatorEmail:        (String): "This is the email of the creator of the campaign",
  newCampaignInfo:     (Object): "This is the object that contains the new campaign Info"":
                               {
                                  name:(String): "New Campaign Name",
                                  limit_total_amount:(Number): "New Campaign Total Limit"
                               }
}
```

###### USAGE EXAMPLE(Javascript)
```javascript
var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  "uuid": "456785yuj9787",
  "campaignId": 7737034,
  "creatorEmail": "abMan@gmail.com",
  "newCampaignInfo": {
    "name": "Hookah Test",
    "limit_total_amount": 500
  }
});

var requestOptions = {
  method: 'POST',
  headers: myHeaders,
  body: raw,
  redirect: 'follow'
};

fetch("https://us-central1-web3-marketing-hub.cloudfunctions.net/api/updateCampaignInfo", requestOptions)
  .then(response => response.text())
  .then(result => console.log(result))
  .catch(error => console.log('error', error));
```

###### RESPONSE DATA(JSON)
- 200 `{res_sts: true, res_msg: Campaign update successful}`
- 4xx `{res_sts: false, res_msg: <Errors from incomplete request body>}`
- 5xx `{res_sts: false, res_msg: error.message}`

---

##### (4)
**ENDPOINT:** ```/playOrPauseCampaign``` </br>
**ACTION:** POST </br>
**DETAIL:** This endpoint updates a campaign info/data via its ID, with the new info/data.</br>

**REQUEST DATA(JSON):** All data in body is required for a valid request.
```
{
  uuid:                (String): "The unique ID for the user making the payment",
  campaignId:          (Number): "The ID for the campaign to be viewed",
  toPlayCampaign:      (Boolean): "To stop campaign, set to false, to start, set to true"
}

```

###### USAGE EXAMPLE(Javascript)
```javascript
var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  "uuid": "456785yuj9787",
  "creativeId": 19823539,
  "toPlayCreative": false
});

var requestOptions = {
  method: 'POST',
  headers: myHeaders,
  body: raw,
  redirect: 'follow'
};

fetch("https://us-central1-web3-marketing-hub.cloudfunctions.net/api/startOrStopCreative", requestOptions)
  .then(response => response.text())
  .then(result => console.log(result))
  .catch(error => console.log('error', error));

```

###### RESPONSE DATA(JSON)
- 200 `{res_sts: true, res_msg: Campaign Started/Stopped successfully}`
- 4xx `{res_sts: false, res_msg: <Errors from incomplete request body>}`
- 5xx `{res_sts: false, res_msg: error.message}`

---

##### (5)
**ENDPOINT:** ```/viewCampaignStats``` </br>
**ACTION:** POST </br>
**DETAIL:** This endpoint updates a campaign info/data via its ID, with the new info/data.</br>

**REQUEST DATA(JSON):** All data in body is required for a valid request.
```
{
  uuid:                (String): "The unique ID for the user making the payment",
  day_from:            (String): "This defines which day the collacted statistics data should start from in the format dd-mm-yyyy"
  campaignId:          (Array): "An array containing the ID for the campaign to be viewed",
}

```

###### USAGE EXAMPLE(Javascript)
```javascript
var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  "uuid": "jhbjsfhsdj9989",
  "day_from": "01/01/2023",
  "campaignId": [7730229]
});

var requestOptions = {
  method: 'POST',
  headers: myHeaders,
  body: raw,
  redirect: 'follow'
};

fetch("https://us-central1-web3-marketing-hub.cloudfunctions.net/api/viewCampaignStats", requestOptions)
  .then(response => response.text())
  .then(result => console.log(result))
  .catch(error => console.log('error', error));
```

###### RESPONSE DATA(JSON)
- 200 `{res_sts: true, res_msg: Campaign statistics retrieved successfully, data}`
- 4xx `{res_sts: false, res_msg: <Errors from incomplete request body>}`
- 5xx `{res_sts: false, res_msg: error.message}`

---

##### (6)
**ENDPOINT:** ```/updateURL``` </br>
**ACTION:** POST </br>
**DETAIL:** This endpoint updates a campaign info/data target URL, with a new URL, the URL is where viewers of the Ads are directed to, when they click.</br>

**REQUEST DATA(JSON):** All data in body is required for a valid request.
```
{
  uuid:                (String): "The unique ID for the user making the payment",
  campaignId:          (Number): "The ID for the campaign to be viewed",
  newTargetUrl:        (String): "The new target URL to update to"
}

```

###### USAGE EXAMPLE(Javascript)
```javascript
var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  "uuid": "456785yuj9787",
  "campaignId": 7737040,
  "newTargetURL": "https://abcabc.xyz"
});

var requestOptions = {
  method: 'POST',
  headers: myHeaders,
  body: raw,
  redirect: 'follow'
};

fetch("https://us-central1-web3-marketing-hub.cloudfunctions.net/api/updateURL", requestOptions)
  .then(response => response.text())
  .then(result => console.log(result))
  .catch(error => console.log('error', error));
```

###### RESPONSE DATA(JSON)
- 200 `{res_sts: true, res_msg: Campaign statistics retrieved successfully, data}`
- 4xx `{res_sts: false, res_msg: <Errors from incomplete request body>}`
- 5xx `{res_sts: false, res_msg: error.message}`

---

##### (7)
**ENDPOINT:** ```/addNewCreative``` </br>
**ACTION:** POST </br>
**DETAIL:** This endpoint updates a campaign info/data target URL, with a new URL, the URL is where viewers of the Ads are directed to, when they click.</br>

**REQUEST DATA(JSON):** All data in body is required for a valid request.
```
{
  uuid:               (String): "The unique ID for the user making the payment",
  campaignId:        (Number): "The ID for the campaign to be viewed",
  creative:          (Array): "This is an array that contains creative objects, the creative  objects are the advert images and title used in the campaign, it can contain multiple creative objects":
                      [
                        {
                          image:(String) "base 64 image string, that conforms to the propellerAds standard"
                          title:(String): The title of the creative being created,
                        }
                      ]
}

```

###### USAGE EXAMPLE(Javascript)
```javascript
var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  "uuid": "456785yuj9787",
  "campaignId": 7737040,
  "creatives": [
    {
      "title": " Test API Title",
      "image": "data:image/jpeg;base64,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"
    }
  ]
});

var requestOptions = {
  method: 'POST',
  headers: myHeaders,
  body: raw,
  redirect: 'follow'
};

fetch("https://us-central1-web3-marketing-hub.cloudfunctions.net/api/addNewCreative", requestOptions)
  .then(response => response.text())
  .then(result => console.log(result))
  .catch(error => console.log('error', error));
```

###### RESPONSE DATA(JSON)
- 200 `{res_sts: true, res_msg: Creative(s) added successfully, data}`
- 4xx `{res_sts: false, res_msg: <Errors from incomplete request body>}`
- 5xx `{res_sts: false, res_msg: error.message}`

---

##### (8)
**ENDPOINT:** ```/startOrStopCreative``` </br>
**ACTION:** POST </br>
**DETAIL:** This endpoint updates/edits an already created creative.</br>

**REQUEST DATA(JSON):** All data in body is required for a valid request.
```
{
  uuid:                (String): "The unique ID for the user making the payment",
  creativeId:          (Number): "The ID for the campaign to be edited/updated",
  toPlayCreative:      (Boolean): "To stop creative, set to false, to start, set to true"
}

```

###### USAGE EXAMPLE(Javascript)
```javascript
var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  "uuid": "456785yuj9787",
  "creativeId": 19819692,
  "toPlayCreative": true
});

var requestOptions = {
  method: 'POST',
  headers: myHeaders,
  body: raw,
  redirect: 'follow'
};

fetch("https://us-central1-web3-marketing-hub.cloudfunctions.net/api/startOrStopCreative", requestOptions)
  .then(response => response.text())
  .then(result => console.log(result))
  .catch(error => console.log('error', error));
```

###### RESPONSE DATA(JSON)
- 200 `{res_sts: true, res_msg: Campaign Started/Stopped successfully}`
- 4xx `{res_sts: false, res_msg: <Errors from incomplete request body>}`
- 5xx `{res_sts: false, res_msg: error.message}`

---
