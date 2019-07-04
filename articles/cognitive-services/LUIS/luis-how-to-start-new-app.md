---
title: 建立新的應用程式
titleSuffix: Language Understanding - Azure Cognitive Services
description: 在 Language Understanding (LUIS) 網頁上建立及管理應用程式。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: b8b0cebf4ba47f875caacfcfbf89b84551b41333
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341866"
---
# <a name="create-a-new-luis-app-in-the-luis-portal"></a>在 LUIS 入口網站中建立新的 LUIS 應用程式
有好幾種方法可建立 LUIS 應用程式。 您可以在 [LUIS](https://www.luis.ai) 入口網站中建立 LUIS 應用程式，或透過 LUIS 撰寫 [API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f)。

## <a name="using-the-luis-portal"></a>使用 LUIS 入口網站
您可在 LUIS 入口網站中以數種方式建立新的應用程式：

* 從空的應用程式著手，並建立意圖、語句和實體。
* 從空的應用程式著手，並新增[預建網域](luis-how-to-use-prebuilt-domains.md)。
* 從已經包含意圖、語句和實體的 JSON 檔案匯入 LUIS 應用程式。

## <a name="using-the-authoring-apis"></a>使用撰寫 API
您可使用撰寫 API 以數種方式建立新的應用程式：

* 從空的應用程式[著手](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f)，並建立意圖、語句和實體。
* 從預建網域[開始](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/59104e515aca2f0b48c76be5)。  


<a name="export-app"></a>
<a name="import-new-app"></a>
<a name="delete-app"></a>
 

## <a name="create-new-app-in-luis"></a>在 LUIS 中建立新的應用程式

1. 在 [我的應用程式]  頁面上，選取 [建立新的應用程式]  。

    ![LUIS 應用程式清單](./media/luis-create-new-app/apps-list.png)


2. 在對話方塊中，將您的應用程式命名為 "TravelAgent"。

    ![建立新的應用程式對話方塊](./media/luis-create-new-app/create-app.png)

3. 選擇您的應用程式文化特性 (對於 TravelAgent 應用程式，選擇 [英文])，然後選取 [完成]  。 

    > [!NOTE]
    > 建立應用程式之後便無法變更文化特性 (Culture)。 

## <a name="import-an-app-from-file"></a>從檔案匯入應用程式

1. 在 [我的應用程式]  頁面上，選取 [匯入新的應用程式]  。
1. 在快顯對話方塊中，請選取有效的應用程式的 JSON 檔案，然後按**完成**。

### <a name="import-errors"></a>匯入錯誤

可能的錯誤是： 

* 已經存在具有該名稱的應用程式。 若要修正此問題，重新匯入應用程式，並設定**選擇性名稱**至新的名稱。 

## <a name="export-app-for-backup"></a>匯出進行備份的應用程式

1. 在 **我的應用程式**頁面上，選取**匯出**。
1. 選取 **將匯出為 JSON**。 您的瀏覽器會下載應用程式的作用中版本。
1. 您可以將這個檔案加入您要封存之模型的備份系統。

## <a name="export-app-for-containers"></a>適用於容器的匯出應用程式

1. 在 **我的應用程式**頁面上，選取**匯出**。
1. 選取 **匯出為容器**然後選取 您要匯出哪一個已發佈的位置 （「 生產 」 或 「 階段 」）。
1. 使用此檔案與您[LUIS 容器](luis-container-howto.md)。 

    如果您有興趣，但未定型的匯出，但與 LUIS 容器所使用的已發行的模型中，請移至**版本**頁面上，及從中匯出。 

## <a name="delete-app"></a>刪除應用程式

1. 在 [我的應用程式]  頁面上，選取應用程式資料列結尾的三個點 (...)。
1. 從功能表中選取 [刪除]  。
1. 在確認視窗中選取 [確定]  。

## <a name="next-steps"></a>後續步驟

您在應用程式中的第一項工作是[新增對應方式](luis-how-to-add-intents.md)。
