---
title: Azure Active Directory Domain Services：通知設定 | Microsoft Docs
description: Azure AD Domain Services 的通知設定
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: b9af1792-0b7f-4f3e-827a-9426cdb33ba6
ms.service: active-directory
ms.subservice: domains
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2018
ms.author: ergreenl
ms.openlocfilehash: d2cdff4b0b961083d8f8ac6e2eb8b1b46d30c06b
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2019
ms.locfileid: "55159209"
---
# <a name="notification-settings-in-azure-ad-domain-services"></a>Azure AD Domain Services 中的通知設定

Azure AD Domain Services 的通知可讓您在系統於您的受控網域上偵測到健康情況警示時，立即獲得更新。  

這項功能只適用於不在傳統虛擬網路上的受控網域。


## <a name="how-to-check-your-azure-ad-domain-services-email-notification-settings"></a>如何檢查您的 Azure AD Domain Services 電子郵件通知設定

1. 在 Azure 入口網站上，瀏覽至 [Azure AD Domain Services 頁面](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices)。
2. 從資料表中選擇您的受控網域
3. 在左側導覽中，選擇 [通知設定]

系統會在頁面上列出 Azure AD Domain Services 電子郵件通知的所有電子郵件收件者。

## <a name="what-does-an-email-notification-look-like"></a>電子郵件通知的外觀為何？

下圖是電子郵件通知的範例：

![範例電子郵件通知](./media/active-directory-domain-services-alerts/email-alert.png)

電子郵件會指定要顯示警示的受控網域，以及給予偵測時間和 Azure 入口網站中 Azure AD Domain Services 健康情況頁面的連結。

> [!WARNING]
> 請務必確定電子郵件是來自已驗證的 Microsoft 寄件者，然後再按一下您電子郵件中的連結。 電子郵件一律來自電子郵件：azure-noreply@microsoft.com
>


## <a name="why-would-i-receive-email-notifications"></a>為什麼我會收到電子郵件通知？

Azure AD Domain Services 會傳送有關您網域重要更新的電子郵件通知。  這些通知僅限會影響您的服務且應立即處理的緊急問題。 每個電子郵件通知都是由您受控網域上的警示所觸發。 這些警示也會出現在 Azure 入口網站上，而且可以在 [Azure AD Domain Services 健康情況頁面](active-directory-ds-check-health.md)上檢視。

Azure AD Domain Services 不會針對廣告、更新或銷售用途將電子郵件傳送給這份清單。

## <a name="when-will-i-receive-email-notifications"></a>何時會收到電子郵件通知？

在您的受控網域上發現[新警示](active-directory-ds-troubleshoot-alerts.md)時，就會立即傳送通知。 如果未解決警示，系統會每 4 天傳送電子郵件通知作為提醒。

## <a name="who-should-receive-the-email-notifications"></a>誰應該收到電子郵件通知？


 我們建議 Azure AD Domain Services 的電子郵件收件者清單應該納入可以管理及變更受控網域的人員。 若發現任何問題，應該將此電子郵件清單視為「首要回應者」。 如果您想要新增五個以上的其他電子郵件，建議您改為建立通訊群組清單，以新增至通知清單。

您可以針對與 Azure AD Domain Services 相關的通知，新增多達五個其他電子郵件。 此外，您也可以選擇讓目錄的所有全域管理員和「AAD DC 系統管理員」群組的每個成員，都收到 Azure AD Domain Services 電子郵件通知。 Azure AD Domain Services 只會將通知傳送給最多 100 個電子郵件地址，包括全域管理員和 AAD DC 系統管理員的清單。


## <a name="how-to-add-an-additional-email-recipient"></a>如何新增其他電子郵件收件者

> [!WARNING]
> 變更通知設定時，也會同時變更整個受控網域的通知設定，並非只變更您自己的通知設定。

1. 在 Azure 入口網站上，瀏覽至 [Azure AD Domain Services 頁面](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices)。
2. 按一下您的受控網域。
3. 在左側導覽中，按一下 [通知設定]。
4. 若要新增電子郵件，請在其他收件者資料表中輸入電子郵件地址。
5. 在上方導覽中，按一下 [儲存]。

## <a name="frequently-asked-questions"></a>常見問題集

#### <a name="i-received-an-email-notification-for-an-alert-but-when-i-logged-on-to-the-azure-portal-there-was-no-alert-what-happened"></a>我收到警示的電子郵件通知，但是登入 Azure 入口網站時並沒有任何警示。 發生什麼情形？

如果警示已解決，警示就會從 Azure 入口網站中消失。 最有可能的原因是收到電子郵件通知的其他人已經解決您受控網域上的警示，或者警示已經由 Azure AD Domain Services 自動解決。


#### <a name="why-can-i-not-edit-the-notification-settings"></a>為什麼我無法編輯通知設定？

如果您無法存取 Azure 入口網站中的通知設定頁面，表示您沒有編輯 Azure AD Domain Services 的權限。 您必須連絡全域管理員，取得編輯 Azure AD Domain Services 資源的權限，或從收件者清單中移除。

#### <a name="i-dont-seem-to-be-receiving-email-notifications-even-though-i-provided-my-email-address-why"></a>即使我已經提供電子郵件地址，似乎還是無法接收電子郵件通知。 原因為何？

請檢查通知是不是在電子郵件的垃圾郵件資料夾中，並確定將寄件者 (azure-noreply@microsoft.com) 列入允許清單。

## <a name="next-steps"></a>後續步驟
- [解決受控網域上的警示](active-directory-ds-troubleshoot-alerts.md)
- [深入了解 Azure AD Domain Services](active-directory-ds-overview.md)
- [連絡產品小組](active-directory-ds-contact-us.md)

## <a name="contact-us"></a>與我們連絡
請連絡 Azure Active Directory Domain Services 產品小組， [分享意見或尋求支援](active-directory-ds-contact-us.md)。
