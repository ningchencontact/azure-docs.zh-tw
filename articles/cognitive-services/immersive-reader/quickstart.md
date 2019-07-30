---
title: 快速入門：使用 C# 建立可啟動沈浸式閱讀程式的 Web 應用程式
titlesuffix: Azure Cognitive Services
description: 在本快速入門中，您會從頭開始建置 Web 應用程式，並且新增沈浸式閱讀程式 API 功能。
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: quickstart
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 6386c22044483a0ac4a324397cf2f9d22e83b579
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442866"
---
# <a name="quickstart-create-a-web-app-that-launches-the-immersive-reader-c"></a>快速入門：建立可啟動沈浸式閱讀程式的 Web 應用程式 (C#)

[沈浸式閱讀程式](https://www.onenote.com/learningtools)是經過全面設計的工具，可實作經實證的技術以改善閱讀理解程度。

在本快速入門中，您會從頭開始建置 Web 應用程式，並且使用沈浸式閱讀程式 SDK 來與沈浸式閱讀程式整合。 本快速入門的完整工作範例可以在[這裡](https://github.com/microsoft/immersive-reader-sdk/tree/master/samples/quickstart-csharp)取得。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>必要條件

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads)
* 為 Azure Active Directory (Azure AD) 驗證所設定的沈浸式閱讀程式資源。 遵循[這些指引](./azure-active-directory-authentication.md)來設定。 設定範例專案屬性時，您需要這裡建立的一些值。 將工作階段的輸出儲存到文字檔中，以供日後參考。

## <a name="create-a-web-app-project"></a>建立 Web 應用程式專案

在 Visual Studio 中，使用 ASP.NET Core Web 應用程式範本與內建 Model-View-Controller 來建立新的專案。

![New Project](./media/vswebapp.png)

![新的 ASP.NET Core Web 應用程式](./media/vsmvc.png)

## <a name="acquire-an-azure-ad-authentication-token"></a>取得 Azure AD 驗證權杖

這一部分，您需要上述 Azure AD 驗證組態先決步驟中的一些值。 請回頭參考您在該工作階段儲存的文字檔。

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

以滑鼠右鍵按一下_方案總管_中的專案，然後選擇 [管理使用者密碼]  。 這樣會開啟名稱為 _secrets.json_ 的檔案。 以下列內容取代該檔案的內容，並提供上述的自訂屬性值。

```json
{
  "TenantId": YOUR_TENANT_ID,
  "ClientId": YOUR_CLIENT_ID,
  "ClientSecret": YOUR_CLIENT_SECRET,
  "Subdomain": YOUR_SUBDOMAIN
}
```

開啟 _Controllers\HomeController.cs_，並以下列程式碼取代此檔案。

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.IdentityModel.Clients.ActiveDirectory;

namespace QuickstartSampleWebApp.Controllers
{
    public class HomeController : Controller
    {
        private readonly string TenantId;     // Azure subscription TenantId
        private readonly string ClientId;     // Azure AD ApplicationId
        private readonly string ClientSecret; // Azure AD Application Service Principal password
        private readonly string Subdomain;    // Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')

        public HomeController(Microsoft.Extensions.Configuration.IConfiguration configuration)
        {
            TenantId = configuration["TenantId"];
            ClientId = configuration["ClientId"];
            ClientSecret = configuration["ClientSecret"];
            Subdomain = configuration["Subdomain"];

            if (string.IsNullOrWhiteSpace(TenantId))
            {
                throw new ArgumentNullException("TenantId is null! Did you add that info to secrets.json?");
            }

            if (string.IsNullOrWhiteSpace(ClientId))
            {
                throw new ArgumentNullException("ClientId is null! Did you add that info to secrets.json?");
            }

            if (string.IsNullOrWhiteSpace(ClientSecret))
            {
                throw new ArgumentNullException("ClientSecret is null! Did you add that info to secrets.json?");
            }

            if (string.IsNullOrWhiteSpace(Subdomain))
            {
                throw new ArgumentNullException("Subdomain is null! Did you add that info to secrets.json?");
            }
        }

        public IActionResult Index()
        {
            return View();
        }

        [Route("subdomain")]
        public string GetSubdomain()
        {
            return Subdomain;
        }

        [Route("token")]
        public async Task<string> GetToken()
        {
            return await GetTokenAsync();
        }

        /// <summary>
        /// Get an Azure AD authentication token
        /// </summary>
        private async Task<string> GetTokenAsync()
        {
            string authority = $"https://login.windows.net/{TenantId}";
            const string resource = "https://cognitiveservices.azure.com/";

            AuthenticationContext authContext = new AuthenticationContext(authority);
            ClientCredential clientCredential = new ClientCredential(ClientId, ClientSecret);

            AuthenticationResult authResult = await authContext.AcquireTokenAsync(resource, clientCredential);

            return authResult.AccessToken;
        }
    }
}
```

## <a name="add-the-microsoftidentitymodelclientsactivedirectory-nuget-package"></a>Add the Microsoft.IdentityModel.Clients.ActiveDirectory NuGet 套件

上述程式碼使用 **Microsoft.IdentityModel.Clients.ActiveDirectory** NuGet 套件中的物件，所以您必須在專案中新增該套件的參考。

從 [工具] -> [NuGet 套件管理員] -> [套件管理員主控台]  開啟 NuGet 套件管理員主控台，然後輸入下列內容：

```powershell
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 5.1.0
```

## <a name="add-sample-content"></a>新增範例內容

現在，我們會將一些範例內容新增至此 Web 應用程式。 開啟 _Views\Home\Index.cshtml_，並且以下列範例取代自動產生的程式碼：

```html
<h1 id='title'>Geography</h1>
<span id='content'>
    <p>The study of Earth's landforms is called physical geography. Landforms can be mountains and valleys. They can also be glaciers, lakes or rivers. Landforms are sometimes called physical features. It is important for students to know about the physical geography of Earth. The seasons, the atmosphere and all the natural processes of Earth affect where people are able to live. Geography is one of a combination of factors that people use to decide where they want to live.</p>
</span>

<div class='immersive-reader-button' data-button-style='iconAndText' onclick='launchImmersiveReader()'></div>

@section scripts {
    <script type='text/javascript' src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.0.0.2.js'></script>
    <script type='text/javascript' src='https://code.jquery.com/jquery-3.3.1.min.js'></script>
    <script type='text/javascript'>
    function getImmersiveReaderTokenAsync() {
        return new Promise((resolve) => {
            $.ajax({
                url: '/token',
                type: 'GET',
                success: token => {
                    resolve(token);
                }
            });
        });
    }

    function getSubdomainAsync() {
        return new Promise((resolve) => {
            $.ajax({
                url: '/subdomain',
                type: 'GET',
                success: subdomain => {
                    resolve(subdomain);
                }
            });
        });
    }

    async function launchImmersiveReader() {
        const content = {
            title: document.getElementById('title').innerText,
            chunks: [ {
                content: document.getElementById('content').innerText,
                lang: 'en'
            } ]
        };

        const token = await getImmersiveReaderTokenAsync();
        var subdomain = await getSubdomainAsync();

        ImmersiveReader.launchAsync(token, subdomain, content, { uiZIndex: 1000000 });
    }
    </script>
}
```

## <a name="build-and-run-the-app"></a>建置並執行應用程式

從功能表列中選取 [偵錯] > [開始偵錯]  ，或按 **F5** 以啟動應用程式。

在瀏覽器中，您應該會看到：

![範例應用程式](./media/quickstart-result.png)

當您按一下 [沈浸式閱讀程式] 按鈕時，您會看到沈浸式閱讀程式啟動且其內容顯示在頁面上。

![沈浸式閱讀程式](./media/quickstart-immersive-reader.png)

## <a name="next-steps"></a>後續步驟

* 檢視[教學課程](./tutorial.md)以查看您還可以使用沈浸式閱讀程式 SDK 來執行哪些作業
* 探索[沈浸式閱讀程式 SDK](https://github.com/Microsoft/immersive-reader-sdk) 和[沈浸式閱讀程式 SDK 參考](./reference.md)
