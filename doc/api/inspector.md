# Inspector

> Stability: 1 - Experimental

The `inspector` module provides an API for interacting with the V8 inspector.

It can be accessed using:

```js
const inspector = require('inspector');
```

## Class: inspector.Session

The `inspector.Session` is used for dispatching messages to the V8 inspector
back-end and receiving message responses and notifications.

### Constructor: new inspector.Session()
<!-- YAML
added: REPLACEME
-->

Create a new instance of the `inspector.Session` class. The inspector session
needs to be connected through [`session.connect()`][] before the messages
can be dispatched to the inspector backend.

`inspector.Session` is an [`EventEmitter`][] with the following events:

### Event: 'inspectorNotification'
<!-- YAML
added: REPLACEME
-->

* {Object} The notification message object

Emitted when any notification from the V8 Inspector is received.

```js
session.on('inspectorNotification', (message) => console.log(message.method));
// Debugger.paused
// Debugger.resumed
```

It is also possible to subscribe only to notifications with specific method:

### Event: &lt;inspector-protocol-method&gt;
<!-- YAML
added: REPLACEME
-->

* {Object} The notification message object

Emitted when an inspector notification is received that has its method field set
to the `<inspector-protocol-method>` value.

The following snippet installs a listener on the [`Debugger.paused`][]
event, and prints the reason for program suspension whenever program
execution is suspended (through breakpoints, for example):

```js
session.on('Debugger.paused', ({params}) => console.log(params.hitBreakpoints));
// [ '/node/test/inspector/test-bindings.js:11:0' ]
```

### session.connect()
<!-- YAML
added: REPLACEME
-->

Connects a session to the inspector back-end. An exception will be thrown
if there is already a connected session established either through the API or by
a front-end connected to the Inspector WebSocket port.

### session.post(method[, params][, callback])
<!-- YAML
added: REPLACEME
-->

* method {string}
* params {Object}
* callback {Function}

Posts a message to the inspector back-end. `callback` will be notified when
a response is received. `callback` is a function that accepts two optional
arguments - error and message-specific result.

```js
session.post('Runtime.evaluate', {'expression': '2 + 2'},
             (error, {result}) => console.log(result.value));
// Output: { type: 'number', value: 4, description: '4' }
```

The latest version of the V8 inspector protocol is published on the
[Chrome DevTools Protocol Viewer][].

Node inspector supports all the Chrome DevTools Protocol domains declared
by V8. Chrome DevTools Protocol domain provides an interface for interacting
with one of the runtime agents used to inspect the application state and listen
to the run-time events.

### session.disconnect()
<!-- YAML
added: REPLACEME
-->

Immediately close the session. All pending message callbacks will be called
with an error. [`session.connect()`] will need to be called to be able to send
messages again. Reconnected session will lose all inspector state, such as
enabled agents or configured breakpoints.

[`session.connect()`]: #sessionconnect
[`Debugger.paused`]: https://chromedevtools.github.io/devtools-protocol/v8/Debugger/#event-paused
[`EventEmitter`]: events.html#events_class_eventemitter
[Chrome DevTools Protocol Viewer]: https://chromedevtools.github.io/devtools-protocol/v8/