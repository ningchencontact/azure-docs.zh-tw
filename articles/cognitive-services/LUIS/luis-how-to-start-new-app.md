---
title: 建立新的應用程式-LUIS
titleSuffix: Azure Cognitive Services
description: 在 Language Understanding (LUIS) 網頁上建立及管理應用程式。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: diberry
ms.openlocfilehash: 5939886f68a877c4cfad752fe93c4f0eca8f80cf
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70258520"
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
 

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-new-app-in-luis"></a>在 LUIS 中建立新的應用程式

1. 在 [我的應用程式] 頁面上，選取 [建立新的應用程式]。

    ![LUIS 應用程式清單](./media/luis-create-new-app/apps-list.png)


2. 在對話方塊中，將您的應用程式命名為 "TravelAgent"。

    ![建立新的應用程式對話方塊](./media/luis-create-new-app/create-app.png)

3. 選擇您的應用程式文化特性 (對於 TravelAgent 應用程式，選擇 [英文])，然後選取 [完成]。 

    > [!NOTE]
    > 建立應用程式之後便無法變更文化特性 (Culture)。 

## <a name="import-an-app-from-file"></a>從檔案匯入應用程式

1. 在 [我的應用程式] 頁面上，選取 [匯入新的應用程式]。
1. 在快顯視窗中，選取有效的應用程式 JSON 檔案，然後選取 [**完成**]。

### <a name="import-errors"></a>匯入錯誤

可能的錯誤包括： 

* 具有該名稱的應用程式已經存在。 若要修正此問題，請重新匯入應用程式，並將**選擇性名稱**設定為新名稱。 

## <a name="export-app-for-backup"></a>匯出應用程式以進行備份

1. 在**我的應用程式** 頁面上，選取 **匯出**。
1. 選取 [**匯出為 JSON**]。 您的瀏覽器會下載應用程式的作用中版本。
1. 將此檔案新增至您的備份系統以封存模型。

## <a name="export-app-for-containers"></a>匯出容器的應用程式

1. 在**我的應用程式** 頁面上，選取 **匯出**。
1. 選取 [**匯出為容器**]，然後選取您想要匯出的已發佈位置（生產或階段）。
1. 將此檔案與您的[LUIS 容器](luis-container-howto.md)搭配使用。 

    如果您想要匯出已定型但尚未發行的模型以與 LUIS 容器搭配使用，請移至 [**版本**] 頁面，並從該處匯出。 

## <a name="delete-app"></a>刪除應用程式

1. 在 [我的應用程式] 頁面上，選取應用程式資料列結尾的三個點 (...)。
1. 從功能表中選取 [刪除]。
1. 在確認視窗中選取 [確定]。

## <a name="next-steps"></a>後續步驟

您在應用程式中的第一項工作是[新增對應方式](luis-how-to-add-intents.md)。
