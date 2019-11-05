---
title: 商務持續性方案 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 商務持續性方案的主要目標是建立彈性的知識庫端點，藉以確保聊天機器人或應用程式使用知識庫時不會停機。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: diberry
ms.openlocfilehash: 0e748e81de39b2bef14b543063adeb51b8b3ecdb
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73486721"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>建立 QnA Maker 服務的商務持續性方案

商務持續性方案的主要目標是建立彈性的知識庫端點，藉以確保聊天機器人或應用程式使用知識庫時不會停機。

![QnA Maker bcp 方案](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

以上呈現的高階構想如下所示：

1. 在 [Azure 配對區域](../How-To/set-up-qnamaker-service-azure.md)中設定兩個平行的 [QnA Maker 服務](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)。

2. 保持主要和次要 Azure 搜尋索引的同步。使用[這裡](https://github.com/pchoudhari/QnAMakerBackupRestore)的 GitHub 範例來瞭解如何備份-還原 Azure 索引。

3. 使用[連續匯出](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry)備份 Application Insights。

4. 設定主要和次要堆疊後，使用[流量管理員](https://docs.microsoft.com/azure/traffic-manager/)設定兩個端點，並設定路由方法。

5. 您必須建立流量管理員端點的 SSL 憑證。 在您的應用程式服務中[繫結 SSL 憑證](https://docs.microsoft.com/azure/app-service/configure-ssl-bindings)。

6. 最後，在聊天機器人或應用程式中使用流量管理員端點。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [選擇 QnA Maker 部署的容量](../Tutorials/choosing-capacity-qnamaker-deployment.md)
