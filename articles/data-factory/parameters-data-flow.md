---
title: Azure Data Factory 將資料流參數對應
description: 了解如何將參數化資料處理站管線中的對應資料流
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: ef97f17bf159511ce94f90cd00623e05489acb92
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2019
ms.locfileid: "67274701"
---
# <a name="mapping-data-flow-parameters"></a>對應資料的流程參數

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Azure Data Factory 中的對應資料流支援使用參數。 您可以定義參數，您就可以使用整個運算式的資料流定義內。 參數值可以設定所呼叫的管線，透過執行資料流程的活動。 您有三個選項可設定值，在資料流程中活動的運算式：

* 若要設定動態值使用管線的控制流程運算式語言
* 若要設定動態值使用資料流的運算式語言
* 使用任一運算式語言來設定靜態的常值

您可以使用這項功能來進行資料流，一般用途、 彈性且可重複使用。 您可以參數化資料流量設定和使用這些參數的運算式。

> [!NOTE]
> 若要使用管線的控制流程運算式，您資料的流程參數必須是字串類型。

## <a name="create-parameters-in-mapping-data-flow"></a>對應資料流程中建立參數

若要將參數加入至資料流程中，按一下以查看 [一般] 屬性的資料流量畫布的空白部分。 在 [設定] 窗格中，您會看到稱為 [參數] 索引標籤。 按一下 [新增] 按鈕，來產生新的參數。 每個參數，您必須指派名稱、 選取類型，並選擇性地設定 預設值。

![建立資料流程的參數](media/data-flow/create-params.png "建立資料流程的參數")

參數可以利用任何資料的流程運算式中。 參數會以 $ 開頭，而且是固定不變。 您會發現在 運算式產生器的 參數 索引標籤內的可用參數的清單。

![資料流程參數運算式](media/data-flow/parameter-expression.png "資料流程參數運算式")

## <a name="set-mapping-data-flow-parameters-from-pipeline"></a>從管線中設定對應的資料流的參數

一旦您已建立您資料的流程參數，您可以從管線執行與資料執行流程活動。 管線畫布中加入活動之後，您將會看到可用的資料活動的 [參數] 索引標籤中的流程參數。

![設定 Data Flow 參數](media/data-flow/parameter-assign.png "設定 Data Flow 參數")

如果您的參數資料類型會是字串，當您按一下 [設定參數值] 文字方塊中，您可以選擇輸入管線或資料的流程運算式。 如果您選擇管線運算式時，您將會看到管線的 [運算式] 面板。 請務必包含管線函式內使用的字串內插補點語法 ' @{<expression>}'，例如：

```'@{pipeline().RunId}'```

如果您的參數不是字串類型，您一定會看到與資料的流程運算式產生器。 在這裡，您可以輸入任何運算式或您想要符合參數的資料類型的常值。 以下是從運算式產生器資料流的運算式和常值字串的範例：

* ```toInteger(Role)```
* ```'this is my static literal string'```

每個對應的資料流程可以有管線和資料的流程運算式參數的任何組合。 

![資料流參數範例](media/data-flow/parameter-example.png "資料流參數範例")



## <a name="next-steps"></a>後續步驟
* [執行資料的流程活動](control-flow-execute-data-flow-activity.md)
* [控制流程運算式](control-flow-expression-language-functions.md)
