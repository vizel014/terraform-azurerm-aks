# Azure Kubernetes Service (AKS) with Terraform

This repository contains HashiCorp Terraform configuration required to create a Azure AKS cluster.

## Prerequisites
* [ubuntu](https://tutorials.ubuntu.com/tutorial/tutorial-ubuntu-on-windows#0) installed.
* [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) possibility.
* [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) installed.
* HashiCorp [Terraform](https://terraform.io/downloads.html) installed.

## Install terraform Ubuntu
Steps to install terraform on Ubuntu / Ubuntu cloud server :

Install unzip

sudo apt-get install unzip
# Download latest version of the terraform

wget https://releases.hashicorp.com/terraform/0.11.11/terraform_0.11.11_linux_amd64.zip
# Extract the downloaded file archive

unzip terraform_0.11.11_linux_amd64.zip
# Move the executable into a directory searched for executables

sudo mv terraform /usr/local/bin/
# Run it

terraform --version 

## Install kubectl Ubuntu

sudo apt-get update && sudo apt-get install -y apt-transport-https
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubectl


### Clone the Github repository

```bash
git clone https://github.com/anubhavmishra/terraform-azurerm-aks.git
```

## Tutorial

Generate Azure client id and secret.

```bash
az ad sp create-for-rbac --role="Contributor" --scopes="/SUBSCRIPTION_ID"
```

Expected output:

```bash
{
  "appId": "00000000-0000-0000-0000-000000000000",
  "displayName": "azure-cli-2017-06-05-10-41-15",
  "name": "http://azure-cli-2017-06-05-10-41-15",
  "password": "0000-0000-0000-0000-000000000000",
  "tenant": "00000000-0000-0000-0000-000000000000"
}
```

`appId` - Client id.
`password` - Client secret.
`tenant` - Tenant id.

Export environment variables to configure the [Azure](https://www.terraform.io/docs/providers/azurerm/index.html) Terraform provider.

```bash
export ARM_SUBSCRIPTION_ID="YOUR_SUBSCRIPTION_ID"
export ARM_TENANT_ID="TENANT_ID"
export ARM_CLIENT_ID="CLIENT_ID"
export ARM_CLIENT_SECRET="CLIENT_SECRET"
export TF_VAR_client_id=${ARM_CLIENT_ID}
export TF_VAR_client_secret=${ARM_CLIENT_SECRET}
```

Run Terraform init and plan.

```bash
cd terraform-azurerm-aks/
```

```bash
terraform init
```

```bash

Initializing provider plugins...

The following providers do not have any version constraints in configuration,
so the latest version was installed.

To prevent automatic upgrades to new major versions that may contain breaking
changes, it is recommended to add version = "..." constraints to the
corresponding provider blocks in configuration, with the constraint strings
suggested below.

* provider.null: version = "~> 1.0"
* provider.tls: version = "~> 1.1"

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```

```bash
terraform plan
```

```bash
terraform apply
```

*Note: Creating an Azure AKS cluster can take up to 15 minutes.*

Configure kubeconfig

Instructions can be obtained by running the following command

```bash
terraform output configure

Run the following commands to configure kubernetes client:

$ terraform output kube_config > ~/.kube/aksconfig
$ export KUBECONFIG=~/.kube/aksconfig

Test configuration using kubectl

$ kubectl get nodes
```

Save kubernetes config file to `~/.kube/aksconfig`

```bash
terraform output kube_config > ~/.kube/aksconfig
```

Set `KUBECONFIG` environment variable to the kubernetes config file

```bash
export KUBECONFIG=~/.kube/aksconfig
```

Test configuration.

```bash
kubectl get nodes
```

```bash
NAME                     STATUS    ROLES     AGE       VERSION
aks-default-75135322-0   Ready     agent     23m       v1.9.6
aks-default-75135322-1   Ready     agent     23m       v1.9.6
aks-default-75135322-2   Ready     agent     23m       v1.9.6
```