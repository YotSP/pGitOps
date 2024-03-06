# Namespace helm chart

Helm chart to create a namespace with RBAC necessary to allow ArgoCD to operate within the namespace.

The chart will create the namespace using the release name provided for the helm execution.

## Values

| Name                                 | Description                                                                          | Default            |
|--------------------------------------|--------------------------------------------------------------------------------------|--------------------|
| **argocdNamespace**                  | The namespace where ArgoCD has been installed. This value is used for the rbac rules | `openshift-gitops` |
| **gitopsConfig.applicationBasePath** | The base path in the gitops repo where application configuration should be installed | `""`               | 
| **gitopsConfig.host**                | The host name of the gitops repo                                                     | `""`               |
| **gitopsConfig.org**                 | The org of the gitops repo                                                           | `""`               |
| **gitopsConfig.repo**                | The repo name for the gitops repo                                                    | `""`               |
| **gitopsConfig.repo**                | The branch for the gitops repo                                                       | `main`             |