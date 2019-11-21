---
title: 反復應用程式設計-LUIS
titleSuffix: Azure Cognitive Services
description: LUIS 在模型變更、語句範例、發佈及從端點查詢收集資料的反覆循環中學習時，成效最佳。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 12a1f2304e4255eb9abd04ab2e2d0726066dd1e6
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73487772"
---
# <a name="authoring-cycles-and-versions"></a>撰寫週期和版本

您的 LUIS 應用程式在反復執行迴圈中學習的效果最佳：

* 建立新版本
* 編輯應用程式架構
    * 範例語句的意圖
    * 實體
    * 特性
* 指導
* test
* publish
    * 在主動式學習的預測端點測試
* 從端點查詢收集資料

![撰寫循環](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-schema"></a>建立 LUIS 架構

應用程式架構的目的是要定義使用者要求的內容（意圖或意圖），而問題的哪些部分會提供有助於判斷答案的詳細資料（實體）。 

應用程式架構必須專屬於應用程式域，才能判斷相關的單字和片語，以及一般的文字順序。 

範例語句代表應用程式預期會在執行時間取得的使用者輸入。 

架構需要意圖，而且_應該有_實體。 

### <a name="example-schema-of-intents"></a>意圖的範例架構

最常見的架構是使用意圖進行組織的意圖架構。 這種類型的架構取決於 LUIS，以判斷使用者的意圖。 

如果此架構類型有助於 LUIS 判斷意圖，則可能會有實體。 例如，出貨實體（作為意圖的描述項）可協助 LUIS 判斷出貨的意圖。 

### <a name="example-schema-of-entities"></a>實體的範例架構

實體架構著重于實體，也就是要從語句中提取的資料。 

對用戶端應用程式而言，語句的意圖較少或不重要。 

組織實體架構的常見方法是將所有範例語句新增至 None 意圖。 

### <a name="example-of-a-mixed-schema"></a>混合架構的範例

最強大且成熟的架構是具有完整範圍的實體和功能的意圖架構。 此架構可以做為意圖或實體架構，並隨著用戶端應用程式需要這些資訊而成長以包含兩者的概念。 

## <a name="add-example-utterances-to-intents"></a>將範例語句新增至意圖

LUIS 需要每個**意圖**中的幾個範例語句。 範例語句需要有足夠的單字選擇和文字順序變化，才能判斷語句的用途。 

> [!CAUTION]
> 請勿在 bulk 中新增範例語句。 從15到30個特定和不同的範例開始。 

每個範例語句都必須有任何**必要的資料，才能使用實體來進行解壓縮**設計和標記。 

|Key 元素|目的|
|--|--|
|意圖|將使用者語句**分類**為單一意圖或動作。 例如 `BookFlight` 與 `GetWeather`。|
|實體|從需要的語句**解壓縮**資料，以完成意圖。 範例包括旅遊的日期和時間，以及位置。|

藉由將語句指派給**None**意圖，您可以將 LUIS 應用程式設計成忽略與您的應用程式網域無關的語句。 

## <a name="test-and-train-your-app"></a>測試和定型您的應用程式

一旦每個意圖中有15到30個不同的範例語句，並將所需的實體標示為，您就必須進行測試和[定型](luis-how-to-train.md)。 

## <a name="publish-to-a-prediction-endpoint"></a>發行至預測端點

請確定您發佈應用程式，使其可在您需要的[預測端點區域](luis-reference-regions.md)中使用。 

## <a name="test-your-published-app"></a>測試已發佈的應用程式

您可以從 HTTPS 預測端點測試已發佈的 LUIS 應用程式。 從預測端點進行測試，可讓 LUIS 選擇任何具有低信賴度[審查](luis-how-to-review-endpoint-utterances.md)的語句。  

## <a name="create-a-new-version-for-each-cycle"></a>為每個週期建立新的版本

LUIS 中的版本類似於傳統程式設計中的版本。 每個版本都是應用程式階段的快照集。 在變更應用程式之前，請建立新版本。 更容易回到較舊的版本，然後嘗試移除意圖並語句到先前的狀態。

版本識別碼由字元、數字或 '.' 所組成，且長度不可超過 10 個字元。

初始版本 (0.1) 是預設的作用中版本。 

### <a name="begin-by-cloning-an-existing-version"></a>從複製現有版本開始

複製現有的版本，做為新版本的起點。 複製版本之後，新版本就會變成**作用中**版本。 

### <a name="publishing-slots"></a>發行位置
您可以發行至階段和生產位置。 每個位置的版本可以不同，也可以相同。 這適用于在發行至生產環境之前驗證變更，這適用于 bot 或其他 LUIS 呼叫應用程式。 

您的應用程式[端點](luis-glossary.md#endpoint)不會自動提供已定型的版本。 若要在您的應用程式端點上提供某個版本，您必須[發佈](luis-how-to-publish-app.md)或重新發佈該版本。 您可以發行至**預備**和**生產環境**，讓您可以在端點上使用兩個版本的應用程式。 如果您需要在端點上提供更多應用程式版本，則應該將版本匯出，然後再重新匯入至新的應用程式。 新應用程式會有不同的應用程式識別碼。

### <a name="import-and-export-a-version"></a>匯入及匯出版本
您可以在應用程式層級匯入版本。 該版本會變成作用中版本，並在應用程式檔的 `versionId` 屬性中使用版本識別碼。 您也可以在版本層級匯入到現有的應用程式。 新版本會變成作用中版本。 

您可以在應用程式或版本層級匯出版本。 唯一的差別在於，在應用程式層級匯出的版本是目前作用中的版本，而在版本層級，您則可以在 [**[Settings](luis-how-to-manage-versions.md)** ] \(設定\) 頁面上選擇任何要匯出的版本。 

匯出的檔案不包含：

* 機器學習的資訊，因為應用程式會在匯入後重新訓練
* 參與者資訊

若要備份 LUIS 應用程式架構，請從 LUIS 入口網站匯出版本。

## <a name="manage-contributor-changes-with-versions-and-apps"></a>使用版本和應用程式管理參與者變更

LUIS 提供應用程式參與者的概念，方法是提供 Azure 資源層級許可權。 將此概念與版本控制結合，以提供目標共同作業。 

使用下列技術來管理應用程式的參與者變更。

### <a name="manage-multiple-versions-inside-the-same-app"></a>管理相同應用程式內的多個版本
針對每個作者，一開始先從基底版本[複製](luis-how-to-manage-versions.md#clone-a-version)。 

每位作者都會對自己的應用程式版本進行變更。 在每個作者對模型感到滿意之後，請將新版本匯出成 JSON 檔案。  

已匯出的應用程式（json 或 lu 檔案）可以針對變更進行比較。 結合檔案以建立新版本的單一檔案。 變更**versionId**屬性以表示新的合併版本。 將該版本匯入至原始應用程式。 

此方法可讓您有一個作用中版本、一個預備版本，以及一個已發佈版本。 您可以在 [[互動式測試] 窗格](luis-interactive-test.md)中，將作用中版本的結果與已發行的版本（階段或生產）做比較。

### <a name="manage-multiple-versions-as-apps"></a>以應用程式方式管理多個檔案
[匯出](luis-how-to-manage-versions.md#export-version)基底版本。 每個作者都匯入版本。 匯入應用程式的人即為該版本的擁有者。 當他們修改完應用程式時，請匯出版本。 

匯出的應用程式會是 JSON 格式的檔案，可用來與基底匯出版本比較變更。 結合檔案以建立新版本的單一 JSON 檔案。 變更 JSON 中的 **versionId** 屬性，以表示新的合併版本。 將該版本匯入至原始應用程式。

深入瞭解如何[撰寫來自共同作業者的投稿](luis-how-to-collaborate.md)。

## <a name="review-endpoint-utterances-to-begin-the-new-authoring-cycle"></a>審查端點語句以開始新的撰寫週期

當您完成撰寫循環時，可以重新開始。 一開始請先複習以低信賴度標記的[預測端點語句](luis-how-to-review-endpoint-utterances.md)LUIS。 請檢查這些語句是否有正確的預測意圖，以及是否已解壓縮正確且完整的實體。 一旦您審查並接受變更之後，審核清單應該是空的。  

## <a name="next-steps"></a>後續步驟

了解[共同作業](luis-concept-keys.md)的相關概念。
