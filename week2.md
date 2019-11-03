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
   * Modify `./controllers/catController.js` and `/` route in `./routes/catRoutes.js` by using the examples above.
   * `localhost:3000/cat` should now send a json formatted array of cats as a response. Test with Postman.
   
1. Study [map, reduce, filter](https://css-tricks.com/an-illustrated-and-musical-guide-to-map-reduce-and-filter-array-methods/)
   * `/:id` route in `./routes/catRoutes.js` defines which cat's data should be returned. The `id` path variable should be passed from routes to controller:
      * Add a new function `cat_get` to `./controllers/catController.js`. The function should send one cat defined with path variable as json formatted object.
      * You can use _array.filter_ or _array.reduce_ to return one cat whose id property matches to `req.params.id`.
   * `localhost:3000/cat/1` should now send a single cat as a response. Test with Postman.
   
1. 
