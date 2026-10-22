This guide shows how to create a new kind of custom resource named `App`. When a user calls the custom resource API to create an `App`, Crossplane creates a `Deployment` and a `Service`.

App XR looks like this:
```yaml
apiVersion: example.crossplane.io/v1
kind: App
metadata:
  namespace: default
  name: my-app
spec:
  image: nginx
status:
  replicas: 2  # Copied from the Deployment's status
  address: 10.0.0.1  # Copied from the Service's status
```

When users create an `App` Crossplane creates this `Deployment` and `Service`:
```yaml
---
apiVersion: apps/v1
kind: Deployment
metadata:
  namespace: default
  name: my-app-dhj3a
  labels:
    example.crossplane.io/app: my-app  # Copied from the App's name
spec:
  replicas: 2
  selector:
    matchLabels:
      example.crossplane.io/app: my-app  # Copied from the App's name
  template:
    metadata:
      labels:
        example.crossplane.io/app: my-app  # Copied from the App's name
    spec:
      containers:
      - name: app
        image: nginx  # Copied from the App's spec
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  namespace: default
  name: my-app-03mda
  labels:
    example.crossplane.io/app: my-app  # Copied from the App's name
spec:
  selector:
    example.crossplane.io/app: my-app  # Copied from the App's name
  ports:
  - protocol: TCP
    port: 8080
    targetPort: 80

```


Define the Schema
```yaml
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
    served: true
    referenceable: true
    schema:
     openAPIV3Schema:
       type: object
       properties:
        spec:
          type: object
          properties:
            image:
              description: The app's OCI container image.
              type: string
          required:
          - image
        status:
          type: object
          properties:
            replicas:
              description: The number of available app replicas.
              type: integer
            address:
              description: The app's IP address.
              type: string
```

```bash
kubectl apply -f xrd.yaml
```

Things to note:
- see [[XRD]] for the full spec
- this xrd only defines the schema, its the blueprint for the composition and functions
- see `kubectl api-resources --api-group=apiextensions.crossplane.io`
	- you will see the `xrd`, `composition`, `mrd`
		- these have its own revision crds except xrd
	- theres also the `environmentconfig`, `mrap` and `usage`

install the function: 
```yaml
apiVersion: pkg.crossplane.io/v1
kind: Function
metadata:
  name: crossplane-contrib-function-go-templating
spec:
  package: xpkg.crossplane.io/crossplane-contrib/function-go-templating:v0.9.2
```

```bash
kubectly apply -f function.yaml
```

things to note:
- see `kubectl api-resources --api-group=pkg.crossplane.io`
	- this has the `configurations`, `functions`, `providers` with its corresponding `revisions`
	- theres also the `imageconfigs`, `locks`, `deploymentruntimeconfigs`
		- `deploymentruntimeconfigs` can be useful for setting the max reconcilations
	- these resources are the ones you can download from an OCI registry
		- when you hear OCI, yes it will create a deployment
			- deployment replicas is set to 1 but can be modified using `deploymentruntimeconfig` 
				- any other configurations including taints and tolerations can be set in this crd 
- see [[PKG]] spec
- name convention is `<owner>-<function_name>`
- this also wont be enough to create the composed resources, you need composition to call this function

configure the composition: 
```yaml
apiVersion: apiextensions.crossplane.io/v1
kind: Composition
metadata:
  name: app-templated-yaml
spec:
  compositeTypeRef:
    apiVersion: example.crossplane.io/v1
    kind: App
  mode: Pipeline
  pipeline:
  - step: create-deployment-and-service
    functionRef:
      name: crossplane-contrib-function-go-templating
    input:
      apiVersion: gotemplating.fn.crossplane.io/v1beta1
      kind: GoTemplate
      source: Inline
      inline:
        template: |
          ---
          apiVersion: apps/v1
          kind: Deployment
          metadata:
            annotations:
              gotemplating.fn.crossplane.io/composition-resource-name: deployment
              {{ if eq (.observed.resources.deployment | getResourceCondition "Available").Status "True" }}
              gotemplating.fn.crossplane.io/ready: "True"
              {{ end }}
            labels:
              example.crossplane.io/app: {{ .observed.composite.resource.metadata.name }}
          spec:
            replicas: 2
            selector:
              matchLabels:
                example.crossplane.io/app: {{ .observed.composite.resource.metadata.name }}
            template:
              metadata:
                labels:
                  example.crossplane.io/app: {{ .observed.composite.resource.metadata.name }}
              spec:
                containers:
                - name: app
                  image: {{ .observed.composite.resource.spec.image }}
                  ports:
                  - containerPort: 80
          ---
          apiVersion: v1
          kind: Service
          metadata:
            annotations:
              gotemplating.fn.crossplane.io/composition-resource-name: service
              {{ if (get (getComposedResource . "service").spec "clusterIP") }}
              gotemplating.fn.crossplane.io/ready: "True"
              {{ end }}
            labels:
              example.crossplane.io/app: {{ .observed.composite.resource.metadata.name }}
          spec:
            selector:
              example.crossplane.io/app: {{ .observed.composite.resource.metadata.name }}
            ports:
            - protocol: TCP
              port: 8080
              targetPort: 80
          ---
          apiVersion: example.crossplane.io/v1
          kind: App
          status:
            replicas: {{ get (getComposedResource . "deployment").status "availableReplicas" | default 0 }}
            address: {{ get (getComposedResource . "service").spec "clusterIP" | default "" | quote }}

```

things to note:
- see [[Composition(Spec)]] for spec
- this is where you put define the steps to create the XR
- select the xrd in `spec.compositeTypeRef`
- some functions need input 
	- dont confuse it as a k8s crd, the input is processed inside the function container, not by the k8s cluster or another controller 

create XR
```yaml
apiVersion: example.crossplane.io/v1
kind: App
metadata:
  namespace: default
  name: my-app
spec:
  image: nginx
```

things to note:
- as you can see, comparing to the first XR at the top, it only has image
- if you want to update the xrd, you need to do this in order:
	- edit the xrd
	- edit the composition or function (depends)
			- this will create compref or funcrev which is automatically created 
	- 