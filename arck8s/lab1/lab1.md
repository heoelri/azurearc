# Getting started with Azure Arc and Kubernetes

## Introduction

In this lab we're going to deploy a Kubernetes Cluster (based on minikube) on a Windows 10 device. At the end of this lab you'll have an Azure Arc managed Kubernetes Cluster running on Windows 10.

## Preparation

> Please start with [Lab 1 Prerequisites](/arck8s/lab1/lab1_prerequisites.md) to make sure that you've all the required components up and running before you proceed with the lab.

> **Azure ARC Private Preview** <br> As Azure Arc is still in private Preview, make sure that your subscription is already whitelisted.

```bash
az provider show --namespace Microsoft.Kubernetes
```

This should return something like this:

```console
{
  "authorizations": [],
  "id": "/subscriptions/c45eeda7-****-****-****-efdd99c9d489/providers/Microsoft.Kubernetes",
  "namespace": "Microsoft.Kubernetes",
  "registrationPolicy": "RegistrationRequired",
  "registrationState": "Registered",
  "resourceTypes": [
```

Make sure that "registrationState" is set to "Registered".

## Deploy Azure ARC

### Check if your cluster is up-and-running

We assume that our Kubernetes Cluster is already up and running and you've all the required tools in place. To make sure that you've access to your cluster, please run:

```bash
kubectl get nodes
```

This should return something like this:

```console
NAME       STATUS   ROLES    AGE    VERSION
minikube   Ready    master   3d1h   v1.17.0
```

If this isn't working, yet, please return to the [Lab 1 Prerequisites](/arck8s/lab1/lab1_prerequisites.md).


###