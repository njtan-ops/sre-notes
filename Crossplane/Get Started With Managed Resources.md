This guide shows how to install and use a new kind of custom resource called `Bucket`. When a user calls the custom resource API to create a `Bucket`, Crossplane creates a bucket in AWS S3.

A `Bucket` managed resource looks like this:
```yaml
apiVersion: s3.aws.m.upbound.io/v1beta1
kind: Bucket
metadata:
  namespace: default
  name: crossplane-bucket-example
spec:
  forProvider:
    region: us-east-2
```

Things to note: 
- the apiVersion depends on the provider
- `metadata.namespace` is required because in v2, managed resources are now namespaced
- the spec has:
	- forProvider
		- maps to the parameters of the external resource.
	- initProvider
		- defines settings Crossplane applies only when creating a new managed resource.
	- managementPolicies
		- determine which actions Crossplane can take on a managed resource and its corresponding external resource.
		- determine what permissions Crossplane has over the resource.
	- providerConfigRef
		- tells the Provider which [ProviderConfig](http://localhost:1313/v2.4/packages/providers/#provider-configuration) to use when creating the managed resource.
	- writeConnectionSecretToRef
		- may generate resource-specific details, like usernames, passwords or connection details like an IP address.
			- so you can store these details to a secret 


Install the provider
```yaml
apiVersion: pkg.crossplane.io/v1
kind: Provider
metadata:
  name: crossplane-contrib-provider-aws-s3
spec:
  package: xpkg.crossplane.io/crossplane-contrib/provider-aws-s3:v2.0.0
```

```bash
kubectl apply -f provider.yaml
```

Create credentials
create `cred.ini`
```ini
[default]
aws_access_key_id = 
aws_secret_access_key = 
```
create secret from file
```bash
kubectl create secret generic aws-secret \
  --namespace=crossplane-system \
  --from-file=creds=./creds.ini
```

Configure the provider
```yaml
apiVersion: aws.m.upbound.io/v1beta1
kind: ClusterProviderConfig
metadata:
  name: default
spec:
  credentials:
    source: Secret
    secretRef:
      namespace: crossplane-system
      name: aws-secret
      key: creds
```

```bash
kubectl apply -f providerconfig.yaml
```

things to note:
- `providerconfig` schema differ per provider, see provider's documentation

create a managed resource
```yaml
apiVersion: s3.aws.m.upbound.io/v1beta1
kind: Bucket
metadata:
  namespace: default
  generateName: crossplane-bucket-
spec:
  forProvider:
    region: us-east-2
```

```bash
kubectl apply -f mr-bucket.yaml
```

```
kubectl get buckets.s3.aws.m.upbound.io
```

delete the managed resource
```bash
kubectl delete buckets.s3.aws.m.upbound.io crossplane-bucket-7tfcj
```

