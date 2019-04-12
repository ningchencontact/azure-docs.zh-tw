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
ms.date: 04/10/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d82c1909a28de694925a12e9b801361cad1f36d2
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/11/2019
ms.locfileid: "59494690"
---
# <a name="home-realm-discovery-for-azure-active-directory-sign-in-pages"></a>Azure Active Directory 登入頁面的主領域探索

我們會變更我們 Azure Active Directory (Azure AD) 登入行為，以騰出空間給新的驗證方法，並改善可用性。 在登入時，Azure AD 會決定使用者需要進行驗證。 Azure AD 中，可讓您做出明智的決策所讀取的登入頁面上輸入的使用者名稱的組織和使用者設定。 這是達到無密碼的未來，可讓額外的認證，例如 FIDO 2.0 的步驟。

## <a name="home-realm-discovery-behavior"></a>主領域探索行為

在過去，提供在登入網域，或某些舊版的應用程式的主領域探索原則所控管主領域探索。 比方說，我們探索行為中的 Azure Active Directory 使用者可以輸入其使用者名稱，但仍會在其組織的認證集合畫面會送達。 發生這種情況的正確使用者提供的組織的網域名稱為"contoso.com"時。 此行為不允許的資料粒度，以自訂個別的使用者體驗。

若要支援較大範圍的認證，並增加可用性，現在會更新登入程序期間的 Azure Active Directory 的使用者名稱查閱行為。 新的行為就可以做出明智的決策讀取登入頁面上輸入的使用者名稱為基礎的租用戶和使用者層級的設定。 若要達到這個目的，Azure Active Directory 會檢查以查看登入頁面所輸入的使用者名稱是否存在於指定的網域，或者提供其認證的使用者重新導向。

這項工作的另一個優點是改良的錯誤訊息。 以下是錯誤的一些改良訊息時登入支援僅限 Azure Active Directory 使用者的應用程式的範例。

1. 輸入使用者名稱不正確，或使用者名稱已經不尚未同步處理至 Azure AD:
  
    ![使用者名稱輸入錯誤，或是找不到](./media/signin-realm-discovery/typo-username.png)
  
2. 網域名稱會輸入錯誤：
  
    ![輸入不正確或找不到的網域名稱](./media/signin-realm-discovery/typo-domain.png)
  
3. 使用者嘗試使用已知的取用者網域登入：
  
    ![使用已知的取用者網域登入](./media/signin-realm-discovery/consumer-domain.png)
  
4. 密碼打錯了，但使用者名稱正確無誤：  
  
    ![密碼是很好的使用者名稱輸入錯誤](./media/signin-realm-discovery/incorrect-password.png)
  
> [!IMPORTANT]
> 這項功能可能會影響依賴舊的網域層級主領域探索來強制同盟的同盟網域。 更新會新增同盟的網域支援的時，請參閱 <<c0> [ 主領域探索 Microsoft 365 服務的登入期間](https://blogs.azure.net/updates/Admin/Blogs/29/Posts/3170)。 在此同時，某些組織有訓練員工能夠使用 Azure Active Directory 中不存在，但是包含適當的網域名稱，使用者名稱登入，因為網域名稱將使用者路由目前到其組織的網域的端點。 新的登入行為不允許這樣做。 若要修正使用者名稱，會通知使用者，它們並不允許使用不存在於 Azure Active Directory 的使用者名稱登入。
>
> 如果您或貴組織有相依於舊版的行為的做法，請務必為組織系統管理員以更新員工的登入和驗證的文件，並訓練員工使用其 Azure Active Directory 使用者名稱登入。
  
如果您有新的行為方面的疑慮，讓您的註解中**意見反應**一節。  

## <a name="next-steps"></a>後續步驟

[自訂您登入的品牌](../fundamentals/add-custom-domain.md)