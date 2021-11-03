# SenseiAQ Cloud API
Endpoints for fetching device info from the database.
Base URL: https://sensei.pierasystems.com

Note, CORS is enabled for all API endpoints. When testing locally in a browser, you can use http://localhost:3000/ or http://localhost:3001/ as the origin if you’re running into CORS restrictions. If you have Python 3 installed, simply run “**python -m http.server 3000**“ in the directory you want to be your web root.

## /api/login
*HTTP POST*

Login with a username and password, responds with a cookie for browser-based authentication.

Request fields:
- username
- password

Response fields:
- message 

```
Example code:
let data = {
    username: "user",
    password: "pass"
};

fetch("https://sensei.pierasystems.com/api/login", {
  method: "POST",
  body: new URLSearchParams(data)
}).then(res => {
  return res.json()
}).then(body => {
    // "login-success"
    console.log(body.message);
});
```

/api/get-access-token
HTTP POST
Login with a username and password, responds with an access token for universal authentication.

Request fields:
username
password

Response fields:
token

Example code:
let data = {
    username: "user",
    password: "pass"
};

fetch("https://sensei.pierasystems.com/api/get-access-token", {
  method: "POST",
  body: new URLSearchParams(data)
}).then(res => {
  return res.json()
}).then(body => {
    console.log(body.token);
});

/api/register
HTTP POST
Register for a new account. The response message indicates whether or not registration was successful.

Request fields:
username
email
password

Response fields:
message

Example code:
let data = {
    username: "user",
    password: "pass",
    email: "user@user.com"
};

fetch("https://sensei.pierasystems.com/api/register", {
  method: "POST",
  body: new URLSearchParams(data)
}).then(res => {
  return res.json()
}).then(body => {
    // "registration-success"
    console.log(body.message);
});

/api/reset-password
HTTP POST
Reset account password via email. If the email address is found in the database, an email is sent out with a password reset token.

Request fields:
email

Response fields:
message

Example code:
let data = {
    email: "user@user.com"
};

fetch("https://sensei.pierasystems.com/api/reset-password", {
  method: "POST",
  body: new URLSearchParams(data)
}).then(res => {
  return res.json()
}).then(body => {
    // "password-reset"
    console.log(body.message);
});

/api/set-new-password
HTTP POST
Set new password, requires password reset token from email.

Request fields:
token
password

Response fields:
message

Example code:
let data = {
    token: "1234abcd1234abcd1234",
    password: "pass"
};

fetch("https://sensei.pierasystems.com/api/set-new-password", {
  method: "POST",
  body: new URLSearchParams(data)
}).then(res => {
  return res.json()
}).then(body => {
    // "reset-success"
    console.log(body.message);
});

/api/logout
HTTP GET
Logout current user. Invalidates session cookie and access token. Authentication is required.

Response fields:
message

Example code:
let accessToken = "1234abcd1234abcd1234";

fetch("https://sensei.pierasystems.com/api/logout", {
  method: "GET",
  headers: new Headers({
     'Authorization': 'Bearer ' + accessToken
   }),
}).then(res => {
  return res.json()
}).then(body => {
    // "logout-success"
    console.log(body.message);
});

/api/account
HTTP GET
Get current user information. Authentication is required.

Response fields:
uid
user
email
created

Example code:
let accessToken = "1234abcd1234abcd1234";

fetch("https://sensei.pierasystems.com/api/account", {
  method: "GET",
  headers: new Headers({
     'Authorization': 'Bearer ' + accessToken
   }),
}).then(res => {
  return res.json()
}).then(body => {
    // "user name"
    console.log(body.user);
});


/api/get-my-devices
HTTP GET
Get the list of devices associated with current account. Authentication is required.

Response fields:
devices - array of device objects with fields:
did
iotid
name
pm100aqi
pm25aqi
pm10aqi
vocaqi
temp
rh
co2
lastupdate
created
location
lat
lng
public
events - array

Example code:
let accessToken = "1234abcd1234abcd1234";

fetch("https://sensei.pierasystems.com/api/get-my-devices", {
  method: "GET",
  headers: new Headers({
     'Authorization': 'Bearer ' + accessToken
   }),
}).then(res => {
  return res.json()
}).then(body => {
    // "Demo - Entry"
    console.log(body.devices[0].name);
});

/api/get-minute-averages/[DID]?page=[0]
HTTP GET
Get 60 one-minute averages. See /api/get-my-devices for device IDs (DID). Endpoint uses pagination, use “?page=0” for first page, “?page=1” for the second page, etc.

Response fields:
devices - array
data - array of one minute averages with fields:
mid
did
pc01, pc03, pc05, pc10, pc25, pc50, pc100
pm01, pm03, pm05, pm10, pm25, pm50, pm100
temp, rh, atm, vocaqi, vocppm, gasest, co2
location
time

Example code:
let accessToken = "1234abcd1234abcd1234";

fetch("https://sensei.pierasystems.com/api/get-minute-averages/20?page=0", {
  method: "GET",
  headers: new Headers({
     'Authorization': 'Bearer ' + accessToken
   }),
}).then(res => {
  return res.json()
}).then(body => {
    // "3.069499"
    console.log(body.data[0].pm100);
});

/api/get-hour-averages/[DID]
HTTP GET
Get 24 one-hour averages. See /api/get-my-devices for device IDs (DID). Endpoint uses pagination, use “?page=0” for first page, “?page=1” for the second page, etc.

Response fields:
devices - array
data - array of one minute averages with fields:
mid
did
pc01, pc03, pc05, pc10, pc25, pc50, pc100
pm01, pm03, pm05, pm10, pm25, pm50, pm100
temp, rh, atm, vocaqi, vocppm, gasest, co2
location
time

Example code:
let accessToken = "1234abcd1234abcd1234";

fetch("https://sensei.pierasystems.com/api/get-hour-averages/20?page=0", {
  method: "GET",
  headers: new Headers({
     'Authorization': 'Bearer ' + accessToken
   }),
}).then(res => {
  return res.json()
}).then(body => {
    // "28934"
    console.log(body.data[0].pc01);
});

/api/get-events/[did]
HTTP GET
Get recent events. See /api/get-my-devices for device IDs (DID). Endpoint uses pagination, use “?page=0” for first page, “?page=1” for the second page, etc.

Response fields:
devices - array
data - array of events with fields:
eid
did
type
time

Example code:
let accessToken = "1234abcd1234abcd1234";

fetch("https://sensei.pierasystems.com/api/get-events/20?page=0", {
  method: "GET",
  headers: new Headers({
     'Authorization': 'Bearer ' + accessToken
   }),
}).then(res => {
  return res.json()
}).then(body => {
    // "smoke"
    console.log(body.data[0].type);
});
