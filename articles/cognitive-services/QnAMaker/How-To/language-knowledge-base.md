---
title: 非英文知識庫 - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker 支援多種語言的知識庫內容。 不過，個別的 QnA Maker 服務均應保留給單一語言使用。 針對特定 QnA Maker 服務而建立的第一個知識庫，會設定該服務的語言。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: diberry
ms.openlocfilehash: 5e50c814fef24aa799549d055ad6496f5bdf05e0
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/12/2019
ms.locfileid: "70961489"
---
# <a name="language-support-of-knowledge-base-content-for-qna-maker"></a>QnA Maker 知識庫內容的語言支援

QnA Maker 支援多種語言的知識庫內容。 不過，個別的 QnA Maker 服務均應保留給單一語言使用。 針對特定 QnA Maker 服務而建立的第一個知識庫，會設定該服務的語言。 請參閱[這裡](../Overview/languages-supported.md)以取得支援的語言清單。

系統會從要擷取的資料來源內容自動辨識語言。 在您建立新的 QnA Maker 服務和該服務中新的知識庫之後，您可以確認語言已正確設定。

1. 瀏覽至 [Azure 入口網站](https://portal.azure.com/)。

1. 選取 [資源群組]，並瀏覽至部署 QnA Maker 服務的資源群組，然後選取 [Azure 搜尋服務] 資源。

    ![選取 [Azure 搜尋服務] 資源](../media/qnamaker-how-to-language-kb/select-azsearch.png)

1. 選取 [**索引**]，然後選取 [ **testkb** ] 索引。 這是第一個建立的 Azure 搜尋服務索引，其中包含該服務中所有知識庫的已儲存內容。 

1. 選取 [**欄位**] 以查看索引中的欄位。

1. `questions` 和`answer`欄位的 [分析器] 資料行設定為特定語言。 在從匯入的檔案和 Url 進行知識庫建立步驟期間，會自動偵測到此語言。 此語言在資源建立後即無法變更。

    ![已選取的分析器](../media/qnamaker-how-to-language-kb/selected-analyzer.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用 Azure Bot 服務建立 QnA Bot](../Tutorials/create-qna-bot.md)
