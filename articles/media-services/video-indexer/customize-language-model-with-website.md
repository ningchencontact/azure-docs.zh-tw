---
title: 使用影片索引器網站自訂語言模型 - Azure
titlesuffix: Azure Media Services
description: 本文說明如何使用影片索引器網站自訂語言模型。
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: anzaman
ms.openlocfilehash: 7b81b53c03104023823bef75beb4ac6077feede7
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "58918683"
---
# <a name="customize-a-language-model-with-the-video-indexer-website"></a>使用影片索引器網站自訂語言模型

影片索引器可讓您上傳適應文字 (也就是從您希望引擎適應其詞彙的網域中取得的文字) 來建立自訂語言模型，以自訂語音辨識。 一旦您將模型定型之後，也會辨識出現在調整文字中的新字。 

如需自訂語言模型的詳細概觀和最佳作法，請參閱[使用影片索引器自訂語言模型](customize-language-model-overview.md)。

您可以使用影片索引器網站，在您的帳戶中建立和編輯自訂語言模型，如本主題中所述。 您也可以使用 API，如[使用 API 自訂語言模型](customize-language-model-with-api.md)中所述。

## <a name="create-a-language-model"></a>建立語言模型

1. 瀏覽至[影片索引子](https://www.videoindexer.ai/)網站並登入。
2. 若要在您的帳戶中自訂模型，按一下頁面右上角的 [內容模型自訂] 按鈕。

   ![自訂內容模型](./media/content-model-customization/content-model-customization.png)

3. 選取 [語言] 索引標籤。

    您會看到支援的語言清單。 

    ![自訂語言模型](./media/customize-language-model/customize-language-model.png)

4. 在您想要的語言下方，按一下 [新增模型]。
5. 輸入語言模型的名稱，然後按 Enter 鍵。

    這會建立模型，並提供將文字檔案上傳至模型的選項。
6. 若要新增文字檔案，按一下 [新增檔案]。 這會開啟 [檔案總管]。

7. 瀏覽至文字檔案並加以選取。 您可以將多個文字檔案新增至語言模型。

    您也可以按一下語言模型右側的 **...** 按鈕，然後選取 [新增檔案]，藉此新增文字檔案。
8. 上傳文字檔案完成之後，按一下綠色的 [定型] 選項。

    ![將語言模型定型](./media/customize-language-model/train-model.png)

定型程序需要數分鐘的時間。 定型完成後，您會在模型旁邊看到 [已定型]。 您可以從模型預覽、下載和刪除檔案。

![定型的語言模型](./media/customize-language-model/preview-model.png)

### <a name="using-a-language-model-on-a-new-video"></a>在新的影片上使用語言模型

若要在新的影片上使用語言模型，請執行下列其中一項操作：

* 按一下頁面頂端的 [上傳] 按鈕 

    ![上傳](./media/customize-language-model/upload.png)
* 將您音訊或視訊檔案放在圓圈中，或瀏覽您的檔案

    ![上傳](./media/customize-language-model/upload2.png)

這會提供您選取 [視訊來源語言] 的選項。 按一下下拉箭號，然後從清單中選取您建立的語言模型。 它應該是語言模型的語言，並在括號中提供您賦予的名稱。

按一下頁面底部的 [上傳] 選項，您的新視訊將會使用您的語言模型編製索引。

### <a name="using-a-language-model-to-reindex"></a>使用語言模型重新編製索引

若要使用您的語言模型為您收藏的視訊重新編製索引，請移至 [[影片索引器](https://www.videoindexer.ai/)] 首頁上的 [帳戶視訊]，然後將滑鼠停駐在您想要重新編製索引之視訊的名稱上。

您會看到編輯視訊、刪除視訊，以及重新編製視訊索引的選項。 按一下重新編製視訊索引的選項。

![重新編製索引](./media/customize-language-model/reindex1.png)

這會提供您選取 [視訊來源語言] 來重新編製視訊索引所使用的選項。 按一下下拉箭號，然後從清單中選取您建立的語言模型。 它應該是語言模型的語言，並在括號中提供您賦予的名稱。

![重新編製索引](./media/customize-language-model/reindex.png)

按一下 [重新編製索引] 按鈕，您的視訊將會使用您的語言模型重新編製索引。

## <a name="edit-a-language-model"></a>編輯語言模型

若要編輯語言模型，您可以變更其名稱、在其中新增檔案，以及從中刪除檔案。

如果您從語言模型中新增或刪除檔案，必須按一下綠色的 [定型] 選項，為模型重新定型。

### <a name="rename-the-language-model"></a>重新命名語言模型

您可以按一下語言模型右側的 **...**，然後選取 [重新命名]，藉此變更語言模型的名稱。 

輸入新的名稱，然後按 Enter 鍵。

### <a name="add-files"></a>新增檔案

若要新增文字檔案，按一下 [新增檔案]。 這會開啟 [檔案總管]。

瀏覽至文字檔案並加以選取。 您可以將多個文字檔案新增至語言模型。

您也可以按一下語言模型右側的 **...** 按鈕，然後選取 [新增檔案]，藉此新增文字檔案。

### <a name="delete-files"></a>刪除檔案

若要從語言模型中刪除檔案，按一下文字檔案右側的 **...** 按鈕，然後選取 [刪除]。 這會顯示新的視窗，告訴您刪除無法復原。 按一下新視窗中的 [刪除] 選項。

此動作會從語言模型中完全移除檔案。

## <a name="delete-a-language-model"></a>刪除語言模型

若要從帳戶中刪除語言模型，按一下語言模型右側的 **...** 按鈕，然後選取 [刪除]。

這會顯示新的視窗，告訴您刪除無法復原。 按一下新視窗中的 [刪除] 選項。

此動作會從帳戶中完全移除語言模型。 使用已刪除之語言模型的任何視訊都會保留相同的索引，直到您重新編製視訊索引為止。 如果您重新編製視訊索引，可以將新的語言模型指派給該視訊。 否則，影片索引器將會使用其預設模型重新編製視訊索引。 

## <a name="customize-language-models-by-correcting-transcripts"></a>自訂語言模型，藉由修正文字記錄

影片索引器支援自動自訂語言模型，根據實際的修正使用者對一部影片轉譯。

1. 若要讓文字記錄的更正，開啟您想要編輯從您的帳戶影片的影片。 選取 [**時間軸**] 索引標籤。

    ![自訂語言模型](./media/customize-language-model/timeline.png)
1. 按一下鉛筆圖示以編輯您轉譯的文字記錄。 

    ![自訂語言模型](./media/customize-language-model/edits.png)

    影片索引器會擷取更正您在您的影片轉譯，並將其自動加入一個稱為 從文字記錄編輯 」 文字檔案的所有行。 這些編輯用來重新訓練已用來編製索引這段影片中的特定語言模型。 
    
    如果這段影片中編製索引時，您未指定的語言模型，對這段影片中的所有編輯將會都儲存在呼叫在偵測到的語言中的視訊的帳戶採用的預設語言模型。 
    
    如果多個編輯已對同一行，已更正的一行的最後一個版本將用於更新語言模型。  
    
    > [!NOTE]
    > 只有文字更正適用於自訂。 這表示修正不包含實際的文字 （例如標點符號或空格） 不會包含。 
    
1. 您會看到顯示在內容模型的 [自訂] 頁面的 [語言] 索引標籤的文字記錄更正。

    ![自訂語言模型](./media/customize-language-model/customize.png)

   若要查看針對每個語言模型的 從文字記錄編輯 「 檔案，按一下以開啟它。 

    ![從文字記錄編輯](./media/customize-language-model/from-transcript-edits.png)

## <a name="next-steps"></a>後續步驟

[使用 API 自訂語言模型](customize-language-model-with-api.md)
