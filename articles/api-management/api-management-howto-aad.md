---
title: "使用 Azure Active Directory 授權開發人員帳戶 - Azure API 管理 | Microsoft Docs"
description: "了解如何在 API 管理中使用 Azure Active Directory 授權使用者"
services: api-management
documentationcenter: API Management
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: apimpm
ms.openlocfilehash: c9d99d6f7c2777c8e68f5db50b402280377d88e9
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2018
---
# <a name="how-to-authorize-developer-accounts-using-azure-active-directory-in-azure-api-management"></a>如何在 Azure API 管理中使用 Azure Active Directory 授權開發人員帳戶

本指南說明如何讓使用者能夠從 Azure Active Directory 存取開發人員入口網站。 本指南也說明如何管理 Azure Active Directory 的使用者，方法是加入包含 Azure Active Directory 的使用者的外部群組。

> [!WARNING]
> 僅[「開發人員」、「標準」與「進階」](https://azure.microsoft.com/pricing/details/api-management/)層提供 Azure Active Directory 整合。

## <a name="prerequisites"></a>先決條件

- 完成下列快速入門：[建立 Azure API 管理執行個體](get-started-create-service-instance.md)。
- 匯入並發佈 API 管理執行個體。 如需詳細資訊，請參閱[匯入和發佈](import-and-publish.md)。

## <a name="how-to-authorize-developer-accounts-using-azure-active-directory"></a>如何使用 Azure Active Directory 授權開發人員帳戶

1. 登入 [Azure 入口網站](https://portal.azure.com)。 
2. 選取 ![箭號](./media/api-management-howto-aad/arrow.png).
3. 在搜尋方塊中，輸入 "api"。
4. 按一下 [API 管理服務]。
5. 選取您的 APIM 服務執行個體。
6. 在 [安全性] 底下選取 [身分識別]。

    ![外部身分識別](./media/api-management-howto-aad/api-management-with-aad001.png)
7. 從頂端按一下 [+新增]。

    [新增識別提供者] 視窗隨即出現在右邊。
8. 在 [提供者類型] 下選取 [Azure Active Directory]。

    視窗中會出現可讓您輸入其他必要資訊的控制項。 這些控制項包括：**用戶端識別碼**、**用戶端密碼** (本教學課程稍後會有相關資訊)。
9. 記下 [重新導向 URL]。  
10. 在瀏覽器中開啟其他索引標籤。 
11. 開啟 [Azure 入口網站](https://portal.azure.com)。
12. 選取 ![箭號](./media/api-management-howto-aad/arrow.png).
13. 輸入 "active"，**Azure Active Directory** 隨即出現。
14. 選取 **Azure Active Directory**。
15. 在 [管理] 底下選取 [應用程式註冊]。

    ![應用程式註冊](./media/api-management-howto-aad/api-management-with-aad002.png)
16. 按一下 [新增應用程式註冊]。

    [建立] 視窗會出現在右邊。 您可以在這裡輸入 AAD 應用程式的相關資訊。
17. 輸入應用程式的名稱。
18. 選取 [Web 應用程式/API] 作為應用程式類型。
19. 輸入開發人員入口網站的登入 URL 作為登入 URL。 在此範例中，登入 URL 是：https://apimwithaad.portal.azure-api.net/signin。
20. 按一下 [建立] 來建立應用程式。
21. 若要尋找您的應用程式，請選取 [應用程式註冊] 並依名稱搜尋。

    ![應用程式註冊](./media/api-management-howto-aad/find-your-app.png)
22. 註冊應用程式之後，請移至 [回覆 URL]，並確定 [重新導向 URL] 已設定為您從步驟 9 取得的值。 
23. 如果您想要設定您的應用程式 (例如，變更**應用程式識別碼 URL**)，請選取 [屬性]。

    ![應用程式註冊](./media/api-management-howto-aad/api-management-with-aad004.png)

    如果將對這個應用程式使用多個 Azure Active Directory，請對 [應用程式是多租用戶] 按一下 [是]。 預設值為 [ **否**]。
24. 藉由選取 [所需的權限] 來設定應用程式權限。
25. 選取您的應用程式並核取 [讀取目錄資料] 和 [登入及讀取使用者設定檔]。

    ![應用程式註冊](./media/api-management-howto-aad/api-management-with-aad005.png)

    如需應用程式和委派的權限的詳細資訊，請參閱[存取 Graph API][Accessing the Graph API]。
26. 在左側視窗中，複製 [應用程式識別碼] 值。

    ![應用程式註冊](./media/api-management-howto-aad/application-id.png)
27. 切換回您的 API 管理應用程式。 [新增識別提供者] 視窗應該會顯示出來。 <br/>在 [用戶端識別碼] 方塊中貼上 [應用程式識別碼] 值。
28. 切換回到 Azure Active Directory 設定，然後按一下 [金鑰]。
29. 指定名稱和持續時間來建立新的金鑰。 
30. 按一下 [檔案] 。 金鑰隨即產生。

    複製金鑰至剪貼簿。

    ![應用程式註冊](./media/api-management-howto-aad/api-management-with-aad006.png)

    > [!NOTE]
    > 記下此金鑰。 關閉 Azure Active Directory 組態視窗之後，即無法再次顯示金鑰。
    > 
    > 
31. 切換回您的 API 管理應用程式。 <br/>在 [新增識別提供者] 視窗中，將金鑰貼入 [用戶端密碼] 文字方塊。
32. [新增識別提供者] 視窗 (包含 [允許的租用戶] 文字方塊) 可指定哪些目錄具有 API 管理服務執行個體之 API 的存取權。 <br/>指定您要授與存取的 Azure Active Directory 執行個體的網域。 您可以使用換行符號、空格或逗號來分隔多個網域。

    可以在 [ **允許的租用戶** ] 區段中指定多個網域。 在使用者可透過與註冊應用程式之原始網域不同的網域登入前，不同網域的全域管理員必須授與權限，應用程式才能存取目錄資料。 若要授與權限，全域系統管理員應該移至 `https://<URL of your developer portal>/aadadminconsent` (例如，https://contoso.portal.azure-api.net/aadadminconsent)，輸入他們想要提供存取權之 Active Directory 租用戶的網域名稱，然後按一下 [提交]。 在下列範例中，來自 `miaoaad.onmicrosoft.com` 的全域管理員嘗試給予這個特定開發人員入口網站的權限。 

33. 指定了所需設定後，按一下 [新增]。

    ![應用程式註冊](./media/api-management-howto-aad/api-management-with-aad007.png)

儲存變更之後，在指定 Azure Active Directory 中的使用者透過遵循[使用 Azure Active Directory 帳戶登入開發人員入口網站](#log_in_to_dev_portal)中的步驟，即可登入開發人員入口網站。

![權限](./media/api-management-howto-aad/api-management-aad-consent.png)

在下一個畫面中，系統會提示全域系統管理員確認要給予權限。 

![權限](./media/api-management-howto-aad/api-management-permissions-form.png)

如果非全域管理員在全域管理員授與其權限之前便嘗試登入，登入嘗試會失敗，並且顯示錯誤畫面。

## <a name="how-to-add-an-external-azure-active-directory-group"></a>如何加入外部 Azure Active Directory 群組

為 Azure Active Directory 中的使用者啟用存取之後，您可以將 Azure Active Directory 群組加入至 API 管理，以更輕鬆管理所需產品的群組中開發人員的關聯。

若要設定外部 Azure Active Directory 群組，必須先遵循上一節中的程序，在 [身分識別] 索引標籤中設定 Azure Active Directory。 

外部 Azure Active Directory 群組可從 API 管理執行個體的 [群組] 索引標籤來新增。

![群組](./media/api-management-howto-aad/api-management-with-aad008.png)

1. 選取 [群組]  索引標籤。
2. 按一下 [新增 AAD 群組] 按鈕。
3. 選取您想要新增的群組。
4. 按 [選取] 按鈕。

一旦建立 Azure Active Directory 群組，如果您要在新增外部群組之後，檢閱並設定其屬性，請從 [群組] 索引標籤按一下群組的名稱。

從此處，您可以編輯群組的 [名稱] 和 [說明]。
 
來自所設定 Azure Active Directory 的使用者可以登入開發人員入口網站，並透過下一節中的指示，以檢視及訂閱他們看的見的任何群組。

## <a name="a-idlogintodevportalhow-to-log-in-to-the-developer-portal-using-an-azure-active-directory-account"></a><a id="log_in_to_dev_portal"/>如何使用 Azure Active Directory 帳戶登入開發人員入口網站

若要使用前一節設定的 Azure Active Directory 帳戶登入開發人員入口網站，請使用來自 Active Directory 應用程式組態的 [登入 URL] 開啟新瀏覽器視窗，然後按一下 [Azure Active Directory]。

![開發人員入口網站][api-management-dev-portal-signin]

輸入您的 Azure Active Directory 中其中一個使用者的認證，然後按一下 [ **登入**]。

![登入][api-management-aad-signin]

如果需要其他資訊，可能會出現註冊表單的提示。 完成註冊表單，然後按一下 [ **註冊**]。

![註冊][api-management-complete-registration]

您的使用者現在已登入您的 API 服務執行個體的開發人員入口網站。

![註冊完成][api-management-registration-complete]

[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.png
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[http://oauth.net/2/]: http://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Accessing the Graph API]: http://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account
