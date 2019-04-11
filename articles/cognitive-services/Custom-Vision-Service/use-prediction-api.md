---
title: 使用預測端點以程式設計方式利用分類器測試影像 - 自訂視覺
titlesuffix: Azure Cognitive Services
description: 了解如何使用 API，以程式設計方式利用您的自訂視覺服務分類器來測試影像。
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: article
ms.date: 04/02/2019
ms.author: anroth
ms.openlocfilehash: 1ee6edbf49bbcd2014afcf29ed3b737168a3b5bc
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/05/2019
ms.locfileid: "59046065"
---
# <a name="use-your-model-with-the-prediction-api"></a>使用您的模型使用預測 API

您已訓練模型之後，您可以在以程式設計方式將它們提交到預測 API 端點中測試映像。

> [!NOTE]
> 本文件示範如何使用 C#，送出影像到預測 API。 如需詳細資訊和範例，請參閱 <<c0> [ 預測 API 參考](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15)。

## <a name="publish-your-trained-iteration"></a>發行已訓練的反覆項目

從[自訂視覺網頁](https://customvision.ai) \(英文\) 選取您的專案，然後選取 [效能] 索引標籤。

若要提交給預測 API 的映像，將必須先發佈的預測，可藉由選取反覆項目的__發佈__並指定已發行的反覆項目名稱。 這可讓您的模型來預測 API，您的自訂視覺 Azure 資源的存取。

![[效能] 索引標籤會顯示，以紅色矩形圍繞 [發行] 按鈕。](./media/use-prediction-api/unpublished-iteration.png)

一旦成功發行您的模型，您會看到左側資訊看板，反覆項目的旁邊會出現 「 已發佈 」 標籤，以及其名稱會出現在反覆項目的描述。

![[效能] 索引標籤會顯示，以紅色矩形圍繞已發佈的標籤，並在已發行的反覆項目名稱。](./media/use-prediction-api/published-iteration.png)

## <a name="get-the-url-and-prediction-key"></a>取得 URL 和預測金鑰

一旦已發行您的模型，您可以藉由選取擷取所需的資訊__預測 URL__。 這會開啟一個對話方塊，以使用預測 API、 資訊包括__預測 URL__並__預測金鑰__。

![效能 索引標籤會顯示以紅色矩形圍繞預測 URL 按鈕。](./media/use-prediction-api/published-iteration-prediction-url.png)

![[效能] 索引標籤會顯示以紅色矩形圍繞使用的映像檔和預測機碼值的預測 URL 值。](./media/use-prediction-api/prediction-api-info.png)

> [!TIP]
> 您__預測金鑰__也可以在中找到[Azure 入口網站](https://portal.azure.com)頁面上自訂願景的 Azure 資源相關聯與您的專案下,__金鑰__刀鋒視窗。

本指南中，您會使用本機映像，所以請複製下方的 URL**如果您有一個映像檔案**到暫存位置。 複製對應__預測金鑰__以及值。

## <a name="create-the-application"></a>建立應用程式

1. 在 Visual Studio 中建立新的C#主控台應用程式。

1. 使用下列程式碼作為 __Program.cs__ 檔案的主體。

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
                client.DefaultRequestHeaders.Add("Prediction-Key", "<Your prediction key>");

                // Prediction URL - replace this example URL with your valid Prediction URL.
                string url = "<Your prediction URL>";

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

1. 變更下列資訊：
   * 設定`namespace`欄位設為您的專案名稱。
   * 將預留位置`<Your prediction key>`具有您稍早擷取的金鑰值。
   * 將預留位置`<Your prediction URL>`您稍早擷取的 url。

## <a name="run-the-application"></a>執行應用程式

當您執行應用程式時，系統會提示您輸入到主控台中的影像檔的路徑。 預測 api，然後提交映像，並預測結果傳回為 JSON 格式的字串。 以下是範例回應。

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

本指南中，您已了解如何提交映像發佈至您的自訂映像分類/偵測器，並接收回應，以程式設計方式使用C#SDK。 接下來，了解如何完成端對端案例使用C#，或開始使用不同的語言 SDK。

* [快速入門：.NET SDK](csharp-tutorial.md)
* [快速入門：Python SDK](python-tutorial.md)
* [快速入門：Java SDK](java-tutorial.md)
* [快速入門：Node SDK](node-tutorial.md)
* [快速入門：Go SDK](go-tutorial.md)
