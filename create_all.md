# From Zero to Hero (Output)

[[Back]](./get_things_done.md)

```
# iserver set ocp task --cluster bm1 --filename C:\tmp\task.json --no-confirm

Cluster: bm1 (type: ocp)

OpenShift Workflow - Create Tasks
=================================

Validate Input
--------------
Completed


OpenShift Workflow - Prometheus - Enable user-workload monitoring
=================================================================

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
    enableUserWorkload: true
kind: ConfigMap
metadata:
  name: name
  namespace: namespace

~~~
Config map updated

Check for resources
-------------------
Wait for deployments ready (optional: False, allow zero replicas: False)...
- openshift-user-workload-monitoring/prometheus-operator
Wait for stateful sets ready...
- openshift-user-workload-monitoring/prometheus-user-workload
- openshift-user-workload-monitoring/thanos-ruler-user-workload


Completed tasks
---------------
- User workload monitoring enabled

OpenShift Workflow - Intersight Open Telemetry (iotel) - Create Instance
========================================================================

OpenShift Cluster: bm1

Check User Workload Monitoring
------------------------------
- config map namespace: openshift-monitoring
- config map name: cluster-monitoring-config
- enableUserWorkload enabled

Resources
---------
namespace: intersight-otel
intersight-otel secret: intersight-otel/intersight-iac
intersight-otel config map: intersight-otel/intersight-iac
otel-collector config map: intersight-otel/otel-iac
deployment: intersight-otel/instance-iac
service: intersight-otel/otel-iac
service monitor: intersight-otel/otel-iac

Create Namespace
----------------
- name: intersight-otel

~~~
apiVersion: v1
kind: Namespace
metadata:
  name: intersight-otel

~~~

Namespace created

Wait for namespace [timeout:60]...

Create Secret
-------------
- namespace: intersight-otel
- name: intersight-iac

Secret created

Wait for secret [timeout:60]...

Create Config Map
-----------------
- namespace: intersight-otel
- name: otel-iac
- labels
	app:opentelemetry
	component:otel-collector-config
- destination: otel-collector-config

~~~
exporters:
  prometheus:
    enable_open_metrics: true
    endpoint: :2112
    metric_expiration: 180m
    resource_to_telemetry_conversion:
      enabled: true
    send_timestamps: true
extensions:
  zpages:
processors:
  batch:
  memory_limiter:
    check_interval: 5s
    limit_mib: 1500
    spike_limit_mib: 512
receivers:
  otlp:
    protocols:
      grpc:
      http:
service:
  extensions:
  - zpages
  pipelines:
    metrics:
      exporters:
      - prometheus
      processors:
      - memory_limiter
      - batch
      receivers:
      - otlp

~~~

Config map created

Wait for config map [timeout:60]...

Create Config Map
-----------------
- namespace: intersight-otel
- name: intersight-iac
- destination: intersight-otel.toml

~~~
otel_collector_endpoint = "http://127.0.0.1:4317"

~~~

Config map created

Wait for config map [timeout:60]...

Create Deployment
-----------------
- namespace: intersight-otel
- name: instance-iac

~~~
apiVersion: apps/v1
kind: Deployment
metadata:
  name: instance-iac
  namespace: intersight-otel
spec:
  selector:
    matchLabels:
      app: instance-iac
  template:
    metadata:
      labels:
        app: instance-iac
        component: otel-collector
    spec:
      containers:
      - command:
        - /target/release/intersight_otel
        - -c
        - /etc/intersight-otel/intersight-otel.toml
        env:
        - name: HTTPS_PROXY
          value: http://proxy.domain.com:80
        - name: RUST_LOG
          value: info
        - name: intersight_otel_key_file
          value: /etc/intersight-otel-key/intersight.pem
        - name: intersight_otel_key_id
          valueFrom:
            secretKeyRef:
              key: intersight-key-id
              name: intersight-iac
        image: ghcr.io/cgascoig/intersight-otel:v0.1.2
        name: intersight-otel
        resources:
          limits:
            cpu: 200m
            memory: 128Mi
          requests:
            cpu: 100m
            memory: 64Mi
        securityContext:
          securityContext:
            allowPrivilegeEscalation: false
            capabilities:
              drop:
              - all
            privileged: false
            readOnlyRootFilesystem: true
        volumeMounts:
        - mountPath: /etc/intersight-otel
          name: intersight-otel-config
          readyOnly: true
        - mountPath: /etc/intersight-otel-key
          name: intersight-otel-key
          readyOnly: true
      - command:
        - /otelcol
        - --config=/conf/otel-collector-config.yaml
        image: otel/opentelemetry-collector:0.59.0
        name: otel-collector
        ports:
        - containerPort: 4317
        - containerPort: 2112
        resources:
          limits:
            cpu: '1'
            memory: 2Gi
          requests:
            cpu: 200m
            memory: 400Mi
        volumeMounts:
        - mountPath: /conf
          name: otel-collector-config-vol
      volumes:
      - configMap:
          name: intersight-iac
        name: intersight-otel-config
      - name: intersight-otel-key
        secret:
          items:
          - key: intersight-key
            path: intersight.pem
          secretName: intersight-iac
      - configMap:
          items:
          - key: otel-collector-config
            path: otel-collector-config.yaml
          name: otel-iac
        name: otel-collector-config-vol

~~~
Wait until deployment found [timeout:60s]...
Wait until deployment resources [timeout:60s]...

Create Service
--------------
- namespace: intersight-otel
- name: otel-iac

~~~
apiVersion: v1
kind: Service
metadata:
  labels:
    app: instance-iac
    component: otel-collector
  name: otel-iac
  namespace: intersight-otel
spec:
  ports:
  - name: prometheus-exporter
    port: 2112
  selector:
    app: instance-iac
    component: otel-collector

~~~
Wait until service found [timeout:60s]...

Create Service Monitor
----------------------
- namespace: intersight-otel
- name: otel-iac

~~~
apiVersion: monitoring.coreos.com/v1
kind: ServiceMonitor
metadata:
  name: otel-iac
  namespace: intersight-otel
spec:
  endpoints:
  - interval: 30s
    path: /metrics
    port: prometheus-exporter
    scheme: http
  selector:
    matchLabels:
      app: instance-iac
      component: otel-collector

~~~
Wait until service monitor found [timeout:60s]...
Wait until service monitor target ready [timeout:360s]...

Completed tasks
- Namespace created
- Secret with intersight authentication ready
- ConfigMap for otel-collector ready
- ConfigMap for intersight poller ready
- Deployment ready
- Service created
- Service monitor ready with prometheus target

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
- mode: replace
- template: hw.*
- target: server-name:my-server
- empty user-provided poller

Select servers...
Collect server api objects [6]...
Selected servers: 6

Resolving intersight ids
------------------------
Server name: my-server

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

OpenShift Workflow - Grafana Operator - Create Operator
=======================================================

OpenShift Cluster: bm1

Create Namespace
----------------
- name: grafana-operator

~~~
apiVersion: v1
kind: Namespace
metadata:
  name: grafana-operator

~~~

Namespace created

Wait for namespace [timeout:60]...

Create Operator Group
---------------------
Operator group: grafana-operator/grafana-operator-group

~~~
apiVersion: operators.coreos.com/v1
kind: OperatorGroup
metadata:
  name: grafana-operator-group
  namespace: grafana-operator
spec:
  targetNamespaces:
  - grafana-operator
  upgradeStrategy: Default

~~~

Operator group created

Wait for operator group [timeout:60]...

Create Subscription
-------------------
Subscription: grafana-operator/grafana-operator
Source: openshift-marketplace/community-operators/grafana-operator
Install plan approval: Automatic
Getting subscription and packege manifest information...
Resolving channel name...
Channel: v5
- CSV [grafana-operator.v5.20.0]
- CSV Display name [Grafana Operator]
- CVS Version [5.20.0]
- CSV Provider [{'name': 'Grafana Labs', 'url': 'https://grafana.com'}]
- CSV Maturity [stable]

~~~
apiVersion: operators.coreos.com/v1alpha1
kind: Subscription
metadata:
  name: grafana-operator
  namespace: grafana-operator
spec:
  channel: v5
  installPlanApproval: Automatic
  name: grafana-operator
  source: community-operators
  sourceNamespace: openshift-marketplace

~~~

Subscription created

Wait for subscription install plan started [timeout:360]...
Install plan: install-kpk6s
Wait for subscription install plan ready [timeout:600]...
Install plan succeeded
Wait for deployments ready (optional: True, allow zero replicas: False)...
- grafana-operator/grafana-operator-controller-manager-v5


Completed tasks
---------------
- Namespace created
- Operator Group created
- Grafana Operator installed

OpenShift Workflow - Grafana Operator - Create Instance
=======================================================

OpenShift Cluster: bm1

Create Grafana Instance
-----------------------

~~~
apiVersion: grafana.integreatly.org/v1beta1
kind: Grafana
metadata:
  labels:
    dashboards: test
    folders: test
  name: test
  namespace: grafana-operator
spec:
  config:
    auth:
      disable_login_form: 'false'
    log:
      mode: console
    security:
      admin_password: pass
      admin_user: user
  route:
    spec: {}

~~~
Wait until grafana found [timeout:60s]...
Wait until grafana resources [timeout:60s]...
Wait for deployments ready (optional: False, allow zero replicas: False)...
- grafana-operator/test-deployment
Wait for service account...
Grafana instance route [test-route-grafana-operator.apps.bm1.domain.com]

Check User Workload Monitoring
------------------------------
- config map namespace: openshift-monitoring
- config map name: cluster-monitoring-config
- enableUserWorkload enabled

Cluster Role Binding
--------------------
Service Account [test-sa] is not yet associated with role [cluster-monitoring-view]
Cluster role binding created: test-sa-view

Create Grafana Datasource
-------------------------
- instance: test
- get service account token for grafana instance

~~~
apiVersion: grafana.integreatly.org/v1beta1
kind: GrafanaDatasource
metadata:
  name: test-thanos
  namespace: grafana-operator
spec:
  datasource:
    access: proxy
    editable: true
    isDefault: true
    jsonData:
      httpHeaderName1: Authorization
      timeInterval: 5s
      tlsSkipVerify: true
    name: k8s
    secureJsonData:
      httpHeaderValue1: Bearer ...
    type: prometheus
    url: https://thanos-querier.openshift-monitoring.svc.cluster.local:9091
  instanceSelector:
    matchLabels:
      dashboards: test

~~~
Wait until grafana found...
Wait until grafana datasource grafana-operator/test-thanos in instance test...

Grafana datasource created
- grafana-operator/test-thanos
- type: prometheus
- name: k8s
- token: service account [grafana-operator/test-sa]
- dashboards: test


Completed tasks
---------------
- Grafana instance defined
- Prometheus data source created

OpenShift Workflow - Grafana Operator - Create Dashboard
========================================================

OpenShift Cluster: bm1
Grafana instance: grafana-operator/test

Collect resources
- grafana
- dashboard
- folder
- datasource

Grafana dashboard template source files
- C:\tmp\imonitor\dashboard\server\intersight.hw.all
- C:\tmp\imonitor\dashboard\server\intersight.hw.cpu
- C:\tmp\imonitor\dashboard\server\intersight.hw.power
- C:\tmp\imonitor\dashboard\server\intersight.hw.temp.bottom
- C:\tmp\imonitor\dashboard\server\intersight.hw.temp.front
- C:\tmp\imonitor\dashboard\server\intersight.hw.temp.middle
- C:\tmp\imonitor\dashboard\server\intersight.hw.temp.p1
- C:\tmp\imonitor\dashboard\server\intersight.hw.temp.p2
- C:\tmp\imonitor\dashboard\server\intersight.hw.temp.top

Grafana dashboard template scope
- server-name:my-server

Create Grafana Dashboard
------------------------

~~~
apiVersion: grafana.integreatly.org/v1beta1
kind: GrafanaDashboard
metadata:
  name: hw-my-server
  namespace: grafana-operator
spec:
  folder: my-server
  instanceSelector:
    matchLabels:
      dashboards: test
  json: |-
    {
        "title": "Hardware Collection",
        "uid": "hw-my-server",
        "panels": [
            {
                "title": "CPU",
                "type": "gauge",
                "gridPos": {
                    "x": 0,
                    "y": 0,
                    "h": 4,
                    "w": 3
                },
                "fieldConfig": {
                    "defaults": {
                        "color": {
                            "mode": "thresholds"
                        },
                        "mappings": [],
                        "thresholds": {
                            "mode": "absolute",
                            "steps": [
                                {
                                    "color": "green",
                                    "value": 0
                                },
                                {
                                    "color": "red",
                                    "value": 80
                                }
                            ]
                        },
                        "unit": "percentunit"
                    },
                    "overrides": []
                },
                "targets": [
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_cpu_max{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "includeNullMetadata": true,
                        "legendFormat": "__auto",
                        "range": true,
                        "refId": "A",
                        "useBackend": false
                    }
                ],
                "datasource": {
                    "uid": "8327c396-080c-471e-b403-69d100cb8aa9",
                    "type": "prometheus"
                }
            },
            {
                "title": "Power [W]",
                "type": "stat",
                "gridPos": {
                    "x": 3,
                    "y": 0,
                    "h": 4,
                    "w": 3
                },
                "fieldConfig": {
                    "defaults": {
                        "color": {
                            "mode": "thresholds"
                        },
                        "mappings": [],
                        "thresholds": {
                            "mode": "absolute",
                            "steps": [
                                {
                                    "color": "green",
                                    "value": 0
                                },
                                {
                                    "color": "red",
                                    "value": 1000
                                }
                            ]
                        },
                        "unit": "none"
                    },
                    "overrides": []
                },
                "targets": [
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_power_avg{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "includeNullMetadata": true,
                        "legendFormat": "__auto",
                        "range": true,
                        "refId": "A",
                        "useBackend": false
                    }
                ],
                "datasource": {
                    "uid": "8327c396-080c-471e-b403-69d100cb8aa9",
                    "type": "prometheus"
                }
            },
            {
                "title": "Temp P1 [C]",
                "type": "stat",
                "gridPos": {
                    "x": 6,
                    "y": 0,
                    "h": 4,
                    "w": 3
                },
                "fieldConfig": {
                    "defaults": {
                        "color": {
                            "mode": "thresholds"
                        },
                        "mappings": [],
                        "thresholds": {
                            "mode": "absolute",
                            "steps": [
                                {
                                    "color": "green",
                                    "value": 0
                                },
                                {
                                    "color": "red",
                                    "value": 100
                                }
                            ]
                        },
                        "unit": "none"
                    },
                    "overrides": []
                },
                "targets": [
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_p1_avg{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "includeNullMetadata": true,
                        "legendFormat": "__auto",
                        "range": true,
                        "refId": "A",
                        "useBackend": false
                    }
                ],
                "datasource": {
                    "uid": "8327c396-080c-471e-b403-69d100cb8aa9",
                    "type": "prometheus"
                }
            },
            {
                "title": "Temp P2 [C]",
                "type": "stat",
                "gridPos": {
                    "x": 9,
                    "y": 0,
                    "h": 4,
                    "w": 3
                },
                "fieldConfig": {
                    "defaults": {
                        "color": {
                            "mode": "thresholds"
                        },
                        "mappings": [],
                        "thresholds": {
                            "mode": "absolute",
                            "steps": [
                                {
                                    "color": "green",
                                    "value": 0
                                },
                                {
                                    "color": "red",
                                    "value": 100
                                }
                            ]
                        },
                        "unit": "none"
                    },
                    "overrides": []
                },
                "targets": [
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_p2_avg{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "includeNullMetadata": true,
                        "legendFormat": "__auto",
                        "range": true,
                        "refId": "A",
                        "useBackend": false
                    }
                ],
                "datasource": {
                    "uid": "8327c396-080c-471e-b403-69d100cb8aa9",
                    "type": "prometheus"
                }
            },
            {
                "title": "Temp Front [C]",
                "type": "stat",
                "gridPos": {
                    "x": 12,
                    "y": 0,
                    "h": 4,
                    "w": 3
                },
                "fieldConfig": {
                    "defaults": {
                        "color": {
                            "mode": "thresholds"
                        },
                        "mappings": [],
                        "thresholds": {
                            "mode": "absolute",
                            "steps": [
                                {
                                    "color": "green",
                                    "value": 0
                                },
                                {
                                    "color": "red",
                                    "value": 100
                                }
                            ]
                        },
                        "unit": "none"
                    },
                    "overrides": []
                },
                "targets": [
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_front_avg{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "includeNullMetadata": true,
                        "legendFormat": "__auto",
                        "range": true,
                        "refId": "A",
                        "useBackend": false
                    }
                ],
                "datasource": {
                    "uid": "8327c396-080c-471e-b403-69d100cb8aa9",
                    "type": "prometheus"
                }
            },
            {
                "title": "Temp Top [C]",
                "type": "stat",
                "gridPos": {
                    "x": 15,
                    "y": 0,
                    "h": 4,
                    "w": 3
                },
                "fieldConfig": {
                    "defaults": {
                        "color": {
                            "mode": "thresholds"
                        },
                        "mappings": [],
                        "thresholds": {
                            "mode": "absolute",
                            "steps": [
                                {
                                    "color": "green",
                                    "value": 0
                                },
                                {
                                    "color": "red",
                                    "value": 100
                                }
                            ]
                        },
                        "unit": "none"
                    },
                    "overrides": []
                },
                "targets": [
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_top_avg{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "includeNullMetadata": true,
                        "legendFormat": "__auto",
                        "range": true,
                        "refId": "A",
                        "useBackend": false
                    }
                ],
                "datasource": {
                    "uid": "8327c396-080c-471e-b403-69d100cb8aa9",
                    "type": "prometheus"
                }
            },
            {
                "title": "Temp Middle [C]",
                "type": "stat",
                "gridPos": {
                    "x": 18,
                    "y": 0,
                    "h": 4,
                    "w": 3
                },
                "fieldConfig": {
                    "defaults": {
                        "color": {
                            "mode": "thresholds"
                        },
                        "mappings": [],
                        "thresholds": {
                            "mode": "absolute",
                            "steps": [
                                {
                                    "color": "green",
                                    "value": 0
                                },
                                {
                                    "color": "red",
                                    "value": 100
                                }
                            ]
                        },
                        "unit": "none"
                    },
                    "overrides": []
                },
                "targets": [
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_middle_avg{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "includeNullMetadata": true,
                        "legendFormat": "__auto",
                        "range": true,
                        "refId": "A",
                        "useBackend": false
                    }
                ],
                "datasource": {
                    "uid": "8327c396-080c-471e-b403-69d100cb8aa9",
                    "type": "prometheus"
                }
            },
            {
                "title": "Temp Bottom [C]",
                "type": "stat",
                "gridPos": {
                    "x": 21,
                    "y": 0,
                    "h": 4,
                    "w": 3
                },
                "fieldConfig": {
                    "defaults": {
                        "color": {
                            "mode": "thresholds"
                        },
                        "mappings": [],
                        "thresholds": {
                            "mode": "absolute",
                            "steps": [
                                {
                                    "color": "green",
                                    "value": 0
                                },
                                {
                                    "color": "red",
                                    "value": 100
                                }
                            ]
                        },
                        "unit": "none"
                    },
                    "overrides": []
                },
                "targets": [
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_bottom_avg{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "includeNullMetadata": true,
                        "legendFormat": "__auto",
                        "range": true,
                        "refId": "A",
                        "useBackend": false
                    }
                ],
                "datasource": {
                    "uid": "8327c396-080c-471e-b403-69d100cb8aa9",
                    "type": "prometheus"
                }
            },
            {
                "title": "CPU Load",
                "type": "timeseries",
                "datasource": {
                    "type": "prometheus",
                    "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                },
                "fieldConfig": {
                    "defaults": {
                        "color": {
                            "mode": "palette-classic"
                        },
                        "custom": {
                            "axisBorderShow": false,
                            "axisCenteredZero": false,
                            "axisColorMode": "text",
                            "axisLabel": "",
                            "axisPlacement": "auto",
                            "barAlignment": 0,
                            "barWidthFactor": 0.6,
                            "drawStyle": "line",
                            "fillOpacity": 0,
                            "gradientMode": "none",
                            "hideFrom": {
                                "legend": false,
                                "tooltip": false,
                                "viz": false
                            },
                            "insertNulls": false,
                            "lineInterpolation": "linear",
                            "lineWidth": 1,
                            "pointSize": 5,
                            "scaleDistribution": {
                                "type": "linear"
                            },
                            "showPoints": "auto",
                            "spanNulls": false,
                            "stacking": {
                                "group": "A",
                                "mode": "none"
                            },
                            "thresholdsStyle": {
                                "mode": "off"
                            }
                        },
                        "mappings": [],
                        "thresholds": {
                            "mode": "absolute",
                            "steps": [
                                {
                                    "color": "green",
                                    "value": 0
                                },
                                {
                                    "color": "red",
                                    "value": 80
                                }
                            ]
                        },
                        "unit": "percentunit"
                    },
                    "overrides": []
                },
                "gridPos": {
                    "h": 8,
                    "w": 12,
                    "x": 0,
                    "y": 4
                },
                "options": {
                    "legend": {
                        "calcs": [],
                        "displayMode": "list",
                        "placement": "bottom",
                        "showLegend": true
                    },
                    "tooltip": {
                        "hideZeros": false,
                        "mode": "single",
                        "sort": "none"
                    }
                },
                "targets": [
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_cpu_min{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "hide": false,
                        "includeNullMetadata": true,
                        "instant": false,
                        "legendFormat": "Min",
                        "range": true,
                        "refId": "A",
                        "useBackend": false
                    },
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_cpu_avg{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "includeNullMetadata": true,
                        "legendFormat": "Avg",
                        "range": true,
                        "refId": "B",
                        "useBackend": false
                    },
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_cpu_max{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "hide": false,
                        "includeNullMetadata": true,
                        "instant": false,
                        "legendFormat": "Max",
                        "range": true,
                        "refId": "C",
                        "useBackend": false
                    }
                ]
            },
            {
                "title": "Host Power [W]",
                "type": "timeseries",
                "datasource": {
                    "type": "prometheus",
                    "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                },
                "fieldConfig": {
                    "defaults": {
                        "color": {
                            "mode": "palette-classic"
                        },
                        "custom": {
                            "axisBorderShow": false,
                            "axisCenteredZero": false,
                            "axisColorMode": "text",
                            "axisLabel": "",
                            "axisPlacement": "auto",
                            "barAlignment": 0,
                            "barWidthFactor": 0.6,
                            "drawStyle": "line",
                            "fillOpacity": 0,
                            "gradientMode": "none",
                            "hideFrom": {
                                "legend": false,
                                "tooltip": false,
                                "viz": false
                            },
                            "insertNulls": false,
                            "lineInterpolation": "linear",
                            "lineWidth": 1,
                            "pointSize": 5,
                            "scaleDistribution": {
                                "type": "linear"
                            },
                            "showPoints": "auto",
                            "spanNulls": false,
                            "stacking": {
                                "group": "A",
                                "mode": "none"
                            },
                            "thresholdsStyle": {
                                "mode": "off"
                            }
                        },
                        "mappings": [],
                        "thresholds": {
                            "mode": "absolute",
                            "steps": [
                                {
                                    "color": "green",
                                    "value": 0
                                },
                                {
                                    "color": "red",
                                    "value": 80
                                }
                            ]
                        },
                        "unit": "none"
                    },
                    "overrides": []
                },
                "gridPos": {
                    "h": 8,
                    "w": 12,
                    "x": 12,
                    "y": 4
                },
                "options": {
                    "legend": {
                        "calcs": [],
                        "displayMode": "list",
                        "placement": "bottom",
                        "showLegend": true
                    },
                    "tooltip": {
                        "hideZeros": false,
                        "mode": "single",
                        "sort": "none"
                    }
                },
                "targets": [
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_power_min{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "hide": false,
                        "includeNullMetadata": true,
                        "instant": false,
                        "legendFormat": "Min",
                        "range": true,
                        "refId": "A",
                        "useBackend": false
                    },
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_power_avg{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "includeNullMetadata": true,
                        "legendFormat": "Avg",
                        "range": true,
                        "refId": "B",
                        "useBackend": false
                    },
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_power_max{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "hide": false,
                        "includeNullMetadata": true,
                        "instant": false,
                        "legendFormat": "Max",
                        "range": true,
                        "refId": "C",
                        "useBackend": false
                    }
                ]
            },
            {
                "title": "Temp P1 [C]",
                "type": "timeseries",
                "datasource": {
                    "type": "prometheus",
                    "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                },
                "fieldConfig": {
                    "defaults": {
                        "color": {
                            "mode": "palette-classic"
                        },
                        "custom": {
                            "axisBorderShow": false,
                            "axisCenteredZero": false,
                            "axisColorMode": "text",
                            "axisLabel": "",
                            "axisPlacement": "auto",
                            "barAlignment": 0,
                            "barWidthFactor": 0.6,
                            "drawStyle": "line",
                            "fillOpacity": 0,
                            "gradientMode": "none",
                            "hideFrom": {
                                "legend": false,
                                "tooltip": false,
                                "viz": false
                            },
                            "insertNulls": false,
                            "lineInterpolation": "linear",
                            "lineWidth": 1,
                            "pointSize": 5,
                            "scaleDistribution": {
                                "type": "linear"
                            },
                            "showPoints": "auto",
                            "spanNulls": false,
                            "stacking": {
                                "group": "A",
                                "mode": "none"
                            },
                            "thresholdsStyle": {
                                "mode": "off"
                            }
                        },
                        "mappings": [],
                        "thresholds": {
                            "mode": "absolute",
                            "steps": [
                                {
                                    "color": "green",
                                    "value": 0
                                },
                                {
                                    "color": "red",
                                    "value": 80
                                }
                            ]
                        },
                        "unit": "none"
                    },
                    "overrides": []
                },
                "gridPos": {
                    "h": 8,
                    "w": 12,
                    "x": 0,
                    "y": 12
                },
                "options": {
                    "legend": {
                        "calcs": [],
                        "displayMode": "list",
                        "placement": "bottom",
                        "showLegend": true
                    },
                    "tooltip": {
                        "hideZeros": false,
                        "mode": "single",
                        "sort": "none"
                    }
                },
                "targets": [
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_p1_min{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "hide": false,
                        "includeNullMetadata": true,
                        "instant": false,
                        "legendFormat": "Min",
                        "range": true,
                        "refId": "A",
                        "useBackend": false
                    },
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_p1_avg{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "includeNullMetadata": true,
                        "legendFormat": "Avg",
                        "range": true,
                        "refId": "B",
                        "useBackend": false
                    },
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_p1_max{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "hide": false,
                        "includeNullMetadata": true,
                        "instant": false,
                        "legendFormat": "Max",
                        "range": true,
                        "refId": "C",
                        "useBackend": false
                    }
                ]
            },
            {
                "title": "Temp P2 [C]",
                "type": "timeseries",
                "datasource": {
                    "type": "prometheus",
                    "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                },
                "fieldConfig": {
                    "defaults": {
                        "color": {
                            "mode": "palette-classic"
                        },
                        "custom": {
                            "axisBorderShow": false,
                            "axisCenteredZero": false,
                            "axisColorMode": "text",
                            "axisLabel": "",
                            "axisPlacement": "auto",
                            "barAlignment": 0,
                            "barWidthFactor": 0.6,
                            "drawStyle": "line",
                            "fillOpacity": 0,
                            "gradientMode": "none",
                            "hideFrom": {
                                "legend": false,
                                "tooltip": false,
                                "viz": false
                            },
                            "insertNulls": false,
                            "lineInterpolation": "linear",
                            "lineWidth": 1,
                            "pointSize": 5,
                            "scaleDistribution": {
                                "type": "linear"
                            },
                            "showPoints": "auto",
                            "spanNulls": false,
                            "stacking": {
                                "group": "A",
                                "mode": "none"
                            },
                            "thresholdsStyle": {
                                "mode": "off"
                            }
                        },
                        "mappings": [],
                        "thresholds": {
                            "mode": "absolute",
                            "steps": [
                                {
                                    "color": "green",
                                    "value": 0
                                },
                                {
                                    "color": "red",
                                    "value": 80
                                }
                            ]
                        },
                        "unit": "none"
                    },
                    "overrides": []
                },
                "gridPos": {
                    "h": 8,
                    "w": 12,
                    "x": 12,
                    "y": 12
                },
                "options": {
                    "legend": {
                        "calcs": [],
                        "displayMode": "list",
                        "placement": "bottom",
                        "showLegend": true
                    },
                    "tooltip": {
                        "hideZeros": false,
                        "mode": "single",
                        "sort": "none"
                    }
                },
                "targets": [
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_p2_min{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "hide": false,
                        "includeNullMetadata": true,
                        "instant": false,
                        "legendFormat": "Min",
                        "range": true,
                        "refId": "A",
                        "useBackend": false
                    },
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_p2_avg{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "includeNullMetadata": true,
                        "legendFormat": "Avg",
                        "range": true,
                        "refId": "B",
                        "useBackend": false
                    },
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_p2_max{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "hide": false,
                        "includeNullMetadata": true,
                        "instant": false,
                        "legendFormat": "Max",
                        "range": true,
                        "refId": "C",
                        "useBackend": false
                    }
                ]
            },
            {
                "title": "Temp Front [C]",
                "type": "timeseries",
                "datasource": {
                    "type": "prometheus",
                    "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                },
                "fieldConfig": {
                    "defaults": {
                        "color": {
                            "mode": "palette-classic"
                        },
                        "custom": {
                            "axisBorderShow": false,
                            "axisCenteredZero": false,
                            "axisColorMode": "text",
                            "axisLabel": "",
                            "axisPlacement": "auto",
                            "barAlignment": 0,
                            "barWidthFactor": 0.6,
                            "drawStyle": "line",
                            "fillOpacity": 0,
                            "gradientMode": "none",
                            "hideFrom": {
                                "legend": false,
                                "tooltip": false,
                                "viz": false
                            },
                            "insertNulls": false,
                            "lineInterpolation": "linear",
                            "lineWidth": 1,
                            "pointSize": 5,
                            "scaleDistribution": {
                                "type": "linear"
                            },
                            "showPoints": "auto",
                            "spanNulls": false,
                            "stacking": {
                                "group": "A",
                                "mode": "none"
                            },
                            "thresholdsStyle": {
                                "mode": "off"
                            }
                        },
                        "mappings": [],
                        "thresholds": {
                            "mode": "absolute",
                            "steps": [
                                {
                                    "color": "green",
                                    "value": 0
                                },
                                {
                                    "color": "red",
                                    "value": 80
                                }
                            ]
                        },
                        "unit": "none"
                    },
                    "overrides": []
                },
                "gridPos": {
                    "h": 8,
                    "w": 12,
                    "x": 0,
                    "y": 20
                },
                "options": {
                    "legend": {
                        "calcs": [],
                        "displayMode": "list",
                        "placement": "bottom",
                        "showLegend": true
                    },
                    "tooltip": {
                        "hideZeros": false,
                        "mode": "single",
                        "sort": "none"
                    }
                },
                "targets": [
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_front_min{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "hide": false,
                        "includeNullMetadata": true,
                        "instant": false,
                        "legendFormat": "Min",
                        "range": true,
                        "refId": "A",
                        "useBackend": false
                    },
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_front_avg{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "includeNullMetadata": true,
                        "legendFormat": "Avg",
                        "range": true,
                        "refId": "B",
                        "useBackend": false
                    },
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_front_max{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "hide": false,
                        "includeNullMetadata": true,
                        "instant": false,
                        "legendFormat": "Max",
                        "range": true,
                        "refId": "C",
                        "useBackend": false
                    }
                ]
            },
            {
                "title": "Temp Top [C]",
                "type": "timeseries",
                "datasource": {
                    "type": "prometheus",
                    "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                },
                "fieldConfig": {
                    "defaults": {
                        "color": {
                            "mode": "palette-classic"
                        },
                        "custom": {
                            "axisBorderShow": false,
                            "axisCenteredZero": false,
                            "axisColorMode": "text",
                            "axisLabel": "",
                            "axisPlacement": "auto",
                            "barAlignment": 0,
                            "barWidthFactor": 0.6,
                            "drawStyle": "line",
                            "fillOpacity": 0,
                            "gradientMode": "none",
                            "hideFrom": {
                                "legend": false,
                                "tooltip": false,
                                "viz": false
                            },
                            "insertNulls": false,
                            "lineInterpolation": "linear",
                            "lineWidth": 1,
                            "pointSize": 5,
                            "scaleDistribution": {
                                "type": "linear"
                            },
                            "showPoints": "auto",
                            "spanNulls": false,
                            "stacking": {
                                "group": "A",
                                "mode": "none"
                            },
                            "thresholdsStyle": {
                                "mode": "off"
                            }
                        },
                        "mappings": [],
                        "thresholds": {
                            "mode": "absolute",
                            "steps": [
                                {
                                    "color": "green",
                                    "value": 0
                                },
                                {
                                    "color": "red",
                                    "value": 80
                                }
                            ]
                        },
                        "unit": "none"
                    },
                    "overrides": []
                },
                "gridPos": {
                    "h": 8,
                    "w": 12,
                    "x": 12,
                    "y": 20
                },
                "options": {
                    "legend": {
                        "calcs": [],
                        "displayMode": "list",
                        "placement": "bottom",
                        "showLegend": true
                    },
                    "tooltip": {
                        "hideZeros": false,
                        "mode": "single",
                        "sort": "none"
                    }
                },
                "targets": [
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_top_min{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "hide": false,
                        "includeNullMetadata": true,
                        "instant": false,
                        "legendFormat": "Min",
                        "range": true,
                        "refId": "A",
                        "useBackend": false
                    },
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_top_avg{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "includeNullMetadata": true,
                        "legendFormat": "Avg",
                        "range": true,
                        "refId": "B",
                        "useBackend": false
                    },
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_top_max{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "hide": false,
                        "includeNullMetadata": true,
                        "instant": false,
                        "legendFormat": "Max",
                        "range": true,
                        "refId": "C",
                        "useBackend": false
                    }
                ]
            },
            {
                "title": "Temp Middle [C]",
                "type": "timeseries",
                "datasource": {
                    "type": "prometheus",
                    "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                },
                "fieldConfig": {
                    "defaults": {
                        "color": {
                            "mode": "palette-classic"
                        },
                        "custom": {
                            "axisBorderShow": false,
                            "axisCenteredZero": false,
                            "axisColorMode": "text",
                            "axisLabel": "",
                            "axisPlacement": "auto",
                            "barAlignment": 0,
                            "barWidthFactor": 0.6,
                            "drawStyle": "line",
                            "fillOpacity": 0,
                            "gradientMode": "none",
                            "hideFrom": {
                                "legend": false,
                                "tooltip": false,
                                "viz": false
                            },
                            "insertNulls": false,
                            "lineInterpolation": "linear",
                            "lineWidth": 1,
                            "pointSize": 5,
                            "scaleDistribution": {
                                "type": "linear"
                            },
                            "showPoints": "auto",
                            "spanNulls": false,
                            "stacking": {
                                "group": "A",
                                "mode": "none"
                            },
                            "thresholdsStyle": {
                                "mode": "off"
                            }
                        },
                        "mappings": [],
                        "thresholds": {
                            "mode": "absolute",
                            "steps": [
                                {
                                    "color": "green",
                                    "value": 0
                                },
                                {
                                    "color": "red",
                                    "value": 80
                                }
                            ]
                        },
                        "unit": "none"
                    },
                    "overrides": []
                },
                "gridPos": {
                    "h": 8,
                    "w": 12,
                    "x": 0,
                    "y": 28
                },
                "options": {
                    "legend": {
                        "calcs": [],
                        "displayMode": "list",
                        "placement": "bottom",
                        "showLegend": true
                    },
                    "tooltip": {
                        "hideZeros": false,
                        "mode": "single",
                        "sort": "none"
                    }
                },
                "targets": [
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_middle_min{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "hide": false,
                        "includeNullMetadata": true,
                        "instant": false,
                        "legendFormat": "Min",
                        "range": true,
                        "refId": "A",
                        "useBackend": false
                    },
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_middle_avg{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "includeNullMetadata": true,
                        "legendFormat": "Avg",
                        "range": true,
                        "refId": "B",
                        "useBackend": false
                    },
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_middle_max{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "hide": false,
                        "includeNullMetadata": true,
                        "instant": false,
                        "legendFormat": "Max",
                        "range": true,
                        "refId": "C",
                        "useBackend": false
                    }
                ]
            },
            {
                "title": "Temp Bottom [C]",
                "type": "timeseries",
                "datasource": {
                    "type": "prometheus",
                    "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                },
                "fieldConfig": {
                    "defaults": {
                        "color": {
                            "mode": "palette-classic"
                        },
                        "custom": {
                            "axisBorderShow": false,
                            "axisCenteredZero": false,
                            "axisColorMode": "text",
                            "axisLabel": "",
                            "axisPlacement": "auto",
                            "barAlignment": 0,
                            "barWidthFactor": 0.6,
                            "drawStyle": "line",
                            "fillOpacity": 0,
                            "gradientMode": "none",
                            "hideFrom": {
                                "legend": false,
                                "tooltip": false,
                                "viz": false
                            },
                            "insertNulls": false,
                            "lineInterpolation": "linear",
                            "lineWidth": 1,
                            "pointSize": 5,
                            "scaleDistribution": {
                                "type": "linear"
                            },
                            "showPoints": "auto",
                            "spanNulls": false,
                            "stacking": {
                                "group": "A",
                                "mode": "none"
                            },
                            "thresholdsStyle": {
                                "mode": "off"
                            }
                        },
                        "mappings": [],
                        "thresholds": {
                            "mode": "absolute",
                            "steps": [
                                {
                                    "color": "green",
                                    "value": 0
                                },
                                {
                                    "color": "red",
                                    "value": 80
                                }
                            ]
                        },
                        "unit": "none"
                    },
                    "overrides": []
                },
                "gridPos": {
                    "h": 8,
                    "w": 12,
                    "x": 12,
                    "y": 28
                },
                "options": {
                    "legend": {
                        "calcs": [],
                        "displayMode": "list",
                        "placement": "bottom",
                        "showLegend": true
                    },
                    "tooltip": {
                        "hideZeros": false,
                        "mode": "single",
                        "sort": "none"
                    }
                },
                "targets": [
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_bottom_min{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "hide": false,
                        "includeNullMetadata": true,
                        "instant": false,
                        "legendFormat": "Min",
                        "range": true,
                        "refId": "A",
                        "useBackend": false
                    },
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_bottom_avg{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "includeNullMetadata": true,
                        "legendFormat": "Avg",
                        "range": true,
                        "refId": "B",
                        "useBackend": false
                    },
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_bottom_max{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "hide": false,
                        "includeNullMetadata": true,
                        "instant": false,
                        "legendFormat": "Max",
                        "range": true,
                        "refId": "C",
                        "useBackend": false
                    }
                ]
            }
        ]
    }

~~~
- dashboard created
- wait until grafana dashboard found [timeout:60s]...

Create Grafana Dashboard
------------------------

~~~
apiVersion: grafana.integreatly.org/v1beta1
kind: GrafanaDashboard
metadata:
  name: hw-cpu-utilization-my-server
  namespace: grafana-operator
spec:
  folder: my-server
  instanceSelector:
    matchLabels:
      dashboards: test
  json: |-
    {
        "title": "Hardware CPU Utilization",
        "uid": "hw-cpu-utilization-my-server",
        "panels": [
            {
                "title": "CPU",
                "type": "gauge",
                "gridPos": {
                    "x": 0,
                    "y": 0,
                    "h": 4,
                    "w": 3
                },
                "fieldConfig": {
                    "defaults": {
                        "color": {
                            "mode": "thresholds"
                        },
                        "mappings": [],
                        "thresholds": {
                            "mode": "absolute",
                            "steps": [
                                {
                                    "color": "green",
                                    "value": 0
                                },
                                {
                                    "color": "red",
                                    "value": 80
                                }
                            ]
                        },
                        "unit": "percentunit"
                    },
                    "overrides": []
                },
                "targets": [
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_cpu_max{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "includeNullMetadata": true,
                        "legendFormat": "__auto",
                        "range": true,
                        "refId": "A",
                        "useBackend": false
                    }
                ],
                "datasource": {
                    "uid": "8327c396-080c-471e-b403-69d100cb8aa9",
                    "type": "prometheus"
                }
            },
            {
                "title": "CPU Load",
                "type": "timeseries",
                "datasource": {
                    "type": "prometheus",
                    "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                },
                "fieldConfig": {
                    "defaults": {
                        "color": {
                            "mode": "palette-classic"
                        },
                        "custom": {
                            "axisBorderShow": false,
                            "axisCenteredZero": false,
                            "axisColorMode": "text",
                            "axisLabel": "",
                            "axisPlacement": "auto",
                            "barAlignment": 0,
                            "barWidthFactor": 0.6,
                            "drawStyle": "line",
                            "fillOpacity": 0,
                            "gradientMode": "none",
                            "hideFrom": {
                                "legend": false,
                                "tooltip": false,
                                "viz": false
                            },
                            "insertNulls": false,
                            "lineInterpolation": "linear",
                            "lineWidth": 1,
                            "pointSize": 5,
                            "scaleDistribution": {
                                "type": "linear"
                            },
                            "showPoints": "auto",
                            "spanNulls": false,
                            "stacking": {
                                "group": "A",
                                "mode": "none"
                            },
                            "thresholdsStyle": {
                                "mode": "off"
                            }
                        },
                        "mappings": [],
                        "thresholds": {
                            "mode": "absolute",
                            "steps": [
                                {
                                    "color": "green",
                                    "value": 0
                                },
                                {
                                    "color": "red",
                                    "value": 80
                                }
                            ]
                        },
                        "unit": "percentunit"
                    },
                    "overrides": []
                },
                "gridPos": {
                    "h": 8,
                    "w": 12,
                    "x": 3,
                    "y": 0
                },
                "options": {
                    "legend": {
                        "calcs": [],
                        "displayMode": "list",
                        "placement": "bottom",
                        "showLegend": true
                    },
                    "tooltip": {
                        "hideZeros": false,
                        "mode": "single",
                        "sort": "none"
                    }
                },
                "targets": [
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_cpu_min{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "hide": false,
                        "includeNullMetadata": true,
                        "instant": false,
                        "legendFormat": "Min",
                        "range": true,
                        "refId": "A",
                        "useBackend": false
                    },
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_cpu_avg{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "includeNullMetadata": true,
                        "legendFormat": "Avg",
                        "range": true,
                        "refId": "B",
                        "useBackend": false
                    },
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_cpu_max{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "hide": false,
                        "includeNullMetadata": true,
                        "instant": false,
                        "legendFormat": "Max",
                        "range": true,
                        "refId": "C",
                        "useBackend": false
                    }
                ]
            }
        ]
    }

~~~
- dashboard created
- wait until grafana dashboard found [timeout:60s]...

Create Grafana Dashboard
------------------------

~~~
apiVersion: grafana.integreatly.org/v1beta1
kind: GrafanaDashboard
metadata:
  name: hw-power-utilization-my-server
  namespace: grafana-operator
spec:
  folder: my-server
  instanceSelector:
    matchLabels:
      dashboards: test
  json: |-
    {
        "title": "Host Power Utilization",
        "uid": "hw-power-utilization-my-server",
        "panels": [
            {
                "title": "Power [W]",
                "type": "stat",
                "gridPos": {
                    "x": 0,
                    "y": 0,
                    "h": 4,
                    "w": 3
                },
                "fieldConfig": {
                    "defaults": {
                        "color": {
                            "mode": "thresholds"
                        },
                        "mappings": [],
                        "thresholds": {
                            "mode": "absolute",
                            "steps": [
                                {
                                    "color": "green",
                                    "value": 0
                                },
                                {
                                    "color": "red",
                                    "value": 1000
                                }
                            ]
                        },
                        "unit": "none"
                    },
                    "overrides": []
                },
                "targets": [
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_power_avg{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "includeNullMetadata": true,
                        "legendFormat": "__auto",
                        "range": true,
                        "refId": "A",
                        "useBackend": false
                    }
                ],
                "datasource": {
                    "uid": "8327c396-080c-471e-b403-69d100cb8aa9",
                    "type": "prometheus"
                }
            },
            {
                "title": "Host Power [W]",
                "type": "timeseries",
                "datasource": {
                    "type": "prometheus",
                    "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                },
                "fieldConfig": {
                    "defaults": {
                        "color": {
                            "mode": "palette-classic"
                        },
                        "custom": {
                            "axisBorderShow": false,
                            "axisCenteredZero": false,
                            "axisColorMode": "text",
                            "axisLabel": "",
                            "axisPlacement": "auto",
                            "barAlignment": 0,
                            "barWidthFactor": 0.6,
                            "drawStyle": "line",
                            "fillOpacity": 0,
                            "gradientMode": "none",
                            "hideFrom": {
                                "legend": false,
                                "tooltip": false,
                                "viz": false
                            },
                            "insertNulls": false,
                            "lineInterpolation": "linear",
                            "lineWidth": 1,
                            "pointSize": 5,
                            "scaleDistribution": {
                                "type": "linear"
                            },
                            "showPoints": "auto",
                            "spanNulls": false,
                            "stacking": {
                                "group": "A",
                                "mode": "none"
                            },
                            "thresholdsStyle": {
                                "mode": "off"
                            }
                        },
                        "mappings": [],
                        "thresholds": {
                            "mode": "absolute",
                            "steps": [
                                {
                                    "color": "green",
                                    "value": 0
                                },
                                {
                                    "color": "red",
                                    "value": 80
                                }
                            ]
                        },
                        "unit": "none"
                    },
                    "overrides": []
                },
                "gridPos": {
                    "h": 8,
                    "w": 12,
                    "x": 3,
                    "y": 0
                },
                "options": {
                    "legend": {
                        "calcs": [],
                        "displayMode": "list",
                        "placement": "bottom",
                        "showLegend": true
                    },
                    "tooltip": {
                        "hideZeros": false,
                        "mode": "single",
                        "sort": "none"
                    }
                },
                "targets": [
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_power_min{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "hide": false,
                        "includeNullMetadata": true,
                        "instant": false,
                        "legendFormat": "Min",
                        "range": true,
                        "refId": "A",
                        "useBackend": false
                    },
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_power_avg{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "includeNullMetadata": true,
                        "legendFormat": "Avg",
                        "range": true,
                        "refId": "B",
                        "useBackend": false
                    },
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_power_max{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "hide": false,
                        "includeNullMetadata": true,
                        "instant": false,
                        "legendFormat": "Max",
                        "range": true,
                        "refId": "C",
                        "useBackend": false
                    }
                ]
            }
        ]
    }

~~~
- dashboard created
- wait until grafana dashboard found [timeout:60s]...

Create Grafana Dashboard
------------------------

~~~
apiVersion: grafana.integreatly.org/v1beta1
kind: GrafanaDashboard
metadata:
  name: hw-temp-bottom-my-server
  namespace: grafana-operator
spec:
  folder: my-server
  instanceSelector:
    matchLabels:
      dashboards: test
  json: |-
    {
        "title": "Temperature Sensor Bottom",
        "uid": "hw-temp-bottom-my-server",
        "panels": [
            {
                "title": "Temp Bottom [C]",
                "type": "stat",
                "gridPos": {
                    "x": 0,
                    "y": 0,
                    "h": 4,
                    "w": 3
                },
                "fieldConfig": {
                    "defaults": {
                        "color": {
                            "mode": "thresholds"
                        },
                        "mappings": [],
                        "thresholds": {
                            "mode": "absolute",
                            "steps": [
                                {
                                    "color": "green",
                                    "value": 0
                                },
                                {
                                    "color": "red",
                                    "value": 100
                                }
                            ]
                        },
                        "unit": "none"
                    },
                    "overrides": []
                },
                "targets": [
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_bottom_avg{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "includeNullMetadata": true,
                        "legendFormat": "__auto",
                        "range": true,
                        "refId": "A",
                        "useBackend": false
                    }
                ],
                "datasource": {
                    "uid": "8327c396-080c-471e-b403-69d100cb8aa9",
                    "type": "prometheus"
                }
            },
            {
                "title": "Temp Bottom [C]",
                "type": "timeseries",
                "datasource": {
                    "type": "prometheus",
                    "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                },
                "fieldConfig": {
                    "defaults": {
                        "color": {
                            "mode": "palette-classic"
                        },
                        "custom": {
                            "axisBorderShow": false,
                            "axisCenteredZero": false,
                            "axisColorMode": "text",
                            "axisLabel": "",
                            "axisPlacement": "auto",
                            "barAlignment": 0,
                            "barWidthFactor": 0.6,
                            "drawStyle": "line",
                            "fillOpacity": 0,
                            "gradientMode": "none",
                            "hideFrom": {
                                "legend": false,
                                "tooltip": false,
                                "viz": false
                            },
                            "insertNulls": false,
                            "lineInterpolation": "linear",
                            "lineWidth": 1,
                            "pointSize": 5,
                            "scaleDistribution": {
                                "type": "linear"
                            },
                            "showPoints": "auto",
                            "spanNulls": false,
                            "stacking": {
                                "group": "A",
                                "mode": "none"
                            },
                            "thresholdsStyle": {
                                "mode": "off"
                            }
                        },
                        "mappings": [],
                        "thresholds": {
                            "mode": "absolute",
                            "steps": [
                                {
                                    "color": "green",
                                    "value": 0
                                },
                                {
                                    "color": "red",
                                    "value": 80
                                }
                            ]
                        },
                        "unit": "none"
                    },
                    "overrides": []
                },
                "gridPos": {
                    "h": 8,
                    "w": 12,
                    "x": 3,
                    "y": 0
                },
                "options": {
                    "legend": {
                        "calcs": [],
                        "displayMode": "list",
                        "placement": "bottom",
                        "showLegend": true
                    },
                    "tooltip": {
                        "hideZeros": false,
                        "mode": "single",
                        "sort": "none"
                    }
                },
                "targets": [
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_bottom_min{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "hide": false,
                        "includeNullMetadata": true,
                        "instant": false,
                        "legendFormat": "Min",
                        "range": true,
                        "refId": "A",
                        "useBackend": false
                    },
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_bottom_avg{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "includeNullMetadata": true,
                        "legendFormat": "Avg",
                        "range": true,
                        "refId": "B",
                        "useBackend": false
                    },
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_bottom_max{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "hide": false,
                        "includeNullMetadata": true,
                        "instant": false,
                        "legendFormat": "Max",
                        "range": true,
                        "refId": "C",
                        "useBackend": false
                    }
                ]
            }
        ]
    }

~~~
- dashboard created
- wait until grafana dashboard found [timeout:60s]...

Create Grafana Dashboard
------------------------

~~~
apiVersion: grafana.integreatly.org/v1beta1
kind: GrafanaDashboard
metadata:
  name: hw-temp-front-my-server
  namespace: grafana-operator
spec:
  folder: my-server
  instanceSelector:
    matchLabels:
      dashboards: test
  json: |-
    {
        "title": "Temperature Sensor Front",
        "uid": "hw-temp-front-my-server",
        "panels": [
            {
                "title": "Temp Front [C]",
                "type": "stat",
                "gridPos": {
                    "x": 0,
                    "y": 0,
                    "h": 4,
                    "w": 3
                },
                "fieldConfig": {
                    "defaults": {
                        "color": {
                            "mode": "thresholds"
                        },
                        "mappings": [],
                        "thresholds": {
                            "mode": "absolute",
                            "steps": [
                                {
                                    "color": "green",
                                    "value": 0
                                },
                                {
                                    "color": "red",
                                    "value": 100
                                }
                            ]
                        },
                        "unit": "none"
                    },
                    "overrides": []
                },
                "targets": [
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_front_avg{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "includeNullMetadata": true,
                        "legendFormat": "__auto",
                        "range": true,
                        "refId": "A",
                        "useBackend": false
                    }
                ],
                "datasource": {
                    "uid": "8327c396-080c-471e-b403-69d100cb8aa9",
                    "type": "prometheus"
                }
            },
            {
                "title": "Temp Front [C]",
                "type": "timeseries",
                "datasource": {
                    "type": "prometheus",
                    "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                },
                "fieldConfig": {
                    "defaults": {
                        "color": {
                            "mode": "palette-classic"
                        },
                        "custom": {
                            "axisBorderShow": false,
                            "axisCenteredZero": false,
                            "axisColorMode": "text",
                            "axisLabel": "",
                            "axisPlacement": "auto",
                            "barAlignment": 0,
                            "barWidthFactor": 0.6,
                            "drawStyle": "line",
                            "fillOpacity": 0,
                            "gradientMode": "none",
                            "hideFrom": {
                                "legend": false,
                                "tooltip": false,
                                "viz": false
                            },
                            "insertNulls": false,
                            "lineInterpolation": "linear",
                            "lineWidth": 1,
                            "pointSize": 5,
                            "scaleDistribution": {
                                "type": "linear"
                            },
                            "showPoints": "auto",
                            "spanNulls": false,
                            "stacking": {
                                "group": "A",
                                "mode": "none"
                            },
                            "thresholdsStyle": {
                                "mode": "off"
                            }
                        },
                        "mappings": [],
                        "thresholds": {
                            "mode": "absolute",
                            "steps": [
                                {
                                    "color": "green",
                                    "value": 0
                                },
                                {
                                    "color": "red",
                                    "value": 80
                                }
                            ]
                        },
                        "unit": "none"
                    },
                    "overrides": []
                },
                "gridPos": {
                    "h": 8,
                    "w": 12,
                    "x": 3,
                    "y": 0
                },
                "options": {
                    "legend": {
                        "calcs": [],
                        "displayMode": "list",
                        "placement": "bottom",
                        "showLegend": true
                    },
                    "tooltip": {
                        "hideZeros": false,
                        "mode": "single",
                        "sort": "none"
                    }
                },
                "targets": [
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_front_min{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "hide": false,
                        "includeNullMetadata": true,
                        "instant": false,
                        "legendFormat": "Min",
                        "range": true,
                        "refId": "A",
                        "useBackend": false
                    },
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_front_avg{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "includeNullMetadata": true,
                        "legendFormat": "Avg",
                        "range": true,
                        "refId": "B",
                        "useBackend": false
                    },
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_front_max{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "hide": false,
                        "includeNullMetadata": true,
                        "instant": false,
                        "legendFormat": "Max",
                        "range": true,
                        "refId": "C",
                        "useBackend": false
                    }
                ]
            }
        ]
    }

~~~
- dashboard created
- wait until grafana dashboard found [timeout:60s]...

Create Grafana Dashboard
------------------------

~~~
apiVersion: grafana.integreatly.org/v1beta1
kind: GrafanaDashboard
metadata:
  name: hw-temp-middle-my-server
  namespace: grafana-operator
spec:
  folder: my-server
  instanceSelector:
    matchLabels:
      dashboards: test
  json: |-
    {
        "title": "Temperature Sensor Middle",
        "uid": "hw-temp-middle-my-server",
        "panels": [
            {
                "title": "Temp Middle [C]",
                "type": "stat",
                "gridPos": {
                    "x": 0,
                    "y": 0,
                    "h": 4,
                    "w": 3
                },
                "fieldConfig": {
                    "defaults": {
                        "color": {
                            "mode": "thresholds"
                        },
                        "mappings": [],
                        "thresholds": {
                            "mode": "absolute",
                            "steps": [
                                {
                                    "color": "green",
                                    "value": 0
                                },
                                {
                                    "color": "red",
                                    "value": 100
                                }
                            ]
                        },
                        "unit": "none"
                    },
                    "overrides": []
                },
                "targets": [
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_middle_avg{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "includeNullMetadata": true,
                        "legendFormat": "__auto",
                        "range": true,
                        "refId": "A",
                        "useBackend": false
                    }
                ],
                "datasource": {
                    "uid": "8327c396-080c-471e-b403-69d100cb8aa9",
                    "type": "prometheus"
                }
            },
            {
                "title": "Temp Middle [C]",
                "type": "timeseries",
                "datasource": {
                    "type": "prometheus",
                    "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                },
                "fieldConfig": {
                    "defaults": {
                        "color": {
                            "mode": "palette-classic"
                        },
                        "custom": {
                            "axisBorderShow": false,
                            "axisCenteredZero": false,
                            "axisColorMode": "text",
                            "axisLabel": "",
                            "axisPlacement": "auto",
                            "barAlignment": 0,
                            "barWidthFactor": 0.6,
                            "drawStyle": "line",
                            "fillOpacity": 0,
                            "gradientMode": "none",
                            "hideFrom": {
                                "legend": false,
                                "tooltip": false,
                                "viz": false
                            },
                            "insertNulls": false,
                            "lineInterpolation": "linear",
                            "lineWidth": 1,
                            "pointSize": 5,
                            "scaleDistribution": {
                                "type": "linear"
                            },
                            "showPoints": "auto",
                            "spanNulls": false,
                            "stacking": {
                                "group": "A",
                                "mode": "none"
                            },
                            "thresholdsStyle": {
                                "mode": "off"
                            }
                        },
                        "mappings": [],
                        "thresholds": {
                            "mode": "absolute",
                            "steps": [
                                {
                                    "color": "green",
                                    "value": 0
                                },
                                {
                                    "color": "red",
                                    "value": 80
                                }
                            ]
                        },
                        "unit": "none"
                    },
                    "overrides": []
                },
                "gridPos": {
                    "h": 8,
                    "w": 12,
                    "x": 3,
                    "y": 0
                },
                "options": {
                    "legend": {
                        "calcs": [],
                        "displayMode": "list",
                        "placement": "bottom",
                        "showLegend": true
                    },
                    "tooltip": {
                        "hideZeros": false,
                        "mode": "single",
                        "sort": "none"
                    }
                },
                "targets": [
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_middle_min{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "hide": false,
                        "includeNullMetadata": true,
                        "instant": false,
                        "legendFormat": "Min",
                        "range": true,
                        "refId": "A",
                        "useBackend": false
                    },
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_middle_avg{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "includeNullMetadata": true,
                        "legendFormat": "Avg",
                        "range": true,
                        "refId": "B",
                        "useBackend": false
                    },
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_middle_max{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "hide": false,
                        "includeNullMetadata": true,
                        "instant": false,
                        "legendFormat": "Max",
                        "range": true,
                        "refId": "C",
                        "useBackend": false
                    }
                ]
            }
        ]
    }

~~~
- dashboard created
- wait until grafana dashboard found [timeout:60s]...

Create Grafana Dashboard
------------------------

~~~
apiVersion: grafana.integreatly.org/v1beta1
kind: GrafanaDashboard
metadata:
  name: hw-temp-p1-my-server
  namespace: grafana-operator
spec:
  folder: my-server
  instanceSelector:
    matchLabels:
      dashboards: test
  json: |-
    {
        "title": "Temperature Sensor P1",
        "uid": "hw-temp-p1-my-server",
        "panels": [
            {
                "title": "Temp P1 [C]",
                "type": "stat",
                "gridPos": {
                    "x": 0,
                    "y": 0,
                    "h": 4,
                    "w": 3
                },
                "fieldConfig": {
                    "defaults": {
                        "color": {
                            "mode": "thresholds"
                        },
                        "mappings": [],
                        "thresholds": {
                            "mode": "absolute",
                            "steps": [
                                {
                                    "color": "green",
                                    "value": 0
                                },
                                {
                                    "color": "red",
                                    "value": 100
                                }
                            ]
                        },
                        "unit": "none"
                    },
                    "overrides": []
                },
                "targets": [
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_p1_avg{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "includeNullMetadata": true,
                        "legendFormat": "__auto",
                        "range": true,
                        "refId": "A",
                        "useBackend": false
                    }
                ],
                "datasource": {
                    "uid": "8327c396-080c-471e-b403-69d100cb8aa9",
                    "type": "prometheus"
                }
            },
            {
                "title": "Temp P1 [C]",
                "type": "timeseries",
                "datasource": {
                    "type": "prometheus",
                    "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                },
                "fieldConfig": {
                    "defaults": {
                        "color": {
                            "mode": "palette-classic"
                        },
                        "custom": {
                            "axisBorderShow": false,
                            "axisCenteredZero": false,
                            "axisColorMode": "text",
                            "axisLabel": "",
                            "axisPlacement": "auto",
                            "barAlignment": 0,
                            "barWidthFactor": 0.6,
                            "drawStyle": "line",
                            "fillOpacity": 0,
                            "gradientMode": "none",
                            "hideFrom": {
                                "legend": false,
                                "tooltip": false,
                                "viz": false
                            },
                            "insertNulls": false,
                            "lineInterpolation": "linear",
                            "lineWidth": 1,
                            "pointSize": 5,
                            "scaleDistribution": {
                                "type": "linear"
                            },
                            "showPoints": "auto",
                            "spanNulls": false,
                            "stacking": {
                                "group": "A",
                                "mode": "none"
                            },
                            "thresholdsStyle": {
                                "mode": "off"
                            }
                        },
                        "mappings": [],
                        "thresholds": {
                            "mode": "absolute",
                            "steps": [
                                {
                                    "color": "green",
                                    "value": 0
                                },
                                {
                                    "color": "red",
                                    "value": 80
                                }
                            ]
                        },
                        "unit": "none"
                    },
                    "overrides": []
                },
                "gridPos": {
                    "h": 8,
                    "w": 12,
                    "x": 3,
                    "y": 0
                },
                "options": {
                    "legend": {
                        "calcs": [],
                        "displayMode": "list",
                        "placement": "bottom",
                        "showLegend": true
                    },
                    "tooltip": {
                        "hideZeros": false,
                        "mode": "single",
                        "sort": "none"
                    }
                },
                "targets": [
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_p1_min{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "hide": false,
                        "includeNullMetadata": true,
                        "instant": false,
                        "legendFormat": "Min",
                        "range": true,
                        "refId": "A",
                        "useBackend": false
                    },
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_p1_avg{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "includeNullMetadata": true,
                        "legendFormat": "Avg",
                        "range": true,
                        "refId": "B",
                        "useBackend": false
                    },
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_p1_max{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "hide": false,
                        "includeNullMetadata": true,
                        "instant": false,
                        "legendFormat": "Max",
                        "range": true,
                        "refId": "C",
                        "useBackend": false
                    }
                ]
            }
        ]
    }

~~~
- dashboard created
- wait until grafana dashboard found [timeout:60s]...

Create Grafana Dashboard
------------------------

~~~
apiVersion: grafana.integreatly.org/v1beta1
kind: GrafanaDashboard
metadata:
  name: hw-temp-p2-my-server
  namespace: grafana-operator
spec:
  folder: my-server
  instanceSelector:
    matchLabels:
      dashboards: test
  json: |-
    {
        "title": "Temperature Sensor P2",
        "uid": "hw-temp-p2-my-server",
        "panels": [
            {
                "title": "Temp P2 [C]",
                "type": "stat",
                "gridPos": {
                    "x": 0,
                    "y": 0,
                    "h": 4,
                    "w": 3
                },
                "fieldConfig": {
                    "defaults": {
                        "color": {
                            "mode": "thresholds"
                        },
                        "mappings": [],
                        "thresholds": {
                            "mode": "absolute",
                            "steps": [
                                {
                                    "color": "green",
                                    "value": 0
                                },
                                {
                                    "color": "red",
                                    "value": 100
                                }
                            ]
                        },
                        "unit": "none"
                    },
                    "overrides": []
                },
                "targets": [
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_p2_avg{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "includeNullMetadata": true,
                        "legendFormat": "__auto",
                        "range": true,
                        "refId": "A",
                        "useBackend": false
                    }
                ],
                "datasource": {
                    "uid": "8327c396-080c-471e-b403-69d100cb8aa9",
                    "type": "prometheus"
                }
            },
            {
                "title": "Temp P2 [C]",
                "type": "timeseries",
                "datasource": {
                    "type": "prometheus",
                    "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                },
                "fieldConfig": {
                    "defaults": {
                        "color": {
                            "mode": "palette-classic"
                        },
                        "custom": {
                            "axisBorderShow": false,
                            "axisCenteredZero": false,
                            "axisColorMode": "text",
                            "axisLabel": "",
                            "axisPlacement": "auto",
                            "barAlignment": 0,
                            "barWidthFactor": 0.6,
                            "drawStyle": "line",
                            "fillOpacity": 0,
                            "gradientMode": "none",
                            "hideFrom": {
                                "legend": false,
                                "tooltip": false,
                                "viz": false
                            },
                            "insertNulls": false,
                            "lineInterpolation": "linear",
                            "lineWidth": 1,
                            "pointSize": 5,
                            "scaleDistribution": {
                                "type": "linear"
                            },
                            "showPoints": "auto",
                            "spanNulls": false,
                            "stacking": {
                                "group": "A",
                                "mode": "none"
                            },
                            "thresholdsStyle": {
                                "mode": "off"
                            }
                        },
                        "mappings": [],
                        "thresholds": {
                            "mode": "absolute",
                            "steps": [
                                {
                                    "color": "green",
                                    "value": 0
                                },
                                {
                                    "color": "red",
                                    "value": 80
                                }
                            ]
                        },
                        "unit": "none"
                    },
                    "overrides": []
                },
                "gridPos": {
                    "h": 8,
                    "w": 12,
                    "x": 3,
                    "y": 0
                },
                "options": {
                    "legend": {
                        "calcs": [],
                        "displayMode": "list",
                        "placement": "bottom",
                        "showLegend": true
                    },
                    "tooltip": {
                        "hideZeros": false,
                        "mode": "single",
                        "sort": "none"
                    }
                },
                "targets": [
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_p2_min{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "hide": false,
                        "includeNullMetadata": true,
                        "instant": false,
                        "legendFormat": "Min",
                        "range": true,
                        "refId": "A",
                        "useBackend": false
                    },
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_p2_avg{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "includeNullMetadata": true,
                        "legendFormat": "Avg",
                        "range": true,
                        "refId": "B",
                        "useBackend": false
                    },
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_p2_max{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "hide": false,
                        "includeNullMetadata": true,
                        "instant": false,
                        "legendFormat": "Max",
                        "range": true,
                        "refId": "C",
                        "useBackend": false
                    }
                ]
            }
        ]
    }

~~~
- dashboard created
- wait until grafana dashboard found [timeout:60s]...

Create Grafana Dashboard
------------------------

~~~
apiVersion: grafana.integreatly.org/v1beta1
kind: GrafanaDashboard
metadata:
  name: hw-temp-top-my-server
  namespace: grafana-operator
spec:
  folder: my-server
  instanceSelector:
    matchLabels:
      dashboards: test
  json: |-
    {
        "title": "Temperature Sensor Top",
        "uid": "hw-temp-top-my-server",
        "panels": [
            {
                "title": "Temp Top [C]",
                "type": "stat",
                "gridPos": {
                    "x": 0,
                    "y": 0,
                    "h": 4,
                    "w": 3
                },
                "fieldConfig": {
                    "defaults": {
                        "color": {
                            "mode": "thresholds"
                        },
                        "mappings": [],
                        "thresholds": {
                            "mode": "absolute",
                            "steps": [
                                {
                                    "color": "green",
                                    "value": 0
                                },
                                {
                                    "color": "red",
                                    "value": 100
                                }
                            ]
                        },
                        "unit": "none"
                    },
                    "overrides": []
                },
                "targets": [
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_top_avg{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "includeNullMetadata": true,
                        "legendFormat": "__auto",
                        "range": true,
                        "refId": "A",
                        "useBackend": false
                    }
                ],
                "datasource": {
                    "uid": "8327c396-080c-471e-b403-69d100cb8aa9",
                    "type": "prometheus"
                }
            },
            {
                "title": "Temp Top [C]",
                "type": "timeseries",
                "datasource": {
                    "type": "prometheus",
                    "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                },
                "fieldConfig": {
                    "defaults": {
                        "color": {
                            "mode": "palette-classic"
                        },
                        "custom": {
                            "axisBorderShow": false,
                            "axisCenteredZero": false,
                            "axisColorMode": "text",
                            "axisLabel": "",
                            "axisPlacement": "auto",
                            "barAlignment": 0,
                            "barWidthFactor": 0.6,
                            "drawStyle": "line",
                            "fillOpacity": 0,
                            "gradientMode": "none",
                            "hideFrom": {
                                "legend": false,
                                "tooltip": false,
                                "viz": false
                            },
                            "insertNulls": false,
                            "lineInterpolation": "linear",
                            "lineWidth": 1,
                            "pointSize": 5,
                            "scaleDistribution": {
                                "type": "linear"
                            },
                            "showPoints": "auto",
                            "spanNulls": false,
                            "stacking": {
                                "group": "A",
                                "mode": "none"
                            },
                            "thresholdsStyle": {
                                "mode": "off"
                            }
                        },
                        "mappings": [],
                        "thresholds": {
                            "mode": "absolute",
                            "steps": [
                                {
                                    "color": "green",
                                    "value": 0
                                },
                                {
                                    "color": "red",
                                    "value": 80
                                }
                            ]
                        },
                        "unit": "none"
                    },
                    "overrides": []
                },
                "gridPos": {
                    "h": 8,
                    "w": 12,
                    "x": 3,
                    "y": 0
                },
                "options": {
                    "legend": {
                        "calcs": [],
                        "displayMode": "list",
                        "placement": "bottom",
                        "showLegend": true
                    },
                    "tooltip": {
                        "hideZeros": false,
                        "mode": "single",
                        "sort": "none"
                    }
                },
                "targets": [
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_top_min{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "hide": false,
                        "includeNullMetadata": true,
                        "instant": false,
                        "legendFormat": "Min",
                        "range": true,
                        "refId": "A",
                        "useBackend": false
                    },
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_top_avg{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "includeNullMetadata": true,
                        "legendFormat": "Avg",
                        "range": true,
                        "refId": "B",
                        "useBackend": false
                    },
                    {
                        "datasource": {
                            "type": "prometheus",
                            "uid": "8327c396-080c-471e-b403-69d100cb8aa9"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_top_max{server_name=\"my-server\"}",
                        "fullMetaSearch": false,
                        "hide": false,
                        "includeNullMetadata": true,
                        "instant": false,
                        "legendFormat": "Max",
                        "range": true,
                        "refId": "C",
                        "useBackend": false
                    }
                ]
            }
        ]
    }

~~~
- dashboard created
- wait until grafana dashboard found [timeout:60s]...
```

[[Back]](./get_things_done.md)