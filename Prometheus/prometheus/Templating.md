
- [[Templating Examples]]
- [[Template Reference]]
- Templating language is based on Go templating system

- Label and annotation values can be templated using [console templates](http://localhost:3000/docs/visualization/consoles/). 
- The `$labels` variable holds the label key/value pairs of an alert instance. 
- The configured external labels can be accessed via the `$externalLabels` variable. 
- The `$value` variable holds the evaluated value of an alert instance.

```yaml
# To insert a firing element's label values:
{{ $labels.<labelname> }}
# To insert the numeric expression value of the firing element:
{{ $value }}
```

Examples:

```yaml
groups:
- name: example
  rules:

  # Alert for any instance that is unreachable for >5 minutes.
  - alert: InstanceDown
    expr: up == 0
    for: 5m
    labels:
      severity: page
    annotations:
      summary: "Instance {{ $labels.instance }} down"
      description: "{{ $labels.instance }} of job {{ $labels.job }} has been down for more than 5 minutes."

  # Alert for any instance that has a median request latency >1s.
  - alert: APIHighRequestLatency
    expr: api_http_request_latencies_second{quantile="0.5"} > 1
    for: 10m
    annotations:
      summary: "High request latency on {{ $labels.instance }}"
      description: "{{ $labels.instance }} has a median request latency above 1s (current value: {{ $value }}s)"
```

