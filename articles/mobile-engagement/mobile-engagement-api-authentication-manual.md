---
title: "使用 Mobile Engagement REST API 進行驗證 - 手動設定"
description: "描述如何手動設定 Mobile Engagement REST API 的驗證"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 2e79f9c9-41e4-45ac-b427-3b8338675163
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 3b678acbae225c28223a2ee76e5be2462a529362
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2018
---
# <a name="authenticate-with-mobile-engagement-rest-apis---manual-setup"></a>使用 Mobile Engagement REST API 進行驗證 - 手動設定
這份文件是以附錄文件集[與 Mobile Engagement REST Api 的驗證](mobile-engagement-api-authentication.md)。 請務必先閱讀它，以取得內容。
它會描述如何設定您的驗證使用 Azure 入口網站的 Mobile Engagement REST api 的單次安裝的替代方式。

> [!NOTE]
> 下列指示會根據這個[Active Directory 指南](../azure-resource-manager/resource-group-create-service-principal-portal.md)和自訂的所需的 Mobile Engagement 應用程式開發介面的驗證內容。 如果您想要了解以下的詳細步驟，請參閱它。

1. 透過 [Azure 入口網站](https://portal.azure.com/)登入 Azure 帳戶。
2. 從左窗格中，選取 [ **Active Directory** ]。

     ![選取 Active Directory][1]

3. 若要檢視的應用程式目錄中，按一下**應用程式註冊**。

     ![檢視應用程式][3]

4. 按一下 [新增應用程式註冊]。

     ![新增應用程式][4]

5. 填入的應用程式名稱，並讓應用程式做為類型保持看**Web API 應用程式/**按 [下一步] 按鈕。 您可以提供任何 dummy Url**登入 URL**： 不會使用此案例中並不會驗證其本身的 Url。
6. 一旦完成，您有 Azure AD 應用程式，但您提供的名稱。 它是您**AD\_應用程式\_名稱**，請記下它。

     ![應用程式名稱][8]

7. 按一下應用程式名稱。
8. 尋找**應用程式識別碼**，記下它，，就是要做為用戶端識別碼**用戶端\_識別碼**對於您的 API 呼叫。

     ![設定應用程式][10]

9. 尋找**金鑰**右邊的區段。

     ![設定應用程式][11]

10. 建立新的金鑰和立即將它複製並儲存它供使用。 它會永遠不會顯示一次。

     ![設定應用程式][12]

    > [!IMPORTANT]
    > 此金鑰到期，請務必更新時卻否則 API 驗證將無法再運作，您所指定的持續時間的結尾。 如果您認為此金鑰遭到盜用，您也可以將它刪除並重新建立。
    >
    >
11. 按一下**端點**和複製的頁面頂端的按鈕**OAUTH 2.0 權杖端點**。

    ![][14]

16. 此端點將會以下列形式在 URL 中的 GUID 所在您**TENANT_ID**所以請記下它：`https://login.microsoftonline.com/<GUID>/oauth2/token`
17. 現在我們將繼續設定此應用程式上的權限。 這將需要開啟 [Azure 入口網站](https://portal.azure.com)。 
18. 按一下 [資源群組] 並尋找 [Mobile Engagement] 資源群組。

    ![][15]

19. 按一下**Mobile Engagement**資源群組，並瀏覽至**設定**這裡區段。

    ![][16]

20. 按一下**使用者**的設定區段，然後按一下 上**新增**新增使用者。

    ![][17]

21. 按一下**選取角色**。

    ![][18]

22. 按一下**擁有者**。

    ![][19]

23. 在 [搜尋] 方塊中搜尋應用程式的名稱 **AD\_APP\_NAME**。 依預設，您不會在這裡看到它。 一旦您找到它，請選取它，然後按一下 **選取**底部的區段。

    ![][20]

24. 在**加入存取** 區段中，它會顯示為**1 個使用者、 群組 0**。 按一下**確定**上此區段，以確認變更。

    ![][21]

您現在已完成所需 Azure AD 設定，您都設為呼叫的 Api。

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication-manual/active-directory.png
[2]: ./media/mobile-engagement-api-authentication-manual/active-directory-details.png
[3]: ./media/mobile-engagement-api-authentication-manual/view-applications.png
[4]: ./media/mobile-engagement-api-authentication-manual/add-icon.png
[5]: ./media/mobile-engagement-api-authentication-manual/what-do-you-want-to-do.png
[6]: ./media/mobile-engagement-api-authentication-manual/tell-us-about-your-application.png
[7]: ./media/mobile-engagement-api-authentication-manual/app-properties.png
[8]: ./media/mobile-engagement-api-authentication-manual/aad-app.png
[9]: ./media/mobile-engagement-api-authentication-manual/configure-menu.png
[10]: ./media/mobile-engagement-api-authentication-manual/client-id.png
[11]: ./media/mobile-engagement-api-authentication-manual/client-secret.png
[12]: ./media/mobile-engagement-api-authentication-manual/keys.png
[13]: ./media/mobile-engagement-api-authentication-manual/view-endpoints.png
[14]: ./media/mobile-engagement-api-authentication-manual/app-endpoints.png
[15]: ./media/mobile-engagement-api-authentication-manual/resource-groups.png
[16]: ./media/mobile-engagement-api-authentication-manual/resource-groups-settings.png
[17]: ./media/mobile-engagement-api-authentication-manual/add-users.png
[18]: ./media/mobile-engagement-api-authentication-manual/add-role.png
[19]: ./media/mobile-engagement-api-authentication-manual/select-role.png
[20]: ./media/mobile-engagement-api-authentication-manual/add-user-select.png
[21]: ./media/mobile-engagement-api-authentication-manual/add-access-final.png



