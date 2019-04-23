---
title: 定義和使用內容的工作流程透過檢閱工具-Content Moderator
titlesuffix: Azure Cognitive Services
description: 您可以使用 Azure 內容仲裁者工作流程設計工具來定義自訂工作流程與根據內容原則的臨界值。
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 04/04/2019
ms.author: sajagtap
ms.openlocfilehash: 006f7d6691b8872aaa7ff8ccacff484585761d00
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59795792"
---
# <a name="define-and-use-moderation-workflows"></a>定義及使用仲裁工作流程

在本指南中，您將了解如何設定和使用[工作流程](../review-api.md#workflows)上[審核工具](https://contentmoderator.cognitive.microsoft.com)網站。 工作流程是雲端架構的自訂的篩選，您可以使用來更有效率地處理內容。 工作流程可以連接到各種不同的方式篩選內容，並採取適當的動作的服務。 本指南將說明如何篩選內容，並將設定一般仲裁案例中的人工審核使用 Content Moderator 連接器 （這包含預設值）。

## <a name="create-a-new-workflow"></a>建立新的工作流程

移至[內容仲裁審查工具](https://contentmoderator.cognitive.microsoft.com/)並登入。 在 [設定] 索引標籤中，選取 [工作流程]。

![[工作流程] 設定](images/2-workflows-0.png)

在下一個畫面上，選取**新增工作流程**。

![新增工作流程](images/2-workflows-1.png)

### <a name="assign-a-name-and-description"></a>指派名稱和描述

命名您的工作流程，輸入描述，並選擇影像或文字，是否將處理工作流程。

![工作流程名稱和描述](images/image-workflow-create.PNG)

### <a name="define-evaluation-criteria"></a>定義評估準則

在下一個畫面上，請移至**如果**一節。 在上方的下拉式功能表中，選擇**條件**。 這可讓您設定的工作流程將會採取動作的條件。 如果您想要使用多個條件，請選擇**組合**改。 

接下來，選取 連接器。 這個範例會使用 **Content Moderator**。 根據您選擇的連接器，您會收到不同的資料輸出選項。 請參閱[連接器](./configure.md#connectors)檢閱工具設定指南，以了解如何設定其他連接器的一節。

![選取工作流程連接器](images/image-workflow-connect-to.PNG)

選擇所需的輸出來使用，並設定它對檢查的條件。

![定義工作流程的條件](images/image-workflow-condition.PNG)

### <a name="define-the-action"></a>定義動作

移至**然後**區段中，您在其中選取動作。 下列範例會建立映像檢閱，並將指派的標記。 （選擇性） 您可以新增替代 (Else) 路徑，然後設定動作，以及。

![定義工作流程的動作](images/image-workflow-action.PNG)

### <a name="save-the-workflow"></a>儲存工作流程

請注意工作流程的名稱;您需要開始工作流程 API （如下所示） 中的仲裁作業名稱。 最後，將儲存工作流程使用**儲存**在頁面頂端的按鈕。

## <a name="test-the-workflow"></a>測試工作流程

既然您已定義自訂工作流程，請使用範例內容測試它。 移至**工作流程**，然後選取 [對應**執行的工作流程**] 按鈕。

![工作流程測試](images/image-workflow-execute.PNG)

儲存此[範例映像](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg)到本機磁碟機。 然後選取**選擇檔案**並將映像上傳至工作流程。

![使用引號，重疊在映像上執行器](images/sample-text.jpg)

### <a name="track-progress"></a>追蹤進度

您可以在下一步 快顯視窗中檢視工作流程的進度。

![追蹤工作流程的執行](images/image-workflow-job.PNG)

### <a name="verify-workflow-action"></a>驗證工作流程動作

移至**映像**索引標籤下**檢閱**，並確認新建立的映像的檢閱。

![檢閱映像](images/image-workflow-review.PNG)

## <a name="next-steps"></a>後續步驟

在本指南中，您已了解如何設定及使用從 Content Moderator 審核工作流程[審核工具](https://contentmoderator.cognitive.microsoft.com)。 接下來，請參閱 < [REST API 指南](../try-review-api-workflow.md)以了解如何以程式設計方式建立工作流程。
