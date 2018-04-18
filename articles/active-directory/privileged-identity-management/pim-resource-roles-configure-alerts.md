---
title: 適用於 Azure 資源的 Privileged Identity Management - 安全性警示 | Microsoft Docs
description: 說明 PIM 安全性警示。
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 86c9a0f12b2598ffbd02810a11622b13b0363a1f
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---alerts"></a>Privileged Identity Management - 資源角色 - 警示
當您環境中有可疑或不安全的活動時，適用於 Azure 資源的 PIM 會產生警示。 觸發後的警示會顯示在 [警示] 頁面上。 

![](media/azure-pim-resource-rbac/RBAC-alerts-home.png)

## <a name="review-alerts"></a>檢閱警示
選取警示，以查看詳列觸發警示之使用者或角色以及補救建議的報告。
![](media/azure-pim-resource-rbac/rbac-alert-info.png)

## <a name="alerts"></a>警示
| 警示 | 嚴重性 | 觸發程序 | 建議 |
| --- | --- | --- | --- |
| **指派了太多的擁有者到資源** |中 |太多使用者具有擁有者角色。 |檢閱清單中的使用者，然後將部分使用者重新指派給較少特殊權限的角色。 |
| **指派太多永久擁有者過到資源** |中 |太多使用者永久指派給某個角色。 |檢閱清單中的使用者，然後重新指派部分使用者以要求啟用角色。 |
| **建立了重複的角色** |中 |多個角色具有相同的準則。 |僅使用其中一個角色。 |


### <a name="severity"></a>嚴重性
* **高**：因為發生原則違規而需要立即採取行動。 
* **中**：不需要立即採取行動，但表示可能發生原則違規。
* **低**：不需要立即採取行動，但建議進行原則變更。

## <a name="configure-security-alert-settings"></a>設定安全性警示設定
從 [警示] 頁面巡覽至 [設定]。
![](media/azure-pim-resource-rbac/rbac-navigate-settings.png)

自訂不同警示的設定，以便合您的環境和安全性目標。
![](media/azure-pim-resource-rbac/rbac-alert-settings.png)
