---
title: QnA Bot - Azure Bot 服務 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 本教學課程會逐步引導您在 Azure 入口網站上使用 Azure Bot 服務 v3 建置 QnA Bot。
services: cognitive-services
author: tulasim88
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 04/02/2019
ms.author: tulasim
ms.openlocfilehash: 218103f2c75ec1016a997c259767ccd011191fab
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2019
ms.locfileid: "58879603"
---
# <a name="tutorial-create-a-qna-bot-with-azure-bot-service-v3"></a>教學課程：使用 Azure Bot 服務 v3 建立 QnA Bot

本教學課程會逐步引導您在 [Azure 入口網站](https://portal.azure.com)中使用 Azure Bot 服務 v3 來建置 QnA Bot，而不必撰寫任何程式碼。 將已發佈的知識庫 (KB) 連線至 Bot 的作業，和變更 Bot 應用程式設定的作業一樣簡單。 

> [!Note] 
> 本主題適用於 Bot SDK 第 3 版。 您可以在[這裡](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs)找到第 4 版。 

**在本教學課程中，您了解如何：**

<!-- green checkmark -->
> [!div class="checklist"]
> * 使用 QnA Maker 範本來建立 Azure Bot 服務
> * 與 Bot 聊天以確認程式碼可行 
> * 將已發佈的 KB 連線至 Bot
> * 使用問題來測試 Bot

在本文中，您可以使用免費的 QnA Maker [服務](../how-to/set-up-qnamaker-service-azure.md)。

## <a name="prerequisites"></a>必要條件

您需要有已發佈的知識庫以供本教學課程使用。 如果沒有，請遵循[建立知識庫](../How-To/create-knowledge-base.md)中的步驟，來建立含有問題和解答的 QnA Maker 服務。

## <a name="create-a-qna-bot"></a>建立 QnA Bot

1. 在 Azure 入口網站中，選取 [建立資源]。

    ![Bot 服務建立](../media/qnamaker-tutorials-create-bot/bot-service-creation.png)

2. 在搜尋方塊中，搜尋 **Web App Bot**。

    ![Bot 服務選取](../media/qnamaker-tutorials-create-bot/bot-service-selection.png)

3. 在 [Bot 服務] 中提供必要資訊：

    - 將 [應用程式名稱] 設定為您 Bot 的名稱。 當您的 Bot 部署到雲端 (例如，mynotesbot.azurewebsites.net) 時，此名稱會當作子網域使用。
    - 選取訂用帳戶、資源群組和 App Service 方案和位置。

4. 若要使用 v3 範本，請選取 **SDK v3** 的 SDK 版本以及 **C#** 或 **Node.js** 的 SDK 語言。

    ![bot sdk 設定](../media/qnamaker-tutorials-create-bot/bot-v3.png)

5. 選取 Bot 範本欄位的 [問答] 範本，然後選取 [選取] 以儲存範本設定。

    ![儲存 Bot 服務範本選取項目](../media/qnamaker-tutorials-create-bot/bot-v3-template.png)

6. 檢閱您的設定，然後選取 [建立]。 這會建立 Bot 服務，並將其部署到 Azure。

    ![建立 Bot](../media/qnamaker-tutorials-create-bot/bot-blade-settings-v3.png)

7. 確認已部署 Bot 服務。

    - 選取 [通知] (位於 Azure 入口網站頂邊的鈴鐺圖示)。 通知會 [部署開始] 變更為 [部署成功]。
    - 在通知變更為 [部署成功] 之後，選取該通知上的 [移至資源]。

## <a name="chat-with-the-bot"></a>與 Bot 聊天

選取 [前往資源] 會帶您前往 Bot 的資源。

選取 [在網路聊天中測試] 來開啟 [網路聊天] 窗格。 在網路聊天中輸入「hi」。

![QnA Bot 網路聊天](../media/qnamaker-tutorials-create-bot/qna-bot-web-chat.PNG)

Bot 會以「請在應用程式設定中設定 QnAKnowledgebaseId 和 QnASubscriptionKey」回應。 此回應可確認 QnA Bot 已收到訊息，但還沒有與其相關聯的 QnA Maker 知識庫。 

## <a name="connect-your-qna-maker-knowledge-base-to-the-bot"></a>將 QnA Maker 知識庫連線至 Bot

1. 開啟 [應用程式設定]，然後編輯 [QnAKnowledgebaseId]、[QnAAuthKey] 和 [QnAEndpointHostName] 欄位以包含 QnA Maker 知識庫的值。

    ![app settings](../media/qnamaker-tutorials-create-bot/application-settings.PNG)

1. 在 QnA Maker 入口網站中，從知識庫的 [設定] 索引標籤取得知識庫識別碼、主機 url 和端點金鑰。

   - 登入 [QnA Maker](https://qnamaker.ai)
   - 移至您的知識庫
   - 選取 [設定] 索引標籤
   - [發佈] 您的知識庫 (如果尚未這麼做)

     ![QnA Maker 值](../media/qnamaker-tutorials-create-bot/qnamaker-settings-kbid-key.PNG)

## <a name="test-the-bot"></a>測試 Bot

在 Azure 入口網站中，選取 [Test in Web Chat]\(在網路聊天中測試\) 以測試 Bot。 

![QnA Maker Bot](../media/qnamaker-tutorials-create-bot/qna-bot-web-chat-response.PNG)

QnA Bot 會從知識庫找出解答。

## <a name="related-to-qna-maker-bots"></a>QnA Maker bot 相關

* QnA Maker 的說明 bot，QnA Maker 入口網站中使用可從[bot 範例](https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/csharp_dotnetcore/qnamaker-support-bot)。
    ![QnA Maker 的說明 bot 圖示為紅色的機器人](../media/qnamaker-tutorials-create-bot/answer-bot-icon.PNG)
* [醫療保健的機器人](https://docs.microsoft.com/HealthBot/qna_model_howto)做為其中一個使用 QnA Maker 他們[語言模型](https://docs.microsoft.com/HealthBot/qna_model_howto)。

## <a name="clean-up-resources"></a>清除資源

當您完成本教學課程的 Bot 時，請在 Azure 入口網站中移除該 Bot。 Bot 服務包括：

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
