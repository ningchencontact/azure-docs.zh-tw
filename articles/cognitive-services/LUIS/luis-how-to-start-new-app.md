---
title: 建立新的應用程式
titleSuffix: Language Understanding - Azure Cognitive Services
description: 在 Language Understanding (LUIS) 網頁上建立及管理應用程式。
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: ddfee80c67c22c7c6016ed87dc17925c91637d21
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/21/2018
ms.locfileid: "53713993"
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

1. 在 [我的應用程式] 頁面上，選取 [建立新的應用程式]。

    ![LUIS 應用程式清單](./media/luis-create-new-app/apps-list.png)


2. 在對話方塊中，將您的應用程式命名為 "TravelAgent"。

    ![建立新的應用程式對話方塊](./media/luis-create-new-app/create-app.png)

3. 選擇您的應用程式文化特性 (對於 TravelAgent 應用程式，選擇 [英文])，然後選取 [完成]。 

    > [!NOTE]
    > 建立應用程式之後便無法變更文化特性 (Culture)。 


## <a name="next-steps"></a>後續步驟

您在應用程式中的第一項工作是[新增對應方式](luis-how-to-add-intents.md)。