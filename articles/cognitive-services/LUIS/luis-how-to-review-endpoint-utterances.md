---
title: 審查使用者語句-LUIS
titleSuffix: Azure Cognitive Services
description: 主動式學習會捕捉端點查詢, 並選取不確定的使用者端點語句。 您可以檢查這些語句來選取意圖, 並標記這些讀取世界語句的實體。 在您的範例語句中接受這些變更, 然後進行定型和發佈。 然後, LUIS 會更準確地識別語句。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: diberry
ms.openlocfilehash: 9b809681b68fe3347a68cb2b2006c41783a356a6
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932800"
---
# <a name="how-to-review-endpoint-utterances-in-luis-portal-for-active-learning"></a>如何在 LUIS 入口網站中審查用於主動式學習的端點語句

[主動式學習](luis-concept-review-endpoint-utterances.md)會捕捉端點查詢, 並選取不確定的使用者端點語句。 您可以檢查這些語句來選取意圖, 並標記這些讀取世界語句的實體。 在您的範例語句中接受這些變更, 然後進行定型和發佈。 然後, LUIS 會更準確地識別語句。


## <a name="enable-active-learning"></a>啟用主動式學習

若要啟用主動式學習, 請記錄使用者查詢。 這是藉由使用`log=true` querystring 參數和值來設定[端點查詢](luis-get-started-create-app.md#query-the-endpoint-with-a-different-utterance)來完成。

## <a name="disable-active-learning"></a>停用主動式學習

若要停用主動式學習, 請不要記錄使用者查詢。 這是藉由使用`log=false` querystring 參數和值來設定[端點查詢](luis-get-started-create-app.md#query-the-endpoint-with-a-different-utterance)來完成。

## <a name="filter-utterances"></a>篩選語句

1. 在 [我的應用程式] 頁面上選取您的應用程式名稱加以開啟 (例如，TravelAgent)，然後選取頂端列中的 [建置]。

1. 在 [改善應用程式效能] 之下，選取 [檢閱端點意圖]。

1. 在 [檢閱端點意圖] 頁面上，選取 [依意圖或實體篩選器清單] 文字方塊。 此下拉式清單包含 [意圖] 之下的所有意圖，以及 [實體] 之下的所有實體。

    ![語句篩選條件](./media/label-suggested-utterances/filter.png)

1. 在下拉式清單中選取類別 (意圖或實體) 並檢閱意圖。

    ![意圖語句](./media/label-suggested-utterances/intent-utterances.png)

## <a name="label-entities"></a>標示實體
LUIS 會以藍色醒目提示的實體名稱取代實體語彙基元 (字組)。 如果語句有未標示的實體，請在語句中加以標示。 

1. 在語句中選取字組。 

1. 從清單中選取實體。

    ![標示實體](./media/label-suggested-utterances/label-entity.png)

## <a name="align-single-utterance"></a>對齊單一語句

每個意圖都有建議的意圖顯示在 [一致的意圖] 資料行中。 

1. 如果您同意該建議，請選取核取記號。

    ![保留一致的意圖](./media/label-suggested-utterances/align-intent-check.png)

1. 如果您不同意該建議，請從一致的意圖下拉式清單中選取正確的意圖，然後選取一致的意圖右邊的核取記號。 

    ![對齊意圖](./media/label-suggested-utterances/align-intent.png)

1. 在您選取核取記號之後，語句就會從清單中移除。 

## <a name="align-several-utterances"></a>對齊數個語句

若要對齊數個語句，請核取語句左邊的方塊，然後選取 [新增所選] 按鈕。 

![對齊數個](./media/label-suggested-utterances/add-selected.png)

## <a name="verify-aligned-intent"></a>驗證一致的意圖

您可以移至 [意圖] 頁面，選取意圖名稱，然後檢閱意圖，以確認語句與正確的意圖一致。 [檢閱端點意圖] 中的意圖會位於清單中。

## <a name="delete-utterance"></a>刪除語句

您可以從檢閱清單中刪除每個語句。 刪除後，它就不再出現於清單中。 即使使用者從端點輸入相同意圖，情況也是如此。 

如果您不確定是否應該刪除意圖，請將它移至 [無] 意圖，或建立新意圖 (例如「其他」) 並將語句移至該意圖。 

## <a name="delete-several-utterances"></a>刪除數個語句

若要刪除數個語句，請選取每個項目並選取 [新增所選] 按鈕右側的垃圾桶。

![刪除數個](./media/label-suggested-utterances/delete-several.png)


## <a name="next-steps"></a>後續步驟

若要在您標示建議的語句之後，測試效能有何改善，您可以選取頂端面板中的 [測試] 來存取測試主控台。 如需有關如何使用測試主控台來測試應用程式的指示，請參閱[訓練和測試您的應用程式](luis-interactive-test.md)。
