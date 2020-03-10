# Week 5
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
      * this is hashed version of 1234
   * $2a$10$H7bXhRqd68DjwFIVkw3G1OpfIdRWIRb735GvvzCBeuMhac/ZniGba
      * this is hashed version of qwer
      
1. Require bcryptjs in `utils/pass.js` and modify the if statement under TODO to use [compareSync](https://github.com/dcodeIO/bcrypt.js#comparesyncs-hash) to check password

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
