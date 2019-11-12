# Week 3
## Data Validation
* Study [client-side form validation](https://developer.mozilla.org/en-US/docs/Learn/HTML/Forms/Form_validation)
* Read this article about [server-side data validation](https://medium.com/@BaYinMin/application-security-what-is-server-side-input-validation-why-is-it-needed-anyway-e0613c733548)
   * code in the article is not important but the principles are
* Read this article about [data sanitization](https://medium.com/@abderrahman.hamila/what-sanitize-mean-and-why-sanitize-in-code-data-5c68c9f76164)

1. HTML5 Built-in form validation
   * Continue last week's exercise. Make sure you have committed previous git branch then create new branch `week3`
   * In index2.html there are three forms: Add user, Add cat, Modify cat
   * Add the following validation properties:
      * Add user
         * name: minimum length 3 characters, required
         * email: valid email address
         * password: minimum length 8 characters, at least one capital letter
      * Add cat
         * name: required
         * age: number, required
         * weight: number, required
         * select owner: required
         * file: required, accept only images (jpg, gif, png)
      * Modify cat:
         * same as Add cat
1. Server side data validation
   * Study [express-validator](https://express-validator.github.io/docs/)
      * [List of all validators](https://github.com/validatorjs/validator.js#validators)
   * Validate the same properties on the server side as with above forms:
      * Add user
         * name: minimum length 3 characters, required
         * email: valid email address
         * password: minimum length 8 characters, at least one capital letter
      * Add cat
         * name: required
         * age: number, required
         * weight: number, required
         * select owner: required
         * file: required, accept only images (jpg, gif, png)
      * Modify cat:
         * same as Add cat
1. Server side data sanitization
   * Study [express-validator sanitization](https://express-validator.github.io/docs/sanitization.html)
   * Some of the fields could be still used to perform attacks. Think which they could be and sanitize those fields.
