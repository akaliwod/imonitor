# Get Things Done in Automated Way

## Connector to Openshift

Make sure you have OpenShift Cluster ready with access [defined](https://github.com/datacenter/iserver/blob/main/doc/ocp/Access.md) in iserver

```
# iserver get ocp connector --cluster bm1

+---------+--------+------------+----------------+---------------+
| Cluster | Domain | Kubeconfig | SSH Public Key | Management IP |
+---------+--------+------------+----------------+---------------+
| bm1     | local  | ✓          | ✓              | 10.66.66.66   |
+---------+--------+------------+----------------+---------------+
```

## Connector to Intersight

Make sure Intersight account credentials are [defined](https://github.com/datacenter/iserver/blob/main/doc/intersight/README.md) in iserver

```
# iserver settings iaccounts get         

+-----------+------------------------------------------------+----------------+--------+
| iaccount  | key file                                       | server         | key id |
+-----------+------------------------------------------------+----------------+--------+
| iac       | C:\Users\user\.itool\iaccount\iac\key.pem      | intersight.com | *****  |
+-----------+------------------------------------------------+----------------+--------+
```

## Git clone

Git clone this repository, for example to your tmp directory

## Tasks file

Use the task below as the reference
- adjust the directory where you cloned this repo
- change iaccount and poller.suffix values
- chane the server name to the one available in the selected Intersight account

Note: if you want to get the nice outputs rather than emptish, select the server connected to Fabric Interconnect to get the Intersight metrics

```
[
    {
        "prometheus": {
           "user": {}
        }
    },
    {
        "iotel": {
            "instance": {
                "iaccount": "iac",
                "wipe": true
            },
            "poller": {
                "suffix": "iac",
                "dir": "C:\\tmp\\imonitor\\intersight-otel",
                "template": ["hw.*"],
                "target": ["server-name:my-server"]
            }
        }
    },
    {
        "grafana": {
            "operator": {},
            "instance": [
                {
                    "instance": "test",
                    "username": "user",
                    "password": "pass",
                    "prometheus": true,
                    "datasource": "k8s"
                }
            ],
            "dashboard": [
                {
                    "instance": "test",
                    "crd": ["C:\\tmp\\imonitor\\dashboard\\server"],
                    "scope": ["server-name:my-server"]
                }
            ]
        }
    }
]
```

## Run the tasks file

```
# iserver set ocp task --cluster bm1 --filename C:\tmp\task.json --no-confirm
```

Check the full output [here](./create_all.md) as a reference.

## Expected outcome

![Dashboards](./image/dashboards.png)

![HW Collection](./image/hw_collection.png)

## Destroy the setup

If you want to get rid of the entire setup, just run delete task

```
# iserver delete ocp task --cluster bm1 --filename C:\tmp\task.json --no-confirm
```

Check the full output [here](./delete_all.md) as a reference

[[Back]](./README.md)