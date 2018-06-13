---
title: 包含檔案
description: 包含檔案
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/21/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: c04514218c7ed8dfd72b94345d2deb88e663fda1
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/23/2018
ms.locfileid: "29816977"
---
[Azure 原則](/azure/azure-policy/)會協助您確認訂用帳戶中的所有資源均符合公司標準。 將部署選項限制為只有已核准的資源類型和 SKU，以使用原則來降低成本。 您為資源定義規則和動作，而那些規則會在部署期間自動強制執行。 例如，您可以控制部署的資源類型。 或者，您可以限制資源的核准位置。 某些原則會拒絕動作，而某些原則會設定動作的稽核。

原則與角色型存取控制 (RBAC) 互補。 RBAC 著重在使用者存取權，並且是預設拒絕和明確允許的系統。 原則在部署期間及之後著重於資源屬性。 原則是預設允許和明確拒絕的系統。

原則有兩個概念要了解 - 原則定義和原則指派。 原則定義描述您想要強制執行的管理條件。 原則指派會將原則定義放入特定範圍的動作。

![指派原則](./media/resource-manager-governance-policy/policy-concepts.png)

Azure 提供數個內建原則定義，您不需任何修改即可使用這些原則定義。 您傳遞參數值來指定在範圍中允許的值。 如果內建原則定義無法滿足您的需求，您可以[建立自訂原則定義](../articles/azure-policy/create-manage-policy.md)。
