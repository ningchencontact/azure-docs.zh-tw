---
title: 遷移知識庫 - Qna Maker
titleSuffix: Azure Cognitive Services
description: 遷移知識庫必須先從某個知識庫匯出，再匯入至另一個知識庫。
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 04/08/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 8ff3c497372a761bd8a02ae81bc897c8ee297bd0
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2019
ms.locfileid: "65794874"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>使用匯出-匯入移轉知識庫

遷移知識庫必須先從某個知識庫匯出，再匯入至另一個知識庫。 

## <a name="prerequisites"></a>必要條件

* 開始之前，請建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 設定新的 [QnA Maker 服務](../How-To/set-up-qnamaker-service-azure.md)

## <a name="migrate-a-knowledge-base-from-qna-maker"></a>從 QnA Maker 遷移知識庫
1. 登入 [QnA Maker 入口網站](https://qnamaker.ai)。
1. 選取您想要遷移的知識庫。

1. 在 [設定] 頁面上，選取 [匯出知識庫] 以下載 .tsv 檔案，其中包含您的知識庫內容 - 問題、解答、中繼資料，以及從中進行擷取的資料來源名稱。

1. 從頂端功能表選取 [建立知識庫]，然後建立空白的知識庫。 

    ![設定資料來源](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - 您的服務提供**名稱**。 重複的名稱和特殊字元均受支援。

1. 選取 [建立] 。

    ![建立知識庫](../media/qnamaker-how-to-create-kb/create-kb.png)

1. 在這個新知識庫中，開啟 [設定] 索引標籤，然後選取 [匯入知識庫]。 這會匯入問題、解答及中繼資料，而且會保留從中進行擷取的資料來源名稱。

   ![匯入知識庫](../media/qnamaker-how-to-migrate-kb/Import.png)

1. 使用 [測試] 面板來 [測試] 新知識庫。 了解如何[測試知識庫](../How-To/test-knowledge-base.md)。
1. [發佈] 知識庫。 了解如何[發佈知識庫](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base)。
1. 在應用程式或 Bot 程式碼中使用此端點。 請參閱如何[建立 QnA Bot](../Tutorials/create-qna-bot.md)。

    ![QnA Maker 值](../media/qnamaker-how-to-migrate-kb/qnamaker-settings-kbid-key.png)

    此時，所有知識庫內容 (問題、解答和中繼資料，以及來源檔案名稱和 URL) 都會匯入新的知識庫。 

## <a name="chat-logs-and-alterations"></a>聊天記錄和變動
不會自動匯入不區分大小寫的變異形式 (同義字)。 使用[V4 Api](https://go.microsoft.com/fwlink/?linkid=2092179)移動新的知識庫中的變更。

沒有任何方法可移轉聊天記錄，因為新的知識庫會使用 Application Insights 來儲存聊天記錄。 

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [編輯知識庫](../How-To/edit-knowledge-base.md)
