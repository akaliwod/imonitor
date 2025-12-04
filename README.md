# imonitor

## Overview

Set of templates that can be used with iserver to easily define
- Intersight metrics polled and exposed to Prometheus using [Intersight OpenTelemtry](./intersight-otel/README.md)
- Grafana [dashboard](./dashboard/README.md) examples visualizing collected Prometheus metrics

## Prerequisites

- [iserver](https://github.com/datacenter/iserver/) available
- access to OpenShift Cluster [defined](https://github.com/datacenter/iserver/blob/main/doc/ocp/Access.md)
- Intersight OpenTelemetry instance [deployed](https://github.com/datacenter/iserver/blob/main/doc/ocp/iotel/create_instance.md)
- Prometheus user-workload monitoring [enabled](https://github.com/datacenter/iserver/blob/main/doc/ocp/prometheus/enable_monitoring.md)
- Grafana operator [installed](https://github.com/datacenter/iserver/blob/main/doc/ocp/grafana/create_operator.md) and instance [created](https://github.com/datacenter/iserver/blob/main/doc/ocp/grafana/create_instance.md)

## HowTo

- Step 1: define Intersight metrics [polling](./intersight-otel/README.md)
- Step 2: create Dashboard from [template](./dashboard/README.md)
- Step 3: enjoy the outcome

![Dashboards](../image/dashboards.png)

![HW Collection](../image/hw_collection.png)