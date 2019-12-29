# Lab 2 - Assign a custom policy to Kubernetes

## Create a new custom policy definition

* Goto portal.azure.com
* Search for "Policy"
* Click on "Services" > "Policy"

You are now in the Azure Policy Panel of your Azure Portal.

* Click "Definitions" (in the Authoring section)
* Click "+ Policy Definition"
* Select a "Definition location"

You can select your Azure Subscription here.

* Click "Select"
* Specify a name e.g. "Ensure GitOps repo for Kubernetes cluster - Azure Arc resource"
* Specify a category, select "Create new" and type in "AzureArc"
* Paste the policy example into the "Policy Rule" textbox
* Click "Save"

You have now created a new custome policy definition. You can filter the list of policy definitions by "Category" (as specified above)

![Custom Policy Definition](img/lab2_azurearc_custom_policy_definition.png)

## Apply the custom policy definition

* Goto portal.azure.com
* Search for our Azure Arc Kubernetes Cluster

![Search for ARC cluster](img/lab2_search_for_azurearc_cluster.png)

* Select your Arc managed cluster
* Click "Settings" > "Policies"
* Click "-> Assign policy"
* Leave the "Scope" as it is
* Select "Policy definition"

![Select policy definition](img/lab2_available_policy_definitions.png)

* Click on "Parameters"
* Specify a few needed parameters

![Specify parameters](img/lab2_specify_policy_assignment_parameters.png)

* Click "Review + Create"
* Click "Create"

This will now apply the policy to your Kubernetes cluster.

