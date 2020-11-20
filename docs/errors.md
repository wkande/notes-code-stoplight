# Error Handler

The **Notes** backend application uses a global error handler which is a standard practice with Node.js/Express applications.

The error handler is located in the app.js file.

```javascript
/**
 * Global Error Handler
 */
app.use(function(err, req, res, next) {
  try{
    // If the default 2xx codes are present convert to 500.
    // "Throws" should be sending a none 2xx code.
    if (res.statusCode < 300){
      res.status(500);
    }

    // ip
    var ip = req.headers['x-forwarded-for'] || 
      req.connection.remoteAddress || 
      req.socket.remoteAddress ||
      (req.connection.socket ? req.connection.socket.remoteAddress : null);

    // Has stack trace
    if(err.stack){
        let e = {message:null, location:null};
        e.message = err.stack.split("\n")[0].trim();
        // Get the first two lines
        e.location = 
        err.stack.split("\n")[1].trim()+'---'+err.stack.split("\n")[2].trim();
        e.ip = ip;

        if(req.get("accept").toLowerCase() === 'application/xml'){
          res.type('application/xml');
          res.status(res.statusCode).send(js2xmlparser.parse("error",e));
        }
        else{
          res.type('application/json');
          res.status(res.statusCode).send({error:e});
        }
    }
    // No stack trace
    else {
        err.ip = ip;
        if(req.get("accept").toLowerCase() === 'application/xml'){
          res.type('application/xml');
          res.status(res.statusCode).send(js2xmlparser.parse("error",err));
        }
        else{
          res.type('application/json');
          res.status(res.statusCode).send({error:err});
        }
    }
  }
  catch(err){
      res.type('text/plain');
      res.status(500).send({message:"Issue at Global Error handler.", error:err.toString(), ip:ip});
  }
});
```

### Stacktrace

While seemingly lengthy it can be broken into two groups. 

- With a stacktrace
- No stacktrace

The delineation between the two is to add the stack trace to the return error should it exists.

### Throw

The **error handler** is added to the Express routing mechanisn and is executed if any code performs a **throw**. Express will then pass the error along with req, res and next to the error handler.

With Notes all **throw** statements are expected to pass a runtime error object or an object with a message. There are two types of throw statements in Notes.

- Runtime thrown error
- Application thrown error


```javascript
// Runtime thrown error
// toLowerCas() will cause a runtime error
try{
  if(req.get("accept").toLowerCas() === 'application/xml'){
    res.type('application/xml');
  }
}
catch(err){
  throw(err);
}

// Application thrown error
// Specific violation of the code flow results in a message object.
if(!verifyNoteOwnership(id, email)){
    res.status(403);
    throw {message:"You cannot change that note."};
  }

```

### Set Status

For **Runtime** thrown errors it is not needed to set the res.status as the error handler will set it to 500 by default when it sees a status < 300. **Applciation** thrown errors must set the res.status to a valid response status code that is described in the OpenAPI specification file for Notes.


