---
title: 範例：使用 Bing 實體搜尋 API-Azure 搜尋服務中建立自訂的認知技能
description: 示範自訂技能，對應至 Azure 搜尋服務中的認知搜尋編製索引管線中使用 Bing 實體搜尋服務。
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 7d90f46ada9b9453b4c1516a4a898456dc73b8e7
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672153"
---
# <a name="example-create-a-custom-skill-using-the-bing-entity-search-api"></a>範例：建立自訂的技術，使用 Bing 實體搜尋 API

在此範例中，了解如何建立 web API 自訂技能。 這項技術會接受位置、 公用的圖形和組織，並傳回它們的描述。 此範例會使用[Azure Function](https://azure.microsoft.com/services/functions/)包裝[Bing 實體搜尋 API](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/) ，以便實作自訂的技能介面。

## <a name="prerequisites"></a>必要條件

+ 了解[自訂技能介面](cognitive-search-custom-skill-interface.md)發行項，如果您不熟悉如何使用自訂的技術應該實作的輸入/輸出介面。

+ [!INCLUDE [cognitive-services-bing-entity-search-signup-requirements](../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

+ 安裝[Visual Studio 2019](https://www.visualstudio.com/vs/)或更新版本，包括 Azure 開發工作負載。

## <a name="create-an-azure-function"></a>建立 Azure 函式

雖然此範例會使用 Azure Function 裝載 web API，不需要它。  只要您符合[認知技能的介面需求](cognitive-search-custom-skill-interface.md)，採取的方式並不重要。 不過，Azure Functions 能讓您輕鬆建立自訂技能。

### <a name="create-a-function-app"></a>建立函數應用程式

1. 在 Visual Studio 中，從 [檔案] 功能表中選取 [新增]   > [專案]  。

1. 在 [新增專案] 對話方塊中，選取 [已安裝]  ，展開 [Visual C#]   > [雲端]  ，選取 [Azure Functions]  ，輸入專案的 [名稱]，然後選取 [確定]  。 函式應用程式名稱必須是有效C#命名空間，因此不會使用底線、 連字號或任何其他非英數字元。

1. 選取  **Azure Functions v2 (.NET Core)** 。 您也可以使用第 1 版來執行，但下方撰寫的程式碼以 v2 範本為基礎。

1. 選取 [HTTP 觸發程序]  類型

1. 對於儲存體帳戶，您可以選取 [無]  ，因為這個函式不需要任何儲存體。

1. 選取 [確定]  以建立函式專案和 HTTP 觸發函式。

### <a name="modify-the-code-to-call-the-bing-entity-search-service"></a>修改程式碼以呼叫 Bing 實體搜尋服務

Visual Studio 會建立一個專案，其中的類別包含所選函式類型的重複使用程式碼。 方法上的 *FunctionName* 屬性會設定函式名稱。 *HttpTrigger* 屬性會指定由 HTTP 要求觸發函式。

現在，將檔案 *Function1.cs* 的所有內容取代為下列程式碼：

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;

namespace SampleSkills
{
    /// <summary>
    /// Sample custom skill that wraps the Bing entity search API to connect it with a 
    /// cognitive search pipeline.
    /// </summary>
    public static class BingEntitySearch
    {
        #region Credentials
        // IMPORTANT: Make sure to enter your credential and to verify the API endpoint matches yours.
        static readonly string bingApiEndpoint = "https://api.cognitive.microsoft.com/bing/v7.0/entities/";
        static readonly string key = "<enter your api key here>";  
        #endregion

        #region Class used to deserialize the request
        private class InputRecord
        {
            public class InputRecordData
            {
                public string Name { get; set; }
            }

            public string RecordId { get; set; }
            public InputRecordData Data { get; set; }
        }

        private class WebApiRequest
        {
            public List<InputRecord> Values { get; set; }
        }
        #endregion

        #region Classes used to serialize the response

        private class OutputRecord
        {
            public class OutputRecordData
            {
                public string Name { get; set; } = "";
                public string Description { get; set; } = "";
                public string Source { get; set; } = "";
                public string SourceUrl { get; set; } = "";
                public string LicenseAttribution { get; set; } = "";
                public string LicenseUrl { get; set; } = "";
            }

            public class OutputRecordMessage
            {
                public string Message { get; set; }
            }

            public string RecordId { get; set; }
            public OutputRecordData Data { get; set; }
            public List<OutputRecordMessage> Errors { get; set; }
            public List<OutputRecordMessage> Warnings { get; set; }
        }

        private class WebApiResponse
        {
            public List<OutputRecord> Values { get; set; }
        }
        #endregion

        #region Classes used to interact with the Bing API
        private class BingResponse
        {
            public BingEntities Entities { get; set; }
        }
        private class BingEntities
        {
            public BingEntity[] Value { get; set; }
        }

        private class BingEntity
        {
            public class EntityPresentationinfo
            {
                public string[] EntityTypeHints { get; set; }
            }

            public class License
            {
                public string Url { get; set; }
            }

            public class ContractualRule
            {
                public string _type { get; set; }
                public License License { get; set; }
                public string LicenseNotice { get; set; }
                public string Text { get; set; }
                public string Url { get; set; }
            }

            public ContractualRule[] ContractualRules { get; set; }
            public string Description { get; set; }
            public string Name { get; set; }
            public EntityPresentationinfo EntityPresentationInfo { get; set; }
        }
        #endregion

        #region The Azure Function definition

        [FunctionName("EntitySearch")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("Entity Search function: C# HTTP trigger function processed a request.");

            var response = new WebApiResponse
            {
                Values = new List<OutputRecord>()
            };

            string requestBody = new StreamReader(req.Body).ReadToEnd();
            var data = JsonConvert.DeserializeObject<WebApiRequest>(requestBody);

            // Do some schema validation
            if (data == null)
            {
                return new BadRequestObjectResult("The request schema does not match expected schema.");
            }
            if (data.Values == null)
            {
                return new BadRequestObjectResult("The request schema does not match expected schema. Could not find values array.");
            }

            // Calculate the response for each value.
            foreach (var record in data.Values)
            {
                if (record == null || record.RecordId == null) continue;

                OutputRecord responseRecord = new OutputRecord
                {
                    RecordId = record.RecordId
                };

                try
                {
                    responseRecord.Data = GetEntityMetadata(record.Data.Name).Result;
                }
                catch (Exception e)
                {
                    // Something bad happened, log the issue.
                    var error = new OutputRecord.OutputRecordMessage
                    {
                        Message = e.Message
                    };

                    responseRecord.Errors = new List<OutputRecord.OutputRecordMessage>
                    {
                        error
                    };
                }
                finally
                {
                    response.Values.Add(responseRecord);
                }
            }

            return (ActionResult)new OkObjectResult(response);
        }

        #endregion

        #region Methods to call the Bing API
        /// <summary>
        /// Gets metadata for a particular entity based on its name using Bing Entity Search
        /// </summary>
        /// <param name="entityName">The name of the entity to extract data for.</param>
        /// <returns>Asynchronous task that returns entity data. </returns>
        private async static Task<OutputRecord.OutputRecordData> GetEntityMetadata(string entityName)
        {
            var uri = bingApiEndpoint + "?q=" + entityName + "&mkt=en-us&count=10&offset=0&safesearch=Moderate";
            var result = new OutputRecord.OutputRecordData();

            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage {
                Method = HttpMethod.Get,
                RequestUri = new Uri(uri)
            })
            {
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                HttpResponseMessage response = await client.SendAsync(request);
                string responseBody = await response?.Content?.ReadAsStringAsync();

                BingResponse bingResult = JsonConvert.DeserializeObject<BingResponse>(responseBody);
                if (bingResult != null)
                {
                    // In addition to the list of entities that could match the name, for simplicity let's return information
                    // for the top match as additional metadata at the root object.
                    return AddTopEntityMetadata(bingResult.Entities?.Value);
                }
            }

            return result;
        }

        private static OutputRecord.OutputRecordData AddTopEntityMetadata(BingEntity[] entities)
        {
            if (entities != null)
            {
                foreach (BingEntity entity in entities.Where(
                    entity => entity?.EntityPresentationInfo?.EntityTypeHints != null
                        && (entity.EntityPresentationInfo.EntityTypeHints[0] == "Person"
                            || entity.EntityPresentationInfo.EntityTypeHints[0] == "Organization"
                            || entity.EntityPresentationInfo.EntityTypeHints[0] == "Location")
                        && !String.IsNullOrEmpty(entity.Description)))
                {
                    var rootObject = new OutputRecord.OutputRecordData
                    {
                        Description = entity.Description,
                        Name = entity.Name
                    };

                    if (entity.ContractualRules != null)
                    {
                        foreach (var rule in entity.ContractualRules)
                        {
                            switch (rule._type)
                            {
                                case "ContractualRules/LicenseAttribution":
                                    rootObject.LicenseAttribution = rule.LicenseNotice;
                                    rootObject.LicenseUrl = rule.License.Url;
                                    break;
                                case "ContractualRules/LinkAttribution":
                                    rootObject.Source = rule.Text;
                                    rootObject.SourceUrl = rule.Url;
                                    break;
                            }
                        }
                    }

                    return rootObject;
                }
            }

            return new OutputRecord.OutputRecordData();
        }
        #endregion
    }
}
```

請務必輸入您自己*金鑰*中的值`key`時註冊 Bing 實體搜尋 API 取得索引鍵為基礎的常數。

此範例會在單一檔案，以便包含所有必要的程式碼。 您可以找到該相同的技術，在稍微更結構化的版本[電源技術存放庫](https://github.com/Azure-Samples/azure-search-power-skills/tree/master/Text/BingEntitySearch)。

當然，您可以重新命名的檔案`Function1.cs`至`BingEntitySearch.cs`。

## <a name="test-the-function-from-visual-studio"></a>從 Visual Studio 測試函式

按 **F5** 以執行程式並測試函式行為。 在此情況下，我們將使用下列函式來查詢兩個實體。 使用 Postman 或 Fiddler 來發出呼叫，如下所示：

```http
POST https://localhost:7071/api/EntitySearch
```

### <a name="request-body"></a>Request body
```json
{
    "values": [
        {
            "recordId": "e1",
            "data":
            {
                "name":  "Pablo Picasso"
            }
        },
        {
            "recordId": "e2",
            "data":
            {
                "name":  "Microsoft"
            }
        }
    ]
}
```

### <a name="response"></a>Response
您應該會看到類似於以下範例的回應：

```json
{
    "values": [
        {
            "recordId": "e1",
            "data": {
                "name": "Pablo Picasso",
                "description": "Pablo Ruiz Picasso was a Spanish painter [...]",
                "source": "Wikipedia",
                "sourceUrl": "http://en.wikipedia.org/wiki/Pablo_Picasso",
                "licenseAttribution": "Text under CC-BY-SA license",
                "licenseUrl": "http://creativecommons.org/licenses/by-sa/3.0/"
            },
            "errors": null,
            "warnings": null
        },
        "..."
    ]
}
```

## <a name="publish-the-function-to-azure"></a>將函式發佈至 Azure

當您滿意的函數行為時，您可以發行分支。

1. 在 [方案總管]  中，以滑鼠右鍵按一下專案並選取 [發佈]  。 選擇 [建立新項目]   > [發佈]  。

1. 如果您尚未將 Visual Studio 連線到您的 Azure 帳戶，請選取 [新增帳戶...]  。

1. 遵循螢幕上的提示進行。 系統會要求您指定您的 app service、 Azure 訂用帳戶、 資源群組、 主控方案和您想要使用的儲存體帳戶的唯一名稱。 如果您沒有上述項目，則可以建立新的資源群組、新的主控方案和儲存體帳戶。 完成後，請選取 [建立] 

1. 完成部署之後，請注意網站 URL。 這是 Azure 中的函數應用程式的位址。 

1. 在  [Azure 入口網站](https://portal.azure.com)、 瀏覽至資源群組，並尋找`EntitySearch`您發佈的函式。 在 [管理]  區段下，應該會看到主機金鑰。 選取 [預設]  主機金鑰的 [複製]  圖示。  

## <a name="test-the-function-in-azure"></a>在 Azure 測試函式

有了預設主機金鑰之後，請如下列所示測試您的函式：

```http
POST https://[your-entity-search-app-name].azurewebsites.net/api/EntitySearch?code=[enter default host key here]
```

### <a name="request-body"></a>要求本文
```json
{
    "values": [
        {
            "recordId": "e1",
            "data":
            {
                "name":  "Pablo Picasso"
            }
        },
        {
            "recordId": "e2",
            "data":
            {
                "name":  "Microsoft"
            }
        }
    ]
}
```

此範例應該產生相同的結果時所看到先前在本機環境中執行函式。

## <a name="connect-to-your-pipeline"></a>連線到您的管線
有了新的自訂技能之後，就可以將它加入您的技能集。 下列範例會示範如何呼叫來新增描述至文件 （這無法擴充至上位置與人員也能運作） 中的組織的技能。 取代`[your-entity-search-app-name]`與應用程式的名稱。

```json
{
    "skills": [
      "[... your existing skills remain here]",  
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "Our new Bing entity search custom skill",
        "uri": "https://[your-entity-search-app-name].azurewebsites.net/api/EntitySearch?code=[enter default host key here]",
          "context": "/document/merged_content/organizations/*",
          "inputs": [
            {
              "name": "name",
              "source": "/document/merged_content/organizations/*"
            }
          ],
          "outputs": [
            {
              "name": "description",
              "targetName": "description"
            }
          ]
      }
  ]
}
```

在這裡，我們要計算的內建[實體辨識技能](cognitive-search-skill-entity-recognition.md)會出現在技能集，以及輔助文件的組織清單。 如需參考，以下是會在產生的資料，我們需要足夠的實體擷取技能組態：

```json
{
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
    "name": "#1",
    "description": "Organization name extraction",
    "context": "/document/merged_content",
    "categories": [ "Organization" ],
    "defaultLanguageCode": "en",
    "inputs": [
        {
            "name": "text",
            "source": "/document/merged_content"
        },
        {
            "name": "languageCode",
            "source": "/document/language"
        }
    ],
    "outputs": [
        {
            "name": "organizations",
            "targetName": "organizations"
        }
    ]
},
```

## <a name="next-steps"></a>後續步驟
恭喜您！ 您已建立您第一個自訂豐富器新增。 現在您可以遵循相同的模式，新增自己的自訂功能。 

+ [將自訂技能新增至認知搜尋管線](cognitive-search-custom-skill-interface.md)
+ [如何定義技能集](cognitive-search-defining-skillset.md)
+ [建立技能集 (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [如何對應擴充的欄位](cognitive-search-output-field-mapping.md)
