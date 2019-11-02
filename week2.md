# Week 2
## Routing
1. Setup
   * Navigate to your course folder in terminal or Git Bash
   * Create new Git branch. `git checkout -b week2`
   * Create `week2` folder to your course folder: `mkdir week2`
   * Create `routing` folder to `week2` folder
   * [Download starter files as zip](https://github.com/ilkkamtk/wop-starters/tree/week2-1), extract and copy all files and folders to `week2` folder
   * Move `week2_public_html` folder to your public_html in shell.metropolia.fi or in your virtual computer. You can rename the folder if you want.
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
     * If the request is `cat/3`, the response should be `You reqested a cat who's id is 3`
1. Study [Router](https://expressjs.com/en/guide/routing.html#express-router) and create a route handler for `cat` path. 
   * Move `cat` routes from `app.js` to `./routes/catRouter.js`
   * Test the routes with [Postman](https://www.getpostman.com/)
   * `./routes/catRouter.js` is a [module](https://stackify.com/node-js-module-exports/)
   * Read this article about [design patterns](https://medium.com/code-to-express/tricky-design-patterns-in-javascript-part-1-ad7228d8db4d)
