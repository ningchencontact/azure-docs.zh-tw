---
title: 快速入門：使用 cURL 定型模型並擷取資料 - 表單辨識器
titleSuffix: Azure Cognitive Services
description: 在本快速入門中，您將搭配使用表單辨識器 REST API 和 cURL 來定型模型，並從表單中擷取資料。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: quickstart
ms.date: 04/15/2019
ms.author: pafarley
ms.openlocfilehash: 1990077e6466e08c1b6c463dafe9809b52df6d14
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537614"
---
# <a name="quickstart-train-a-form-recognizer-model-and-extract-form-data-by-using-the-rest-api-with-curl"></a>快速入門：搭配使用 REST API 與 cURL 將表單辨識器模型定型並擷取表單資料

在本快速入門中，您將搭配使用 Azure 表單辨識器的 REST API 與 cURL 來定型及評分表單，以擷取金鑰/值組和資料表。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>必要條件
若要完成此快速入門，您必須：
- 有權存取表單辨識器的有限存取預覽版。 若要存取此預覽服務，請先填寫並提交[表單辨識器存取要求](https://aka.ms/FormRecognizerRequestAccess)表單。
- 已安裝 [cURL](https://curl.haxx.se/windows/)。
- 至少有五個相同類型的表單。 您將使用此資料來定型模型。 您可以使用本快速入門的[範例資料集](https://go.microsoft.com/fwlink/?linkid=2090451)。 將資料上傳至 Azure Blob 儲存體帳戶的根目錄。

## <a name="create-a-form-recognizer-resource"></a>建立表單辨識器資源

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="train-a-form-recognizer-model"></a>定型表單辨識器模型

首先，您需要一組 Azure 儲存體 Blob 中的定型資料。 您至少要有五個類型/結構相同的已填妥表單 (PDF 文件和/或影像) 作為主要輸入資料。 或者，您可以使用單一空白表單和兩個已填寫的表單。 空白表單的檔案名稱必須包含「空白」一詞。 請參閱[為自訂模型建置訓練資料集](../build-training-data-set.md)，以獲得如何將訓練資料放在一起的祕訣和選項。

若要使用 Azure Blob 容器中的文件來訓練表單辨識器模型，請執行下列 cURL 命令以呼叫**訓練** API。 執行命令之前，請進行下列變更：

1. 將 `<Endpoint>` 取代為您從表單辨識器訂用帳戶金鑰中取得的端點。 您可以在表單辨識器的資源 [概觀]  索引標籤上找到此項目。
1. 將 `<subscription key>` 取代為您在先前的步驟中複製的訂用帳戶金鑰。
1. 將 `<SAS URL>` 取代為 Azure Blob 儲存體容器的共用存取簽章 (SAS) URL。 若要擷取此資訊，請開啟 Microsoft Azure 儲存體總管、以滑鼠右鍵按一下您的容器，然後選取 [取得共用存取簽章]  。 確定 [讀取]  和 [列出]  權限均已勾選，再按一下 [建立]  。 然後，複製 [URL]  區段的值。 其格式應該為：`https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`。

```bash
curl -X POST "https://<Endpoint>/formrecognizer/v1.0-preview/custom/train" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"source\": \""<SAS URL>"\"}"
```

您會收到 `200 (Success)` 回應及下列 JSON 輸出：

```json
{
  "modelId": "59e2185e-ab80-4640-aebc-f3653442617b",
  "trainingDocuments": [
    {
      "documentName": "Invoice_1.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    },
    {
      "documentName": "Invoice_2.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    },
    {
      "documentName": "Invoice_3.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    },
    {
      "documentName": "Invoice_4.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    },
    {
      "documentName": "Invoice_5.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    }
  ],
  "errors": []
}
```

請記下 `"modelId"` 值。 您在後續步驟中將用到此值。
  
## <a name="extract-key-value-pairs-and-tables-from-forms"></a>從表單擷取機碼值組與資料表

接下來，您會分析文件，並從中擷取金鑰/值組和資料表。 執行下列 cURL 命令以呼叫**模型 - 分析** API。 執行命令之前，請進行下列變更：

1. 將 `<Endpoint>` 取代為您從表單辨識器訂用帳戶金鑰中取得的端點。 您可以在表單辨識器的資源 [概觀]  索引標籤上找到此項目。
1. 將 `<modelID>` 取代為您在上一節中取得的模型識別碼。
1. 將 `<path to your form>` 取代為表單的檔案路徑 (例如，C:\temp\file.pdf)。
1. 將 `<file type>` 取代為檔案類型。 支援的類型：`application/pdf`、`image/jpeg`、`image/png`。
1. 將 `<subscription key>` 取代為訂用帳戶金鑰。


```bash
curl -X POST "https://<Endpoint>/formrecognizer/v1.0-preview/custom/models/<modelID>/analyze" -H "Content-Type: multipart/form-data" -F "form=@\"<path to your form>\";type=<file type>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

### <a name="examine-the-response"></a>檢查回應

成功的回應會以 JSON 的形式傳回。 這代表從表單中擷取的金鑰/值組與資料表：

```bash
{
  "status": "success",
  "pages": [
    {
      "number": 1,
      "height": 792,
      "width": 612,
      "clusterId": 0,
      "keyValuePairs": [
        {
          "key": [
            {
              "text": "Address:",
              "boundingBox": [
                57.4,
                683.1,
                100.5,
                683.1,
                100.5,
                673.7,
                57.4,
                673.7
              ]
            }
          ],
          "value": [
            {
              "text": "1 Redmond way Suite",
              "boundingBox": [
                57.4,
                671.3,
                154.8,
                671.3,
                154.8,
                659.2,
                57.4,
                659.2
              ],
              "confidence": 0.86
            },
            {
              "text": "6000 Redmond, WA",
              "boundingBox": [
                57.4,
                657.1,
                146.9,
                657.1,
                146.9,
                645.5,
                57.4,
                645.5
              ],
              "confidence": 0.86
            },
            {
              "text": "99243",
              "boundingBox": [
                57.4,
                643.4,
                85,
                643.4,
                85,
                632.3,
                57.4,
                632.3
              ],
              "confidence": 0.86
            }
          ]
        },
        {
          "key": [
            {
              "text": "Invoice For:",
              "boundingBox": [
                316.1,
                683.1,
                368.2,
                683.1,
                368.2,
                673.7,
                316.1,
                673.7
              ]
            }
          ],
          "value": [
            {
              "text": "Microsoft",
              "boundingBox": [
                374,
                687.9,
                418.8,
                687.9,
                418.8,
                673.7,
                374,
                673.7
              ],
              "confidence": 1
            },
            {
              "text": "1020 Enterprise Way",
              "boundingBox": [
                373.9,
                673.5,
                471.3,
                673.5,
                471.3,
                659.2,
                373.9,
                659.2
              ],
              "confidence": 1
            },
            {
              "text": "Sunnayvale, CA 87659",
              "boundingBox": [
                373.8,
                659,
                479.4,
                659,
                479.4,
                645.5,
                373.8,
                645.5
              ],
              "confidence": 1
            }
          ]
        }
      ],
      "tables": [
        {
          "id": "table_0",
          "columns": [
            {
              "header": [
                {
                  "text": "Invoice Number",
                  "boundingBox": [
                    38.5,
                    585.2,
                    113.4,
                    585.2,
                    113.4,
                    575.8,
                    38.5,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "34278587",
                    "boundingBox": [
                      38.5,
                      547.3,
                      82.8,
                      547.3,
                      82.8,
                      537,
                      38.5,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            },
            {
              "header": [
                {
                  "text": "Invoice Date",
                  "boundingBox": [
                    139.7,
                    585.2,
                    198.5,
                    585.2,
                    198.5,
                    575.8,
                    139.7,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "6/18/2017",
                    "boundingBox": [
                      139.7,
                      546.8,
                      184,
                      546.8,
                      184,
                      537,
                      139.7,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            },
            {
              "header": [
                {
                  "text": "Invoice Due Date",
                  "boundingBox": [
                    240.5,
                    585.2,
                    321,
                    585.2,
                    321,
                    575.8,
                    240.5,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "6/24/2017",
                    "boundingBox": [
                      240.5,
                      546.8,
                      284.8,
                      546.8,
                      284.8,
                      537,
                      240.5,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            },
            {
              "header": [
                {
                  "text": "Charges",
                  "boundingBox": [
                    341.3,
                    585.2,
                    381.2,
                    585.2,
                    381.2,
                    575.8,
                    341.3,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "$56,651.49",
                    "boundingBox": [
                      387.6,
                      546.4,
                      437.5,
                      546.4,
                      437.5,
                      537,
                      387.6,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            },
            {
              "header": [
                {
                  "text": "VAT ID",
                  "boundingBox": [
                    442.1,
                    590,
                    474.8,
                    590,
                    474.8,
                    575.8,
                    442.1,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "PT",
                    "boundingBox": [
                      447.7,
                      550.6,
                      460.4,
                      550.6,
                      460.4,
                      537,
                      447.7,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            }
          ]
        }
      ]
    }
  ],
  "errors": []
}
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已搭配使用表單辨識器 REST API 和 cURL 來定型模型，並在範例案例中加以執行。 接下來，請參閱參考文件來深入探索表單辨識器 API。

> [!div class="nextstepaction"]
> [REST API 參考文件](https://aka.ms/form-recognizer/api)
