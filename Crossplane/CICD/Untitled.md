| Command                              | Description                                                   |
| ------------------------------------ | ------------------------------------------------------------- |
| `pipeline { }`Copy                   | Top-level block for a Declarative Pipeline                    |
| `agent any`Copy                      | Run pipeline on any available agent                           |
| `agent none`Copy                     | No global agent — must define per stage                       |
| `agent { label 'linux' }`Copy        | Run on an agent with a specific label                         |
| `agent { node { label 'gpu' } }`Copy | Run on a node matching a label expression                     |
| `stages { }`Copy                     | Container for all stage blocks                                |
| `stage('Build') { steps { } }`Copy   | Define a named stage with steps                               |
| `steps { }`Copy                      | Container for the actual build steps within a stage           |
| `post { always { } }`Copy            | Post-build actions block                                      |
| `post { success { } }`Copy           | Run steps only on successful build                            |
| `post { failure { } }`Copy           | Run steps only on failed build                                |
| `post { unstable { } }`Copy          | Run steps when build is unstable                              |
| `post { cleanup { } }`Copy           | Run cleanup steps regardless of status (after all other post) |
| `post { changed { } }`Copy           | Run when build status differs from previous build             |