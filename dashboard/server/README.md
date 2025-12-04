## Server Dashboards Library

File | Description | Example
--- | --- | --- | ---
[intersight.hw.all](./intersight.hw.all) | All intersight metrics | [Link](../../image/intersight_hw_all_dashboard.png)
[intersight.hw.cpu](./intersight.hw.cpu) | CPU metrics | [Link](../../image/intersight_hw_cpu_dashboard.png)
[intersight.hw.power](./intersight.hw.power) | Power metrics | [Link](../../image/intersight_hw_power_dashboard.png)
[intersight.hw.temp.bottom](./intersight.hw.temp.bottom) | Temperature rear bottom metrics | [Link](../../image/intersight_hw_temp_bottom_dashboard.png)
[intersight.hw.temp.front](./intersight.hw.temp.front) | Temperature front metrics | [Link](../../image/intersight_hw_temp_front_dashboard.png)
[intersight.hw.temp.middle](./intersight.hw.temp.middle) | Temperature rear middle metrics | [Link](../../image/intersight_hw_temp_middle_dashboard.png)
[intersight.hw.temp.p1](./intersight.hw.temp.p1) | Temperature P1 metrics | [Link](../../image/intersight_hw_temp_p1_dashboard.png)
[intersight.hw.temp.p2](./intersight.hw.temp.p2) | Temperature P2 metrics | [Link](../../image/intersight_hw_temp_p2_dashboard.png)

## HowTo

```
# iserver set ocp grafana --mode dashboard
  --cluster TEXT                  Cluster Name
  --instance TEXT                 Grafana instance name
  --crd TEXT                      Grafana CRDs or template
  --scope TEXT                    Prometheus metric scope
  --target TEXT                   Target dashboard folder:name for template
  --no-confirm                    Confirmation mode
```

## Expected Outcome

```
# iserver set ocp grafana --cluster bm3 --mode dashboard --instance test --crd C:\kali\cisco\devel\imonitor\dashboard\server\ --scope server-name:outshift-ai-pod-1-3 --no-confirm
```

[Dashboards](../../image/dashboards.png)

Full [output](./example.md) reference

## From Template to GrafanaDashboard CRD

Template example

```
{
    "title": "Hardware CPU Utilization",
    "folder": "${FOLDER}",
    "uid": "hw-cpu-utilization-${NAME}",
    "panels": [
        {
            "ipanel": "intersight.hw.cpu:gauge"
        },
        {
            "ipanel": "intersight.hw.cpu:ts"
        }
    ]
}
```

GrafanaDashboard CRD 

```
apiVersion: grafana.integreatly.org/v1beta1
kind: GrafanaDashboard
metadata:
  name: hw-cpu-utilization-${NAME}
  namespace: grafana-operator
spec:
  folder: ${FOLDER}
  instanceSelector:
    matchLabels:
      dashboards: test
  json: |-
    {
        "title": "Hardware CPU Utilization",
        "uid": "hw-cpu-utilization-${NAME}",
        "panels": [
            {
                "ipanel": "intersight.hw.cpu:gauge"
            },
            {
                "ipanel": "intersight.hw.cpu:ts"
            }
        ]
    }
```

ipanels are resolved based on [panel templates](./panel/README.md) and variable replaced with values

## Variable Values

Variable | Value
--- | ---
${PROMETHEUS} | Prometheus datasource uid of grafana --instance parameter
${SCOPE} | Based on --scope parameter in key:value format is translated into 'key=\"value\"' string
${FOLDER} | Based on --target parameter in name:folder format that defaults to value:value based on --scope
${NAME} | Based on --target parameter in name:folder format that defaults to value:value based on --scope

### Example

```
# iserver set ocp grafana --cluster bm1 --mode dashboard --instance test --crd C:\test\imonitor\dashboard\server\intersight.hw.cpu --scope server-name:my-server
```

Panel expression that selects configured prometheus metric value by attribute

```
"expr": "intersight_hw_cpu_max{server_name=\"my-server\"}"
```

Grafana CRD that contains dashbord json source

```
apiVersion: grafana.integreatly.org/v1beta1
kind: GrafanaDashboard
metadata:
  name: hw-cpu-utilization-my-server
  namespace: grafana-operator
spec:
  folder: my-server
  json: |-
    {
        "title": "Hardware CPU Utilization",
        "uid": "hw-cpu-utilization-my-server",
```

### Example

```
# iserver set ocp grafana --cluster bm1 --mode dashboard --instance test --crd C:\test\imonitor\dashboard\server\intersight.hw.cpu --scope server-name:my-server --target abc:xyz
```

Panel expression that selects configured prometheus metric value by attribute

```
"expr": "intersight_hw_cpu_max{server_name=\"my-server\"}"
```

Grafana CRD that contains dashbord json source

```
apiVersion: grafana.integreatly.org/v1beta1
kind: GrafanaDashboard
metadata:
  name: hw-cpu-utilization-abc
  namespace: grafana-operator
spec:
  folder: xyz
  json: |-
    {
        "title": "Hardware CPU Utilization",
        "uid": "hw-cpu-utilization-abc",
```

[[Back]](../README.md)