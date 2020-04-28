This package was forked from [passport-slack](https://github.com/mjpearson/passport-slack) and updated for slack v2 of oauth.

# passport-slack-v2

[Passport](https://github.com/jaredhanson/passport) strategy for authenticating
with [Slack](https://slack.com) using the OAuth 2.0 API version 2.

Updated to support Add to Slack by default.<br>
[![Add to Slack](https://platform.slack-edge.com/img/add_to_slack.png)](https://api.slack.com/docs/slack-button)

## Install
```shell
$ npm install passport-slack-v2
```

## Express Example
```js
const {CLIENT_ID, CLIENT_SECRET, PORT} = process.env,
      SlackStrategy = require('passport-slack-v2').Strategy,
      passport = require('passport'),
      express = require('express'),
      app = express();

// setup the strategy using defaults 
passport.use(new SlackStrategy({
    clientID: CLIENT_ID,
    clientSecret: CLIENT_SECRET
  }, (accessToken, refreshToken, profile, done) => {
    // optionally persist profile data
    done(null, profile);
  }
));

app.use(passport.initialize());
app.use(require('body-parser').urlencoded({ extended: true }));

// path to start the OAuth flow
app.get('/auth/slack', passport.authorize('slack'));

// OAuth callback url
app.get('/auth/slack/callback', 
  passport.authorize('slack', { failureRedirect: '/login' }),
  (req, res) => res.redirect('/')
);

app.listen(PORT);
```

## Sample Profile
```json
{
    "provider": "Slack",
    "url": "https://subarachnoid.slack.com/",
    "team": "Subarachnoid Workspace",
    "team_id": "T12345678",
}

```

## Usage

### Configure Strategy

The Slack authentication strategy authenticates users using a Slack
account and OAuth 2.0 tokens.  The strategy requires a `verify` callback, which
accepts these credentials and calls `done` providing a user, as well as
`options` specifying a client ID, client secret, and callback URL.

```js
passport.use(new SlackStrategy({
    clientID: CLIENT_ID,
    clientSecret: CLIENT_SECRET,
    skipUserProfile: false, // default
    scope: [] // default is none, you will have to add yours as strings comma seperated
  },
  (accessToken, refreshToken, profile, done) => {
    // optionally persist user data into a database
    done(null, profile);
  }
));
```

### Authenticate Requests

Use `passport.authorize()` (or `passport.authenticate()` if you want to authenticate with Slack and affect `req.user` and user session), specifying the `'slack'` strategy, to
authenticate requests.

For example, as route middleware in an [Express](http://expressjs.com/)
application:

```js
app.get('/auth/slack', passport.authorize('slack'));

app.get('/auth/slack/callback',
  passport.authorize('slack', { failureRedirect: '/login' }),
  (req, res) => res.redirect('/') // Successful authentication, redirect home.
);
```

### Custom Scopes
By default passport-slack-v2 strategy will have no slack scopes. To override these, set the `scope` parameter to an array of scopes.

```js
passport.use(new SlackStrategy({
	clientID: CLIENT_ID,
	clientSecret: CLIENT_SECRET,
	scope: ['chat:write', 'groups:read', 'im:read', 'mpim:read', 'users.profile:read', 'users:read', 'users:read.email']
}, () => { });
```

### Ignore Profile Info
If you just need an access token and not user profile data, you can avoid getting profile info by setting `skipUserProfile` to true.
```js
passport.use(new SlackStrategy({
	clientID: CLIENT_ID,
	clientSecret: CLIENT_SECRET,
	scope: ['incoming-webhook'],
	skipUserProfile: true
}, () => { });
```

## Thanks

  - [Noah Bragg](http://github.com/normdoow)
  This package was forked from [passport-slack](https://github.com/mjpearson/passport-slack)

## License

[The MIT License](http://opensource.org/licenses/MIT)

Copyright (c) 2020 [Noah Bragg](http://github.com/normdoow)
