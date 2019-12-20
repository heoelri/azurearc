# Lab 1 - Prerequisites
This page contains the prerequisites for [Lab 1](/arck8s/lab1/lab1.md) (Getting started with Azure Arc and Kubernetes). Please make sure that you've all prerequisites in place before you proceed with the lab.

## Hyper-V (or VirtualBox)
Minikube can run on Hyper-V or VirtualBox. We recommend to use Hyper-V for Windows 10 devices. 

You can find the detailed instructions here:

* [Hypervisor Setup (for Minikube)](https://minikube.sigs.k8s.io/docs/start/windows/)

Please make sure that the Hypervisor (either Hyper-V or Virtualbox is configured correctly).

## Minikube

After you've made sure that your Hypervisor is up and running, you can continue with installing Minikube.

* [Download the Minikube Installer](https://minikube.sigs.k8s.io/docs/start/windows/)
* [Install and Configure Minikube](https://minikube.sigs.k8s.io/docs/start/windows/
)

## Windows Subsystem for Linux (WSL)
For a suitable work environment we recommend to install the Windows Subsystem for Linux. This will allow you to work with bash on your Windows 10 System.

* [Windows Subsystem for Linux Installation Guide for Windows 10](https://docs.microsoft.com/windows/wsl/install-win10)

If you don't have a prefered Linux Distribution, use "Ubuntu", as we've build our labs on top of the Ubuntu Distribution.

### WSL and Minikube
To make sure that you can talk to your Minikube-based Kubernetes Cluster from within the WSL, you've to modify the Kubernetes Configuration (.kube/config) first.

```bash
kubectl config set-cluster minikube --server=https://<minikubeip>:8443 --certificate-authority=/mnt/c/Users/<windowsusername>/.minikube/ca.crt
kubectl config set-credentials minikube --client-certificate=/mnt/c/Users/<windowsusername>/.minikube/client.crt --client-key=/mnt/c/Users/<windowsusername>/.minikube/client.key
kubectl config set-context minikube --cluster=minikube --user=minikube
```
> **Important!** Make sure that you that you replace <minikubeip> and <windowsusername> with the correct values.
> This is based on James Sturtevant's blog post [Running Kubernetes Minikube on Windows 10 with WSL](https://www.jamessturtevant.com/posts/Running-Kubernetes-Minikube-on-Windows-10-with-WSL/) 

## Azure CLI
Now that we've our Hypervisor, Minikube and the Windows Subsystem for Linux installed, we can continue to install some additional tooling. First of all we need the Azure CLI in our WSL environment.

To make sure that you're in the right environment, please open your WSL or start Powershell and run:

```powershell
wsl
```

The installation is pretty easy. For Debian/Ubuntu you can use the following command:

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

For other Distributions and platforms, please have a look [here](https://docs.microsoft.com/cli/azure/install-azure-cli-apt?view=azure-cli-latest).

## Helm

The last component we need is Helm which is a package manager for Kubernetes. You can install helm directly from within your WSL (see above in "Azure CLI").

```bash
curl https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | bash
```

If you need more information about helm, please have a look [here](https://helm.sh/docs/intro/install/).

We're now ready to proceed with [Lab 1](/arck8s/lab1/lab1.md).