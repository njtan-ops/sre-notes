```yaml
apiVersion: string
kind: string
metadata: object
	name: string
spec: object
	scope: string [Namespaced | Cluster]
	group: string
	names: object
		kind: string
		plural: string
	versions: # [[CRDVersionList]]
```
