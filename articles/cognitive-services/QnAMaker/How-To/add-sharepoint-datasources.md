---
title: Sharepoint 檔案-QnA Maker
titleSuffix: Azure Cognitive Services
description: 加入您的知識庫來擴充 Active Directory 可能會受到保護，問題和解答知識庫中的受保護的 Sharepoint 資料來源。
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 04/05/2019
ms.author: tulasim
ms.openlocfilehash: e479cf1729b7dcd2ed2f2470f2a935bdf94af80b
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2019
ms.locfileid: "65954964"
---
# <a name="add-a-secured-sharepoint-data-source-to-your-knowledge-base"></a>將受保護的 Sharepoint 資料來源新增至您的知識庫

加入您的知識庫來擴充 Active Directory 可能會受到保護，問題和解答知識庫中的受保護的 Sharepoint 資料來源。 

當您將受保護的 Sharepoint 文件新增至您的知識庫，QnA Maker 管理員 中，為您必須要求 QnA Maker 的 Active Directory 權的限。 一旦此權限針對 Sharepoint 的存取，從 Active Directory 管理員獲得權 QnA Maker，它不需要再次提供。 如果是在相同的 Sharepoint 資源，每次的後續的文件加入至知識庫就不需要授權。 

如果 QnA Maker 知識庫管理員不是 Active Directory 管理員，您必須使用 Active Directory 管理員，才能完成此程序進行通訊。

## <a name="add-supported-file-types-to-knowledge-base"></a>將支援的檔案類型加入至知識庫

您可以將所有的 QnA Maker 支援[檔案類型](../Concepts/data-sources-supported.md)從 Sharepoint 伺服器到您的知識庫。 您可能必須授與[權限](#permissions)如果受保護檔案資源。

1. 從 Sharepoint 伺服器上，選取 檔案的省略符號功能表， `...`。
1. 複製檔案的 URL。

    ![選取檔案的省略符號功能表，然後複製 URL，以取得 Sharepoint 檔案的 URL。](../media/add-sharepoint-datasources/get-sharepoint-file-url.png)

1. QnA Maker 入口網站中，在上**設定**頁面上， [URL 新增到](edit-knowledge-base.md#add-datasource)至知識庫。 

## <a name="permissions"></a>權限

受保護的檔案從 Sharepoint 伺服器加入至知識庫時，會發生授與權限。 根據如何設定 Sharepoint 向上鍵和新增檔案，這可能需要的人的權限：

* 任何額外的步驟-將檔案新增的人員不擁有所需的所有權限。
* 步驟都[知識庫管理員](#knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal)並[Active Directory 管理員](#active-directory-manager-grant-file-read-access-to-qna-maker)。

請參閱下面所列的步驟。 

### <a name="knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal"></a>知識庫管理員： QnA Maker 入口網站中新增 Sharepoint 資料來源

當**QnA Maker manager**將受保護的 Sharepoint 文件加入至知識庫，知識庫管理員起始的要求必須完成的 Active Directory 管理員的權限。

要求會開始使用快顯視窗，來驗證 Active Directory 帳戶。 

![驗證使用者帳戶](../media/add-sharepoint-datasources/authenticate-user-account.png)

一旦 QnA Maker manager 選取的帳戶，Active Directory 系統管理員就會收到通知，他們必須允許 QnA Maker (不 QnA Maker manager) 應用程式存取 Sharepoint 資源。 若要這樣做的每個 Sharepoint 資源，但該資源不是每個文件需要 Active Directory 管理員。 

### <a name="active-directory-manager-grant-file-read-access-to-qna-maker"></a>Active directory 管理員： 檔案讀取權限授與 QnA Maker

Active Directory 管理員 （而不 QnA Maker 管理員） 必須授與存取權來存取 Sharepoint 資源，方法是選取的 QnA Maker[此連結](https://login.microsoftonline.com/common/oauth2/v2.0/authorize?response_type=id_token&scope=Files.Read%20Files.Read.All%20Sites.Read.All%20User.Read%20User.ReadBasic.All%20profile%20openid%20email&client_id=c2c11949-e9bb-4035-bda8-59542eb907a6&redirect_uri=https%3A%2F%2Fwww.qnamaker.ai%3A%2FCreate&state=68)授權 QnA Maker 入口網站 Sharepoint 企業應用程式能夠讀取檔案權限。 

![Azure Active Directory 管理員授與權限以互動方式](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)

<!--
The Active Directory manager must grant QnA Maker access either by application name, `QnAMakerPortalSharepoint`, or by application ID, `c2c11949-e9bb-4035-bda8-59542eb907a6`. 
-->
<!--
### Grant access from the interactive pop-up window 

The Active Directory manager will get a pop-up window requesting permissions to the `QnAMakerPortalSharepoint` app. The pop-up window includes the QnA Maker Manager email address that initiated the request, an `App Info` link to learn more about **QnAMakerPortalSharepoint**, and a list of permissions requested. Select **Accept** to provide those permissions. 

![Azure Active Directory manager grants permission interactively](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)
-->
<!--

### Grant access from the App Registrations list

1. The Active Directory manager signs in to the Azure portal and opens **[App registrations list](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ApplicationsListBlade)**. 

1. Search for and select the **QnAMakerPortalSharepoint** app. Change the second filter box from **My apps** to **All apps**. The app information will open on the right side.

    ![Select QnA Maker app in App registrations list](../media/add-sharepoint-datasources/select-qna-maker-app-in-app-registrations.png)

1. Select **Settings**.

    [![Select Settings in the right-side blade](../media/add-sharepoint-datasources/select-settings-for-qna-maker-app-registration.png)](../media/add-sharepoint-datasources/select-settings-for-qna-maker-app-registration.png#lightbox)

1. Under **API access**, select **Required permissions**. 

    ![Select 'Settings', then under 'API access', select 'Required permission'](../media/add-sharepoint-datasources/select-required-permissions-in-settings-blade.png)

1. Do not change any settings in the **Enable Access** window. Select **Grant Permission**. 

    [![Under 'Grant Permission', select 'Yes'](../media/add-sharepoint-datasources/grant-app-required-permissions.png)](../media/add-sharepoint-datasources/grant-app-required-permissions.png#lightbox)

1. Select **YES** in the pop-up confirmation windows. 

    ![Grant required permissions](../media/add-sharepoint-datasources/grant-required-permissions.png)
-->
### <a name="grant-access-from-the-azure-active-directory-admin-center"></a>從 Azure Active Directory 系統管理中心授與存取權

1. Active Directory 管理員登入 Azure 入口網站，並開啟**[企業應用程式](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps)**。 

1. 搜尋`QnAMakerPortalSharepoint`選取 QnA Maker 應用程式。 

    [![在 企業應用程式清單中搜尋 QnAMakerPortalSharepoint](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png)](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png#lightbox)

1. 底下**安全性**，請前往**權限**。 選取 **授與組織的系統管理員同意**。 

    [![Active Directory 系統管理員選取已驗證的使用者](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png)](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png#lightbox)

1. 選取 Active directory 的權限授與的權限的登入帳戶。 


  
<!--

## Add Sharepoint data source with APIs

You need to get the Sharepoint file's URI before adding it to QnA Maker. 

## Get Sharepoint File URI

Use the following steps to transform the Sharepoint URL into a sharing token.

1. Encode the URL using [base64](https://en.wikipedia.org/wiki/Base64). 

1. Convert the base64-encoded result to an unpadded base64url format with the following character changes. 

    * Remove the equal character, `=` from the end of the value. 
    * Replace `/` with `_`. 
    * Replace `+` with `-`. 
    * Append `u!` to be beginning of the string. 

1. Sign in to Graph explorer and run the following query, where `sharedURL` is ...:

    ```
    https://graph.microsoft.com/v1.0/shares/<sharedURL>/driveitem
    ```

    Get the **@microsoft.graph.downloadUrl** and use this as `fileuri` in the QnA Maker APIs.

### Add or update a Sharepoint File URI to your knowledge base

Use the **@microsoft.graph.downloadUrl** from the previous section as the `fileuri` in the QnA Maker API for [adding a knowledge base](https://go.microsoft.com/fwlink/?linkid=2092179) or [updating a knowledge base](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update). The following fields are mandatory: name, fileuri, filename, source.

```
{
    "name": "Knowledge base name",
    "files": [
        {
            "fileUri": "<@microsoft.graph.downloadURL>",
            "fileName": "filename.xlsx",
            "source": "<sharepoint link>"
        }
    ],
    "urls": [],
    "users": [],
    "hostUrl": "",
    "qnaList": []
}
```



## Remove QnA Maker app from Sharepoint authorization

1. Use the steps in the previous section to find the Qna Maker app in the Active Directory admin center. 
1. When you select the **QnAMakerPortalSharepoint**, select **Overview**. 
1. Select **Delete** to remove permissions. 

-->

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [在您的知識庫上共同作業](collaborate-knowledge-base.md)
