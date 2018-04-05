---
title: 使用 Mobile Engagement REST API 進行驗證：手動設定
description: 描述如何手動設定 Mobile Engagement REST API 的驗證
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 2e79f9c9-41e4-45ac-b427-3b8338675163
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 0d71908b34ddf8313aa45014420c9e63a00078c9
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2018
---
# <a name="authenticate-with-mobile-engagement-rest-apis---manual-setup"></a>使用 Mobile Engagement REST API 進行驗證 - 手動設定
> [!IMPORTANT]
> Azure Mobile Engagement 將於 2018 年 3 月 31 日停止服務。 此頁面將於不久之後刪除。
> 

本文件是[使用 Mobile Engagement REST API 進行驗證](mobile-engagement-api-authentication.md)的附錄。 請務必先閱讀該篇文章以了解內容。 文中也會說明使用 Azure 入口網站執行 Mobile Engagement REST API 單次驗證設定的替代方法。

> [!NOTE]
> 以下是根據[此 Active Directory 指南](../azure-resource-manager/resource-group-create-service-principal-portal.md)撰寫的指示。 這些指示經過自訂以符合 Mobile Engagement API 的驗證需求。 如果您想要詳細了解下列步驟，請參閱該文。

1. 透過 [Azure 入口網站](https://portal.azure.com/)登入 Azure 帳戶。
2. 從左窗格中，選取 [ **Active Directory** ]。

   ![選取 Active Directory][1]

3. 若要檢視目錄中的應用程式，請選取 [應用程式註冊]。

   ![檢視應用程式][3]

4. 選取 [新增應用程式註冊]。

   ![新增應用程式][4]

5. 填入應用程式的名稱。 讓應用程式類型保持 [Web 應用程式/API]，然後選取 [下一步] 按鈕。 您可以針對 [登入 URL] 提供任何虛擬 URL。 此案例不會使用它們，也不會驗證 URL 本身。

   完成之後，Azure Active Directory (Azure AD) 應用程式會具有您所提供的名稱。 它就是您的 **AD\_APP\_NAME**，請務必將此記下。

   ![應用程式名稱][8]

7. 選取該應用程式名稱。

8. 找到 [應用程式識別碼]，並將此記下。 這將作為 API 呼叫之 **CLIENT\_ID** 的用戶端識別碼。

   ![找到應用程式識別碼][10]

9. 找到右側的 [金鑰] 區段。

   ![金鑰區段][11]

10. 建立新的金鑰，然後立即複製。 它不會再次顯示。

    ![含有金鑰詳細資料的金鑰窗格][12]

    > [!IMPORTANT]
    > 此金鑰會在您指定的持續時間結束時到期。 到時候請務必更新金鑰，否則您的 API 驗證將無法再運作。 如果您認為此金鑰已遭到盜用，您可將它刪除並重新建立。
    >
    
11. 選取頁面頂端的 [端點] 按鈕。 然後複製 **OAUTH 2.0 權杖端點**。

    ![複製端點][14]

16. 此端點的格式如下，而 URL 中的 GUID 就是您的 **TENANT_ID**︰`https://login.microsoftonline.com/<GUID>/oauth2/token`

17. 接下來，您要設定此應用程式上的權限。 請移至 [Azure 入口網站](https://portal.azure.com)來開始該程序。

18. 選取 [資源群組]，然後尋找 [MobileEngagement] 資源群組。

    ![尋找 MobileEngagement][15]

19. 選取 [MobileEngagement] 資源群組，然後選取 [所有設定]。

    ![瀏覽至 MobileEngagement 的設定][16]

20. 在 [設定] 區段中，選取 [使用者]。 接下來，若要新增使用者，請選取 [新增]。

    ![新增使用者][17]

21. 按一下 [選取角色]。

    ![選取角色][18]

22. 選取 [擁有者]。

    ![選取 [擁有者] 作為角色][19]

23. 在搜尋方塊中，搜尋應用程式的名稱 **AD\_APP\_NAME**。 此名稱預設不會在此。 請在找到之後予以選取。 然後，按一下位於區段底部的 [選取]。

    ![選取名稱][20]

24. 在 [新增存取] 區段中，它會顯示為 [1 個使用者、0 個群組]。 選取 [確定] 以確認變更。

    ![確認新增的使用者][21]

您現在已完成所需的 Azure AD 組態，可以呼叫 API。

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



