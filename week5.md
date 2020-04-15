# Week 5

## HTTPS


* [HTTPS](https://en.wikipedia.org/wiki/HTTPS)
  * HTTP over [TLS/SSL](https://en.wikipedia.org/wiki/Transport_Layer_Security)
    * TLS is the new progression of SSL
  * _authentication_ of the visited website
  * protection of the _privacy_ and _integrity_ of the exchanged data
* Generally better to implement TLS in reverse-proxy such as Apache or Nginx

### express (localhost development)

* Generate keys and certificate with [openssl](https://www.openssl.org/) (in real life, you would need to get certified by a third party, e.g. [Let’s Encrypt](https://letsencrypt.org/))
* If you target [modern compatibility](https://wiki.mozilla.org/Security/Server_Side_TLS#Modern_compatibility), recommendation is to use elliptic curve algorithm [ECDSA](https://en.wikipedia.org/wiki/Elliptic_Curve_Digital_Signature_Algorithm) with prime256v1, secp384r1 or secp521r1 TLS curves (e.g. [see](https://msol.io/blog/tech/create-a-self-signed-ecc-certificate/)) instead of [RSA](https://en.wikipedia.org/wiki/RSA_(cryptosystem))
  * For intermediate compatibility (IE7, Android 2.3, Java 7,...), minimum is 2048 bit RSA key; but you can safely use 4096 key

```shell
$ openssl genrsa -out ssl-key.pem 2048
$ openssl req -new -key ssl-key.pem -out certrequest.csr
$ openssl x509 -req -in certrequest.csr -signkey ssl-key.pem -out ssl-cert.pem
```

* Put the keys and certificate in the app folder and make sure to **add them in .gitignore** too!
  * Alternative, put the keys and certificate outside the app folder and use relative (e.g. ../certs/) or absolute (e.g. /etc/pki/tls/certs/) path to them

```javascript
'use strict';

const express = require('express');
const https = require('https');
const fs = require('fs');

const sslkey = fs.readFileSync('ssl-key.pem');
const sslcert = fs.readFileSync('ssl-cert.pem')

const options = {
      key: sslkey,
      cert: sslcert
};

const app = express();

https.createServer(options, app).listen(8000);

app.get('/', (req, res) => {
  res.send('Hello Secure World!');
});
```

* Eventually, force redirection from HTTP to HTTPS

```javascript
const http = require('http');

http.createServer((req, res) => {
      res.writeHead(301, { 'Location': 'https://localhost:8000' + req.url });
      res.end();
}).listen(3000);
```

Notes:
* about [HTTP status code](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html), the 3XX codes are redirect, 301 means Moved Permanently.
* when testing with your browser, because of self-signed certificate, it will show a warning page instead of your app. In Chrome, click [details and proceed unsafe](https://support.google.com/chrome/answer/99020?co=GENIE.Platform%3DDesktop&hl=en-GB). In Firefox, click [Advanced and Accept the Risk and Continue](https://support.mozilla.org/en-US/kb/what-does-your-connection-is-not-secure-mean).

### express (production server)

1. Generate a self-signed certificate for [CentOS](https://wiki.centos.org/HowTos/Https)
1. configure apache httpd proxy for https: ``sudo nano /etc/httpd/conf.d/https-node.conf`` (or any .conf file)
    ```apacheconf
    <VirtualHost *:443>
        ServerName tunnus-numero.metropolia.fi
        SSLEngine on
        SSLCertificateFile /etc/pki/tls/certs/ca.crt
        SSLCertificateKeyFile /etc/pki/tls/private/ca.key
        SSLProxyCACertificateFile /etc/pki/tls/certs/ca.crt

        SSLProxyEngine on
        SSLProxyCheckPeerCN off
        SSLProxyCheckPeerName off
        ProxyPreserveHost On
        RequestHeader set X-Forwarded-Proto https

        ProxyPass /app/ http://127.0.0.1:3000/
        ProxyPassReverse /app/ http://127.0.0.1:3000/
    </VirtualHost>
    ```
    * Don't forget to restart apche server ``sudo systemctl restart httpd``
    * Note, the proxy pass to http (not https), it's the X-Forwarded-Proto that does it
1. Express app need to trust the tls/ssl configuration from the proxy server
1. Eventually, force the redirection from HTTP to HTTPS

```javascript
'use strict';

// dotenv, db, etc.
const express = require('express');
const app = express();

app.enable('trust proxy');

// Add a handler to inspect the req.secure flag (see
// http://expressjs.com/api#req.secure). This allows us
// to know whether the request was via http or https.
// https://github.com/aerwin/https-redirect-demo/blob/master/server.js
app.use ((req, res, next) => {
  if (req.secure) {
    // request was via https, so do no special handling
    next();
  } else {
    // if express app run under proxy with sub path URL
    // e.g. http://www.myserver.com/app/
    // then, in your .env, set PROXY_PASS=/app
    // Adapt to your proxy settings!
    const proxypath = process.env.PROXY_PASS || ''
    // request was via http, so redirect to https
    res.redirect(301, `https://${req.headers.host}${proxypath}${req.url}`);
  }
});

app.listen(3000);
```

### consider separating development and production code

* create node modules for localhost (development) and remote (production) server
  * cut/paste corresponding code and use node export e.g. for localhost.js

 ```javascript
// cut-pasted code about localhost: require, tls certs, options,...

module.exports = (app, httpsPort, httpPort) => {
  https.createServer(options, app).listen(httpsPort);
  http.createServer(httpsRedirect).listen(httpPort);
};
```

* use .env file to choose which code to load

```javascript
process.env.NODE_ENV = process.env.NODE_ENV || 'development';
if (process.env.NODE_ENV === 'production') {
  const prod = require('./production')(app, process.env.PORT);
} else {
  const localhost = require('./localhost')(app, process.env.HTTPS_PORT, process.env.HTTP_PORT);
}
app.get('/', (req, res) => {
  res.send('Hello Secure World!');
});
```


## Password hash
* [Salted Password Hashing - Doing it Right](https://crackstation.net/hashing-security.htm)

### Bcrypt
1. Setup
   * Continue the app started on week 2. You should be now in `week4` branch. Make sure you have committed all files (`git status`) then create new branch `week5`
   * Install [bcryptjs](https://github.com/dcodeIO/bcrypt.js#bcryptjs): `npm i bcryptjs`

1. Use [index4.html](https://raw.githubusercontent.com/ilkkamtk/wop-starters/week2-1/week2_public_html/index4.html) and [main4.js](https://raw.githubusercontent.com/ilkkamtk/wop-starters/week2-1/week2_public_html/js/main4.js) as front-end

### Login

1. Modify local strategy in `utils/pass.js`:
   ```javascript
   passport.use(new Strategy(
       async (username, password, done) => {
         const params = [username];
         try {
           const [user] = await userModel.getUserLogin(params);
           console.log('Local strategy', user); // result is binary row
           if (user === undefined) { // user not found
             return done(null, false);
           }
           // TODO: use bcrypt to check of passwords don't match
           if (password !== user.password) { // passwords dont match
             console.log('here');
             return done(null, false);
           }
           delete user.password; // remove password propety from user object
           return done(null, {...user}); // use spread syntax to create shallow copy to get rid of binary row type
         } catch (err) { // general error
           return done(err);
         }
       }));
   ```

1. Modify `getUserLogin` function in `models/userModel.js`:
   ```javascript
   const getUserLogin = async (params) => {
     try {
       console.log(params);
       const [rows] = await promisePool.execute(
           'SELECT * FROM wop_user WHERE email = ?;',
           params);
       return rows;
     } catch (e) {
       console.log('error', e.message);
     }
   };
   ```

1. Test login with index4.html

1. Logout by deleting the token from browser's session storage (developer tools/application)

1. You should have at least two users in your database. Change the passwords in the database to these:
   * $2a$10$5RzpyimIeuzNqW7G8seBiOzBiWBvrSWroDomxMa0HzU6K2ddSgixS
      * this is a hashed version of 1234
   * $2a$10$H7bXhRqd68DjwFIVkw3G1OpfIdRWIRb735GvvzCBeuMhac/ZniGba
      * this is a hashed version of qwer

1. Require bcryptjs in `utils/pass.js` and modify the if statement under TODO to use [compareSync](https://github.com/dcodeIO/bcrypt.js#comparesyncs-hash) or better ``await compare(...)`` to check password

1. Test login with index4.html

### Register

1. Delete `/user` route for post method in `/routes/userRoute.js`
   * we'll move adding users (registration) to `/routes/authRoute.js`:
   ```javascript
   'use strict';
   const express = require('express');
   const router = express.Router();
   const {body, sanitizeBody} = require('express-validator');
   const authController = require('../controllers/authController');

   router.post('/login', authController.login);
   router.get('/logout', authController.logout);
   router.post('/register',
       [
         body('name', 'minimum 3 characters').isLength({min: 3}),
         body('username', 'email is not valid').isEmail(),
         body('password', 'at least one upper case letter').
             matches('(?=.*[A-Z]).{8,}'),
         sanitizeBody('name').escape(),
       ],
       authController.user_create_post,
       authController.login,
   );

   module.exports = router;
   ```
   * note the addition on third middelware `login` in `/register` route. Why is it there?

1. Delete `user_create_post` function from `controllers/userController.js` and add following to `controllers/authController.js`:
   ```javascript
   const user_create_post = async (req, res, next) => {
     // Extract the validation errors from a request.
     const errors = validationResult(req); // TODO require validationResult, see userController

     if (!errors.isEmpty()) {
       console.log('user create error', errors);
       res.send(errors.array());
     } else {
       // TODO: bcrypt password

       const params = [
         req.body.name,
         req.body.username,
         req.body.password, // TODO: save hash instead of the actual password
       ];

       if (await userModel.addUser(params)) {
         next();
       } else {
         res.status(400).json({error: 'register error'});
       }
     }
   };

   const logout = (req, res) => {
     req.logout();
     res.json({message: 'logout'});
   };

   ```
   * remember to update the requires and exports of both files
   * now users can be added (registered) without logging in first
   * Complete the TODOs in the code above
   * Create a new user by using the register form in index4.html
   * Test logout button and login again

## Create thumbnails
1. Use [index5.html](https://raw.githubusercontent.com/ilkkamtk/wop-starters/week2-1/week2_public_html/index5.html), [main5.js](https://raw.githubusercontent.com/ilkkamtk/wop-starters/week2-1/week2_public_html/js/main5.js), [mapbox.js](https://raw.githubusercontent.com/ilkkamtk/wop-starters/week2-1/week2_public_html/js/mapbox.js) and [style5.css](https://raw.githubusercontent.com/ilkkamtk/wop-starters/week2-1/week2_public_html/css/style5.css) as front-end for testing
   * ask mapbox key from the teacher or create your own
1. Add new folder `thumbnails`
1. Add to `app.js`:
   ```javascript
   app.use('/thumbnails', express.static('thumbnails'));

1. Install [sharp](https://github.com/lovell/sharp): `npm i sharp`
1. Add new file `utils/resize.js`:
   ```javascript
   'use strict';
   const sharp = require('sharp');

   const makeThumbnail = async (file, thumbname) => { // file = full path to image (req.file.path), thumbname = filename (req.file.filename)
     // TODO: use sharp to create a png thumbnail of 160x160px, use async await
   };

   module.exports = {
     makeThumbnail,
   };
   ```
1. Complete TODO in the code above. Call `makeThumbnail` function in `catController` before you add the cat to database.
1. Upload a new cat in index5.html

## Metadata from image
1. Study [Creating a Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise#Creating_a_Promise)
1. Take a couple of photos with your phone. Make sure that location services are enabled so that the gps data is stored in the images.
1. Email the images to yourself
1. Add new field `coords` of type text to `wop_cat` table
1. Add this as the value for `coords` to existing cats in the table: [24.74,60.24]
1. Modify `addCat` function in `models/catModel.js`:
   ```javascript
   const addCat = async (params) => {
     try {
       const [rows] = await promisePool.execute(
           'INSERT INTO wop_cat (name, age, weight, owner, filename, coords) VALUES (?, ?, ?, ?, ?, ?);',
           params);
       return rows;
     }
     catch (e) {
       console.log('error', e.message);
     }
   };
   ```
1. Modify `cat create post` in `catController.js`:
   ```javascript
   const cat_create_post = async (req, res) => {
    ...
       try {
         // make thumbnail
         resize.makeThumbnail(req.file.path, req.file.filename);
         // get coordinates
         const coords = await imageMeta.getCoordinates(req.file.path);
         console.log('coords', coords);
         // add to db
         const params = [
           req.body.name,
           req.body.age,
           req.body.weight,
           req.body.owner,
           req.file.filename,
           coords,
         ];
         const cat = await catModel.addCat(params);
         await res.json({message: 'upload ok'});
       }
       catch (e) {
         console.log('exif error', e);
         res.status(400).json({message: 'error'});
       }
     ...
   };
   ```
1. Install [node-exif](https://github.com/gomfunkel/node-exif#readme): `npm i exif`
1. Add new file `utils/imageMeta.js`:
   ```javascript
   'use strict';
   const ExifImage = require('exif').ExifImage;

   const getCoordinates = (imgFile) => { // imgFile = full path to uploaded image
     return new Promise((resolve, reject) => {
       try {
         // TODO: Use node-exif to get longitude and latitude from imgFile
         // coordinates below should be an array of GPS coordinates in decimal format: [longitude, latitude]
         resolve(coordinates);
       }
       catch (error) {
         reject(error);
       }
     });
   };

   // convert GPS coordinates to decimal format
   // for longitude, send exifData.gps.GPSLongitude, exifData.gps.GPSLongitudeRef
   // for latitude, send exifData.gps.GPSLatitude, exifData.gps.GPSLatitudeRef
   const gpsToDecimal = (gpsData, hem) => {
     let d = parseFloat(gpsData[0]) + parseFloat(gpsData[1] / 60) +
         parseFloat(gpsData[2] / 3600);
     return (hem === 'S' || hem === 'W') ? d *= -1 : d;
   };

   module.exports = {
     getCoordinates,
   };
   ```
1. Require `utils/imageMeta.js` as `imageMeta` in `catController.js`
1. Complete TODO in `utils/imageMeta.js`
1. Upload cat in index5.html to test

### Upload to virtual computer and run
1. Upload final app to your virtual computer
   * don't upload node_modules
   * after uploading, run `npm install`
   * edit .env if neccessary and run `node app.js` or `nodemon app.js`
   * modify app.js so that app runs via https
