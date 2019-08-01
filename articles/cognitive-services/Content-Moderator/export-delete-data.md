---
title: 匯出或刪除使用者資料-內容仲裁
titleSuffix: Azure Cognitive Services
description: 了解如何匯出或刪除 Content Moderator 中的資料。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: pafarley
ms.openlocfilehash: b18bc2907c00ed1424db440f09c914faff6a9863
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565559"
---
# <a name="export-or-delete-user-data-in-content-moderator"></a>匯出或刪除 Content Moderator 中的使用者資料

Content Moderator 收集使用者資料來操作服務，但客戶擁有完整控制權，可以使用[審核工具](https://contentmoderator.cognitive.microsoft.com/) \(英文\) 和[仲裁和檢閱 API](https://docs.microsoft.com/azure/cognitive-services/content-moderator/api-reference) 來檢視、匯出及刪除其資料。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

如需如何匯出和刪除 Content Moderator 中使用者資料的詳細資訊，請參閱下表。

| Data | 匯出作業 | 刪除作業 |
| ---- | ---------------- | ---------------- |
| 帳戶資訊 (訂用帳戶金鑰) | N/A | 使用 Azure 入口網站刪除 (Azure 訂用帳戶)。 或者，使用 [Review UI](https://contentmoderator.cognitive.microsoft.com/) Team 設定頁面中的 [刪除小組]  按鈕。 |
| 自訂比對的影像 | 呼叫[取得影像識別碼 API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f676) \(英文\)。 影像會以單向專屬雜湊格式儲存，而且沒有任何方法可以擷取實際影像。 | 呼叫[刪除所有影像 API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f686) \(英文\)。 或者，使用 Azure 入口網站刪除 Content Moderator 資源。 |
| 自訂比對的字詞 | 呼叫[取得所有字詞 API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67e) \(英文\) | 呼叫[刪除所有字詞 API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67d) \(英文\)。 或者，使用 Azure 入口網站刪除 Content Moderator 資源。 |
| Tags | N/A | 使用 [Review UI Tag] \(Review UI 標籤\) 設定頁面中每個標籤可用的**刪除**圖示。 或者，使用 [Review UI](https://contentmoderator.cognitive.microsoft.com/) Team 設定頁面中的 [刪除小組]  按鈕。 |
| 評論 | 呼叫[取得檢閱 API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) \(英文\) | 使用 [Review UI](https://contentmoderator.cognitive.microsoft.com/) 小組設定頁面中的 [刪除小組]  按鈕。
| 使用者人數 | N/A | 使用 [Review UI](https://contentmoderator.cognitive.microsoft.com/) 小組設定頁面中每個使用者可用的**刪除**圖示。 或者，使用 [Review UI](https://contentmoderator.cognitive.microsoft.com/) Team 設定頁面中的 [刪除小組]  按鈕。 |

