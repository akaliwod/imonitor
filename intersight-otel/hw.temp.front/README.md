# hw.temp.front

- Intersight metric [documentation](https://intersight.com/apidocs/introduction/hw-temperature/)
- pollers [template](./pollers.txt)
- prometheus metrics

```
# HELP intersight_hw_temp_front_avg
# TYPE intersight_hw_temp_front_avg gauge
intersight_hw_temp_front_avg{server_ip="10.10.10.21",server_name="server1",telemetry_sdk_name="intersight-otel"} 25 1764767773129
# HELP intersight_hw_temp_front_max
# TYPE intersight_hw_temp_front_max gauge
intersight_hw_temp_front_max{server_ip="10.10.10.21",server_name="server1",telemetry_sdk_name="intersight-otel"} 25 1764767773245
# HELP intersight_hw_temp_front_min
# TYPE intersight_hw_temp_front_min gauge
intersight_hw_temp_front_min{server_ip="10.10.10.21",server_name="server1",telemetry_sdk_name="intersight-otel"} 25 1764767773227
```

- iserver command [documentation](https://github.com/datacenter/iserver/blob/main/doc/ocp/iotel/README.md)

```
# iserver set ocp iotel --cluster bm1 --suffix iac --mode poller --template hw.temp.front --target server-ip:10.10.10.21-23 --pmode set --dir C:\tmp\imonitor\intersight-otel\ --no-confirm

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
- template: hw.temp.front
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
name = "hw_temp_front_avg"
datasource = "PhysicalEntities"
dimensions = []
filter = { type = "and", fields = [{type = "selector", dimension = "instrument.name", value = "hw.temperature"}, {type = "selector", dimension = "hw.temperature.sensor.name", value = "TEMP_FRONT"}, {type = "selector", dimension = "name", value = "TEMP_FRONT"}, {type = "selector", dimension = "intersight.asset.device_registration.moid", value = "111111"}]}
aggregations = [{type = "longSum", name = "count", fieldName = "hw.temperature_count"}, {type = "doubleSum", name = "hw.temperature-Sum", fieldName = "hw.temperature"}]
post_aggregations = [{"type" = "expression", "name" = "intersight.hw.temp.front.avg", "expression" = "(\"hw.temperature-Sum\" / \"count\")"}]
field_names = ["intersight.hw.temp.front.avg"]
otel_dimension_to_attribute_map = { }
otel_attributes = { server-name = "server1", server-ip = "1010.10.21" }
interval = 60

[[tspollers]]
name = "hw_temp_front_min"
datasource = "PhysicalEntities"
dimensions = []
filter = { type = "and", fields = [{type = "selector", dimension = "instrument.name", value = "hw.temperature"}, {type = "selector", dimension = "hw.temperature.sensor.name", value = "TEMP_FRONT"}, {type = "selector", dimension = "name", value = "TEMP_FRONT"}, {type = "selector", dimension = "intersight.asset.device_registration.moid", value = "111111"}]}
aggregations = [{type = "doubleMin", name = "intersight.hw.temp.front.min", fieldName = "hw.temperature_min"}]
post_aggregations = []
field_names = ["intersight.hw.temp.front.min"]
otel_dimension_to_attribute_map = { }
otel_attributes = { server-name = "server1", server-ip = "1010.10.21" }
interval = 60

[[tspollers]]
name = "hw_temp_front_max"
datasource = "PhysicalEntities"
dimensions = []
filter = { type = "and", fields = [{type = "selector", dimension = "instrument.name", value = "hw.temperature"}, {type = "selector", dimension = "hw.temperature.sensor.name", value = "TEMP_FRONT"}, {type = "selector", dimension = "name", value = "TEMP_FRONT"}, {type = "selector", dimension = "intersight.asset.device_registration.moid", value = "111111"}]}
aggregations = [{type = "doubleMax", name = "intersight.hw.temp.front.max", fieldName = "hw.temperature_max"}]
post_aggregations = []
field_names = ["intersight.hw.temp.front.max"]
otel_dimension_to_attribute_map = { }
otel_attributes = { server-name = "server1", server-ip = "1010.10.21" }
interval = 60
[[tspollers]]
name = "hw_temp_front_avg"
datasource = "PhysicalEntities"
dimensions = []
filter = { type = "and", fields = [{type = "selector", dimension = "instrument.name", value = "hw.temperature"}, {type = "selector", dimension = "hw.temperature.sensor.name", value = "TEMP_FRONT"}, {type = "selector", dimension = "name", value = "TEMP_FRONT"}, {type = "selector", dimension = "intersight.asset.device_registration.moid", value = "222222"}]}
aggregations = [{type = "longSum", name = "count", fieldName = "hw.temperature_count"}, {type = "doubleSum", name = "hw.temperature-Sum", fieldName = "hw.temperature"}]
post_aggregations = [{"type" = "expression", "name" = "intersight.hw.temp.front.avg", "expression" = "(\"hw.temperature-Sum\" / \"count\")"}]
field_names = ["intersight.hw.temp.front.avg"]
otel_dimension_to_attribute_map = { }
otel_attributes = { server-name = "server2", server-ip = "1010.10.22" }
interval = 60

[[tspollers]]
name = "hw_temp_front_min"
datasource = "PhysicalEntities"
dimensions = []
filter = { type = "and", fields = [{type = "selector", dimension = "instrument.name", value = "hw.temperature"}, {type = "selector", dimension = "hw.temperature.sensor.name", value = "TEMP_FRONT"}, {type = "selector", dimension = "name", value = "TEMP_FRONT"}, {type = "selector", dimension = "intersight.asset.device_registration.moid", value = "222222"}]}
aggregations = [{type = "doubleMin", name = "intersight.hw.temp.front.min", fieldName = "hw.temperature_min"}]
post_aggregations = []
field_names = ["intersight.hw.temp.front.min"]
otel_dimension_to_attribute_map = { }
otel_attributes = { server-name = "server2", server-ip = "1010.10.22" }
interval = 60

[[tspollers]]
name = "hw_temp_front_max"
datasource = "PhysicalEntities"
dimensions = []
filter = { type = "and", fields = [{type = "selector", dimension = "instrument.name", value = "hw.temperature"}, {type = "selector", dimension = "hw.temperature.sensor.name", value = "TEMP_FRONT"}, {type = "selector", dimension = "name", value = "TEMP_FRONT"}, {type = "selector", dimension = "intersight.asset.device_registration.moid", value = "222222"}]}
aggregations = [{type = "doubleMax", name = "intersight.hw.temp.front.max", fieldName = "hw.temperature_max"}]
post_aggregations = []
field_names = ["intersight.hw.temp.front.max"]
otel_dimension_to_attribute_map = { }
otel_attributes = { server-name = "server2", server-ip = "1010.10.22" }
interval = 60
[[tspollers]]
name = "hw_temp_front_avg"
datasource = "PhysicalEntities"
dimensions = []
filter = { type = "and", fields = [{type = "selector", dimension = "instrument.name", value = "hw.temperature"}, {type = "selector", dimension = "hw.temperature.sensor.name", value = "TEMP_FRONT"}, {type = "selector", dimension = "name", value = "TEMP_FRONT"}, {type = "selector", dimension = "intersight.asset.device_registration.moid", value = "333333"}]}
aggregations = [{type = "longSum", name = "count", fieldName = "hw.temperature_count"}, {type = "doubleSum", name = "hw.temperature-Sum", fieldName = "hw.temperature"}]
post_aggregations = [{"type" = "expression", "name" = "intersight.hw.temp.front.avg", "expression" = "(\"hw.temperature-Sum\" / \"count\")"}]
field_names = ["intersight.hw.temp.front.avg"]
otel_dimension_to_attribute_map = { }
otel_attributes = { server-name = "server3", server-ip = "1010.10.23" }
interval = 60

[[tspollers]]
name = "hw_temp_front_min"
datasource = "PhysicalEntities"
dimensions = []
filter = { type = "and", fields = [{type = "selector", dimension = "instrument.name", value = "hw.temperature"}, {type = "selector", dimension = "hw.temperature.sensor.name", value = "TEMP_FRONT"}, {type = "selector", dimension = "name", value = "TEMP_FRONT"}, {type = "selector", dimension = "intersight.asset.device_registration.moid", value = "333333"}]}
aggregations = [{type = "doubleMin", name = "intersight.hw.temp.front.min", fieldName = "hw.temperature_min"}]
post_aggregations = []
field_names = ["intersight.hw.temp.front.min"]
otel_dimension_to_attribute_map = { }
otel_attributes = { server-name = "server3", server-ip = "1010.10.23" }
interval = 60

[[tspollers]]
name = "hw_temp_front_max"
datasource = "PhysicalEntities"
dimensions = []
filter = { type = "and", fields = [{type = "selector", dimension = "instrument.name", value = "hw.temperature"}, {type = "selector", dimension = "hw.temperature.sensor.name", value = "TEMP_FRONT"}, {type = "selector", dimension = "name", value = "TEMP_FRONT"}, {type = "selector", dimension = "intersight.asset.device_registration.moid", value = "333333"}]}
aggregations = [{type = "doubleMax", name = "intersight.hw.temp.front.max", fieldName = "hw.temperature_max"}]
post_aggregations = []
field_names = ["intersight.hw.temp.front.max"]
otel_dimension_to_attribute_map = { }
otel_attributes = { server-name = "server3", server-ip = "1010.10.23" }
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
    name = "hw_temp_front_avg"
    datasource = "PhysicalEntities"
    dimensions = []
    filter = { type = "and", fields = [{type = "selector", dimension = "instrument.name", value = "hw.temperature"}, {type = "selector", dimension = "hw.temperature.sensor.name", value = "TEMP_FRONT"}, {type = "selector", dimension = "name", value = "TEMP_FRONT"}, {type = "selector", dimension = "intersight.asset.device_registration.moid", value = "111111"}]}
    aggregations = [{type = "longSum", name = "count", fieldName = "hw.temperature_count"}, {type = "doubleSum", name = "hw.temperature-Sum", fieldName = "hw.temperature"}]
    post_aggregations = [{"type" = "expression", "name" = "intersight.hw.temp.front.avg", "expression" = "(\"hw.temperature-Sum\" / \"count\")"}]
    field_names = ["intersight.hw.temp.front.avg"]
    otel_dimension_to_attribute_map = { }
    otel_attributes = { server-name = "server1", server-ip = "1010.10.21" }
    interval = 60

    [[tspollers]]
    name = "hw_temp_front_min"
    datasource = "PhysicalEntities"
    dimensions = []
    filter = { type = "and", fields = [{type = "selector", dimension = "instrument.name", value = "hw.temperature"}, {type = "selector", dimension = "hw.temperature.sensor.name", value = "TEMP_FRONT"}, {type = "selector", dimension = "name", value = "TEMP_FRONT"}, {type = "selector", dimension = "intersight.asset.device_registration.moid", value = "111111"}]}
    aggregations = [{type = "doubleMin", name = "intersight.hw.temp.front.min", fieldName = "hw.temperature_min"}]
    post_aggregations = []
    field_names = ["intersight.hw.temp.front.min"]
    otel_dimension_to_attribute_map = { }
    otel_attributes = { server-name = "server1", server-ip = "1010.10.21" }
    interval = 60

    [[tspollers]]
    name = "hw_temp_front_max"
    datasource = "PhysicalEntities"
    dimensions = []
    filter = { type = "and", fields = [{type = "selector", dimension = "instrument.name", value = "hw.temperature"}, {type = "selector", dimension = "hw.temperature.sensor.name", value = "TEMP_FRONT"}, {type = "selector", dimension = "name", value = "TEMP_FRONT"}, {type = "selector", dimension = "intersight.asset.device_registration.moid", value = "111111"}]}
    aggregations = [{type = "doubleMax", name = "intersight.hw.temp.front.max", fieldName = "hw.temperature_max"}]
    post_aggregations = []
    field_names = ["intersight.hw.temp.front.max"]
    otel_dimension_to_attribute_map = { }
    otel_attributes = { server-name = "server1", server-ip = "1010.10.21" }
    interval = 60
    [[tspollers]]
    name = "hw_temp_front_avg"
    datasource = "PhysicalEntities"
    dimensions = []
    filter = { type = "and", fields = [{type = "selector", dimension = "instrument.name", value = "hw.temperature"}, {type = "selector", dimension = "hw.temperature.sensor.name", value = "TEMP_FRONT"}, {type = "selector", dimension = "name", value = "TEMP_FRONT"}, {type = "selector", dimension = "intersight.asset.device_registration.moid", value = "222222"}]}
    aggregations = [{type = "longSum", name = "count", fieldName = "hw.temperature_count"}, {type = "doubleSum", name = "hw.temperature-Sum", fieldName = "hw.temperature"}]
    post_aggregations = [{"type" = "expression", "name" = "intersight.hw.temp.front.avg", "expression" = "(\"hw.temperature-Sum\" / \"count\")"}]
    field_names = ["intersight.hw.temp.front.avg"]
    otel_dimension_to_attribute_map = { }
    otel_attributes = { server-name = "server2", server-ip = "1010.10.22" }
    interval = 60

    [[tspollers]]
    name = "hw_temp_front_min"
    datasource = "PhysicalEntities"
    dimensions = []
    filter = { type = "and", fields = [{type = "selector", dimension = "instrument.name", value = "hw.temperature"}, {type = "selector", dimension = "hw.temperature.sensor.name", value = "TEMP_FRONT"}, {type = "selector", dimension = "name", value = "TEMP_FRONT"}, {type = "selector", dimension = "intersight.asset.device_registration.moid", value = "222222"}]}
    aggregations = [{type = "doubleMin", name = "intersight.hw.temp.front.min", fieldName = "hw.temperature_min"}]
    post_aggregations = []
    field_names = ["intersight.hw.temp.front.min"]
    otel_dimension_to_attribute_map = { }
    otel_attributes = { server-name = "server2", server-ip = "1010.10.22" }
    interval = 60

    [[tspollers]]
    name = "hw_temp_front_max"
    datasource = "PhysicalEntities"
    dimensions = []
    filter = { type = "and", fields = [{type = "selector", dimension = "instrument.name", value = "hw.temperature"}, {type = "selector", dimension = "hw.temperature.sensor.name", value = "TEMP_FRONT"}, {type = "selector", dimension = "name", value = "TEMP_FRONT"}, {type = "selector", dimension = "intersight.asset.device_registration.moid", value = "222222"}]}
    aggregations = [{type = "doubleMax", name = "intersight.hw.temp.front.max", fieldName = "hw.temperature_max"}]
    post_aggregations = []
    field_names = ["intersight.hw.temp.front.max"]
    otel_dimension_to_attribute_map = { }
    otel_attributes = { server-name = "server2", server-ip = "1010.10.22" }
    interval = 60
    [[tspollers]]
    name = "hw_temp_front_avg"
    datasource = "PhysicalEntities"
    dimensions = []
    filter = { type = "and", fields = [{type = "selector", dimension = "instrument.name", value = "hw.temperature"}, {type = "selector", dimension = "hw.temperature.sensor.name", value = "TEMP_FRONT"}, {type = "selector", dimension = "name", value = "TEMP_FRONT"}, {type = "selector", dimension = "intersight.asset.device_registration.moid", value = "333333"}]}
    aggregations = [{type = "longSum", name = "count", fieldName = "hw.temperature_count"}, {type = "doubleSum", name = "hw.temperature-Sum", fieldName = "hw.temperature"}]
    post_aggregations = [{"type" = "expression", "name" = "intersight.hw.temp.front.avg", "expression" = "(\"hw.temperature-Sum\" / \"count\")"}]
    field_names = ["intersight.hw.temp.front.avg"]
    otel_dimension_to_attribute_map = { }
    otel_attributes = { server-name = "server3", server-ip = "1010.10.23" }
    interval = 60

    [[tspollers]]
    name = "hw_temp_front_min"
    datasource = "PhysicalEntities"
    dimensions = []
    filter = { type = "and", fields = [{type = "selector", dimension = "instrument.name", value = "hw.temperature"}, {type = "selector", dimension = "hw.temperature.sensor.name", value = "TEMP_FRONT"}, {type = "selector", dimension = "name", value = "TEMP_FRONT"}, {type = "selector", dimension = "intersight.asset.device_registration.moid", value = "333333"}]}
    aggregations = [{type = "doubleMin", name = "intersight.hw.temp.front.min", fieldName = "hw.temperature_min"}]
    post_aggregations = []
    field_names = ["intersight.hw.temp.front.min"]
    otel_dimension_to_attribute_map = { }
    otel_attributes = { server-name = "server3", server-ip = "1010.10.23" }
    interval = 60

    [[tspollers]]
    name = "hw_temp_front_max"
    datasource = "PhysicalEntities"
    dimensions = []
    filter = { type = "and", fields = [{type = "selector", dimension = "instrument.name", value = "hw.temperature"}, {type = "selector", dimension = "hw.temperature.sensor.name", value = "TEMP_FRONT"}, {type = "selector", dimension = "name", value = "TEMP_FRONT"}, {type = "selector", dimension = "intersight.asset.device_registration.moid", value = "333333"}]}
    aggregations = [{type = "doubleMax", name = "intersight.hw.temp.front.max", fieldName = "hw.temperature_max"}]
    post_aggregations = []
    field_names = ["intersight.hw.temp.front.max"]
    otel_dimension_to_attribute_map = { }
    otel_attributes = { server-name = "server3", server-ip = "1010.10.23" }
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