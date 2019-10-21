---
title: Azure Resource Manager 範本範例
description: 用於部署管理功能 (例如角色和鎖定) 的 Azure Resource Manager 範本範例。
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.date: 11/16/2018
ms.author: tomfitz
ms.openlocfilehash: e342507b584a405637fc6728dfcd6e49199a154f
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390170"
---
# <a name="azure-resource-manager-templates-for-management-features"></a>管理功能的 Azure Resource Manager 範本

下表包含 Resource Manager 所提供功能的 Azure Resource Manager 範本連結。

| | |
|-|-|
|**角色指派**||
| [指派資源群組的角色](https://github.com/Azure/azure-quickstart-templates/tree/master/101-rbac-builtinrole-resourcegroup)| 將內建角色指派給現有資源群組的使用者。 |
| [指派現有虛擬機器的角色](https://github.com/Azure/azure-quickstart-templates/tree/master/101-rbac-builtinrole-virtualmachine)| 將內建角色指派給現有 VM 的使用者。 |
| [指派多部虛擬機器的角色](https://github.com/Azure/azure-quickstart-templates/tree/master/201-rbac-builtinrole-multipleVMs)| 將內建角色指派給多部虛擬機器的使用者。 |
| [指派 Azure 訂用帳戶的角色](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/subscription-role-assigment)| 將角色指派給 Azure 訂用帳戶的使用者。 |
|**角色定義**||
| [建立自訂角色定義](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/create-role-def)| 在 Azure 訂用帳戶中建立新的角色定義。 |
|**資源鎖定**||
| [鎖定資源群組](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/create-rg-lock-role-assignment)| 建立資源群組，並將 **DoNotDelete** 鎖定套用到資源群組。 將參與者角色指派給使用者。 |
| | |
