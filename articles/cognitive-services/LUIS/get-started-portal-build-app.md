---
title: 快速入門：使用 LUIS 入口網站建立新的應用程式
titleSuffix: Language Understanding - Azure Cognitive Services
description: 在本快速入門中，您將在 LUIS 入口網站中建立新的應用程式。 建立應用程式的基本組件 (意圖和實體)，然後藉由在互動式測試面板中提供範例使用者語句進行測試，以取得預測意圖。 建置應用程式是免費的，且不需要 Azure 訂用帳戶。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: e97be28261d28c2a72e507adcdac0248691745c7
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2019
ms.locfileid: "57783259"
---
# <a name="quickstart-create-a-new-app-in-the-luis-portal"></a>快速入門：在 LUIS 入口網站中建立新的應用程式

在本快速入門中，您將在 [LUIS 入口網站](https://www.luis.ai)中建立新的應用程式。 建立應用程式的基本組件 (**意圖**和**實體**)，然後藉由在互動式測試面板中提供範例使用者語句進行測試，以取得預測意圖。

建置應用程式是免費的，且不需要 Azure 訂用帳戶。 當您準備好要部署應用程式時，您即可建立 Azure 認知服務資源，並將其指派給應用程式。 此部署程序將於[下一個快速入門](get-started-portal-deploy-app.md)說明。

## <a name="create-app"></a>建立應用程式 

1. 在瀏覽器中開啟 [LUIS 入口網站](https://www.luis.ai)並登入。 如果這是您第一次登入，則必須建立免費的 LUIS 入口網站使用者帳戶。

1. 從內容工具列中選取 [建立新的應用程式]。

    [![在 LUIS 入口網站中建立新的應用程式](./media/get-started-portal-build-app/create-app-in-portal.png)](./media/get-started-portal-build-app/create-app-in-portal.png#lightbox)

1. 在快顯視窗中，使用下列設定進行應用程式設定，然後選取 [完成]。

    |設定名稱| 值 | 目的|
    |--|--|--|
    |Name|`myEnglishApp`|唯一的 LUIS 應用程式名稱<br>必要|
    |文化特性|**英文**|使用者語句的語言 **en-us**<br>必要|
    |說明|`App made with LUIS Portal`|應用程式的描述<br>選用|

    ![輸入新的應用程式設定](./media/get-started-portal-build-app/create-new-app-settings.png)


## <a name="create-intent"></a>建立意圖 

此應用程式建立後，下一個步驟是建立意圖。 意圖是對使用者的文字進行分類的方式之一。 如果您人力資源應用程式有兩個函式：一個用來協助使用者尋找及應徵工作，另一個用來尋找應徵工作的表單，這兩個不同的_意圖_會對應於下列意圖：

|意圖|使用者的文字範例<br>稱為_語句_|
|--|--|
|ApplyForJob|`I want to apply for the new software engineering position in Cairo.`|
|FindForm|`Where is the job transfer form hrf-123456?`|

1. 應用程式建立後，您會在 [建置] 區段的 [意圖] 頁面上。 選取 [Create new intent] \(建立新意圖\)。 

    [![選取建立新的意圖按鈕](./media/get-started-portal-build-app/create-new-intent-button.png)](./media/get-started-portal-build-app/create-new-intent-button.png#lightbox)

1. 輸入意圖名稱 `FindForm`，然後選取 [完成]。

    ![輸入 FindForm 的意圖名稱](./media/get-started-portal-build-app/create-new-intent-dialog.png)

## <a name="add-example-utterance"></a>新增範例語句 

建立意圖後，下一個步驟是新增範例語句。 這是使用者在聊天機器人或其他用戶端應用程式中輸入的文字，會將使用者文字的意圖對應至 LUIS 意圖。 

就此範例應用程式的 `FindForm` 意圖而言，範例語句將包含表單號碼，這是語句內的重要資訊，因為用戶端應用程式必須有此資訊才能因應使用者的要求。 

請將以下 15 個範例語句新增至 `FindForm` 意圖。 

|#|範例語句|
|--|--|
|1|尋找 hrf-123456|
|2|人力資源表單 hrf-234591 在哪裡？|
|3|hrf-345623 在哪裡|
|4|可以將 hrf-345794 傳送給我嗎|
|5|應徵內部工作需要 hrf-234695 嗎？|
|6|我的經理需要知道我要應徵 hrf-234091 的工作嗎|
|7|hrf-234918 應傳送至何處？ 送到時我會收到電子郵件回應嗎？|
|8|hrf-234555|
|9|何時更新了 hrf-234987？|
|10|應徵工程師職位應使用 hrf-876345 表單嗎|
|11|我的公開徵才提交的是新版的 hrf-765234 嗎？|
|12|應徵國際職位應使用 hrf-234234 嗎？|
|13|hrf-234598 有拼字錯誤|
|14|有新的需求應編輯 hrf-234567 嗎|
|15|hrf-123456、hrf-123123、hrf-234567|

這些範例語句在以下幾方面有刻意設計的差異：

* 語句長度
* 標點符號
* 用字
* 動詞時態
* 文字順序

[![輸入 FindForm 意圖的範例語句](./media/get-started-portal-build-app/add-example-utterance.png)](./media/get-started-portal-build-app/add-example-utterance.png#lightbox)

## <a name="create-regular-expression-entity"></a>建立規則運算式實體 

若要取得在執行階段預測回應中傳回的表單號碼，表單必須標示為實體。 由於表單號碼文字高度結構化，因此可以使用規則運算式實體來標示。 請依照下列步驟建立此實體。 

1. 從左側導覽功能表中選取 [實體]。 

1. 選取 [實體] 頁面上的 [建立新的實體]。

1. 輸入名稱 `Human Resources Form Number`，選取 **Regex** 實體類型，然後輸入規則運算式 `hrf-[0-9]{6}`。 這會比對出常值字元 `hrf-`，並僅允許 6 個數字。 

    ![輸入規則運算式實體的實體資訊](./media/get-started-portal-build-app/create-regular-expression-entity.png)

1. 選取 [完成] 。 

## <a name="add-example-utterances-to-none-intent"></a>將範例語句新增至 None 意圖

**None** 意圖是後援意圖，不應保留為空白。 每當應用程式的其餘意圖有 10 個語句，此意圖就應有 1 個語句。 

**None** 意圖的範例語句應在您的用戶端應用程式定義域以外。 

1. 從左功能表中選取 [意圖]，然後從 [意圖] 清單中選取 [None]。

1. 請將下列範例語句新增至意圖：

    |None 意圖範例語句|
    |--|
    |狗一直叫很煩人|
    |幫我訂披薩|
    |海裡面的企鵝|

    在此人力資源應用程式中，這些範例語句不在定義域內。 如果您的人力資源定義域包含動物、食物或海洋，這些範例語句就不可用於 **None** 意圖。 

## <a name="train-the-app"></a>進行應用程式定型

在右上方的導覽中選取**定型**，以將意圖和實體模型的變更套用至目前的應用程式版本。 

## <a name="look-at-the-regular-expression-entity-in-the-example-utterances"></a>查看範例語句中的規則運算式實體

1. 從左側功能表中選取 [意圖]，然後選取 **FindForm** 意圖，以確認實體位於 **FindForm** 意圖中。 

    實體會標示它在範例語句中的顯示之處。 如果您想要查看原始文字，請從工具列切換 [實體檢視]，而不是使用實體名稱。

    [![標示了實體的所有範例語句](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png)](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png#lightbox)

## <a name="test-your-new-app-with-the-interactive-test-pane"></a>使用互動式測試窗格測試新的應用程式

使用 LUIS 入口網站中的互動式**測試**窗格，驗證實體擷取自應用程式尚未看過新語句。

1. 從右上方的功能表中選取 [測試]。

1. 新增語句，然後按 Enter 鍵：

    ```Is there a form named hrf-234098```

    ![對測試窗格中的新語句進行測試](./media/get-started-portal-build-app/test-new-utterance.png)

    最高分的預測意圖為 **FindForm**，信賴度超過 90% (0.977)，而**人力資源表單號碼**實體的擷取值為 hrf-234098。 

## <a name="clean-up-resources"></a>清除資源
在完成本快速入門後，若您不繼續進行下一個快速入門，請從頂端導覽功能表中選取 [我的應用程式]。 然後，從清單中選取應用程式左側的核取方塊，再從清單上方的內容工具列中選取 [刪除]。 

[![刪除 [我的應用程式] 清單中的應用程式](./media/get-started-portal-build-app/delete-app.png)](./media/get-started-portal-build-app/delete-app.png#lightbox)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [2.部署應用程式](get-started-portal-deploy-app.md)