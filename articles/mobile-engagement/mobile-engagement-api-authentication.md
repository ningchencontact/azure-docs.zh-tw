---
title: 使用 Mobile Engagement REST API 進行驗證
description: 描述如何使用 Azure Mobile Engagement REST API 進行驗證
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: da82cb36-957a-4e19-a805-b44733cf6597
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 10/05/2016
ms.author: wesmc;ricksal
ms.openlocfilehash: 52843c926248627e4e530612cfe9326d4302eb95
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/19/2018
---
# <a name="authenticate-with-mobile-engagement-rest-apis"></a>使用 Mobile Engagement REST API 進行驗證
> [!IMPORTANT]
> Azure Mobile Engagement 將於 2018 年 3 月 31 日停止服務。 此頁面將於不久之後刪除。
> 

## <a name="overview"></a>概觀

本文件說明如何取得有效的 Azure Active Directory (Azure AD) OAuth 權杖，以使用 Mobile Engagement REST API 進行驗證。

此程序假設您具備有效的 Azure 訂用帳戶，且已使用其中一個[開發人員教學課程](mobile-engagement-windows-store-dotnet-get-started.md)建立 Mobile Engagement 應用程式。

## <a name="authentication"></a>驗證

使用 Microsoft Azure Active Directory 型的 OAuth 權杖進行驗證。 

若要驗證 API 要求，必須將授權標頭加入至每個要求。 授權標頭的格式如下：

    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGmJlNmV2ZWJPamg2TTNXR1E...

> [!NOTE]
> Azure Active Directory 權杖會在一小時內過期。
> 
> 

有幾種方式可以取得權杖。 因為會從雲端服務呼叫 API，所以您要使用 API 金鑰。 在 Azure 術語中，API 金鑰稱為「服務主體密碼」。 下列程序說明以手動設定的一種方法。

### <a name="one-time-setup-using-a-script"></a>單次設定 (使用指令碼)

若要使用 PowerShell 指令碼執行此設定，請依照下列指示來執行步驟。 PowerShell 指令碼需要的設定時間最少，但使用所允許的預設值最多。 

或者，您也可以依照[手動設定](mobile-engagement-api-authentication-manual.md)中的指示，直接從 Azure 入口網站執行此動作。 當您從 Azure 入口網站設定時，可執行更詳細的設定。

1. [下載](http://aka.ms/webpi-azps)以取得最新版的 Azure PowerShell。 如需下載指示的詳細資訊，請參閱[此概觀](/powershell/azure/overview)。

2. 安裝 Azure PowerShell 之後，使用下列命令，以確保您已安裝 **Azure 模組**︰

    a. 確定 Azure PowerShell 模組可在可用模組清單中取得。

        Get-Module –ListAvailable

    ![可用的 Azure 模組][1]

    b. 如果您在先前的清單中找不到 Azure PowerShell 模組，則必須執行︰

        Import-Module Azure
3. 執行下列命令，從 PowerShell 登入 Azure Resource Manager。 提供 Azure 帳戶的使用者名稱和密碼： 

        Connect-AzureRmAccount
4. 如果您擁有多個訂用帳戶，請執行下列步驟︰

    a. 取得所有訂用帳戶的清單。 然後，複製所要使用訂用帳戶的 **SubscriptionId**。 確定此訂用帳戶有 Mobile Engagement 應用程式。 您要使用此應用程式來與 API 互動。 

        Get-AzureRmSubscription

    b. 執行下列命令。 提供 **SubscriptionId** 來設定您要使用的訂用帳戶：

        Select-AzureRmSubscription –SubscriptionId <subscriptionId>
5. 將 [New-AzureRmServicePrincipalOwner.ps1](https://raw.githubusercontent.com/matt-gibbs/azbits/master/src/New-AzureRmServicePrincipalOwner.ps1) 指令碼的文字複製到本機電腦。 然後儲存為 PowerShell Cmdlet (例如 `APIAuth.ps1`)，然後予以執行。

         `.\APIAuth.ps1`.

6. 指令碼會要求您提供 **principalName** 的輸入。 提供您要使用於 Active Directory 應用程式的適當名稱 (例如 APIAuth)。 

7. 在指令碼完成執行之後，會顯示以下四個值。 請務必複製它們，因為您需要它們才能以程式設計方式利用 Active Directory 進行驗證： 

   - **TenantId**
   - **SubscriptionId**
   - **ApplicationId**
   - **祕密**

   使用 TenantId 作為 `{TENANT_ID}`、使用 ApplicationId 作為 `{CLIENT_ID}`，並使用 Secret 作為 `{CLIENT_SECRET}`。

   > [!NOTE]
   > 預設的安全性原則可能會阻止您執行 PowerShell 指令碼。 如果是這樣，請使用下列命令，暫時將您的執行原則設定為允許執行指令碼：
   > 
   > Set-ExecutionPolicy RemoteSigned
8. 以下是一組 PowerShell Cmdlet 的樣貌。
    ![PowerShell Cmdlet][3]
9. 在 Azure 入口網站中，移至 Active Directory，選取 [應用程式註冊]，然後搜尋您的應用程式以確保其存在。
    ![搜尋您的應用程式][4]

### <a name="steps-to-get-a-valid-token"></a>取得有效權杖的步驟

1. 搭配下列參數呼叫 API。 務必要取代 **TENANT\_ID**、**CLIENT\_ID** 及 **CLIENT\_SECRET**：
   
   * **要求 URL** 為 `https://login.microsoftonline.com/{TENANT_ID}/oauth2/token`

   * **HTTP Content-Type 標頭**為 `application/x-www-form-urlencoded`
   
   * **HTTP 要求本文**為 `grant_type=client\_credentials&client_id={CLIENT_ID}&client_secret={CLIENT_SECRET}&resource=https%3A%2F%2Fmanagement.core.windows.net%2F`
     
    以下是範例要求：
    ```
    POST /{TENANT_ID}/oauth2/token HTTP/1.1
    Host: login.microsoftonline.com
    Content-Type: application/x-www-form-urlencoded
    grant_type=client_credentials&client_id={CLIENT_ID}&client_secret={CLIENT_SECRET}&reso
    urce=https%3A%2F%2Fmanagement.core.windows.net%2F
    ```
    以下是範例回應：
    ```
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Content-Length: 1234

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1445395811","not_before":"144
    5391911","resource":"https://management.core.windows.net/","access_token":{ACCESS_TOKEN}}
    ```
     這個範例包含 POST 參數的 URL 編碼，而 `resource` 值實際上是 `https://management.core.windows.net/`。 請注意也要將 `{CLIENT_SECRET}` 進行 URL 編碼，因為它可能包含特殊字元。

     > [!NOTE]
     > 如需測試，您可以使用像是 [Fiddler](http://www.telerik.com/fiddler) 或 [Chrome Postman 擴充功能](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop)的 HTTP 用戶端工具。 
     > 
     > 
2. 現在，在每個 API 呼叫中加入授權要求標頭：
   
        Authorization: Bearer {ACCESS_TOKEN}
   
    如果您的要求傳回 401 狀態碼，請檢查回應本文。 其可能指出權杖已過期。 在此情況下，請取得新的權杖。

## <a name="use-the-apis"></a>使用 API
既然您已取得有效的權杖，您可以開始執行 API 呼叫。

1. 在每個 API 要求中，您都要傳遞有效且未到期的權杖。 您已在上一節取得未到期的權杖。

2. 在用來識別應用程式的要求 URI 中插入一些參數。 要求 URI 看起來會像下列程式碼：
   
        https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/
        providers/Microsoft.MobileEngagement/appcollections/{app-collection}/apps/{app-resource-name}/
   
    若要取得參數，請選取您的應用程式名稱。 然後，選取 [儀表板]。 您會看到含有以下這三個參數的頁面：
   
   * **1** `{subscription-id}`
   * **2** `{app-collection}`
   * **3** `{app-resource-name}`
   * **4** 除非您建立一個新的，否則您的資源群組名稱應該是 **MobileEngagement**。 

> [!NOTE]
> 請忽略 API 根位址，因為該位址適用於舊版 API。
> 
> 若您已使用 Azure 入口網站建立應用程式，則必須使用和應用程式名稱本身不同的應用程式資源名稱。 如果您已在 Azure 入口網站建立應用程式，則應該使用應用程式名稱。 (對於在新的入口網站建立的應用程式，應用程式資源名稱與應用程式名稱之間並無差異。)
> 
> 

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication/azure-module.png
[2]: ./media/mobile-engagement-api-authentication/mobile-engagement-api-uri-params.png
[3]: ./media/mobile-engagement-api-authentication/ps-cmdlets.png
[4]: ./media/mobile-engagement-api-authentication/search-app.png



