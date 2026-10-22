Composition Functions allow the use of these languages: ^languages
- YAML
- KCL
- Python
- Golang


- Premise of Composition ^premise
	- Avoid writing controllers manually for your Custom Resource Definitions (CRD)
	- Configure a pipeline of functions
		- Functions return declarative configurations that Crossplane should apply 
	- Focus on expressing business logic and work in preferred language ([[Composition#^languages]])
	- 


- You must grant Crossplane access to compose resources that aren’t Crossplane resources like MRs or XRs.
	- Read [the composition documentation](https://docs.crossplane.io/latest/composition/compositions/#grant-access-to-composed-resources) to learn how to grant Crossplane access.