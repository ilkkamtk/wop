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
1. Install passport and passport-local `npm i passport passport-local`
1. Add new folder `utils` and create new file `./utils/pass.js`
   ```javascript
   
   ```


