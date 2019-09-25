---
title: 將認知搜尋擴充的輸入欄位對應至輸出欄位 - Azure 搜尋服務
description: 擷取並擴充來源資料欄位，並對應至 Azure 搜尋服務索引中的輸出欄位。
manager: nitinme
author: luiscabrer
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.openlocfilehash: 4ceb8e6290e3e7f4fb552db13c97558db1c8c97e
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2019
ms.locfileid: "71265512"
---
# <a name="how-to-map-enriched-fields-to-a-searchable-index"></a>如何將擴充的欄位對應至可搜尋的索引

在本文中，您可以了解如何將擴充的輸入欄位對應至可搜尋索引中的輸出欄位。 一旦具備[定義的技能集](cognitive-search-defining-skillset.md)，即必須將直接提供值之任何技能的輸出欄位，對應至搜尋索引中的指定欄位。 將內容從擴充的文件移至索引需要欄位對應。


## <a name="use-outputfieldmappings"></a>使用 outputFieldMappings
若要對應欄位，請將 `outputFieldMappings` 新增至您的索引子定義，如下所示：

```http
PUT https://[servicename].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
api-key: [admin key]
Content-Type: application/json
```

要求主體已結構化，如下所示：

```json
{
    "name": "myIndexer",
    "dataSourceName": "myDataSource",
    "targetIndexName": "myIndex",
    "skillsetName": "myFirstSkillSet",
    "fieldMappings": [
        {
            "sourceFieldName": "metadata_storage_path",
            "targetFieldName": "id",
            "mappingFunction": {
                "name": "base64Encode"
            }
        }
    ],
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/content/organizations/*/description",
            "targetFieldName": "descriptions"
        },
        {
            "sourceFieldName": "/document/content/organizations",
            "targetFieldName": "orgNames"
        },
        {
            "sourceFieldName": "/document/content/sentiment",
            "targetFieldName": "sentiment"
        }
    ]
}
```
對於每個輸出欄位對應，請設定擴充欄位的名稱 (sourceFieldName)，以及索引中參考的欄位名稱 (targetFieldName)。

sourceFieldName 中的路徑可以代表一個元素或多個元素。 在上述範例中，```/document/content/sentiment``` 代表單一數值，而 ```/document/content/organizations/*/description``` 代表數個組織描述。 如果有數個元素，這些元素會「壓平合併」至包含每個元素的陣列。 更具體地說，對於 ```/document/content/organizations/*/description``` 範例，[描述] 欄位中的資料在進行編製索引之前，看起來像描述的平面陣列：

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```
## <a name="next-steps"></a>後續步驟
一旦您將擴充欄位對應至可搜尋欄位，即可為每個可搜尋欄位將欄位屬性設定為[索引定義的一部分](search-what-is-an-index.md)。

如需欄位對應的詳細資訊，請參閱 [Azure 搜尋服務索引子中的欄位對應](search-indexer-field-mappings.md)。
