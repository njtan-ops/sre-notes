- **Scenario 1** — wire a table-scoped IAM Policy: relay a value up into the XR's status, then use it to build a brand-new `Policy` scoped to it, the same two-hop pattern `app-bucket`/`app-policy` uses, applied to a resource that actually reaches `Ready`.
```yaml
- name: app-table
            base:
              apiVersion: dynamodb.aws.m.upbound.io/v1beta1
              kind: Table
              spec:
                forProvider:
                  billingMode: PAY_PER_REQUEST
                  hashKey: id
                  attribute:
                    - name: id
                      type: S
            patches:
              - type: FromCompositeFieldPath
                fromFieldPath: spec.region
                toFieldPath: spec.forProvider.region
              - type: FromCompositeFieldPath
                fromFieldPath: spec.namePrefix
                toFieldPath: metadata.name
                transforms:
                  - type: string
                    string:
                      type: Format
                      fmt: "%s-table"
              # --- ADD THIS: Relay table ARN up to XR status ---
              - type: ToCompositeFieldPath
                fromFieldPath: status.atProvider.arn
                toFieldPath: status.tableArn
            readinessChecks:
              - type: MatchCondition
                matchCondition:
                  type: Ready
                  status: "True"
```
```yaml
- name: app-table-policy
            base:
              apiVersion: iam.aws.m.upbound.io/v1beta1
              kind: Policy
              spec:
                forProvider: {}
            patches:
              - type: FromCompositeFieldPath
                fromFieldPath: spec.namePrefix
                toFieldPath: metadata.name
                transforms:
                  - type: string
                    string:
                      type: Format
                      fmt: "%s-table-access"
              - type: FromCompositeFieldPath
                fromFieldPath: status.tableArn
                toFieldPath: spec.forProvider.policy
                policy:
                  fromFieldPath: Required
                transforms:
                  - type: string
                    string:
                      type: Format
                      fmt: '{"Version":"2012-10-17","Statement":[{"Effect":"Allow","Action":["dynamodb:GetItem","dynamodb:PutItem"],"Resource":"%s"}]}'
```

```yaml
status:
  type: object
  properties:
    # ... existing status fields (e.g., bucketArn, securityGroupId) ...
    tableArn:
      type: string
      description: The ARN of the DynamoDB table.
```

- **Scenario 2** — consume an EnvironmentConfig on a healthy resource: the same `FromEnvironmentFieldPath` mechanism the Lab Activity demonstrates in Trigger a Composition Revision, this time on a resource where you'll actually see it land.

```yaml
- name: app-security-group
            base:
              apiVersion: ec2.aws.m.upbound.io/v1beta1
              kind: SecurityGroup
              spec:
                forProvider:
                  description: Lab simple-app security group
            patches:
              - type: FromCompositeFieldPath
                fromFieldPath: spec.region
                toFieldPath: spec.forProvider.region
              - type: FromCompositeFieldPath
                fromFieldPath: spec.namePrefix
                toFieldPath: metadata.name
                transforms:
                  - type: string
                    string:
                      type: Format
                      fmt: "%s-sg"
              - type: ToCompositeFieldPath
                fromFieldPath: status.atProvider.id
                toFieldPath: status.securityGroupId
              # --- ADD THIS: Consume costCenter from EnvironmentConfig ---
              - type: FromEnvironmentFieldPath
                fromFieldPath: costCenter
                toFieldPath: spec.forProvider.tags.costCenter
```



- **Scenario 3** — compose a Kubernetes Secret: a new composed resource that isn't a cloud resource at all, proving v2's "compose any resource" capability.

```yaml
- name: app-secret
            base:
              apiVersion: kubernetes.m.crossplane.io/v1alpha1
              kind: Object
              spec:
                forProvider:
                  manifest:
                    apiVersion: v1
                    kind: Secret
                    metadata:
                      namespace: default
                    type: Opaque
                    stringData: {}
                providerConfigRef:
                  kind: ClusterProviderConfig
                  name: default
            patches:
              - type: FromCompositeFieldPath
                fromFieldPath: spec.namePrefix
                toFieldPath: spec.forProvider.manifest.metadata.name
                transforms:
                  - type: string
                    string:
                      type: Format
                      fmt: "%s-secret"
              - type: FromCompositeFieldPath
                fromFieldPath: spec.namePrefix
                toFieldPath: spec.forProvider.manifest.stringData.tableName
                transforms:
                  - type: string
                    string:
                      type: Format
                      fmt: "%s-table"
```