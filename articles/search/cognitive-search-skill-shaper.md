---
title: 增強認知搜尋技能 - Azure 搜尋服務
description: 從非結構化資料中擷取中繼資料和結構化資訊，並在 Azure 搜尋服務擴充管線中塑形為複雜類型。
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 058b6c979346d9dcce36940432d0e222e919dba9
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/11/2019
ms.locfileid: "65540836"
---
#   <a name="shaper-cognitive-skill"></a>塑形器認知技能

**Shaper**技能會合併到數種輸入[複雜型別](search-howto-complex-data-types.md)來擴充管線中稍後參考。 **塑形器**技能可讓您實質上建立結構、定義該結構成員的名稱，並將值指派給每個成員。 在搜尋案例中有用的彙總欄位的範例包括將第一個和最後一個名稱結合成單一結構、 縣 （市） 和成單一結構或名稱的狀態和成單一結構來建立唯一的身分識別的出生日期。

API 版本會決定可以達到塑造您的深度。 

| API 版本 | 塑造行為 | 
|-------------|-------------------|
| 2019-05-06-preview 版 REST API （不支援.NET SDK） | 複雜物件，多個可以在其中一個深度層級**Shaper**技能定義。 |
| 2019-05-06 * * （正式推出） 2017年-11-11-預覽| 複雜的物件，一個層級深度。 多層級的圖形需要將 shaper 的幾個步驟鏈結在一起。|

所提供`api-version=2019-05-06-Preview`，則**Shaper**中所述的技巧[案例 3](#nested-complex-types)新增選擇性*sourceContext*的輸入屬性。 *來源*並*sourceContext*屬性互斥。 如果輸入是在技能的內容，只需使用*來源*。 如果輸入是在*不同*技能內容中，使用內容*sourceContext*。 *SourceContext*需要您定義的巢狀的輸入與處理做為來源的特定項目。 

在回應中，所有的 API 版本，輸出名稱是一律"output"。 就內部而言，管線可以對應不同的名稱，例如"analyzedText 」 所示，在以下範例中，但**Shaper**本身的技能在回應中傳回 「 輸出 」。 如果您正在對擴充文件進行偵錯，並發現命名差異，或如果您建置自訂技能並自行建構回應，這可能十分重要。

> [!NOTE]
> **Shaper**技能未繫結至認知服務 API，您不需支付使用它。 不過，您仍然應該[連結認知服務資源](cognitive-search-attach-cognitive-services.md)，以覆寫限制您每天少量擴充的**免費**資源選項。

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ShaperSkill

## <a name="scenario-1-complex-types"></a>案例 1： 複雜型別

假設您要建立一個稱為 *analyzedText* 的結構，此結構有兩個成員，分別為：*text* 和 *sentiment*。 在 Azure 搜尋服務索引，稱為多部分的可搜尋欄位*複雜型別*通常會建立來源資料具有對應的複雜結構對應至它時。

不過，用於建立複雜類型的另一種方法是透過**Shaper**技能。 這項技術在中包含的技能組合，技能集處理期間的記憶體中作業可輸出具有巢狀結構，然後在索引中的複雜型別對應的資料圖形。 

下列範例遊戲定義提供的成員名稱做為輸入。 


```json
{
  "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
  "context": "/document/content/phrases/*",
  "inputs": [
    {
      "name": "text",
      "source": "/document/content/phrases/*"
    },
    {
      "name": "sentiment",
      "source": "/document/content/phrases/*/sentiment"
    }
  ],
  "outputs": [
    {
      "name": "output",
      "targetName": "analyzedText"
    }
  ]
}
```

### <a name="sample-index"></a>範例索引

索引子，會叫用的技能組合和索引子需要索引。 在索引中的複雜的欄位表示看起來可能如下列範例所示。 

```json

    "name": "my-index",
    "fields": [
        {   "name": "myId", "type": "Edm.String", "key": true, "filterable": true   },
        {   "name": "analyzedText", "type": "Edm.ComplexType",
            "fields": [{
                    "name": "text",
                    "type": "Edm.String",
                    "filterable": false,
                    "sortable": false,
                    "facetable": false,
                    "searchable": true  },
          {
                    "name": "sentiment",
                    "type": "Edm.Double",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
```

### <a name="skill-input"></a>技能輸入

傳入的 JSON 文件提供可用的輸入，這個**Shaper**技能可能是：

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "text": "this movie is awesome",
                "sentiment": 0.9
            }
        }
    ]
}
```


### <a name="skill-output"></a>技能輸出

**塑形器**技能會產生一個稱為 *analyzedText* 的新元素。此新元素結合了 *text* 和 *sentiment* 的元素。 此輸出符合索引結構描述。 它會匯入並在 Azure 搜尋服務索引編製索引。

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
            "analyzedText": 
              {
                "text": "this movie is awesome" ,
                "sentiment": 0.9
              }
           }
      }
    ]
}
```

## <a name="scenario-2-input-consolidation"></a>案例 2： 輸入彙總

在另一個範例中，假設在不同的管線處理階段，您已經擷取書名和書本不同頁面上的章節標題。 您現在可以建立由這些各種輸入所組成的單一結構。

**Shaper**技能定義，在此案例看起來如下列範例所示：

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document",
    "inputs": [
        {
            "name": "title",
            "source": "/document/content/title"
        },
        {
            "name": "chapterTitles",
            "source": "/document/content/pages/*/chapterTitles/*/title"
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "titlesAndChapters"
        }
    ]
}
```

### <a name="skill-output"></a>技能輸出
在此情況下， **Shaper**壓平合併所有的章節標題，建立一個單一的陣列。 

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "titlesAndChapters": {
                    "title": "How to be happy",
                    "chapterTitles": [
                        "Start young",
                        "Laugh often",
                        "Eat, sleep and exercise"
                    ]
                }
            }
        }
    ]
}
```

<a name="nested-complex-types"></a>

## <a name="scenario-3-input-consolidation-from-nested-contexts"></a>案例 3： 從巢狀內容的輸入彙總

> [!NOTE]
> 巢狀結構中支援[REST API 版本 2019年-05-06-Preview](search-api-preview.md)可用於[知識市集](knowledge-store-concept-intro.md)或在 Azure 搜尋服務索引中。

假設您有標題、 章節和活頁簿的內容和實體辨識和金鑰的片語上執行內容而現在彙總結果需要從不同的技巧，進入 「 單一 」 圖形與章節名稱、 實體和關鍵片語。

**Shaper**技能定義，在此案例看起來如下列範例所示：

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document",
    "inputs": [
        {
            "name": "title",
            "source": "/document/content/title"
        },
        {
            "name": "chapterTitles",
            "sourceContext": "/document/content/pages/*/chapterTitles/*",
            "inputs": [
              {
                  "name": "title",
                  "source": "/document/content/pages/*/chapterTitles/*/title"
              },
              {
                  "name": "number",
                  "source": "/document/content/pages/*/chapterTitles/*/number"
              }
            ]
        }

    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "titlesAndChapters"
        }
    ]
}
```

### <a name="skill-output"></a>技能輸出
在此情況下， **Shaper**建立複雜型別。 此結構存在記憶體中。 如果您想要將它儲存到的知識存放區，您應該建立在您定義儲存體特性的技能組合中的投影。

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "titlesAndChapters": {
                    "title": "How to be happy",
                    "chapterTitles": [
                      { "title": "Start young", "number": 1},
                      { "title": "Laugh often", "number": 2},
                      { "title": "Eat, sleep and exercise", "number: 3}
                    ]
                }
            }
        }
    ]
}
```

## <a name="see-also"></a>請參閱

+ [預先定義的技能](cognitive-search-predefined-skills.md)
+ [如何定義技能集](cognitive-search-defining-skillset.md) (英文)
+ [如何使用複雜型別](search-howto-complex-data-types.md)
+ [知識存放區概觀](knowledge-store-concept-intro.md)
+ [如何開始使用知識存放區](knowledge-store-howto.md)