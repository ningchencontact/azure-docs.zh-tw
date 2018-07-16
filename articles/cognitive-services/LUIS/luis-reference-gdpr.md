---
title: 匯出和刪除客戶資料 - LUIS - Azure 認知服務 | Microsoft Docs
description: 從 Language Understanding 服務 (LUIS) 匯出和刪除客戶資料的參考。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 05/23/2018
ms.author: v-geberr;
ms.openlocfilehash: f684b8ab875e2fbb774dc4a29bce25be41b24e6d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2018
ms.locfileid: "35370551"
---
# <a name="export-and-delete-your-customer-data-in-language-understanding-luis-in-cognitive-services"></a>在認知服務的 Language Understanding (LUIS) 中匯出和刪除客戶資料

## <a name="summary-of-customer-data-request-features"></a>客戶資料要求功能的摘要
Language Understanding Intelligent Service (LUIS) 可保存客戶內容以執行此服務，但 LUIS 使用者具有檢視、匯出和刪除其資料的完整控制權。 此作業可透過 LUIS Web [入口網站](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-reference-regions)或 [LUIS 程式設計 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) 來完成。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

客戶的內容會加密並儲存在 Microsoft 區域 Azure 儲存體中，且包含：

- 註冊時收集的使用者帳戶內容
- 建置模型所需的訓練資料 (也就是目的和實體)
- 在執行階段記錄以利改善使用者模型的使用者查詢
  - 使用者可藉由將 `&log=false`附加至要求以關閉查詢記錄，詳情請見[這裡](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-resources-faq#how-can-i-disable-the-logging-of-utterances)

## <a name="deleting-customer-data"></a>刪除客戶資料
LUIS 使用者有完整的控制權可刪除任何使用者內容 (透過 LUIS Web 入口網站或 LUIS 程式設計 API)。 下表顯示這兩者的輔助連結：

| | **使用者帳戶** | **應用程式** | **語句** | **使用者查詢** |
| --- | --- | --- | --- | --- |
| **入口網站** | [連結](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-how-to-account-settings) | [連結](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/create-new-app#delete-app) | [連結](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/create-new-app#delete-app) | [連結](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/create-new-app#delete-app) |
| **API** | [連結](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [連結](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [連結](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [連結](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>匯出客戶資料
LUIS 使用者有完整的控制權可檢視入口網站上的資料，但必須透過 LUIS 程式設計 API 才可加以匯出。 下表顯示透過 LUIS 程式設計 API 進行資料匯出的輔助連結：

| | **使用者帳戶** | **應用程式** | **語句** | **使用者查詢** |
| --- | --- | --- | --- | --- |
| **API** | [連結](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [連結](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [連結](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [連結](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |


## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [LUIS 區域參考](./luis-reference-regions.md)
