---
title: 匯出及刪除資料
titleSuffix: Azure Cognitive Services
description: 刪除客戶資料，以確保隱私權和合規性。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: diberry
ms.openlocfilehash: eb125133138c6de173fdeb90024a9e5d961a929d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "58895151"
---
# <a name="export-and-delete-your-customer-data-in-language-understanding-luis-in-cognitive-services"></a>在認知服務的 Language Understanding (LUIS) 中匯出和刪除客戶資料

刪除客戶資料，以確保隱私權和合規性。 

## <a name="summary-of-customer-data-request-features"></a>客戶資料要求功能的摘要
Language Understanding Intelligent Service (LUIS) 可保存客戶內容以執行此服務，但 LUIS 使用者具有檢視、匯出和刪除其資料的完整控制權。 這可以透過 LUIS web[入口網站](luis-reference-regions.md)或[LUIS 撰寫 （也就是以程式設計方式） Api](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f)。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

客戶的內容會加密並儲存在 Microsoft 區域 Azure 儲存體中，且包含：

- 註冊時收集的使用者帳戶內容
- 建置模型所需的訓練資料
- 記錄所用的使用者查詢[主動式學習](luis-concept-review-endpoint-utterances.md)以協助改善模型
  - 使用者可藉由將 `&log=false`附加至要求以關閉查詢記錄，詳情請見[這裡](troubleshooting.md#how-can-i-disable-the-logging-of-utterances)

## <a name="deleting-customer-data"></a>刪除客戶資料
LUIS 使用者具有刪除內容，透過 LUIS web 入口網站或 LUIS 撰寫 （也稱為程式設計） Api 的任何使用者的完整控制權。 下表顯示這兩者的輔助連結：

| | **使用者帳戶** | **應用程式** | **範例 Utterance(s)** | **使用者查詢** |
| --- | --- | --- | --- | --- |
| **入口網站** | [連結](luis-concept-data-storage.md#delete-an-account) | [連結](luis-how-to-start-new-app.md#delete-app) | [連結](luis-concept-data-storage.md#utterances-in-an-intent) | [主動學習表達方式](luis-how-to-review-endpoint-utterances.md#disable-active-learning)<br>[記錄的表達方式](luis-concept-data-storage.md#disable-logging-utterances) |
| **API** | [連結](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [連結](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c39) | [連結](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [連結](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>匯出客戶資料
LUIS 使用者已經有在入口網站中，檢視資料的完整控制權，不過它必須匯出透過 LUIS 撰寫的 Api （也就是以程式設計方式）。 下表顯示可協助進行資料匯出，透過 LUIS 撰寫的 （也就是以程式設計方式） Api 的連結：

| | **使用者帳戶** | **應用程式** | **語句** | **使用者查詢** |
| --- | --- | --- | --- | --- |
| **API** | [連結](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [連結](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [連結](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [連結](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |

## <a name="location-of-active-learning"></a>作用中的學習位置

若要啟用[主動式學習](luis-how-to-review-endpoint-utterances.md#enable-active-learning)，使用者的登入的談話，在已發行的 LUIS 端點，接收會儲存在下列的 Azure 地理位置：

* [歐洲](#europe)
* [澳大利亞](#australia)
* [美國](#united-states)

主動學習資料 （詳述如下），除了遵循 LUIS[地區服務的資料儲存體作法](http://azuredatacentermap.azurewebsites.net/)。 

### <a name="europe"></a>歐洲

[Eu.luis.ai](https://eu.luis.ai)入口網站和歐洲撰寫 (也稱為程式設計的 Api) 會裝載於 Azure 的歐洲地理位置。 Eu.luis.ai 入口網站和歐洲撰寫 (也稱為程式設計的 Api) 支援部署下列的 Azure 地理位置的端點：

* 歐洲
* 法國
* 英國

部署到這些 Azure 地理位置時，您的應用程式的終端使用者從端點收到的表達方式會儲存在 Azure 的歐洲地理位置中進行主動式學習。 您可以停用主動式學習，請參閱[停用主動式學習](luis-how-to-review-endpoint-utterances.md#disable-active-learning)。 若要管理預存的談話，請參閱[刪除 [utterance]](luis-how-to-review-endpoint-utterances.md#delete-utterance)。 

### <a name="australia"></a>澳大利亞

[Au.luis.ai](https://au.luis.ai)入口網站，而且澳洲撰寫 (也稱為程式設計的 Api) 會裝載於 Azure 的澳洲地理位置。 Au.luis.ai 入口網站和澳洲撰寫 (也稱為程式設計的 Api) 支援部署下列的 Azure 地理位置的端點：

* 澳大利亞

部署到這些 Azure 地理位置時，您的應用程式的終端使用者從端點收到的表達方式會儲存在 Azure 的澳洲地理位置中進行主動式學習。 您可以停用主動式學習，請參閱[停用主動式學習](luis-how-to-review-endpoint-utterances.md#disable-active-learning)。 若要管理預存的談話，請參閱[刪除 [utterance]](luis-how-to-review-endpoint-utterances.md#delete-utterance)。 

### <a name="united-states"></a>美國

[Luis.ai](https://www.luis.ai)入口網站和 United States 撰寫 (也稱為程式設計的 Api) 會裝載於 Azure 的美國地理位置。 Luis.ai 入口網站和 United States 撰寫 (也稱為程式設計的 Api) 支援部署下列的 Azure 地理位置的端點：

* 歐洲或澳洲撰寫區域不支援的 azure 地理位置

部署到這些 Azure 地理位置時，您的應用程式的終端使用者從端點收到的表達方式會儲存在 Azure 的美國地理位置中進行主動式學習。 您可以停用主動式學習，請參閱[停用主動式學習](luis-how-to-review-endpoint-utterances.md#disable-active-learning)。 若要管理預存的談話，請參閱[刪除 [utterance]](luis-how-to-review-endpoint-utterances.md#delete-utterance)。 


## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [LUIS 區域參考](./luis-reference-regions.md)
