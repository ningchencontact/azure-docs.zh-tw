---
title: 建立知識庫-QnA Maker
titleSuffix: Azure Cognitive Services
description: 使用 QnA Maker API 服務入口網站, 以使用閒聊來新增建立知識庫。 這可讓您的應用程式更吸引人。 將閒聊內容預先匯入知識庫中，作為聊天機器人閒聊起始點，節省您從頭開始編寫它們的時間和成本。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 05/10/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: dfd0663c09b78539a1d6b78aa7820385b9ddbdca
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563090"
---
# <a name="quickstart-create-a-knowledge-base-using-the-qna-maker-api-service-portal"></a>快速入門：使用 QnA Maker API 服務入口網站建立知識庫

QnA Maker API 服務入口網站可讓您在建立知識庫時, 輕鬆地加入現有的資料來源。 您可以從下列文件類型建立新的 QnA Maker 知識庫：

<!-- added for scanability -->
* 常見問題集頁面
* 產品手冊
* 結構化文件

加入閒聊特質，讓您的知識更貼近您的用戶。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。 

## <a name="create-a-new-knowledge-base"></a>建立新的知識庫

1. 使用您的 Azure 認證登入 [QnA Maker 入口網站](https://qnamaker.ai)，然後選取 [建立新的知識庫]  。

1. 如果您尚未建立 QnA Maker 服務，請選取 [建立 QnA 服務]  。 

1. 在 QnA Maker 入口網站中從**步驟 2** 的清單中，選取您的 Azure 租用戶、Azure 訂用帳戶名稱，以及與 QnA Maker 服務相關聯的 Azure 資源名稱。 選取將裝載知識庫的 Azure QnA Maker 服務。

    ![設定 QnA 服務](../media/qnamaker-how-to-create-kb/setup-qna-resource.png)

1. 輸入知識庫的名稱和新知識庫的資料來源。

    ![設定資料來源](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - 您的服務提供**名稱**。 重複的名稱和特殊字元均受支援。
    - 針對您想要擷取的資料新增 URL。 您可以在[這裡](../Concepts/data-sources-supported.md)查看相關資訊，以了解支援的來源類型。
    - 針對您想要擷取的資料上傳檔案。 請參閱[定價資訊](https://aka.ms/qnamaker-pricing)，以了解您可以新增多少文件。
    - 如果您想要手動新增 QnA，可以略過上圖中顯示的**步驟 4**。

1. 將**閒聊**新增至您的 KB。 選擇其中一個特質, 為您的 bot 新增閒聊的交談支援。 

    ![將閒聊新增至知識庫](../media/qnamaker-how-to-create-kb/create-kb-chit-chat.png)

1. 選取 [建立您的 KB]  。

    ![建立知識庫](../media/qnamaker-how-to-create-kb/create-kb.png)

1. 擷取資料需要幾分鐘的時間。

    ![擷取](../media/qnamaker-how-to-create-kb/hang-tight-extraction.png)

1. 成功建立知識庫後，系統會將您重新導向至 [知識庫]  頁面。

## <a name="clean-up-resources"></a>清除資源

當知識庫完成時，則會從 QnA Maker 入口網站中移除。

## <a name="next-steps"></a>後續步驟

針對成本節約量值, 您可以[共用](upgrade-qnamaker-service.md?#share-existing-services-with-qna-maker)部分 (而非所有) 為 QnA Maker 建立的 Azure 資源。

> [!div class="nextstepaction"]
> [新增閒聊特質](./chit-chat-knowledge-base.md)
