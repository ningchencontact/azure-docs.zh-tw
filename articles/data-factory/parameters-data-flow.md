---
title: Azure Data Factory 對應的資料流程參數
description: 瞭解如何從 data factory 管線參數化對應資料流程
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: 733d3f9c4079193107f22178bdbde3a3ecf0e7ca
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2019
ms.locfileid: "72028220"
---
# <a name="mapping-data-flow-parameters"></a>對應資料流程參數



對應中的資料流程 Azure Data Factory 支援使用參數。 您可以在資料流程定義內定義參數，然後在整個運算式中使用。 參數值可透過「執行資料流程」活動來設定呼叫管線。 您有三個選項可設定 [資料流程] 活動運算式中的值：

* 使用管線控制流程運算式語言來設定動態值
* 使用資料流程運算式語言來設定動態值
* 使用任一個運算式語言來設定靜態常值

使用此功能可讓您的資料流程程成為一般用途、彈性且可重複使用。 您可以使用這些參數將資料流程設定和運算式參數化。

> [!NOTE]
> 若要使用管線控制流程運算式，資料流程參數必須是字串類型。

## <a name="create-parameters-in-mapping-data-flow"></a>在對應資料流程中建立參數

若要將參數加入至您的資料流程，請按一下 [資料流程] 畫布的空白部分，以查看一般屬性。 在 [設定] 窗格中，您會看到名為 [參數] 的索引標籤。 按一下 [新增] 按鈕以產生新的參數。 針對每個參數，您必須指派名稱、選取類型，並選擇性地設定預設值。

![建立資料流程參數](media/data-flow/create-params.png "建立資料流程參數")

可以在任何資料流程運算式中使用參數。 參數是以 $ 開頭，而且是不可變的。 您會在 [參數] 索引標籤底下的 [運算式產生器] 中找到可用參數的清單。

資料流程![參數運算式](media/data-flow/parameter-expression.png "資料流程 參數運算式")

## <a name="use-parameters-in-your-data-flow"></a>在資料流程中使用參數

* 您可以在轉換運算式內使用參數值。 您會在 [運算式產生器] 中的 [參數] 索引標籤下找到參數清單。 ![使用資料流程參數](media/data-flow/params9.png "使用資料流程參數")

* 參數也會用來設定來源和接收轉換設定的動態值。 當您按一下可設定的欄位內部時，您會看到 [新增動態內容] 連結出現。 按一下會帶您前往運算式產生器，您可以在其中使用參數來使用動態值。 資料流程![動態內容](media/data-flow/params6.png "資料流程 動態內容")

## <a name="set-mapping-data-flow-parameters-from-pipeline"></a>從管線設定對應的資料流程參數

建立具有參數的資料流程之後，您可以使用「執行資料流程」活動從管線執行它。 將活動新增至管線畫布之後，您會在活動的 [參數] 索引標籤中看到可用的資料流程參數。

![設定資料流程參數](media/data-flow/parameter-assign.png "設定資料流程參數")

如果您的參數資料類型是 [字串]，當您按一下文字方塊來設定參數值時，您可以選擇輸入管線或資料流程運算式。 如果您選擇 [管線運算式]，將會顯示 [管線運算式] 面板。 請務必使用 `'@{<expression>}'`，在字串插補語法內包含管線函數，例如：

```'@{pipeline().RunId}'```

如果您的參數不是字串類型，您一定會看到資料流程運算式產生器。 在這裡，您可以輸入任何您想要的運算式或常值，以符合參數的資料類型。 以下是「運算式產生器」中的「資料流程運算式」和「常值字串」範例：

* ```toInteger(Role)```
* ```'this is my static literal string'```

每個對應資料流程可以有管線和資料流程運算式參數的任意組合。 

資料流程![參數範例](media/data-flow/parameter-example.png "資料流程 參數範例")



## <a name="next-steps"></a>後續步驟
* [執行資料流程活動](control-flow-execute-data-flow-activity.md)
* [控制流程運算式](control-flow-expression-language-functions.md)
