---
title: What is Azure Stack? | Microsoft Docs
description: Azure Stack Development Kit is an environment for evaluating Azure Stack features and scenarios.
services: azure-stack
documentationcenter: 
author: HeathL17
manager: byronr
editor: 
ms.assetid: d9e6aee1-4cba-4df5-b5a3-6f38da9627a3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/10/2017
ms.author: helaw
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: d941879aee6042b38b7f5569cd4e31cb78b4ad33
ms.openlocfilehash: 65278e7b352df5651f04151210ccc34a58dd321a
ms.contentlocale: de-de
ms.lasthandoff: 07/10/2017


---
# <a name="what-is-azure-stack"></a>What is Azure Stack?

Microsoft Azure Stack is a hybrid cloud platform that lets you deliver Azure services from your organization’s datacenter.  Azure Stack is designed to help you in key scenarios, like meeting security and compliance requirements, or where you need to access Azure resources without internet connectivity.  

## <a name="azure-stack-development-kit"></a>Azure Stack Development Kit
Microsoft Azure Stack Development Kit is a single-node version of Azure Stack, which you can use to evaluate and learn about Azure Stack.  You can also use Azure Stack Development Kit as a developer environment, where you can develop using consistent APIs and tooling.  

You should be aware of these points with Azure Stack Development Kit:

* Azure Stack Development Kit must not be used as a production environment and should only be used for testing, evaluation, and demonstration.  
* Your deployment of Azure Stack is associated with a single Azure Active Directory or Active Directory Federation Services identity provider. You can create multiple users in this directory and assign subscriptions to each user.
* With all components deployed on the single machine, there are limited physical resources available for tenant resources. This configuration is not intended for scale or performance evaluation.
* Networking scenarios are limited due to the single host/NIC requirement.

## <a name="next-steps"></a>Next steps
[Key features and concepts](azure-stack-key-features.md)

[Hybrid Application innovation with Azure and Azure Stack (pdf)](https://go.microsoft.com/fwlink/?LinkId=842846&clcid=0x409)


