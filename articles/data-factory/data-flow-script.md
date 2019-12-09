---
title: 對應資料流程腳本
description: Data Factory 的資料流程腳本程式碼後置語言總覽
author: kromerm
ms.author: nimoolen
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/10/2019
ms.openlocfilehash: d861a4355158dfe18ac3aa40a7f98dc11ebda90b
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930264"
---
# <a name="data-flow-script-dfs"></a>資料流程腳本（DFS）

資料流程腳本（DFS）是基礎中繼資料，類似于編碼語言，用來執行對應資料流程中包含的轉換。 每個轉換都會以一系列的屬性來表示，以提供必要的資訊來正確執行工作。 您可以透過按一下瀏覽器 UI 頂端功能區上的 [腳本] 按鈕，從 ADF 看到腳本並加以編輯。

![腳本按鈕](media/data-flow/scriptbutton.png "腳本按鈕")

例如，來源轉換中的 `allowSchemaDrift: true,` 會告訴服務在資料流程中包含來自源資料集的所有資料行，即使它們並未包含在架構投影中也一樣。

## <a name="use-cases"></a>使用案例
DFS 會由使用者介面自動產生。 您可以按一下 [腳本] 按鈕來查看和自訂腳本。 您也可以在 ADF UI 外產生腳本，然後將其傳遞至 PowerShell Cmdlet。 在進行複雜資料流程的調試時，您可能會發現，掃描腳本的程式碼後置會比較容易，而不是掃描您流程的 UI 圖形標記法。

以下是幾個範例使用案例：
- 以程式設計方式產生許多非常類似的資料流程，也就是「戳記」的資料流程。
- 難以在 UI 中管理或導致驗證問題的複雜運算式。
- 對執行期間傳回的各種錯誤進行調試和更進一步的瞭解。

當您建立要搭配 PowerShell 或 API 使用的資料流程腳本時，您必須將格式化的文字折迭成一行。 您可以將定位字元和分行符號保留為 escape 字元。 但必須將文字格式化以符合 JSON 屬性。 底部的 [腳本編輯器] UI 上有一個按鈕，可讓您將腳本格式化為單一行。

![複製按鈕](media/data-flow/copybutton.png "畫面右方的 [複製] 按鈕")

## <a name="how-to-add-transforms"></a>如何新增轉換
加入轉換需要三個基本步驟：新增核心轉換資料、將輸入資料流程重新路由，然後將輸出資料流程重設路徑。 在範例中，這會很容易看到。
假設我們從一個簡單的來源接收資料流程程，如下所示：

```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

如果我們決定加入「衍生」轉換，首先我們需要建立「核心」轉換文字，它具有簡單的運算式，可新增名為 `upperCaseTitle`的新大寫資料行：
```
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
```

然後，我們會採用現有的 DFS 並新增轉換：
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

現在，我們會藉由識別要讓新轉換之後的轉換（在此案例中為 `source1`），並將資料流程的名稱複製到新的轉換，來重新路由傳入資料流程：
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

最後，我們會識別我們想要在這個新轉換之後出現的轉換，並使用新轉換的輸出資料流程名稱來取代其輸入資料流程（在此案例中為 `sink1`）：
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
deriveTransformationName sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="dfs-fundamentals"></a>DFS 基本概念
DFS 是由一系列的已連線轉換組成，包括來源、接收和各種其他專案，可以加入新的資料行、篩選資料、聯結資料，還有更多功能。 腳本通常會以一或多個來源開頭，後面接著多個轉換，並以一或多個接收結束。

來源全都具有相同的基本結構：
```
source(
  source properties
) ~> source_name
```

例如，具有三個數據行（movieId、title、內容內容）的簡單來源會是：
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
```

來源以外的所有轉換都具有相同的基本結構：
```
name_of_incoming_stream transformation_type(
  properties
) ~> new_stream_name
```

例如，採用資料行（標題）並以大寫版本覆寫它的簡單衍生轉換會如下所示：
```
source1 derive(
  title = upper(title)
) ~> derive1
```

而沒有架構的接收則是：
```
derive1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="next-steps"></a>後續步驟

開始使用資料流程[總覽文章](concepts-data-flow-overview.md)來探索資料流程
