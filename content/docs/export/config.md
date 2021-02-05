---
title: "Standalone Server"
description: "Configuring Standalone Server"
lead: "Configuring Standalone Server"
date: 2020-10-06T08:48:57+00:00
lastmod: 2020-10-06T08:48:57+00:00
draft: false
images: []
menu:
  docs:
    parent: "export"
weight: 100
toc: true
---

**RillRate** library bundled with a standalone server that provides a dashboard
and many ways to export metrics.

The standalone server started by default if you haven't set a link to a node
with `RILLRATE_NODE` environment variable. If that variable set than upstream
node will be used to register a prvodier and no export features available.

It makes sense, because if you have a node you have more capabilities to monitor
and control metrics, but if you haven't than obviously you want to use metrics
in system like *Prometheus* or just track and check them on-the-fly with an embedded
dashboard.

## Configuration

By default, *rllrate* library launches a standalone server and you don't need
to configure anything if you want to use a dashboard locally.

The dashbaord is available an [localhost:9090](http://localhost:9090) when you
started your app with installed `RillRate` provider.

If you want to start a `Prometheus` exporter or want to have access to the dashboard
by public IP address you should add a configuration file to control that.

### Configuration file

To set a configuration file to *rillrate* in your app just put a `rillrate.toml` file
into a working folder or set `RILLRATE_CONFIG` environment variable with a path to your
configuration file.

### Server settings

#### `[server]`

This section contains parameters of a standalone server.

##### `address`

Set IP for the standalone server. Server will bind a socket to this address.

#### Example

```toml
[server]
address = "0.0.0.0"
```

### Exporters

#### `[export.prometheus]`

Activates **Prometheus** exporter. When activated it will add `/metrics` endpoint to
the server.

##### `paths`

Set of paths that have to be published by the *Prometheus* exporter.

#### Example

```toml
[export.prometheus]
paths = [
    "my.counter.one",
    "my.gauge.multithread",
]
```

#### `[export.graphite]`

Activates **Graphite** exporter. When activated it start sending events to `` address.

##### `paths`

Set of paths that have to be published by the *Graphite* exporter.

#### Example

```toml
[export.graphite]
paths = [
    "my.counter.two",
]
```
