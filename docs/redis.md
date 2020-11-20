# Redis Labs Database

Data is stored in a Redis Labs database. The models.js file manages conncetions to Redis and the data it holds.

### Startup

When the Notes backend application starts the models.js file is referenced by and thus loaded from the app.js file. Model.js will run init code to connect to Redis and then retrieve the notes array.

 ```javascript
 /**
 * This script runs at startup to connect to RedisLabs where the 
 * notes are stored.
 */
// STARTUP
debug('Running startup script');
// Connect to RedisLabs
const client = redis.createClient(
  process.env.REDIS_PORT,
  process.env.REDIS_HOST
);
client.auth(process.env.REDIS_KEY);

client.get("notes", function(err, obj) {
  // arr is null when the key is missing
  if(err) console.log('err', err);
  notes = JSON.parse(obj);
  debug('Notes array length:', notes.length);
  debug('Connection to RedisLabs OK');
});
// END STARTUP
```


### Data Management

Only note data is stored in Redis as an array of the Notes class.

```javascript
/**
 * Note class definition
 */
class Note {
  constructor(email, content, tags) {
    this.id = shortid.generate();
    this.email = email;
    this.content = content;
    this.tags = tags;
    this.dttm = new Date();
  }
}
```


### Shutdown
Notes utilizes the global exit handler found in app.js to shut down the Redis Labs database connetion. The actual function to close Redis is in models.js.

```javascript
// Closes the connection to RedisLabs where the notes are stored.
module.exports.closeRedis = function(){
  console.log('Closing connection to RedisLabs');
  client.quit();
}
```
The closeRedis() function is called by the exit handler.

```javascript
/*------------------- Ending Node.js --------------------*/
function exitHandler(options, exitCode) {
  if (options.exit) closeRedis();
  if (options.cleanup) console.log('clean');
  if (exitCode || exitCode === 0) console.log('Exit code:', exitCode);
  if (options.exit) process.exit();
}
```