---
title: Azure Data Factory 對應資料流程資料行模式
description: 使用對應資料流程中的 Azure Data Factory 資料行模式來建立一般化資料轉換模式
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 41037e0687274d123bea742cee5cf2887548aa0f
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2019
ms.locfileid: "68775217"
---
# <a name="mapping-data-flows-column-patterns"></a>對應資料流程資料行模式

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

數個 Azure Data Factory 資料流程轉換支援「資料行模式」的概念，因此您可以根據模式建立範本資料行，而不是硬式編碼的資料行名稱。 您可以在運算式產生器中使用這項功能來定義模式, 以符合轉換的資料行, 而不需要精確的特定功能變數名稱。 如果傳入來源欄位經常變更, 則模式非常有用, 特別是在變更文字檔或 NoSQL 資料庫中的資料行時。 這種情況有時稱為「架構漂移」。

此「彈性架構」處理目前是在衍生的資料行和匯總轉換, 以及做為「規則型對應」的選取和接收轉換中找到。

![資料行模式](media/data-flow/columnpattern2.png "資料行模式")

## <a name="column-patterns"></a>資料行模式
資料行模式可用於處理結構描述漂移案例以及一般案例。 這對於您在無法完全得知每個資料行名稱的情況時非常實用。 您可以針對資料行名稱和資料行資料類型進行模式比對，並建立轉換運算式，其將針對符合您 `name` & `type` 模式之資料流中的任何欄位執行該作業。

將運算式新增至接受模式的轉換時，請選擇 [新增資料行模式]。 資料行模式允許符合模式的結構描述漂移資料行。

建置範本資料行模式時，在運算式中使用 `$$` 以代表每個相符欄位的參考都來自輸入資料流。

如果您選擇使用某個運算式產生器 Regex 函式，可以接著連續使用 $1、$2、$3 等來參考與您 Regex 運算式相符的子模式。

資料行模式案例的範例是使用 SUM 搭配一系列的輸入欄位。 彙總 SUM 計算是在「彙總」轉換中。 接著, 您可以在符合 "integer" 的欄位類型的每個相符項上使用 SUM, 然後使用 $ $ 來參考運算式中的每個相符項。

## <a name="match-columns"></a>符合資料行
資料![行模式類型](media/data-flow/pattern2.png "模式類型")

若要根據資料行建立模式, 您可以比對資料行名稱、類型、資料流程或位置, 並搭配運算式函數和正則運算式使用任何組合。

資料![行位置]資料(media/data-flow/position.png "行位置")

## <a name="rule-based-mapping"></a>以規則為基礎的對應
對應來源中的資料行並選取轉換時, 您可以選擇 [固定對應] 或 [規則型對應]。 當您知道資料的架構, 而且預期源資料集的特定資料行永遠符合特定的靜態名稱時, 您可以使用固定對應。 但是當您使用彈性的架構時, 請使用以規則為基礎的對應。 您將能夠使用上述規則來建立模式比對。

以![規則為基礎的對應]以(media/data-flow/rule2.png "規則為基礎的對應")

使用運算式產生器建立您的規則。 您的運算式會傳回布林值, 使其符合資料行 (true) 或排除資料行 (false)。

## <a name="pattern-matching-special-columns"></a>模式比對特殊資料行

* `$$`會在設計階段以「偵測模式」和在執行時間執行時, 轉譯為每個相符項的名稱
* `name`代表每個傳入資料行的名稱
* `type`代表每個傳入資料行的資料類型
* `stream`表示在您的流程中, 與每個資料流程或轉換相關聯的名稱
* `position`這是資料流程中資料行的序數位置

## <a name="next-steps"></a>後續步驟
* 深入瞭解資料轉換的 ADF 對應資料流程[運算式語言](http://aka.ms/dataflowexpressions)
* 使用[接收轉換](data-flow-sink.md)中的資料行模式, 並選取 [使用以規則為基礎的對應進行[轉換](data-flow-select.md)]
