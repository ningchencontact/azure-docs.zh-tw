---
title: 在 Azure 資訊安全中心監視身分識別和存取 | Microsoft Docs
description: 了解如何在 Azure 資訊安全中心使用身分識別和存取功能，來監視使用者的存取活動和身分識別相關問題。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2019
ms.author: memildin
ms.openlocfilehash: 6b262baddd10c9d0dff4b196b733972b97d99872
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552979"
---
# <a name="monitor-identity-and-access-preview"></a>監視身分識別和存取（預覽）
當資訊安全中心發現潛在的安全性弱點時，它會建立可引導您完成所需控制之設定程序的建議，以加強並保護您的資源。

本文說明 Azure 資訊安全中心的 [資源安全性] 區段的 [身分**識別] 和 [存取**] 頁面。

如需您可能會在此頁面上看到之建議的完整清單，請參閱身分[識別和存取建議](recommendations-reference.md#recs-identity)。

> [!NOTE]
> 監視身分識別和存取是預覽功能，只會在資訊安全中心的標準層提供使用。 若要深入了解資訊安全中心的定價層，請參閱[價格](security-center-pricing.md)。
>

身分識別應該是您企業的控制平面，而保護身分識別應該是您的最高優先順序。 安全性周邊已從網路周邊發展到身分識別周邊。 安全性變得較不著重於防禦您的網路，而是較著重於防禦您的資料，以及管理您應用程式和使用者的安全性。 現今有越來越多資料和應用程式移至雲端，身分識別成為新的周邊。

監視您的身分識別活動，您就能夠在事件發生前採取主動式動作，或採取回應式動作以停止攻擊。 「身分識別和存取」儀表板提供您如下建議：

- 為您訂用帳戶中具有特殊權限的帳戶啟用 MFA
- 從訂用帳戶移除具有寫入權限的外部帳戶
- 從您的訂用帳戶中移除具有特殊權限的外部帳戶

> [!NOTE]
> 如果訂用帳戶內含超過 600 個帳戶，資訊安全中心就無法針對訂用帳戶執行身分識別建議。 未執行的建議會列在下方的 [無法使用的評量] 底下。
資訊安全中心無法針對雲端解決方案提供者 (CSP) 合作夥伴的管理專員執行身分識別建議。
>

## <a name="monitor-identity-and-access"></a>監視身分識別和存取

從 [資訊安全中心] 提要欄位（在 [**資源**] 下），或從 [總覽] 頁面選取 [身分識別] **& [存取**]，以開啟識別身分識別和存取問題的清單。 

[身分識別和存取] 底下有兩個索引標籤：

- **概觀**：資訊安全中心所識別的建議。
- **訂用帳戶**：訂用帳戶的清單和各自的目前安全性狀態。

[![身分識別 & 存取](./media/security-center-identity-access/identity-dashboard.png)](./media/security-center-identity-access/identity-dashboard.png#lightbox)

### <a name="overview-section"></a>概觀區段
[概觀] 底下有建議清單。 第一個資料行會列出建議。 第二個資料行則顯示受該項建議影響的訂用帳戶總數。 第三個資料行顯示問題的嚴重性。

1. 選取建議。 [建議] 視窗隨即開啟並顯示：

   - 建議的描述
   - 狀況不良和狀況良好的訂用帳戶清單
   - 資源清單，內含由於評估失敗而未掃描的資源，或是位於免費層中所執行訂用帳戶底下且未進行評估的資源

    [![建議視窗](./media/security-center-identity-access/select-subscription.png)](./media/security-center-identity-access/select-subscription.png#lightbox)

1. 如需其他詳細資料，請選取清單中的訂用帳戶。

### <a name="subscriptions-section"></a>訂用帳戶區段
[訂用帳戶] 底下有訂用帳戶的清單。 第一個資料行會列出訂用帳戶。 第二個資料行會顯示每個訂用帳戶的建議總數。 第三個資料行會顯示問題的嚴重性。

[![訂閱 索引標籤](./media/security-center-identity-access/subscriptions.png)](./media/security-center-identity-access/subscriptions.png#lightbox)

1. 選取訂用帳戶。 摘要檢視隨即開啟，並有三個索引標籤：

   - **建議**：根據資訊安全中心所執行的失敗評估。
   - **已通過的評估**：資訊安全中心所執行、已通過的評估清單。
   - **無法使用的評估**：由於發生錯誤或訂用帳戶內含超過 600 個帳戶而無法執行的評估清單。

   [建議] 底下有所選訂用帳戶的建議清單和每個建議的嚴重性。

   [選取訂用帳戶的 ![建議](./media/security-center-identity-access/recommendations.png)](./media/security-center-identity-access/recommendations.png#lightbox)

1. 選取建議，以獲得建議描述、狀況不良和狀況良好的訂用帳戶清單和未掃描的資源清單。

   [建議的 ![描述](./media/security-center-identity-access/designate.png)](./media/security-center-identity-access/designate.png#lightbox)

   [已通過的評估] 底下有已通過的評估清單。  這些評估的嚴重性永遠是綠色的。

   [![通過的評量](./media/security-center-identity-access/passed-assessments.png)](./media/security-center-identity-access/passed-assessments.png#lightbox)

1. 從清單中選取已通過的評估，以取得評估的描述和狀況良好訂用帳戶的清單。 狀況不良的訂用帳戶會有一個索引標籤，其中會列出所有失敗的訂用帳戶。

   [![通過的評量](./media/security-center-identity-access/remove.png)](./media/security-center-identity-access/remove.png#lightbox)

> [!NOTE]
> 如果您已建立條件式存取原則來要求 MFA，但已設定排除專案，則資訊安全中心 MFA 建議評估會將原則視為不符合規範，因為它可讓某些使用者登入 Azure 而不需要 MFA。

## <a name="next-steps"></a>後續步驟
若要深入瞭解適用于其他 Azure 資源類型的建議，請參閱下列文章：

- [保護 Azure 資訊安全中心內的機器和應用程式](security-center-virtual-machine-protection.md)
- [保護 Azure 資訊安全中心內的網路](security-center-network-recommendations.md)
- [保護 Azure 資訊安全中心內的 Azure SQL 服務和資料](security-center-sql-service-recommendations.md)