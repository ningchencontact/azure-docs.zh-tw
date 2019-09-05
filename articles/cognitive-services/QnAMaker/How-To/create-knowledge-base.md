---
title: 建立知識庫-QnA Maker
titleSuffix: Azure Cognitive Services
description: 使用 QnA Maker API 服務入口網站，以使用閒聊來新增建立知識庫。 這可讓您的應用程式更吸引人。 將閒聊內容預先匯入知識庫中，作為聊天機器人閒聊起始點，節省您從頭開始編寫它們的時間和成本。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 322a1d25ed434d8be674288b1b13d6ecf961590b
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/30/2019
ms.locfileid: "70193575"
---
# <a name="quickstart-create-a-knowledge-base-using-the-qna-maker-api-service-portal"></a>快速入門：使用 QnA Maker API 服務入口網站建立知識庫

QnA Maker API 服務入口網站可讓您在建立知識庫時，輕鬆地加入現有的資料來源。 您可以從下列文件類型建立新的 QnA Maker 知識庫：

<!-- added for scanability -->
* 常見問題集頁面
* 產品手冊
* 結構化文件

加入閒聊特質，讓您的知識更貼近您的用戶。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。 

## <a name="create-a-new-knowledge-base"></a>建立新的知識庫

1. 使用您的 Azure 認證登入 [QnA Maker 入口網站](https://qnamaker.ai)，然後選取 [建立新的知識庫]。

1. 如果您尚未建立 QnA Maker 服務，請選取 [建立 QnA 服務]。 

1. 在 QnA Maker 入口網站中從**步驟 2** 的清單中，選取您的 Azure 租用戶、Azure 訂用帳戶名稱，以及與 QnA Maker 服務相關聯的 Azure 資源名稱。 選取將裝載知識庫的 Azure QnA Maker 服務。

    ![設定 QnA 服務](../media/qnamaker-how-to-create-kb/setup-qna-resource.png)

1. 輸入知識庫的名稱和新知識庫的資料來源。

    ![設定資料來源](../media/qnamaker-how-to-create-kb/set-data-sources.png)

1. 提供服務的**名稱**，例如`my first kb`。 重複的名稱和特殊字元均受支援。

1. 將 [QnA Maker 疑難排解] 頁面新增為 URL `https://docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/troubleshooting`：，然後`+ Add URL`選取 []。 您可以在[這裡](../Concepts/data-sources-supported.md)查看相關資訊，以了解支援的來源類型。 在本快速入門中，**請勿上傳**您想要解壓縮的資料檔案。 請參閱[定價資訊](https://aka.ms/qnamaker-pricing)，以了解您可以新增多少文件。

1. 將 **_專業_閒聊**新增至您的知識庫。 

1. 選取 [建立您的 KB]。

    ![建立知識庫](../media/qnamaker-how-to-create-kb/create-kb.png)

1. 可能需要幾分鐘的時間來解壓縮資料。

    ![擷取](../media/qnamaker-how-to-create-kb/hang-tight-extraction.png)

1. 成功建立知識庫時，系統會將您重新導向至 [**知識庫**] 頁面。

## <a name="clean-up-resources"></a>清除資源

當知識庫完成時，則會從 QnA Maker 入口網站中移除。

## <a name="next-steps"></a>後續步驟

針對成本節約量值，您可以[共用](set-up-qnamaker-service-azure.md#share-existing-services-with-qna-maker)部分（而非所有）為 QnA Maker 建立的 Azure 資源。

> [!div class="nextstepaction"]
> [使用中繼資料新增問題](../quickstarts/add-question-metadata-portal.md)
