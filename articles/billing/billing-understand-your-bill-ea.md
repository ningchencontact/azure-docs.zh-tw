---
title: 了解 Azure 企業版上的帳單 |Microsoft Docs
description: 了解如何閱讀並了解適用於 Azure Enterprise 合約的使用量和帳單
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
ms.date: 04/01/2019
ms.author: banders
ms.openlocfilehash: 810d0795bd005cc313ec3567811ad55ba1d9d522
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60369988"
---
# <a name="understand-your-azure-enterprise-agreement-bill"></a>了解 Azure Enterprise 合約帳單

當 Azure Enterprise 合約客戶超過組織的信用額度，或使用信用額度未涵蓋的服務時，他們就會收到發票。

貴組織的信用額度包含您的承諾用量金額。 承諾用量金額是貴組織針對 Azure 服務使用量所預付的金額。 您可以連絡您的 Microsoft 客戶經理或轉銷商，在 Enterprise 合約中增加承諾用量金額的資金。  

## <a name="invoices-for-most-customers"></a>對大多數客戶的發票

本節不適用於在澳洲、 日本、 或新加坡的 Azure 客戶。 如果您是在這些國家/地區的其中一個，請參閱[若為其他客戶的發票](#invoices-for-other-customers)。

在您的計費週期期間發生下列其中一項時，您會收到 Azure 發票：

- **服務超額**：組織的使用量費用超過您的信用額度餘額。
- **個別計費的費用**：信用額度未涵蓋貴組織所使用的服務。 不論您的信用額度餘額為何，都會收到下列服務的發票：
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
- **Marketplace 費用**：Azure Marketplace 購買與使用方式不係由貴組織的信用額度。 因此，您是按發票支付 Marketplace 費用不論您的信用額度餘額。 在企業版入口網站中，企業系統管理員可以啟用和停用 Marketplace 購買項目。

## <a name="review-charges-for-most-customers"></a>對大多數客戶的檢閱費用
本節不適用於在澳洲、 日本、 或新加坡的 Azure 客戶。 如果您是在這些國家/地區的其中一個，請參閱[檢閱其他客戶的費用](#review-charges-for-other-customers)。

若要檢閱並確認發票上的費用，您必須是企業系統管理員。 如需詳細資訊，請參閱[了解 Azure 中的 Azure Enterprise 合約系統管理角色](billing-understand-ea-roles.md)。 如果您不知道誰是貴組織的企業系統管理員，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

發票會顯示所有您的 Azure 使用量，後面接著任何 Marketplace 費用。 如果您的信用結餘，它會套用到 Azure 的使用狀況。

比較您的企業入口網站中所示的結合總金額**報表** > **使用量摘要**與您的 Azure 發票。 中的金額**使用量摘要**不含稅。

1. 登入[企業版入口網站](https://ea.azure.com)。
1. 選取 [報表]。
1. 在索引標籤右上角，將檢視從 **M** 切換為 **C**，以符合發票上的期間。  
    ![螢幕擷取畫面顯示 M + C 選項，在 使用量摘要。](./media/billing-understand-your-bill-ea/ea-portal-usage-sumary-cm-option.png)
1. 總和的量**總使用量**並**Azure Marketplace**應該符合**擴充總量變更**您發票上。
1. 若要取得有關費用的詳細資料，請前往**下載使用量**。  
    ![顯示 [下載使用量] 索引標籤的螢幕擷取畫面](./media/billing-understand-your-bill-ea/ea-portal-download-usage.png)

## <a name="invoices-for-other-customers"></a>若為其他客戶的的發票

本節僅適用於在澳洲、 日本、 或新加坡的 Azure 客戶。

當發生下列情況時，您會收到一或多個 Azure 發票：

- **服務超額**：組織的使用量費用超過您的信用額度餘額。
- **個別計費的費用**：信用額度未涵蓋貴組織所使用的服務。 不論您的信用額度餘額為何，都會收到下列服務的發票：
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
- **Marketplace 費用**：Azure Marketplace 的購買和使用量並未涵蓋在貴組織的信用額度內，會個別計費。 在企業版入口網站中，企業系統管理員可以啟用和停用 Marketplace 購買項目。

當您有費用，因為服務超額部分和會分開計費期間的計費期間的費用，您會取得一個的發票。 它包含這兩種類型的費用。 Marketplace 費用的發票一律會單獨開立。

## <a name="review-charges-for-other-customers"></a>若為其他客戶的的檢閱費用

如果您位於澳洲、 日本或新加坡，僅適用於這一節。

若要檢閱並確認發票上的費用，您必須是企業系統管理員。 如需詳細資訊，請參閱[了解 Azure 中的 Azure Enterprise 合約系統管理角色](billing-understand-ea-roles.md)。 如果您不知道誰是貴組織的企業系統管理員，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

### <a name="review-service-overage-invoice"></a>檢閱服務超額發票

比較您的企業入口網站中的總使用量量**報表** > **使用量摘要**和服務超額發票。 服務超額發票包含超過組織信用額度的使用量，和/或信用額度未涵蓋的服務。 **使用量摘要**的金額不含稅。

1. 登入[企業版入口網站](https://ea.azure.com)。
1. 選取 [報表]。
1. 在索引標籤右上角，將檢視從 **M** 切換為 **C**，以符合發票上的期間。  
    ![螢幕擷取畫面顯示 M + C 選項，在 使用量摘要。](./media/billing-understand-your-bill-ea/ea-portal-usage-sumary-cm-option.png)
1. [總使用量] 金額應該符合您服務超額發票上的 [應收金額總計]。  
1. 若要取得費用的詳細資訊，請前往 [下載使用量] > [進階報表下載]。 報告不包含稅金，或保留項目的費用或 marketplace 費用。  
      ![在 [下載使用量] 索引標籤上，下載螢幕擷取畫面顯示進階的報表。](./media/billing-understand-your-bill-ea/ea-portal-download-usage-advanced.png)

下表列出發票上和企業版入口網站中 [使用量摘要] 上所顯示的詞彙和說明：

|發票詞彙|使用量摘要詞彙|描述|
|---|---|---|
|應收金額總計|總使用量|套用信用額度之前針對特定期間的稅前使用量費用總計。|
|已使用的承諾用量|已使用的承諾用量|在該特定期間內套用的信用額度。|
|銷售總額|總超額|超過您信用額度金額的使用量費用總計。 此金額不含稅。|
|稅額|不適用|適用於特定期間之銷售總額的稅金。|
|總金額|不適用|在套用信用額度並增加稅金之後發票的應付金額。|

### <a name="marketplace-invoice"></a>Marketplace 發票

如果您位於澳洲、 日本或新加坡，僅適用於這一節。

在企業入口網站的 [報表] > [使用量摘要] 上，比較您的 Azure Marketplace 總計和 Marketplace 發票。 Marketplace 發票僅適用於 Azure Marketplace 購買和使用量。 **使用量摘要**的金額不含稅。

1. 登入[企業版入口網站](https://ea.azure.com)。
1. 選取 [報表]。
1. 在索引標籤右上角，將檢視從 **M** 切換為 **C**，以符合發票上的期間。  
     ![螢幕擷取畫面顯示 M + C 選項，在 使用量摘要。](./media/billing-understand-your-bill-ea/ea-portal-usage-sumary-cm-option.png)  
1. **Azure Marketplace** 總計應該符合 Marketplace 發票上的**銷售總額**。
1. 若要取得依使用量計算的費用詳細資訊，請前往 [下載使用量]。 在 [Marketplace 費用] 下方，選取 [下載]。 此報表不包含稅金或顯示一次性購買。  
     ![螢幕擷取畫面，顯示下載 Marketplace 費用下的選項。](./media/billing-understand-your-bill-ea/ea-portal-download-usage-marketplace.png)

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如果您有任何疑問或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟
- [檢視及下載您的 Azure 使用量和費用](billing-download-azure-daily-usage.md)
