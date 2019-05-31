---
title: 在 登入驗證-Azure Active Directory 的使用者名稱查閱 |Microsoft Docs
description: 如何螢幕上訊息會反映出在登入期間的使用者名稱查閱
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/15/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: db627359b75aa0ea19e30a8d22bcacaa3409cb4a
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/30/2019
ms.locfileid: "66418187"
---
# <a name="home-realm-discovery-for-azure-active-directory-sign-in-pages"></a>Azure Active Directory 登入頁面的主領域探索

我們正在變更 Azure Active Directory (Azure AD) 的登入行為，來為新的驗證方法騰出空間並改善可用性。 登入期間，Azure AD 會判斷使用者應在何處進行驗證。 Azure AD 會讀取組織和使用者設定，來針對於登入頁面輸入的使用者名稱做出最佳的決策。 這讓我們離採用 FIDO 2.0 等其他認證的免用密碼未來更進了一步。

## <a name="home-realm-discovery-behavior"></a>主領域探索行為

在過去，提供在登入網域，或某些舊版的應用程式的主領域探索原則所控管主領域探索。 比方說，我們探索行為中的 Azure Active Directory 使用者可以輸入其使用者名稱，但仍會在其組織的認證集合畫面會送達。 發生這種情況的正確使用者提供的組織的網域名稱為"contoso.com"時。 這項行為並不允許個別使用者自訂體驗的細微性。

若要支援較大範圍的認證，並增加可用性，現在會更新登入程序期間的 Azure Active Directory 的使用者名稱查閱行為。 新的行為就可以做出明智的決策讀取登入頁面上輸入的使用者名稱為基礎的租用戶和使用者層級的設定。 若要達到這個目的，Azure Active Directory 會檢查以查看登入頁面所輸入的使用者名稱是否存在於指定的網域，或者提供其認證的使用者重新導向。

這項工作的另一個優點是改良的錯誤訊息。 以下是錯誤的一些改良訊息時登入支援僅限 Azure Active Directory 使用者的應用程式的範例。

- 輸入使用者名稱不正確，或使用者名稱已經不尚未同步處理至 Azure AD:
  
    ![使用者名稱輸入錯誤，或是找不到](./media/signin-realm-discovery/typo-username.png)
  
- 網域名稱會輸入錯誤：
  
    ![輸入不正確或找不到的網域名稱](./media/signin-realm-discovery/typo-domain.png)
  
- 使用者嘗試使用已知的取用者網域登入：
  
    ![使用已知的取用者網域登入](./media/signin-realm-discovery/consumer-domain.png)
  
- 密碼打錯了，但使用者名稱正確無誤：  
  
    ![密碼是很好的使用者名稱輸入錯誤](./media/signin-realm-discovery/incorrect-password.png)
  
> [!IMPORTANT]
> 這項功能可能會影響依賴舊的網域層級主領域探索來強制同盟的同盟網域。 更新會新增同盟的網域支援的時，請參閱 <<c0> [ 主領域探索 Microsoft 365 服務的登入期間](https://azure.microsoft.com/updates/signin-hrd/)。 在此同時，某些組織有訓練員工能夠使用 Azure Active Directory 中不存在，但是包含適當的網域名稱，使用者名稱登入，因為網域名稱將使用者路由目前到其組織的網域的端點。 新的登入行為不允許這樣做。 若要修正使用者名稱，會通知使用者，它們並不允許使用不存在於 Azure Active Directory 的使用者名稱登入。
>
> 如果您或貴組織有相依於舊版的行為的做法，請務必為組織系統管理員以更新員工的登入和驗證的文件，並訓練員工使用其 Azure Active Directory 使用者名稱登入。
  
如果您有新的行為方面的疑慮，讓您的註解中**意見反應**一節。  

## <a name="next-steps"></a>後續步驟

[自訂您登入的品牌](../fundamentals/add-custom-domain.md)
