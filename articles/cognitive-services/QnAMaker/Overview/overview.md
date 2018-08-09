---
title: 了解 QnA Maker - Microsoft 認知服務 | Microsoft Docs
titleSuffix: Azure
description: QnA Maker 可讓您加強常見問題集文件或 URL 及產品手冊等半結構化內容中的問題與解答服務。
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: overview
ms.date: 06/15/2018
ms.author: nolachar
ms.openlocfilehash: f34cec047c18a6db10b5adda82800c51d44c1155
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2018
ms.locfileid: "36295140"
---
# <a name="what-is-qna-maker"></a>什麼是 QnA Maker？

[QnA Maker](https://qnamaker.ai) 可讓您加強 FAQ (常見問題集) 文件或 URL 及產品手冊等半結構化內容中的問題與解答服務。 您可以建置可靈活應對使用者查詢的問題與解答模型，並以自然的談話方式訓練聊天機器人提供回應。

易於使用的圖形化使用者介面可讓您在沒有任何開發人員經驗的情況下，建立、管理和訓練您的服務，並讓其上線及執行。

![概觀](../media/qnamaker-overview-learnabout/overview.png)

## <a name="highlights"></a>重點摘要

- 透過完全**無程式碼**的體驗來[建立常見問題集聊天機器人](https://aka.ms/qnamaker-docs-create-faqbot)。
- **不再有網路節流**。 針對裝載的服務支付費用，而非針對交易數目。 請參閱[定價頁面](https://aka.ms/qnamaker-docs-pricing)，以取得詳細資料。
- **根據您的需求進行調整**。 為符合您案例的個別元件選擇適當的 SKU。 了解如何為您的 QnA Maker 服務[選擇容量](https://aka.ms/qnamaker-docs-capacity)。
- **完整的資料合規性**。 資料和執行階段元件會部署在使用者的 Azure 訂用帳戶中，且在其合規性界限內。 詳細資訊請見下文。

## <a name="key-qna-maker-processes"></a>主要的 QnA Maker 處理程序

QnA Maker 為您的資料提供兩個主要服務：

* **擷取**：結構化的問題-解答資料會擷取自半結構化資料來源 (例如常見問題集和產品手冊)。 建立知識庫時就會進行此擷取。 [在此](https://aka.ms/qnamaker-docs-createkb)建立您的知識庫。

* **比對**：一旦您的知識庫經過[訓練和測試](https://aka.ms/qnamaker-docs-trainkb)後，您就可以[發行](https://aka.ms/qnamaker-docs-publishkb)它。 這可對 QnA Maker 知識庫啟用端點，然後讓您在聊天機器人或應用程式中使用。 此端點接受使用者問題，而且會以知識庫中最佳的問題/答案組合作為回應，其中會包含該組合的信賴分數。

## <a name="qna-maker-architecture"></a>QnA Maker 架構

QnA Maker 堆疊是由下列各部分所組成：

1. **QnA Maker 管理服務 (控制階段)**：QnA Maker 知識庫的管理體驗，其中包含初始建立、更新、訓練及發佈。 這些活動可以透過[入口網站](https://qnamaker.ai)或[管理 API](https://aka.ms/qnamaker-v4-apis) 來完成。 管理服務會與下方執行階段元件通訊。

2. **QnA Maker 執行階段 (資料階段)**：資料和執行階段會部署在使用者所選區域中的 Azure 訂用帳戶中。 客戶的問題/解答內容都會儲存在 [Azure 搜尋服務](https://azure.microsoft.com/services/search/)中，而執行階段會部署為[應用程式服務](https://azure.microsoft.com/services/app-service/)。 (選擇性) 您也可以選擇部署 [Application Insights](https://azure.microsoft.com/services/application-insights/) 資源來進行分析。

![架構](../media/qnamaker-overview-learnabout/architecture.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建立 QnA Maker 服務](../how-to/set-up-qnamaker-service-azure.md)
