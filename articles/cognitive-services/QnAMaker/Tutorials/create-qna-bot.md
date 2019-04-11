---
title: QnA Bot - Azure Bot 服務 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 建立從現有知識庫為基礎的發行頁面的 QnA 聊天機器人。 此 bot 將會使用 Bot Framework SDK v4。 您不需要撰寫任何程式碼來建置機器人，為您提供的所有程式碼。
services: cognitive-services
author: tulasim88
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 04/08/2019
ms.author: tulasim
ms.openlocfilehash: 85b0004288a06a834b61f6e3d50017d35d66ce86
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2019
ms.locfileid: "59263871"
---
# <a name="tutorial-create-a-qna-bot-with-azure-bot-service-v4"></a>教學課程：建立與 Azure 的 QnA Bot Bot 服務 v4

建立從 QnA 聊天機器人**發佈**現有知識庫為基礎的頁面。 此 bot 將會使用 Bot Framework SDK v4。 您不需要撰寫任何程式碼來建置機器人，為您提供的所有程式碼。

**在本教學課程中，您了解如何：**

<!-- green checkmark -->
> [!div class="checklist"]
> * 從現有的知識庫中建立的 Azure Bot Service
> * 與 Bot 聊天以確認程式碼可行 

## <a name="prerequisites"></a>必要條件

您需要有已發佈的知識庫以供本教學課程使用。 如果您沒有，請依照下列中的步驟[建立並回應從 KB](create-publish-query-in-portal.md)教學課程，以使用問與答建立 QnA Maker 的知識庫。

<a name="create-a-knowledge-base-bot"></a>

## <a name="create-a-qna-bot"></a>建立 QnA Bot

建立知識庫的用戶端應用程式 bot。 

1. 在 QnA Maker 入口網站中，移至**發佈**頁面，然後發佈您的知識庫。 選取 **建立 Bot**。 

    ![QnA Maker 入口網站中，移至 [發行] 頁面中，並發佈您的知識庫。 選取 建立 Bot。](../media/qnamaker-tutorials-create-bot/create-bot-from-published-knowledge-base-page.png)

    Azure 入口網站會開啟與 bot 建立組態。

1.  輸入要建立 bot 的設定：

    |設定|值|目的|
    |--|--|--|
    |Bot 名稱|`my-tutorial-kb-bot`|這是機器人的 Azure 資源名稱。|
    |訂用帳戶|請參閱用途。|選取相同的訂用帳戶與您用來建立 QnA Maker 資源。|
    |資源群組|`my-tutorial-rg`|所有的 bot 相關 Azure 資源使用資源群組。|
    |位置|`west us`|機器人的 Azure 資源的位置。|
    |定價層|`F0`|Azure bot 服務免費層。|
    |應用程式名稱|`my-tutorial-kb-bot-app`|這是以支援您的機器人，只有 web 應用程式。 因為已使用 QnA Maker 服務，這不應該是相同的應用程式名稱。 不支援與任何其他資源共用 QnA Maker 的 web 應用程式。|
    |SDK 語言|C#|這是使用 bot framework SDK 的基礎程式設計語言。 您可以選擇C#或 Node.js。|
    |QnA 驗證金鑰|**不要變更**|這個值會為您填入。|
    |App Service 方案/位置|**不要變更**|本教學課程中，位置並不重要。|
    |Azure 儲存體|**不要變更**|對話資料會儲存在 Azure 儲存體資料表。|
    |Application Insights|**不要變更**|記錄會傳送至 Application Insights。|
    |Microsoft 應用程式識別碼|**不要變更**|Active directory 使用者和密碼是必要項目。|

    ![使用這些設定建立知識庫 bot。](../media/qnamaker-tutorials-create-bot/create-bot-from-published-knowledge-base.png)

    等待幾分鐘，直到 bot 建立處理程序通知會報告成功。

<a name="test-the-bot"></a>

## <a name="chat-with-the-bot"></a>與 Bot 聊天

1. 在 Azure 入口網站中，開啟新的 bot 資源從通知。 

    ![在 Azure 入口網站中，開啟新的 bot 資源從通知。](../media/qnamaker-tutorials-create-bot/azure-portal-notifications.png)

1. 從**Bot 管理**，選取**測試中 Web 對談**，然後輸入： `How large can my KB be?`。 Bot 會回應： 


    `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.`


    ![測試新的知識庫 bot。](../media/qnamaker-tutorial-create-publish-query-in-portal/test-bot-in-web-chat-in-azure-portal.png)

    如需有關 Azure 上的機器人的詳細資訊，請參閱[使用 QnA Maker 回答問題](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs)

## <a name="related-to-qna-maker-bots"></a>QnA Maker bot 相關

* QnA Maker 的說明 bot，QnA Maker 入口網站中使用可從[bot 範例](https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/csharp_dotnetcore/qnamaker-support-bot)。
    ![QnA Maker 的說明 bot 圖示為紅色的機器人](../media/qnamaker-tutorials-create-bot/answer-bot-icon.PNG)
* [醫療保健的機器人](https://docs.microsoft.com/HealthBot/qna_model_howto)做為其中一個使用 QnA Maker 他們[語言模型](https://docs.microsoft.com/HealthBot/qna_model_howto)。

## <a name="clean-up-resources"></a>清除資源

當您完成本教學課程的 Bot 時，請在 Azure 入口網站中移除該 Bot。 

如果您建立新的資源群組，機器人的資源，刪除資源群組。 

如果您未建立新的資源群組，您需要尋找 bot 相關聯的資源。 最簡單的方法是搜尋的 bot 和 bot 應用程式的名稱。 Bot 資源包括：

* App Service 方案
* 搜尋服務
* 認知服務
* 應用程式服務
* 或者，亦可包括 Application Insights 服務和 Application Insights 資料的儲存體

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [概念： 知識庫](../concepts/knowledge-base.md)

## <a name="see-also"></a>請參閱

- [管理您的知識庫](https://qnamaker.ai)
- [啟用您的 bot 在不同的管道](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels)
