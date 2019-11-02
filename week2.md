# Week 2
1. [Routing](https://expressjs.com/en/guide/routing.html)
   * Navigate to your course folder in terminal or Git Bash
   * Create new Git branch. `git checkout -b week2`
   * Create `week2` folder to your course folder: `mkdir week2`
   * Create `routing` folder to `week2` folder
   * [Download starter files as zip](https://github.com/ilkkamtk/wop-starters/tree/week2-1), extract and copy all files to `week2` folder
   * Run `npm install` in `week2` folder
   * 
   * Add endpoint 'catinfo' to app.js: 
   ```javascript
   app.get('/catinfo', (req, res) => {
     const cat = {
       'name': 'Frank',
       'age': 6,
       'weight': 5,
     };
     res.json(cat);
   });
   ```
   * test in browser: `localhost:3000`