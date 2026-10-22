```yaml
groups:
- name: example
  rules:
  - alert: HighRequestLatency
    expr: job:request_latency_seconds:mean5m{job="myjob"} > 0.5
    for: 10m
    labels:
      severity: page
    annotations:
      summary: High request latency
```

- Optionals
	- `for` 
	- `labels` 
		- set of additional labels to be attached to the alert
		- can be templated\*
	- `annotations` 
		- specifies a set of informational labels
		- can be templated\*

- \*
	- templated - See [[Templating]]