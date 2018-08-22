# discordbans-id

[![Greenkeeper badge](https://badges.greenkeeper.io/Zoddo/discordbans-id.svg)](https://greenkeeper.io/)

A node.js package to check users on Discord Bans.

## Installation
```
$ npm install --save discordbans-id
```

## Quick example
```js
const Lookup = require('discordbans-id').Lookup;
const dbans = new Lookup('your_dban_token');

dbans.lookup('123456789123456789').then(r => {
    console.log(r);
}).catch(err => console.error(err));
```
Console output:
```js
{ banned: true,
  user_id: '123456789123456789',
  case_id: '9999',
  reason: 'THIS IS A VIRTUAL DBANS API TESTING ACCOUNT',
  proof: 'https://yourmom-is.gae' }
```

### Scan a whole server
*This example is using [Eris](https://abal.moe/Eris/) as a Discord library.*

```js
const Eris = require('eris');
const Lookup = require('discordbans-id').Lookup;

const bot = new Eris('your_discord_bot_token', {getAllUsers: true});
const dbans = new Lookup('your_dban_token');

bot.once('messageCreate', msg => {
    if (msg.content === '!scan') {
        for (const [id, m] of msg.channel.guild.members) {
            // Here, we call dbans.lookup() in a loop.
            // The library will automatically concat all requests and do bulk lookups. It's magic :-)
            dbans.lookup(id).then(r => {
                if (r.banned) {
                    // The user is listed on Discord Bans. You can do what you want.
                }
            }).catch(err => console.log(err));
        }
    }
});

bot.connect();
```

## Documentation
*Note: Internal methods and properties not meant to be used outside of the library's code are not documented.*

### `Lookup` class
#### Constructor
```js
new Lookup(token[, options]);
```
Argument            |   Type   | Description
------------------- | -------- | -----------
`token`             | string   | Your bans.discord.id API token. To get one, DM `!token` to Discord Bans#0269.
`options.interval`  | number   | [Optional=1000] The interval between two API requests in ms. Default to 1000ms (1 sec).
`options.cacheSize` | number   | [Optional=-1] The maximum size of the internal cache. When the cache is full, oldest elements are removed. Set to `0` to disable the cache. Default to `-1` (unlimited).
`options.cacheLife` | number   | [Optional=3600000] The maximum lifetime of a cached entry. Default to 3600000ms (1 hour).

#### `lookup` method
```js
dbans.lookup(userid[, high_priority[, no_cache]])
```
Argument            |   Type   | Description
------------------- | -------- | -----------
`userid`            | string   | The user ID to lookup in the Discord Bans database.
`high_priority`     | bool     | [Optional=false] If set to `true`, the lookup request will be processed as soon as possible. For example, this may be useful when you want to check users who are joining the server in realtime, but a scan of your whole memberlist have be launched and added a lot of user IDs in the internal queue.
`no_cache`          | bool     | [Optional=false] If set to `true`, the cache will not be used, forcing a lookup to the Discord Bans API.

This method returns a `Promise` that will resolve once the response have been obtained from the API with the following object:
```js
// If the user is not listed
{ banned: false,
  user_id: '987654321987654321' }

// If the user is listed
{ banned: true,
  user_id: '123456789123456789',
  case_id: '9999',
  reason: 'THIS IS A VIRTUAL DBANS API TESTING ACCOUNT',
  proof: 'https://yourmom-is.gae' }
```
*Note: If something wrong happens during the API request, the promise will be rejected with an appropriate `Error` object.*

## Issues
If you want to report a bug or any other issues, please [open an issue on GitHub](https://github.com/Zoddo/discordbans-id/issues).  
Pull requests are also welcome ;)
