+++
title = "Core Rust library"
template = "doc.html"
weight = 1
+++

**RillRate** is available as Rust crate [here](https://crates.io/crates/rillrate).

This is the main implementation of the library and other languages uses this implementations as well.
You can get more details on this page about basic usage of the library.


## Installation

To add it to your Rust application you should add the dependency to your `Cargo.toml` file.
You can do it using the following command:

```bash
cargo add -s rillrate
```

**Note:** `-s` flags keep your dependencies list sorted. Skip this flag if you have own order of dependencies.

You can also add it directly to `[dependencies]` section of your `Cargo.toml` file:

```toml
[dependencies]
rillrate = "*"
```

**Note:** Replace asterisk with an actual version of the library. Or the sepcific one that you want to use.

## Activating a provider

To start the provider you should `install` it using the following code:

```rust
use rillrate::RillRate;
use std::error::Error;

fn main() -> Result<(), Box<dyn Error>> {
    let _rillrate = RillRate::from_env("my-app")?;
    Ok(())
}
```

Put it somewhere in `main` functions before any `Tracer` created, because on installation
the tracer will be registered in the shared space where tracers can get it to register themselves.

Also as you can see we keep the instance of `RillRate` tracer, because the worker is alive when
the instance of the tracer exists and gracefully terminates all routines when the instance dropped.

**Note:** Remember that `RillRate` instance can block the thread for a tiny interval while all routines
completely terminated. It's important to deliver all metrics even if you want to shutdown
the application.

### Provider

`RillRate` is a provider.

#### Methods

##### `from_env(name: &str) -> RillRate`

Spawns a new instance of a `Provider`.

## Using tracers

`Tracer` is a tool with a unique id to deliver metrics to a server. Every `Tracer`
has a sepcial type of a stream that attached to provider's type.

`Tracer` can be **active** and **reactive**.

**Active** tracers always forwards data to a worker and a server can get a snapshot
of the data any time it needed.

**Reactive** tracers counts data internally,
but forward updates to a worker and server only when the stream of the provider has
subscribers. In other words reactive tracers needs less resources, because
they are inactive till they activated by subscribers.



### Tracer

Every `Tracer` has methods that you can use to explore some conditions and properties.

#### Methods

##### `is_active(&self) -> bool`

Returns `true` is the `Tracer` is active. If a `Tracer` is `active` than it always
returns `true`.

##### `path(&self) -> &Path`

Return a reference to a parsed `Path` of the `Tracer`.



### Counter

`Counter` just adds all incoming values and represents cumulative sum of them.

```rust
use rillrate::Counter;

let counter = Counter::create("my.counter")?;
counter.inc(1.0);
```

#### Methods

##### `create(path: &str) -> Result<Self, Error>`

Creates a new instance of `Counter` with the provided `path`.

##### `inc(&self, delta: f64)`

Increments an internal counter with the provided delta that can't be negative.

#### Rendering

| RillRate View     | Prometheus | Graphite |
| ----------------- | ---------- | -------- |
| Panel with number | `COUNTER`  | as `f64` |



### Gauge

`Gauge` is a value in a range.

```rust
use rillrate::Gauge;

let gauge = Gauge::create("my.gauge", 0.0, 100.0)?;
gauge.set(55.0);
```

#### Methods

##### `create(path: &str, min: f64, max: f64) -> Result<Self, Error>`

Creates a new instance of `Gauge` with the provided `path` and the range between `min` and `max` values.

##### `set(&self, value: f64)`

Set the gauge value in range.

#### Rendering

| RillRate View     | Prometheus | Graphite |
| ----------------- | ---------- | -------- |
| Panel with gauge  | `GAUGE`    | as `f64` |



### Pulse

`Pulse` is a frame of values that rendered as a moving chart.

```rust
use rillrate::Pulse;

let pulse = Pulse::create("my.pulse")?;
pulse.inc(1.0);
pulse.dec(1.0);
pulse.set(1.23);
```

As you can see it doesn't require to be mutable with `mut`, because tracers are thread-safe
and designed to be used anywhere in your code without limitations.

#### Methods

##### `create(path: &str) -> Result<Self, Error>`

Creates a new instance of `Pulse` with the provided `path`.

##### `inc(&self, delta: f64)`

Increments an internal value with the provided delta.

##### `dec(&self, delta: f64)`

Decrements an internal value with the provided delta.

##### `set(&self, value: f64)`

Set an internal value.

#### Rendering

| RillRate View     | Prometheus | Graphite |
| ----------------- | ---------- | -------- |
| Real-time chart   | `GAUGE`    | as `f64` |



### Histogram

`Histogram` counts values in buckets.

```rust
use rillrate::Histogram;

let histogram = Histogram::create("my.histogram", &[10.0, 50.0, 100.0, 250.0])?;
histogram.add(125.0);
```

#### Methods

##### `create(path: &str, levels: &[f64]) -> Result<Self, Error>`

Creates a new instance of `Histogram` with the provided `path` and `levels` (buckets).

##### `add(&self, value: f64)`

Adds the value to the histogram.

#### Rendering

| RillRate View        | Prometheus  | Graphite |
| -------------------- | ----------- | -------- |
| Panel with histogram | `HISTOGRAM` | N/A      |



### Dict

`Dict` is a table of key-value pairs.

```rust
use rillrate::Dict;

let dict = Dict::create("my.dict")?;
dict.add(125.0);
```

#### Methods

##### `create(path: &str) -> Result<Self, Error>`

Creates a new instance of `Dict` with the provided `path`.

##### `set(&self, key: impl ToString, value: impl ToString)`

Set the value in the dict.

#### Rendering

| RillRate View   | Prometheus | Graphite |
| ----------------| ---------- | -------- |
| Table with dict | N/A        | N/A      |



### Logger

`Logger` represents an ordinary logger with a single level.

`Logger` has only one level of logging. If you want to have multiple levels you
should create multiple loggers and separate them by name. For example if you want
to write logs for module *my.module.log* in three levels `info`, `warn` and `error` you
can create three loggers with corresponding paths: *my.module.log.info*,
*my.module.log.warn* and *my.module.log.error*.

The beniefit of this approach is that you can activate any level absolutely separately.

```rust
use rillrate::Logger;

let logger = Logger::create("my.logger")?;
logger.log("my text message");
```

#### Methods

##### `create(path: &str) -> Result<Self, Error>`

Creates a new instance of `Logger` with the provided `path`.

##### `log(&self, message: impl ToString)`

Writes a message and send it to subscribers.

#### Rendering

| RillRate View     | Prometheus | Graphite |
| ----------------- | ---------- | -------- |
| Row in log-stream | N/A        | N/A      |



## Expansions

*In progress...*
