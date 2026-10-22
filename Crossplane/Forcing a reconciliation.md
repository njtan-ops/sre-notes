```bash
kubectl annotate <kind> <name> \
  crossplane.io/reconcile-requested-at="$(date -u +'%Y-%m-%dT%H:%M:%SZ')" --overwrite
```

