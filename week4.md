# Week 4
## Authentication with [passport.js](http://www.passportjs.org/docs/downloads/html/)
1. Setup
   * Continue last week's `state-management` exercise. Make sure you have committed all files (`git status`) then create new branch `passport`
   * Install passport and passport-local `npm i passport passport-local`
   
### [Local Strategy](http://www.passportjs.org/docs/username-password/)
1. Modify `app.js`:
   ```javascript
   // add following to about line 4
   const passport = require('./utils/pass');
   
   // add following after const port = 3000;
   const loggedIn = (req, res, next) => {
     if (req.user) {
       next();
     } else {
       res.redirect('/form');
     }
   };
   
   // add following after app.use(session...
   app.use(passport.initialize());
   app.use(passport.session());
   
   // modify app.post('/login',...
   app.post('/login',
       passport.authenticate('local', {failureRedirect: '/form'}),
       (req, res) => {
         console.log('success');
         res.redirect('/secret');
       });
   
   // modify app.get('/secret',...
   app.get('/secret', loggedIn, (req, res) => {
     res.render('secret');
   });
   ```
1. Add new folder `utils` and create new file `./utils/pass.js`
   ```javascript
   'use strict';
      const passport = require('passport');
      const Strategy = require('passport-local').Strategy;
      
      // fake database: ****************
      const users = [
        {
          user_id: 1,
          name: 'Foo Bar',
          email: 'foo@bar.fi',
          password: 'foobar',
        },
        {
          user_id: 2,
          name: 'Bar Foo',
          email: 'bar@foo.fi',
          password: 'barfoo',
        },
      ];
      // *******************
   
      // fake database functions *********
       const getUser = (id) => {
         const user = users.filter((usr) => {
           if (usr.user_id === id) {
             return usr;
           }
         });
         return user[0];
       };
       
       const getUserLogin = (email, pass) => {
         const user = users.filter((usr) => {
           if (usr.email === email && usr.password === pass) {
             return usr;
           }
         });
         return user[0];
       };
       // *****************
       
      // serialize: store user id in session 
      passport.serializeUser((id, done) => {
        console.log('serialize', id);
        // serialize user id by adding it to 'done()' callback
      });
      
      // deserialize: get user id from session and get all user data
      passport.deserializeUser(async (id, done) => {
          // get user data by id from getUser
          console.log('deserialize', user);
          // deserialize user by adding it to 'done()' callback
      });
      
      passport.use(new Strategy(
          (username, password, done) => {
            // get user by username and password from getUserLogin
            // if user is undefined 
            // return done(null, false);
            // else
            // return done(null, user.user_id);
          }
      ));
      
      module.exports = passport;
   ```
1. Follow the comments in the code above to finish the task
1. Run with nodemon and open localhost:3000 to test
1. Add [logout](http://www.passportjs.org/docs/logout/) functionality
1. Commit and push to git

### [JSON Web Tokens Strategy](http://www.passportjs.org/packages/passport-jwt/)
* [JSON Web Tokens](https://jwt.io/)
* [Session vs Token](https://medium.com/@sherryhsu/session-vs-token-based-authentication-11a6c5ac45e4)

* The following task is based on [this](https://medium.com/front-end-weekly/learn-using-jwt-with-passport-authentication-9761539c4314) article

1. Setup
   * Continue the app started on week 2. You should be now in `week3` branch. Make sure you have committed all files (`git status`) then create new branch `week4`
   * Install passport, passport-local and passport-jwt `npm i passport passport-local passport-jwt`
   
1. Create a function getUserLogin to `./models/userModel.js`
   ```javascript
   ...
   const getUserLogin = async (params) => {
     try {
       console.log(params);
       const [rows] = await promisePool.execute(
           'SELECT user_id, name, email FROM wop_user WHERE email = ? AND password = ?;',
           params);
       return rows;
     } catch (e) {
       console.log('error', e.message);
     }
   };
   ...
   ```
1. Add new file `./controllers/authController.js`
   ```javascript
   'use strict';
   const jwt = require('jsonwebtoken');
   const passport = require('passport');
   
   const login = (req, res) => {
     // TODO: add passport authenticate
   };
   
   module.exports = {
     login,
   };

   ```
1. Study [this](https://medium.com/front-end-weekly/learn-using-jwt-with-passport-authentication-9761539c4314#025a) example and add passport local strategy authentication to TODO section
   * the example starts with passport.authenticate...
   
1. Add new file `./routers/authRouter.js`
   ```javascript
   'use strict';
   const express = require('express');
   const router = express.Router();
   const passport = require('../passport/passport');
   const authController = require('../controllers/authController');
   
   router.post('/login', passport.authenticate('local'), authController.login);
   
   module.exports = router;
   ```
1. Require `./utils/pass.js` and `./routes/authRoute.js` in `app.js`

1. Add `passport.authenticate('jwt', {session: false})` [middleware](https://medium.com/front-end-weekly/learn-using-jwt-with-passport-authentication-9761539c4314#dfa8) to `/cat` and `/user` routes.

1. Add new folder `utils` and create new file `./utils/pass.js`
   ```javascript
   'use strict';
   const passport = require('passport');
   const Strategy = require('passport-local').Strategy;
   const userModel = require('../models/userModel');
   
   // local strategy for username password login
   passport.use(new Strategy(
       async (username, password, done) => {
         const params = [username, password];
         try {
           const [user] = await userModel.getUserLogin(params);
           console.log('Local strategy', user); // result is binary row
           if (user === undefined) {
             return done(null, false, {message: 'Incorrect email or password.'});
           }
           return done(null, {...user}, {message: 'Logged In Successfully'}); // use spread syntax to create shallow copy to get rid of binary row type
         } catch (err) {
           return done(err);
         }
       }));
   
   // TODO: JWT strategy for handling bearer token
   
   
   module.exports = passport;
   ```
1. Study [this](https://medium.com/front-end-weekly/learn-using-jwt-with-passport-authentication-9761539c4314#fcdf) example and add JWT strategy to TODO section
   * use arrow functions
   * instead of `cb`, use `done` as the name for the callback function
   * use the local strategy for username password login in the code above as an example

1. Test with postman
   * start with login: POST, localhost:3000/auth/login
   * after login is succesful, copy the token from response
   * test token with GET, localhost:3000/cat and localhost:3000/user
      * add token to Authorization tab, choose TYPE/Bearer Token
