# Logging
Reaction uses the [ongoworks:bunyan-logger](https://github.com/ongoworks/meteor-bunyan) package, which exports `loggers`, which `reactioncommerce:reaction-core` configures and exports as `ReactionCore.Log`.

The [ongoworks:bunyan-logger](https://github.com/ongoworks/meteor-bunyan) package implements the [Bunyan](https://github.com/trentm/node-bunyan) logging library to provide a Stream capable, JSON friendly log handler in Reaction Core.

To configure logging you can modify `isDebug: true` in `settings.json`.  Value can be any valid `Bunyan level` in settings.json, or true/false.

Setting a level of _debug_  `isDebug:  "debug"` or higher will display verbose logs as JSON. The JSON format is also the storage / display format for production.

_Recommend running meteor with `--raw-log` to remove Meteor's default console formatting. This is the default when you use `./bin/run` to start Meteor._

Feel free to include verbose logging, but follow [Bunyan recommendations on log levels](https://github.com/trentm/node-bunyan#levels) and use appropriate levels for your messages.

```
The log levels in bunyan are as follows. The level descriptions are best practice opinions.

"fatal" (60): The service/app is going to stop or become unusable now. An operator should definitely look into this soon.
"error" (50): Fatal for a particular request, but the service/app continues servicing other requests. An operator should look at this soon(ish).
"warn" (40): A note on something that should probably be looked at by an operator eventually.
"info" (30): Detail on regular operation.
"debug" (20): Anything else, i.e. too verbose to be included in "info" level.
"trace" (10): Logging from external libraries used by your app or very detailed application logging.
Suggestions: Use "debug" sparingly. Information that will be useful to debug errors post mortem should usually be included in "info" messages if it's generally relevant or else with the corresponding "error" event. Don't rely on spewing mostly irrelevant debug messages all the time and sifting through them when an error occurs.
```

Example logging message:

```js
ReactionCore.Log.info("Something info, most likely for development", result);
ReactionCore.Log.error("Something error want to investigate", error);
```

Example creation of new server logging for the [reaction-inventory](https://github.com/reactioncommerce/reaction/blob/development/packages/reaction-inventory/server/logger.js) package:

```js
// set logging level
let formatOut = logger.format({
  outputMode: "short",
  levelInString: false
});
// define package Log
ReactionInventory.Log = logger.bunyan.createLogger({name: "inventory", stream: formatOut});
```
