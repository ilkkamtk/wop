# Week 5
## Password hash
* [Salted Password Hashing - Doing it Right](https://crackstation.net/hashing-security.htm)

### Bcrypt
1. Setup
   * Continue the app started on week 2. You should be now in `week4` branch. Make sure you have committed all files (`git status`) then create new branch `week5`
   * Install [bcryptjs](https://github.com/dcodeIO/bcrypt.js#bcryptjs): `npm i bcryptjs`
   
1. Use [index4.html](https://raw.githubusercontent.com/ilkkamtk/wop-starters/week2-1/week2_public_html/index4.html) and [main4.js](https://raw.githubusercontent.com/ilkkamtk/wop-starters/week2-1/week2_public_html/js/main4.js) as front-end

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
   
1. Move `user_create_post` function from `controllers/userController.js` to `controllers/authController.js`:
   ```javascript
   const user_create_post = async (req, res, next) => {
     // Extract the validation errors from a request.
     const errors = validationResult(req);
   
     if (!errors.isEmpty()) {
       console.log('user create error', errors);
       res.send(errors.array());
     } else {
       // TODO: bcrypt password
   
       const params = [
         req.body.name,
         req.body.username,
         req.body.password,
       ];
       
       if (await userModel.addUser(params)) {
         next();
       } else {
         res.status(400).json({error: 'register error'});
       }
   
     }
   };
   ```
   * remember to update the requires and exports of both modules
   * now users can be added (registered) without logging in first
   
1. Modify local strategy in `utils/pass.js`:
   ```javascript
   passport.use(new Strategy(
       async (username, password, done) => {
         const params = [username];
         try {
           const [user] = await userModel.getUserLogin(params);
           console.log('Local strategy', user); // result is binary row
           if (user === undefined) {
             return done(null, false, {message: 'No user found.'});
           }
           if (!bcrypt.compareSync(password, user.password)) {
             console.log('here');
             return done(null, false, {message: 'Incorrect password.'});
           }
           delete user.password;
           return done(null, {...user}, {message: 'Logged In Successfully'}); // use spread syntax to create shallow copy to get rid of binary row type
         } catch (err) {
           return done(err);
         }
       }));
   ```

1. Modify ``
   
1. You should have at least two users in your database. Change the passwords in the database to these:
   * $2a$10$5RzpyimIeuzNqW7G8seBiOzBiWBvrSWroDomxMa0HzU6K2ddSgixS
      * this is hashed version of 1234
   * $2a$10$H7bXhRqd68DjwFIVkw3G1OpfIdRWIRb735GvvzCBeuMhac/ZniGba
      * this is hashed version of qwer
      

       

