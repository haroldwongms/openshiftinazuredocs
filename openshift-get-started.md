---
title: OpenShift in Azure overview | Microsoft Docs
description: An overview of OpenShift in Azure.
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

# OpenShift in Azure

OpenShift is an open and extensible container application platform that brings Docker and Kubernetes to the enterprise.  

OpenShift includes Kubernetes for container orchestration and management. It adds developer and operations-centric tools that enable:

- Rapid application development.
- Easy deployment and scaling.
- Long-term lifecycle maintenance for teams and applications.

There are multiple versions of OpenShift, of which two are available to run in Azure:

- OpenShift Container Platform
- OpenShift Dedicated
- OpenShift Online
- OpenShift Origin

Of the four versions covered in this article, two are available for customers to deploy in Azure on their own: OpenShift Container Platform and OpenShift Origin.

## OpenShift Container Platform

Container Platform is an enterprise-ready [commercial version](https://www.openshift.com) from and supported by Red Hat. With this version, customers purchase the necessary entitlements for OpenShift Container Platform and are responsible for installation and management of the entire infrastructure.

Because customers "own" the entire platform, they can install it in their on-premises datacenter, or in a public cloud (such as Azure, AWS, or Google).

## OpenShift Dedicated

Dedicated is a Red Hat-managed *single-tenant* OpenShift that uses OpenShift Container Platform. Red Hat manages all of the underlying infrastructure (VMs, OpenShift cluster, networking, storage, etc.). The cluster is specific to one customer and runs in a public cloud (such as AWS or Google, with Azure coming in early 2018). A starting cluster includes four application nodes for $48,000 per year (paid up front).

## OpenShift Online

Online is a Red Hat-managed *multi-tenant* OpenShift that uses Container Platform. Red Hat manages all of the underlying infrastructure (such as VMs, OpenShift cluster, networking, and storage). 

With this version, the customer deploys containers but has no control over which hosts the containers run. Because Online is multi-tenant, containers may be located on the same VM hosts as containers from other customers. Cost is per container.

## OpenShift Origin

Origin is an [open-source](https://www.openshift.org/) upstream project of OpenShift that's community supported. Origin can be installed on CentOS or Red Hat Enterprise Linux (RHEL).


## Next steps

- [Configure common prerequisites for OpenShift in Azure](./openshift-prerequisites.md)
- [Deploy OpenShift Container Platform in Azure](./openshift-container-platform.md)
- [Deploy OpenShift Origin in Azure](./openshift-origin.md)
- [Post-deployment tasks](./openshift-post-deployment.md)
- [Troubleshoot OpenShift deployment](./openshift-troubleshooting.md)

### Documentation contributors

Thank you to Vincent Power (vincepower) and Alfred Sin (asinn826) for their contributions to keeping this documentation up to date!