---
title: 在 Azure Content Moderator 中管理認證 - Content Moderator
titlesuffix: Azure Cognitive Services
description: 管理您使用 API 時所需的 Content Moderator 認證。
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 3d183870938f44cd71f1dab4105eec390ba12d94
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2019
ms.locfileid: "54266140"
---
# <a name="manage-content-moderator-service-credentials"></a>管理 Content Moderator 服務認證

您的 Content Moderator 認證會建立於下列位置：

- [Azure 入口網站](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator)
- [Content Moderator 審查工具](https://contentmoderator.cognitive.microsoft.com/)

本文說明何處尋找這些認證，以及彼此間的關係。

## <a name="the-azure-portal"></a>Azure 入口網站

在 Azure 入口網站儀表板上，選取您的 Content Moderator 帳戶。 在 [資源管理] 之下，選取 [金鑰]。 若要複製金鑰，請選取金鑰右邊的圖示。

![Azure 入口網站中的 Content Moderator 金鑰](images/credentials-azure-portal-keys.PNG)

### <a name="use-the-azure-account-with-the-review-tool-and-review-api"></a>使用 Azure 帳戶搭配審查工具和審查 API
若要使用您的 Azure 金鑰搭配審查 API，請複製下列螢幕擷取畫面中列在 [屬性] 畫面上的 [資源識別碼]，並將它輸入於審查工具認證畫面的 [列入允許清單的資源識別碼] 欄位中，如以下**資源識別碼**一節所示。 

> [!NOTE]
> Content Moderator 訂用帳戶的區域應符合審核小組的區域，以便辨識您的小組及存取小組資料。 例如，在此頁面的影像中，[美國西部] 區域 **(4)** 包含 Content Moderator Azure 訂用帳戶和您的審核小組。
>
> 使用您 Azure 訂用帳戶中的金鑰和資源識別碼取代審查工具中的兩個地點後，便不再使用 [認證] 畫面上顯示的 **Trial Ocp-Apim-Subscription-Key** (但仍可使用)。
> 試用金鑰會限制您每個月最多有 5,000 筆交易，且每秒 1 個要求 (RPS)。

![Azure 入口網站中的 Content Moderator 資源識別碼](images/credentials-azure-portal-resourceid.PNG)

### <a name="use-the-azure-account-with-the-workflows-in-the-review-tool"></a>使用 Azure 帳戶搭配審查工具中的工作流程

若要將您的 Azure 金鑰使用於 Content Moderator 內可用的工作流程，請將它輸入於 [工作流程設定] 區段的 [Ocp-Apim-Subscription-Key] 欄位中，如以下**工作流程**一節所示。 點擊 **'+'** 以儲存您的資源識別碼。

![審查工具中的 Content Moderator 工作流程認證](images/credentials-workflow.PNG)

## <a name="the-review-tool"></a>審查工具

在審查工具儀表板的 [設定] 索引標籤上，選取 [認證]。

![審查工具中的 Content Moderator 認證](images/credentials-trial-resource-workflow.PNG)

下一節會更詳細地檢查上述影像：

### <a name="api"></a>API

第一個部分列出您的**審查 API 端點**、**小組識別碼** 和 **Ocp-Apim-Subscription-Key (Content Moderator 試用金鑰)** (在建立審核小組的過程中產生)。 使用它們來呼叫所有 Content Moderator API，包括審核 API。

此外，記下您 API 端點的區域識別碼。 例如 **uswest** 是 "https://westus.api.cognitive.microsoft.com/contentmoderator/review/v1.0" 中的區域

![審查工具中的 Content Moderator 金鑰](images/credentials-trialkey.PNG)

### <a name="resource-id"></a>資源識別碼

上述的[使用 Azure 帳戶搭配檢閱工具與 API](credentials.md#use-the-azure-account-with-the-review-tool-and-review-api)一節中涵蓋這組欄位。 此欄位通常空白，除非您如上一節所述，將您的 Azure 資源識別碼新增到此欄位。

### <a name="workflows"></a>工作流程

上述的[使用 Azure 帳戶搭配檢閱工具中的工作流程](credentials.md#use-the-azure-account-with-the-workflows-in-the-review-tool)一節中涵蓋這組欄位。 根據預設，審查工具會使用其自動產生的試用金鑰來執行工作流程，而這就是一開始顯示的內容。 其他兩個欄位允許在 [畫面文字] 和 [評估影像] 作業中分別使用字詞和影像清單。

## <a name="next-steps"></a>後續步驟

* 了解如何在[工作流程](workflows.md)中使用 Content Moderator 認證。
