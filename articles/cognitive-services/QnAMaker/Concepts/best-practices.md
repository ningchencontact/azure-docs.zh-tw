---
title: 最佳做法 - QnA Maker - Azure 認知服務 | Microsoft Docs
description: 使用下列最佳做法可改善您的知識庫，並為您的應用程式/聊天機器人使用者提供更好的結果。
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: 7a85ebbc3892a90e98e73a73425c1f8ec1de0b35
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/01/2018
ms.locfileid: "35370871"
---
# <a name="best-practices"></a>最佳作法
[知識庫開發生命週期](../Concepts/development-lifecycle-knowledge-base.md)可引導您了解如何管理端對端知識庫。 使用下列最佳做法可改善您的知識庫，並為您的應用程式/聊天機器人使用者提供更好的結果。

## <a name="extraction"></a>擷取
QnA Maker 會持續改進從內容中擷取 QnA 的演算法，並擴充支援的檔案和 HTML 網頁格式的清單。 請根據您要從中擷取的文件類型，遵循擷取的[指導方針](../Concepts/data-sources-supported.md)。 

一般情況下，常見問題集頁面應獨立存在，而不會與其他資訊結合。 產品手冊應有清楚的標題，且最好有索引頁面。 

## <a name="rankingmatching"></a>排名/比對
請務必充分利用 QnA Maker 所支援的排名功能。 這樣將可提高指定的使用者查詢獲得適當回應的可能性。

### <a name="add-alternate-questions"></a>新增替代問題
[替代問題](../How-To/edit-knowledge-base.md)可提高使用者查詢產生相符項目的可能性。 在同樣的問題可用多種方式提問時，替代問題就有其效用。 這包括文句結構的變更 (例如，「有地方停車嗎？」 與「你們有停車場嗎？」的不同)，或用語風格和俚語的變更 (例如，「嗨」與「嘿」、「你好啊」的不同)。

### <a name="use-metadata-filters"></a>使用中繼資料篩選條件
[中繼資料](../How-To/edit-knowledge-base.md)可增加根據篩選條件縮小使用者查詢結果範圍的能力。 即使查詢相同，知識庫解答也可能根據中繼資料標記而不同。 例如，如果餐廳分店的地點不同 (亦即，中繼資料為「地點：西雅圖」和「地點：雷德蒙」的不同)，「停車場在哪裡」就可能有不同的解答。

### <a name="use-synonyms"></a>使用同義字
同義字在英文中可獲得某種程度的支援，使用[文字變異形式](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fd)可為關鍵字新增採用不同格式的同義字，例如：*buy* -> *purchase* 或 *netbanking* -> *net banking*。 同義字應新增於 QnA Maker 服務層級上，而由服務中所有的知識庫所共用。

### <a name="use-distinct-words-to-differentiate-questions"></a>使用相異字詞區隔問題
會比對使用者查詢與知識庫中所含問題的 QnA Maker 比對和排名演算法，在以每個問題分別處理不同的需求時，可獲得最佳效果。 若在不同問題間有相同的字組重複出現，就比較難以用這些字組為指定的使用者查詢選擇正確的解答。

## <a name="collaborate"></a>共同作業
QnA Maker 可讓使用者對知識庫進行[共同作業](../How-to/collaborate-knowledge-base.md)。 使用者必須要有 Azure QnA Maker 資源群組的存取權，才能存取知識庫。 有些組織可能想要將知識庫的編輯和維護委外處理，但同時仍能夠保護其 Azure 資源的存取權。 若要達成此「編輯者-核准者」模型，可以在不同的訂用帳戶中設定兩個相同的 [QnA Maker 服務](../How-to/set-up-qnamaker-service-azure.md)，並將其中一個指定給編輯測試週期。 測試完成之後，即可使用[匯入-匯出](../Tutorials/migrate-knowledge-base.md)程序將知識庫內容轉移至核准者的 QnA Maker 服務，由他來執行最終的知識庫發佈和端點更新。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [編輯知識庫](../How-to/edit-knowledge-base.md)

