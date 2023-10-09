## Setting up Passport

Passport's docs are a little confusing. This guide is extremely helpful: [https://github.com/jwalton/passport-api-docs](https://github.com/jwalton/passport-api-docs)


### To write/config:
#### 1. ```Strategy(options, verify)``` 
  - ```options``` will differ on what type of strategy constructor is used. For example, for the ```passport-local``` strategy, ```username``` and ```passport``` aliases can be set.
  - ```verify()```
    - For local auth strategies:, ```verify(username, password, done)``` will verify the request credentials. For example, it will search in the db to see if the username and password match. 
    - For federated auth: ```verify(accessToken, refreshToken, profile, done)``` is used by Passport to process the authenticated user profile provided by the federated source. For example, it can create a user for the app associated with the profile if one cannot be found.
    - ```verify()``` must return a call to ```done(error, user, info)```. ```user``` is null if not verified. 

- The strategy is then attached to the Passport middleware with ```passport.use(strategy)```. Strategies can be supplied in an array, to be used one after another, until one succeeds. 

#### 2. ```passport.authenticate(strategyName, options, callback)```
  - ```strategyName``` is a string - ```'local'``` or  ```'google'``` or ```'github'```
  - ```options``` - options pertaining to this use of ```passport.authenticate()```, such as redirect urls
  - ```callback(error, user, info)``` is for you to write. The ```passport.authenticate()``` middleware calls the appropriate ```verify()``` then creates ```req.user``` and ```req.isAuthenticated()``` for  ```callback``` to process and send back a proper response or for other middleware to process.

#### 3. If using sessions:
  - The session ```Strategy``` is already written, so just run ```passport.authenticate('session')``` for the appropriate routes.
  - Passport uses ```express-sesssion```,  so call ```passport.authenticate('session')``` after that the ```express-session``` middleware.
  - ```passport.serializeUser(user, done)``` and ```passport.deserializeUser(user, done)``` must be written to detail how a verified user is serialized/deserialized from how it is stored in the session. For example, deserializeUser may require fetching more info from db. 
  - ```req.login()``` and ```req.logout()``` are added by ```passport.authenticate()``` alongside ```req.user``` to be used in the callback to start/end a session. Passport will not automatically start a session when a user's credentials are verified.

#### 4. If using federated auth such as Google Oauth2.0, 
- Two routes must be made: 
    - ```app.get('/google', passport.authenticate('google', options))``` Redirects users to Google's login page.
    -```app.get('/google/callback', passport.authenticate('google', callback))``` Receive google's authentication results. 

- Because the actual verification occurs on Google's end, ```verify()``` contains logic, that, for example, creates a user for the app associated with the verified Google profile if one isn't found. 

#### The general flow goes like this:
1. Request is made to the server.
2. Request goes through```passport.initialize()```. This attaches ```req._passport```, which Passport needs.
3. Request is routed to the appropriate authenticating strategy middleware ```passport.authenticate(strategy)```.
4. If the strategy involves, for example, Google Oauth2.0,  the user is redirected to sign in with Google. After authorizing the user, it will redirect the user to the callback URL specified in the options. 
5. ```passport.authenticate()``` runs the request credentials or the results of the federated authentication through ```verify()```, which you wrote for that strategy. ```verify()```. 
6. ```passport.authenticate()``` calls the ```callback()``` with the results from ```verify()```. ```callback()``` may send a response. Or the request may continue to travel through your other middleware. 
