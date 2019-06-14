---
title: 使用 Azure Active Directory 來授權開發人員帳戶 - Azure API 管理 | Microsoft Docs
description: 了解如何在 API 管理中使用 Azure Active Directory 來授權使用者。
services: api-management
documentationcenter: API Management
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: apimpm
ms.openlocfilehash: d267ff3a43438d9fe6e4e21f0ac023cfa6675f19
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65956297"
---
# <a name="authorize-developer-accounts-by-using-azure-active-directory-in-azure-api-management"></a>在 Azure API 管理中使用 Azure Active Directory 來授權開發人員帳戶

本文說明如何讓使用者能夠從 Azure Active Directory (Azure AD) 來存取開發人員入口網站。 本指南也說明如何新增包含 Azure AD 使用者的外部群組來管理使用者。

## <a name="prerequisites"></a>必要條件

- 完成下列快速入門：[建立 Azure APIM 執行個體](get-started-create-service-instance.md)。
- 匯入並發佈 Azure API 管理執行個體。 如需詳細資訊，請參閱[匯入和發佈](import-and-publish.md)。

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-ad"></a>使用 Azure AD 來授權開發人員帳戶

1. 登入 [Azure 入口網站](https://portal.azure.com)。 
2. 选择 ![箭號](./media/api-management-howto-aad/arrow.png)上也提供本文中使用的原始碼。
3. 在搜尋方塊中，輸入 **api**。
4. 選取 [API 管理服務]  。
5. 選取 API 管理服務執行個體。
6. 底下**安全性**，選取**識別**。
7. 從頂端選取 [+新增]  。

    [新增識別提供者]  窗格隨即出現在右邊。
8. 在 [提供者類型]  下選取 [Azure Active Directory]  。

    窗格中會出現可讓您輸入其他必要資訊的控制項。 控制項包括 [用戶端識別碼]  與 [用戶端密碼]  。 (您可在本文稍後取得這些控制項的相關資訊)。
9. 請記下的內容**重新導向 URL**。
    
   ![在 Azure 入口網站中新增識別提供者的步驟](./media/api-management-howto-aad/api-management-with-aad001.png)  
10. 在瀏覽器中開啟其他索引標籤。 
11. 瀏覽至[Azure 入口網站-應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908)Active Directory 中註冊應用程式。
12. 底下**管理**，選取**應用程式註冊**。
13. 選取 [新增註冊]  。 在 **註冊應用程式**頁面上，設定值，如下所示：
    
* 設定**名稱**有意義的名稱。 例如，*開發人員入口網站*
* 設定**支援的帳戶類型**要**只有此組織目錄中的帳戶**。 
* 設定**重新導向 URI**您從步驟 9 取得的值。 
* 選擇**註冊**。 

14.  註冊應用程式之後，請將複製**應用程式 （用戶端） 識別碼**從**概觀**頁面。 
15. 回到您的 API 管理執行個體。 在 **新增識別提供者** 視窗中，貼上**應用程式 （用戶端） 識別碼**值插入**用戶端識別碼** 方塊中。
16. 切換回 Azure AD 組態中，選取**憑證與祕密**下方**管理**。 選取 [**新的用戶端祕密**] 按鈕。 輸入中的值**描述**，選取任何選項**Expires** ，然後選擇 **新增**。 離開頁面之前，將複製用戶端祕密值。 您在下一個步驟將會用到這些資料。 
17. 底下**管理**，選取**驗證**，然後選取**識別碼權杖**下**隱含授與**
18. 返回您的 API 管理執行個體中，貼上密碼貼入**用戶端祕密** 方塊中。

    > [!IMPORTANT]
    > 請務必在金鑰到期之前，更新 [用戶端密碼]  。 
    >  
    >

19. [新增識別提供者]  視窗也包含 [允許的租用戶]  文字方塊。 請在該處指定 Azure AD 執行個體的網域，您將會對它授與 API 管理服務執行個體的 API 存取權。 您可以使用換行符號、空格或逗號來分隔多個網域。

> [!NOTE]
> 您可以在 [允許的租用戶]  區段中指定多個網域。 在使用者可透過與註冊應用程式之原始網域不同的網域登入前，不同網域的全域管理員必須授與權限，應用程式才能存取目錄資料。 若要授與權限，全域系統管理員應：。 移至 `https://<URL of your developer portal>/aadadminconsent` (例如， https://contoso.portal.azure-api.net/aadadminconsent) 。
> b. 輸入想要授與存取權的 Azure AD 租用戶網域名稱。
> c. 選取 [提交]  。 

20.  在您指定所需的設定之後，請選取 [新增]  。

儲存變更之後，所指定 Azure AD 執行個體中的使用者即可依照[使用 Azure AD 帳戶來登入開發人員入口網站](#log_in_to_dev_portal)中的步驟來登入開發人員入口網站。

## <a name="add-an-external-azure-ad-group"></a>新增外部 Azure AD 群組

在您讓 Azure AD 執行個體中的使用者可以存取之後，即可在 API 管理中新增 Azure AD 群組。 之後，您就可以更輕鬆地管理群組中開發人員與產品之間的關聯。

 > [!IMPORTANT]
 > 若要新增外部 Azure AD 群組，您必須先設定 Azure AD 執行個體上**身分識別**遵循上一節中的程序 索引標籤。 此外，應用程式必須具有存取權搭配使用的 Azure AD Graph API`Directory.Read.All`權限。 

您可以從 API 管理執行個體的 [群組]  索引標籤來新增外部 Azure AD 群組。

1. 選取 [群組]  索引標籤。
2. 選取 [新增 AAD 群組]  按鈕。
   ![[新增 AAD 群組] 按鈕](./media/api-management-howto-aad/api-management-with-aad008.png)
3. 選取您想要新增的群組。
4. 按 [選取]  按鈕。

新增外部 Azure AD 群組之後，您就可以檢閱並設定其屬性。 從 [群組]  索引標籤中選取群組名稱。您可以在這裡編輯群組的 [名稱]  與 [描述]  資訊。
 
所設定 Azure AD 執行個體的使用者現在可以登入開發人員入口網站。 這些使用者也可以檢視和訂閱所能看見的任何群組。

## <a name="a-idlogintodevportalsign-in-to-the-developer-portal-by-using-an-azure-ad-account"></a><a id="log_in_to_dev_portal"/>使用 Azure AD 帳戶來登入開發人員入口網站

使用您在先前小節中設定的 Azure AD 帳戶來登入開發人員入口網站：

1. 使用 Active Directory 應用程式設定中的登入 URL 來開啟新的瀏覽器視窗，然後選取 [Azure Active Directory]  。

   ![登入頁面][api-management-dev-portal-signin]

1. 輸入 Azure AD 中其中一位使用者的認證，然後選取 [登入]  。

   ![以使用者名稱和密碼來登入][api-management-aad-signin]

1. 如果需要其他資訊，可能會出現註冊表單的提示。 完成註冊表單，然後選取 [註冊]  。

   ![註冊表單上的 [註冊] 按鈕][api-management-complete-registration]

您的使用者現在已登入 API 管理服務執行個體的開發人員入口網站。

![註冊完成之後的開發人員入口網站][api-management-registration-complete]

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

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Accessing the Graph API]: https://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Sign in to the developer portal by using an Azure AD account]: #Sign-in-to-the-developer-portal-by-using-an-Azure-AD-account
