---
title: 疑難排解 - QnAMaker
titleSuffix: Azure Cognitive Services
description: QnAMaker 由使用者 Azure 帳戶中裝載的元件所組成。 在進行偵錯時，使用者可能需要操作其 QnAMaker Azure 資源，或將關於其設定的其他資訊提供給 QnAMaker 支援小組。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 08/20/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b58a08c4cfa97356be4064456b2e9ec5cceccfbc
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876330"
---
# <a name="troubleshooting-tips-to-support-the-qna-maker-service-and-runtime"></a>支援 QnA Maker 服務和執行階段的疑難排解秘訣

QnAMaker 是由裝載于使用者 Azure 訂用帳戶中的資源所組成。 可能需要使用者管理其 Azure QnAMaker 資源, 或為 QnAMaker 支援小組提供有關其設定的其他資訊。

## <a name="how-to-get-latest-qnamaker-runtime-updates"></a>如何取得最新的 QnAMaker 執行階段更新

QnAMaker 執行時間是您在 Azure 入口網站中[建立 QnAMaker 服務](./set-up-qnamaker-service-azure.md)時所部署 Azure App Service 的一部分。 執行階段會定期進行更新。 QnA Maker App Service 在2019版網站延伸模組版本 (版本 5 +) 之後, 處於自動更新模式。 這已經過設計, 可在升級期間處理零停機時間。 

您可以在上 https://www.qnamaker.ai/UserSettings 檢查目前的版本。 如果您的版本早于版本 5.x, 您必須重新開機 App Service, 才能套用最新的更新。

1. 在 [Azure 入口網站](https://portal.azure.com)中移至 QnAMaker 服務 (資源群組)

    ![QnAMaker Azure 資源群組](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. 按一下 [App Service]，然後開啟 [概觀] 區段

     ![QnAMaker App Service](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. 重新啟動 App Service。 此作業應會在數秒內完成。 請注意, 在此重新開機期間, 使用者將無法使用此 QnAMaker 服務的任何相依應用程式或 bot。

    ![QnAMaker AppService 重新啟動](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="how-to-get-the-qnamaker-service-hostname"></a>如何取得 QnAMaker 服務主機名稱
當您連絡 QnAMaker 支援人員或 UserVoice 時，QnAMaker 服務主機名稱將有助於偵錯的用途。 主機名稱是下列格式的 URL: HTTPs:// *{hostname}* . azurewebsites.net。
    
1. 在 [Azure 入口網站](https://portal.azure.com)中移至 QnAMaker 服務 (資源群組)

    ![Azure 入口網站中的 QnAMaker Azure 資源群組](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. 選取與 QnA Maker 資源相關聯的 App Service。 一般來說, 名稱是相同的。

     ![選取 QnAMaker App Service](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. 主機名稱 URL 會顯示於 [概觀] 區段中

    ![QnAMaker 主機名稱](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)
    

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用主動式學習來改善知識庫問題](./improve-knowledge-base.md)
