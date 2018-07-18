---
title: 如何發佈知識庫 - Microsoft 認知服務 | Microsoft Docs
titleSuffix: Azure
description: 如何發佈知識庫
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: e9dbeacfb0df98c6b8f084c263690c05fe966cdc
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2018
ms.locfileid: "35369558"
---
# <a name="publish-a-knowledge-base"></a>發佈知識庫

發佈您的知識庫是讓知識庫成為問題回答端點的最後一個步驟。 

您發佈知識庫時，知識庫的 QnA 內容會從測試索引移到 Azure 搜尋服務中的生產索引。

![發佈生產測試索引](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

## <a name="publish-a-knowledge-base"></a>發佈知識庫

1. 變更知識庫之後，請選取上方導覽列中的 [發佈]。 您可以發佈知識庫直到達到 Azure 搜尋服務的分配數目為止。 

    ![發佈知識庫](../media/qnamaker-how-to-publish-kb/publish.png)

2. 再次選取 [發佈]，查看應用程式或聊天機器人中可用的端點詳細資料。

    ![發佈知識庫](../media/qnamaker-how-to-publish-kb/publish-success.png)
    
## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [取得知識庫的分析](./get-analytics-knowledge-base.md)
