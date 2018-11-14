# custom-database-controller

Example respository for the blog post [
Kubernetes Custom Resources Grow Up in v1.10](https://blog.openshift.com/kubernetes-custom-resources-grow-up-in-v1-10/).

This repository implements a simple custom database controller which scales `mysql` deployments as per the `Database` custom resources.

This follows the same pattern as that of the Kuberentes [sample-controller](https://github.com/kubernetes/sample-controller).

## Installation

```
export GOPATH=~/go
go get github.com/nikhita/custom-database-controller
```

## Prerequisites

Custom Resources support `/status` and `/scale` subresources as an
[alpha feature](https://kubernetes.io/docs/tasks/access-kubernetes-api/extend-api-custom-resource-definitions/#subresources) in v1.10. You will need a Kubernetes cluster with version of at least 1.10.
Enable this feature using the `CustomResourceSubresources` feature gate on the [kube-apiserver](https://kubernetes.io/docs/admin/kube-apiserver) for v1.10.
This feature is enabled by default for versions >v1.10.

```sh
--feature-gates=CustomResourceSubresources=true
```

## Running

```sh
# assumes you have a working kubeconfig, not required if operating in-cluster
$ go run *.go -kubeconfig=$HOME/.kube/config

# create a CustomResourceDefinition
$ kubectl create -f artifacts/databases-crd.yaml

# create a custom resource of type Database
$ kubectl create -f artifacts/mysql-database.yaml

# check deployments created through the Databse custom resource
$ kubectl get deployments
```

You can scale the `Database` custom resource, which can automatically scale the `mysql` deployments as well.

```sh
$ kubectl get deployments
NAME    DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
my-user 1         1         1            1           4s

# scaling
$ kubectl scale --replicas=3 databases/mysql
database.example.com "mysql" scaled

# after scaling
$ kubectl get deployments
NAME    DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
my-user 3         3         3            3           1m
```

