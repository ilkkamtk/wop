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
1. Study [Routing](https://expressjs.com/en/guide/routing.html)
   * Add `cat` routes which respond to POST, PATCH and DELETE methods.
     * POST response: `With this endpoint you can add cats.`
     * PATCH response: `With this endpoint you can edit cats.`
     * DELETE response: `With this endpoint you can delete cats.`
   * Test the routes with [Postman](https://www.getpostman.com/)