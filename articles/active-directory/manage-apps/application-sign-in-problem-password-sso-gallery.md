---
title: 登入針對密碼 SSO 設定 Azure AD 資源庫應用程式遇到問題 |Microsoft Docs
description: 如何針對與 Azure AD 資源庫應用程式已針對密碼單一登入問題進行疑難排解。
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 742df882fb64e09ff63ef2eceb5514ca070dc227
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190330"
---
# <a name="sign-in-problems-with-an-azure-ad-gallery-app-configured-for-sso"></a>登入的問題與 Azure AD 資源庫應用程式設定 SSO

存取面板是網頁型入口網站。 它可以讓使用者有 Azure Active Directory (Azure AD) 的工作或學校帳戶來存取雲端架構的應用程式所擁有的權限。 自助群組和應用程式管理功能，透過存取面板，也可以使用具有 Azure AD 版本的使用者。

存取面板是 Azure 入口網站分開。 使用者不需要 Azure 訂用帳戶，使用存取面板。

若要使用密碼型單一登入 (SSO) 存取面板 」 中，必須在瀏覽器中安裝存取面板延伸模組。 當您選取已設定密碼型 SSO 的應用程式時，將會自動下載擴充功能。

## <a name="browser-requirements-for-access-panel"></a>存取面板的瀏覽器需求

存取面板需要支援 JavaScript 的瀏覽器，並已啟用 CSS。

下列瀏覽器支援密碼型 SSO:

- Internet Explorer 8、 9、 10 和 11 在 Windows 7 或更新版本

- 在 Windows 7 或更新版本或在 MacOS X 或更新版本的 chrome

- Firefox 26.0 或更新版本，在 Windows XP SP2 或更新版本或在 Mac OS X 10.6 或更新版本

>[!NOTE]
>密碼型 SSO 延伸模組供適用於 Windows 10 中的 Microsoft Edge 瀏覽器擴充功能已加入 Microsoft edge 時支援。

## <a name="install-the-access-panel-browser-extension"></a>安裝存取面板的瀏覽器擴充功能

請遵循下列步驟：

1. 開啟[存取面板](https://myapps.microsoft.com)中支援的瀏覽器，並在 Azure AD 中的使用者身分登入。

2. 您可以選取 密碼-已啟用 SSO 的應用程式存取面板 」 中。

3. 當系統提示您時，選取**立即安裝**。

4. 您會被導向至您的瀏覽器為基礎的下載連結。 選取 **新增**安裝瀏覽器延伸模組。

5. 如果系統提示您，請選取**啟用**或是**允許**。

6. 安裝之後，重新啟動您的瀏覽器。

7.  登入存取面板，並看看是否您可以啟動您的密碼-已啟用 SSO 的應用程式。

您也可以直接可以下載適用於 Chrome 和 Firefox 的延伸模組，透過這些連結：

-   [Chrome 存取面板延伸模組](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Firefox 存取面板延伸模組](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="set-up-a-group-policy-for-internet-explorer"></a>設定適用於 Internet Explorer 的群組原則

您可以設定群組原則，可讓您使用者的電腦上遠端安裝 Internet explorer 的存取面板延伸模組。

必要條件如下：

-   [Active Directory 網域服務](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)必須設定，以及您在使用者電腦必須加入至您的網域。

-   您擁有 「 編輯設定 」 權限以編輯群組原則物件 (GPO)。 根據預設，下列安全性群組的成員擁有此權限：網域系統管理員、企業系統管理員及群組原則建立者擁有者。 [深入了解](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx)。

若要設定群組原則，並將它部署給使用者，請參閱[如何部署使用群組原則設定 Internet Explorer 的存取面板延伸模組](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy)。

## <a name="troubleshoot-access-panel-in-internet-explorer"></a>疑難排解 Internet Explorer 中的存取面板

若要存取診斷工具和設定的延伸模組的指示，請參閱[疑難排解 Internet explorer 的存取面板延伸模組](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting)。

## <a name="configure-password-sso-for-an-azure-ad-gallery-app"></a>設定 Azure AD 資源庫應用程式的密碼 SSO

若要設定 Azure AD 資源庫應用程式，您需要執行下列項目：

-   從 Azure AD 資源庫新增應用程式
-   [設定密碼單一登入的應用程式](#configure-the-app-for-password-sso)
-   [將使用者指派給應用程式](#assign-users-to-the-app)

### <a name="add-the-app-from-the-azure-ad-gallery"></a>從 Azure AD 資源庫新增應用程式

請遵循下列步驟：

1. 開啟[Azure 入口網站](https://portal.azure.com)並以全域管理員或共同管理員。

2. 選取 **所有服務**在若要開啟 Azure AD 延伸模組的左側導覽窗格頂端。

3. 型別**Azure Active Directory**篩選搜尋方塊中，然後選取**Azure Active Directory**。

4. 選取 **企業應用程式**從 Azure AD 瀏覽窗格。

5. 選取 **新增**的右上角**企業應用程式**窗格。

6. 在 [**從資源庫新增**區段中，輸入中的應用程式的名稱**輸入一個名稱**] 方塊中。

7. 選取您想要為 SSO 設定的應用程式。

8. *選擇性：* 新增應用程式之前，您可以變更其名稱中的**名稱** 方塊中。

9. 按一下 **新增**新增的應用程式。

   短暫延遲之後，您將能夠看見應用程式的設定窗格。

### <a name="configure-the-app-for-password-sso"></a>設定密碼 SSO 應用程式

請遵循下列步驟：

1. 開啟[Azure 入口網站](https://portal.azure.com/)並以全域管理員或共同管理員。

2. 選取 **所有服務**在若要開啟 Azure AD 延伸模組的左側導覽窗格頂端。

3. 型別**Azure Active Directory**篩選搜尋方塊中，然後選取**Azure Active Directory**。

4. 選取 **企業應用程式**Azure AD 瀏覽窗格中。

5. 選取 **所有應用程式**若要檢視您的應用程式清單。

   > [!NOTE]
   > 如果您沒有看到您想要的應用程式，使用**篩選條件**控制項頂端**所有應用程式清單**。 設定**顯示**選項，所有應用程式。 」

6. 選取您想要為 SSO 設定的應用程式。

7. 應用程式載入之後，請選取**單一登入**在應用程式的左側窗格中。

8. 選取 **密碼型登入**模式。

9. 將使用者指派至應用程式。

10. 您也可以為使用者提供認證。 （否則將會提示使用者輸入認證，在應用程式啟動。）若要這樣做，請選取使用者的資料列。 然後選取**更新認證**並輸入其使用者名稱和密碼。

### <a name="assign-users-to-the-app"></a>將使用者指派給應用程式

若要將使用者直接指派至應用程式，請遵循下列步驟：

1. 開啟[Azure 入口網站](https://portal.azure.com/)和全域管理員身分登入

2. 選取 **所有服務**中開啟 Azure AD 延伸模組的左側瀏覽痛苦。

3. 型別**Azure Active Directory**篩選搜尋方塊中，然後選取**Azure Active Directory**。

4. 選取 **企業應用程式**Azure AD 瀏覽窗格中。

5. 選取 **所有應用程式**若要檢視您的應用程式清單。

   > [!NOTE]
   > 如果您沒有看到您想要的應用程式，使用**篩選條件**控制項頂端**所有應用程式清單**。 設定**顯示**選項，所有應用程式。 」

6. 從清單中，選取您想要將使用者指派至應用程式。

7. 應用程式載入之後，請選取**使用者和群組**從左邊的應用程式的瀏覽窗格。

8. 選取**新增**頂端**使用者和群組**以開啟清單**新增指派**窗格。

9. 選取 **使用者和群組**中**新增指派**窗格。

10. 在 **依名稱或電子郵件地址**方塊中，輸入完整名稱或電子郵件地址，您想要指派的使用者。

11. 暫留在清單中的使用者。 選取使用者的設定檔照片或標誌，以新增該使用者的核取方塊**Selected**清單。

12. *選擇性：* 若要新增另一位使用者，輸入中的另一個名稱或電子郵件地址**依名稱或電子郵件地址**方塊，並選取核取方塊，以新增該使用者才**選取**清單。

13. 當您完成選取使用者時，按一下**選取**將它們新增到使用者和群組指派給應用程式的清單。

14. *選擇性：* 按一下 **選取的角色**中**新增指派**窗格，即可選取要指派給您所選取的使用者的角色。

15. 選取 **指派**若要將應用程式指派給選取的使用者。

    短暫延遲之後，使用者將能夠從存取面板存取這些應用程式。

## <a name="request-support"></a>要求支援 
如果當您設定 SSO，並將使用者指派時，您會收到一則錯誤訊息，請開啟支援票證。 請盡可能包含越好的下列資訊：

-   相互關聯錯誤 ID
-   UPN (使用者電子郵件地址)
-   TenantID
-   瀏覽器類型
-   時區和發生錯誤時的時間/時間範圍
-   Fiddler 追蹤

## <a name="next-steps"></a>後續步驟
[使用應用程式 Proxy 提供單一登入應用程式](application-proxy-configure-single-sign-on-with-kcd.md)
