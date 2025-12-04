# Grafana Dashboard Panels Library

## Overview

This directory contains the Grafana dashboard panels designed to visualize metrics in Prometheus. The panels are then referred in Grafana [dashboard template](../dashboard/README.md).

Panels can be modified (added or edited). Just follow few simple rules 
- organize panels in into subdirectories in flat structure (1-level)
- keep single panel json source per file within directory
- refer to panel in dashboard template using directory-name:panel-name reference

## Panel Definition

- Grafana [documentation](https://grafana.com/docs/grafana/latest/visualizations/panels-visualizations/) is the single source of truth for panel json definition
- iserver expects json structure i.e. it is not validating grafana panel data model

## Panel Variables

- panel defines the visualization of Prometheus metrics and as such it needs to refer to Prometheus datasource as well as select the metric
- if Prometheus datasource is parametrized with ${PROMETHEUS} then it will be on-the-fly replaced with the Prometheus datasource uid when grafana dashboard is created in the selected grafana instance
- if Prometheus target is parameterized with ${SCOPE} then it will be on-the-fly replaced with the appropriate scope value of the server's metric for which grafana dashboard is created

These two parameters make the panel re-usable.

Note: the engine is replacing these two variables across entire JSON body of the panel definition

## GridPos

Every panel definition contiains GridPos parameters that define both its size and position in the grid e.g.

```
  "gridPos": {
    "x": 0,
    "y": 0,
    "h": 4,
    "w": 3
  }
```

The size parameters (h/w) are used in the generated dashboard while location parameters (x/y) are always recalculated based on the grafana dashboard template content. You can change/override the panels size in grafana dashboard template if needed.

## Panels Library

Name | Metric | Type | Template | Example
--- | --- | --- | --- | ---
intersight.hw.cpu | CPU utilization | gauge | [Link](./intersight.hw.cpu/gauge) | [Link](../image/intersight_hw_cpu_gauge.png)
intersight.hw.cpu | CPU utilization | time series | [Link](./intersight.hw.cpu/ts) | [Link](../image/intersight_hw_cpu_ts.png)
intersight.hw.power | Host Power | stat | [Link](./intersight.hw.power/stat) | [Link](../image/intersight_hw_power_stat.png)
intersight.hw.power | Host Power | time series | [Link](./intersight.hw.power/ts) | [Link](../image/intersight_hw_power_ts.png)
intersight.hw.temp.bottom | Temperature Rear Bottom | stat | [Link](./intersight.hw.temp.bottom/stat) | [Link](../image/intersight_hw_temp_bottom_stat.png)
intersight.hw.temp.bottom | Temperature Rear Bottom | time series | [Link](./intersight.hw.temp.bottom/ts) | [Link](../image/intersight_hw_temp_bottom_ts.png)
intersight.hw.temp.front | Temperature Front | stat | [Link](./intersight.hw.temp.front/stat) | [Link](../image/intersight_hw_temp_front_stat.png)
intersight.hw.temp.front | Temperature Front | time series | [Link](./intersight.hw.temp.front/ts) | [Link](../image/intersight_hw_temp_front_ts.png)
intersight.hw.temp.middle | Temperature Rear Middle | stat | [Link](./intersight.hw.temp.middle/stat) | [Link](../image/intersight_hw_temp_middle_stat.png)
intersight.hw.temp.middle | Temperature Rear Middle | time series | [Link](./intersight.hw.temp.middle/ts) | [Link](../image/intersight_hw_temp_middle_ts.png)
intersight.hw.temp.p1 | Temperature P1 | stat | [Link](./intersight.hw.temp.p1/stat) | [Link](../image/intersight_hw_temp_p1_stat.png)
intersight.hw.temp.p1 | Temperature P1 | time series | [Link](./intersight.hw.temp.p1/ts) | [Link](../image/intersight_hw_temp_p1_ts.png)
intersight.hw.temp.p2 | Temperature P2 | stat | [Link](./intersight.hw.temp.p2/stat) | [Link](../image/intersight_hw_temp_p2_stat.png)
intersight.hw.temp.p2 | Temperature P2 | time series | [Link](./intersight.hw.temp.p2/ts) | [Link](../image/intersight_hw_temp_p2_ts.png)
intersight.hw.temp.top | Temperature Rear Top | stat | [Link](./intersight.hw.temp.top/stat) | [Link](../image/intersight_hw_temp_top_stat.png)
intersight.hw.temp.top | Temperature Rear Top | time series | [Link](./intersight.hw.temp.top/ts) | [Link](../image/intersight_hw_temp_top_ts.png)

[[Back]](../README.md)