---
title: 定義使用 REST API 主控台-Content Moderator 審核工作流程
titlesuffix: Azure Cognitive Services
description: 您可以使用 Azure 內容仲裁者檢閱 Api 來定義自訂工作流程與根據內容原則的臨界值。
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/14/2019
ms.author: sajagtap
ms.openlocfilehash: e150b1321f2fbd348e737222c752203281503643
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60605851"
---
# <a name="define-and-use-moderation-workflows-rest"></a>定義及使用仲裁工作流程 (REST)

工作流程是雲端架構的自訂的篩選，您可以使用來更有效率地處理內容。 工作流程可以連接到各種不同的方式篩選內容，並採取適當的動作的服務。 本指南將說明如何使用工作流程 REST Api，以透過 API 主控台中，建立及使用工作流程。 一旦您了解 Api 的結構，您可以輕鬆地移植這些呼叫任何 REST 相容平台。

## <a name="prerequisites"></a>必要條件

- 登入或建立帳戶，在內容仲裁[審核工具](https://contentmoderator.cognitive.microsoft.com/)站台。

## <a name="create-a-workflow"></a>建立工作流程

若要建立或更新工作流程，請前往 **[工作流程-建立或更新](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59)** API 參考頁面，然後選取您的主要區域 按鈕 (您可以在端點 URL 上找到**認證**頁面的[審核工具](https://contentmoderator.cognitive.microsoft.com/))。 這會啟動 API 主控台中，您可以輕鬆地建構並執行 REST API 呼叫。

![[工作流程 - 建立或更新] 頁面的區域選取項目](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>輸入 REST 呼叫的參數

輸入的值**team**， **workflowname**，並**Ocp Apim-訂用帳戶金鑰**:

- **team**：當您設定時，您建立 「 小組識別碼您[審核工具](https://contentmoderator.cognitive.microsoft.com/)帳戶 (位於**識別碼**檢閱工具的 [認證] 畫面上的欄位)。
- **workflowname**：若要新增新的工作流程 （或現有的名稱，如果您想要更新現有的工作流程） 的名稱。
- **Ocp-Apim-Subscription-Key**：您的內容仲裁者金鑰。 您可以找到這**設定**索引標籤[審核工具](https://contentmoderator.cognitive.microsoft.com)。

![[工作流程 - 建立或更新] 主控台查詢參數和標頭](images/workflow-console-parameters.PNG)

### <a name="enter-a-workflow-definition"></a>輸入工作流程定義

1. 編輯**要求本文** 方塊中輸入詳細資料的 JSON 要求**描述**並**型別**(可能是`Image`或`Text`)。
2. 針對**運算式**，複製 預設的工作流程 JSON 運算式。 最終的 JSON 字串應該如下所示：

```json
{
  "Description":"<A description for the Workflow>",
  "Type":"Text",
  "Expression":{
    "Type":"Logic",
    "If":{
      "ConnectorName":"moderator",
      "OutputName":"isAdult",
      "Operator":"eq",
      "Value":"true",
      "Type":"Condition"
    },
    "Then":{
      "Perform":[
        {
          "Name":"createreview",
          "CallbackEndpoint":null,
          "Tags":[

          ]
        }
      ],
      "Type":"Actions"
    }
  }
}
```

> [!NOTE]
> 您可以為您的工作流程使用此 API 來定義簡單、 複雜且甚至是巢狀運算式。 [工作流程-建立或更新](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59)文件有更複雜的邏輯的範例。

### <a name="submit-your-request"></a>提交您的要求
  
選取 [傳送]  。 如果作業成功，[Response status] \(回應狀態\)  就會是 `200 OK`，而 [Response content] \(回應內容\)  方塊則會顯示 `true`。

### <a name="examine-the-new-workflow"></a>檢查新的工作流程

在 [審核工具](https://contentmoderator.cognitive.microsoft.com/)，選取**設定** > **工作流程**。 新的工作流程應該會出現在清單中。

![工作流程的審核工具清單](images/workflow-console-new-workflow.PNG)

選取 [**編輯**工作流程選項，然後前往**設計師**] 索引標籤。在這裡，您可以看到 JSON 邏輯的直覺化表示法。

![所選工作流程的 [設計工具] 索引標籤](images/workflow-console-new-workflow-designer.PNG)

## <a name="get-workflow-details"></a>取得工作流程詳細資料

若要擷取現有的工作流程有關的詳細資訊，請前往 **[工作流程-Get](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b44b3f9b0711b43c4c58)**  API 參考頁面，然後選取您的區域 按鈕 （取決於您的金鑰所在的區域）。

![[工作流程 - 取得] 的區域選取項目](images/test-drive-region.png)

輸入上述節所述的 REST 呼叫參數。 請確定這次**workflowname**是現有的工作流程的名稱。

![取得查詢參數和標頭](images/workflow-get-default.PNG)

選取 [傳送]  。 如果作業成功，**回應狀態**是`200 OK`，而**回應內容**方塊會顯示工作流程，以 JSON 格式，如下所示：

```json
{
  "Name":"default",
  "Description":"Default",
  "Type":"Image",
  "Expression":{
    "If":{
      "ConnectorName":"moderator",
      "OutputName":"isadult",
      "Operator":"eq",
      "Value":"true",
      "AlternateInput":null,
      "Type":"Condition"
    },
    "Then":{
      "Perform":[
        {
          "Name":"createreview",
          "Subteam":null,
          "CallbackEndpoint":null,
          "Tags":[

          ]
        }
      ],
      "Type":"Actions"
    },
    "Else":null,
    "Type":"Logic"
  }
}
```

## <a name="next-steps"></a>後續步驟

- 了解如何搭配[內容審核作業](try-review-api-job.md)使用工作流程。