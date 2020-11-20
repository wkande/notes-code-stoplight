# Routes

Notes uses the express.Router class to create modular, mountable route handlers. A Router instance is a complete middleware and routing system; for this reason, it is often referred to as a “mini-app” or in the case of Notes, route groups.

### Route Groups

Using the **Express** framework the Notes backend application uses three major route groups. Rather than map each path such as **GET /user**, common routes are grouped together and forwarded to an express router. 

Paths are routed to a route group in the app.js file. The following extract from the app.js file illustrates the grouping of paths to their designated route group.

- /user > usersRouter
- /notes > notesRouter
- /tags > tagsRouter


```javascript

... 

app.use(cors());

/**
 * Set up body parsers for the request body types expected.
 * parse application/json
 * parse application/x-www-form-urlencoded
 */
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: false }));

app.use(logger('dev'));
app.use(express.json());
app.use(express.urlencoded({ extended: false }));
app.use(cookieParser());
app.use(express.static(path.join(__dirname, 'public')));

app.use('/', indexRouter);
app.use('/users', usersRouter);
app.use('/user', usersRouter);
app.use('/note', notesRouter);
app.use('/notes', notesRouter);
app.use('/tag', tagsRouter);
app.use('/tags', tagsRouter);

... 

module.exports = app;

```

### Methods

Each route group then implements standard REST methods (or operations) for the route group. The following code snippet peforms the GET method on the /note/:id route which is a member of the notesRouter route group.

```javascript
var express = require('express');
var router = express.Router();

...

/**
 * GET /note
 * Gets a note from the current user.
 */
router.get('/:id', function(req, res, next) {
  try{
    // JWY verify and get the email
    const email = tokens.validateToken(req.get('Authorization'), res);
    const id = req.params['id']

    let found = false;
    getNotes().forEach( (element)=>{
      if(element.email === email && element.id === id){
        found = true;
        if(req.get("accept").toLowerCase() === 'application/xml'){
          res.type('application/xml');
          res.status(200).send(js2xmlparser.parse("note", element));
        }
        else{
          res.type('application/json');
          res.status(200).send(element);
        }
      }
    });

    if (!found) {
      res.status(400);
      throw {message:"Invalid note id."};
    }
  }
  catch(err){
    debug(err);
    throw(err);
  }
});

...

module.exports = router;

```


