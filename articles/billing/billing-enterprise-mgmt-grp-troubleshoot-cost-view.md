---
title: 針對 Azure 企業成本檢視進行疑難排解
description: 了解如何解決 Azure 入口網站中與組織成本檢視有關的任何問題。
author: bandersmsft
manager: amberb
ms.service: billing
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 83f7f424b265582a3830c02973cbbb9962ddfbfb
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2019
ms.locfileid: "67491268"
---
# <a name="troubleshoot-enterprise-cost-views"></a>為企業成本檢視疑難排解

在企業註冊內，有多個設定可能導致註冊內的使用者無法檢視成本。  這些設定由註冊管理員負責管理。 或者，如果並非直接經由 Microsoft 購買註冊，則會由合作夥伴管理設定。  本文可協助您了解設定，以及這些設定如何影響註冊。 這些設定與 Azure 角色型存取控制 (RBAC) 角色無關。

## <a name="enable-access-to-costs"></a>能夠存取成本

您查看成本資訊時，是否看見「未經授權」訊息或「成本檢視在您的註冊中停用」  ？
![訂用帳戶的「目前成本」欄位顯示「未授權」的螢幕擷取畫面。](media/billing-enterprise-mgmt-groups/unauthorized.png)

這可能是下列其中一個原因所造成：

1. 您是經由企業夥伴購買 Azure，而該夥伴尚未發佈價格。 請連絡夥伴更新[企業版入口網站](https://ea.azure.com)內的價格設定。
2. 如果您是 EA Direct 客戶，有幾種可能：
    * 您是帳戶擁有者，且您的註冊管理員已停用 [AO 檢視費用]  設定。  
    * 您是部門管理員，且您的註冊管理員已停用 [DA 檢視費用]  設定。
    * 請連絡您的註冊管理員以取得存取權。 註冊管理員可以更新[企業版入口網站](https://ea.azure.com/manage/enrollment)中的設定。

      ![顯示企業版入口網站檢視費用設定的螢幕擷取畫面。](media/billing-enterprise-mgmt-groups/ea-portal-settings.png)

## <a name="asset-is-unavailable"></a>資產無法使用

如果您在嘗試存取訂用帳戶或管理群組時收到「此資產無法使用」  錯誤訊息，則表示您不具備可檢視此項目的正確角色。  

![顯示「資產無法使用」訊息的螢幕擷取畫面。](media/billing-enterprise-mgmt-groups/asset-not-found.png)

向 Azure 訂用帳戶或管理群組系統管理員要求存取。 如需詳細資料，請參閱[使用 RBAC 和 Azure 入口網站來管理存取權](../role-based-access-control/role-assignments-portal.md)。

## <a name="next-steps"></a>後續步驟
- 如果您有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。
