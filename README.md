# logger-winston

Provides a logging helper to work with multiple winston logger instances using different configuration settings for
 each logger. This is useful in larger application where you may want group logging output into topics or categories
 for different parts of the code.

## Features

* A default configuration can be provided which applies to all logger instances unless a specific configuration has
  been set for a logger topic.
* Each logger can have multiple sinks defined by winston transports as part of the logger configuration. If you need
  to setup multiple transports of the same type, e.g., two file transports the transport name can be augmented with
  a "#name" postfix, e.g. "file#debug", to avoid duplicate key errors in strict mode.

## API
<dl>
<dt><a href="#init">init(config)</a></dt>
<dd><p>Initialize logging with the given configuration. As the given configuration will be cached internally the first
invocation will take effect, only. Make sure to invoke this function at the very beginning of the program before
other topics using logging get loaded.</p>
</dd>
<dt><a href="#getLogger">getLogger(topicName)</a> ? <code>Object</code></dt>
<dd><p>Get a winston logger for a given topic name. If the requested logger does not yet exist a new instance will be
created internally.</p>
</dd>
</dl>

<a name="init"></a>
### init(config)
Initialize logging with the given configuration. As the given configuration will be cached internally the first
invocation will take effect, only. Make sure to invoke this function at the very beginning of the program before
other topics using logging get loaded.

**Kind**: global function

| Param | Type | Default | Description |
| --- | --- | --- | --- |
| config | <code>Object</code> |  | The logging configuration. |
| config.logging | <code>Object</code> |  | Contains the logging configuration for at least one winston container. A specific container configuration can be provided by defining a configuration for a category matching the topicName. A default configuration can provided by defining a container for the category "default". A container configuration contains at least one transport configuration. Note, you may run into an issue in strict mode, if you need to setup multiple transports of the same type, e.g., two file transports, as the strict mode inhibits duplicate object keys. In this case the transport name can be augmented with a "#name" postfix, e.g. "file#debug". |
| [config.logging.[topicName]] | <code>Object</code> | <code>default</code> | A specific container for a given topicName can be setup by providing a container configuration where the property name is equal to given topicName. The container configuration must contain at least one winston transport configuration. If the container configuration contains the key "inheritDefault" set to true, the default configuration will be mixed in. This way, it possible to solely define transport properties which shall differ from the default configuration. |
| [config.logging.default] | <code>Object</code> | <code>builtin</code> | Contains the default configuration applicable to loggers without a specific container configuration. The container configuration must contain at least one winston transport configuration.  If no default configuration is provided a console transport configuration with winston builtin defaults will be used. |

<a name="getLogger"></a>
### getLogger(topicName) ? <code>Object</code>
Get a winston logger for a given topic name. If the requested logger does not yet exist a new instance will be
created internally.

**Kind**: global function

**Returns**: <code>Object</code> - The winston logger for the given topic name

| Param | Type | Description |
| --- | --- | --- |
| topicName | <code>Object</code> | The name of the topic, topic or category the returned logger is assigned to. The topicName will be added automatically to the log output, if the configuration does not include the "label" property as part of the applicable winston transport configuration. |

**Example**
```js
// Initialize logging in the main program file using the server configuration.
// You only need to call this once! Modules loaded subsequently, can simply
// obtain a logger using getLogger().
var config = require("./config.json");
var logging = require("logger-winston");
logging.init(config);

// Now you can obtain a logger for the topic "MyApp", for example, and start logging.
// The log output will be augmented with a label for the given topic name, automatically.
// By default, the output will look like a follows:
// info: [MyApp] Hello world!
var logger = logging.getLogger("MyApp");
logger.info("Hello world!");

// You can set up a "default" configuration which applies for all
// logger instances unless you provide specific configuration for
// some topics.
// In the given example the the "default" applies to topic "MyApp" while
// a specific configuration has been set for topic "Server".
var logger2 = logging.getLogger("Server");
logger2.info("Starting Server");
```
**Example Configuration config.json**
```js
{
  "logging": {
    "default": {
      "console": {
        "level": "debug",
        "colorize": true,
        "timestamp": true
      }
    },
    "Server": {
      "console": {
        "level": "debug",
        "colorize": false,
        "timestamp": false
      }
    }
  }
}
```
