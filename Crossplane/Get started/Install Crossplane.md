- Installing Crossplane enables the installation of Crossplane _Provider_, _Function_, and _Configuration_ resources.
- Prerequisites:
	- actively supported Kubernetes version
		- this means the 3 latest kubernetes versions
	-  [Helm](https://helm.sh/docs/intro/install/) version `v3.2.0` or later

Installation: 

```bash
helm repo add crossplane-stable https://charts.crossplane.io/stable
helm repo update
```


```bash
helm install crossplane \
--namespace crossplane-system \
--create-namespace crossplane-stable/crossplane
```


Verification:
 ```bash
 kubectl get pods -n crossplane-system
 ```
- Both pods; crossplane and crossplane-rbac-manager should be ready and running status