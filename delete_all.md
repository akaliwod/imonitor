# From Hero to Zero (Output)

[[Back]](./get_things_done.md)

```
# iserver delete ocp task --cluster bm1 --filename C:\tmp\task.json --no-confirm

OpenShift Workflow - Delete Tasks
=================================

Validate Input
--------------
Completed


OpenShift Workflow - Grafana Operator - Delete instance
=======================================================

OpenShift Cluster: bm1

Grafana Data Source
-------------------
- delete test-thanos

Cluster Role Binding
--------------------
Service Account [test-sa] associated with role [cluster-monitoring-view] in ClusterRoleBinding CR [test-sa-view]
Delete ClusterRoleBinding CR [test-sa-view]

Delete Grafana Instance
-----------------------
- namespace: grafana-operator
- name: grafana-operator
Wait until grafana gone [timeout:60s]...
Wait until grafana resources are gone [timeout:60s]...
Wait for deployments deleted (optional: False)...
- grafana-operator/test-deployment
Wait for no service account...


Completed tasks
---------------
- Grafana instance deleted

OpenShift Workflow - Grafana Operator - Wipe Resources
======================================================

OpenShift Cluster: bm1

Grafana
-------
- no resources found

GrafanaAlertRuleGroup
---------------------
- no resources found

GrafanaContactPoint
-------------------
- no resources found

GrafanaDashboard
----------------
- grafana-operator/hw-cpu-utilization-my-server
- grafana-operator/hw-my-server
- grafana-operator/hw-power-utilization-my-server
- grafana-operator/hw-temp-bottom-my-server
- grafana-operator/hw-temp-front-my-server
- grafana-operator/hw-temp-middle-my-server
- grafana-operator/hw-temp-p1-my-server
- grafana-operator/hw-temp-p2-my-server
- grafana-operator/hw-temp-top-my-server

GrafanaDatasource
-----------------
- no resources found

GrafanaFolder
-------------
- no resources found

GrafanaLibraryPanel
-------------------
- no resources found

GrafanaMuteTiming
-----------------
- no resources found

GrafanaNotificationPolicy
-------------------------
- no resources found

GrafanaNotificationPolicyRoute
------------------------------
- no resources found

GrafanaNotificationTemplate
---------------------------
- no resources found

Completed tasks
- Grafana resources deleted

OpenShift Workflow - Grafana Operator - Delete Operator
=======================================================

OpenShift Cluster: bm1

Check Grafana resource
----------------------
- Grafana
- GrafanaAlertRuleGroup
- GrafanaContactPoint
- GrafanaDashboard
- GrafanaDatasource
- GrafanaFolder
- GrafanaLibraryPanel
- GrafanaMuteTiming
- GrafanaNotificationPolicy
- GrafanaNotificationPolicyRoute
- GrafanaNotificationTemplate

Delete Subscription
-------------------
- subscription: grafana-operator/grafana-operator
- checking cluster service version...
- csv found and will be deleted: grafana-operator/grafana-operator.v5.20.0
- wait for no subscription
- check cluster service version: grafana-operator/grafana-operator.v5.20.0
- wait for no csv
Wait for deployments deleted (optional: False)...
- grafana-operator/grafana-operator-controller-manager-v5

Delete Operator Group
---------------------
- namespace: grafana-operator
- name: grafana-operator-group
- wait for no operator group

Delete Namespace
----------------
- name: grafana-operator

Namespace [grafana-operator] resources
- no pods
- no deployments
- no daemon sets
- no replica sets
- no services
- no pvcs
- wait for no namespace

Completed tasks
- Grafana Operator deleted
- Operator group deleted
- Namespace deleted

OpenShift Workflow - Intersight Open Telemetry (iotel) - Delete Poller
======================================================================

OpenShift Cluster: bm1
Collect resources
- deployment
- secret
- config map
Poller selection
----------------
- suffix: iac

Instance
--------
- deployment intersight-otel/instance-iac
- config map intersight-otel/intersight-iac

Removed pollers
---------------

[[tspollers]]
name = "hw_cpu_avg"
datasource = "PhysicalEntities"
dimensions = []
filter = { type = "and", fields = [{type = "selector", dimension = "instrument.name", value = "hw.cpu"}, {type = "selector", dimension = "intersight.asset.device_registration.moid", value = "111111"}]}
aggregations = [{type = "longSum", name = "count", fieldName = "hw.cpu.utilization_c0_count"}, {type = "doubleSum", name = "hw.cpu.utilization_c0-Sum", fieldName = "hw.cpu.utilization_c0"}]
post_aggregations = [{"type" = "expression", "name" = "intersight.hw.cpu.avg", "expression" = "(\"hw.cpu.utilization_c0-Sum\" / \"count\")"}]
field_names = ["intersight.hw.cpu.avg"]
otel_dimension_to_attribute_map = { }
otel_attributes = { server-name = "my-server", server-ip = "10.10.10.11" }
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
otel_attributes = { server-name = "my-server", server-ip = "10.10.10.11" }
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
otel_attributes = { server-name = "my-server", server-ip = "10.10.10.11" }
interval = 60

[[tspollers]]
name = "hw_power_avg"
datasource = "PhysicalEntities"
dimensions = []
filter = { type = "and", fields = [{type = "selector", dimension = "instrument.name", value = "hw.host"}, {type = "selector", dimension = "intersight.asset.device_registration.moid", value = "111111"}]}
aggregations = [{type = "longSum", name = "count", fieldName = "hw.host.power_count"}, {type = "doubleSum", name = "hw.host.power-Sum", fieldName = "hw.host.power"}]
post_aggregations = [{"type" = "expression", "name" = "intersight.hw.power.avg", "expression" = "(\"hw.host.power-Sum\" / \"count\")"}]
field_names = ["intersight.hw.power.avg"]
otel_dimension_to_attribute_map = { }
otel_attributes = { server-name = "my-server", server-ip = "10.10.10.11" }
interval = 60

[[tspollers]]
name = "hw_power_min"
datasource = "PhysicalEntities"
dimensions = []
filter = { type = "and", fields = [{type = "selector", dimension = "instrument.name", value = "hw.host"}, {type = "selector", dimension = "intersight.asset.device_registration.moid", value = "111111"}]}
aggregations = [{type = "doubleMin", name = "intersight.hw.power.min", fieldName = "hw.host.power_min"}]
post_aggregations = []
field_names = ["intersight.hw.power.min"]
otel_dimension_to_attribute_map = { }
otel_attributes = { server-name = "my-server", server-ip = "10.10.10.11" }
interval = 60

[[tspollers]]
name = "hw_power_max"
datasource = "PhysicalEntities"
dimensions = []
filter = { type = "and", fields = [{type = "selector", dimension = "instrument.name", value = "hw.host"}, {type = "selector", dimension = "intersight.asset.device_registration.moid", value = "111111"}]}
aggregations = [{type = "doubleMax", name = "intersight.hw.power.max", fieldName = "hw.host.power_min"}]
post_aggregations = []
field_names = ["intersight.hw.power.max"]
otel_dimension_to_attribute_map = { }
otel_attributes = { server-name = "my-server", server-ip = "10.10.10.11" }
interval = 60

[[tspollers]]
name = "hw_temp_bottom_avg"
datasource = "PhysicalEntities"
dimensions = []
filter = { type = "and", fields = [{type = "selector", dimension = "instrument.name", value = "hw.temperature"}, {type = "selector", dimension = "hw.temperature.sensor.name", value = "TEMP_REAR_BOT"}, {type = "selector", dimension = "name", value = "TEMP_REAR_BOT"}, {type = "selector", dimension = "intersight.asset.device_registration.moid", value = "111111"}]}
aggregations = [{type = "longSum", name = "count", fieldName = "hw.temperature_count"}, {type = "doubleSum", name = "hw.temperature-Sum", fieldName = "hw.temperature"}]
post_aggregations = [{"type" = "expression", "name" = "intersight.hw.temp.bottom.avg", "expression" = "(\"hw.temperature-Sum\" / \"count\")"}]
field_names = ["intersight.hw.temp.bottom.avg"]
otel_dimension_to_attribute_map = { }
otel_attributes = { server-name = "my-server", server-ip = "10.10.10.11" }
interval = 60

[[tspollers]]
name = "hw_temp_bottom_min"
datasource = "PhysicalEntities"
dimensions = []
filter = { type = "and", fields = [{type = "selector", dimension = "instrument.name", value = "hw.temperature"}, {type = "selector", dimension = "hw.temperature.sensor.name", value = "TEMP_REAR_BOT"}, {type = "selector", dimension = "name", value = "TEMP_REAR_BOT"}, {type = "selector", dimension = "intersight.asset.device_registration.moid", value = "111111"}]}
aggregations = [{type = "doubleMin", name = "intersight.hw.temp.bottom.min", fieldName = "hw.temperature_min"}]
post_aggregations = []
field_names = ["intersight.hw.temp.bottom.min"]
otel_dimension_to_attribute_map = { }
otel_attributes = { server-name = "my-server", server-ip = "10.10.10.11" }
interval = 60

[[tspollers]]
name = "hw_temp_bottom_max"
datasource = "PhysicalEntities"
dimensions = []
filter = { type = "and", fields = [{type = "selector", dimension = "instrument.name", value = "hw.temperature"}, {type = "selector", dimension = "hw.temperature.sensor.name", value = "TEMP_REAR_BOT"}, {type = "selector", dimension = "name", value = "TEMP_REAR_BOT"}, {type = "selector", dimension = "intersight.asset.device_registration.moid", value = "111111"}]}
aggregations = [{type = "doubleMax", name = "intersight.hw.temp.bottom.max", fieldName = "hw.temperature_max"}]
post_aggregations = []
field_names = ["intersight.hw.temp.bottom.max"]
otel_dimension_to_attribute_map = { }
otel_attributes = { server-name = "my-server", server-ip = "10.10.10.11" }
interval = 60

[[tspollers]]
name = "hw_temp_front_avg"
datasource = "PhysicalEntities"
dimensions = []
filter = { type = "and", fields = [{type = "selector", dimension = "instrument.name", value = "hw.temperature"}, {type = "selector", dimension = "hw.temperature.sensor.name", value = "TEMP_FRONT"}, {type = "selector", dimension = "name", value = "TEMP_FRONT"}, {type = "selector", dimension = "intersight.asset.device_registration.moid", value = "111111"}]}
aggregations = [{type = "longSum", name = "count", fieldName = "hw.temperature_count"}, {type = "doubleSum", name = "hw.temperature-Sum", fieldName = "hw.temperature"}]
post_aggregations = [{"type" = "expression", "name" = "intersight.hw.temp.front.avg", "expression" = "(\"hw.temperature-Sum\" / \"count\")"}]
field_names = ["intersight.hw.temp.front.avg"]
otel_dimension_to_attribute_map = { }
otel_attributes = { server-name = "my-server", server-ip = "10.10.10.11" }
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
otel_attributes = { server-name = "my-server", server-ip = "10.10.10.11" }
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
otel_attributes = { server-name = "my-server", server-ip = "10.10.10.11" }
interval = 60

[[tspollers]]
name = "hw_temp_middle_avg"
datasource = "PhysicalEntities"
dimensions = []
filter = { type = "and", fields = [{type = "selector", dimension = "instrument.name", value = "hw.temperature"}, {type = "selector", dimension = "hw.temperature.sensor.name", value = "TEMP_REAR_MID"}, {type = "selector", dimension = "name", value = "TEMP_REAR_MID"}, {type = "selector", dimension = "intersight.asset.device_registration.moid", value = "111111"}]}
aggregations = [{type = "longSum", name = "count", fieldName = "hw.temperature_count"}, {type = "doubleSum", name = "hw.temperature-Sum", fieldName = "hw.temperature"}]
post_aggregations = [{"type" = "expression", "name" = "intersight.hw.temp.middle.avg", "expression" = "(\"hw.temperature-Sum\" / \"count\")"}]
field_names = ["intersight.hw.temp.middle.avg"]
otel_dimension_to_attribute_map = { }
otel_attributes = { server-name = "my-server", server-ip = "10.10.10.11" }
interval = 60

[[tspollers]]
name = "hw_temp_middle_min"
datasource = "PhysicalEntities"
dimensions = []
filter = { type = "and", fields = [{type = "selector", dimension = "instrument.name", value = "hw.temperature"}, {type = "selector", dimension = "hw.temperature.sensor.name", value = "TEMP_REAR_MID"}, {type = "selector", dimension = "name", value = "TEMP_REAR_MID"}, {type = "selector", dimension = "intersight.asset.device_registration.moid", value = "111111"}]}
aggregations = [{type = "doubleMin", name = "intersight.hw.temp.middle.min", fieldName = "hw.temperature_min"}]
post_aggregations = []
field_names = ["intersight.hw.temp.middle.min"]
otel_dimension_to_attribute_map = { }
otel_attributes = { server-name = "my-server", server-ip = "10.10.10.11" }
interval = 60

[[tspollers]]
name = "hw_temp_middle_max"
datasource = "PhysicalEntities"
dimensions = []
filter = { type = "and", fields = [{type = "selector", dimension = "instrument.name", value = "hw.temperature"}, {type = "selector", dimension = "hw.temperature.sensor.name", value = "TEMP_REAR_MID"}, {type = "selector", dimension = "name", value = "TEMP_REAR_MID"}, {type = "selector", dimension = "intersight.asset.device_registration.moid", value = "111111"}]}
aggregations = [{type = "doubleMax", name = "intersight.hw.temp.middle.max", fieldName = "hw.temperature_max"}]
post_aggregations = []
field_names = ["intersight.hw.temp.middle.max"]
otel_dimension_to_attribute_map = { }
otel_attributes = { server-name = "my-server", server-ip = "10.10.10.11" }
interval = 60

[[tspollers]]
name = "hw_temp_p1_avg"
datasource = "PhysicalEntities"
dimensions = []
filter = { type = "and", fields = [{type = "selector", dimension = "instrument.name", value = "hw.temperature"}, {type = "selector", dimension = "hw.temperature.sensor.name", value = "P1_TEMP_SENS"}, {type = "selector", dimension = "name", value = "P1_TEMP_SENS"}, {type = "selector", dimension = "intersight.asset.device_registration.moid", value = "111111"}]}
aggregations = [{type = "longSum", name = "count", fieldName = "hw.temperature_count"}, {type = "doubleSum", name = "hw.temperature-Sum", fieldName = "hw.temperature"}]
post_aggregations = [{"type" = "expression", "name" = "intersight.hw.temp.p1.avg", "expression" = "(\"hw.temperature-Sum\" / \"count\")"}]
field_names = ["intersight.hw.temp.p1.avg"]
otel_dimension_to_attribute_map = { }
otel_attributes = { server-name = "my-server", server-ip = "10.10.10.11" }
interval = 60

[[tspollers]]
name = "hw_temp_p1_min"
datasource = "PhysicalEntities"
dimensions = []
filter = { type = "and", fields = [{type = "selector", dimension = "instrument.name", value = "hw.temperature"}, {type = "selector", dimension = "hw.temperature.sensor.name", value = "P1_TEMP_SENS"}, {type = "selector", dimension = "name", value = "P1_TEMP_SENS"}, {type = "selector", dimension = "intersight.asset.device_registration.moid", value = "111111"}]}
aggregations = [{type = "doubleMin", name = "intersight.hw.temp.p1.min", fieldName = "hw.temperature_min"}]
post_aggregations = []
field_names = ["intersight.hw.temp.p1.min"]
otel_dimension_to_attribute_map = { }
otel_attributes = { server-name = "my-server", server-ip = "10.10.10.11" }
interval = 60

[[tspollers]]
name = "hw_temp_p1_max"
datasource = "PhysicalEntities"
dimensions = []
filter = { type = "and", fields = [{type = "selector", dimension = "instrument.name", value = "hw.temperature"}, {type = "selector", dimension = "hw.temperature.sensor.name", value = "P1_TEMP_SENS"}, {type = "selector", dimension = "name", value = "P1_TEMP_SENS"}, {type = "selector", dimension = "intersight.asset.device_registration.moid", value = "111111"}]}
aggregations = [{type = "doubleMax", name = "intersight.hw.temp.p1.max", fieldName = "hw.temperature_max"}]
post_aggregations = []
field_names = ["intersight.hw.temp.p1.max"]
otel_dimension_to_attribute_map = { }
otel_attributes = { server-name = "my-server", server-ip = "10.10.10.11" }
interval = 60

[[tspollers]]
name = "hw_temp_p2_avg"
datasource = "PhysicalEntities"
dimensions = []
filter = { type = "and", fields = [{type = "selector", dimension = "instrument.name", value = "hw.temperature"}, {type = "selector", dimension = "hw.temperature.sensor.name", value = "P2_TEMP_SENS"}, {type = "selector", dimension = "name", value = "P2_TEMP_SENS"}, {type = "selector", dimension = "intersight.asset.device_registration.moid", value = "111111"}]}
aggregations = [{type = "longSum", name = "count", fieldName = "hw.temperature_count"}, {type = "doubleSum", name = "hw.temperature-Sum", fieldName = "hw.temperature"}]
post_aggregations = [{"type" = "expression", "name" = "intersight.hw.temp.p2.avg", "expression" = "(\"hw.temperature-Sum\" / \"count\")"}]
field_names = ["intersight.hw.temp.p2.avg"]
otel_dimension_to_attribute_map = { }
otel_attributes = { server-name = "my-server", server-ip = "10.10.10.11" }
interval = 60

[[tspollers]]
name = "hw_temp_p2_min"
datasource = "PhysicalEntities"
dimensions = []
filter = { type = "and", fields = [{type = "selector", dimension = "instrument.name", value = "hw.temperature"}, {type = "selector", dimension = "hw.temperature.sensor.name", value = "P2_TEMP_SENS"}, {type = "selector", dimension = "name", value = "P2_TEMP_SENS"}, {type = "selector", dimension = "intersight.asset.device_registration.moid", value = "111111"}]}
aggregations = [{type = "doubleMin", name = "intersight.hw.temp.p2.min", fieldName = "hw.temperature_min"}]
post_aggregations = []
field_names = ["intersight.hw.temp.p2.min"]
otel_dimension_to_attribute_map = { }
otel_attributes = { server-name = "my-server", server-ip = "10.10.10.11" }
interval = 60

[[tspollers]]
name = "hw_temp_p2_max"
datasource = "PhysicalEntities"
dimensions = []
filter = { type = "and", fields = [{type = "selector", dimension = "instrument.name", value = "hw.temperature"}, {type = "selector", dimension = "hw.temperature.sensor.name", value = "P2_TEMP_SENS"}, {type = "selector", dimension = "name", value = "P2_TEMP_SENS"}, {type = "selector", dimension = "intersight.asset.device_registration.moid", value = "111111"}]}
aggregations = [{type = "doubleMax", name = "intersight.hw.temp.p2.max", fieldName = "hw.temperature_max"}]
post_aggregations = []
field_names = ["intersight.hw.temp.p2.max"]
otel_dimension_to_attribute_map = { }
otel_attributes = { server-name = "my-server", server-ip = "10.10.10.11" }
interval = 60

[[tspollers]]
name = "hw_temp_top_avg"
datasource = "PhysicalEntities"
dimensions = []
filter = { type = "and", fields = [{type = "selector", dimension = "instrument.name", value = "hw.temperature"}, {type = "selector", dimension = "hw.temperature.sensor.name", value = "TEMP_REAR_TOP"}, {type = "selector", dimension = "name", value = "TEMP_REAR_TOP"}, {type = "selector", dimension = "intersight.asset.device_registration.moid", value = "111111"}]}
aggregations = [{type = "longSum", name = "count", fieldName = "hw.temperature_count"}, {type = "doubleSum", name = "hw.temperature-Sum", fieldName = "hw.temperature"}]
post_aggregations = [{"type" = "expression", "name" = "intersight.hw.temp.top.avg", "expression" = "(\"hw.temperature-Sum\" / \"count\")"}]
field_names = ["intersight.hw.temp.top.avg"]
otel_dimension_to_attribute_map = { }
otel_attributes = { server-name = "my-server", server-ip = "10.10.10.11" }
interval = 60

[[tspollers]]
name = "hw_temp_top_min"
datasource = "PhysicalEntities"
dimensions = []
filter = { type = "and", fields = [{type = "selector", dimension = "instrument.name", value = "hw.temperature"}, {type = "selector", dimension = "hw.temperature.sensor.name", value = "TEMP_REAR_TOP"}, {type = "selector", dimension = "name", value = "TEMP_REAR_TOP"}, {type = "selector", dimension = "intersight.asset.device_registration.moid", value = "111111"}]}
aggregations = [{type = "doubleMin", name = "intersight.hw.temp.top.min", fieldName = "hw.temperature_min"}]
post_aggregations = []
field_names = ["intersight.hw.temp.top.min"]
otel_dimension_to_attribute_map = { }
otel_attributes = { server-name = "my-server", server-ip = "10.10.10.11" }
interval = 60

[[tspollers]]
name = "hw_temp_top_max"
datasource = "PhysicalEntities"
dimensions = []
filter = { type = "and", fields = [{type = "selector", dimension = "instrument.name", value = "hw.temperature"}, {type = "selector", dimension = "hw.temperature.sensor.name", value = "TEMP_REAR_TOP"}, {type = "selector", dimension = "name", value = "TEMP_REAR_TOP"}, {type = "selector", dimension = "intersight.asset.device_registration.moid", value = "111111"}]}
aggregations = [{type = "doubleMax", name = "intersight.hw.temp.top.max", fieldName = "hw.temperature_max"}]
post_aggregations = []
field_names = ["intersight.hw.temp.top.max"]
otel_dimension_to_attribute_map = { }
otel_attributes = { server-name = "my-server", server-ip = "10.10.10.11" }
interval = 60


New pollers
-----------

otel_collector_endpoint = "http://127.0.0.1:4317"

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
  intersight-otel.toml: otel_collector_endpoint = "http://127.0.0.1:4317"
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

OpenShift Workflow - Intersight Open Telemetry (iotel) - Delete Instance
========================================================================

OpenShift Cluster: bm1

Collect resources in namespace intersight-otel
- deployment
- pod
- secret
- config map
- service
- service monitor

Delete Deployment
-----------------
- namespace: intersight-otel
- name: instance-iac
- replica set: instance-iac-6f6fdbc5f6
- pod: instance-iac-6f6fdbc5f6-566sg
- wait for no deployment
- wait for no pod: instance-iac-6f6fdbc5f6-566sg

Delete Secret
-------------
- namespace: intersight-otel
- name: intersight-iac
- wait for no secret

Delete Config Map
-----------------
- namespace: intersight-otel
- name: intersight-iac
- wait for no config map

Delete Service
--------------
- namespace: intersight-otel
- name: otel-iac
- wait for no service

Delete Service Monitor
----------------------
- namespace: intersight-otel
- name: otel-iac
- wait for no service monitor

Namespace [intersight-otel] resources
- no pods
- no deployments
- no daemon sets
- no replica sets
- no services
- no pvcs

Delete Namespace
----------------
- name: intersight-otel
- wait for no namespace

Completed tasks
- Service monitor deleted
- Service deleted
- Deployment deleted
- ConfigMap for intersight poller deleted
- ConfigMap for otel-collector deleted
- Secret with intersight authentication deleted
- Namespace deleted (if empty)

OpenShift Workflow - Prometheus - Disable user-workload monitoring
==================================================================

OpenShift Cluster: bm1

Config Map
----------
- namespace: openshift-monitoring
- name: cluster-monitoring-config
- found and will be checked
- enableUserWorkload value will be changed in config map

Change Config Map
-----------------
- namespace: openshift-monitoring
- name: cluster-monitoring-config

~~~
apiVersion: v1
data:
  config.yaml: |-
    enableUserWorkload: false
kind: ConfigMap
metadata:
  name: name
  namespace: namespace

~~~
Config map updated

Check for resources gone
------------------------
Wait for deployments deleted (optional: False)...
- openshift-user-workload-monitoring/prometheus-operator
Wait for stateful sets deleted (optional: False)...
- openshift-user-workload-monitoring/prometheus-user-workload
- openshift-user-workload-monitoring/thanos-ruler-user-workload


Completed tasks
---------------
- User workload monitoring disabled
```

[[Back]](./get_things_done.md)