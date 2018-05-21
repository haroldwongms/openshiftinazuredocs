---
title: Deploy OpenShift Container Platform in Azure | Microsoft Docs
description: Deploy OpenShift Container Platform in Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: najoshi
editor: 
tags: azure-resource-manager

ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: haroldw
---

# Deploy OpenShift Container Platform in Azure

You can use one of several methods to deploy OpenShift Container Platform in Azure:

- You can manually deploy the necessary Azure infrastructure components and then follow the OpenShift Container Platform [documentation](https://docs.openshift.com/container-platform).
- You can also use an existing [Resource Manager template](https://github.com/Microsoft/openshift-container-platform/) that simplifies the deployment of the OpenShift Container Platform cluster.
- Another option is to use the [Azure Marketplace offer](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

For all options, a Red Hat subscription is required. During the deployment, the Red Hat Enterprise Linux instance is registered to the Red Hat subscription and attached to the Pool ID that contains the entitlements for OpenShift Container Platform.
Ensure that you have a valid Red Hat Subscription Manager (RHSM) username, password, and Pool ID. Alternatively, you can use an Activation Key, Org ID and Pool ID.  You can verify this information by signing in to https://access.redhat.com.

## Deploy by using the OpenShift Container Platform Resource Manager template

To deploy by using the Resource Manager template, you use a parameters file to supply the input parameters. To customize any of the deployment items that are not covered by using input parameters, fork the GitHub repo and change the appropriate items.

Some common customization options include, but are not limited to:

- Virtual network CIDR (variable in azuredeploy.json)
- Bastion VM size (variable in azuredeploy.json)
- Naming conventions (variables in azuredeploy.json)
- OpenShift cluster specifics, modified via hosts file (deployOpenShift.sh)

### Configure the parameters file

The OpenShift Container Platform template located at https://github.com/Microsoft/openshift-container-platform has multiple branches available for different versions of OpenShift Container Platform.  Depending on your needs, you may be able to deploy directly from the repo or you may need to fork the repo and make custom changes to the templates and / or scripts before deploying.

Use the `appId` value from the service principal you created earlier for the `aadClientId` parameter. 

The following is an example of a parameters file named azuredeploy.parameters.json with all the required inputs.

```json
{
	"$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
	"contentVersion": "1.0.0.0",
	"parameters": {
		"masterVmSize": {
			"value": "Standard_E2s_v3"
		},
		"infraVmSize": {
			"value": "Standard_E2s_v3"
		},
		"nodeVmSize": {
			"value": "Standard_E2s_v3"
		},
		"storageKind": {
			"value": "managed"
		},
		"openshiftClusterPrefix": {
			"value": "mycluster"
		},
		"masterInstanceCount": {
			"value": 3
		},
		"infraInstanceCount": {
			"value": 2
		},
		"nodeInstanceCount": {
			"value": 2
		},
		"dataDiskSize": {
			"value": 128
		},
		"adminUsername": {
			"value": "clusteradmin"
		},
		"openshiftPassword": {
			"value": "{Strong Password}"
		},
		"enableMetrics": {
			"value": "true"
		},
		"enableLogging": {
			"value": "false"
		},
		"enableCNS": {
			"value": "false"
		},
		"rhsmUsernameOrOrgId": {
			"value": "{RHSM Username}"
		},
		"rhsmPasswordOrActivationKey": {
			"value": "{RHSM Password}"
		},
		"rhsmPoolId": {
			"value": "{Pool ID}"
		},
		"sshPublicKey": {
			"value": "{SSH Public Key}"
		},
		"keyVaultResourceGroup": {
			"value": "keyvaultrg"
		},
		"keyVaultName": {
			"value": "keyvault"
		},
		"keyVaultSecret": {
			"value": "keysecret"
		},
		"enableAzure": {
			"value": "true"
		},
		"aadClientId": {
			"value": "11111111-abcd-1234-efgh-111111111111"
		},
		"aadClientSecret": {
			"value": "{Strong Password}"
		},
		"defaultSubDomainType": {
			"value": "nipio"
		},
		"addressPrefix": {
			"value": "10.0.0.0/14"
		},
		"masterSubnetPrefix": {
			"value": "10.1.0.0/16"
		},
		"nodeSubnetPrefix": {
			"value": "10.2.0.0/16"
		}
	}
}
```

Replace the parameters with your specific information.

Different releases may have slightly different parameters so please verify the necessary parameters for the branch you are using.

### Deploy by using Azure CLI

> [!NOTE] 
> The following command requires Azure CLI 2.0.8 or later. You can verify the CLI version with the `az --version` command. To update the CLI version, see [Install Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latesti).

The following example deploys the OpenShift cluster and all related resources into a resource group named myResourceGroup, with a deployment name of myOpenShiftCluster. The template is referenced directly from the GitHub repo, and a local parameters file named azuredeploy.parameters.json file is used.

```azurecli 
az group deployment create -g myResourceGroup --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

The deployment takes at least 30 minutes to complete, depending on the total number of nodes deployed and options configured. The URL of the OpenShift console and the DNS name of the OpenShift master prints to the terminal when the deployment finishes.

```json
{
  "OpenShift Console URL": "http://openshiftlb.eastus.cloudapp.azure.com/console",
  "Bastion DNS FQDN": "bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com",
  "OpenShift Master SSH": "ssh -p 2200 clusteradmin@masterdnshaw2vlrt5dsp6.cloudapp.azure.com",
  "openshift Infra Load Balancer FQDN": "infradns5772hawwn5afw.eastus.cloudapp.azure.com"
}
```

## Deploy by using the OpenShift Container Platform Azure Marketplace offer

The simplest way to deploy OpenShift Container Platform into Azure is to use the [Azure Marketplace offer](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

This is the simplest option, but it also has limited customization capabilities. The Marketplace offer includes the following configuration options:

- **Master Nodes**: Three (3) Master Nodes of instance type, Standard_E2s_v3 with a 64 GB OS disk and 128 GB data disk for Docker volume.
- **Infra Nodes**: Three (3) Infra Nodes of instance type, Standard_D4s_v3 with a 64 GB OS disk and 128 GB data disk for Docker volume.
- **Nodes**: The number of Nodes is configurable (between 1 and 9) as well as the instance type. The Nodes will also have a 64 GB OS disk and 128 GB data disk for Docker volume.
- **Disk Type**: Managed Disks are used.
- **Metrics**: Metrics can be enabled.
- **Logging**: Logging is enabled.
- **Azure Cloud Provider**: Can be enabled or disabled. It is set to enabled by default.

## Connect to the OpenShift cluster

When the deployment finishes, retrieve the connection from the output section of the deployment. Connect to the OpenShift console with your browser by using the `OpenShift Console URL`. Alternatively, you can ssh to the Bastion host by using the following command:

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```
You can also connect to the OpenShift master by using the following command:

```bash
$ ssh -p 2200 clusteradmin@masterdnshaw2vlrt5dsp6.eastus.cloudapp.azure.com
```

## Clean up resources

Use the [az group delete](/cli/azure/group#az_group_delete) command to remove the resource group, OpenShift cluster, and all related resources when they're no longer needed.

```azurecli 
az group delete --name myResourceGroup
```

## Next steps

- [Post-deployment tasks](./openshift-post-deployment.md)
- [Troubleshoot OpenShift deployment in Azure](./openshift-troubleshooting.md)
- [Getting started with OpenShift Container Platform](https://docs.openshift.com/container-platform/3.6/getting_started/index.html)
