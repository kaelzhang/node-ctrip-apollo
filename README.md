[![Build Status](https://travis-ci.org/kaelzhang/node-ctrip-apollo.svg?branch=master)](https://travis-ci.org/kaelzhang/node-ctrip-apollo)
[![Coverage](https://codecov.io/gh/kaelzhang/node-ctrip-apollo/branch/master/graph/badge.svg)](https://codecov.io/gh/kaelzhang/node-ctrip-apollo)
<!-- optional appveyor tst
[![Windows Build Status](https://ci.appveyor.com/api/projects/status/github/kaelzhang/node-ctrip-apollo?branch=master&svg=true)](https://ci.appveyor.com/project/kaelzhang/node-ctrip-apollo)
-->
<!-- optional npm version
[![NPM version](https://badge.fury.io/js/ctrip-apollo.svg)](http://badge.fury.io/js/ctrip-apollo)
-->
<!-- optional npm downloads
[![npm module downloads per month](http://img.shields.io/npm/dm/ctrip-apollo.svg)](https://www.npmjs.org/package/ctrip-apollo)
-->
<!-- optional dependency status
[![Dependency Status](https://david-dm.org/kaelzhang/node-ctrip-apollo.svg)](https://david-dm.org/kaelzhang/node-ctrip-apollo)
-->

# ctrip-apollo

Node.js client for ctrip [apollo](https://github.com/ctripcorp/apollo)

## Install

```sh
$ npm i ctrip-apollo
```

## Usage

```js
const apollo = require('ctrip-apollo')

const client = apollo({
  host: 'http://localhost:8070',
  appId: '100004458'
})

await client.ready()
```

## apollo(options)

- **options** `Object`
  - **host** `URL::host` the host (including protocol, hostname and port) of the apollo config service
  - **appId** `string` apollo application id
  - **cluster** `string` cluster name
  - **namespace?** `string='application'` namespace name. Defaults to `'application'`
  - **refreshInterval?** `number=5 * 60 * 1000` interval in milliseconds to pull the new configurations. Set this option to `0` to disable the feature. Defaults to `5` minutes
  - **longPolling?** `boolean=true` set to `false` to disable HTTP long polling.
  - **cachePath?** `path` set the `cachePath` to enable the feature to save configurations to the disk

Returns `ApolloClient` and class `ApolloClient` is a subclass of [`EventEmitter`](https://nodejs.org/dist/latest-v11.x/docs/api/events.html#events_class_eventemitter)

### await client.ready()

Fetch the configuration from config service for the first time or fallback to local cache file.

Make sure we await `client.ready()` before any `client.config()` or `client.get()` methods are invoked.

### client.config()

Returns `Object` all configurations for the current namespace / application

```js
console.log('application config', client.config())
```

### client.get(key)

- **key** `string` config key name

Returns the config value of the corresponding key `key`

```js
console.log('config for host', client.get('host'))
```

### client.namespace(namespace)

- **namespace** `string` namespace name

Creates and returns a new `ApolloClient` with `namespace`. If the given `namespace` is equivalent to the namespace of `client`, `this` object will be returned.

```js
const ns = client.namespace('web')
```

### Event: `change`

Emits if the any configuration changes. `ctrip-apollo` uses HTTP long polling to listen the changes of the configurations.

And the client will pull the new configurations every `5` miutes which can be changed by `options.refreshInterval`

```js
client.on('change', e => {
  console.log('key', e.key)
  console.log('oldValue', e.oldValue)
  console.log('newValue', e.newValue)
  console.log('type', e.type)
})
```

If `options.refreshInterval` is set to `0` and `options.longPolling` is set to `false`, then the event will never emit.

## License

MIT
