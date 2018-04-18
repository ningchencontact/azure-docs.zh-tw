---
title: 適用於 Azure 資源的 Privileged Identity Management - 使用自訂角色作為 PIM 設定的目標 | Microsoft Docs
description: 說明如何在適用於 Azure 資源的 PIM 中使用自訂角色。
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/30/2018
ms.author: billmath
ms.openlocfilehash: 9ac14125b837f45634870941e72ea63e9921ca70
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2018
---
# <a name="use-custom-roles-to-target-pim-settings"></a>使用自訂角色作為 PIM 設定的目標

有必要將嚴格的 PIM 設定套用到某個角色的某些成員，同時為其他人提供較大的自主權。 假設您的組織雇用數名約聘夥伴，協助開發將在 Azure 訂用帳戶中執行的應用程式。 

您是資源管理員，而且希望貴組織的員工不需核准就具有合格的存取權，但所有約聘夥伴必須在要求啟用時尋求核准。 下列步驟概述為 Azure 資源角色啟用目標 PIM 設定的程序，請依照這些步驟操作。

## <a name="create-the-custom-role"></a>建立自訂角色

[使用本指南來建立資源的自訂角色](../role-based-access-control-custom-roles.md)。

包含描述性名稱，因此您可輕易記住您打算複製的內建角色。

>[!NOTE]
>確保自訂角色是您所預期的角色重複項，而且其範圍符合內建角色。

## <a name="apply-pim-settings"></a>套用 PIM 設定

在您的租用戶中建立角色後，請巡覽至 PIM 並選取角色適用範圍的資源。

![](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

[進行 PIM 角色設定](pim-resource-roles-configure-role-settings.md)，這些PIM 角色設定應套用到這些成員

最後，[將角色指派](pim-resource-roles-assign-roles.md)給您希望使用這些設定的不同成員群組。

## <a name="next-steps"></a>後續步驟

[檢閱訂用帳戶的擁有者](pim-resource-roles-perform-access-review.md)
