Conducted checks
----------------

## OpenShift Components
Task file: `tasks/verify_openshift_components.yml`

|Check                                                                    |Expected result        |
|-------------------------------------------------------------------------|-----------------------|
|Does node have memory pressure ?                                         |False                  |
|Does node have disk pressure ?                                           |False                  |
|Does node have PID pressure ?                                            |False                  |
|Is node ready ?                                                          |True                   |
|Is any cluster operator in progressing state ?                           |False                  |
|Is any cluster operator in degraded state ?                              |False                  |
|Are there any pod that are not in completed, running or succeeded state ?|0 pods                 |
|Is there any pod with high restart count ?                               |0 pods with more than 3|
|Is there any pending CSR ?                                               |0 pending CSR          |
|Is there any PDB preventing node from being drained ?                    |0 malformed pdb        |

## Prometheus Alerts
Task file: `tasks/verify_prometheus_alerts.yml`

|Check                                                              |Expected result|
|-------------------------------------------------------------------|---------------|
|Are there any alerts from the default/prometheus_alert.yml firing ?|False          |

## Resources consumption
Task file: `tasks/verify_resources_consumption.yml`

|Check                                               |Expected result|
|----------------------------------------------------|---------------|
|How much CPU is currently consumed on each node ?   |<80% - Variabilized |
|How much memory is currently consumed on each node ?|<80% - Variabilized |
|How much disk is currently consumed on each node ?  |<80% - Variabilized |

## API Deprecation
Task file: `tasks/verify_api_deprecation.yml`

|Check                                |Expected result                            |
|-------------------------------------|-------------------------------------------|
|Is there any API that is deprecated ?|No custom program is using a deprecated API|

## etcd performance
Task file: `tasks/verify_etcd_performance.yml`

|Check                                                                  |Expected result|
|-----------------------------------------------------------------------|---------------|
|Is etcd `backend_commit` duration between recommended thresholds ?     |True           |
|Is etcd `disk_wal_fsync` duration between recommended thresholds ?     |True           |
|Is etcd hosts `cpu_io_wait` between recommended thresholds ?           |True           |
|Is etcd `network_peer_round_trip_time` between recommended thresholds ?|True           |

