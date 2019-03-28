---
title: 範例：使用預測端點以程式設計方式利用分類器測試影像 - 自訂視覺
titlesuffix: Azure Cognitive Services
description: 了解如何使用 API，以程式設計方式利用您的自訂視覺服務分類器來測試影像。
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: article
ms.date: 03/26/2019
ms.author: anroth
ms.openlocfilehash: 715fa526c83608c9922315e3a0d89b67b31e0d16
ms.sourcegitcommit: fbfe56f6069cba027b749076926317b254df65e5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2019
ms.locfileid: "58472715"
---
#  <a name="use-your-model-with-the-prediction-api"></a>使用您的模型使用預測 API

為模型定型之後，您可以藉由送出影像到預測 API，以程式設計方式測試它們。

> [!NOTE]
> 本文件示範如何使用 C#，送出影像到預測 API。 如需使用 API 的詳細資訊和範例，請參閱[預測 API 參考](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15) \(英文\)。

## <a name="publish-your-trained-iteration"></a>發行已訓練的反覆項目

從[自訂視覺網頁](https://customvision.ai) \(英文\) 選取您的專案，然後選取 [效能] 索引標籤。

若要提交給預測 API 的映像，將必須先發佈的預測，可藉由選取反覆項目的__發佈__並指定已發行的反覆項目名稱。 這可讓您的模型，可供預測 API，您的自訂視覺 Azure 資源的存取。 

![[效能] 索引標籤會顯示，以紅色矩形圍繞 [發行] 按鈕。](./media/use-prediction-api/unpublished-iteration.png)

已成功發行您的模型，您會看到左側資訊看板，以及在已發行的反覆項目描述中的反覆項目名稱在反覆項目的旁邊會出現 「 已發佈 」 標籤。

![[效能] 索引標籤會顯示，以紅色矩形圍繞已發佈的標籤，並在已發行的反覆項目名稱。](./media/use-prediction-api/published-iteration.png)

## <a name="get-the-url-and-prediction-key"></a>取得 URL 和預測金鑰

一旦已發行您的模型，您可以擷取選取使用預測 API 的相關資訊__預測 URL__。 這會開啟的對話方塊類似下列所示使用預測 API 的資訊包括__預測 URL__並__預測金鑰__。

![效能 索引標籤會顯示以紅色矩形圍繞預測 URL 按鈕。](./media/use-prediction-api/published-iteration-prediction-url.png)

![[效能] 索引標籤會顯示以紅色矩形圍繞使用的映像檔和預測機碼值的預測 URL 值。](./media/use-prediction-api/prediction-api-info.png)

> [!TIP]
> 您__預測金鑰__也可以在中找到[Azure 入口網站](https://portal.azure.com)頁面上自訂願景的 Azure 資源相關聯至您的專案下,__金鑰__。 

在對話方塊中，複製 應用程式中使用的下列資訊：

* __預測 URL__使用於__映像檔__。
* __預測金鑰__值。

## <a name="create-the-application"></a>建立應用程式

1. 從 Visual Studio 中，建立新的 C# 主控台應用程式。

1. 使用下列程式碼作為 __Program.cs__ 檔案的主體。

    > [!IMPORTANT]
    > 變更下列資訊：
    >
    > * 將__命名空間__設為專案的名稱。
    > * 設定__預測 Public-key__您稍早在開頭的行中擷取的值`client.DefaultRequestHeaders.Add("Prediction-Key",`。
    > * 設定__預測 URL__您稍早在開頭的行中擷取的值`string url =`。

    ```csharp
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Threading.Tasks;

    namespace CVSPredictionSample
    {
        public static class Program
        {
            public static void Main()
            {
                Console.Write("Enter image file path: ");
                string imageFilePath = Console.ReadLine();

                MakePredictionRequest(imageFilePath).Wait();

                Console.WriteLine("\n\nHit ENTER to exit...");
                Console.ReadLine();
            }

            public static async Task MakePredictionRequest(string imageFilePath)
            {
                var client = new HttpClient();

                // Request headers - replace this example key with your valid Prediction-Key.
                client.DefaultRequestHeaders.Add("Prediction-Key", "3b9dde6d1ae1453a86bfeb1d945300f2");

                // Prediction URL - replace this example URL with your valid Prediction URL.
                string url = "https://southcentralus.api.cognitive.microsoft.com/customvision/v3.0/Prediction/8622c779-471c-4b6e-842c-67a11deffd7b/classify/iterations/Cats%20vs.%20Dogs%20-%20Published%20Iteration%203/image";

                HttpResponseMessage response;

                // Request body. Try this sample with a locally stored image.
                byte[] byteData = GetImageAsByteArray(imageFilePath);

                using (var content = new ByteArrayContent(byteData))
                {
                    content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
                    response = await client.PostAsync(url, content);
                    Console.WriteLine(await response.Content.ReadAsStringAsync());
                }
            }

            private static byte[] GetImageAsByteArray(string imageFilePath)
            {
                FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }
        }
    }
    ```

## <a name="use-the-application"></a>使用應用程式

執行應用程式，您會在主控台中的影像檔中輸入的路徑。 預測 api 提交映像，並預測結果傳回為 JSON 文件。 下列 JSON 是回應範例。

```json
{
    "Id":"7796df8e-acbc-45fc-90b4-1b0c81b73639",
    "Project":"8622c779-471c-4b6e-842c-67a11deffd7b",
    "Iteration":"59ec199d-f3fb-443a-b708-4bca79e1b7f7",
    "Created":"2019-03-20T16:47:31.322Z",
    "Predictions":[
        {"TagId":"d9cb3fa5-1ff3-4e98-8d47-2ef42d7fb373","TagName":"cat", "Probability":1.0},
        {"TagId":"9a8d63fb-b6ed-4462-bcff-77ff72084d99","TagName":"dog", "Probability":0.1087869}
    ]
}
```

## <a name="next-steps"></a>後續步驟

[匯出模型以供行動裝置使用](export-your-model.md)

[開始使用.NET Sdk](csharp-tutorial.md)

[開始使用 Python Sdk](python-tutorial.md)

[開始使用 Java Sdk](java-tutorial.md)

[開始使用 Node Sdk](node-tutorial.md)

[開始使用 Go Sdk](go-tutorial.md)
