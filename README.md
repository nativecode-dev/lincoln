# @nofrills/lincoln

[![npm](https://img.shields.io/npm/v/@nofrills/lincoln.svg?style=flat-square)](https://www.npmjs.com/package/@nofrills/lincoln)
[![Travis](https://img.shields.io/travis/nativecode-dev/nofrills-lincoln.svg?style=flat-square&label=travis)](https://travis-ci.org/nativecode-dev/nofrills-lincoln)
[![David](https://img.shields.io/david/nativecode-dev/nofrills-lincoln.svg?style=flat-square&label=deps)](https://www.npmjs.com/package/@nofrills/lincoln)
[![David](https://img.shields.io/david/dev/nativecode-dev/nofrills-lincoln.svg?style=flat-square&label=devdeps)](https://www.npmjs.com/package/@nofrills/lincoln)

# Install

```bash
npm install --save @nofrills/lincoln
```

# Quick Start
### Javascript
```javascript
const logging = require('@nofrills/lincoln')
const logger = new logging.Lincoln('myapp')
logger.debug('Hello, cruel Javascript world!')
```

### Typescript
```typescript
import { Lincoln } from '@nofrills/lincoln'
const logger = new Lincoln('myapp')
logger.debug('Hello, cruel Typescript world!')
```

NOTE: From here on, we'll use Javascript in examples.

# Options
You can also use an options object to inject some customizations and extensions.

```javascript
const logging = require('@nofrills/lincoln')
const options = {
  filters: [(log) => log.tag.indexOf('debug') >= 0],
  interceptors: [logging.Debug],
  namespace: 'myapp',
}

const logger = new logging.Lincoln(options)
logger.debug('Something something options something...')
```

# Extending
Although our mantra is "no frills", that doesn't mean we can't offer something extensible so that someone else can add all the frills they want!

## Child loggers
You can extend an existing logger so that it writes messages to uniquely identify the child logger.

```javascript
const logging = require('@nofrills/lincoln')
const logger = new logging.Lincoln('myapp')
// -> messages will write "myapp"
const child = logger.extend('child')
// -> messages will write "myapp:child"
const log = child.extend('log')
// -> messages will write "myapp:child:log"
```

## Filters
You can filter log messages so that they are ignored. Use the `filters` array property on `options`. When the filter is called, return `true` to mark the item as filtered or `false` to indicate it should not be filtered.

NOTE: If multiple filters are assigned, something could be filtered by any of the handlers.

```javascript
const MySuperAwesomeFilter = (log) => false
const options = {
  filters[MySuperAwesomeFilter]
}
```

## Interceptors
Interceptors allow mutation of the log message before it is written or can be used to output the message. For instance, if we want to forward our log messages to the console, we can use:

```javascript
const MySuperAwesomeConsoleWriter =
  (log) => console.log(log.parameters)

const options = {
  interceptors: [MySuperAwesomeConsoleWriter]
}
```

# Integrations
You can integrate various plugins or functions to mutate the log message. For instance, you can use the `@nofrills/scrubs` package to remove sensitive data before being logged.

```javascript
const logging = require('@nofrills/lincoln')
const scrubs = require('@nofrills/scrubs')

const scrubber = (log) => {
  log.parameters = scrubs.scrub(log.parameters)
  return log
}

const logger = new logging.Lincoln({
  interceptors: [scrubber],
  namespace: 'myapp'
})
logger.debug({
  password: 'SuperSecret',
})
// -> should return { password: "<secret>" }
```