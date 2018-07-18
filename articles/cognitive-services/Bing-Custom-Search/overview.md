---
title: 什麼是 Bing 自訂搜尋？ | Microsoft Docs
description: 提供 Bing 自訂搜尋的簡略概觀
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/29/2017
ms.author: v-brapel
ms.openlocfilehash: 7cd61fc63d0d7734b842ed222c67c6753da9a418
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2018
ms.locfileid: "35368842"
---
# <a name="what-is-bing-custom-search"></a>什麼是 Bing 自訂搜尋？

Bing 自訂搜尋可讓您針對感興趣的主題，建立量身訂做的搜尋經驗。 例如，如果您的網站提供搜尋體驗，可以指定 Bing 搜尋的網域、網站及網頁。 您的使用者會看到針對他們感興趣的內容量身訂做的搜尋結果，而不需要逐頁查看包含不相關內容的搜尋結果。

若要建立自訂的網頁檢視，請使用 Bing 自訂搜尋[入口網站](https://customsearch.ai)。 入口網站可讓您建立自訂搜尋執行個體，指定要 Bing 搜尋的網域、網站和網頁，以及不想要搜尋的網站。 除了指定您知道內容的 URL 之外，也可以使用入口網站尋找您要加入的相關內容。

如果使用者輸入特定的搜尋字詞，入口網站也可讓您將特定網頁釘選到搜尋結果的頂端。 

定義您的執行個體之後，可以呼叫自訂搜尋 API，將自訂的搜尋整合到網站、傳統型應用程式或行動裝置應用程式。 如果您有網頁型的網站或應用程式，則可以讓託管的 UI 來呈現搜尋介面。

下圖顯示自訂搜尋整合的簡易性。

![圖片替代文字](./media/bcs-overview.png "Bing 自訂搜尋如何運作。")

## <a name="customize-search-suggestions"></a>自訂搜尋建議

如果您訂閱了適當等級的自訂搜尋 (請參閱[定價頁面](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/))，就可以自訂在您的自訂搜尋體驗中所做的搜尋建議。 自訂自動建議 API 會根據使用者提供的部分查詢字串，傳回一份建議的查詢清單。 有了自訂自動建議，您可以提供有助於搜尋體驗的自訂搜尋建議。 您可以指定只傳回自訂的建議，或者要包含 Bing 的建議。 如果包含 Bing 的建議，自訂的建議會在 Bing 提供的建議前面出現。 Bing 的建議會受限於您自訂搜尋執行個體的內容。

## <a name="next-steps"></a>後續步驟

若要快速開始，請參閱[建立您的第一個 Bing 自訂搜尋執行個體](quick-start.md)。

如需自訂搜尋執行個體可用選項的詳細資訊，請參閱[定義自訂搜尋執行個體](define-your-custom-view.md)。

請熟悉[自訂搜尋 API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference) 參考。 此參考包含端點、標頭，以及您用來要求搜尋結果的查詢參數清單。 它也包含回應物件的定義。

若要了解如何自訂建議，請參閱[定義自訂搜尋建議](define-custom-suggestions.md)。

務必閱讀 [Bing 使用和顯示需求](./use-and-display-requirements.md)，您才不會違反任何有關使用搜尋結果的規則。