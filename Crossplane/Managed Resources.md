- Ready made k8s custom resources
- Each MR extends k8s with the ability to manage a new system
- Crossplane has extensive library of MRs 
	- No need to write a controller for custom resources, Crossplane has most probably already have an MR for that 
- Can use MR together with [[Composition]] to build custom resource APIs powered by MRs

```yaml
apiVersion: s3.aws.m.upbound.io/v1beta1
kind: Bucket
metadata:
  namespace: default
  generateName: my-bucket
spec:
  forProvider:
    region: us-east-2
```

- Namespaced MRs work great with or without composition.
- 