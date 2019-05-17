---
title: 使用影片索引器網站自訂人員模型 - Azure
titlesuffix: Azure Media Services
description: 本文說明如何使用影片索引器網站自訂人員模型。
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 37e556ca458a5b0a171664e089d39cfb448d5f96
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2019
ms.locfileid: "65800123"
---
# <a name="customize-a-person-model-with-the-video-indexer-website"></a>使用影片索引器網站自訂人員模型

影片索引器支援的視訊內容的名人辨識。 名人辨識功能能根據常被要求的資料來源 (例如 IMDB、維基百科以及 LinkedIn 最有影響力的人)，涵蓋大約 1 百萬個人臉。 如需詳細概觀，請參閱[自訂影片索引器的人員模型](customize-person-model-overview.md)。

您可以使用影片索引器網站，編輯在視訊中偵測到的人臉，如本主題中所述。 您也可以使用 API，如中所述[來自訂人員模型使用 Api](customize-person-model-with-api.md)。

## <a name="central-management-of-person-models-in-your-account"></a>集中管理您的帳戶中的人員模型

1. 若要檢視、 編輯和刪除您帳戶中的人員模型、 瀏覽至影片索引器網站和登入。
2. 按一下頁面右上角的內容模型的自訂按鈕。

    ![內容模型自訂](./media/customize-face-model/content-model-customization.png)
3. 選取 [人員] 索引標籤。

    在您的帳戶，您會看到預設的人員模型。 預設的人員模型會保留任何您可能已編輯或變更您的影片，您未指定自訂的人員模型編製索引期間的深入解析中的臉部。 

    如果您建立其他人模型時，它們會在此頁面上列出。 

    ![內容模型自訂](./media/customize-face-model/content-model-customization-people-tab.png)

## <a name="create-a-new-person-model"></a>建立新的人員模型

1. 按一下 [ **+ Add model** ] 按鈕。

    ![新增人員](./media/customize-face-model/add-new-person.png)
2. 輸入模型的名稱，然後按一下名稱旁邊的核取按鈕。

    ![新增人員](./media/customize-face-model/add-new-person2.png)

    您已建立新的人員模型。 您現在可以新增至新的 「 個人 」 模式的臉部。
3. 按一下 [清單] 功能表按鈕，然後選擇**新增人員 +**。

    ![新增人員](./media/customize-face-model/add-new-person3.png)
    
## <a name="add-a-new-person-to-a-person-model"></a>新增人員模型的人員

> [!NOTE]
> 影片索引器可讓您新增人員模型中的多個具有相同名稱的人員。 不過，建議您給 unque 名稱每個人在模型中的可用性和避免困擾。

1. 若要加入人員模型中的新面貌，按一下您想要新增至所面臨的人員 model 旁的清單功能表按鈕。
1. 按一下 **新增人員 +** 從功能表。

    ![加入之人員的新面貌](./media/customize-face-model/add-new-face.png)
 
    快顯視窗會提示您填寫連絡人的詳細資料。 輸入人員的名稱，然後按一下核取記號按鈕。

    ![加入之人員的新面貌](./media/customize-face-model/add-new-face2.png)
 
您可以接著選擇 從檔案總管或拖放正面的臉部映像。 影片索引子會採用所有標準映像檔案類型 (例如：JPG、 PNG、 等等）。

影片索引器應該能夠偵測這位使用者的出現次數在未來的影片中，您索引和目前的視訊，您必須已編製索引，使用人員模型加入至這個新的面貌。 在您目前的視訊中的辨識可能需要一些時間才會生效，因為這是批次程序。


## <a name="rename-a-person-model"></a>重新命名使用者模型

您可以在您的帳戶包括預設的人員模型中，重新命名任何人的模型。 即使您重新命名預設的人員模型，仍將做為預設的人員模型，在您的帳戶。

1. 按一下您想要重新命名個人 model 旁的清單功能表按鈕。
2. 按一下 **重新命名**從功能表。

    ![重新命名的人員](./media/customize-face-model/rename-person.png)
3. 按一下目前的模型名稱，然後輸入新名稱。

    ![重新命名的人員](./media/customize-face-model/rename-person2.png)
4. 按一下 [檢查] 按鈕，為您要重新命名的模型。

## <a name="delete-a-person-model"></a>刪除人員模型

您可以刪除您帳戶中建立的任何人模型。 不過，您無法刪除預設的人員模型。

1. 按一下 **刪除**從功能表。

    ![刪除使用者](./media/customize-face-model/delete-person.png)
    
    快顯視窗會顯示，並通知您這個動作會刪除人員模型及其所有的人和它所包含的檔案。 此動作無法復原。 

    ![刪除使用者](./media/customize-face-model/delete-person2.png)
1. 如果您確定，請按一下 [刪除]。

> [!NOTE]
> 使用此 （現在已刪除） 的人員模型已編製索引的現有影片將不支援，讓您更新的影片中出現的名稱。 您可以編輯這些影片中臉部的名稱，您將使用另一個人模型進行重新索引時，才。 如果您未指定人員模型重新建立索引，則將使用預設的模型。 

## <a name="manage-existing-people-in-a-person-model"></a>管理人員模型中的現有使用者

若要查看任何人模型的內容，按一下 人員模型的名稱旁邊的箭號。
下拉式清單會顯示您的所有人員在該特定的人員模型。 如果您按一下每個人員旁邊的 [清單] 功能表按鈕時，您會看到管理、 重新命名和刪除選項。  

![加入之人員的新面貌](./media/customize-face-model/manage-people.png)

### <a name="rename-a-person"></a>重新命名的人員

1. 若要重新命名您個人的模型中的人員，請按一下 [清單] 功能表按鈕，然後選擇**重新命名**從清單中的功能表。
1. 按一下 在您的新名稱的類型與個人的目前名稱。
1. 按一下 [檢查] 按鈕和人員將會重新命名。

### <a name="delete-a-person"></a>刪除使用者

1. 若要從您個人的模型中刪除個人，按一下 [清單] 功能表按鈕，然後選擇**刪除**從清單中的功能表。
1. 快顯視窗會告訴您這個動作會刪除該人員，此動作無法復原。
1. 再次按一下 [刪除]，這會移除人員模型中的人員。

### <a name="manage-a-person"></a>管理人員

如果您按一下**管理**，您會看到從定型此模型中人員的所有人臉。 這些臉部來自該人員使用此模型中人員的影片中的項目，或從您已經手動上傳的映像。 

您可以新增多個臉部的人員，依序按一下 新增映像。

若要重新命名之人員，並從 Person 模型中刪除該人員，您可以使用 [管理] 窗格。

## <a name="use-a-person-model-to-index-a-video"></a>使用個人的模型來編製索引的影片

您可以使用個人模型的視訊上傳期間，指派人員模型，您新的影片編製索引。

若要使用您個人的模型，在新的影片，執行下列作業：

1. 按一下 **上傳**頁面頂端的按鈕。

    ![上傳](./media/customize-face-model/upload.png)
1. 您的視訊檔案拖放的圓圈中，或瀏覽您的檔案。
1. 按一下 [進階的選項] 箭頭。

    ![上傳](./media/customize-face-model/upload2.png)
1. 按一下下拉箭號，然後選取您所建立的人員模型。

    ![上傳](./media/customize-face-model/upload3.png)
1. 按一下 在頁面底部的 上傳 選項，會使用您個人的模型建立新的視訊的索引。

如果您未指定人員模型上傳期間，影片索引器會編製索引中您的帳戶使用預設的人員模型的影片。

## <a name="use-a-person-model-to-reindex-a-video"></a>使用個人的模型來重新建立索引的影片 

若要使用的人員模型重新索引您的集合中的影片，請移至您帳戶的影片上的影片索引器首頁和暫留時的視訊，您想要重新建立索引的名稱。

您會看到選項來編輯、 刪除和重新索引您的影片。 

1. 按一下選項以重新索引您的影片。

    ![重新編製索引](./media/customize-face-model/reindex.png)

    您現在可以選取個人模式，以重新索引影片的對象。
1. 按一下下拉箭號，然後選取您想要使用的人員模型。 

    ![重新編製索引](./media/customize-face-model/reindex2.png)

1. 按一下  **Reindex**按鈕，然後您的影片將會重新編製索引使用您個人的模型。

您用來重新建立索引的影片的人員模型中，將會儲存您對臉部偵測和辨識在影片中，您只編製索引的任何新編輯。

## <a name="managing-people-in-your-videos"></a>管理您的影片中的人員

您可以管理的臉部偵測到的和已辨識出在影片中您的編輯和刪除臉部編製索引的人員。

刪除臉部，從視訊的深入解析中移除特定的臉部。

編輯臉部、 重新命名，偵測到可能在視訊中辨識臉部。 當您編輯在視訊中的臉部時，該名稱會儲存為個人模型上傳並編製索引期間所指派的影片中人員項目。

如果您沒有指派人員模型的影片上傳期間，您的編輯會儲存在您的帳戶預設人員模型中。

### <a name="edit-a-face"></a>編輯人臉


> [!NOTE]
> 如果人員模型有兩個或多個不同的人，具有相同名稱，您無法標記使用該人員模型的視訊中該名稱。 您只能對影片索引器中的內容模型自訂 頁面的 人員 索引標籤中的該名稱的共用人員進行變更。 基於這個理由，建議每個人提供唯一的名稱，在您個人的模型。

1. 瀏覽至影片索引器網站並登入。
1. 在您的帳戶中搜尋您要檢視和編輯的視訊。
1. 若要編輯的表面，在您的影片中，移至 [Insights] 索引標籤並按一下視窗右上角的鉛筆圖示。

    ![編輯影片中臉部](./media/customize-face-model/edit-face.png)
1. 按一下任何偵測到的人臉，並從 "Unknown #X" (或先前已指派給該人臉的名稱) 變更其名稱。 
1. 輸入新名稱之後，按一下新名稱旁邊的核取圖示。 這會儲存新的名稱和會辨識並命名其他影片目前和未來您上傳的影片中這張臉部的所有項目。 您目前的影片中臉部辨識可能需要一些時間才會生效，因為這是批次程序。

如果您使用影片的人員模型中現有人員的名稱取代名稱表面，這將會合併這段影片中的項目已經存在模型中該人員偵測到的臉部映像。 如果您使用完全新名稱名稱表面，這會建立新的連絡人項目中使用影片的人員模型。 

    ![Edit a face in your video](./media/customize-face-model/edit-face2.png)

### <a name="delete-a-face"></a>刪除人臉

若要刪除偵測的臉部在視訊中，移至 [Insights] 窗格中，按一下窗格右上角的鉛筆圖示。 按一下 表面的 名稱底下的 刪除 選項。 這將會從視訊中移除偵測到的該張人臉。 此人臉部仍然能夠偵測中的其他影片中出現，但它們都已編製索引之後，您可以從這些影片也刪除所面臨的。 顯示 person、 如果有已命名，也仍存在於用來從中您刪除所面臨的除非您特別人員模型中刪除之人員的影片編製索引的人員模型。

![刪除您的影片中臉部](./media/customize-face-model/delete-face.png)


## <a name="next-steps"></a>後續步驟

[使用 API 自訂人員模型](customize-person-model-with-api.md)
