---
title: Azure AD Connect：疑難排解安裝期間的來源錨點的問題 |Microsoft Docs
description: 本主題提供如何在安裝期間疑難排解問題的來源錨點的步驟。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/19/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: fac33a01afc2efc1ab06c4783c11f7a089bb6208
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "62114149"
---
# <a name="troubleshooting-source-anchor-issues-during-installation"></a>在安裝期間來源錨點問題進行疑難排解
這篇文章說明不同來源錨點相關來解決這些問題的安裝和供應項目方法期間可能發生的問題。

## <a name="invalid-source-anchor-in-azure-active-directory"></a>Azure Active Directory 中的 無效的來源錨點

### <a name="custom-installation"></a>自訂安裝

自訂安裝期間，Azure AD Connect 會讀取來自 Azure Active Directory 的來源錨點的原則。 如果原則存在於 Azure Active Directory，Azure AD Connect 適用於相同的原則，除非它由客戶覆寫。 精靈會通知您哪些屬性已讀取。 此外，精靈會警告您如果嘗試覆寫來源錨點的原則。

這個讀取作業期間，就可以在 Azure Active Directory 中的來源錨點原則是意外。 在此案例中，Azure AD Connect 不知道要使用哪些來源錨點，而且需要手動覆寫。</br>
![unexpected](media/tshoot-connect-source-anchor/source1.png)

若要解決此問題，您可以手動覆寫來源錨點選取特定的屬性。 繼續使用此選項才確定要選取哪一個屬性。 如果您不確定，請連絡[Microsoft 支援服務](https://support.microsoft.com/contactus/)指導方針。 如果您變更來源錨點原則時，可能會中斷您的內部部署使用者和其相關聯的 Azure 資源之間的關聯。</br>
![unexpected](media/tshoot-connect-source-anchor/source2.png)

### <a name="express-installation"></a>快速安裝
快速在安裝期間，Azure AD Connect 會讀取來自 Azure Active Directory 的來源錨點的原則。 如果原則存在於 Azure Active Directory，Azure AD Connect 會套用相同的原則。 沒有要手動覆寫選項。

這個讀取作業期間，就可以在 Azure Active Directory 中的來源錨點原則是意外。 在此情況下，Azure AD Connect 不知道來源錨點應該是什麼。</br>
![unexpected](media/tshoot-connect-source-anchor/source3.png)

若要解決此問題，您需要重新安裝 使用自訂的模式，並以手動方式選取特定的屬性，以覆寫來源錨點。 繼續使用此選項才確定要選取哪一個屬性。 如果您不確定，請連絡[Microsoft 支援服務](https://support.microsoft.com/contactus/)指導方針。 如果您變更來源錨點原則時，可能會中斷您的內部部署使用者和其相關聯的 Azure 資源之間的關聯。

### <a name="invalid-source-anchor-in-sync-engine"></a>同步處理引擎中的 無效的來源錨點
在安裝期間，它是可能的 Azure AD Connect 來設定使用無效的來源錨點的同步處理引擎嘗試。 這項作業很可能是產品問題和 Azure AD Connect 的安裝將會失敗。 請連絡[Microsoft 支援服務](https://support.microsoft.com/contactus/)如果您在執行此問題。</br>
![unexpected](media/tshoot-connect-source-anchor/source4.png)


## <a name="next-steps"></a>後續步驟
深入了解 [整合內部部署身分識別與 Azure Active Directory](whatis-hybrid-identity.md)。