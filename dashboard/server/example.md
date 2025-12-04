# Server Dashboard Example

```
# iserver set ocp grafana --cluster bm1 --mode dashboard --instance test --crd C:\tmp\imonitor\dashboard\server\ --scope server-name:my-server --no-confirm

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
  name: hw-cpu-utilization-my-server
  namespace: grafana-operator
  resourceVersion: '17463529'
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
                            "uid": "da183399-bccb-40c4-902e-d672321d193a"
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
                    "uid": "da183399-bccb-40c4-902e-d672321d193a",
                    "type": "prometheus"
                }
            },
            {
                "title": "CPU Load",
                "type": "timeseries",
                "datasource": {
                    "type": "prometheus",
                    "uid": "da183399-bccb-40c4-902e-d672321d193a"
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
                            "uid": "da183399-bccb-40c4-902e-d672321d193a"
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
                            "uid": "da183399-bccb-40c4-902e-d672321d193a"
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
                            "uid": "da183399-bccb-40c4-902e-d672321d193a"
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
- dashboard updated

Create Grafana Dashboard
------------------------

~~~
apiVersion: grafana.integreatly.org/v1beta1
kind: GrafanaDashboard
metadata:
  name: hw-power-utilization-my-server
  namespace: grafana-operator
  resourceVersion: '17463535'
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
                            "uid": "da183399-bccb-40c4-902e-d672321d193a"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_power_avg{scope=\"server:server_name=\"my-server\"\"}",
                        "fullMetaSearch": false,
                        "includeNullMetadata": true,
                        "legendFormat": "__auto",
                        "range": true,
                        "refId": "A",
                        "useBackend": false
                    }
                ],
                "datasource": {
                    "uid": "da183399-bccb-40c4-902e-d672321d193a",
                    "type": "prometheus"
                }
            },
            {
                "title": "Host Power [W]",
                "type": "timeseries",
                "datasource": {
                    "type": "prometheus",
                    "uid": "da183399-bccb-40c4-902e-d672321d193a"
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
                            "uid": "da183399-bccb-40c4-902e-d672321d193a"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_power_min{scope=\"server:server_name=\"my-server\"\"}",
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
                            "uid": "da183399-bccb-40c4-902e-d672321d193a"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_power_avg{scope=\"server:server_name=\"my-server\"\"}",
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
                            "uid": "da183399-bccb-40c4-902e-d672321d193a"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_power_max{scope=\"server:server_name=\"my-server\"\"}",
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
- dashboard updated

Create Grafana Dashboard
------------------------

~~~
apiVersion: grafana.integreatly.org/v1beta1
kind: GrafanaDashboard
metadata:
  name: hw-temp-bottom-my-server
  namespace: grafana-operator
  resourceVersion: '17463539'
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
                            "uid": "da183399-bccb-40c4-902e-d672321d193a"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_bottom_avg{scope=\"server:server_name=\"my-server\"\"}",
                        "fullMetaSearch": false,
                        "includeNullMetadata": true,
                        "legendFormat": "__auto",
                        "range": true,
                        "refId": "A",
                        "useBackend": false
                    }
                ],
                "datasource": {
                    "uid": "da183399-bccb-40c4-902e-d672321d193a",
                    "type": "prometheus"
                }
            },
            {
                "title": "Temp Bottom [C]",
                "type": "timeseries",
                "datasource": {
                    "type": "prometheus",
                    "uid": "da183399-bccb-40c4-902e-d672321d193a"
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
                            "uid": "da183399-bccb-40c4-902e-d672321d193a"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_bottom_min{scope=\"server:server_name=\"my-server\"\"}",
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
                            "uid": "da183399-bccb-40c4-902e-d672321d193a"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_bottom_avg{scope=\"server:server_name=\"my-server\"\"}",
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
                            "uid": "da183399-bccb-40c4-902e-d672321d193a"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_bottom_max{scope=\"server:server_name=\"my-server\"\"}",
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
- dashboard updated

Create Grafana Dashboard
------------------------

~~~
apiVersion: grafana.integreatly.org/v1beta1
kind: GrafanaDashboard
metadata:
  name: hw-temp-front-my-server
  namespace: grafana-operator
  resourceVersion: '17463544'
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
                            "uid": "da183399-bccb-40c4-902e-d672321d193a"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_front_avg{scope=\"server:server_name=\"my-server\"\"}",
                        "fullMetaSearch": false,
                        "includeNullMetadata": true,
                        "legendFormat": "__auto",
                        "range": true,
                        "refId": "A",
                        "useBackend": false
                    }
                ],
                "datasource": {
                    "uid": "da183399-bccb-40c4-902e-d672321d193a",
                    "type": "prometheus"
                }
            },
            {
                "title": "Temp Front [C]",
                "type": "timeseries",
                "datasource": {
                    "type": "prometheus",
                    "uid": "da183399-bccb-40c4-902e-d672321d193a"
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
                            "uid": "da183399-bccb-40c4-902e-d672321d193a"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_front_min{scope=\"server:server_name=\"my-server\"\"}",
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
                            "uid": "da183399-bccb-40c4-902e-d672321d193a"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_front_avg{scope=\"server:server_name=\"my-server\"\"}",
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
                            "uid": "da183399-bccb-40c4-902e-d672321d193a"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_front_max{scope=\"server:server_name=\"my-server\"\"}",
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
- dashboard updated

Create Grafana Dashboard
------------------------

~~~
apiVersion: grafana.integreatly.org/v1beta1
kind: GrafanaDashboard
metadata:
  name: hw-temp-middle-my-server
  namespace: grafana-operator
  resourceVersion: '17463552'
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
                            "uid": "da183399-bccb-40c4-902e-d672321d193a"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_middle_avg{scope=\"server:server_name=\"my-server\"\"}",
                        "fullMetaSearch": false,
                        "includeNullMetadata": true,
                        "legendFormat": "__auto",
                        "range": true,
                        "refId": "A",
                        "useBackend": false
                    }
                ],
                "datasource": {
                    "uid": "da183399-bccb-40c4-902e-d672321d193a",
                    "type": "prometheus"
                }
            },
            {
                "title": "Temp Middle [C]",
                "type": "timeseries",
                "datasource": {
                    "type": "prometheus",
                    "uid": "da183399-bccb-40c4-902e-d672321d193a"
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
                            "uid": "da183399-bccb-40c4-902e-d672321d193a"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_middle_min{scope=\"server:server_name=\"my-server\"\"}",
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
                            "uid": "da183399-bccb-40c4-902e-d672321d193a"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_middle_avg{scope=\"server:server_name=\"my-server\"\"}",
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
                            "uid": "da183399-bccb-40c4-902e-d672321d193a"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_middle_max{scope=\"server:server_name=\"my-server\"\"}",
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
- dashboard updated

Create Grafana Dashboard
------------------------

~~~
apiVersion: grafana.integreatly.org/v1beta1
kind: GrafanaDashboard
metadata:
  name: hw-temp-p1-my-server
  namespace: grafana-operator
  resourceVersion: '17463560'
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
                            "uid": "da183399-bccb-40c4-902e-d672321d193a"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_p1_avg{scope=\"server:server_name=\"my-server\"\"}",
                        "fullMetaSearch": false,
                        "includeNullMetadata": true,
                        "legendFormat": "__auto",
                        "range": true,
                        "refId": "A",
                        "useBackend": false
                    }
                ],
                "datasource": {
                    "uid": "da183399-bccb-40c4-902e-d672321d193a",
                    "type": "prometheus"
                }
            },
            {
                "title": "Temp P1 [C]",
                "type": "timeseries",
                "datasource": {
                    "type": "prometheus",
                    "uid": "da183399-bccb-40c4-902e-d672321d193a"
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
                            "uid": "da183399-bccb-40c4-902e-d672321d193a"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_p1_min{scope=\"server:server_name=\"my-server\"\"}",
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
                            "uid": "da183399-bccb-40c4-902e-d672321d193a"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_p1_avg{scope=\"server:server_name=\"my-server\"\"}",
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
                            "uid": "da183399-bccb-40c4-902e-d672321d193a"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_p1_max{scope=\"server:server_name=\"my-server\"\"}",
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
- dashboard updated

Create Grafana Dashboard
------------------------

~~~
apiVersion: grafana.integreatly.org/v1beta1
kind: GrafanaDashboard
metadata:
  name: hw-temp-p2-my-server
  namespace: grafana-operator
  resourceVersion: '17463566'
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
                            "uid": "da183399-bccb-40c4-902e-d672321d193a"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_p2_avg{scope=\"server:server_name=\"my-server\"\"}",
                        "fullMetaSearch": false,
                        "includeNullMetadata": true,
                        "legendFormat": "__auto",
                        "range": true,
                        "refId": "A",
                        "useBackend": false
                    }
                ],
                "datasource": {
                    "uid": "da183399-bccb-40c4-902e-d672321d193a",
                    "type": "prometheus"
                }
            },
            {
                "title": "Temp P2 [C]",
                "type": "timeseries",
                "datasource": {
                    "type": "prometheus",
                    "uid": "da183399-bccb-40c4-902e-d672321d193a"
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
                            "uid": "da183399-bccb-40c4-902e-d672321d193a"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_p2_min{scope=\"server:server_name=\"my-server\"\"}",
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
                            "uid": "da183399-bccb-40c4-902e-d672321d193a"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_p2_avg{scope=\"server:server_name=\"my-server\"\"}",
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
                            "uid": "da183399-bccb-40c4-902e-d672321d193a"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_p2_max{scope=\"server:server_name=\"my-server\"\"}",
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
- dashboard updated

Create Grafana Dashboard
------------------------

~~~
apiVersion: grafana.integreatly.org/v1beta1
kind: GrafanaDashboard
metadata:
  name: hw-temp-top-my-server
  namespace: grafana-operator
  resourceVersion: '17463579'
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
                            "uid": "da183399-bccb-40c4-902e-d672321d193a"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_top_avg{scope=\"server:server_name=\"my-server\"\"}",
                        "fullMetaSearch": false,
                        "includeNullMetadata": true,
                        "legendFormat": "__auto",
                        "range": true,
                        "refId": "A",
                        "useBackend": false
                    }
                ],
                "datasource": {
                    "uid": "da183399-bccb-40c4-902e-d672321d193a",
                    "type": "prometheus"
                }
            },
            {
                "title": "Temp Top [C]",
                "type": "timeseries",
                "datasource": {
                    "type": "prometheus",
                    "uid": "da183399-bccb-40c4-902e-d672321d193a"
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
                            "uid": "da183399-bccb-40c4-902e-d672321d193a"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_top_min{scope=\"server:server_name=\"my-server\"\"}",
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
                            "uid": "da183399-bccb-40c4-902e-d672321d193a"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_top_avg{scope=\"server:server_name=\"my-server\"\"}",
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
                            "uid": "da183399-bccb-40c4-902e-d672321d193a"
                        },
                        "disableTextWrap": false,
                        "editorMode": "builder",
                        "expr": "intersight_hw_temp_top_max{scope=\"server:server_name=\"my-server\"\"}",
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
- dashboard updated
```

[[Back]](./README.md)