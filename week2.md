# Week 2
## Routing
1. Setup
   * Navigate to your course folder in terminal or Git Bash
   * Create `week2` folder to your course folder: `mkdir week2`
   * Start new git repository to `week2` folder
   * Add approriate `.gitignore to `week2` folder` and commit changes to code regularly
   * [Download starter files as zip](https://github.com/ilkkamtk/wop-starters/tree/week2-1), extract and copy all files and folders to `week2` folder
   * Move `week2_public_html` folder to your public_html in shell.metropolia.fi or in your virtual computer. You can rename the folder if you want. (This folder is needed later.)
   * Run `npm install` in `week2` folder
   * Run `nodemon app.js` or `node app.js` and test in browser: `localhost:3000/cat`
   
1. Study [Routing](https://expressjs.com/en/guide/routing.html) and [app.METHOD](https://expressjs.com/en/4x/api.html#app.METHOD)
   * Add `cat` routes to `app.js` which respond to POST, PUT and DELETE methods.
     * POST response: `With this endpoint you can add cats.`
     * PUT response: `With this endpoint you can edit cats.`
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
  
## Middleware
1. Study [writing middleware](https://expressjs.com/en/guide/writing-middleware.html) and [using middleware](https://expressjs.com/en/guide/using-middleware.html)

1. Earlier you moved front end files (html, css etc.) to public_html. If you test the index.html in public_html, you'll get [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) errors in your browser's console. To fix cross-origin issue, you need te enable CORS in your Express app with [these instructions](https://expressjs.com/en/resources/middleware/cors.html). 
   * Test that `index.html` works when you run it locally and from public_html

1. Study [req.body](https://expressjs.com/en/4x/api.html#req.body) on receiving and using data sent by e.g. form
   * `index.html` contains a form that sends userdata with POST method to `http://localhost:3000/user` endpoint.
   * Modify `/` route for post method in `./routes/userRoutes.js`. The route should log the user data sent by the form to the console. Test with Postman and with the form in `index.html`

1. Files are sent in HTTP as [multipart/form-data](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods/POST). Because Express does not handle this type by default you need to use third party middelware like [Multer](https://github.com/expressjs/multer) 
   * Add another form to `index.html` for uploading file
   * Add `uploads` folder to `week2` and use the 'Basic usage example' in Multer's documentation as an example and add file upload functionality to `./routes/catRoutes.js`. Use `/` for POST method as route.
   * Check `uploads` folder after uploading to see new files. 
      * Filenames are automatically hashed.
      * If you want to have more control over filenames use [Diskstorage](https://github.com/expressjs/multer#diskstorage)
      
1. Add all files to git, commit and push
   
## Database connection
1. Create new Git branch. `git checkout -b database`
1. Goal of this task is to modify the models so that the data comes from database instead of hard coded arrays.
1. Run the SQL from `tables.txt` in your database (phpMyAdmin in your virtual computer or [users.metropolia.fi/phpMyAdmin](http://users.metropolia.fi/phpMyAdmin))
1. Study [Node MySQL 2](https://github.com/sidorares/node-mysql2#readme)
    * install Node MySQL 2 as a dependency
    * also install [dotenv](https://github.com/motdotla/dotenv#readme) as a dependency
       * `.env` (dotenv) is used to store sensitive data such as database username and password. Add `.env` to `.gitignore` so that it won't be pushed to GitHub.
       * follow the instructions of dotenv to create `.env` file
       * for virtual computer host is localhost or the ip-address
       * for metropolia's database host is mysql.metropolia.fi
1. Add new folder `database` and add there a new file `db.js`
   * Instead of regular [connection](https://github.com/sidorares/node-mysql2#first-query) we'll use [connection pool](https://github.com/sidorares/node-mysql2#using-connection-pools):
   ```javascript
   'use strict';
   const mysql = require('mysql2');
   require('dotenv').config();
   
   const pool = mysql.createPool({
     host: process.env.DB_HOST,
     user: process.env.DB_USER,
     password: process.env.DB_PASS,
     database: process.env.DB_NAME,
     waitForConnections: true,
     connectionLimit: 10,
     queueLimit: 0,
   });
   
   module.exports = pool;
   ```
   * Example query in a model using a [promise](https://github.com/sidorares/node-mysql2#using-promise-wrapper):
   ```javascript
   // ./models/catModel.js
   'use strict';
   const pool = require('../database/db');
   const promisePool = pool.promise();
   
   const getAllCats = async () => {
     try {
       const [rows] = await promisePool.query('SELECT * FROM wop_cat');
       return rows;
     } catch (e) {
       console.log('error', e.message);
     }   
   };
   
   module.exports = {
     getAllCats,
   };
   ```
   * Use getAllCats() in controller:
   ```javascript
   // ./controllers/catController.js
   ...
   const cat_list_get = async (req, res) => {
     const cats = await catModel.getAllCats();
     res.json(cats);
   };
   ...
   ```
   * More on [async/await](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function)
   * Why does the server hang if there is an error with database query? How to prevent that?
 
1. [Prepared statements](https://github.com/sidorares/node-mysql2#using-prepared-statements)
   * Create a function getCat to `./models/catModel.js`
   * The function should make a prepared statement which executes this query: `SELECT * FROM wop_cat WHERE cat_id = X`
      * X is the path variable from route `/:id` of `./routes/catRoute.js`
   * Modify cat_get function in `./controllers/catController.js` so that it sends the path variable 'id' to getCat function of `./models/catModel.js` and then sends the data of single cat as json. Use cat_list_get function as an example. 

1. Make user route to use database as well
   * Use cat model, router and controller as an example and modify user routes etc.
   * Add all the same features as in cat route:
      * localhost:3000/user lists all users
      * localhost:3000/user/1 returns one user by id.
      * _extra:_ delete password poperty from user's data before sending (= don't include it to query results).

1. Use the routes for POST method to add users and cats to database
   * Saving data to a database is mostly the same JavaScript as selecting data from a database. The difference is in the SQL query. Instead of SELECT use INSERT.
   * Adding a new user:
      * use the Add user form in `index.html` for testing
      * add a new function addUser to `./models/userModel.js`
         * the function should add user to the database
      * add a new function user_create_post to `./controllers/userController.js`
         * the function should receive req.body and send it to addUser function of `./models/userModel.js`
      * modify `/` route for POST method in `./routes/userRoute.js` to call user_create_post function
      * fill the Add user form in `index.html`, send the form and check in phpMyAdmin whether a new user has been added.
   * Adding a new cat
      * Modify the file upload form you did earlier in `index.html`. Add text inputs for name, age, weight and owner
      * In the server code:
         * save the file with multer to `uploads` folder (this is already done earlier)
         * _next_ save name, age, weight, owner and [filename](https://github.com/expressjs/multer#file-information) to database. Modify model, controller and route the same way as in adding a new user.
         * Hint: You can add the filename to req.body
      * Testing:
         * Fill the form and send, check phpMyAdmin
         * Reload `index.html`
         
1. Better UX
   * Save these files to week2_public_html
      * [index2.html](https://raw.githubusercontent.com/ilkkamtk/wop-starters/week2-1/week2_public_html/index2.html)
      * [main2.js to /js](https://raw.githubusercontent.com/ilkkamtk/wop-starters/week2-1/week2_public_html/js/main2.js)
      * [serialize.js to /js](https://raw.githubusercontent.com/ilkkamtk/wop-starters/week2-1/week2_public_html/js/serialize.js)
      * [style2.js to /css](https://raw.githubusercontent.com/ilkkamtk/wop-starters/week2-1/week2_public_html/css/syle2.css)
   * open index2.html in your browser and test (see also browser's console)
   
1. Use the `/` route for PUT method in `./routes/catRoute.js` to update cats in database
   * Updating data in a database is mostly the same JavaScript as saving data to a database. The difference is in the SQL query. Instead of INSERT use UPDATE.
   * Modifying a cat:
      * add a new function updateCat to `./models/catModel.js`
         * the function should modify all cat data of the selected cat (cat_id) in the database except filename 
      * add a new function cat_update_put to `./controllers/catController.js`
         * the function should receive _name_, _age_, _weight_, _owner_ and _id_ in req.body and send them to updateCat function of `./models/catModel.js`
      * modify `/` route for PUT method in `./routes/catRoute.js` to call cat_update_put function
      * click on a modify-button under the cats to fill the Modify cat form in `index2.html`, send the form and check in phpMyAdmin whether the data has been modified.
         
1. Use the `/` route for DELETE method in `./routes/catRoute.js` to delete cats in database
   * Deleting data in a database is mostly the same JavaScript as selecting data from a database. The difference is in the SQL query. Instead of SELECT use DELETE.
   * Deleting a cat:
      * Add id parameter `/` route for DELETE method in `./routes/catRoute.js`: `/` => `/:id`
      * add a new function deleteCat to `./models/catModel.js`
         * the function should delete the selected cat's (cat_id) data from the database 
      * add a new function cat_delete to `./controllers/catController.js`
         * the function should receive _id_ in req.body and send it to deleteCat function of `./models/catModel.js`
      * modify `/:id` route for DELETE method in `./routes/catRoute.js` to call cat_delete function
      * click on a delete-button under the cats to delete a cat in `index2.html`.
         
1. Commit changes and push to GitHub






