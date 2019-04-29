---
title: 在 Azure 資訊安全中心提供安全性連絡人詳細資料 | Microsoft Docs
description: 本文件說明如何在 Azure 資訊安全中心提供安全性連絡人詳細資料。
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 26b5dcb4-ce3f-4f22-8d56-d2bf743cfc90
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/9/2018
ms.author: rkarlin
ms.openlocfilehash: b6babf7d5d5a0f5796efa9418044366c6a135ed9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60909264"
---
# <a name="provide-security-contact-details-in-azure-security-center"></a>在 Azure 資訊安全中心提供安全性連絡人詳細資料
Azure 資訊安全中心會建議您針對您的 Azure 訂用帳戶提供安全性連絡人詳細資料 (如果您還沒有這麼做)。 如果 Microsoft 安全性回應中心 (MSRC) 發現您的客戶資料遭到非法或未經授權的對象存取，Microsoft 會使用此資訊連絡您。 MSRC 執行 Azure 網路和基礎結構的選取安全性監視，並接收來自協力廠商的威脅情報和濫用客訴。

在每天第一個警示發生時且僅針對高嚴重性警示，才會傳送電子郵件通知。 電子郵件喜好設定只能針對訂用帳戶原則設定。 在訂用帳戶內的資源群組會繼承這些設定。 

所傳送的警示電子郵件通知：
- 僅針對高嚴重性的警示傳送
- 每日傳送給各警示類型的一個電子郵件收件者  
- 在一天內不會傳送給單一收件者超過 3 個電子郵件訊息
- 每個電子郵件訊息均包含單一警示，而非警示的彙總
 
例如，如果電子郵件訊息已傳送警示表示您即將遭受 RDP 攻擊，您在同一天內不會收到另一個表示您即將遭受 RDP 攻擊的電子郵件訊息，即使是另一個警示已觸發。 
 

> [!NOTE]
> 本文件將使用範例部署來介紹服務。  這不是逐步指南。
>
>

## <a name="implement-the-recommendation"></a>實作建議
1. 在 [建議] 下方，選取 [提供安全性連絡人詳細資料]。
   ![提供安全性連絡人][1]
2. 選取要提供連絡人資訊的 Azure 訂用帳戶。
3. 這會開啟 [電子郵件通知]。

   ![提供安全性連絡人詳細資料][2]

   * 輸入安全性連絡人的電子郵件地址，若有多個則以逗號分隔。 您可以輸入的電子郵件地址數沒有限制。
   * 輸入一個安全性連絡人國際電話號碼。
   * 若要接收有關高嚴重性警示的電子郵件，請開啟 [傳送給我有關警示的電子郵件] 選項。
   * 未來，您會有將電子郵件通知傳送給訂用帳戶擁有者的選項。 此選項目前無法使用。
   * 選取 [儲存]  將安全性連絡人資訊套用至您的訂用帳戶。

## <a name="see-also"></a>請參閱
如要深入了解資訊安全中心，請參閱下列主題：

* [在 Azure 資訊安全中心設定安全性原則](tutorial-security-policy.md) -- 了解如何為您的 Azure 訂用帳戶及資源群組設定安全性原則。
* [管理 Azure 資訊安全中心的安全性建議](security-center-recommendations.md) -- 了解建議如何協助保護您的 Azure 資源。
* [Azure 資訊安全中心的安全性健全狀況監視](security-center-monitoring.md) -- 了解如何監視 Azure 資源的健全狀況。
* [管理與回應 Azure 資訊安全中心的安全性警示](security-center-managing-and-responding-alerts.md) -- 了解如何管理與回應安全性警示。
* [使用 Azure 資訊安全中心監視合作夥伴解決方案](security-center-partner-solutions.md) -- 了解如何監視合作夥伴解決方案的健全狀況。
* [Azure 資訊安全中心常見問題集](security-center-faq.md) -- 尋找有關使用服務的常見問題。
* [Azure 安全性部落格](https://blogs.msdn.com/b/azuresecurity/) -- 取得最新的 Azure 安全性新聞和資訊。

<!--Image references-->
[1]: ./media/security-center-provide-security-contacts/provide-contacts.png
[2]:./media/security-center-provide-security-contacts/provide-contact-details.png
