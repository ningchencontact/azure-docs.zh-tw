---
title: 偵測特定網域內容 - 電腦視覺
titleSuffix: Azure Cognitive Services
description: 了解如何指定影像分類網域，以傳回影像的詳細資訊。
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 50942634bd50974453c242d1980db9fc589bd47e
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2018
ms.locfileid: "53579204"
---
# <a name="detecting-domain-specific-content"></a>偵測特定網域內容

除了標記與最上層分類以外，電腦視覺也支援特殊 (或特定領域) 資訊。 特殊資訊可作為獨立的方法來實作，或透過高階分類實作。 它可作為透過特定領域模型的新增而進一步細分 86 類別分類的機制。

使用特定領域模型時可採用兩種選項：

* 已設定範圍的分析  
  藉由叫用 HTTP POST 呼叫，僅分析選擇的模型。 如果您知道您想要使用的是哪一個模型，請指定模型的名稱。 您只能取得與該模型相關的資訊。 例如，您可以使用此選項，針對名人辨識進行搜尋。 其回應會包含可能符合的名人清單，及其信賴分數。
* 強化分析  
  進行分析，並提供與 86 類別分類中的類別有關的其他詳細資料。 此選項可運用在使用者除了一或多個特定領域模型中的詳細資料以外，還想要取得一般影像分析的應用程式中。 叫用此方法時，必須先呼叫 86 類別分類器。 如果有任何類別符合已知或比對模型的類別，則會繼續進行第二輪的分類器叫用。 例如，如果 HTTP POST 呼叫的 `details` 參數設為「全部」或包含「名人」，則此方法會在呼叫 86 類別分類器之後呼叫名人分類器。 如果影像分類為 `people_` 或該類別的子目錄，則會呼叫名人分類器。

## <a name="listing-domain-specific-models"></a>列出特定領域模型

您可以依「電腦視覺」列出特定領域的模型。 目前，電腦視覺支援下列特定領域模型來偵測特定領域內容：

| Name | 說明 |
|------|-------------|
| 名人 | 支援 `people_` 類別中分類影像的名人辨識 |
| 地標 | 支援 `outdoor_` 或 `building_` 類別中分類影像的地標辨識 |

### <a name="domain-model-list-example"></a>領域模型清單範例

下列 JSON 回應中列出「電腦視覺」列出特定領域的模型。

```json
{
    "models": [
        {
            "name": "celebrities",
            "categories": ["people_", "人_", "pessoas_", "gente_"]
        },
        {
            "name": "landmarks",
            "categories": ["outdoor_", "户外_", "屋外_", "aoarlivre_", "alairelibre_",
                "building_", "建筑_", "建物_", "edifício_"]
        }
    ]
}
```

## <a name="next-steps"></a>後續步驟

了解[分類影像](concept-categorizing-images.md)的相關概念。