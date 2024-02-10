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
**ENDPOINT:** ```/APIavailable``` </br>
**ACTION:** GET </br>
**DETAIL:** This endpoint lets the developer get a response from the API server, to know if it is alive and working fine. It responds with a single line of text to give it's availaility status. No response shows that the server is either dead or it's crashed. </br>

###### USAGE EXAMPLE(Javascript)
```javascript

var requestOptions = {
  method: 'GET',
  redirect: 'follow'
};

fetch("https://us-central1-web3-marketing-hub.cloudfunctions.net/api/APIavailable", requestOptions)
  .then(response => response.text())
  .then(result => console.log(result))
  .catch(error => console.log('error', error));

```

###### RESPONSE DATA(TEXT)
- 200 `Blockchain-Ad API Server is up and running.`
- 4xx `<Errors from incomplete request body>`
- 5xx `<try/catch error response>`

---

##### (2)
**ENDPOINT:** ```/APIavailableWithAuth``` </br>
**ACTION:** GET </br>
**DETAIL:** This endpoint works almost the same way as the endpoint above, APIavailable, but in this case, it takes in uuid, and the user authorization token in its header, to ensure that a user is logged in and that the same user is the one making an API call. </br>

**REQUEST DATA(JSON):** All data in body is required for a valid request.
```
{
  uuid:       (String): "This is the unique ID created by Google when the account was created, it is also in the browser context, as UID",
}
```

###### USAGE EXAMPLE(Javascript)
```javascript

var myHeaders = new Headers();
myHeaders.append("Authorization", "\"Bearer <token>\"");
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  "uuid": "<user-unique-ID>"
});

var requestOptions = {
  method: 'GET',
  headers: myHeaders,
  body: raw,
  redirect: 'follow'
};

fetch("https://us-central1-web3-marketing-hub.cloudfunctions.net/api/APIavailableWithAuth", requestOptions)
  .then(response => response.text())
  .then(result => console.log(result))
  .catch(error => console.log('error', error));

```

###### RESPONSE DATA(TEXT)
- 200 `Blockchain-Ad API Server is up and running.`
- 4xx `<Errors from incomplete request body>` || `<Errors for unauthorized users>`
- 5xx `<try/catch error response>`

---


##### [ACCOUNT ACTIONS]
---
##### (1)
**ENDPOINT:** ```/newSignUp``` </br>
**ACTION:** POST </br>
**DETAIL:** This endpoint is for creating a new user account via the form fields data collected. Once the form is filled, the frontend application sends a post request with that information to the endpoint, a new account is created, a success status is sent, and user can be prompted to login </br>

**REQUEST DATA(JSON):** All data in body is required for a valid request.
```
{
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

fetch("https://us-central1-web3-marketing-hub.cloudfunctions.net/api/completeSignUp", requestOptions)
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
**DETAIL:** This endpoint is for getting user information during the login process. Once a user logs in, either via the form or via one click sign In, You use the google AUTH SDK for that, you can read the documentation [here](https://firebase.google.com/docs/auth/web/password-auth), once you get a successful response from the call to Google, you then call this endpoint, to pull all the user's information from the database and render it on the dashboard page. Now takes authorization token for route protection.</br>

**REQUEST DATA(JSON):** All data in body is required for a valid request.
```
{
  uuid:    (String): "All that is required is the uuid to identify the user in the DB, the Firebase SDK returns that, so you can call the endpoint with the response from the SDK",
}
```

###### USAGE EXAMPLE(Javascript)
```javascript

var myHeaders = new Headers();
myHeaders.append("Authorization", "\"Bearer <token>\"");
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  "uuid": "<user-unique-ID>"
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
**ENDPOINT:** ```/loadCampaignData``` </br>
**ACTION:** POST </br>
**DETAIL:** This endpoint is for getting all of the organization's campaign data on a user's profile. It pulls all the campaign ID, and retrieves the data for that campaign, which is returned in the response body. It should be called after the loadprofile endpoint as the currentOrganizationID required to make a valud loadCampaignData is returned in the loadUserProfile endpoint. You could add an animation, as it would take longer, due to the provider not creating an endpoint to directly check for the campaign status, and this has to be done one after the other. Also, in the response is a field called extraDataAvailable, this field resolves to true or false, depending on if the there is data on the next page. This lets the frontend able to show a next page or not, according to, if there is extra data   It takes authorization token for route protection.</br>

**REQUEST DATA(JSON):** All data in body is required for a valid request.
```
{
  uuid:                 (String): "All that is required is the uuid to identify the user in the DB, the Firebase SDK returns that, so you can call the endpoint with the response from the SDK",
  currentOrganizationID (String): "The user's current organization ID, returned in the response of the loadUserProfile endpoint call",
  page                  (Number): "The pagination field to specify the page to be loaded maximum item per page is defined by the pageSize field below.",
  pageSize:             (Number): This defines how many items should be on the page. From the UI, the accepted options are "5, 10, or 15",
}
```

###### USAGE EXAMPLE(Javascript)
```javascript

var myHeaders = new Headers();
myHeaders.append("Authorization", "\"Bearer <token>\"");
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  "uuid": "<user-unique-ID>"
  "currentOrganizationID": "<currentOrganizationID>"
  "page": 1,
  "pageSize": 10
});

var requestOptions = {
  method: 'POST',
  headers: myHeaders,
  body: raw,
  redirect: 'follow'
};

fetch("https://us-central1-web3-marketing-hub.cloudfunctions.net/api/loadCampaignData", requestOptions)
  .then(response => response.text())
  .then(result => console.log(result))
  .catch(error => console.log('error', error));

```

###### RESPONSE DATA(JSON)
- 200 `{res_sts: true, res_msg: Campaign data retrieved successfully, res_data: <campaignData> }`
- 4xx `{res_sts: false, res_msg: <Errors from incomplete request body>}`
- 5xx `{res_sts: false, res_msg: error.message}`

---

##### (5)
**ENDPOINT:** ```/loadBlockchainData``` </br>
**ACTION:** POST </br>
**DETAIL:** This endpoint makes an HTTP GET request to retrieve blockchain data for the smart Contract address, tied to the campaign, created by users. The request should include parameters such as uuid, chainNetwork, smartContractAddress, page, and order. It takes authorization token in it's header for route protection.</br>

**REQUEST DATA(JSON):** All data in body is required for a valid request.
```
{
  uuid:                  (String): "All that is required is the uuid to identify the user in the DB, the Firebase SDK returns that, so you can call the endpoint with the response from the SDK",
  chainNetwork:          (String): "The blockchain Network on which the smart Contract resides, to pull data from, the two supported blockchain networks are 'BSC' and 'ETH'",
  smartContractAddress:  (String): " The blockchain smart contract address to be queried for transaction data. An example of a 'BSC' address is: '0xF426a8d0A94bf039A35CEE66dBf0227A7a12D11e' ",
  page:                  (Number): "This for selecting the current page to show data, starting from page 1. Each page returns 10 items, to continue from the 11th to 21st items for the same address, the page number should be switched to 2, and then 3 and so forth",
  order:                 (String): "This is the order in which the transactions shouls be listed, in either ascending order('asc') or descending order('desc'). The current default is 'desc'",
}
```

###### USAGE EXAMPLE(Javascript)
```javascript

var myHeaders = new Headers();
myHeaders.append("Authorization", "Bearer <token>");
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  "uuid": "<user-unique-ID>",
  "chainNetwork": "BSC",
  "smartContractAddress": "0xF426a8d0A94bf039A35CEE66dBf0227A7a12D11e",
  "page": 1,
  "order": "desc"
});

var requestOptions = {
  method: 'POST',
  headers: myHeaders,
  body: raw,
  redirect: 'follow'
};

fetch("https://us-central1-web3-marketing-hub.cloudfunctions.net/api/loadBlockchainData", requestOptions)
  .then(response => response.text())
  .then(result => console.log(result))
  .catch(error => console.log('error', error));

```

###### RESPONSE DATA(JSON)
- 200 `{res_sts: true, res_msg: Blockchain data retrieved successfully, res_data: <blockchainData> }`
- 4xx `{res_sts: false, res_msg: <Errors from incomplete request body>}`
- 5xx `{res_sts: false, res_msg: error.message}`

---

##### (6)
**ENDPOINT:** ```/connectAnalyticsAccount``` </br>
**ACTION:** POST </br>
**DETAIL:** This endpoint works after an authorization code has been retrieved from Google's authorization. The retrieved code is sent to this endpoint to create a connection with the user's account and the credentials stored in the DB for later connection re-establishment.</br>

**REQUEST DATA(JSON):** All data in body is required for a valid request.
```
{
  uuid:                  (String): "All that is required is the uuid to identify the user in the DB, the Firebase SDK returns that, so you can call the endpoint with the response from the SDK",
  authCode:               (String): "String for code retrieved from Google authorization",
}
```

###### USAGE EXAMPLE(Javascript)
```javascript
var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");
myHeaders.append("Authorization", "Bearer <token>");

var raw = JSON.stringify({
  "uuid": "<user-unique-ID>",
  "authCode": "<authCode>"
});

var requestOptions = {
  method: 'POST',
  headers: myHeaders,
  body: raw,
  redirect: 'follow'
};

fetch("https://us-central1-web3-marketing-hub.cloudfunctions.net/api/connectAnalyticsAccount", requestOptions)
  .then(response => response.text())
  .then(result => console.log(result))
  .catch(error => console.log('error', error));

```

###### RESPONSE DATA(JSON)
- 200 `{res_sts: true, res_msg: Google analytics account connected successfully}`
- 4xx `{res_sts: false, res_msg: <Errors from incomplete request body>}`
- 5xx `{res_sts: false, res_msg: error.message}`

---

##### (7)
**ENDPOINT:** ```/disconnectAnalyticsAccount``` </br>
**ACTION:** POST </br>
**DETAIL:** This endpoint lets a user revoke our connection to their Google analytics account. It revokes their connection and drops their credentials from the database.</br>

**REQUEST DATA(JSON):** All data in body is required for a valid request.
```
{
  uuid:                  (String): "All that is required is the uuid to identify the user in the DB, the Firebase SDK returns that, so you can call the endpoint with the response from the SDK",
}
```

###### USAGE EXAMPLE(Javascript)
```javascript
var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");
myHeaders.append("Authorization", "Bearer <token>");

var raw = JSON.stringify({
  "uuid": "<user-unique-ID>"
});

var requestOptions = {
  method: 'POST',
  headers: myHeaders,
  body: raw,
  redirect: 'follow'
};

fetch("https://us-central1-web3-marketing-hub.cloudfunctions.net/api/disconnectAnalyticsAccount", requestOptions)
  .then(response => response.text())
  .then(result => console.log(result))
  .catch(error => console.log('error', error));

```

###### RESPONSE DATA(JSON)
- 200 `{res_sts: true, res_msg: Google analytics account disconnected successfully}`
- 4xx `{res_sts: false, res_msg: <Errors from incomplete request body>}`
- 5xx `{res_sts: false, res_msg: error.message}`

---

##### (8)
**ENDPOINT:** ```/loadAnalyticsProject``` </br>
**ACTION:** POST </br>
**DETAIL:** This endpoint works to retrieve a summary of the user's Google profile data, all their connected and monitored projects with their IDs. The IDs can then be used in the next endpoint call to query the data for those projects.</br>

**REQUEST DATA(JSON):** All data in body is required for a valid request.
```
{
  uuid:                  (String): "All that is required is the uuid to identify the user in the DB, the Firebase SDK returns that, so you can call the endpoint with the response from the SDK",
}
```

###### USAGE EXAMPLE(Javascript)
```javascript
var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");
myHeaders.append("Authorization", "Bearer <token>");

var raw = JSON.stringify({
  "uuid": "<user-unique-ID>"
});

var requestOptions = {
  method: 'POST',
  headers: myHeaders,
  body: raw,
  redirect: 'follow'
};

fetch("https://us-central1-web3-marketing-hub.cloudfunctions.net/api/loadAnalyticsProfile", requestOptions)
  .then(response => response.text())
  .then(result => console.log(result))
  .catch(error => console.log('error', error));

```

##### (9)
**ENDPOINT:** ```/loadAnalyticsProfile``` </br>
**ACTION:** POST </br>
**DETAIL:** This endpoint works to retrieve all the data related to a project, The project is identified by it's project ID, whcich is sent as a part of the JSON payload.</br>

**REQUEST DATA(JSON):** All data in body is required for a valid request.
```
{
  uuid:                  (String): "All that is required is the uuid to identify the user in the DB, the Firebase SDK returns that, so you can call the endpoint with the response from the SDK",
  projectID:             (String): "The id for the project to be queried.",
  startDate:             (String): "The start date for the analytics data to be pulled from in YYYY-MM-DD format. It is optional and if not provided, it uses 30days from the day",
  endDate:               (String): "The end date for the analytics data to be pulled from in YYYY-MM-DD format. It is optional and if not provided, it uses yesterday as the day",
}
```

###### USAGE EXAMPLE(Javascript)
```javascript
var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");
myHeaders.append("Authorization", "Bearer <token>");

var raw = JSON.stringify({
  "uuid": "<user-unique-ID>",
  "projectID": "<project-id>",
  "startDate": "YYYY-MM-DD",
  "endDate": "YYYY-MM-DD"
});

var requestOptions = {
  method: 'POST',
  headers: myHeaders,
  body: raw,
  redirect: 'follow'
};

fetch("https://us-central1-web3-marketing-hub.cloudfunctions.net/api/loadAnalyticsProfile", requestOptions)
  .then(response => response.text())
  .then(result => console.log(result))
  .catch(error => console.log('error', error));

```

###### RESPONSE DATA(JSON)
- 200 `{res_sts: true, res_msg: Google analytics projects retrieved, res_data: <profile_data>}`
- 4xx `{res_sts: false, res_msg: <Errors from incomplete request body>}`
- 5xx `{res_sts: false, res_msg: error.message}`

---

##### (10)
**ENDPOINT:** ```/switchOrganization``` </br>
**ACTION:** POST </br>
**DETAIL:** This endpoint is used for switching the user's organization while logged in. To switch the organization, they make a call to the endpoint with the organization's ID they want to switch to and also their own ID to ensure they are logged in. Now takes authorization token for route protection.</br>

**REQUEST DATA(JSON):** All data in body is required for a valid request.
```
{
  uuid:          (String): "All that is required is the uuid to identify the user in the DB, the Firebase SDK returns that, so you can call the endpoint with the response from the SDK",
  organizationID:(String): "ID for the organization. This is loaded in when a user is logged in, you can then make it the value of the drop down for the organization selection. When a user select to switch an organization, it picks up that organization id.",
}
```

###### USAGE EXAMPLE(Javascript)
```javascript

var myHeaders = new Headers();
myHeaders.append("Authorization", "\"Bearer <token>\"");
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  "uuid": "<user-unique-ID>",
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

##### (11)
**ENDPOINT:** ```/createNewOrganization``` </br>
**ACTION:** POST </br>
**DETAIL:** This endpoint is used for creating a new Organization and adding that individual as the admin and member of the organization. Now takes authorization token for route protection.</br>

**REQUEST DATA(JSON):** All data in body is required for a valid request.
```
{
  uuid:            (String): "All that is required is the uuid to identify the user in the DB, the Firebase SDK returns that, so you can call the endpoint with the response from the SDK",
  organizationName:(String): "Name for the organization during creation.",
}
```

###### USAGE EXAMPLE(Javascript)
```javascript

var myHeaders = new Headers();
myHeaders.append("Authorization", "\"Bearer <token>\"");
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  "uuid": "<user-unique-ID>",
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


##### (12)
**ENDPOINT:** ```/updateUserInfo``` </br>
**ACTION:** POST </br>
**DETAIL:** This endpoint is used for the user to update their information. It can update the user's information on the profile page. Now takes authorization token for route protection.</br>

**REQUEST DATA(JSON):** All data in the main body is required for a valid request, but data in the info body is optional, but there should at least be one.
```
{
  uuid:            (String): "All that is required is the uuid to identify the user in the DB, the Firebase SDK returns that, so you can call the endpoint with the response from the SDK",
  info:            (object):{
                              name: "The user's new name for update",
                              email: "The user's new email for the update",
                              password: "The user's new password",
                              profile_image: "The link to the user's new profile image hosted on our Google storage",
                              organizationName: "The new name for the organization, works only if organization Admin"
                              organizationID: "The organization ID which the user wants to change the name. If organizationName is not provided, organizationID would not be accepted. The two are only required during a name change."
                            }
}
```

###### USAGE EXAMPLE(Javascript)
```javascript

var myHeaders = new Headers();
myHeaders.append("Authorization", "\"Bearer <token>\"");
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  "uuid": "<user-unique-ID>",
  "info": {
    "name": "<user-new-name>", 
    "email":"<user-new-email>", 
    "profile_image":"<user-new-profile-image>",
    "password":"<user-new-password>",
    "organizationName":"<user-new-organization-name>"
    "organizationID":"<user-current-organization-ID>"
    }
  "profile_image": "<image-URL>"
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

##### (13)
**ENDPOINT:** ```/inviteUser``` </br>
**ACTION:** POST </br>
**DETAIL:** This endpoint is used for the only Admins/creators of an organization to invite others into the organization. Now takes authorization token for route protection.</br>

**REQUEST DATA(JSON):** All data in the  body is required for a valid request.
```
{
  uuid:             (String): "All that is required is the uuid to identify the user in the DB, the Firebase SDK returns that, so you can call the endpoint with the response from the SDK",
  organizationID:   (String): "ID for the organization the new user is being invited into",
  senderName:       (String): "This is the name of the sender, picked up from the user object returned in the SDK",
  inviteeEmail:     (String): "The email for the user being invited into the organization",
}
```

###### USAGE EXAMPLE(Javascript)
```javascript

var myHeaders = new Headers();
myHeaders.append("Authorization", "\"Bearer <token>\"");
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  "uuid": "<user-unique-ID>",
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

fetch("https://us-central1-web3-marketing-hub.cloudfunctions.net/api/inviteUser", requestOptions)
  .then(response => response.text())
  .then(result => console.log(result))
  .catch(error => console.log('error', error));

```

###### RESPONSE DATA(TEXT)
- 200 `Invite Emails sending to user.`
- 4xx `<Errors from incomplete request body>`
- 5xx `<try/catch error response>`

---

##### (14)
**ENDPOINT:** ```/acceptInvite``` </br>
**ACTION:** POST </br>
**DETAIL:** This endpoint is hosted on a page with buttons, where the accept button calls the accept I=invite endpoint. It accepts the user into the organization as a member and returns a new loadUserprofile object. An email is sent to the admin to let them know invite has been accepted</br>

**REQUEST DATA(JSON):** All data in the  body is required for a valid request.
```
{
  uuid:             (String): "All that is required is the uuid to identify the user in the DB, the Firebase SDK returns that, so you can call the endpoint with the response from the SDK",
  organizationID:   (String): "ID for the organization the new user is being invited into",
}
```

###### USAGE EXAMPLE(Javascript)
```javascript

var myHeaders = new Headers();
myHeaders.append("Authorization", "\"Bearer <token>\"");
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  "uuid": "<user-unique-ID>",
  "organizationID": "<organization-ID>"
});

var requestOptions = {
  method: 'POST',
  headers: myHeaders,
  body: raw,
  redirect: 'follow'
};

fetch("https://us-central1-web3-marketing-hub.cloudfunctions.net/api/acceptInvite", requestOptions)
  .then(response => response.text())
  .then(result => console.log(result))
  .catch(error => console.log('error', error));

```

###### RESPONSE DATA(TEXT)
- 200 `Invite accept successful and user data retrieved successfully`
- 4xx `<Errors from incomplete request body>`
- 5xx `<try/catch error response>`

---

##### (15)
**ENDPOINT:** ```/rejectInvite``` </br>
**ACTION:** POST </br>
**DETAIL:** This endpoint is hosted on a page with buttons, where the reject button calls this reject invite endpoint. It removes the user from the invite link, and also sends an email to the admin to let him know a user rejected the invitation.</br>

**REQUEST DATA(JSON):** All data in the  body is required for a valid request.
```
{
  uuid:             (String): "All that is required is the uuid to identify the user in the DB, the Firebase SDK returns that, so you can call the endpoint with the response from the SDK",
  organizationID:   (String): "ID for the organization the new user is being invited into",
}
```

###### USAGE EXAMPLE(Javascript)
```javascript

var myHeaders = new Headers();
myHeaders.append("Authorization", "\"Bearer <token>\"");
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  "uuid": "<user-unique-ID>",
  "organizationID": "<organization-ID>"
});

var requestOptions = {
  method: 'POST',
  headers: myHeaders,
  body: raw,
  redirect: 'follow'
};

fetch("https://us-central1-web3-marketing-hub.cloudfunctions.net/api/rejectInvite", requestOptions)
  .then(response => response.text())
  .then(result => console.log(result))
  .catch(error => console.log('error', error));

```

###### RESPONSE DATA(TEXT)
- 200 `Invite to Organization Rejected`
- 4xx `<Errors from incomplete request body>`
- 5xx `<try/catch error response>`

---

##### (16)
**ENDPOINT:** ```/resetAccountPassword``` </br>
**ACTION:** POST </br>
**DETAIL:** This endpoint takes in an email address and sends that user, if they are an actual user, a reset password link for the account password. No route protection on here.</br>

**REQUEST DATA(JSON):** All data in the  body is required for a valid request.
```
{
 "email": "The email address of the user requesting a password change",
}
```

###### USAGE EXAMPLE(Javascript)
```javascript

var myHeaders = new Headers();
myHeaders.append("Authorization", "\"Bearer <token>\"");

var raw = JSON.stringify({
  "email": "<eemail>",
});

var requestOptions = {
  method: 'POST',
  headers: myHeaders,
  body: raw,
  redirect: 'follow'
};

fetch("https://us-central1-web3-marketing-hub.cloudfunctions.net/api/resetAccountPassword", requestOptions)
  .then(response => response.text())
  .then(result => console.log(result))
  .catch(error => console.log('error', error));

```

###### RESPONSE DATA(TEXT)
- 200 `Email sent || Current security warning`
- 4xx `<Errors from incomplete request body>`
- 5xx `<try/catch error response>`

---

##### [PAYMENT ACTIONS]
---
##### (1)
**ENDPOINT:** ```/getAllCurrencies```</br>
**ACTION:** GET</br>
**DETAIL:** This is a method to obtain detailed information about all cryptocurrencies available for payments. The frontend can then use the response as a dropdown menu when a user needs to pay, to let them choose which cryptocurrency to pay with. Now takes authorization token for route protection.</br>

###### USAGE EXAMPLE(Javascript)
```javascript

var myHeaders = new Headers();
myHeaders.append("Authorization", "\"Bearer <token>\"");

var requestOptions = {
  method: 'GET',
  headers: myHeaders,
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
  "data": [
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
myHeaders.append("Authorization", "\"Bearer <token>\"");
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  "price_amount": 400,
  "uuid": "<user-unique-ID>",
  "pay_currency": "ltc"
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
  price_amount:      (number): "This is the fiat equivalent of the price to be paid in crypto in USD",
  uuid:              (String): "The unique ID for the user making the payment",
}
```

###### USAGE EXAMPLE(Javascript)
```javascript
var myHeaders = new Headers();
myHeaders.append("Authorization", "\"Bearer <token>\"");
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  "price_amount": 1000,
  "uuid": "<user-unique-ID>"
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
##### (4)
**ENDPOINT:** ```/checkPaymentStatus```</br>
**ACTION:** GET</br>
**DETAIL:** This endpoint is used to get information about a payment.</br>
**REQUEST DATA(JSON):** All data in body is required for a valid request.
```
{
  paymentId:        (string): "Unique ID corresponding to a valid user's payment transaction",
}
```

###### USAGE EXAMPLE(Javascript)
```javascript
var myHeaders = new Headers();
myHeaders.append("Authorization", "\"Bearer <token>\"");
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  "paymentId": 6366009964,
  "uuid": "<user-unique-ID>"
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
**DETAIL:** This endpoint creates a new campaign for user. It does validity check on organization balance before allowing a campaign get created. The minimum price allowed for a  campaign is USD 300. Now takes authorization token for route protection.</br>

**REQUEST DATA(JSON):** All data in body is required for a valid request.
```
{
  uuid:                (String): "The unique ID for the user making the payment",
  campaignInfo:        (Object): "This is an object that contains the information required for creating a valid campaign":
                      {
                        campaignName:(String): Name of the Campaign being created,
                        targetURL:(String): The url that ad clicker would be sent to,
                        startDate: The date for the ads to be started in DD/MM/YYYY format,
                        budget:(Number): The budget amount for campaign,
                        maxCPC:(Number): This is the price a user is willing to pay for a click on an advert shown,
                        timezone:(Number): The timezone for the advert to be shown,
                        smartContractAddress:(String): The smart contract address of the organization being advertised, 
                        chainTargeting:(Array): The blockchain network being targeted,
                        operativeSystem:(Array): The operating system option wanted by the advertiser for their ads to be shown on. Example: ["mobile", "desktop"],
                        category:(Array): This is the array of category, as specified from the model page,
                        web3Audience:(Array): This is the array of web3Audience as specified by the model page,
                        walletTargeting:(Array): This is the array of wallet Targeting as specified by the model page,
                        browser:(Array): This is the array of browser as specified by the model page,
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
myHeaders.append("Authorization", "<Bearer token>");
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  "uuid": "0mPPJjSttgcMJjItLHWhhDzZv383",
  "campaignInfo": {
    "campaignName": "Budget Test2 API Campaign",
    "targetURL": "https://abc.xyz",
    "startDate": "09/01/2024",
    "budget": 500,
    "maxCPC": "0.25",
    "timezone": 3,
    "smartContractAddress": "0x0000000000000000000000000000000000001000",
    "campaignType": [
      "Awareness(max reach)",
      "Engagement(website visit/interaction)",
      "Conversion(website download/transactions/sales)"
    ],
    "chainTargeting": [
      "Ethereum",
      "Polygon",
      "Binance Smart Chain"
    ],
    "operativeSystem": [
      "android",
      "iOS"
    ],
    "category": [
      "NFT",
      "Decentralized Finance",
      "DEX"
    ],
    "web3Audience": [
      "ETH Holders",
      "BNB Holders"
    ],
    "walletTargeting": [
      "Metamask",
      "Coinbase",
      "OKX"
    ],
    "deviceType": [
      "mobile"
    ],
    "browser": [
      "Google Chrome",
      "Firefox"
    ]
  },
  "creatives": [
    {
      "status": 1,
      "title": " Test API Title",
      "image": "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAewAAAFIAQMAAACoaV/bAAAAAXNSR0IB2cksfwAAAAlwSFlzAAALEwAACxMBAJqcGAAAAANQTFRF////p8QbyAAAACtJREFUeJztwYEAAAAAw6D5U1/gCFUBAAAAAAAAAAAAAAAAAAAAAAAAAHwDULgAAVNxxnoAAAAASUVORK5CYII="
    }
  ]
});

var requestOptions = {
  method: 'POST',
  headers: myHeaders,
  body: raw,
  redirect: 'follow'
};

fetch("http://localhost:5000/web3-marketing-hub/us-central1/api/createNewCampaign", requestOptions)
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
**ENDPOINT:** ```/updateCampaignInfo``` </br>
**ACTION:** POST </br>
**DETAIL:** This endpoint updates a campaign info/data via its ID, with the new info/data. Now takes authorization token for route protection.</br>

**REQUEST DATA(JSON):** All data in body is required for a valid request.
```
{
  uuid:                (String): "The unique ID for the user making the payment",
  campaignId:          (Number): "The ID for the campaign to be viewed",
  creatorEmail:        (String): "This is the email of the creator of the campaign",
  newCampaignInfo:     (Object): "This is the object that contains the new campaign Info"":
                               {
                                  name:(String): "New Campaign Name",
                                  limit_daily_amount:(Number): "New Campaign daily Limit"
                               }
}
```

###### USAGE EXAMPLE(Javascript)
```javascript
var myHeaders = new Headers();
myHeaders.append("Authorization", "\"Bearer <token>\"");
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  "uuid": "<user-unique-ID>",
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

##### (3)
**ENDPOINT:** ```/playOrPauseCampaign``` </br>
**ACTION:** POST </br>
**DETAIL:** This endpoint updates a campaign info/data via its ID, with the new info/data. Now takes authorization token for route protection.</br>

**REQUEST DATA(JSON):** All data in body is required for a valid request.
```
{
  uuid:              (String): "The unique ID for the user making the payment",
  organizationID:    (String): "The ID for the organization where the campaign is",,
  campaignId:        (Number): "The ID for the campaign to be paused or started",
  toPlayCampaign:    (Boolean): "To stop campaign, set to false, to start, set to true",
}

```

###### USAGE EXAMPLE(Javascript)
```javascript
var myHeaders = new Headers();
myHeaders.append("Authorization", "\"Bearer <token>\"");
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  "uuid": "<user-unique-ID>",
  "organizationID": "<organization-ID>",
  "campaignId": "<campaign-ID>",
  "toPlayCampaign": false
});

var requestOptions = {
  method: 'POST',
  headers: myHeaders,
  body: raw,
  redirect: 'follow'
};

fetch("https://us-central1-web3-marketing-hub.cloudfunctions.net/api/playOrPauseCampaign", requestOptions)
  .then(response => response.text())
  .then(result => console.log(result))
  .catch(error => console.log('error', error));

```

###### RESPONSE DATA(JSON)
- 200 `{res_sts: true, res_msg: Campaign Started/Stopped successfully}`
- 4xx `{res_sts: false, res_msg: <Errors from incomplete request body>}`
- 5xx `{res_sts: false, res_msg: error.message}`

---

##### (4)
**ENDPOINT:** ```/updateURL``` </br>
**ACTION:** POST </br>
**DETAIL:** This endpoint updates a campaign info/data target URL, with a new URL, the URL is where viewers of the Ads are directed to, when they click. Now takes authorization token for route protection.</br>

**REQUEST DATA(JSON):** All data in body is required for a valid request.
```
{
  uuid:                (String): "The unique ID for the user making the payment",
  campaignId:          (Number): "The ID for the campaign to be viewed",
  newTargetUrl:        (String): "The new target URL to update",
}

```

###### USAGE EXAMPLE(Javascript)
```javascript
var myHeaders = new Headers();
myHeaders.append("Authorization", "\"Bearer <token>\"");
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  "uuid": "<user-unique-ID>",
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

##### (5)
**ENDPOINT:** ```/addNewCreative``` </br>
**ACTION:** POST </br>
**DETAIL:** This endpoint updates a campaign creative information. Users can add a new creative to a campaign. Now takes authorization token for route protection.</br>

**REQUEST DATA(JSON):** All data in body is required for a valid request.
```
{
  uuid:              (String): "The unique ID for the user making the payment",
  organizationID:    (String): "The ID for the organization where the campaign is",
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
myHeaders.append("Authorization", "\"Bearer <token>\"");
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  "uuid": "<user-unique-ID>",
  "organizationID":  "<organization-ID>",
  "campaignId": "<campaign-ID>",
  "creatives": [
    {
      "title": " Test API Title",
      "image": "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAewAAAFIAQMAAACoaV/bAAAAAXNSR0IB2cksfwAAAAlwSFlzAAALEwAACxMBAJqcGAAAAANQTFRF////p8QbyAAAACtJREFUeJztwYEAAAAAw6D5U1/gCFUBAAAAAAAAAAAAAAAAAAAAAAAAAHwDULgAAVNxxnoAAAAASUVORK5CYII="
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

##### (6)
**ENDPOINT:** ```/editCreative``` </br>
**ACTION:** POST </br>
**DETAIL:** This updates a previous creative, to change the name, title, or the image. Now takes authorization token for route protection.</br>

**REQUEST DATA(JSON):** All data in body is required for a valid request.
```
{
  uuid:              (String): "The unique ID for the user making the payment",
  organizationID:    (String): "The ID for the organization where the campaign sits in",
  campaignId:        (Number): "The ID for the campaign that contains the creative",
  creativeId:        (Number): "The ID for the creative been edited",
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
myHeaders.append("Authorization", "\"Bearer <token>\"");
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  "uuid": "<user-unique-ID>",
  "organizationID": "<organization-ID>"
  "campaignID": "<campaign-ID>",
  "creativeId": "<creativeId>",
  "creatives": [
    {
      "title": " Test API Title",
      "image": "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAewAAAFIAQMAAACoaV/bAAAAAXNSR0IB2cksfwAAAAlwSFlzAAALEwAACxMBAJqcGAAAAANQTFRF////p8QbyAAAACtJREFUeJztwYEAAAAAw6D5U1/gCFUBAAAAAAAAAAAAAAAAAAAAAAAAAHwDULgAAVNxxnoAAAAASUVORK5CYII="
    }
  ]
});

var requestOptions = {
  method: 'POST',
  headers: myHeaders,
  body: raw,
  redirect: 'follow'
};

fetch("https://us-central1-web3-marketing-hub.cloudfunctions.net/api/editCreative", requestOptions)
  .then(response => response.text())
  .then(result => console.log(result))
  .catch(error => console.log('error', error));
```

###### RESPONSE DATA(JSON)
- 200 `{res_sts: true, res_msg: Creative(s) updated successfully}`
- 4xx `{res_sts: false, res_msg: <Errors from incomplete request body>}`
- 5xx `{res_sts: false, res_msg: error.message}`

---

##### (7)
**ENDPOINT:** ```/startOrStopCreative``` </br>
**ACTION:** POST </br>
**DETAIL:** This endpoint updates/edits an already created creative. Now takes authorization token for route protection.</br>

**REQUEST DATA(JSON):** All data in body is required for a valid request.
```
{
  uuid:                (String): "The unique ID for the user making the payment",
  creativeId:          (Number): "The ID for the campaign to be edited/updated",
  toPlayCreative:      (Boolean): "To stop creative, set to false, to start, set to true",
}

```

###### USAGE EXAMPLE(Javascript)
```javascript
var myHeaders = new Headers();
myHeaders.append("Authorization", "\"Bearer <token>\"");
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  "uuid": "<user-unique-ID>",
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
