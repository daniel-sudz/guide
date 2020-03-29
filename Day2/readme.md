# Hosting custom html pages with express

## We will now make our server from day1 display a custom html page
File: server.js

```js
var compression = require('compression');
var express = require('express');

var app = express();
module.exports = app;

app.use(compression());

var path = require('path');
app.use(express.static(path.resolve('./public')));


app.get('/', (req, res) => {
	res.sendfile('public.html', { root: __dirname + "/public/index.html" } );
});
```
```
app.use(express.static(path.resolve('./public')));
```

As from before, this command allows us to serve content from a folder named public as part of the root path. So if we have a file named mysite.html placed in the public folder, this command will allow us to access that file under mysite.com/myfile.html. Similarly, if there is a file names index.css in the public folder we can reference it in our html file as './index.css'.
<br>
<br>
Now we will place two files into the public folder: 
### File: public/index.html

```html
<!DOCTYPE html>
<html>

<head>
  <meta charset="utf-8">
  <title></title>
  <meta name="author" content="">
  <meta name="description" content="">
  <meta name="viewport" content="width=device-width, initial-scale=1">

  <link href="./index.css" rel="stylesheet">
</head>

<body>
   <div class="testcss">
        <p>Hello, world!</p>
  </div>
</body>

</html>
```
### File public/index.css
```css
.testcss {
  color: green;
}
```
```js
app.get('/', (req, res) => {
	res.sendfile('public.html', { root: __dirname + "/public/index.html" } );
});
```
Here we tell express to send /public/index.html when a user requests mysite.com.
<br>
<br>
If you setup up greenlock, you should be able to run greenlock.js. If you have not, add in the listening code from day 1 and you will be able to run server.js. 