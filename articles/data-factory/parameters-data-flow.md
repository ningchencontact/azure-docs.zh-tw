---
title: Azure Data Factory 的對應資料的流程參數
description: 了解如何將參數化資料處理站管線中的對應資料流
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: af5f421cc3802f3a7ad44bb294f5066c32569f8b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082881"
---
# <a name="mapping-data-flow-parameters"></a>對應資料的流程參數

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

對應資料流程 data factory 中的支援參數的使用。 您可以定義參數，您就可以使用整個運算式的資料流定義內。 然後呼叫管線，透過執行資料流程的活動可以設定參數。 您有三種選項來使用資料流程中的資料活動運算式設定的值：

* 若要設定動態值使用管線的控制流程運算式語言
* 若要設定動態值使用資料流的運算式語言
* 使用任一運算式語言來設定靜態的常值

您可以使用這項功能來進行資料流，一般用途、 彈性且可重複使用。 您可以參數化資料流量設定和使用這些參數的運算式。

> [!NOTE]
> 若要使用管線的控制流程運算式，您資料的流程參數必須是字串類型。

* 將執行資料流程的活動新增至管線畫布中。
* 如果您資料的流程有參數，您會看到輸入參數值的每個參數旁邊的文字方塊上按一下 參數 tab.* * 中的可用參數清單。
* 您可以選擇建立您的參數透過管線的控制流程運算式語言的運算式或工作流程運算式。

![資料流程參數 3](media/data-flow/params3.png "資料流程參數 3")

## <a name="create-parameters-in-data-flow"></a>在資料流程中建立參數

![資料流程參數 1](media/data-flow/params1.png "資料流程參數 1")

若要將參數加入至資料流程中，按一下以查看 [一般] 屬性的資料流量畫布的空白部分。 在 [設定] 窗格中，您會看到稱為 [參數] 索引標籤。 按一下 [新增] 按鈕來產生新的參數，然後可以從管線中，將值傳遞到您的資料流設定。 輸入參數名稱，然後選取每個參數的資料類型。

在您工作流程運算式，您可以利用下列參數使用從管線設定的值。 參數會以 $ 開頭，而且是固定不變。 您也可以找到您可用的參數，在 運算式產生器，在 參數 索引標籤下的清單。雖然您可能會將新的值指派給參數，您可以在您的運算式，使用這些值。

![資料流程參數 2](media/data-flow/params2.png "資料流程參數 2")

## <a name="set-data-flow-parameters-from-pipeline"></a>從管線的設定資料流程參數

使用參數建立資料流之後, 現在具有執行資料流活動執行該資料流程管線中。 一旦您將該活動新增至管線設計畫布時，您將會看到可用的資料活動的參數設定 索引標籤中的流程參數。

![資料流程參數運算式語言](media/data-flow/params4.png "資料流程參數運算式語言")

當您按一下來填入參數值的文字方塊中時，將會看到資料的流程運算式產生器。 在這裡，您可以輸入任何運算式或您想符合參數的資料類型的常值。 以下是從運算式產生器資料流的運算式和常值字串的範例：

* ```toInteger(Role)```
* ```'this is my static literal string'```

如果您的參數資料類型是字串，您可以選擇輸入管線或資料的流程運算式。 如果您選擇管線運算式時，您會改為看到與管線運算式面板。 請務必包含管線函式內使用的字串內插補點語法 ' @{<expression>}'，例如：

```'@{pipeline().RunId}'```

![資料流參數範例](media/data-flow/params5.png "資料流參數範例")

## <a name="next-steps"></a>後續步驟

* [執行資料的流程活動](control-flow-execute-data-flow-activity.md)
* [控制流程運算式](control-flow-expression-language-functions.md)
