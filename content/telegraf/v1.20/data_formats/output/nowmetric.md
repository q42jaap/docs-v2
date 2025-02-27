---
title: ServiceNow Metrics output data format
description: Use the ServiceNow Metrics output data format (serializer) to output metrics in the ServiceNow Operational Intelligence format.
menu:
  telegraf_1_20:
    name: ServiceNow Metrics
    weight: 50
    parent: Output data formats
---

The ServiceNow Metrics output data format (serializer) outputs metrics in the [ServiceNow Operational Intelligence format](https://docs.servicenow.com/bundle/kingston-it-operations-management/page/product/event-management/reference/mid-POST-metrics.html).

It can be used to write to a file using the File output plugin, or for sending metrics to a MID Server with Enable REST endpoint activated using the standard telegraf HTTP output.
If you're using the HTTP output plugin, this serializer knows how to batch the metrics so you don't end up with an HTTP POST per metric.

An example event looks like:

```javascript
[{
    "metric_type": "Disk C: % Free Space",
    "resource": "C:\\",
    "node": "lnux100",
    "value": 50,
    "timestamp": 1473183012000,
    "ci2metric_id": {
        "node": "lnux100"
    },
    "source": “Telegraf”
}]
```

## Using with the HTTP output plugin

To send this data to a ServiceNow MID Server with Web Server extension activated, you can use the HTTP output plugin, there are some custom headers that you need to add to manage the MID Web Server authorization, here's a sample config for an HTTP output:

```toml
[[outputs.http]]
  ## URL is the address to send metrics to
  url = "http://<mid server fqdn or ip address>:9082/api/mid/sa/metrics"

  ## Timeout for HTTP message
  # timeout = "5s"

  ## HTTP method, one of: "POST" or "PUT"
  method = "POST"

  ## HTTP Basic Auth credentials
  username = 'evt.integration'
  password = 'P@$$w0rd!'

  ## Optional TLS Config
  # tls_ca = "/etc/telegraf/ca.pem"
  # tls_cert = "/etc/telegraf/cert.pem"
  # tls_key = "/etc/telegraf/key.pem"
  ## Use TLS but skip chain & host verification
  # insecure_skip_verify = false

  ## Data format to output.
  ## Each data format has it's own unique set of configuration options, read
  ## more about them here:
  ## https://github.com/influxdata/telegraf/blob/master/docs/DATA_FORMATS_OUTPUT.md
  data_format = "nowmetric"

  ## Additional HTTP headers
  [outputs.http.headers]
  #   # Should be set manually to "application/json" for json data_format
  Content-Type = "application/json"
  Accept = "application/json"
```

Starting with the London release, you also need to explicitly create event rule to allow binding of metric events to host CIs.

https://docs.servicenow.com/bundle/london-it-operations-management/page/product/event-management/task/event-rule-bind-metrics-to-host.html

## Using with the File output plugin

You can use the File output plugin to output the payload in a file.
In this case, just add the following section to your telegraf configuration file.

```toml
[[outputs.file]]
  ## Files to write to, "stdout" is a specially handled file.
  files = ["C:/Telegraf/metrics.out"]

  ## Data format to output.
  ## Each data format has its own unique set of configuration options, read
  ## more about them here:
  ## https://github.com/influxdata/telegraf/blob/master/docs/DATA_FORMATS_OUTPUT.md
  data_format = "nowmetric"
```
