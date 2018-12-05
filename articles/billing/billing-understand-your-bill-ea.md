---
title: 了解 Azure 企業版的帳單 | Microsoft Docs
description: 了解如何閱讀並了解 Azure Enterprise 合約客戶的使用量和帳單
services: ''
documentationcenter: ''
author: adpick
manager: dougeby
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2018
ms.author: cwatson
ms.openlocfilehash: b724fc7a887550b4115a988149b4b7a6c95de830
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/29/2018
ms.locfileid: "52584463"
---
# <a name="understand-your-bill-for-azure-customers-with-an-enterprise-agreement"></a>了解 Azure Enterprise 合約客戶的帳單

當 Azure Enterprise 合約客戶超過組織的信用額度，或使用信用額度未涵蓋的服務時，他們就會收到發票。 

貴組織的信用額度包含您的承諾用量金額。 承諾用量金額是貴組織針對 Azure 服務使用量所預付的金額。 您可以連絡您的 Microsoft 客戶經理或轉銷商，在 Enterprise 合約中增加承諾用量金額的資金。  

## <a name="when-credit-exceeded-or-doesnt-apply"></a>當信用額度超過或不適用時

發生下列情況時，您就會收到一或多張發票：

- **服務超額**：組織的使用量費用超過您的信用額度餘額。
- **分別計費的費用**：信用額度未涵蓋貴組織所使用的服務。 不論您的信用額度餘額為何，都會收到下列服務的發票：
    - Canonical
    - Citrix XenApp Essentials
    - Citrix XenDesktop 
    - 已註冊的使用者
    - Openlogic
    - 遠端存取權限 XenApp Essentials 已註冊的使用者
    - Ubuntu Advantage
    - Visual Studio Enterprise (每月)
    - Visual Studio Enterprise (每年)
    - Visual Studio Professional (每月)
    - Visual Studio Professional (每年)
- **Marketplace 費用**：Azure Marketplace 購買與使用量並未涵蓋於組織的信用額度內，會分開計費。 企業系統管理員能夠在企業版入口網站中，啟用和停用其組織的 Marketplace 購買。 

當您在計費期間因服務超額和分別計費的費用而產生費用時，您就會收到一張包含這兩種類型費用的發票。 Marketplace 費用的發票一律會單獨開立。

## <a name="review-charges"></a>檢閱費用

若要檢閱並確認發票上的費用，您必須是企業系統管理員。 如需詳細資訊，請參閱[了解 Azure 中的 Azure Enterprise 合約系統管理角色](billing-understand-ea-roles.md)。 如果您不知道誰是貴組織的企業系統管理員，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

### <a name="review-service-overage-invoice"></a>檢閱服務超額發票

在企業版入口網站，從 [報表] > [使用量摘要] 中，比較您的總使用量金額與服務超額發票。 服務超額發票包含超過組織信用額度的使用量，和/或信用額度未涵蓋的服務。 **使用量摘要**的金額不含稅。 

1. 登入[企業版入口網站](https://ea.azure.com)。
1. 選取 [報表]。
1. 在索引標籤右上角，將檢視從 **M** 切換為 **C**，以符合發票上的期間。
 
   ![在 [使用量摘要] 上顯示 M + C 選項的螢幕擷取畫面。](./media/billing-understand-your-bill-ea/ea-portal-usage-sumary-cm-option.png)

1. [總使用量] 金額應該符合您服務超額發票上的 [應收金額總計]。 下表列出發票上和企業版入口網站中 [使用量摘要] 上所顯示的詞彙和說明：

   |發票詞彙|使用量摘要詞彙|說明|
   |---|---|---|
   |應收金額總計|總使用量|套用信用額度之前針對特定期間的稅前使用量費用總計。|
   |已使用的承諾用量|已使用的承諾用量|在該特定期間內套用的信用額度。|
   |銷售總額|總超額|超過您信用額度金額的使用量費用總計。 此金額不含稅。|
   |稅額|不適用|適用於特定期間之銷售總額的稅金。|
   |總金額|不適用|在套用信用額度並增加稅金之後發票的應付金額。|
1. 若要取得費用的詳細資訊，請前往 [下載使用量] > [進階報表下載]。 此報表不包含保留項目的稅金或費用，或是 Marketplace 費用。

      ![在 [下載使用量] 索引標籤上顯示 [進階報表下載] 的螢幕擷取畫面。](./media/billing-understand-your-bill-ea/ea-portal-download-usage-advanced.png)

### <a name="review-marketplace-invoice"></a>檢閱 Marketplace 發票

在企業入口網站的 [報表] > [使用量摘要] 上，比較您的 Azure Marketplace 總計和 Marketplace 發票。 Marketplace 發票僅適用於 Azure Marketplace 購買和使用量。 **使用量摘要**的金額不含稅。 

1. 登入[企業版入口網站](https://ea.azure.com)。
1. 選取 [報表]。
1. 在索引標籤右上角，將檢視從 **M** 切換為 **C**，以符合發票上的期間。

     ![在 [使用量摘要] 上顯示 M + C 選項的螢幕擷取畫面。](./media/billing-understand-your-bill-ea/ea-portal-usage-sumary-cm-option.png)

1. **Azure Marketplace** 總計應該符合 Marketplace 發票上的**銷售總額**。
1. 若要取得依使用量計算的費用詳細資訊，請前往 [下載使用量]。 在 [Marketplace 費用] 下方，選取 [下載]。 此報表不包含稅金或顯示一次性購買。

     ![在 Marketplace 費用下方顯示下載選項的螢幕擷取畫面。](./media/billing-understand-your-bill-ea/ea-portal-download-usage-marketplace.png)

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如果您有問題或需要協助，請[建立支援要求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。
