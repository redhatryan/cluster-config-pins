### Introduction

This repository is used to manage commit pins to enable promotion of changes across clusters. The repo
retrieves the application objects from the cluster-config repository and then patches them to use
specific commit hashes or tags to "pin" the application to a specific revision.

To manage promotions the pins can be updated on a cluster-by-cluster basis to manage the revisions.

### How it Works

We are using ACM to manage the bootstrap process for new clusters, ACM has a policy that deploys
the OpenShift GitOps operator plus a bootstrap application for a specific cluster. This bootstrap
application points to a cluster specific folder under `bootstrap/overlays` that contains the commit
pins for that cluster.

The kustomization for each cluster loads the configuration from the matching path in the `cluster-config`
repository, using a kustomize remote reference, and patches the target revision in the Argo CD Applications
for specific pins. In the `cluster-config` repo we label the applications with a `repo` label so we can
have kustomize match the pins on a per repo basis.

Diagramming the flow, it looks like this:

    ACM Policy (acm-hub-bootstrap HEAD)
         ↓
    Cluster Config Pins (cluster-config-pins HEAD)
         ↓
    Cluster Config (cluster-config TAG)
