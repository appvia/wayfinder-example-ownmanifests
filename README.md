# Example 2: Helm chart for Online Boutique

This directory contains files for deploying an Online Boutique application via its Helm Chart, into a Wayfinder managed Kubernetes Cluster.

## Prerequisites

1. Workspace is created and selected.
2. Cluster is created and available for use.
3. You have both the `wf` and `kubectl` CLIs installed and authenticated with the appvia Wayfinder POV instance.

## Deploy Online Boutique

There are 4 steps to deploying this application:

### Create a Wayfinder Application

Update the `app.yaml` to specify:
* The name of the application resource
* The `spec.key` and `spec.name` of the application resource

```sh
$ wf -w tandi apply -f app-appvia.yaml

◉ Processing file app-appvia.yaml...
◉ Applying resource Application/onlineboutique...
◉ Resource Application/onlineboutique does not exist, creating...
✔ Application/onlineboutique configured
```

### Create an Application Environment

Update the `appenv.yaml` to specify:
* The name of the application environment resource
* The name of the application that this relates to
* The name of the environment to create (e.g. `dev`, `test`, `staging`)
* The namespace within the Cluster to create (optional, omit this to have an auto-generated name)

```sh
$ wf -w tandi apply -f appenv-appvia.yaml

◉ Processing file appenv-appvia.yaml...
◉ Applying resource AppEnv/onlineboutique-dev...
◉ Resource AppEnv/onlineboutique-dev does not exist, creating...
✔ AppEnv/onlineboutique-dev configured
```

### Access the Application Environment

The AppEnv that was created is referencing an existing Cluster, and so a Namespace has been created in that Cluster for you. You can access this Namespace using the `wf access` command:

```sh
$ wf access env onlineboutique dev
? Which role would you like to use?
  ▸ namespace.admin
    namespace.edit
    namespace.troubleshooting
    namespace.view
    secrets.view

  Owner:    ws-tandi
  CLI Show: $ wf get role namespace.admin -o yaml

  Full namespace administration


◉ Waiting for access role binding to be ready.....
✔ Access to cluster bestest with role namespace.admin granted until: 11 May 23 23:26 BST
✔ Current kubectl context set to tandi.bestest and namespace set to onlineboutique-dev
```

### Deploy the Helm Chart

```sh
helm -n onlineboutique-dev upgrade --install onlineboutique ./onlineboutique -f ./onlineboutique/values.yaml -f values-appvia.yaml --set frontend.ingress.host="<domain>"
```

