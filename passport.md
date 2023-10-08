## Setting up passport: 

Passport's docs are a little confusing. This is extremely helpful: [https://github.com/jwalton/passport-api-docs](https://github.com/jwalton/passport-api-docs)


#### 1. App-level middleware calls
```app.use(passport.initialize())``` Initializes passport for each request to the server.

```app.use(passport.authenticate('session'))``` Reads the cookie from the request, retrieves the session data from the cache, and deserializeds the user if necessary. Sets ```session.passport.user```.

Passport sessions use ```express-session```, so set that up and call ```app.use(expressSession())``` before ```app.use(passport.authenticate('session'))```.

#### 2. ```serialize``` and ```deserialize```
 ```passport.serializeUser()``` and ```passport.deserializeUser()``` 
detail how to store/retrieve users from the session. For example, only the userId is stored in the session cache and deserializing the user requires retrieving user data from db.

#### 3. ```verify```  and ```Strategy``` options

```Strategy(options, verify)``` ```verify``` details how the user's credentials should be verified. The arguments depend on the 

Then ```Strategy``` is attached to the passport middleware with ```passport.use(strategy)```.

There can be 3 returns for ```verify```:

1. ```return callback(null, user)``` When the user is verified. 
2. ```return callback(null, false)``` When the user cannot be verified. 
3. ```return callback(error)``` When there is an error. Not when, for example, db cannot find a user.

The options that can be set differ for each strategy.

#### 4. Strategy-specific middleware calls and options

For example: ```app.get('/login', passport.authenticate('local', options, callback))``` 

The middleware created will run the request through the appropriate```verify``` function. If verified, the user is stored to req.user. ```req.isAuthenticated()```, ```req.login()``` and ```req.logout()``` are also added to```req``` to 
be used in ```callback```. 

#### 5. ```callback``` for each strategy
```callback(err, user, info)``` This will deal with redirection and sending a response depending on the results of the ```verify```.

And finally, an authorization middleware can determine if the authenticated user has the appropriate permissions.

### Strategy specifics:
#### Local
```app.get('/login', passport.authenticate('local', options, callback))``` Note that ```verify``` takes```username``` and ```password``` from the request body. Aliases can be named in the options.

#### Google OAuth2.0
Two routes must be made: 
1. ```app.get('/google', passport.authenticate('google', options))``` Redirects users to Google's login page.
2. ```app.get('/google/callback', passport.authenticate('google', callback))``` Receive google's authentication results. 

 Because the actual verification occurs on Google's end, ```verify()``` will contain logic, that, for example, creates a user for the app associated with the verified Google profile.


