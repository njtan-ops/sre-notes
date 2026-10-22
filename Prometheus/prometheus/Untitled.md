```yaml
tasks:
  success_rate:
    panel_title: "Success Rate %"
    expr: '100 * sum(rate(http_requests_total{status="200"}[5m])) / sum(rate(http_requests_total[5m]))'
    comparison_labels: []
    tolerance: 0.15 # rate() over a live, randomly-driven counter jitters; 15% relative
    expected_panel_type: null # any panel type is fine, just needs the right expr

  median_latency:
    panel_title: "Median Latency"
    expr: "histogram_quantile(0.50, sum(rate(http_request_duration_seconds_bucket[5m])) by (le))"
    comparison_labels: []
    tolerance: 0.20
    expected_panel_type: null

  error_rate_by_path:
    # Task 1 filters on status="200" (success). This task filters on non-2xx
    # (404s and 500s) instead, so a student can't reuse Task 1's filter here
    # and coincidentally pass -- it's a different value, not just different
    # syntax. Combines that filter with the original Requests-by-Path grouping.
    panel_title: "Error Rate by Path"
    expr: 'sum(rate(http_requests_total{status!~"2.."}[5m])) by (path)'
    comparison_labels:
      - "path"
    tolerance: 0.25 # filtering to just errors shrinks the sample per path -> noisier than the unfiltered version
    expected_panel_type: null # visualization isn't graded here -- Task 4 (Queue Depth) covers that

  queue_depth_gauge:
    panel_title: "Queue Depth"
    expr: "app_queue_depth"
    comparison_labels: []
    tolerance: 0.05
    expected_panel_type: "gauge"
    expected_min: 0
    expected_max: 100
    expected_description: "Background job queue depth"
    expected_thresholds:
      - color: "green"
        value: null
      - color: "yellow"
        value: 60
      - color: "red"
        value: 80

  silence_noisy_alert:
    # target_labels/decoy_labels are each alert's real label set (from
    # grafana-provisioning/alerting/rules.yaml). The checker replays
    # Alertmanager's own matcher evaluation against these, so any silence
    # that genuinely does (or doesn't) match -- by alertname, by severity,
    # by any combination -- is judged correctly, not just ones that happen
    # to mention "alertname" explicitly.
    target_labels:
      alertname: "HighCPUTemperature"
      severity: "warning"
    decoy_labels:
      alertname: "DatabaseConnectionPoolExhausted"
      severity: "critical"
    max_duration_hours: 6 # reject absurdly long/permanent silences

  fix_broken_alert:
    # The alert's query (refId A) has a bad label matcher so it returns no
    # data and the alert can never fire, even though disk is critically low
    # and the downstream threshold condition (< 20) is already correct.
    # Student must fix the *query expression*, not the threshold.
    alertname: "LowDiskSpace"
    broken_expr: 'disk_free_percent{job="nonexistent-job"}'
    reference_expr: "disk_free_percent"
    comparison_labels: []
    tolerance: 0.10
    expect_firing: true # once the expr is fixed, real disk_free_percent (~15) is < 20 -> should fire
```