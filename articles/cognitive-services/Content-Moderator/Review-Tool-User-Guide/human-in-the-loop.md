---
title: 了解檢閱工具概念-Content Moderator
titlesuffix: Azure Cognitive Services
description: 深入了解內容仲裁者檢閱工具，可協調合併的人工智慧及人工檢閱仲裁投入時間的網站。
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.date: 03/15/2019
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.author: sajagtap
ms.openlocfilehash: b7ec997fd3e9bfe294050893d80fd57a96a47aae
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "61269979"
---
# <a name="content-moderator-review-tool"></a>內容仲裁審查工具

Azure 內容仲裁提供結合與人工審核，機器學習服務內容仲裁服務和[審核工具](https://contentmoderator.cognitive.microsoft.com)網站是使用者易記的後端，可讓這些服務的詳細的存取。

![在瀏覽器檢閱工具儀表板](./images/0-dashboard.png)

## <a name="what-it-does"></a>作用

[審核工具](https://contentmoderator.cognitive.microsoft.com)，在使用機器輔助仲裁 Api，搭配可讓您完成下列工作中的內容仲裁程序：

- 到中等的多種格式 （文字、 影像和視訊） 的內容中使用一的組工具。
- 自動建立的人類看得[檢閱](../review-api.md#reviews)審核 API 時產生隨附在中。
- 指派和提報內容的評論，依內容的類別或經驗層級的多個檢閱小組。
- 使用預設或自訂邏輯的篩選器 ([工作流程](../review-api.md#workflows)) 來排序和追蹤的內容，而不需要撰寫任何程式碼。
- 使用[連接器](./configure.md#connectors)處理 Microsoft PhotoDNA、 文字分析與臉部 Api，以及內容仲裁 Api 的內容。
- 建置您自己的連接器，來建立工作流程的任何 API 或商務程序。
- 取得您內容審核程序的關鍵效能計量。

## <a name="review-tool-dashboard"></a>檢閱工具儀表板

在 **儀表板**索引標籤上，您可以看到工具內完成的內容檢閱的關鍵計量。 請參閱許多總計，完成，且暫止的影像、 文字和影片內容的評論。 您也可以看到的使用者和小組已完成檢閱，以及已套用的仲裁標記的細目。

![檢視儀表板](images/0-dashboard.png)

## <a name="review-tool-credentials"></a>檢閱工具認證

當您註冊[審核工具](https://contentmoderator.cognitive.microsoft.com)，將提示您選取 Azure 區域，您的帳戶。 這是因為[審核工具](https://contentmoderator.cognitive.microsoft.com)會產生免費的試用版金鑰，Azure 內容仲裁服務; 您將需要此金鑰才能存取任何服務從用戶端 SDK 或 REST 呼叫。 您可以選取，以檢視您的金鑰和 API 端點 URL**設定** > **認證**。

![Content Moderator 認證](images/settings-6-credentials.png)

## <a name="next-steps"></a>後續步驟

請參閱[設定檢閱工具](./configure.md)以了解如何存取檢閱工具資源和變更設定。