# Getting started with nodejs

## We will create a simple nodejs app that will return a hello message on a root domain
File: server.js

```javascript
var compression = require('compression');
var express = require('express');

var app = express();
module.exports = app;

app.use(compression());


var path = require('path');
app.use(express.static(path.resolve('./public')));


app.get('/', function (req,res) {
	res.send("Welcome to the https server");
});
```
### Explanation: 

```javascript
var compression = require('compression');
var express = require('express');
```
 Here we import two modules: express and compression. They are included in the package.json file so you can just npm install them. Express is the most commonly used library for nodejs because it allows us to write simple webservers with very minimal code.

  Compression is is library that is known as a "middleware". Middleware are basically plugins for express. 

```javascript
var app = express();
module.exports = app;
```
Here we create an instance of express called app and export it.

```javascript
app.use(compression());
```

 Here we tell express that we would like to use the middleware compression. Compression applies g-zip encoding for our server to reduce the bandwidth and load time if we want to serve large content like pictures. 


```javascript
var path = require('path');
app.use(express.static(path.resolve('./public')));
```
Here we import the middleware path and tell our express instance to serve a folder names public under our root domain. We don't use this for anything in our server right now but if we wanted to say serve an html page, we would place the file in our public folder and would be able to serve it from "/myfile.html"

```javascript
app.get('/', function (req,res) {
	res.send("Welcome to the https server");
});
```
This tells our server to get requests on the path "/" or the root domain. Req represents the request object and res represents the response object. 

```javascript
	res.send("Welcome to the https server");
```
 Here we tell our server to send a response. <br>
 Now if you were to run this js file then nothing would happen. The reson for this is that our server is not listening for any incoming traffic. To tell our server to listen for traffic we would need to include this command: 
```javascript
app.listen(80, () => console.log(`Listening on Port 80`))
```
 This tells our server to listen on port 80. However, we won't add this peice of code because we are going to let greenlock handle the requests

# Introducing greenlock express 

## We will integrate https into our server with greenlock

 Greenlock-express is a wrapper for our server that will issue free https certificates.

### Requirments: 
    1) A domain with proper dns records forwarded to the ip from which you plan to run your server
    2) Properly forwarded ports 443 and 80

### File: greenlock.js

```javascript
"use strict";
 
var app = require("./server.js");
 
require("greenlock-express")
    .init({
        packageRoot: __dirname,
        configDir: "./greenlock.d",
 
        // contact for security and critical bug notices
        maintainerEmail: "ransudz@gmail.com",
 
        // whether or not to run at cloudscale
        cluster: false
    })
    // Serves on 80 and 443
    // Get's SSL certificates magically!
    .serve(app);
```

```javascript
var app = require("./server.js");
```
Here we import the express instance that we created in our server.js file as app.  

```javascript
require("greenlock-express")
    .init({
        packageRoot: __dirname,
        configDir: "./greenlock.d",
 
        // contact for security and critical bug notices
        maintainerEmail: "ransudz@gmail.com",
 
        // whether or not to run at cloudscale
        cluster: false
    })
    // Serves on 80 and 443
    // Get's SSL certificates magically!
    .serve(app);
```

Here is greenlock-express doing magic by automatically creating certificates for our domain. You neeed to modify the greenlock.d/config.json file and the maintainerEmail. 

### File: ./greenlock.d/config.json
```javascript
{ "sites": [{ "subject": "sudz.dev", "altnames": ["sudz.dev"] }] }
```

 This simply tells greenlock what domains we want to create certificates for. I used my domain as an example. 
<br>
 Now assuming that you have your ports forwarded and have setup your dns records, you should be able to run greenlock.js. The file will generate certs and will start to run the server we created in server.js. Navigate to your domain (in my case, sudz.dev) and you should see the hello message that we created earlier. 
<br>
 If you see any errors, you probably have misconfigured your domain
