# Getting started with Azure Arc and Kubernetes

## Introduction

In this lab we are going to deploy a Kubernetes Cluster (based on minikube) on a Windows 10 device. At the end of this lab you will have an Azure Arc managed Kubernetes Cluster running on Windows 10.

## Preparation

> Please start with [Lab 1 Prerequisites](/arck8s/lab1/lab1_prerequisites.md) to make sure that you've all the required components up and running before you proceed with the lab.

> **Azure ARC Private Preview**  
> As Azure Arc is still in private Preview, make sure that your subscription is already whitelisted.

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

We assume that our Kubernetes Cluster is already up and running and you have all the required tools in place. To make sure that you have access to your cluster, please run:

```bash
kubectl get nodes
```

This should return something like this:

```console
NAME       STATUS   ROLES    AGE    VERSION
minikube   Ready    master   3d1h   v1.17.0
```

If this is not working, yet, please return to the [Lab 1 Prerequisites](/arck8s/lab1/lab1_prerequisites.md).

### Prepare Arc Agent deployment

Before we can proceed with the deployment we have to gather a few information about our subscription.

```bash
# Azure Configuration Details
export RESOURCE_GROUP=connectedClusterTest # Resource Group
export SUBSCRIPTION_ID=*******-****-****-****-********** # Target Subscription ID
export LOCATION=eastus # Supported prod regions: eastus, Canary: eastus2euap (internal only)
export CONNECTED_CLUSTER_NAME=azure-arc5 # Arc Cluster Name
export CONFIGURATION_NAME=azure-arc5 # Arc Configuration Name

# no edits needed below this point 
export CONNECTED_CLUSTER_URI=https://management.azure.com/subscriptions/${SUBSCRIPTION_ID}/resourceGroups/${RESOURCE_GROUP}/providers/Microsoft.Kubernetes/connectedClusters/${CONNECTED_CLUSTER_NAME}
export CONFIG_URI=${CONNECTED_CLUSTER_URI}/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/${CONFIGURATION_NAME}
```

Based on aboves values we have to make sure that our resource group exists. You can run this in your Azure Cloud Shell:

```bash
az group create -n ${RESOURCE_GROUP} -l EastUS
```

We also need a Service Principal (SP) that has access to our new resource group (RG):

```bash
az ad sp create-for-rbac --skip-assignment
```

Make sure that you store the output of the previous command a bit as we need three of it's values later. The output should look like this:

```json
{
  "appId": "*********-****-****-****-**********",
  "displayName": "azure-cli-2019-10-01-16-52-15",
  "name": "http://azure-cli-2019-10-01-16-52-15",
  "password": "REDACTED",
  "tenant": "********-****-****-****-***********"
}
```

Save the appId, tenant, and password. These values will be used for role assignment as well as helm chart values when onboarding a cluster.

```bash
export AZURE_ARC_ONBOARDING_SECRET=<password>
export AZURE_ARC_ONBOARDING_CLIENT=<appId>
export AZURE_ARC_ONBOARDING_TENANT=<tenant>
```

Assign Contributor role to the appId from the previous step, scoped to our test resource group.

```bash
az role assignment create --role Contributor --assignee ${AZURE_ARC_ONBOARDING_CLIENT} -g ${RESOURCE_GROUP}
```

### Deploy Arc Agent

Now that we have gathered all required information we can now proceed with the deployment itself. The Arc Agent is packaged using [helm](http://helm.sh) (a Kubernetes package manager).

Add the AzureArc Helm Chart repository:

```bash
helm repo add azurearcfork8s https://azurearcfork8s.azurecr.io/helm/v1/repo
helm repo update
```

Validate the previously collected information:

```bash
echo "subscriptionId=${SUBSCRIPTION_ID}"
echo "resourceGroupName=${RESOURCE_GROUP}"
echo "resourceName=${CONNECTED_CLUSTER_NAME}"
echo "location=${LOCATION}"
echo "tenantId=${AZURE_ARC_ONBOARDING_TENANT}"
echo "clientId=${AZURE_ARC_ONBOARDING_CLIENT}"
echo "global.clientSecret=${AZURE_ARC_ONBOARDING_SECRET}"
```

And now install the AzureArc Agent Helm chart:

```bash
helm upgrade azure-arc azurearcfork8s/azure-arc-k8sagents \
    --install \
    --set global.subscriptionId=${SUBSCRIPTION_ID},global.resourceGroupName=${RESOURCE_GROUP} \
    --set global.resourceName=${CONNECTED_CLUSTER_NAME},global.location=${LOCATION} \
    --set global.tenantId=${AZURE_ARC_ONBOARDING_TENANT} \
    --set global.clientId=${AZURE_ARC_ONBOARDING_CLIENT} \
    --set global.clientSecret=${AZURE_ARC_ONBOARDING_SECRET}
```

This will now install the AzureArc agent on your Kubernetes cluster.

To validate the deployment, check the status of the Azure Arc agent deployments and pods:

```bash
kubectl get pods,deploy --all-namespaces
```

```bash
NAME                                     READY   STATUS             RESTARTS   AGE
pod/config-agent-675df89d78-n7thn        2/2     Running            0          41m
pod/connect-agent-69ffc75fd7-4lcpg       2/2     Running            0          41m
pod/controller-manager-c485c499b-vkkhh   2/2     Running            1          41m
pod/metrics-agent-7f4b6bf9cc-cmngc       1/1     Running            9          41m

NAME                                 READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/config-agent         1/1     1            1           2d
deployment.apps/connect-agent        1/1     1            1           2d
deployment.apps/controller-manager   1/1     1            1           2d
deployment.apps/metrics-agent        1/1     1            0           2d
```
