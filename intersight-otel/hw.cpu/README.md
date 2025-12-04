# hw.cpu

- Intersight metric [documentation](https://intersight.com/apidocs/introduction/hw-cpu/)
- hw.cpu pollers [template](./pollers.txt)
- prometheus metrics

```
# HELP intersight_hw_cpu_avg
# TYPE intersight_hw_cpu_avg gauge
# HELP intersight_hw_cpu_max
# TYPE intersight_hw_cpu_max gauge
# HELP intersight_hw_cpu_min
# TYPE intersight_hw_cpu_min gauge
```

- iserver command [documentation](https://github.com/datacenter/iserver/blob/main/doc/ocp/iotel/README.md)

```
# iserver set ocp iotel --cluster bm1 --suffix iac --mode poller --template hw.cpu --target server-ip:10.10.10.21-23 --pmode set --dir C:\tmp\imonitor\intersight-otel\ --no-confirm

OpenShift Workflow - Intersight Open Telemetry (iotel) - Set Poller
===================================================================

OpenShift Cluster: bm1
Collect resources
- deployment
- secret
- config map

Instance
--------
- deployment intersight-otel/instance-iac
- config map intersight-otel/intersight-iac
- mode: set
- template: hw.cpu
- target: server-ip:10.10.10.21-23
- empty user-provided poller

Select servers...
Collect server api objects [20]...
Selected servers: 20

Resolving intersight ids
------------------------
Server IP: 10.10.10.21-23

~~~
otel_collector_endpoint = "http://127.0.0.1:4317"


[[tspollers]]
name = "hw_cpu_avg"
datasource = "PhysicalEntities"
dimensions = []
filter = { type = "and", fields = [{type = "selector", dimension = "instrument.name", value = "hw.cpu"}, {type = "selector", dimension = "intersight.asset.device_registration.moid", value = "111111"}]}
aggregations = [{type = "longSum", name = "count", fieldName = "hw.cpu.utilization_c0_count"}, {type = "doubleSum", name = "hw.cpu.utilization_c0-Sum", fieldName = "hw.cpu.utilization_c0"}]
post_aggregations = [{"type" = "expression", "name" = "intersight.hw.cpu.avg", "expression" = "(\"hw.cpu.utilization_c0-Sum\" / \"count\")"}]
field_names = ["intersight.hw.cpu.avg"]
otel_dimension_to_attribute_map = { }
otel_attributes = { server-name = "server1", server-ip = "10.10.10.21" }
interval = 60

[[tspollers]]
name = "hw_cpu_min"
datasource = "PhysicalEntities"
dimensions = []
filter = { type = "and", fields = [{type = "selector", dimension = "instrument.name", value = "hw.cpu"}, {type = "selector", dimension = "intersight.asset.device_registration.moid", value = "111111"}]}
aggregations = [{type = "doubleMin", name = "intersight.hw.cpu.min", fieldName = "hw.cpu.utilization_c0_min"}]
post_aggregations = []
field_names = ["intersight.hw.cpu.min"]
otel_dimension_to_attribute_map = { }
otel_attributes = { server-name = "server1", server-ip = "10.10.10.21" }
interval = 60

[[tspollers]]
name = "hw_cpu_max"
datasource = "PhysicalEntities"
dimensions = []
filter = { type = "and", fields = [{type = "selector", dimension = "instrument.name", value = "hw.cpu"}, {type = "selector", dimension = "intersight.asset.device_registration.moid", value = "111111"}]}
aggregations = [{type = "doubleMax", name = "intersight.hw.cpu.max", fieldName = "hw.cpu.utilization_c0_max"}]
post_aggregations = []
field_names = ["intersight.hw.cpu.max"]
otel_dimension_to_attribute_map = { }
otel_attributes = { server-name = "server1", server-ip = "10.10.10.21" }
interval = 60
[[tspollers]]
name = "hw_cpu_avg"
datasource = "PhysicalEntities"
dimensions = []
filter = { type = "and", fields = [{type = "selector", dimension = "instrument.name", value = "hw.cpu"}, {type = "selector", dimension = "intersight.asset.device_registration.moid", value = "222222"}]}
aggregations = [{type = "longSum", name = "count", fieldName = "hw.cpu.utilization_c0_count"}, {type = "doubleSum", name = "hw.cpu.utilization_c0-Sum", fieldName = "hw.cpu.utilization_c0"}]
post_aggregations = [{"type" = "expression", "name" = "intersight.hw.cpu.avg", "expression" = "(\"hw.cpu.utilization_c0-Sum\" / \"count\")"}]
field_names = ["intersight.hw.cpu.avg"]
otel_dimension_to_attribute_map = { }
otel_attributes = { server-name = "server2", server-ip = "10.10.10.22" }
interval = 60

[[tspollers]]
name = "hw_cpu_min"
datasource = "PhysicalEntities"
dimensions = []
filter = { type = "and", fields = [{type = "selector", dimension = "instrument.name", value = "hw.cpu"}, {type = "selector", dimension = "intersight.asset.device_registration.moid", value = "222222"}]}
aggregations = [{type = "doubleMin", name = "intersight.hw.cpu.min", fieldName = "hw.cpu.utilization_c0_min"}]
post_aggregations = []
field_names = ["intersight.hw.cpu.min"]
otel_dimension_to_attribute_map = { }
otel_attributes = { server-name = "server2", server-ip = "10.10.10.22" }
interval = 60

[[tspollers]]
name = "hw_cpu_max"
datasource = "PhysicalEntities"
dimensions = []
filter = { type = "and", fields = [{type = "selector", dimension = "instrument.name", value = "hw.cpu"}, {type = "selector", dimension = "intersight.asset.device_registration.moid", value = "222222"}]}
aggregations = [{type = "doubleMax", name = "intersight.hw.cpu.max", fieldName = "hw.cpu.utilization_c0_max"}]
post_aggregations = []
field_names = ["intersight.hw.cpu.max"]
otel_dimension_to_attribute_map = { }
otel_attributes = { server-name = "server2", server-ip = "10.10.10.22" }
interval = 60
[[tspollers]]
name = "hw_cpu_avg"
datasource = "PhysicalEntities"
dimensions = []
filter = { type = "and", fields = [{type = "selector", dimension = "instrument.name", value = "hw.cpu"}, {type = "selector", dimension = "intersight.asset.device_registration.moid", value = "333333"}]}
aggregations = [{type = "longSum", name = "count", fieldName = "hw.cpu.utilization_c0_count"}, {type = "doubleSum", name = "hw.cpu.utilization_c0-Sum", fieldName = "hw.cpu.utilization_c0"}]
post_aggregations = [{"type" = "expression", "name" = "intersight.hw.cpu.avg", "expression" = "(\"hw.cpu.utilization_c0-Sum\" / \"count\")"}]
field_names = ["intersight.hw.cpu.avg"]
otel_dimension_to_attribute_map = { }
otel_attributes = { server-name = "server3", server-ip = "10.10.10.23" }
interval = 60

[[tspollers]]
name = "hw_cpu_min"
datasource = "PhysicalEntities"
dimensions = []
filter = { type = "and", fields = [{type = "selector", dimension = "instrument.name", value = "hw.cpu"}, {type = "selector", dimension = "intersight.asset.device_registration.moid", value = "333333"}]}
aggregations = [{type = "doubleMin", name = "intersight.hw.cpu.min", fieldName = "hw.cpu.utilization_c0_min"}]
post_aggregations = []
field_names = ["intersight.hw.cpu.min"]
otel_dimension_to_attribute_map = { }
otel_attributes = { server-name = "server3", server-ip = "10.10.10.23" }
interval = 60

[[tspollers]]
name = "hw_cpu_max"
datasource = "PhysicalEntities"
dimensions = []
filter = { type = "and", fields = [{type = "selector", dimension = "instrument.name", value = "hw.cpu"}, {type = "selector", dimension = "intersight.asset.device_registration.moid", value = "333333"}]}
aggregations = [{type = "doubleMax", name = "intersight.hw.cpu.max", fieldName = "hw.cpu.utilization_c0_max"}]
post_aggregations = []
field_names = ["intersight.hw.cpu.max"]
otel_dimension_to_attribute_map = { }
otel_attributes = { server-name = "server3", server-ip = "10.10.10.23" }
interval = 60
~~~


Configure deployment replicas
-----------------------------
- namespace: intersight-otel
- name: instance-iac
- replicas: 0

~~~
apiVersion: apps/v1
kind: Deployment
metadata:
  name: instance-iac
  namespace: intersight-otel
spec:
  replicas: 0

~~~
Patch successful

Wait for desired replica pods...

Change Config Map
-----------------
- namespace: intersight-otel
- name: intersight-iac

~~~
apiVersion: v1
data:
  intersight-otel.toml: |-
    otel_collector_endpoint = "http://127.0.0.1:4317"


    [[tspollers]]
    name = "hw_cpu_avg"
    datasource = "PhysicalEntities"
    dimensions = []
    filter = { type = "and", fields = [{type = "selector", dimension = "instrument.name", value = "hw.cpu"}, {type = "selector", dimension = "intersight.asset.device_registration.moid", value = "111111"}]}
    aggregations = [{type = "longSum", name = "count", fieldName = "hw.cpu.utilization_c0_count"}, {type = "doubleSum", name = "hw.cpu.utilization_c0-Sum", fieldName = "hw.cpu.utilization_c0"}]
    post_aggregations = [{"type" = "expression", "name" = "intersight.hw.cpu.avg", "expression" = "(\"hw.cpu.utilization_c0-Sum\" / \"count\")"}]
    field_names = ["intersight.hw.cpu.avg"]
    otel_dimension_to_attribute_map = { }
    otel_attributes = { server-name = "server1", server-ip = "10.10.10.21" }
    interval = 60

    [[tspollers]]
    name = "hw_cpu_min"
    datasource = "PhysicalEntities"
    dimensions = []
    filter = { type = "and", fields = [{type = "selector", dimension = "instrument.name", value = "hw.cpu"}, {type = "selector", dimension = "intersight.asset.device_registration.moid", value = "111111"}]}
    aggregations = [{type = "doubleMin", name = "intersight.hw.cpu.min", fieldName = "hw.cpu.utilization_c0_min"}]
    post_aggregations = []
    field_names = ["intersight.hw.cpu.min"]
    otel_dimension_to_attribute_map = { }
    otel_attributes = { server-name = "server1", server-ip = "10.10.10.21" }
    interval = 60

    [[tspollers]]
    name = "hw_cpu_max"
    datasource = "PhysicalEntities"
    dimensions = []
    filter = { type = "and", fields = [{type = "selector", dimension = "instrument.name", value = "hw.cpu"}, {type = "selector", dimension = "intersight.asset.device_registration.moid", value = "111111"}]}
    aggregations = [{type = "doubleMax", name = "intersight.hw.cpu.max", fieldName = "hw.cpu.utilization_c0_max"}]
    post_aggregations = []
    field_names = ["intersight.hw.cpu.max"]
    otel_dimension_to_attribute_map = { }
    otel_attributes = { server-name = "server1", server-ip = "10.10.10.21" }
    interval = 60
    [[tspollers]]
    name = "hw_cpu_avg"
    datasource = "PhysicalEntities"
    dimensions = []
    filter = { type = "and", fields = [{type = "selector", dimension = "instrument.name", value = "hw.cpu"}, {type = "selector", dimension = "intersight.asset.device_registration.moid", value = "222222"}]}
    aggregations = [{type = "longSum", name = "count", fieldName = "hw.cpu.utilization_c0_count"}, {type = "doubleSum", name = "hw.cpu.utilization_c0-Sum", fieldName = "hw.cpu.utilization_c0"}]
    post_aggregations = [{"type" = "expression", "name" = "intersight.hw.cpu.avg", "expression" = "(\"hw.cpu.utilization_c0-Sum\" / \"count\")"}]
    field_names = ["intersight.hw.cpu.avg"]
    otel_dimension_to_attribute_map = { }
    otel_attributes = { server-name = "server2", server-ip = "10.10.10.22" }
    interval = 60

    [[tspollers]]
    name = "hw_cpu_min"
    datasource = "PhysicalEntities"
    dimensions = []
    filter = { type = "and", fields = [{type = "selector", dimension = "instrument.name", value = "hw.cpu"}, {type = "selector", dimension = "intersight.asset.device_registration.moid", value = "222222"}]}
    aggregations = [{type = "doubleMin", name = "intersight.hw.cpu.min", fieldName = "hw.cpu.utilization_c0_min"}]
    post_aggregations = []
    field_names = ["intersight.hw.cpu.min"]
    otel_dimension_to_attribute_map = { }
    otel_attributes = { server-name = "server2", server-ip = "10.10.10.22" }
    interval = 60

    [[tspollers]]
    name = "hw_cpu_max"
    datasource = "PhysicalEntities"
    dimensions = []
    filter = { type = "and", fields = [{type = "selector", dimension = "instrument.name", value = "hw.cpu"}, {type = "selector", dimension = "intersight.asset.device_registration.moid", value = "222222"}]}
    aggregations = [{type = "doubleMax", name = "intersight.hw.cpu.max", fieldName = "hw.cpu.utilization_c0_max"}]
    post_aggregations = []
    field_names = ["intersight.hw.cpu.max"]
    otel_dimension_to_attribute_map = { }
    otel_attributes = { server-name = "server2", server-ip = "10.10.10.22" }
    interval = 60
    [[tspollers]]
    name = "hw_cpu_avg"
    datasource = "PhysicalEntities"
    dimensions = []
    filter = { type = "and", fields = [{type = "selector", dimension = "instrument.name", value = "hw.cpu"}, {type = "selector", dimension = "intersight.asset.device_registration.moid", value = "333333"}]}
    aggregations = [{type = "longSum", name = "count", fieldName = "hw.cpu.utilization_c0_count"}, {type = "doubleSum", name = "hw.cpu.utilization_c0-Sum", fieldName = "hw.cpu.utilization_c0"}]
    post_aggregations = [{"type" = "expression", "name" = "intersight.hw.cpu.avg", "expression" = "(\"hw.cpu.utilization_c0-Sum\" / \"count\")"}]
    field_names = ["intersight.hw.cpu.avg"]
    otel_dimension_to_attribute_map = { }
    otel_attributes = { server-name = "server3", server-ip = "10.10.10.23" }
    interval = 60

    [[tspollers]]
    name = "hw_cpu_min"
    datasource = "PhysicalEntities"
    dimensions = []
    filter = { type = "and", fields = [{type = "selector", dimension = "instrument.name", value = "hw.cpu"}, {type = "selector", dimension = "intersight.asset.device_registration.moid", value = "333333"}]}
    aggregations = [{type = "doubleMin", name = "intersight.hw.cpu.min", fieldName = "hw.cpu.utilization_c0_min"}]
    post_aggregations = []
    field_names = ["intersight.hw.cpu.min"]
    otel_dimension_to_attribute_map = { }
    otel_attributes = { server-name = "server3", server-ip = "10.10.10.23" }
    interval = 60

    [[tspollers]]
    name = "hw_cpu_max"
    datasource = "PhysicalEntities"
    dimensions = []
    filter = { type = "and", fields = [{type = "selector", dimension = "instrument.name", value = "hw.cpu"}, {type = "selector", dimension = "intersight.asset.device_registration.moid", value = "333333"}]}
    aggregations = [{type = "doubleMax", name = "intersight.hw.cpu.max", fieldName = "hw.cpu.utilization_c0_max"}]
    post_aggregations = []
    field_names = ["intersight.hw.cpu.max"]
    otel_dimension_to_attribute_map = { }
    otel_attributes = { server-name = "server3", server-ip = "10.10.10.23" }
    interval = 60
kind: ConfigMap
metadata:
  name: name
  namespace: namespace

~~~
Config map updated

Configure deployment replicas
-----------------------------
- namespace: intersight-otel
- name: instance-iac
- replicas: 1

~~~
apiVersion: apps/v1
kind: Deployment
metadata:
  name: instance-iac
  namespace: intersight-otel
spec:
  replicas: 1

~~~
Patch successful

Wait for desired replica pods...

Completed tasks
- Config map changed
- Deployment restarted
```

[[Back]](../README.md)