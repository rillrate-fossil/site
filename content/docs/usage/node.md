+++
title = "Node.js bindings"
template = "doc.html"
+++

**RillRate** is available for Node.js [here](https://www.npmjs.com/package/@rillrate/rillrate).

Node.js version of the library based on the fast Rust inplementation and you can get
there more deep details about all methods and usage of them.

## Installation

To install the library you can use `npm` tool in your project:

```bash
npm install --save @rillrate/rillrate
```

When the library installed you can import it and start a provider:

```js
rillrate = require('@rillrate/rillrate');
rillrate.install();
```

## Using tracers

All rillrate tracers available in Node.js binding.

### Tracer

Every `Tracer` has methods that you can use to explore some conditions and properties.

#### Methods

##### `is_active()` -> bool

Returns `true` is the `Tracer` is active or `false` if it's not active.

### Gauge

To create `Gauge` call it as a construction with a `Path`:

```js
gauge = new rillrate.Gauge("my.gauge");
gauge.set(1.23);
```

#### Methods

##### `inc(delta)`

Increments an internal value with the provided delta.

##### `dec(delta)`

Decrements an internal value with the provided delta.

##### `set(delta)`

Set an internal value.

### Counter

To create `Counter` call it as a construction with a `Path`:

```python
counter = new rillrate.Counter("my.counter");
counter.inc(1.0);
```

#### Methods

##### `inc(delta)`

Increments an internal counter with the provided delta that can't be negative.

### Logger

To create `Logger` call it as a construction with a `Path`:

```python
logger = new rillrate.Logger("my.logger");
logger.log("message to log");
```

#### Methods

##### `log(message)`

Writes a message and send it to subscribers.

## Expansions

*In progress...*
