---
title: 使用知識存放區-Azure 搜尋服務中的投影
description: 儲存並塑造您已充實的資料，從搜尋以外的情況下使用 AI 編製索引的管線
manager: eladz
author: vkurpad
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: vikurpad
ms.custom: seomay2019
ms.openlocfilehash: 3ab5ffafd1b20eb0e3e453d3e730840baf9233e1
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028361"
---
# <a name="working-with-projections-in-a-knowledge-store-in-azure-search"></a>使用 Azure 搜尋服務中的知識存放區中的投影

Azure 搜尋服務可讓透過 AI 認知技能和編製索引的一部分的自訂技術內容擴充。 類將結構新增至您的文件，並讓搜尋更有效率。 在許多情況下，很適用於搜尋服務中，例如，針對知識採礦以外的案例的豐富的文件。

投影、 的元件[知識存放區 （預覽）](knowledge-store-concept-intro.md)，是豐富知識採礦用途的實體儲存體可以儲存的文件的檢視。 投影可讓您配合您的需求，保留關聯性，讓 Power BI 等工具可以讀取透過任何額外資料的圖形到 「 專案 」 您的資料。 

投射可以是表格式、 資料儲存在資料列和資料行，在 Azure 資料表儲存體，或在 Azure Blob 儲存體中儲存的 JSON 物件。 您可以定義多個預測的資料，如擴充它。 當您想針對個別的使用案例採用不同的形狀相同的資料時，這非常有用。 

知識存放區支援兩種類型的投影：

+ **資料表**：針對最能代表做為資料列和資料行的資料，資料表投影可讓您在資料表儲存體中定義的結構描述化的圖形或投影。 

+ **物件**:當您需要的資料和類的 JSON 表示法時，物件投影會儲存為 blob。

若要查看內容中定義的投影，逐步執行[如何開始使用知識存放區](knowledge-store-howto.md)

## <a name="projection-groups"></a>投影群組

在某些情況下，您必須將豐富的資料，以不同的圖形，以符合不同目標的專案。 知識存放區可讓您定義多個群組的投影。 投影群組具有以下的主要特性有互斥性和關聯。

### <a name="mutually-exclusivity"></a>相互為獨佔

投影至投影中的其他群組的資料投影至單一群組的所有內容都無關。 這表示您可以有相同的資料成形以不同的方式，但在每個投影群組中重複。 

強制執行投影群組中的一個條件約束是與投影群組的投影類型的互斥現象。 您只可以定義資料表投影或單一群組內的物件投影。 如果您想要資料表和物件，定義一個投影群組的資料表，與第二個投影群組物件。

### <a name="relatedness"></a>關聯

投射單一投影群組內的所有內容會都保留在資料內的關聯性。 關聯性根據產生的金鑰，以及每個子節點會保留父節點的參考。 關聯性並不會跨越投影群組和資料表或一個投影群組中建立的物件有其他投射群組中所產生的資料沒有關聯性。

## <a name="input-shaping"></a>輸入成形
取得正確的圖形或結構中的資料是以有效的索引鍵使用，是資料表或物件。 圖形或結構，根據您計劃如何存取並將其資料的能力是公開為的重要功能**Shaper**技能集內的技能。  

預測就是當您有在擴充樹狀目錄中符合投影的結構描述的物件定義的工作變得更容易。 已更新[Shaper 技能](cognitive-search-skill-shaper.md)可讓您撰寫擴充樹狀結構的不同節點中的物件以及父這些新的節點下。 **Shaper**技術可讓您定義複雜型別使用巢狀物件。

當您有新的圖形定義，其中包含您要投影出的所有項目時，您現在可以使用此圖形做為您的預測的來源，或做為另一項技能的輸入。

## <a name="table-projections"></a>資料表投影

因為它可更輕鬆地匯入，我們會建議資料表進行資料瀏覽 Power bi 的投影。 此外，資料表投影允許變更變更資料表關聯性之間的基數。 

您可以投影單一文件索引中多個資料表，保留關聯性。 當投影至多個資料表，完整的圖形會投射到每個資料表，，除非子節點是相同群組內的另一個資料表的來源。

### <a name="defining-a-table-projection"></a>定義資料表投影

定義內的資料表投影時`knowledgeStore`元素的技能，開始將擴充樹狀結構上的節點對應到資料表來源。 此節點通常是輸出**Shaper**您新增至清單的技巧，以產生您要投影至資料表的特定圖形的技能。 您選擇專案節點可以切割成多個資料表的專案。 資料表定義是一份您想要投影的資料表。 

每個資料表都需要三個屬性：

+ TableName:Azure 儲存體中的資料表名稱。

+ generatedKeyName:唯一識別此資料列的索引鍵資料行名稱。

+ source：從擴充樹狀結構節點來源從您類。 這通常是 shaper，輸出，但可能是任何技術的輸出。

以下是範例的資料表的投射。

```json
{
    "name": "your-skillset",
    "skills": [
      …your skills
      
    ],
"cognitiveServices": {
… your cognitive services key info
    },

    "knowledgeStore": {
      "storageConnectionString": "an Azure storage connection string",
      "projections" : [
        {
          "tables": [
            { "tableName": "MainTable", "generatedKeyName": "SomeId", "source": "/document/EnrichedShape" },
            { "tableName": "KeyPhrases", "generatedKeyName": "KeyPhraseId", "source": "/document/EnrichedShape/*/KeyPhrases/*" },
            { "tableName": "Entities", "generatedKeyName": "EntityId", "source": "/document/EnrichedShape/*/Entities/*" }
          ]
        },
        {
          "objects": [
            
          ]
        }
      ]
    }
}
```
此範例所示，主要片語和實體會被塑造成不同的資料表，並將包含每個資料列的父代 (MainTable) 的參考。 

下圖是參考中的 Caselaw 練習[如何開始使用知識 store](knowledge-store-howto.md)。 案例有多個意見，且每個意見藉由識別其內所含的實體擴充的案例，在您無法模型預測，如下所示。

![實體和關聯性資料表中的](media/knowledge-store-projection-overview/TableRelationships.png "模型資料表投影中的關聯性")

## <a name="object-projections"></a>物件投影

物件的預測就是可以來自任何節點的擴充樹狀結構的 JSON 表示法。 在許多情況下，相同**Shaper**建立資料表的投影的技巧可以用來產生物件投影。 

```json
{
 
    "name": "your-skillset",
    "skills": [
      …your skills
      
    ],
"cognitiveServices": {
… your cognitive services key info
    },

    "knowledgeStore": {
      "storageConnectionString": "an Azure storage connection string",
      "projections" : [
        {
          "tables": [ ]
        },
        {
          "objects": [
            {
              "storageContainer": "Reviews", 
              "format": "json", 
              "source": "/document/Review", 
              "key": "/document/Review/Id" 
            }
          ]
        }
      ]
    }
}
```

產生的物件投影需要幾個特定物件的屬性：

+ storageContainer:要在其中儲存之物件的容器
+ source：擴充樹狀結構是投影的根節點的路徑
+ 索引鍵：表示要儲存之物件的唯一索引鍵路徑。 它將用於在容器中建立 blob 的名稱。

## <a name="projection-lifecycle"></a>投影生命週期

您的預測具有會在您的資料來源繫結至來源資料的生命週期。 您的資料更新並重新建立索引，您的預測會更新以確保您的預測會與您的資料來源中的資料最終一致類的結果。 投影會繼承您已設定您的索引與刪除原則。 

## <a name="using-projections"></a>使用投影

執行索引子之後，您可以讀取容器或您指定透過投影的資料表中預計的資料。 

進行分析，只要設定 Azure 資料表儲存體做為資料來源在 Power BI 中的瀏覽動作。 您可以非常輕鬆建立視覺效果的一的組運用內的關聯性資料。

或者，如果您需要的資料科學管線中使用已充實的資料，您可以[從 blob 將資料載入 Pandas 資料框架](../machine-learning/team-data-science-process/explore-data-blob.md)。

最後，如果您需要從知識存放區匯出您的資料，Azure Data Factory 有連接器，以匯出資料，並登陸在您選擇的資料庫中。 

## <a name="next-steps"></a>後續步驟

下一個步驟中，建立您第一次的知識存放區使用範例資料和指示。

> [!div class="nextstepaction"]
> [如何建立知識存放區](knowledge-store-howto.md)。