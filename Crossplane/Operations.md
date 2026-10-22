- Crossplane v2 introduces Operations
- Run operational tasks using function pipelines ^function-pipelines
- MR and Composition are focused on creating managing infrastructure
	- Operations handle tasks that don’t fit the typical resource creation pattern - like certificate monitoring, rolling upgrades, or scheduled maintenance.
- Operations run function pipelines to completion like a Kubernetes Job
- Three modes:
	- Operation
		- run once to completion
	- CronOperation
		- run on a schedule
	- WatchOperation
		- run when resources change


```yaml
apiVersion: ops.crossplane.io/v1alpha1
kind: CronOperation
metadata:
  name: cert-monitor
spec:
  schedule: "0 6 * * *"  # Daily at 6 AM
  mode: Pipeline
  pipeline:
  - step: check-certificates
    functionRef:
      name: crossplane-contrib-function-python
    # function checks SSL certificates and reports status
```

