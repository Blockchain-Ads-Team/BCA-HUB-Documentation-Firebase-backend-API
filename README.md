### Blockchain-Ads Backend API DOCUMENTATION
---
#### Quick and Simple doc
---

This repository contains the documentation and additional files for the entirety of the Firebase Backend API. It is a quick description for Blockchain-Ad API endpoints defined in the ./functions/routes/API-routes.js file.
This documentation is in its infancy, and would grow as the project grows also. Nonetheless, it gives a good idea of how requests to each endpoints should be fashioned, the expected action(get, post, patch delete, etc) and response and data type following each request.

The endpoints are broken into 3 major sections:
[ PAYMENT ACTION || CAMPAIGN MANAGMENT || ACCOUNT ACTION ]


##### SUMMARY
- **BASE URL**: https://us-central1-bca-app-c8ef0.cloudfunctions.net/app/
- **HOST**: Google Firebase (GCP)
- **DB**: Firestore
- **AUTH**: Firebase Auth
- **LANGUAGE**: JS on NodeJS
- **Version**: 1.0.0 |as at| 25.11.2023

**Into the Docs:**
##### [PAYMENT ACTIONS]
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

fetch("https://us-central1-bca-app-c8ef0.cloudfunctions.net/app/APIavailability", requestOptions)
  .then(response => response.text())
  .then(result => console.log(result))
  .catch(error => console.log('error', error));

```

###### RESPONSE DATA(TEXT)
- 200 `Blockchain-Ad API Server is up and running.`
- 4xx `<Errors from incomplete request body>`
- 5xx `<try/catch error response>`

---

---
##### (2)
**ENDPOINT:** ```/getAllCurrencies```</br>
**ACTION:** GET</br>
**DETAIL:** This is a method to obtain detailed information about all cryptocurrencies available for payments. The frontend can then use the response as a dropdown menu when a user needs to pay, to let them choose which cryptocurrency to pay with.</br>

###### USAGE EXAMPLE(Javascript)
```javascript

var requestOptions = {
  method: 'GET',
  redirect: 'follow'
};

fetch("https://us-central1-bca-app-c8ef0.cloudfunctions.net/app/getAllCurrencies", requestOptions)
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
##### (3)
**ENDPOINT:** ```/paymentOnSite```</br>
**ACTION:** POST</br>
**DETAIL:** This endpoint help users create and make payment. With this method, the customer will be able to complete the payment without leaving the website.</br>
**REQUEST DATA(JSON):** All data in body is required for a valid request.
```
{
  price_amount:        (number): "This is the fiat equivalent of the price to be paid in crypto in USD",
  pay_currency:        (string): "The crypto currency in which the pay_amount is specified (btc, eth, etc)",
}
```

###### USAGE EXAMPLE(Javascript)
```javascript

var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  "price_amount": 400,
  "pay_currency": "ltc"
});

var requestOptions = {
  method: 'POST',
  headers: myHeaders,
  body: raw,
  redirect: 'follow'
};

fetch("https://us-central1-bca-app-c8ef0.cloudfunctions.net/app/paymentOnSite", requestOptions)
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
##### (4)
**ENDPOINT:** ```/paymentWithInvoice```</br>
**ACTION:** POST</br>
**DETAIL:** This endpoint creates a payment link. With this method, the customer is required to follow the generated url to complete their payment.</br>
**REQUEST DATA(JSON):** All data in body is required for a valid request.
```
{
  price_amount:        (number): This is the fiat equivalent of the price to be paid in crypto in USD,
}
```

###### USAGE EXAMPLE(Javascript)
```javascript

var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  "price_amount": 1000
});

var requestOptions = {
  method: 'POST',
  headers: myHeaders,
  body: raw,
  redirect: 'follow'
};

fetch("https://us-central1-bca-app-c8ef0.cloudfunctions.net/app/paymentWithInvoice", requestOptions)
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
##### (5)
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

fetch("https://us-central1-bca-app-c8ef0.cloudfunctions.net/app/checkPaymentStatus", requestOptions)
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
