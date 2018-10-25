---
title: Azure 資訊安全中心中的威脅偵測 | Microsoft Docs
description: " 藉由整合 Microsoft Cloud App Security 與 Azure 資訊安全中心來偵測威脅和惡意行為。 "
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/21/2018
ms.author: rkarlin
ms.openlocfilehash: 2a747bdd8de41283b9cba1e40e2652aa826e9c60
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/02/2018
ms.locfileid: "48044445"
---
# <a name="ueba-for-azure-resources-and-users"></a>Azure 資源和使用者的 UEBA 

Azure 資訊安全中心與 Microsoft Cloud App Security 合作，為您的 Azure 資源和使用者 (Azure 活動) 提供以使用者和實體行為分析 (UEBA) 為基礎的警示。 這些警示會偵測使用者行為的異常，並且是以使用者實體與行為分析和機器學習為基礎 (ML)，讓您可以立即跨訂用帳戶對其活動執行進階威脅偵測。 因為他們會自動啟用，新的異常偵測透過提供即時偵測來提供即時結果，並且跨與您訂用帳戶相關聯的使用者和資源，將大量行為異常設為目標。 此外，這些警示會利用 Microsoft Cloud App Security 偵測引擎中已經存在的其他資料，來協助您加速調查流程和遏制發生中的威脅。 

> [!NOTE]
> Azure 資訊安全中心，可能會將從客戶資源 (如虛擬機器或 Azure Active Directory 租用戶) 收集而來或與其相關聯的一份安全性相關客戶資料儲存在：(a) 該資源所在的相同地區，除非 Microsoft 尚未在這些地區部署 Azure 資訊安全中心，在此情況下，該資料會儲存在美國；以及 (b) 在 Azure 資訊安全中心使用其他 Microsoft Online Services 來處理這類資料的位置，它可能會根據其他 Online Service 的地理位置規則來儲存這類資料。
>

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="prerequisites"></a>先決條件

- 有效且作用中的 [Microsoft Cloud App Security 授權](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security)
- [資訊安全中心標準層](https://azure.microsoft.com/pricing/details/security-center/)
 
## <a name="threat-detection-alerts"></a>威脅偵測警示

資訊安全中心支援 Cloud App Security 的異常偵測警示，例如：

**不可能的移動**
-  此偵測會識別在一段時間內出現的兩個使用者活動 (可能是單一或多個工作階段)，它們來自遙遠的地理位置，而使用者從第一個位置移動到第二個位置所需的時間會比此段時間長，這代表不同的使用者在使用相同的認證。 這種偵測所利用的機器學習演算法會忽略造成不可能之移動情況的明顯「誤判」，例如，組織中的其他使用者經常使用的 VPN 和位置。 該偵測有七天的初始學習期間，在此期間它會學習新使用者的活動模式。

**非經常性國家/地區的活動**
- 此偵測會考慮過去的活動位置，以判斷新的和非經常性的位置。 異常偵測引擎會儲存組織中使用者先前所使用位置的相關資訊。 當活動出現自組織中任何使用者最近都沒有造訪或從未造訪的位置時，就會觸發警示。 

**來自匿名 IP 位址的活動**
- 此偵測會識別活動是來自已視為匿名 Proxy IP 位址的使用者。 這些 Proxy 通常由想要隱藏其裝置 IP 位址的人員使用，而且可能用於惡意意圖。 此偵測利用可減少「誤判」的機器學習演算法，例如組織中使用者廣泛使用之誤標記的 IP 位址。
 
  ![威脅偵測警示](./media/security-center-ueba-mcas/security-center-mcas-alert.png)

## <a name="disabling-threat-detection-alerts"></a>停用威脅偵測警示

這些警示預設為啟用，但您可以停用它們：

1. 在 [資訊安全中心] 刀鋒視窗中，選取 [安全性原則]。 針對您要變更的訂用帳戶按一下 [編輯設定]。
2.  按一下 [威脅偵測]。
3. 在 [啟用整合] 底下，取消選取 [允許 Microsoft Cloud App Security 存取我的資料]，然後按一下 [儲存]。

   ![威脅偵測警示](./media/security-center-ueba-mcas/security-center-mcas-optout.png)

> [!NOTE]
> 有七天的初始學習期間，在此期間內，並非所有的異常偵測警示都會發出。 在此期間之後，每個工作階段都會和過去一個月偵測到的活動 (使用者活動的時間、IP 位址、裝置等) 及這些活動的風險評分比較。 這些偵測是機器學習異常偵測引擎的一部分，它會剖析您的環境，並根據從您組織的活動學習到的基準線來觸發警示。 這些偵測也利用專門用來剖析使用者和登入模式的機器學習演算法，以減少誤判。
>
  
## <a name="next-steps"></a>後續步驟
此文章說明如何使用 Azure 資訊安全中心中的威脅偵測。 如要深入了解資訊安全中心，請參閱下列主題：

* [Azure 資訊安全中心常見問題集](security-center-faq.md)-- 尋找有關使用服務的常見問題。
* [Azure 資訊安全中心的安全性健康狀態監視](security-center-monitoring.md)--了解如何監視 Azure 資源的健康狀態。



<!--Image references-->
[1]: ./media/security-center-confidence-score/confidence-score.png
[2]: ./media/security-center-confidence-score/suspicious-confidence-score.png
