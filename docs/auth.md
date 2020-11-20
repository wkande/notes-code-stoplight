# Authentication

Notes implements an Email-Code-Token mechanism for authentication using JWT. The user must request a code be sent to their email address. The code is then exchanged for a [jsonwebtoken](https://www.npmjs.com/package/jsonwebtoken).

### Email/Code Management

The user.js file manages user routes and the email/code data a an array. The array is memory only without persistance. If the Node.js instance is restarted the array is lost for security reasons.

```javascript
let codes = [];
```

When a new code is created and sent to the user it is stored in this array. 

```javascript
codes.push({email:email, code:code});
```

A user then returns to acquire a token using their email address and the code. Upon receipt of a token the email/code from the user is removed from the array. Tokens are not stored by the backend, only verified.

### Tokens

Token are managed by the tokens.js file. Tokens are not stored by the backend, only verified. The encrypted token holds the user's email address within. No other information about the user is known.

```javascript
module.exports.getToken = function(email){
  return jwt.sign(email, process.env.JWT_SECRET);
}
```
When the token is verified the email address within is returned. If the tokens fails to decrypt an error is thrown. The password that encrypts and decrypts the token is a proces.ENV var loaded into the Heroku Dyno at startup.

```javascript
module.exports.validateToken = function(auth){
  try{
    // auth param has Bearer attached
    const token = auth.split(' ')[1];

    // The decoded token is just the email
    return jwt.verify(token, process.env.JWT_SECRET);
  }
  catch(err){
    response.status(401);
    throw err;
  }
}
```

Each route that requires a security will request a verification of the token to get the user's email address. Should verification fail the error thrown will find its way to the global error handler preventing the route from completion.

```javascript
const email = tokens.validateToken(req.get('Authorization'), res);
```

