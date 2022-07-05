# SenseiAQ Cloud API
Endpoints for fetching device info from the database.
Base URL: https://sensei.pierasystems.com

Note, CORS is enabled for all API endpoints. When testing locally in a browser, you can use http://localhost:3000/ or http://localhost:3001/ as the origin if you’re running into CORS restrictions. If you have Python 3 installed, simply run “**python -m http.server 3000**“ in the directory you want to be your web root.

PC and PM values use the following naming convention in the code: PM1.0 becomes pm10, PM2.5 becomes pm25, PM5.0 becomes pm50, and PM10 becomes pm100. 

For more information about each field, see our [Data Dictionary](https://github.com/PieraSystems/senseiaq-api#data-dictionary).

## /api/login
*HTTP POST*

Login with a username and password, responds with a cookie for browser-based authentication.

**Request fields:**
- username
- password

**Response fields:**
- message 

**Example code:**
```
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

## /api/get-access-token
*HTTP POST*

Login with a username and password, responds with an access token for universal authentication.

**Request fields:**
- username
- password

**Response fields:**
- token

**Example code:**
```
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
```

## /api/register
*HTTP POST*

Register for a new account. The response message indicates whether or not registration was successful.

**Request fields:**
- username
- email
- password

**Response fields:**
- message

**Example code:**
```
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
```

## /api/reset-password
*HTTP POST*

Reset account password via email. If the email address is found in the database, an email is sent out with a password reset token.

**Request fields:**
- email

**Response fields:**
- message

**Example code:**
```
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
```

## /api/set-new-password
*HTTP POST*

Set new password, requires password reset token from email.

**Request fields:**
- token
- password

**Response fields:**
- message

**Example code:**
```
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
```

## /api/logout
*HTTP GET*

Logout current user. Invalidates session cookie and access token. Authentication is required.

**Response fields:**
- message

**Example code:**
```
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
```

## /api/account
*HTTP GET*

Get current user information. Authentication is required.

**Response fields:**
- uid (user ID)
- user (user name)
- email
- ccode (country code)
- phone
- created

**Example code:**
```
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
```


## /api/get-my-devices
*HTTP GET*

Get the list of devices associated with current account. Authentication is required.

**Response fields:**
- devices - array of device objects with fields:
  - did
  - iotid
  - name
  - pm100aqi, pm25aqi, pm10aqi
  - vocaqi, temp, rh, co2
  - lastupdate
  - created
  - location
  - lat, lng
  - public
  - events - array

**Example code:**
```
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
```

## /api/get-minute-averages/[DID]?page=[0]
*HTTP GET*

Get 60 one-minute averages. See /api/get-my-devices for device IDs (DID). Endpoint uses pagination, use “?page=0” for first page, “?page=1” for the second page, etc.

**Update:** Endpoint now supports optional start and end times, using ISO 8061 format. Each query is limited to 100 results, and the page offset can be used if you need more than 100. The following is an example query for getting all results from "2022-06-28 18:33:00" to "2022-06-28 19:33:59" for device ID 592:

*https://sensei.pierasystems.com/api-dev/get-minute-averages/592?page=0&start=2022-06-28%2018:33:00&end=2022-06-28%2019:33:59*

**Response fields:**
- devices - array
- data - array of one minute averages with fields:
  - mid
  - did
  - pc01, pc03, pc05, pc10, pc25, pc50, pc100
  - pm01, pm03, pm05, pm10, pm25, pm50, pm100
  - temp, rh, atm, vocaqi, vocppm, gasest, co2
  - location
  - time

**Example code:**
```
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
```

## /api/get-hour-averages/[DID]?page=[0]
*HTTP GET*

Get 24 one-hour averages. See /api/get-my-devices for device IDs (DID). Endpoint uses pagination, use “?page=0” for first page, “?page=1” for the second page, etc.

**Update:** Endpoint now supports optional start and end times, using ISO 8061 format. Each query is limited to 100 results, and the page offset can be used if you need more than 100. The following is an example query for getting all results from "2022-06-28 18:33:00" to "2022-06-29 18:33:59" for device ID 592:

*https://sensei.pierasystems.com/api-dev/get-hour-averages/592?page=0&start=2022-06-28%2018:33:00&end=2022-06-29%2018:33:59*

**Response fields:**
- devices - array
- data - array of one minute averages with fields:
  - mid
  - did
  - pc01, pc03, pc05, pc10, pc25, pc50, pc100
  - pm01, pm03, pm05, pm10, pm25, pm50, pm100
  - temp, rh, atm, vocaqi, vocppm, gasest, co2
  - location
  - time

**Example code:**
```
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
```

## /api/get-events/[did]?page=[0]
*HTTP GET*

Get recent events. See /api/get-my-devices for device IDs (DID). Endpoint uses pagination, use “?page=0” for first page, “?page=1” for the second page, etc.

**Response fields:**

devices - array

data - array of events with fields:
- eid
- did
- type
- time

**Example code:**
```
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
```

## /api/get-recent?limit=[5]
*HTTP GET*

Get recent updates for all devices on account. You can use *limit* to select 1-5 minutes of data, default 5. 

**Response fields:**

devices - array of devices with fields:
- did
- iotid
- name
- pm100aqi, pm25aqi, pm10aqi
- vocaqi, temp, rh, co2
- lastupdate
- created
- location
- lat, lng
- public
- events - array
- data - array of one minute averages with fields:
  - mid
  - did
  - pc01, pc03, pc05, pc10, pc25, pc50, pc100
  - pm01, pm03, pm05, pm10, pm25, pm50, pm100
  - temp, rh, atm, vocaqi, vocppm, gasest, co2
  - location
  - time

**Example code:**
```
let accessToken = "1234abcd1234abcd1234";

fetch("https://sensei.pierasystems.com/api/get-recent", {
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
```

# Data dictionary

## Device fields:

- **did** -   device ID (32-bit signed int) 
- **iotid** - serial# or MAC address (string)
- **name** - Serial number by default, or assigned name (string) 
- **pm100aqi** - Last reading of AQI Value for PM10 (int 0-500) a value of -1 indicates no data available
- **pm25aqi** Last reading of AQI Value for PM2.5 (int 0-500) a value of -1 indicates no data available
- **pm10aqi** -  Last reading of AQI Value for PM1.0 (int 0-500) a value of -1 indicates no data available
- **vocaqi** - Last reading VOC AQI Value (int 0-500) a value of -1 indicates no data available 
- **temp** - Last reading Temperature \*C to 1 decimal (float) a value of -274 indicates no data available 
- **rh** - Last reading relative humidity 0-100% (float) a value of -1 indicates no data available 
- **co2** - Last reading CO2 PPM Parts per million to 2 decimals (float) a value of -1 indicates no data available 
- **lastupdate** - Last update from sensor (UTC Timezone datetime) 
- **created** - First online date (UTC Timezone datetime) 
- **location** - Assigned location ID (32-bit signed int) 
- **lat** - GPS latitude Coordinates for location (float)
- **lng** - GPS longitude Coordinates for location (float)
- **public** - Public flag enables fetching of data without login: 0=Private, 1=Public (boolean)

## Minute/Hour Average fields:

- **mid/hid** - minute/hour ID (32-bit signed int) 
- **did** - device ID (32-bit signed int) 
- **pc01** - PC Particle Count bin count for PC0.1 in #/Liter (32-bit signed int) 
- **pc03** -  PC Particle Count bin count for PC0.3 in #/Liter (32-bit signed int) 
- **pc05** -  PC Particle Count bin count for PC0.5 in #/Liter (32-bit signed int) 
- **pc10** -  PC Particle Count bin count for PC1.0 in #/Liter (32-bit signed int) 
- **pc25** -  PC Particle Count bin count for PC2.5 in #/Liter (32-bit signed int) 
- **pc50** -  PC Particle Count bin count for PC5.0 in #/Liter (32-bit signed int) 
- **pc100** - PC Particle Count bin count for PC10.0 in #/Liter (32-bit signed int) 
- **pm01** - PM Particle Mass Value for bin PM0.1 in ug/m3 (float) 
- **pm03** - PM Particle Mass Value for bin PM0.3 in ug/m3 (float) 
- **pm05** - PM Particle Mass Value for bin PM0.5 in ug/m3 (float) 
- **pm10** - PM Particle Mass Value for bin PM1.0 in ug/m3 (float) 
- **pm25** - PM Particle Mass Value for bin PM2.5 in ug/m3 (float) 
- **pm50** - PM Particle Mass Value for bin PM5 in ug/m3 (float) 
- **pm100** - PM Particle Mass Value for bin PM10 in ug/m3 (float) 
- **temp** - Temperature in \*C to 1 decimal (float)   a value of -274 indicates no data available 
- **rh** - Relative Humidity in % to 1 decimal (float) a value of -1 indicates no data available 
- **atm** - Atmospheric Pressure in hPa hecto Pascals to 2 decimals (float) a value of -1 indicates no data available 
- **vocaqi** - Volatile organic compounds AQI score 0-400 (32-bit signed int) a value of -1 indicates no data available 
- **vocppm** - Volatile organic compounds parts per million to 2 decimals (float) a value of -1 indicates no data available 
- **gasest** - Future Use as Gas Estimate: 1=true, false=0 (float) a value of -1 indicates no data available 
- **co2** - CO2 Gas Estimate in parts per million to 2 decimals (float) a value of -1 indicates no data available 
- **location** - location ID (32-bit signed int) a value of -1 indicates no data available 
- **time** - Timestamp of readings in UTC Timezone (datetime) 

## Event fields:

- **eid** - event ID (32-bit signed int) 
- **did** - device ID (32-bit signed int) 
- **type** - event type (string) 
- **time** - event timestamps in UTC Timezone (datetime) 
