---
title: Bing 自訂搜尋：定義自訂自動建議的建議 | Microsoft Docs
description: 描述如何使用自訂的建議設定自訂自動建議
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.technology: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: a41b4e5b6c268ec68488c6764d4192cf8d2345a4
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2018
ms.locfileid: "35368819"
---
# <a name="configure-your-custom-autosuggest-experience"></a>設定您的自訂自動建議體驗
如果您訂閱了適當等級的自訂搜尋 (請參閱[定價頁面](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/))，就可以自訂在您的自訂搜尋體驗中所做的搜尋建議。 自訂自動建議會根據使用者提供的部分查詢字串，傳回一份建議的查詢清單。 有了自訂自動建議，您可以提供有助於搜尋體驗的自訂搜尋建議。 您可以指定只傳回自訂的建議，或者也要包含 Bing 的建議。 如果您包含 Bing 的建議，自訂的建議會在 Bing 的建議前面出現。 Bing 的建議會受限於您自訂搜尋執行個體的內容。

## <a name="configure-custom-autosuggest"></a>設定自訂自動建議
若要為您的自訂搜尋執行個體設定自訂自動建議，請使用下列指示。

1.  登入[自訂搜尋](https://customsearch.ai)。
2.  按一下 [自訂搜尋] 執行個體。 若要建立執行個體，請參閱[建立您的第一個 Bing 自訂搜尋執行個體](quick-start.md)。
3.  按一下 [自動建議] 索引標籤。

## <a name="enable-bing-suggestions"></a>啟用 Bing 的建議
若要啟用 Bing 的建議，請將 [自動 Bing 建議] 滑桿切換到開啟的位置。 滑桿會變成藍色。

## <a name="add-suggestions"></a>新增建議
若要新增建議，請在文字方塊中輸入。 按下 Enter 鍵或按一下 **+** 圖示。 自訂的建議可以是任何語言，而且會在 Bing 的建議前面出現。

## <a name="upload-suggestions"></a>上傳建議
您可以從檔案上傳建議清單。 將每個建議放在不同行。 按一下上傳圖示，然後選取您的檔案。

## <a name="remove-suggestions"></a>移除建議
若要移除建議，請針對想要移除的建議，按一下建議旁邊的移除圖示。

[!INCLUDE[publish or revert](./includes/publish-revert.md)]

  >[!NOTE]  
  >這個程序可能需要 24 小時，自動建議設定的變更才會生效。

## <a name="next-steps"></a>後續步驟

- [取得自訂建議](./get-custom-suggestions.md)
- [搜尋自訂執行個體](./search-your-custom-view.md)
- [設定和使用自訂託管的 UI](./hosted-ui.md)