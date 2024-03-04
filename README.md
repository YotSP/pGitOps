# pGitOps
# Guia de despliegue GitOps

The GitOps concept originated from [Weaveworks](https://www.weave.works/) back in 2017 and the goal was to automate the operations of a Kubernetes (K8s) system using a model external to the system as the source of truth ([History of GitOps](https://www.weave.works/blog/the-history-of-gitops)).

En este repositorio mostramos nuestro punto de vista de como implementar `GitOps` para administrar infraestructura, servicios y la capa de aplicaciones en sistemas basados en K8s. Los ejemplos están centrados alrededor de la plataforma [Red Hat OpenShift](https://cloud.redhat.com/learn/what-is-openshift) y el software de [IBM Cloud Paks](https://www.ibm.com/cloud/paks).

La arquitectura de referencia "Flujo de GitOps" se puede encontrar [Aqui](https://cloudnativetoolkit.dev/adopting/use-cases/gitops/gitops-ibm-cloud-paks/).


## Tabla de contenido
- [Guia de despliegue GitOps](#guia-de-despliegue-gitops)
  - [Tabla de contenido](#tabla-de-contenido)
  - [Pre-requisitos](#pre-requisitos)
    - [Red Hat OpenShift cluster](#red-hat-openshift-cluster)
    - [CLI tools](#cli-tools)
    - [IBM Entitlement Key](#ibm-entitlement-key)
  - [Setup git repositories](#setup-git-repositories)
    - [Tasks:](#tasks)
  - [Install and configure OpenShift GitOps](#install-and-configure-openshift-gitops)
    - [Tasks:](#tasks-1)
  - [Bootstrap the OpenShift cluster](#bootstrap-the-openshift-cluster)
    - [Tasks:](#tasks-2)
  - [Select resources to deploy](#select-resources-to-deploy)
    - [Tasks:](#tasks-3)


## Pre-requisitos

### Red Hat OpenShift cluster
- Es necesario un Cluster de OpenShift v4.7+.

### CLI tools
- Install the [git CLI](https://github.com/git-guides/install-git).
    - Configure your username for your Git commits - [link](https://docs.github.com/en/get-started/getting-started-with-git/setting-your-username-in-git).
    - Configure your email for your Git commits - [link](https://docs.github.com/en/account-and-profile/setting-up-and-managing-your-github-user-account/managing-email-preferences/setting-your-commit-email-address).
- Install the OpenShift CLI oc (version 4.7+) .  The binary can be downloaded from the Help menu from the OpenShift Console.
    <details>
    <summary>Download oc cli</summary>

    ![oc cli](doc/images/oc-cli.png)
    </details>
- Log in from a terminal window.
    ```bash
    oc login --token=<token> --server=<server>
    ```

### IBM Entitlement Key
- The `IBM Entitlement Key` is required to pull IBM Cloud Pak specific container images from the IBM Entitled Registry.  To get an entitlement key,

    1. Log in to [MyIBM Container Software Library](https://myibm.ibm.com/products-services/containerlibrary) with an IBMid and password associated with the entitled software.
    2. Select the **View library** option to verify your entitlement(s).
    3. Select the **Get entitlement key** to retrieve the key.

- A **Secret** containing the entitlement key is created in the `tools` namespace.

    ```bash
    oc new-project tools || true
    oc create secret docker-registry ibm-entitlement-key -n tools \
    --docker-username=cp \
    --docker-password="<entitlement_key>" \
    --docker-server=cp.icr.io
    ```

## Setup git repositories
- The following set of Git repositories will be used for our GitOps workflow.
    - Main GitOps repository ([https://github.com/cloud-native-toolkit/multi-tenancy-gitops](https://github.com/cloud-native-toolkit/multi-tenancy-gitops)): This repository contains all the ArgoCD Applications for  the `infrastructure`, `services` and `application` layers.  Each ArgoCD Application will reference a specific K8s resource (yaml resides in a separate git repository), contain the configuration of the K8s resource, and determine where it will be deployed into the cluster.
    - Infrastructure GitOps repository ([https://github.com/cloud-native-toolkit/multi-tenancy-gitops-infra](https://github.com/cloud-native-toolkit/multi-tenancy-gitops-infra)): Contains the YAMLs for cluster-wide and/or infrastructure related K8s resources managed by a cluster administrator.  This would include `namespaces`, `clusterroles`, `clusterrolebindings`, `machinesets` to name a few.
    - Services GitOps repository ([https://github.com/cloud-native-toolkit/multi-tenancy-gitops-services](https://github.com/cloud-native-toolkit/multi-tenancy-gitops-services)): Contains the YAMLs for K8s resources which will be used by the `application` layer.  This could include `subscriptions` for Operators, YAMLs of custom resources provided, or Helm Charts for tools provided by a third party.  These resource would usually be managed by the Administrator(s) and/or a DevOps team supporting application developers.


