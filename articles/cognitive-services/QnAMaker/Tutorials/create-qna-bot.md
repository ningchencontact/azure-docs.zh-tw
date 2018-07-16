---
title: 使用 Azure Bot Service 建立 QnA Bot - Azure 認知服務 | Microsoft Docs
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: f2721dfa9a0922ee4a5af1eb66fd4506feb94d28
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/01/2018
ms.locfileid: "35370867"
---
# <a name="create-a-qna-bot-with-azure-bot-service"></a>使用 Azure Bot Service 建立 QnA Bot
本教學課程會逐步引導您在 Azure 入口網站上使用 Azure Bot Service 建置 QnA Bot。

## <a name="prerequisite"></a>必要條件
建立之前，請依照[建立知識庫]()中的步驟建立含有問題和解答的 QnA Maker 服務。

Bot 會透過 QnAMakerDialog，從您建立的知識庫回應問題。

## <a name="create-a-qna-bot"></a>建立 QnA Bot
1. 在 [Azure 入口網站](https://portal.azure.com)中，選取功能表刀鋒視窗中的 [建立] 新資源，然後選取 [全部查看]。

    ![Bot 服務建立](../media/qnamaker-tutorials-create-bot/bot-service-creation.png)

2. 在搜尋方塊中，搜尋 **Web App Bot**。

    ![Bot 服務選取](../media/qnamaker-tutorials-create-bot/bot-service-selection.png)

3. 在 [Bot 服務] 刀鋒視窗中提供必要資訊，然後選取 [建立]。 這會使用 QnAMakerDialog 建立 Bot 服務，並將它部署到 Azure。

    - 將 [應用程式名稱] 設定為您的 Bot 名稱。 當您的 Bot 部署到雲端 (例如，mynotesbot.azurewebsites.net) 時，此名稱會當作子網域使用。
    - 選取訂用帳戶、資源群組和 App Service 方案和位置。
    - 選取 Bot 範本欄位的 [問答] (Node.js 或 C#) 範本。
    - 選取法律聲明的確認核取方塊。 法律聲明的條款在此核取方塊下方。

        ![Bot 服務選取](../media/qnamaker-tutorials-create-bot/bot-service-qna-template.PNG)

4. 確認已部署 Bot 服務。

    - 選取 [通知] (位於 Azure 入口網站頂邊的鈴鐺圖示)。 通知會 [部署開始] 變更為 [部署成功]。
    - 在通知變更為 [部署成功] 之後，選取該通知上的 [移至資源]。

## <a name="chat-with-the-bot"></a>與 Bot 聊天
選取 [前往資源] 會帶您前往 Bot 的資源刀鋒視窗。

註冊 Bot 後，按一下 [在網路聊天中測試] 以開啟 [網路聊天] 窗格。 在網路聊天中輸入「您好」。

![QnA Bot 網路聊天](../media/qnamaker-tutorials-create-bot/qna-bot-web-chat.PNG)

Bot 會以「請在應用程式設定中設定 QnAKnowledgebaseId 和 QnASubscriptionKey」回應。 了解如何在 https://aka.ms/qnaabssetup 取得。 此回應可確認 QnA Bot 已收到訊息，但還沒有與其相關聯的 QnA Maker 知識庫。 在下一個步驟中執行該作業。

## <a name="connect-your-qna-maker-knowledge-base-to-the-bot"></a>將 QnA Maker 知識庫連線至 Bot

1. 開啟 [應用程式設定]，然後編輯 [QnAKnowledgebaseId]、[QnAAuthKey] 和 [QnAEndpointHostName] 欄位以包含 QnA Maker 知識庫的值。

    ![app settings](../media/qnamaker-tutorials-create-bot/application-settings.PNG)

2. 在 https://qnamaker.ai 中從知識庫的 [設定] 索引標籤取得您的知識庫識別碼、主機 url 和端點金鑰。
    - 登入 [QnA Maker](https://qnamaker.ai)
    - 移至您的知識庫
    - 按一下 [設定] 索引標籤
    - [發佈] 您的知識庫 (如果尚未這麼做)

    ![QnA Maker 值](../media/qnamaker-tutorials-create-bot/qnamaker-settings-kbid-key.PNG)

> [!NOTE]
> 如果您想要連接知識庫的預覽版本與 QnA Bot，請將 [Ocp-Apim-Subscription-Key] 的值設定為 [QnAAuthKey]。 將 **QnAEndpointHostName** 保留空白。

## <a name="test-the-bot"></a>測試 Bot
在 Azure 入口網站中，按一下 [在網路聊天中測試] 以測試 Bot。 

![QnA Maker Bot](../media/qnamaker-tutorials-create-bot/qna-bot-web-chat-response.PNG)

QnA Bot 現在會從您的知識庫進行解答。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [整合 QnA Maker 與 LUIS](./integrate-qnamaker-luis.md)

## <a name="see-also"></a>另請參閱

- [管理您的知識庫](https://qnamaker.ai)
- [在不同的頻道啟用您的 Bot](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels)
