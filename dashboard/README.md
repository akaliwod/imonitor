# Grafana Dashboard Templates 

## Overview

Grafana dashboard templates designed to visualize metrics in Prometheus as defined by [panel templates](./panel/README.md)

Goal:
- generate GrafanaDashboard CRD based on templates and user provided parameters
- apply it via Kubernetes API on the target OpenShift cluster
- Grafana operator should show the dashboard in UI

## Dashboard Template 

Grafana dashboards can be modified (added or edited). Just follow few simple rules 
- keep the dashboards in the single subdirectory 
- every dashboard file contains single dashboard definition following Grafana [data model](https://grafana.com/docs/grafana/latest/visualizations/dashboards/build-dashboards/view-dashboard-json-model/)
- panels list may contain panel template reference
- panel template reference follows the format panel-type:panel-name reference
- panel is expected to be in the ./panel/ location relative to dashboard source

Template Group | Description | Link
--- | --- | ---
Server | Dashboards for single server metrics | [Link](./server/README.md)

[[Back]](../README.md)