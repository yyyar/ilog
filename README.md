## loginator

[![Build Status](https://travis-ci.org/yyyar/loginator.svg?branch=master)](https://travis-ci.org/yyyar/loginator) [![NPM version](https://badge.fury.io/js/loginator.svg)](http://badge.fury.io/js/loginator)

Loginator is simple and configurable logger for Node.js.

* **Configurable**: Logging format, level, multiple appenders, formatters and overrides
* **Formatters**: Configurable Text and JSON formatters
* **Appenders**: Configurable Stdout, File & Redis appenders

### Installation
```bash
$ npm install loginator
```

### Usage

#### Simple example

```javascript
var loginator = require('loginator');

var log = loginator.createLogger( /* {optional configuration} */ );

// Logging levels
log.debug('Hello world!');
log.info('Hello world!');
log.warn('Hello world!');
log.error('Hello world!');
log.fatal('Hello world!');

// Logging objects
log.info('Hello world!', {'some': 'object'}, new Date(), new Error('Error!!!'));
```
Outputs:
```
2015-03-28 11:54:31 [DEBUG] [node] (default) : Hello world!
2015-03-28 11:54:31 [INFO ] [node] (default) : Hello world!
2015-03-28 11:54:31 [WARN ] [node] (default) : Hello world!
2015-03-28 11:54:31 [ERROR] [node] (default) : Hello world!
2015-03-28 11:54:31 [FATAL] [node] (default) : Hello world!
2015-03-28 11:54:31 [INFO ] [node] (default) : Hello world! {"some":"object"} "2015-03-28T09:54:31.096Z" Error: Error!!!
```


#### Configuration
`loginator.createLogger()` accepts one optional configuration object.
*Note*. Loginator configuration object may be a simple JSON so you can easily store logger configuration in external JSON file.

```javascript
var log = loginator.createLogger({

    // Logger name
    name: 'mylogger',

    // logging level. Logs with less logging level will be omitted.
    // Supported values are: 'TRACE', 'DEBUG', 'INFO', 'WARN', 'ERROR' and 'FATAL':
    level: 'DEBUG',

    // truncate 'message' to limitMessageLength chars.
    // 0, if leave message as it is without truncation
    limitMessageLength: 0,

    // formatter defines how to format log output.
    // this is a default formatter that will apply to all appenders by default,
    // and it can be overwritten in any concrete appender configuration
    // (see following paragraphs for more examples)
    formatter: {
        type: 'text',
        options: {
            pattern: '[%level] %dtime ~ %message'
        }
    },

    // appenders defines a list of destinations
    // where log messages will go
    // (see following paragraphs for more examples)
    appenders: [
        {
            type: 'stdout',
            options: {
                formatter: { /* ... */ } # optional formatter override
            }
        }
    ]
});
```

#### Formatters
Formatter defines how log output will be formated. Formatter can be configured:
- Globally for logger. It's configuration should be passed into logger config.
- Per appender. By defaults all appenders inherit logger's formatter, but you can
overwrite formatter for concrete appender (see Appenders section for more details).

There are several predefined variables that can be used in formatters config:
```
message, date, dtime, time, level, process, name
```

##### Text Formatter
```javascript
{
    type: 'text',
    options: {
        pattern: '[%dtime] [%level] - %message'
    }
}
```

Where `pattern` may be any mix of variables prefixed by `%`.

##### JSON Formatter
```javascript
{
    type: 'json',
    options: {
        fields: ['dtime', 'message', 'level', 'process'],
        pretty: true | false
    }
}
```

Where `fields` is an array of variables to include to resulting json.
And `pretty` is indicator that you want pretty-printed multi-line json


##### Custom Formatters
Feature will be added soon.


#### Appenders
Appender is a destination of where log output will go.

##### Appender Formatter Override
You can pass formatter to any appender options to override logger-level formatter:
```javascript
{
    type: '<type>',
    options: {
        formatter: { /* ... */ } // optional
    }
}
```

##### Stdout Appender
```javascript
{
    type: 'stdout',
    options: {
        formatter: { /* ... */ } // optional
    }
}
```

##### File Appender
```javascript
{
    type: 'file',
    options: {
        path: '/tmp/out.log'
        formatter: { /* ... */ } // optional
    }
}
```
Where `path` is path to log output file.

##### Redis Appender
When using Redis Appenders messages would be PUBLISHed to `loginator:<namespace>` channel.

```javascript
{
    type: 'redis',
    options: {

        // redis client configuration
        host: 'localhost',
        port: 6379,
        options: {},

        // other
        namespace: 'default',
        formatter: { /* ... */ } // optional
    }
}
```
Where `host`, `port` and `options` are redis configuration params.


##### Custom Appenders
Feature will be added soon.

#### Examples
See `tests` directory for examples.

#### Tests
```bash
$ sudo npm install nodeunit -g
$ npm test
```

#### Author
* [Yaroslav Pogrebnyak](https://github.com/yyyar/)

#### License
MIT

