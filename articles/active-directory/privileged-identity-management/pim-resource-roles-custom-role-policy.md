---
title: 使用自訂角色以指定 Azure 資源適用的 Privileged Identity Management 設定 | Microsoft Docs
description: 說明如何在 Azure 資源適用的 PIM 中使用自訂角色。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: protection
ms.date: 03/30/2018
ms.author: rolyon
ms.openlocfilehash: 03904990d54db0dd39ed7059f57a0a13efe0aaca
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2018
ms.locfileid: "35233374"
---
# <a name="use-custom-roles-to-target-privileged-identity-management-settings"></a>使用自訂角色以指定 Privileged Identity Management 設定

您可能需要將嚴格的 Privileged Identity Management (PIM) 設定套用到某個角色的某些成員，同時為其他人提供較大的自主權。 假設您的組織雇用數名約聘夥伴，協助開發將在 Azure 訂用帳戶中執行的應用程式。

身為資源管理員的您，會希望員工能夠存取而不需要經過核准。 不過，當約聘夥伴要求存取組織資源時，全部都必須經過核准。

請依照下節概述的步驟操作，為 Azure 資源角色設置指定的 PIM 設定。

## <a name="create-the-custom-role"></a>建立自訂角色

若要建立資源適用的自訂角色，請依照[建立 Azure 角色型存取控制適用的自訂角色](../role-based-access-control-custom-roles.md)所述步驟操作。

建立自訂角色時請包含描述性名稱，如此方可輕易記住您打算複製的內建角色。

> [!NOTE]
> 確保自訂角色是您想複製的內建角色重複項，且其範圍符合內建角色。

## <a name="apply-pim-settings"></a>套用 PIM 設定

在 Azure 入口網站的租用戶中建立角色之後，請前往 [Privileged Identity Management - Azure 資源] 窗格。 選取角色適用的資源。

![「Privileged Identity Management - Azure 資源」窗格](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

[進行 PIM 角色設定](pim-resource-roles-configure-role-settings.md)，這些 PIM 角色設定應套用到該角色的成員。

最後，[將角色指派](pim-resource-roles-assign-roles.md)給您想要使用這些設定來鎖定的不同成員群組。

## <a name="next-steps"></a>後續步驟

[檢閱訂用帳戶擁有者和存取](pim-resource-roles-perform-access-review.md)
