```
apiVersion: example.crossplane.io/v1
kind: App
metadata:
  namespace: default
  name: my-app
spec:
  image: nginx
  crossplane: 
    compositionRef:
      name: app-kcl
    compositionRevisionRef:
      name: app-kcl-41b6efe
    resourceRefs:
    - apiVersion: apps/v1
      kind: Deployment
      name: my-app-9bj8j
    - apiVersion: v1
      kind: Service
      name: my-app-bflc4

```
- `spec.crossplane` is the internal tracking field for crossplane
	- `spec.crossplane.resourceRefs` references the actual underlying resources ([[composed resources]])

see [[Composite Resource Definitions (XRDs)]]



