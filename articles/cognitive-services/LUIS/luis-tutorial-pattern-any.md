---
title: 教學課程：Pattern.any 實體資料 - LUIS
titleSuffix: Azure Cognitive Services
description: 針對語句已正確格式化，但資料結尾可能會因為語句中其餘字組而容易造成混淆的語句，使用 pattern.any 實體來從中擷取資料。
services: cognitive-services
ms.custom: seodec18
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 09/05/2019
ms.author: diberry
ms.openlocfilehash: 57a7deee3112737dc457646d040d5d3e02e63d46
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390290"
---
# <a name="tutorial-extract-free-form-data-with-patternany-entity"></a>教學課程：搭配 Pattern.any 實體擷取自由格式的資料

在此教學課程中，您將針對語句已正確格式化，但資料結尾可能會因為語句中其餘字組而容易造成混淆的語句，使用 pattern.any 實體來從中擷取資料。 

**在本教學課程中，您將了解如何：**

> [!div class="checklist"]
> * 匯入範例應用程式
> * 將範例語句新增至現有實體
> * 建立 Pattern.any 實體
> * 建立模式
> * 定型
> * 測試新模式

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="using-patternany-entity"></a>使用 Pattern.any 實體

Pattern.any 實體可讓您尋找自由格式的資料，其中實體的用字方式使其很難從語句的剩餘部分判斷實體的結尾。 

這個人力資源應用程式可協助員工尋找公司表單。 

|語句|
|--|
|**HRF-123456** 在哪裡？|
|**HRF-123234** 的作者是誰？|
|**HRF-456098** 是以法文發行的嗎？|

不過，每個表單都具備一個格式化的名稱 (上表所使用的名稱) 及一個易記名稱 (例如 `Request relocation from employee new to the company 2018 version 5`)。 

含有易記表單名稱的語句看起來如下：

|語句|
|--|
|**新進員工要求調職 2018 年第 5 版**在哪裡？|
|**「新進員工要求調職 2018 年第 5 版」** 的作者是誰？|
|**新進員工要求調職 2018 年第 5 版**是以法文發行的嗎？|

這個變動長度包含可能造成 LUIS 無法正確辨識實體結束位置的字組。 在模式中使用 Pattern.any 實體可讓您指定表單名稱的開頭和結尾，讓 LUIS 能夠正確擷取表單名稱。

|範本語句的範例|
|--|
|{FormName} 在哪裡[?]|
|{FormName} 的作者是誰[?]|
|{FormName} 是以法文發行的嗎[?]|

## <a name="import-example-app"></a>匯入範例應用程式

1. 下載並儲存[應用程式的 JSON 檔案](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-pattern-roles-HumanResources.json)。

1. 在 [LUIS 入口網站](https://www.luis.ai) 的 [我的應用程式]  頁面上，將 JSON 匯入新的應用程式中。

1. 從 [管理]  區段的 [版本]  索引標籤上，複製版本並將它命名為 `patt-any`。 複製是一個既可測試各種 LUIS 功能又不影響原始版本的絕佳方式。 因為版本名稱會作為 URL 路由的一部分，所以此名稱不能包含任何在 URL 中無效的字元。

## <a name="add-example-utterances"></a>新增範例語句 

1. 從上方瀏覽列中選取 [建置]  ，然後從左方瀏覽列中選取 [意圖]  。

1. 從意圖清單中選取 [FindForm]  。

1. 新增一些範例語句：

    |範例語句|
    |--|
    |**實驗室發生火災時該怎麼辦**的表單在哪裡，而且當我閱讀該表單之後需要誰簽名？|
    |**新進員工要求調職**位於伺服器上的何處？|
    |「**主要校園的健康與福利要求**」的作者是誰且最新版本為何？|
    |我正在尋找名為「包含實體資產在內的辦公室搬遷要求」  的表單。 |

    在沒有 Pattern.any 實體的情況下，LUIS 很難理解表單標題結束位置，因為表單名稱有許多種變化。

## <a name="create-a-patternany-entity"></a>建立 Pattern.any 實體
Pattern.any 實體可擷取各種不同長度的實體。 它僅適用於模式，因為模式會標示實體的開頭和結尾。  

1. 從左側導覽窗格中選取 [Entities] \(實體\)  。

1. 選取 [建立新實體]  、輸入名稱 `FormName`，然後選取 [Pattern.any]  作為類型。 選取 [完成]  。 

    因為 Pattern.any 只在模式中才有效，所以您不能在意圖的範例語句中標示實體。 

    如果您想要使所擷取的資料包含其他實體 (例如 number 或 datetimeV2)，您需要建立包含 Pattern.any 以及 number 和 datetimeV2 的複合實體。

## <a name="add-a-pattern-that-uses-the-patternany"></a>新增使用 Pattern.any 的模式

1. 從左側導覽窗格中選取 [Patterns] \(模式\)  。

1. 選取 [FindForm]  意圖。

1. 輸入下列使用新實體的範本語句：

    |範本語句|
    |--|
    |["]{FormName}["] 的表單在哪裡，而且當我閱讀該表單之後需要誰簽名[？]|
    |["]{FormName}["] 位於伺服器上的何處[？]|
    |["] {FormName}["] 的作者是誰且最新版本為何[？]|
    |我正在尋找名為 ["]{FormName}["] 的表單[。]|

    如果您想要將表單的變化納入考量 (例如，單引號而非雙引號，或是句號而非問號)，請針對每種變化建立新模式。

## <a name="train-the-luis-app"></a>進行 LUIS 應用程式定型

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="test-the-new-pattern-for-free-form-data-extraction"></a>測試新模式以擷取自由格式資料
1. 從頂端列選取 [Test] \(測試\)  來開啟測試面板。 

1. 輸入下列語句： 

    `Where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

1. 選取結果底下的 [Inspect] \(檢查\)  ，以查看實體和意圖的測試結果。

    系統會先尋找實體 `FormName`，然後才尋找可判斷意圖的模式。 如果您的測試結果未偵測到實體，因而未找到模式，您就必須針對意圖新增更多範例語句 (而非模式)。

1. 選取頂端導覽列中的 [Test] \(測試\)  按鈕。

## <a name="using-an-explicit-list"></a>使用明確的清單

如果您發現模式在包含 Pattern.any 時所擷取的實體不正確，請使用[明確清單](luis-concept-patterns.md#explicit-lists)來更正此問題。


## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>後續步驟

本教學課程已將範例語句新增至現有意圖，然後為表單名稱建立新的 Pattern.any。 然後本教學課程以新的語句範例和實體，為現有意圖建立模式。 互動式測試顯示已對模式和其意圖做出預測，因為已找到實體。 

> [!div class="nextstepaction"]
> [了解如何將角色與模式搭配使用](luis-tutorial-pattern-roles.md)
