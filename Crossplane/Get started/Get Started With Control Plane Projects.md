This guide shows how to use the Crossplane CLI to build a platform API from scratch. You create a _project_, define a new `WebApp` custom resource, and configure how Crossplane composes it. When a user creates a `WebApp`, Crossplane creates a Kubernetes `Deployment` and a `Service`.

A `WebApp` custom resource looks like this:
```yaml
apiVersion: platform.example.com/v1alpha1
kind: WebApp
metadata:
  name: podinfo
  namespace: default
spec:
  image: docker.io/stefanprodan/podinfo:6.11.0
  replicas: 3
  ports: [9898]
```

When a user creates a `WebApp`, Crossplane creates a `Deployment` and a `Service`:

```yaml
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: podinfo
  namespace: default
  labels:
    app.kubernetes.io/name: podinfo  # Copied from the WebApp's name
spec:
  replicas: 3  # Copied from the WebApp's spec
  selector:
    matchLabels:
      app.kubernetes.io/name: podinfo
  template:
    metadata:
      labels:
        app.kubernetes.io/name: podinfo
    spec:
      containers:
      - name: podinfo
        image: docker.io/stefanprodan/podinfo:6.11.0  # Copied from the WebApp's spec
        ports:
        - containerPort: 9898  # Copied from the WebApp's spec
---
apiVersion: v1
kind: Service
metadata:
  name: podinfo
  namespace: default
spec:
  selector:
    app.kubernetes.io/name: podinfo
  ports:
  - protocol: TCP
    port: 9898
    targetPort: 9898
```

Create the project
```bash
crossplane project init example-project-webapp
```

- The `crossplane-project.yaml` file contains metadata about your project.
	- This metadata influences how the Crossplane CLI builds your project into [Crossplane packages](https://docs.crossplane.io/latest/packages/).
- `spec.repository` in `crossplane-project.yaml` is an OCI repository, not the code repo, when you run `crossplane project build`, it will generate a `xpkg` (Crossplane package)


- Kubernetes calls user-defined API resources _custom resources_.
- Crossplane calls user-defined API resources that use composition _composite resources_.
- A composite resource is a kind of custom resource.

The generated `crossplane-project.yaml` :
```yaml
apiVersion: dev.crossplane.io/v1alpha1
kind: Project
metadata:
  name: example-project-webapp
spec:
  repository: example.com/my-org/example-project-webapp
```

````tabs
tab: Using XR
Create an xr at `examples/webapps/podinfo.yaml`

```yaml
apiVersion: platform.example.com/v1alpha1
kind: WebApp
metadata:
  name: podinfo
  namespace: default
spec:
  image: docker.io/stefanprodan/podinfo:6.11.0
  replicas: 3
  ports: [9898]

```

Generate an xrd from xr 

```bash
crossplane xrd generate examples/webapps/podinfo.yaml --from xr
```

tab: Using SimpleSchema
Create SimpleSchema document at `apis/webapps/schema.yaml`

```yaml
apiVersion: platform.example.com/v1alpha1
kind: WebApp
spec:
  image: string | required=true description="OCI image for the webapp"
  replicas: integer | default=1 minimum=1 maximum=100 description="Number of replicas to run"
  ports: "[]integer | default=[80] description=\"Ports to expose from the application container\""
```

Generate xrd from simpleschema 

```bash
crossplane xrd generate apis/webapps/schema.yaml --from simpleschema
```
````
Things to note:
- apiVersion is customizable 
- kind will be pluralized (e.g. `WebApp` to webapps)
- the pluralized name becomes the prefix to `metadata.name` from the base `apiVersion`
	- e.g `Kind: WebApp` and `apiVersion: platform.example.com/v1alpha1` becomes `metadata.name: webapps.platform.example.com`
- `spec.group` is also derived from `apiVersion`
- `spec.scope` defaults to `Namespaced` but it can also be `Cluster`
- `spec.versions[x].name` is derived from `apiVersion` after the `/` part 
- `spec.versions[x].schema.openAPIV3Schema` is derived from the XR/SimpleSchema `spec` specifically on the `properties.spec.properties`  at `openAPIV3Schema`
- see [[XRD]] for spec 
- When creating an XR, the `apiVersion` depends on XRD's `spec.group` and `spec.versions[x].name` (x signifies the selected version index)

The output should be something like this, located at `apis/webapps/definition.yaml` :
```yaml
apiVersion: apiextensions.crossplane.io/v2
kind: CompositeResourceDefinition
metadata:
  name: webapps.platform.example.com
spec:
  group: platform.example.com
  names:
    categories:
    - crossplane
    kind: WebApp
    plural: webapps
  scope: Namespaced
  versions:
  - name: v1alpha1
    referenceable: true
    schema:
      openAPIV3Schema:
        description: WebApp is the Schema for the WebApp API.
        properties:
          spec:
            description: WebAppSpec defines the desired state of WebApp.
            properties:
              image:
                type: string
              ports:
                items:
                  type: number
                type: array
              replicas:
                type: number
            type: object
          status:
            description: WebAppStatus defines the observed state of WebApp.
            type: object
        required:
        - spec
        type: object
    served: true
```

Generate composition from xrd 
```bash
crossplane composition generate apis/webapps/definition.yaml
```

If it fails because docker it cannot connect to docker:
```bash
docker context inspect --format '{{.Endpoints.docker.Host}}'

export DOCKER_HOST=$(docker context inspect --format '{{.Endpoints.docker.Host}}')
```

Things to note: 
- `metadata.name` is the same 
- `spec.compositeTypeRef` is derived from xr/simpleschema `apiVersion` and `kind` or related fields in xrd
	- this selects the xrd the composition references from
- `spec.mode` is by default set to `Pipeline` but can be `Resources` (This is legacy / Patch & Transform)
- `spec.pipeline` is the list of steps; each `step` has `functionRef.name` to link to a function file 

The command writes the composition to `apis/webapps/composition.yaml`:
```yaml
apiVersion: apiextensions.crossplane.io/v1
kind: Composition
metadata:
  name: webapps.platform.example.com
spec:
  compositeTypeRef:
    apiVersion: platform.example.com/v1alpha1
    kind: WebApp
  mode: Pipeline
  pipeline:
  - functionRef:
      name: crossplane-contrib-function-auto-ready
    step: crossplane-contrib-function-auto-ready
```

The command also updates the `crossplane-project.yaml`
```yaml hl:6-12
apiVersion: dev.crossplane.io/v1alpha1
kind: Project
metadata:
  name: example-project-webapp
spec:
  dependencies:
  - type: xpkg
    xpkg:
      apiVersion: pkg.crossplane.io/v1
      kind: Function
      package: xpkg.crossplane.io/crossplane-contrib/function-auto-ready
      version: '>=v0.0.0'
  repository: example.com/my-org/example-project-webapp
```

Add dependencies
```bash
crossplane dependency add k8s:v1.35.0
```

This updates the `crossplane-project.yaml`:
```yaml hl:13-16
apiVersion: dev.crossplane.io/v1alpha1
kind: Project
metadata:
  name: example-project-webapp
spec:
  dependencies:
  - type: xpkg
    xpkg:
      apiVersion: pkg.crossplane.io/v1
      kind: Function
      package: xpkg.crossplane.io/crossplane-contrib/function-auto-ready
      version: '>=v0.0.0'
  - k8s:
      version: v1.35.0
    type: k8s
  repository: example.com/my-org/example-project-webapp
```

````tabs
tab: Templated YAML
Generate a templated YAML function named compose-webapp and add it to the composition’s pipeline:
```bash
crossplane function generate compose-webapp apis/webapps/composition.yaml --language go-templating
```

this will create the file `functions/compose-webapp/00-prelude.yaml.gotmpl`
```yaml
# Get the observed composite resource into a variable. This can be used in any
# subsequent templates.
{{ $xr := getCompositeResource . }}
```
this will create the file `functions/compose-webapp/01-compose.yaml.gotmpl`
```yaml
# code: language=yaml
# yaml-language-server: $schema=../../schemas/json/index.schema.json

# Write your composed resources here. You can use any of the features described
# in this documentation:
# https://github.com/crossplane-contrib/function-go-templating?tab=readme-ov-file#using-this-function
#
# Example:
#
# ---
# apiVersion: nop.crossplane.io/v1alpha1
# kind: NopResource
# metadata:
#   annotations:
#     {{ setResourceNameAnnotation "example" }}
# spec:
#   forProvider:
#     conditionAfter:
#       - conditionType: Ready
#         conditionStatus: "True"
#         time: 5s
```
Edit the file: 
```yaml
# code: language=yaml
# yaml-language-server: $schema=../../schemas/json/index.schema.json

---
apiVersion: apps/v1
kind: Deployment
metadata:
  annotations:
    gotemplating.fn.crossplane.io/composition-resource-name: deployment
  name: {{ $xr.metadata.name }}
  namespace: {{ $xr.metadata.namespace }}
  labels:
    app.kubernetes.io/name: {{ $xr.metadata.name }}
spec:
  replicas: {{ $xr.spec.replicas }}
  selector:
    matchLabels:
      app.kubernetes.io/name: {{ $xr.metadata.name }}
  template:
    metadata:
      labels:
        app.kubernetes.io/name: {{ $xr.metadata.name }}
    spec:
      containers:
      - name: {{ $xr.metadata.name }}
        image: {{ $xr.spec.image }}
        ports:
#{{- range $p := $xr.spec.ports }}
        - containerPort: {{ $p }}
#{{- end }}

---
apiVersion: v1
kind: Service
metadata:
  annotations:
    gotemplating.fn.crossplane.io/composition-resource-name: service
  name: {{ $xr.metadata.name }}
  namespace: {{ $xr.metadata.namespace }}
  labels:
    app.kubernetes.io/name: {{ $xr.metadata.name }}
spec:
  selector:
    app.kubernetes.io/name: {{ $xr.metadata.name }}
  ports:
#{{- range $p := $xr.spec.ports }}
  - protocol: TCP
    port: {{ $p }}
    targetPort: {{ $p }}
#{{- end }}
```
````
The generate command also appends to the `composition.yaml`
```yaml hl:11-13
apiVersion: apiextensions.crossplane.io/v1
kind: Composition
metadata:
  name: webapps.platform.example.com
spec:
  compositeTypeRef:
    apiVersion: platform.example.com/v1alpha1
    kind: WebApp
  mode: Pipeline
  pipeline:
  - functionRef:
      name: my-org-example-project-webappcompose-webapp
    step: compose-webapp
  - functionRef:
      name: crossplane-contrib-function-auto-ready
    step: crossplane-contrib-function-auto-ready
```

Things to note:
- the `$schema` points to a file inside the crossplane project in `./schema`
- the `my-org-example-project-webapp` prefix in the `functionRef.name` comes from the `crossplane-project.yaml`'s `spec.repository`
- the `metadata.annotation` `gotemplating.fn.crossplane.io/composition-resource-name` is required for crossplane as a stable resource identifier
- in Go templating, codes are wrapped in `{{}}`
	- `#{{- <code here> }}`
		- the `#{{-` makes it so that it will not render an empty line
		- the `#` in `#{{-` makes it so that the YAML wont flag it as invalid yaml syntax since thats a comment in yaml but go will still consider it a code 


Render the composition
```bash
crossplane composition render examples/webapps/podinfo.yaml apis/webapps/composition.yaml
```
