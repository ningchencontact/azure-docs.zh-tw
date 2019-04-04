---
title: 檢閱工作流程和工作概念-Content Moderator
titlesuffix: Azure Cognitive Services
description: 深入了解評論、 工作流程和工作
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: sajagtap
ms.openlocfilehash: c1d4ef640e2ae072dacba7a665b6689e3224c55c
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2019
ms.locfileid: "58756300"
---
# <a name="content-moderation-reviews-workflows-and-jobs"></a>內容仲裁審查、 工作流程和工作

Content Moderator 結合機器輔助仲裁，在迴圈人類的功能，來建立最佳的仲裁的處理序的真實世界案例。 它會透過以雲端為基礎[審核工具](https://contentmoderator.cognitive.microsoft.com)。 本指南中，您將了解審核工具的核心概念： 檢閱、 工作流程和工作。

## <a name="reviews"></a>評論

檢閱中，在內容上傳至審核工具和之下**檢閱** 索引標籤。從這裡開始，使用者可以更改套用的標籤，並套用自己自訂的標籤，適當地。 當使用者提交檢閱時，結果會傳送至指定的回呼端點，以及內容會從站台移除。

![在瀏覽器中，在 [檢閱] 索引標籤上，開啟檢閱工具網站](./Review-Tool-user-Guide/images/image-workflow-review.png)

請參閱[檢閱工具指南](./review-tool-user-guide/review-moderated-images.md)來開始建立評論，或查看[REST API 指南](./try-review-api-review.md)若要了解如何以程式設計方式操作。

## <a name="workflows"></a>工作流程

工作流程是雲端架構自訂篩選器內容。 工作流程可以連接到各種不同的方式篩選內容，並採取適當的動作的服務。 使用 Content Moderator 連接器，工作流程可以自動套用仲裁標記，並建立已提交的內容檢閱。

### <a name="view-workflows"></a>檢視工作流程

若要檢視您現有的工作流程，請前往[審核工具](https://contentmoderator.cognitive.microsoft.com/)，然後選取**設定** > **工作流程**。

![預設工作流程](images/default-workflow-listed.PNG)

工作流程可以完全稱為 JSON 字串，使其可存取以程式設計的方式。 如果您選取**編輯**工作流程選項，然後選取**JSON**索引標籤上，您會看到 JSON 運算式如下所示：

```json
{
    "Type": "Logic",
    "If": {
        "ConnectorName": "moderator",
        "OutputName": "isAdult",
        "Operator": "eq",
        "Value": "true",
        "Type": "Condition"
        },
    "Then": {
    "Perform": [
    {
        "Name": "createreview",
        "CallbackEndpoint": null,
        "Tags": []
    }
    ],
    "Type": "Actions"
    }
}
```

請參閱[檢閱工具指南](./review-tool-user-guide/workflows.md)若要開始建立及使用工作流程，請參閱[REST API 指南](./try-review-api-workflow.md)若要了解如何以程式設計方式操作。

## <a name="jobs"></a>工作

仲裁作業可做為一種包裝函式的內容仲裁、 工作流程及檢閱功能。 工作會掃描您的內容使用 Content Moderator 影像審核 API 或文字審核 API，然後檢查它與指定的工作流程。 根據工作流程的結果，可能或可能不會建立內容的評論[審核工具](./review-tool-user-guide/human-in-the-loop.md)。 雖然可以建立並設定其各自的 Api 檢閱和工作流程，作業 API 可讓您取得在整個程序 （可以傳送至指定的回呼端點） 的詳細的報告。

請參閱[REST API 指南](./try-review-api-job.md)若要開始使用作業。

## <a name="next-steps"></a>後續步驟

* 試用[作業 API 主控台](try-review-api-job.md)，並使用 REST API 程式碼範例。 如果您已熟悉 Visual Studio 和 C#，也請參閱[作業 .NET 快速入門](moderation-jobs-quickstart-dotnet.md)。 
* 若為檢閱者，請開始使用[檢閱 API 主控台](try-review-api-review.md)，並使用 REST API 程式碼範例。 然後請參閱[檢閱 .NET 快速入門](moderation-reviews-quickstart-dotnet.md)。
* 若為影片檢閱者，請使用[影片檢閱快速入門](video-reviews-quickstart-dotnet.md)，並了解如何[在影片檢閱中新增文字記錄](video-transcript-reviews-quickstart-dotnet.md)。
