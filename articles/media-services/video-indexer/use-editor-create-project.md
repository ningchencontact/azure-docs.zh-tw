---
title: 使用影片索引器編輯器來建立專案
titlesuffix: Azure Media Services
description: 本主題示範如何使用影片索引器編輯器來建立專案。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/02/2019
ms.author: juliako
ms.openlocfilehash: 725e265bb7f8c98db19971c50e690974cb38a0d2
ms.sourcegitcommit: 17411cbf03c3fa3602e624e641099196769d718b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2019
ms.locfileid: "65518145"
---
# <a name="use-the-video-indexer-editor-to-create-projects"></a>使用影片索引器編輯器來建立專案

影片索引子網站，可讓您使用您的影片深入解析，以： 找到正確的媒體內容，請找出您感興趣，並使用結果來建立全新的專案的組件。 專案建立之後，可以轉譯和影片索引器從下載並用在自己的編輯應用程式或下游工作流程。

某些案例中，您可能會發現這項功能很有用︰ 

* 建立電影反白顯示的結尾。
* 在新聞轉換中使用舊的剪輯的影片。
* 建立用於社交媒體較短的內容。

這篇文章將示範如何從頭開始建立專案以及如何從您的帳戶中的影片中建立專案。

## <a name="create-new-project-and-manage-videos"></a>建立新的專案，並管理影片

1. 瀏覽至[影片索引子](https://www.videoindexer.ai/)網站並登入。
1. 選取 [**專案**] 索引標籤。如果您已建立專案之前，您會看到您的所有其他專案這裡。
1. 按一下 **建立新專案**。  

    ![新增專案](./media/video-indexer-view-edit/new-project.png)
1. 按一下鉛筆圖示為您的專案命名。 指出 「 未命名的專案 」 的文字取代為您的專案名稱，然後按一下核取。

    ![新增專案](./media/video-indexer-view-edit/new-project3.png)
    
### <a name="add-videos-to-the-project"></a>將影片新增至專案

> [!NOTE]
> 目前，專案只能包含在相同的語言中編製索引的影片。 一旦您選取一種語言的影片，您無法新增帳戶位於不同的語言中的視訊。

1. 新增您想要使用此專案中所選取的視訊**將影片新增**。

    在您的帳戶 和 搜尋 方塊，指出 「 搜尋的文字、 關鍵字或視覺內容 」，您會看到所有影片。 若要搜尋具有指定的人員、 標籤、 品牌、 關鍵字或相符項目中文字稿和 OCR 的影片。
    
    比方說，在下圖中，我們正在尋找的影片提到"GitHub"。
    
    ![GitHub](./media/video-indexer-view-edit/github.png)

    您可以選取，以進一步篩選結果**篩選結果**。 您可以篩選以顯示含有特定人員的影片，或是指定您只想查看影片的結果會以特定語言或具有特定的擁有者。 <br/> 您也可以指定查詢的範圍。 例如，如果您想要搜尋"GitHub"OCR 中，選取**視覺文字**。

    ![Filter](./media/video-indexer-view-edit/visual-text.png)

    您可以在查詢層多個篩選條件。 使用**+** / **-** 按鈕來新增/移除篩選。 使用**清除篩選**移除所有篩選。
1. 若要新增影片，請選取它們，然後選取**新增**。
1. 現在，您會看到所有您所選擇的影片。 這些是您要選取專案的剪輯的影片。

    藉由拖放或選取清單的 [功能表] 按鈕並選取，您可以重新排列的順序，包括視訊**下移**或是**向上移動**。 從 [清單] 功能表中，您也能夠從這個專案中移除這部影片。 

    ![重新排列](./media/video-indexer-view-edit/rearrange.png)
    
    您可以選取此專案來加入更多視訊隨時**將影片新增**。 您也可以新增多個項目在相同的視訊到您的專案。 您可能想要這樣做，如果您想要顯示從一個視訊，然後從另一個剪輯剪輯和從第一個視訊的另一個剪輯。 

### <a name="select-clips-to-use-in-your-project"></a>選取您的專案中使用剪輯

如果您按一下每個影片的右邊的向下箭號，您會開啟在影片中根據時間戳記 （剪輯的視訊） 的深入解析。 

1. 選取 **檢視深入剖析資訊**想要請參閱和您不想要查看自訂的深入解析。 

    ![檢視深入剖析資訊](./media/video-indexer-view-edit/insights.png)
1. 若要建立特定的剪輯的查詢，使用 [搜尋] 方塊，指出 「 搜尋文字記錄、 視覺文字、 人員和標籤中 」。
1. 新增篩選，進一步指定何種方法是選取您要尋找的場景中的 詳細資料**篩選選項**。

    ![篩選選項](./media/video-indexer-view-edit/filter-options.png)

    例如，您可能要查看 GitHub 提到 Donovan Brown 時在螢幕上的剪輯。 這麼做，您需要新增具有 「 人 」 的 「 包含 」 篩選條件做深入解析的型別。 然後，您需要在篩選器的 [搜尋] 方塊中的"Donovan Brown"中輸入。
    
    ![包含](./media/video-indexer-view-edit/include.png)
    
    如果您想 GitHub 提到 Donovan Brown 時的剪輯_不_在畫面上，您只要變更 「 包含 」 篩選條件使用下拉式清單中的 「 排除 」 篩選條件。 

1. 新增剪輯至專案中，選取您想要新增的區段。 您可以再按一下區段上，取消選取這個項目。
    
    新增影片的所有區段旁邊的影片和選取清單 功能表選項，即可**選取所有區段**。 

    ![全部新增](./media/video-indexer-view-edit/add-all.png)

    您可以清除所有您選取項目的選取 清除選取項目。

> [!TIP]
> 選取及排序您的剪輯時，您可以預覽的影片播放程式頁面的右邊。 

![預覽](./media/video-indexer-view-edit/preview.png)

儲存您的專案，選取變更時，請記得**儲存專案**。 

### <a name="render-and-download-the-project"></a>轉譯，並將專案下載

> [!NOTE]
> 影片索引器付費帳戶，呈現您的專案會有編碼的成本。 影片索引子的試用帳戶僅限於 5 小時的轉譯。

1. 完成之後，請確定已儲存您的專案。 您現在可以轉譯此專案。 選取 **轉譯，並下載**。 

    ![儲存](./media/video-indexer-view-edit/save.png)

    會告訴您影片索引器會轉譯檔案的快顯視窗和 [下載] 連結則會傳送至您的電子郵件。 選取繼續進行。 
    
    您也會看到專案所呈現網頁頂端的通知。 完成後所呈現，您會看到已成功轉譯專案的新通知。 按一下通知以下載專案。 它會下載 mp4 格式的專案。

    ![完成轉譯](./media/video-indexer-view-edit/rendering-done.png)

1. 您可以存取已儲存的專案，從**專案** 索引標籤。 

    如果您選取此專案時，您會看到所有深入解析和這個專案的時間軸。 如果您選取**視訊編輯器**，您可以繼續對這個專案進行編輯。 編輯包含新增或移除影片和多媒體項目或重新命名專案。

    ![視訊編輯程式](./media/video-indexer-view-edit/video-editor.png)
     
## <a name="create-a-project-from-your-video"></a>從您的影片建立專案

您可以直接從影片中建立新的專案，在您的帳戶。 

1. 移至**程式庫**影片索引器網站 索引標籤。
1. 開啟您想要用來建立您的專案的影片。 在深入解析和時間軸 頁面上，選取**視訊編輯器** 按鈕。

    這會帶您前往您用來建立新專案的同一個頁面。 不同於新的專案中，您會看到視訊，您已開始編輯之前的時間戳記 insights 區段。

## <a name="see-also"></a>請參閱

[影片索引子概觀](video-indexer-overview.md)

