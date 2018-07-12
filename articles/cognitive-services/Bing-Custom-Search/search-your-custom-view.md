---
title: Bing 自訂搜尋：搜尋自訂檢視 | Microsoft Docs
description: 描述如何搜尋網頁的自訂檢視
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 75f6c8d299c7eed901dda0631fca74b040f72e30
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2018
ms.locfileid: "35368822"
---
# <a name="call-your-custom-search"></a>呼叫您的自訂搜尋
在您第一次呼叫自訂搜尋 API 取得執行個體的搜尋結果之前，必須先取得認知服務訂用帳戶金鑰。 若要取得自訂搜尋 API 的金鑰，請參閱[試用認知服務](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search)。

> [!NOTE]
> 現有的 Bing 自訂搜尋客戶如果擁有在 2017 年 10 月 15 日 (含) 以前佈建的預覽版金鑰，將能夠使用其金鑰到 2017 年 11 月 30 日，或用完允許的最大查詢數目為止。 之後，這些客戶必須遷移到 Azure 上的正式運作版本。

## <a name="try-it-out"></a>試做
設定自訂的搜尋體驗之後，您可以從自訂搜尋入口網站中測試設定。 登入[自訂搜尋](https://customsearch.ai)，按一下 [自訂搜尋] 執行個體，然後按一下 [生產] 索引標籤。此時會顯示 [端點] 索引標籤。 您的訂用帳戶會決定可以試用哪些端點，請參閱[定價頁面](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)。 若要測試端點，請從下拉式清單中選取端點，並設定相關聯的設定選項。 

以下是可用的選項。

- **查詢**：要搜尋的搜尋字詞。 僅適用於網頁、影像和自動建議端點。
- **自訂組態識別碼**：所選取自訂搜尋執行個體的組態識別碼。 這是唯讀欄位。
- **市場**：結果來自的市場。 僅適用於網頁、影像和託管的 UI 端點。
- **訂用帳戶金鑰**：要用來測試的訂用帳戶金鑰。 您可以從下拉式清單中選取金鑰，或手動輸入一個。
- **安全搜尋**：用來篩選成人內容網頁的篩選條件。 僅適用於網頁、影像和託管的 UI 端點。
- **計數**：回應中傳回的搜尋結果數目。 僅適用於網頁和影像端點。
- **位移**：回應中傳回的搜尋結果數目。 僅適用於網頁和影像端點。

為網頁、影像或自動建議指定所有必要的選項之後，請按一下 [呼叫] 在右窗格中檢視 JSON 回應。 

如果您選取託管的 UI 端點，可以從右窗格測試搜尋體驗。

## <a name="next-steps"></a>後續步驟
- [使用 C# 呼叫您的自訂檢視](./call-endpoint-csharp.md)
- [使用 Java 呼叫您的自訂檢視](./call-endpoint-java.md)
- [使用 NodeJs 呼叫您的自訂檢視](./call-endpoint-nodejs.md)
- [使用 Python 呼叫您的自訂檢視](./call-endpoint-python.md)