```
apiVersion: apiextensions.crossplane.io/v2
kind: CompositeResourceDefinition
metadata:
  name: apps.example.crossplane.io
spec:
  scope: Namespaced
  group: example.crossplane.io
  names:
    kind: App
    plural: apps
  versions:
  - name: v1
```

- Composite resource definitions (XRDs) now have a `scope` field. The `scope` field defaults to `Namespaced` in the new v2 version of the XRD API.
- You can also set the `scope` field to `Cluster` to create a cluster scoped XR.
- All `schemas` follow the Kubernetes custom resource definition [OpenAPIv3 structural schema](https://kubernetes.io/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/#specifying-a-structural-schema).
- Spec: [[XRD]]

