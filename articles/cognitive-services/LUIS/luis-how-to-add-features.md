---
title: 在 LUIS 應用程式中新增功能 | Microsoft Docs
description: 使用 Language Understanding (LUIS) 來新增應用程式功能，可改善對類別和模式進行意圖和實體的偵測或預測
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/30/2018
ms.author: diberry
ms.openlocfilehash: 5ec75436c7df5c08f5507794229bec1f9adb2804
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2018
ms.locfileid: "39222948"
---
# <a name="use-features-to-improve-your-luis-apps-performance"></a>使用功能來改善 LUIS 應用程式的效能  

您可以將功能新增到 LUIS 應用程式來改善它的準確性。 功能協助 LUIS 的方式是提供特定的文字與片語屬於某個類別的提示。 如果 LUIS 學習到如何辨識類別的其中一個成員，就能以同樣方式處理其他成員。

## <a name="add-phrase-list"></a>新增片語清單

1. 在 [我的應用程式] 頁面上按一下應用程式的名稱，接著按一下 [建置]，然後在應用程式的左窗格中按一下 [片語清單]，即可開啟您的應用程式。 

    ![片語清單瀏覽](./media/luis-add-features/phrase-list-nav.png)

2. 在 [片語清單] 頁面上，按一下 [建立新的片語清單]。 
 
    ![建立新的片語清單](./media/luis-add-features/create-new-phrase-list.png)
    
3. 在 [新增片語清單] 對話方塊中，輸入 "Cities" (城市) 作為片語清單的名稱。 在 [值] 方塊中，輸入片語清單的值。 您可以一次輸入一個值，或輸入一組以逗號分隔的值，然後按 **Enter**。

    ![新增片語清單 Cities](./media/luis-add-features/add-phrase-list-cities.png)

4. LUIS 可以提議要新增到片語清單的相關值。 按一下 [建議]，以取得一組語意上與已新增值相關的提議值。 您可以按一下任何提議的值，或按一下 [全部新增] 以將它們全部新增。

    ![片語清單提議的值](./media/luis-add-features/related-values.png)

5. 如果已新增的片語清單值是可交換使用的替代項，請按一下 [這些值是可交換的]。

    ![片語清單提議的值](./media/luis-add-features/interchangeable.png)

6. 按一下 [檔案]。 "Cities" 片語清單會新增到 [片語清單] 頁面。

    ![片語清單已新增](./media/luis-add-features/phrase-list-cities.png)

## <a name="edit-phrase-list"></a>編輯片語清單

在 [片語清單] 頁面上，按一下片語清單的名稱。 在開啟的 [編輯片語清單] 對話方塊中，進行任何所需的編輯變更，然後按一下 [儲存]。

 ![片語清單已新增](./media/luis-add-features/edit-phrase-list.png)

## <a name="delete-phrase-list"></a>刪除片語清單 

按一下該列結尾的省略符號 (***...***) 按鈕，然後選取 [刪除]。

 ![刪除已新增的清單](./media/luis-add-features/delete-phrase-list.png)

## <a name="deactivate-phrase-list"></a>停用片語清單 

按一下該列結尾的省略符號 (***...***) 按鈕，然後選取 [停用]。

 ![停用已新增的清單](./media/luis-add-features/deactivate-phrase-list.png)

## <a name="pattern-regular-expression-feature"></a>模式 (規則運算式) 功能 
**此功能已被取代**。 新的模式功能無法新增到 LUIS。 任何現有的模式功能均支援到 2018 年 5 月為止。 將與 PR 進行比對的標準 LUIS 規則運算式提供給 [Recognizers-Text Github 存放庫](https://github.com/Microsoft/Recognizers-Text) \(英文\)。 

## <a name="next-steps"></a>後續步驟

新增、編輯、刪除或停用片語清單之後，請再次[將應用程式定型和測試](luis-interactive-test.md)，以查看效能是否改善。
