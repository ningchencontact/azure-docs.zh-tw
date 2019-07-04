---
title: 檢閱使用者語句
titleSuffix: Language Understanding - Azure Cognitive Services
description: 主動學習擷取端點的查詢，並選取使用者的不確定的結束點談話。 您檢閱這些談話選取目的，並將標示這些讀取世界的發音的實體。 接受範例談話到這些變更，則定型和發佈。 LUIS 然後更精確地識別談話。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/25/2019
ms.author: diberry
ms.openlocfilehash: 8fac360682ef11c438cdec333fac21d6f8cfc117
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60195077"
---
# <a name="how-to-review-endpoint-utterances-in-luis-portal-for-active-learning"></a>如何檢閱主動式學習 LUIS 入口網站中的端點表達方式

[主動學習](luis-concept-review-endpoint-utterances.md)擷取端點的查詢，並選取使用者的不確定的結束點談話。 您檢閱這些談話選取目的，並將標示這些讀取世界的發音的實體。 接受範例談話到這些變更，則定型和發佈。 LUIS 然後更精確地識別談話。


## <a name="enable-active-learning"></a>啟用主動學習

若要啟用主動式學習，記錄使用者的查詢。 這可以藉由設定[端點查詢](luis-get-started-create-app.md#query-the-endpoint-with-a-different-utterance)使用`log=true`querystring 參數和值。

## <a name="disable-active-learning"></a>停用主動學習

若要停用主動式學習，不會記錄使用者的查詢。 這可以藉由設定[端點查詢](luis-get-started-create-app.md#query-the-endpoint-with-a-different-utterance)使用`log=false`querystring 參數和值。

## <a name="filter-utterances"></a>篩選語句

1. 在 [我的應用程式]  頁面上選取您的應用程式名稱加以開啟 (例如，TravelAgent)，然後選取頂端列中的 [建置]  。

1. 在 [改善應用程式效能]  之下，選取 [檢閱端點意圖]  。

1. 在 [檢閱端點意圖]  頁面上，選取 [依意圖或實體篩選器清單]  文字方塊。 此下拉式清單包含 [意圖]  之下的所有意圖，以及 [實體]  之下的所有實體。

    ![語句篩選條件](./media/label-suggested-utterances/filter.png)

1. 在下拉式清單中選取類別 (意圖或實體) 並檢閱意圖。

    ![意圖語句](./media/label-suggested-utterances/intent-utterances.png)

## <a name="label-entities"></a>標示實體
LUIS 會以藍色醒目提示的實體名稱取代實體語彙基元 (字組)。 如果語句有未標示的實體，請在語句中加以標示。 

1. 在語句中選取字組。 

1. 從清單中選取實體。

    ![標示實體](./media/label-suggested-utterances/label-entity.png)

## <a name="align-single-utterance"></a>對齊單一語句

每個意圖都有建議的意圖顯示在 [一致的意圖]  資料行中。 

1. 如果您同意該建議，請選取核取記號。

    ![保留一致的意圖](./media/label-suggested-utterances/align-intent-check.png)

1. 如果您不同意該建議，請從一致的意圖下拉式清單中選取正確的意圖，然後選取一致的意圖右邊的核取記號。 

    ![對齊意圖](./media/label-suggested-utterances/align-intent.png)

1. 在您選取核取記號之後，語句就會從清單中移除。 

## <a name="align-several-utterances"></a>對齊數個語句

若要對齊數個語句，請核取語句左邊的方塊，然後選取 [新增所選]  按鈕。 

![對齊數個](./media/label-suggested-utterances/add-selected.png)

## <a name="verify-aligned-intent"></a>驗證一致的意圖

您可以移至 [意圖]  頁面，選取意圖名稱，然後檢閱意圖，以確認語句與正確的意圖一致。 [檢閱端點意圖]  中的意圖會位於清單中。

## <a name="delete-utterance"></a>刪除語句

您可以從檢閱清單中刪除每個語句。 刪除後，它就不再出現於清單中。 即使使用者從端點輸入相同意圖，情況也是如此。 

如果您不確定是否應該刪除意圖，請將它移至 [無] 意圖，或建立新意圖 (例如「其他」) 並將語句移至該意圖。 

## <a name="delete-several-utterances"></a>刪除數個語句

若要刪除數個語句，請選取每個項目並選取 [新增所選]  按鈕右側的垃圾桶。

![刪除數個](./media/label-suggested-utterances/delete-several.png)


## <a name="next-steps"></a>後續步驟

若要在您標示建議的語句之後，測試效能有何改善，您可以選取頂端面板中的 [測試]  來存取測試主控台。 如需有關如何使用測試主控台來測試應用程式的指示，請參閱[訓練和測試您的應用程式](luis-interactive-test.md)。
