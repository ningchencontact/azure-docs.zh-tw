---
title: 使用知識存放區中的投影（預覽）-Azure 搜尋服務
description: 儲存並塑造來自 AI 索引管線的擴充資料，以用於搜尋以外的案例
manager: nitinme
author: vkurpad
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: vikurpad
ms.openlocfilehash: c5fb547b18bc4014f91341070f49c4af84c01005
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2019
ms.locfileid: "71265174"
---
# <a name="working-with-projections-in-a-knowledge-store-in-azure-search"></a>在 Azure 搜尋服務的知識存放區中使用投影

> [!Note]
> 知識存放區處於預覽狀態，不適合用於生產環境。 [REST API 版本 2019-05-06-Preview](search-api-preview.md) 提供此功能。 目前沒有 .NET SDK 支援。
>

Azure 搜尋服務可透過 AI 認知技能和自訂技能來擴充內容，做為編制索引的一部分。 擴充將結構新增至您的檔，並讓搜尋更有效率。 在許多情況下，擴充的檔適用于搜尋以外的案例，例如知識發掘。

「[知識存放區](knowledge-store-concept-intro.md)」的一個元件是擴充檔的觀點，可以儲存至實體儲存體以供知識挖掘之用。 預測可讓您將資料「投射」到符合您需求的圖形，保留關聯性，讓 Power BI 之類的工具可以讀取資料，而不需要額外的工作。 

投影可以是表格式，其中的資料會儲存在 Azure 資料表儲存體中的資料列和資料行，或儲存在 Azure Blob 儲存體中的 JSON 物件。 您可以在擴充資料時，定義多項資料的預測。 當您想要在個別使用案例中以不同方式塑造相同的資料時，這會很有用。 

知識存放區支援兩種類型的預測：

+ **資料表**：對於最佳呈現為數據列和資料行的資料，資料表預測可讓您在資料表儲存體中定義架構化圖形或投影。 

+ **物件**：當您需要資料和擴充的 JSON 標記法時，物件投影會儲存為 blob。

若要查看內容中定義的預測，請逐步解說[如何開始使用知識存放區](knowledge-store-howto.md)。

## <a name="projection-groups"></a>投射群組

在某些情況下，您必須將擴充的資料投影到不同的圖形，以符合不同的目標。 知識存放區可讓您定義多個投射群組。 投射群組具有相互獨佔性和關聯的下列重要特性。

### <a name="mutually-exclusivity"></a>相互獨佔性

投影到單一群組中的所有內容，都與投影至其他投射群組的資料無關。 這表示您可以將相同的資料成形成不同的，但在每個投射群組中都有重複。 

在投影群組中強制執行的一個條件約束，是投射類型與投射群組的相互獨佔性。 您只能在單一群組中定義資料表投影或物件投射。 如果您想要資料表和物件，請為數據表定義一個投射群組，並為物件定義第二個投射群組。

### <a name="relatedness"></a>關聯

在單一投射群組內投射的所有內容都會保留資料中的關聯性。 關聯性是以產生的索引鍵為基礎，而每個子節點都會保留父節點的參考。 關聯性不會跨越投射群組，而在某個投射群組中建立的資料表或物件與其他投射群組中產生的資料沒有關聯性。

## <a name="input-shaping"></a>輸入成形
取得正確的圖形或結構中的資料，是有效使用的關鍵，也就是資料表或物件。 能夠根據您計畫的存取和使用方式來塑造或結構化您的資料，這是在技能集中以**整形**者技能的形式公開的重要功能。  

當您在擴充樹狀結構中的物件符合投射的架構時，可以更輕鬆地定義投影。 更新的[整形程式技能](cognitive-search-skill-shaper.md)可讓您從擴充樹狀結構的不同節點撰寫物件，並將其父系在新節點底下。 「**整形**」技能可讓您定義具有嵌套物件的複雜類型。

當您定義了一個新的圖形，其中包含您所需的所有專案時，您現在可以使用此圖形做為投影的來源，或做為另一個技能的輸入。

## <a name="table-projections"></a>資料表投射

因為它可讓匯入更容易，所以我們建議使用 Power BI 來探索資料的資料表。 此外，資料表投射可以變更資料表關聯性之間的基數。 

您可以將索引中的單一檔投影成多個資料表，並保留關聯性。 投影到多個資料表時，除非子節點是相同群組內另一個資料表的來源，否則整個圖形都會投射到每個資料表中。

### <a name="defining-a-table-projection"></a>定義資料表投射

在技能集的`knowledgeStore`元素內定義資料表投影時，請先將擴充樹狀目錄上的節點對應到資料表來源。 此節點通常是您加入至技能清單中，以產生您需要投影到資料表的特定圖形之**塑造技能的**輸出。 您選擇要加入至專案的節點可以切割成多個資料表。 資料表定義是您想要投影的資料表清單。 

#### <a name="projection-slicing"></a>投射切割
定義資料表投射群組時，擴充樹狀結構中的單一節點可以分割成多個相關的資料表。 加入具有現有資料表投射子系之來源路徑的資料表，會導致子節點從父節點切割，並投射到新的相關資料表。 這可讓您在整形程式技能中定義單一節點，這種技術可以是所有資料表投影的來源。

每個資料表都需要三個屬性：

+ TableNameAzure 儲存體中的資料表名稱。

+ generatedKeyName:唯一識別此資料列之索引鍵的資料行名稱。

+ source：從您的擴充來源的擴充樹狀結構中的節點。 這通常是整形者的輸出，但可能是任何技能的輸出。

以下是資料表投影的範例。

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
如本範例所示，主要片語和實體會模型化為不同的資料表，而且將包含每個資料列之父系（MainTable）的參考。 

下圖是 [如何開始使用知識存放區](knowledge-store-howto.md) 中 Caselaw 練習的參考。 在案例中有多項意見，而且每個意見都藉由識別包含在其中的實體而擴充時，您可以建立預測的模型，如下所示。

![資料表中的實體和關聯]性(media/knowledge-store-projection-overview/TableRelationships.png "資料表投影中的模型關聯")性

## <a name="object-projections"></a>物件投影

物件投射是擴充樹狀結構的 JSON 標記法，可以從任何節點來源。 在許多情況下，建立資料表投射的同一個**整形**程式技能可以用來產生物件投射。 

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

產生物件投射需要一些特定物件屬性：

+ StorageContainer將儲存物件的容器
+ source：擴充樹狀結構之節點的路徑，而這是投射的根
+ 擊鍵路徑，表示要儲存之物件的唯一索引鍵。 它會用來建立容器中的 blob 名稱。

## <a name="projection-lifecycle"></a>預測生命週期

您的預測有一個生命週期，系結至資料來源中的來源資料。 當您的資料更新並重新編制索引時，您的投影會以擴充的結果進行更新，確保您的預測最終會與資料來源中的資料一致。 投影會繼承您為索引設定的刪除原則。 

## <a name="using-projections"></a>使用投影

執行索引子之後，您可以讀取透過投影指定的容器或資料表中的預測資料。 

針對分析，Power BI 中的探索就像將 Azure 資料表儲存體設定為數據源一樣簡單。 您可以利用中的關聯性，輕鬆地在您的資料上建立一組視覺效果。

或者，如果您需要在資料科學管線中使用擴充的資料，您可以將[資料從 blob 載入至 Pandas 資料框架](../machine-learning/team-data-science-process/explore-data-blob.md)。

最後，如果您需要從知識存放區匯出資料，Azure Data Factory 有連接器可以匯出資料，並將其存放在您選擇的資料庫中。 

## <a name="next-steps"></a>後續步驟

在下一個步驟中，請使用範例資料和指示來建立您的第一個知識存放區。

> [!div class="nextstepaction"]
> [如何建立知識存放區](knowledge-store-howto.md)。
