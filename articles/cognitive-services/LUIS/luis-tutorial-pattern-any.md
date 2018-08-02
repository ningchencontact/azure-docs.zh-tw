---
title: 使用 pattern.any 實體來改善 LUIS 預測的教學課程 - Azure | Microsoft Docs
titleSuffix: Cognitive Services
description: 在本教學課程中，使用 pattern.any 實體來改善 LUIS 意圖和實體預測。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 07/20/2018
ms.author: diberry
ms.openlocfilehash: ca4ed4444cd753c0fe22c6596dfd34d033b7b0cf
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/25/2018
ms.locfileid: "39236997"
---
# <a name="tutorial-improve-app-with-patternany-entity"></a>教學課程：使用 pattern.any 實體來改善應用程式

在本教學課程中，使用 pattern.any 實體來提高意圖和實體預測。  

> [!div class="checklist"]
* 了解何時及如何使用 pattern.any
* 建立使用 pattern.any 的模式
* 如何確認預測改善

在本文中，您需要免費 [LUIS](luis-reference-regions.md) 帳戶才能撰寫 LUIS 應用程式。

## <a name="before-you-begin"></a>開始之前
如果您沒有來自[模式角色](luis-tutorial-pattern-roles.md)教學課程中的人力資源應用程式，請將 JSON [匯入](luis-how-to-start-new-app.md#import-new-app)到 [LUIS](luis-reference-regions.md#luis-website) 網站中的新應用程式。 您可以在 [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-roles-HumanResources.json) GitHub 存放庫中找到要匯入的應用程式。

如果您想要保留原始的「人力資源」應用程式，請在 [[設定](luis-how-to-manage-versions.md#clone-a-version)] 頁面上複製該版本，並將其命名為 `patt-any`。 複製是一個既可測試各種 LUIS 功能又不影響原始版本的絕佳方式。 

## <a name="the-purpose-of-patternany"></a>Pattern.any 的用途
Pattern.any 實體可讓您尋找自由格式的資料，其中實體的用字方式使其很難從語句的剩餘部分判斷實體的結尾。 

這個人力資源應用程式可協助員工尋找公司表單。 表單已在[規則運算式教學課程](luis-quickstart-intents-regex-entity.md)中新增。 來自該教學課程的表單名稱使用規則運算式來擷取已正確格式化的表單名稱，例如，下列語句表格中以粗體顯示的表單名稱：

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

這個變動長度包含可能造成 LUIS 無法正確辨識實體結束位置的片語。 在模式中使用 Pattern.any 實體可讓您指定表單名稱的開頭和結尾，讓 LUIS 能夠正確擷取表單名稱。

**當模式可讓您提供較少的範例語句時，如果偵測不到實體，模式就不會進行比對。**

## <a name="add-example-utterances-to-the-existing-intent-findform"></a>將範例語句新增到現有意圖 FindForm 
如果很難建立和標示 FormName 實體，請移除預先建置的 keyPhrase 實體。 

1. 從上方瀏覽列中選取 [建置]，然後從左方瀏覽列中選取 [意圖]。

2. 從意圖清單中選取 [FindForm]。

3. 新增一些範例語句：

    |範例語句|
    |--|
    |**實驗室發生火災時該怎麼辦**的表單在哪裡，而且當我閱讀該表單之後需要誰簽名？|
    |**新進員工要求調職**位於伺服器上的何處？|
    |「**主要校園的健康與福利要求**」的作者是誰且最新版本為何？|
    |我正在尋找名為「包含實體資產在內的辦公室搬遷要求」的表單。 |

    在沒有 Pattern.any 實體的情況下，LUIS 很難理解表單標題結束位置，因為表單名稱有許多種變化。

## <a name="create-a-patternany-entity"></a>建立 Pattern.any 實體
Pattern.any 實體可擷取各種不同長度的實體。 它僅適用於模式，因為模式會標示實體的開頭和結尾。 如果您發現模式在包含 Pattern.any 時所擷取的實體不正確，請使用[明確清單](luis-concept-patterns.md#explicit-lists)來更正此問題。 

1. 從左側瀏覽窗格中選取 [Entities] \(實體\)。

2. 選取 [建立新實體]、輸入名稱 `FormName`，然後選取 [Pattern.any] 作為類型。 選取 [完成] 。 

    因為 Pattern.any 只在模式中才有效，因此，您不能在意圖中標示實體。 

    如果您想要使所擷取的資料包含其他實體 (例如 number 或 datetimeV2)，您需要建立包含 Pattern.any 以及 number 和 datetimeV2 的複合實體。

## <a name="add-a-pattern-that-uses-the-patternany"></a>新增使用 Pattern.any 的模式

1. 從左側瀏覽窗格中選取 [Patterns] \(模式\)。

2. 選取 [FindForm] 意圖。

3. 輸入下列使用新實體的範本語句：

    |範本語句|
    |--|
    |["]{FormName}["] 的表單在哪裡，而且當我閱讀該表單之後需要誰簽名[？]|
    |["]{FormName}["] 位於伺服器上的何處[？]|
    |["] {FormName}["] 的作者是誰且最新版本為何[？]|
    |我正在尋找名為 ["]{FormName}["] 的表單[。]|

    如果您想要將表單的變化納入考量 (例如，單引號而非雙引號，或是句號而非問號)，請針對每種變化建立新模式。

4. 如果您移除了 keyPhrase 實體，請將它新增回應用程式。 

5. 將應用程式定型。


## <a name="test-the-new-pattern-for-free-form-data-extraction"></a>測試新模式以擷取自由格式資料
1. 從頂端列選取 [Test] \(測試\) 來開啟測試面板。 

2. 輸入下列語句： 

    `Where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

3. 選取結果底下的 [Inspect] \(檢查\)，以查看實體和意圖的測試結果。

    系統會先尋找實體 `FormName`，然後才尋找可判斷意圖的模式。 如果您的測試結果未偵測到實體，因而未找到模式，您就必須針對意圖新增更多範例語句 (而非模式)。

4. 選取頂端瀏覽列中的 [Test] \(測試\) 按鈕。

## <a name="clean-up-resources"></a>清除資源
當不再需要 LUIS 應用程式時，請將其刪除。 若要執行此動作，請選取應用程式清單中應用程式名稱右邊的省略符號 (***...***)，然後選取 [刪除]。 在 [Delete app?] \(刪除應用程式?\) 快顯對話方塊上，選取 [Ok] \(確定\)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [了解如何將角色與模式搭配使用](luis-tutorial-pattern-roles.md)