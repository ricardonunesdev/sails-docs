# Custom responses

### Overview

Sails apps come bundled with several pre-configured _responses_ that can be called from [action code](http://sailsjs.com/documentation/concepts/actions-and-controllers).  These default responses can handle situations like &ldquo;resource not found&rdquo; (the [`notFound` response](http://sailsjs.com/documentation/reference/response-res/res-not-found)) and &ldquo;internal server error&rdquo; (the [`serverError` response](http://sailsjs.com/documentation/reference/response-res/res-server-error)).  If your app needs to modify the way that the default responses work, or create new responses altogether, you can do so by adding files to the `api/responses` folder.

> Note: `api/responses` is not generated by default in new Sails apps, so you&rsquo;ll have to add it yourself if you want to add / customize responses.

### Using responses

As a quick example, consider the following controller action:

```javascript
getProfile: function(req, res) {
   // Get the user ID from the session.
   var userId = req.session.userId;

   // Look up the user in the database.
   User.findOne(userId).exec(function(err, user) {

     // Handle a database error.
     if (err) {
       res.status(500);
       return res.view('500', {data: err});
     }

    // ...

  });
}
```

This code handles a database error by sending a 500 error status and sending the error data to a view to be displayed.  However, this code has several drawbacks, primarily:

*  It isn't *normalized*; the code is specific to this instance, and we'd have to work hard to keep the same format everywhere
*  It isn't *abstracted*; if we wanted to use a similar approach elsewhere, we'd have to copy / paste the code
*  The response isn't *content-negotiated*; if the client is expecting a JSON response, they're out of luck

Now, consider this replacement:

```javascript
getProfile: function(req, res) {
  // Get the user ID from the session.
  var userId = req.session.userId;

  // Look up the user in the database.
  User.findOne(userId).exec(function(err, user) {

   // Handle a database error.
   if (err) {
     return res.serverError(err);
   }

   // ...

  });
}
```


This approach has many advantages:

 - Error payloads are normalized
 - Production vs. Development logging is taken into account
 - Error codes are consistent
 - Content negotiation (JSON vs HTML) is taken care of
 - API tweaks can be done in one quick edit to the appropriate generic response file

### Responses methods and files

Any `.js` script saved in the `api/responses/` folder will be executed by calling `res.[responseName]` in your controller.  For example, `api/responses/disappeared.js` can be executed with a call to `res.disappeared()`.  The request and response objects are available inside the response script as `this.req` and `this.res`; this allows the actual response function to take arbitrary parameters (e.g. `res.disappeared('foo','bar','baz')`).

### Built-in responses

All Sails apps have several pre-configured responses like [`res.serverError()`](http://sailsjs.com/documentation/reference/response-res/res-server-error) and [`res.notFound()`](http://sailsjs.com/documentation/reference/response-res/res-not-found) that can be used even if they don&rsquo;t have corresponding files in `api/responses`.

Any of the default responses may be overridden by adding a file with the same name to `api/responses/` in your app (e.g. `api/responses/serverError.js`).

> You can use the [Sails command-line tool](http://sailsjs.com/documentation/reference/command-line-interface/sails-generate) as a shortcut for doing this.
>
> For example:
>
>```bash
>sails generate response serverError
>```



<docmeta name="displayName" value="Custom responses">