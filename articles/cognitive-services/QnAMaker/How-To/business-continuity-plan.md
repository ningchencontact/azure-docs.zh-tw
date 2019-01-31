---
title: 商務持續性方案 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 商務持續性方案的主要目標是建立彈性的知識庫端點，藉以確保聊天機器人或應用程式使用知識庫時不會停機。
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/14/2019
ms.author: tulasim
ms.openlocfilehash: 66d2b4667adaac6fa5d6bba2a5a78af3a0c2c255
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2019
ms.locfileid: "55221108"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>建立 QnA Maker 服務的商務持續性方案

商務持續性方案的主要目標是建立彈性的知識庫端點，藉以確保聊天機器人或應用程式使用知識庫時不會停機。

![QnA Maker bcp 方案](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

以上呈現的高階構想如下所示：

1. 在 [Azure 配對區域](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)中設定兩個平行的 [QnA Maker 服務](../How-To/set-up-qnamaker-service-azure.md)。

2. 使主要和次要的 Azure 搜尋索引保持同步。使用[這裡](https://github.com/pchoudhari/QnAMakerBackupRestore)的 GitHub 範例以了解如何備份還原 Azure 索引。

3. 使用[連續匯出](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry)備份 Application Insights。

4. 設定主要和次要堆疊後，使用[流量管理員](https://docs.microsoft.com/azure/traffic-manager/)設定兩個端點，並設定路由方法。

5. 您必須建立流量管理員端點的 SSL 憑證。 在您的應用程式服務中[繫結 SSL 憑證](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl)。

6. 最後，在聊天機器人或應用程式中使用流量管理員端點。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [選擇 QnA Maker 部署的容量](../Tutorials/choosing-capacity-qnamaker-deployment.md)
