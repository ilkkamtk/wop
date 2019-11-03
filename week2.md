# Week 2
## Routing
1. Setup
   * Navigate to your course folder in terminal or Git Bash
   * Create new Git branch. `git checkout -b week2`
   * Create `week2` folder to your course folder: `mkdir week2`
   * Add approriate `.gitignore to `week2` folder` and commit changes to code regularly
   * Create `routing` folder to `week2` folder
   * [Download starter files as zip](https://github.com/ilkkamtk/wop-starters/tree/week2-1), extract and copy all files and folders to `week2` folder
   * Move `week2_public_html` folder to your public_html in shell.metropolia.fi or in your virtual computer. You can rename the folder if you want. (This folder is needed later.)
   * Run `npm install` in `week2` folder
   * Run `nodemon app.js` or `node app.js` and test in browser: `localhost:3000/cat`
   
1. Study [Routing](https://expressjs.com/en/guide/routing.html) and [app.METHOD](https://expressjs.com/en/4x/api.html#app.METHOD)
   * Add `cat` routes to `app.js` which respond to POST, PATCH and DELETE methods.
     * POST response: `With this endpoint you can add cats.`
     * PATCH response: `With this endpoint you can edit cats.`
     * DELETE response: `With this endpoint you can delete cats.`
   * Test the routes with [Postman](https://www.getpostman.com/)
   
1. Study [route parameters](https://expressjs.com/en/guide/routing.html#route-parameters)
   * Use [Path variable](https://medium.com/@fullsour/when-should-you-use-path-variable-and-query-parameter-a346790e8a6d) to get a cat with a specific id from `./models/catModel.js`
     * Add new route `cat/:id` which responds to GET method
     * If the request is `cat/3`, the response should be `You reqested a cat whose id is 3`
     
1. Study [Router](https://expressjs.com/en/guide/routing.html#express-router) and create a route handler for `cat` path. 
   * Move `cat` routes from `app.js` to `./routes/catRouter.js`
   * Test the routes with [Postman](https://www.getpostman.com/)
   * `./routes/catRouter.js` is a [module](https://stackify.com/node-js-module-exports/)
   * _Optional:_ Read this article about [design patterns](https://medium.com/code-to-express/tricky-design-patterns-in-javascript-part-1-ad7228d8db4d)
   
1. Study [MVC](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller)
   * Here is an example of MVC in express:
   ```javascript
   // Model (usually gets data from database, in this case data is hard coded)
   'use strict';
   const cats = [
     {
       id: '1',
       name: 'Frank',
       age: '6',
       weight: '5',
       owner: '1',
       filename: 'http://placekitten.com/400/300',
     },
     {
       id: '2',
       name: 'James',
       age: '4',
       weight: '11',
       owner: '2',
       filename: 'http://placekitten.com/400/302',
     },
   ];
   
   module.exports = {
     cats,
   };
   ```
   ```javascript
   // Controller
   'use strict';
   const catModel = require('../models/catModel');
   
   const cats = catModel.cats;
   
   const cat_list_get = (req, res) => {
     res.json(cats);
   };
      
   module.exports = {
     cat_list_get,
   };
   ```
   ```javascript
   // Router
   'use strict';
   const express = require('express');
   const router = express.Router();
   const catController = require('../controllers/catController');
   
   router.get('/', catController.cat_list_get);
   
   module.exports = router;
   ```
   * Modify `./controllers/catController.js` and `/` route for GET method in `./routes/catRoutes.js` by using the examples above.
   * `localhost:3000/cat` should now send a json formatted array of cats as a response. Test with Postman.
   
1. Study [map, reduce, filter](https://css-tricks.com/an-illustrated-and-musical-guide-to-map-reduce-and-filter-array-methods/)
   * `/:id` route for GET method in `./routes/catRoutes.js` defines which cat's data should be returned. The `id` path variable should be passed from routes to controller:
      * Add a new function `cat_get` to `./controllers/catController.js`. The function should send one cat defined with path variable as json formatted object.
      * You can use _array.filter_ or _array.reduce_ to return one cat whose id property matches to `req.params.id`.
   * `localhost:3000/cat/1` should now send a single cat as a response. Test with Postman.
   
1. User route
   * Use cat router and controller as an example and create new router and controller for `user` routes. The model already exists in `./models` folder
   * Add all the same features as in cat route: 
      * `localhost:3000/user` lists all users
      * `localhost:3000/user/1` returns one user by id.
      * _extra:_ delete password poperty from user's data before sending.
  * Test with Postman.
  * Also now `./week2_public_html/index.html` should work when you open it locally.
  
## Middleware
1. Study [writing middleware](https://expressjs.com/en/guide/writing-middleware.html) and [using middleware](https://expressjs.com/en/guide/using-middleware.html)
1. Study [req.body](https://expressjs.com/en/4x/api.html#req.body) on receiving and using data sent by e.g. form
   * `index.html` contains a form that sends userdata with POST method to `http://localhost:3000/user` endpoint.
   * Modify `/` route for post method in `./routes/userRoutes.js`. The route should log the user data sent by the form to the console. Test with Postman and with the form in `index.html`
1. Files are sent in HTTP as [multipart/form-data](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods/POST). Because Express does not handle this type by default you need to use third party middelware like [Multer](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods/POST) 
   * Add another form to `index.html` for uploading file
   * Add `uploads` folder to `week2` and use the 'Basic usage example' in Multer's documentation as an example and add file upload functionality to `./routes/catRoutes.js`. Use `/` for POST method as route.
1. Earlier you moved front end files (html, css etc.) to public_html. If you test the index.html in public_html, you'll get [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) errors in your browser's console. To fix cross-origin issue, you need te enable CORS in your Express app with [these instructions](https://enable-cors.org/server_expressjs.html). 


