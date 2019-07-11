---
title: 快速入門：建立可啟動沈浸式閱讀程式的 Web 應用程式 (C#)
titlesuffix: Azure Cognitive Services
description: 在本快速入門中，您會從頭開始建置 Web 應用程式，並且新增沈浸式閱讀程式功能。
services: cognitive-services
author: metanMSFT
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: quickstart
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 77d95383c801038c256ccb2bf386ddf06048cf78
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/21/2019
ms.locfileid: "67311799"
---
# <a name="quickstart-create-a-web-app-that-launches-the-immersive-reader-c"></a>快速入門：建立可啟動沈浸式閱讀程式的 Web 應用程式 (C#)

[沈浸式閱讀程式](https://www.onenote.com/learningtools)是經過全面設計的工具，可實作經實證的技術以改善閱讀理解程度。

在本快速入門中，您會從頭開始建置 Web 應用程式，並且使用沈浸式閱讀程式 SDK 來與沈浸式閱讀程式整合。 本快速入門的完整工作範例可以在[這裡](https://github.com/Microsoft/immersive-reader-sdk/samples/quickstart-csharp)取得。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>必要條件

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads)
* 沈浸式閱讀程式的訂用帳戶金鑰。 依照[下列指示](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)取得金鑰。

## <a name="create-a-web-app-project"></a>建立 Web 應用程式專案

在 Visual Studio 中，使用 ASP.NET Core Web 應用程式範本與內建 Model-View-Controller 來建立新的專案。

![New Project](./media/vswebapp.png)

![新的 ASP.NET Core Web 應用程式](./media/vsmvc.png)

## <a name="acquire-an-access-token"></a>取得存取權杖

這個步驟您需要訂用帳戶金鑰和端點。 您可以在 Azure 入口網站沈浸式閱讀程式資源的 [金鑰] 頁面中找到您的訂用帳戶金鑰。 您可以在 [概觀] 頁面中找到您的端點。

以滑鼠右鍵按一下_方案總管_中的專案，然後選擇 [管理使用者密碼]  。 這樣會開啟名稱為 _secrets.json_ 的檔案。 以下列項目取代該檔案的內容，在適當的位置提供您的訂用帳戶金鑰和端點。

```json
{
  "SubscriptionKey": YOUR_SUBSCRIPTION_KEY,
  "Endpoint": YOUR_ENDPOINT
}
```

開啟 _Controllers\HomeController.cs_，並將 `HomeController` 類別取代為下列程式碼。

```csharp
public class HomeController : Controller
{
    private readonly string SubscriptionKey;
    private readonly string Endpoint;

    public HomeController(Microsoft.Extensions.Configuration.IConfiguration configuration)
    {
        SubscriptionKey = configuration["SubscriptionKey"];
        Endpoint = configuration["Endpoint"];

        if (string.IsNullOrEmpty(Endpoint) || string.IsNullOrEmpty(SubscriptionKey))
        {
            throw new ArgumentNullException("Endpoint or subscriptionKey is null!");
        }
    }

    public IActionResult Index()
    {
        return View();
    }

    [Route("token")]
    public async Task<string> Token()
    {
        return await GetTokenAsync();
    }

    /// <summary>
    /// Exchange your Azure subscription key for an access token
    /// </summary>
    private async Task<string> GetTokenAsync()
    {
        using (var client = new System.Net.Http.HttpClient())
        {
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", SubscriptionKey);
            using (var response = await client.PostAsync(Endpoint, null))
            {
                return await response.Content.ReadAsStringAsync();
            }
        }
    }
}
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
<script type='text/javascript' src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.0.0.1.js'></script>
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

    async function launchImmersiveReader() {
        const content = {
            title: document.getElementById('title').innerText,
            chunks: [ {
                content: document.getElementById('content').innerText,
                lang: 'en'
            } ]
        };

        const token = await getImmersiveReaderTokenAsync();
        ImmersiveReader.launchAsync(token, content, { uiZIndex: 1000000 });
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