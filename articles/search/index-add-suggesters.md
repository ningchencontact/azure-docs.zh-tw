---
title: 將建議工具新增到 Azure 搜尋服務索引
description: 啟用欄位以取得預先輸入查詢動作功能，其中建議的查詢會由來自 Azure 搜尋服務索引中欄位的文字組成。
ms.date: 02/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: fd4b29134fd45ed2888fbc81ded413ecf7286959
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/04/2019
ms.locfileid: "57308647"
---
# <a name="add-suggesters-to-an-azure-search-index"></a>將建議工具新增到 Azure 搜尋服務索引

**建議工具**是 Azure 搜尋服務建構，支援「在輸入期間進行搜尋」的[建議](https://docs.microsoft.com/rest/api/searchservice/suggestions) \(英文\) 功能及[自動完成 (預覽)](search-autocomplete-tutorial.md) 功能。 在您可以呼叫建議 API 之前，您必須在索引中定義**建議工具**，以在特定欄位上啟用建議。

雖然**建議工具**具有數個屬性，其主要是由您要啟用[建議 API](https://docs.microsoft.com/rest/api/searchservice/suggestions) \(英文\) 的欄位集合所組成。 例如，旅遊應用程式應該針對目的地、城市及景點啟用鍵盤預先鍵入緩衝搜尋。 因此，這三個欄位都會被納入欄位集合中。

針對每個索引，您只能有一個**建議工具**資源 (具體而言，就是**建議工具**集合中只能有一個**建議工具**)。

## <a name="creating-a-suggester"></a>建立建議工具

您隨時都可以建立**建議工具**，但其對您索引所產生的影響會根據欄位而有所不同。

+ 以相同更新的形式新增到建議工具的新欄位造成的影響較小，因為不需要進行任何索引重建。
+ 不過，將現有的欄位新增至建議工具會變更欄位定義，並導致需對索引進行完整重建。

**建議工具**最適合用來建議特定文件，而非鬆散的詞彙或片語。 最佳的候選欄位是標題、名稱，以及可識別項目的其他相關簡短片語。 不太有效的是重複的欄位 (例如，類別和標記) 或非常長的欄位 (例如，說明或註解欄位)。

在建立建議工具之後，請將[建議 API](https://docs.microsoft.com/rest/api/searchservice/suggestions) \(英文\) 新增到您的查詢邏輯中以叫用該功能。

定義**建議工具**的屬性包括下列項目：

|屬性|描述|
|--------------|-----------------|
|`name`|**建議工具**的名稱。 您會在呼叫[建議 &#40;Azure 搜尋服務 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/suggestions) \(英文\) 時使用**建議工具**的名稱。|
|`searchMode`|用來搜尋候選片語的策略。 目前唯一支援的模式是 `analyzingInfixMatching`，其可在句子開頭或中間執行彈性的片語比對。|
|`sourceFields`|建議之內容來源的一或多個欄位清單。 只有類型 `Edm.String` 和 `Collection(Edm.String)` 的欄位可以用來做為提供建議的來源。 您只能使用未設定自訂語言分析器的欄位。 |

## <a name="suggester-example"></a>建議工具範例
**建議工具**是索引定義的一部分。 在目前版本中，連同**欄位**集合和 **scoringProfiles**，**建議工具**集合中只能存在一個**建議工具**。

```
{
  "name": "hotels",
  "fields": [
    . . .
  ],
  "suggesters": [
    {
      "name": "sg",
      "searchMode": "analyzingInfixMatching",
      "sourceFields": ["hotelName", "category"]
    }
  ],
  "scoringProfiles": [
    . . .
  ]
}

```

## <a name="see-also"></a>請參閱
[建立索引 &#40;Azure 搜尋服務 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index) \(英文\)  
[更新索引&#40;Azure 搜尋服務 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/update-index)  
[建議&#40;Azure 搜尋服務 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/suggestions)  
[索引作業&#40;Azure 搜尋服務 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/index-operations)  
[Azure Search Service REST](https://docs.microsoft.com/rest/api/searchservice/)  
[Azure 搜尋服務 .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)
