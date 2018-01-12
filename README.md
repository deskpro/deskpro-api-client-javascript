Deskpro API JavaScript Client
=============================
JavaScript client for use with the Deskpro API.

[![Build Status](https://travis-ci.org/deskpro/deskpro-api-client-javascript.svg?branch=master)](https://travis-ci.org/deskpro/deskpro-api-client-javascript)

* [Installing](#installing)
* [Basic Usage](#basic-usage)
* [Browser Usage](#browser-usage)
* [Default Headers](#default-headers)
* [Logging](#logging)
* [Axios](#axios)
* [Testing](#testing)

## Installing

```
npm install --save @deskpro/deskpro-api-client-javascript
```

## Basic Usage

```js
const DeskproClient = require('@deskpro/deskpro-api-client-javascript');

// Create a new client with the URL to your Deskpro instance.
const client = new DeskproClient('http://deskpro-dev.com');

// Set the ID of the user to authenticate, and either the auth
// key or token.
// client.setAuthKey(1, 'dev-admin-code');
// client.setAuthToken(1, 'AWJ2BQ7WG589PQ6S862TCGY4');

client.get('/articles')
  .then((resp) => {
    console.log(resp.data);
    console.log(resp.meta);
  })
  .catch((err) => {
    console.error(err.message);
  });
```

#### Posting values

```js
const DeskproClient = require('@deskpro/deskpro-api-client-javascript');

const client = new DeskproClient('http://deskpro-dev.com');
client.setAuthKey(1, 'dev-admin-code');

const body = {
  title: 'This is a title',
  content: 'This is the content',
  content_input_type: 'rte',
  status: 'published'
};

client.post('/articles', body)
  .then((resp) => {
    console.log(resp.data);
  })
  .catch((err) => {
    console.error(err.message);
  });
```

#### Uploading files

Sending values using multipart/form-data, for instance to upload files, is done by passing an object with the property "multipart" to the `post()` or `put()` methods.

```js
const DeskproClient = require('@deskpro/deskpro-api-client-javascript');
const FormData = require('form-data');
const fs = require('fs');

const client = new DeskproClient('http://deskpro-dev.com');
client.setAuthKey(1, 'dev-admin-code');

// Create a form with the file data to upload.
const fileStream = fs.createReadStream('test.gif');
const form = new FormData();
form.append('file', fileStream, 'test.gif');

// Create body data containing the property 'multipart', which
// contains the form data.
const body = {
  multipart: form
};

client.post('/blobs/temp', body)
  .then((resp) => {
    console.log(resp.data);
  })
  .catch((err) => {
    console.error(err.message);
  });
```

#### Batch request

```js
const DeskproClient = require('@deskpro/deskpro-api-client-javascript');

const client = new DeskproClient('http://deskpro-dev.com');
client.setAuthKey(1, 'dev-admin-code');

client.batch({
  one: {
    method: 'GET',
    url: '/articles/105'
  },
  two: '/articles/106'
}).then((resp) => {
    console.log(resp.one.data);
    console.log(resp.two.data);
  })
  .catch((err) => {
    console.error(err.message);
  });
```

#### Interpolating URLs

```js
const DeskproClient = require('@deskpro/deskpro-api-client-javascript');

const client = new DeskproClient('http://deskpro-dev.com');
client.setAuthKey(1, 'dev-admin-code');

const params = {
  id:       5,
  parentId: 101,
  limit:    25,
  offset:   100
};

// The params are interplated into the endpoint URL so it becomes:
// "/articles/101/5?limit=25&offset=100"
client.get('/articles/{parentId}/{id}', params);
```

## Browser Usage
DeskproClient may also be used in the browser.

```html
<!DOCTYPE>
<html>
  <head>
    <!-- Import DeskproClient from node_modules -->
    <script src="node_modules/@deskpro/deskpro-api-client-javascript/dist/index.js"></script>
    
    <!-- Or import it from CDN -->
    <!-- <script src="https://unpkg.com/@deskpro/deskpro-api-client-javascript@2.0.0/dist/index.js"></script> -->
  </head>
  <body>
    <script>
      var client = new DeskproClient('http://deskpro-dev.com');
      client.setAuthKey(1, 'dev-admin-code');

      client.get('/articles')
          .then(function(resp) {
            console.log(resp.data);
          })
          .catch(function(err) {
            console.error(err.message);
          });
    </script>
  </body>
</html>
```

## Default Headers
Use the `setDefaultHeaders()` method to set headers that get sent with each request.

```js
const DeskproClient = require('@deskpro/deskpro-api-client-javascript');

const client = new DeskproClient('http://deskpro-dev.com');
client.setAuthKey(1, 'dev-admin-code');
client.setDefaultHeaders({
  'X-Custom-Value': 'foo'
});

client.get('/articles')
  .then((resp) => {
    console.log(resp.data);
  })
  .catch((err) => {
    console.error(err.message);
  });
```

## Logging
Pass a function to the `setLogger()` method to enable request logging.

```js
const DeskproClient = require('@deskpro/deskpro-api-client-javascript');

const client = new DeskproClient('http://deskpro-dev.com');
client.setAuthKey(1, 'dev-admin-code');
client.setLogger(console.log);

client.get('/articles')
  .then((resp) => {
    console.log(resp.data);
  })
  .catch((err) => {
    console.error(err.message);
  });
```

## Axios

```js
const DeskproClient = require('@deskpro/deskpro-api-client-javascript');

const client = new DeskproClient('http://deskpro-dev.com');
client.setAuthKey(1, 'dev-admin-code');

// @see https://github.com/axios/axios#global-axios-defaults
client.getHTTPClient().defaults.timeout = 2500;

client.get('/articles')
  .then((resp) => {
    console.log(resp.data);
  })
  .catch((err) => {
    console.error(err.message);
  });
```

For debugging purposes, the request, response, and exception generated by the last operation may be retrieved for inspection.

```js
const DeskproClient = require('@deskpro/deskpro-api-client-javascript');

const client = new DeskproClient('http://deskpro-dev.com');
client.setAuthKey(1, 'dev-admin-code');

client.get('/articles')
  .then(() => {
    console.log(client.getLastHTTPRequest());
    console.log(client.getLastHTTPResponse());
    console.log(client.getLastHTTPRequestException());
  });
```

## Testing
Tests are run using the npm "test" script.

```
npm test
```
