---
title: Azure Data Factory 對應資料流程結構描述漂移
description: 透過結構描述漂移在 Azure Data Factory 中建置復原性資料流程
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: 562daa024985a546ffb49c4da11eace3bc81a659
ms.sourcegitcommit: da0a8676b3c5283fddcd94cdd9044c3b99815046
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/18/2019
ms.locfileid: "68314814"
---
# <a name="mapping-data-flow-schema-drift"></a>對應資料流程結構描述漂移

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

結構描述漂移的概念是您的來源經常變更中繼資料的情況。 欄位、資料行、類型等都可以即時新增、移除或變更。 不需要處理結構描述漂移，您的結構描述變得容易受上游資料來源變更所影響。 當傳入的資料行和欄位變更時，典型的 ETL 模式會失敗，因為它們通常會繫結至這些來源名稱。

若要防止結構描述漂移，資料流程工具中一定要有一些設施，可讓您以資料工程師的身分：

* 定義具有可變動欄位名稱、資料類型、值和大小的來源
* 定義可搭配資料模式 (而不是硬式編碼的欄位和值) 運作的轉換參數
* 定義一些運算式，以了解要比對傳入欄位的模式，而不需使用具名欄位

## <a name="how-to-implement-schema-drift-in-adf-mapping-data-flows"></a>如何在 ADF 對應資料流程中執行架構漂移
ADF 原本就支援從執行變更為執行的彈性架構, 讓您可以建立泛型資料轉換邏輯, 而不需要重新編譯您的資料流程。

* 選擇在您的來源轉換中 [允許結構描述漂移]

<img src="media/data-flow/schemadrift001.png" width="400">

* 在您選取此選項後，每次執行資料流程時都會從您的來源讀取所有傳入欄位，而這些欄位會通過整個流程送至接收。

* 根據預設, 所有新偵測到的資料行 (漂移資料行) 都會以字串資料類型的形式抵達。 如果您想要讓 ADF 自動從來源推斷資料類型, 請在您的來源轉換中選擇 [推斷漂移資料行類型]。

* 請務必使用「自動對應」來對應「接收」轉換中的所有新欄位, 以便在您的目的地中取得並進入所有新的欄位, 並在接收上設定「允許架構漂移」。

<img src="media/data-flow/automap.png" width="400">

* 當使用簡單的來源 > 接收 (複製) 對應在該案例中引進新的欄位時, 所有專案都會正常執行。

* 若要在處理結構描述漂移的工作流程中新增轉換，您可以使用模式比對，依照名稱、類型和值比對資料行。

* 如果您想要建立了解「結構描述漂移」的轉換，請按一下 [衍生資料行] 或 [彙總] 轉換中的 [新增資料行模式]。

<img src="media/data-flow/columnpattern.png" width="400">

> [!NOTE]
> 您需要在資料流程中做出架構決策，才能接受整個流程的結構描述漂移。 當您這樣做時，您可以抵抗來自來源的結構描述變更。 不過，您會遺失整個資料流程中早期的資料行與類型繫結。 Azure Data Factory 會將結構描述漂移流程視為晚期繫結的流程，因此當您建置轉換時，資料行名稱無法供您在整個流程的結構描述檢視中使用。

<img src="media/data-flow/taxidrift1.png" width="400">

在「計程車示範」範例資料流程中，底部資料流程中有一個採用 TripFare 來源的範例結構描述漂移。 請注意在 [彙總] 轉換中，我們對彙總欄位使用「資料行模式」設計。 我們假設資料可以變更，且在執行之間可能不會以相同的順序出現，而不需命名特定資料行，或依照位置尋找資料行。

在此 Azure Data Factory 資料流程結構描述漂移處理的範例中，我們已建置彙總以掃描 'double' 類型的資料行，並且知道資料網域包含每趟車程的價格。 不論資料行置於何處，也不論資料行的命名為何，我們接著都可以跨來源中的所有 double 欄位執行彙總數學計算。

Azure Data Factory 資料流程語法會使用 $$ 表示您的比對模式中每個相符的資料行。 您也可以使用複雜的字串搜尋和規則運算式函式來比對資料行名稱。 在此情況下，我們將根據 'double' 類型資料行的每個相符項目，建立新的彙總欄位名稱，並將 ```_total``` 文字附加至每個相符的名稱： 

```concat($$, '_total')```

然後，我們將會捨入並加總每個相符資料行的值：

```round(sum ($$))```

您可以使用 Azure Data Factory 資料流程範例「計程車示範」測試看看。 使用 [資料流程] 設計介面頂端的 [偵錯] 切換開關來開啟 [偵錯] 工作階段，您便可以互動方式查看您的結果：

<img src="media/data-flow/taxidrift2.png" width="800">

## <a name="access-new-columns-downstream"></a>存取下游的新資料行
當您使用資料行模式產生新的資料行時, 可以使用下列方法, 稍後在資料流程轉換中存取這些新的資料行:

* 使用 "byPosition", 依位置號碼來識別新的資料行。
* 使用 "byName", 依其名稱來識別新的資料行。
* 在 [資料行模式] 中, 使用 [名稱]、[資料流程]、[位置] 或 [類型] 或任何組合, 以符合新的資料行。

## <a name="next-steps"></a>後續步驟
在[資料流程運算式語言](data-flow-expression-functions.md)中, 您會發現資料行模式和架構漂移的其他設備, 包括 "byName" 和 "byPosition"。
