---
title: 使用影片索引子的動畫字元偵測
titleSuffix: Azure Media Services
description: 本主題示範如何使用影片索引子的動畫字元偵測。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/05/2019
ms.author: juliako
ms.openlocfilehash: ac31a2945e59df3b57000a0ffdcbca8344e44c18
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/19/2019
ms.locfileid: "71122307"
---
# <a name="animated-character-detection-preview"></a>動畫字元偵測（預覽）

Azure 媒體服務影片索引子可透過與[認知服務自訂視覺](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/)的整合，支援在動畫內容中偵測、分組和辨識字元。 這項功能可透過入口網站和 API 取得。

使用特定的動畫模型上傳動畫影片之後，影片索引子會抽取主要畫面格、在這些框架中偵測動畫字元、將類似的字元分組，以及選擇最佳的範例。 然後，它會將分組的字元傳送至自訂視覺，以根據所定型的模型來識別字元。 

開始訓練模型之前，namelessly 會偵測到這些字元。 當您新增名稱並訓練模型時，影片索引子會辨識這些字元，並據以命名。

## <a name="flow-diagram"></a>流程圖

下圖將示範動畫字元偵測程式的流程。

![流程圖](./media/animated-characters-recognition/flow.png)

## <a name="accounts"></a>帳戶

視您的影片索引子帳戶類型而定，有不同的功能集可供使用。 如需如何將您的帳戶連接到 Azure 的相關資訊，請參閱[建立連線到 azure 的影片索引子帳戶](connect-to-azure.md)。

* 試用帳戶：影片索引子會使用內部自訂視覺帳戶來建立模型，並將它連接到您的影片索引子帳戶。 
* 付費帳戶：您可以將自訂視覺帳戶連線到您的影片索引子帳戶（如果您還沒有帳戶，則必須先建立帳戶）。

### <a name="trial-vs-paid"></a>試用版與付費

|功能|試用版|付費|
|---|---|---|
|自訂視覺帳戶|由影片索引子在幕後進行管理。 |您的自訂視覺帳戶已連線到影片索引子。|
|動畫模型的數目|一個|每個帳戶最多100個模型（自訂視覺限制）。|
|定型模型|影片索引子會為新字元定型模型，以取得現有字元的其他範例。|帳戶擁有者可以在準備好進行變更時，訓練模型。|
|自訂視覺中的 Advanced 選項|沒有自訂視覺入口網站的存取權。|您可以在自訂視覺入口網站中自行調整模型。|

## <a name="use-the-animated-character-detection-with-portal"></a>使用入口網站的動畫字元偵測 

本節說明開始使用動畫字元偵測模型所需採取的步驟。

### <a name="connect-your-custom-vision-account-paid-accounts-only"></a>連接您的自訂視覺帳戶（僅限付費帳戶）

如果您擁有影片索引子付費帳戶，您必須先連接自訂視覺帳戶。 如果您還沒有自訂視覺帳戶，請建立一個。 如需詳細資訊，請參閱[自訂視覺](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/home)。

### <a name="create-an-animated-characters-model"></a>建立動畫字元模型

1. 瀏覽至[影片索引子](https://vi.microsoft.com/)網站並登入。
1. 按一下頁面右上角的 [內容模型自訂] 按鈕。

    ![內容模型自訂](./media/animated-characters-recognition/content-model-customization.png)
1. 移至 [模型自訂] 區段中的 [**動畫字元**] 索引標籤。
1. 按一下 [**加入模型**]。
1. 命名您的模型，然後按一下 enter 來儲存名稱。

> [!NOTE]
> 最佳做法是為每個動畫數列各有一個自訂視覺模型。 

### <a name="index-a-video-with-an-animated-model"></a>使用動畫模型為影片編制索引

1. 按一下頂端功能表中的 [上**傳**] 按鈕。
1. 選擇要上傳的影片（從檔案或 URL）。
1. 按一下 [ **Advanced options**]。
1. 在 [**人員/動畫字元**] 底下選擇 [**動畫模型**]。
1. 如果您有一個模型，就會自動選擇它，如果您有多個模型，可以選擇下拉式功能表中的相關一個。
1. 按一下 [上傳]。
1. 建立影片的索引後，您會在 [**深入**解析] 窗格的 [**動畫字元**] 區段中看到偵測到的字元。

> [!NOTE] 
> 在標記和定型模型之前，所有的動畫字元都會命名為「未知的 #X」。 在您將模型定型之後，也會一併辨識。

### <a name="customize-the-animated-characters-models"></a>自訂動畫字元模型

1. 標記和定型模型。

    1. 藉由編輯偵測到的字元名稱來標記。 一旦將一個字元定型到模型中，就會辨識出下一個使用該模型編制索引的影片。 
    1. 若要在影片中標記動畫字元，請移至 [**深入**解析] 索引標籤，然後按一下視窗右上角的 [**編輯**] 按鈕。
    1. 在 [**深入**解析] 窗格中，按一下任何偵測到的動畫字元，並將其名稱從「未知的 #X」（或先前指派給該字元的名稱）變更。
    1. 輸入新名稱之後，按一下新名稱旁邊的核取圖示。 這會將新名稱儲存在影片索引子的模型中。
    1. 完成所有想要的名稱編輯之後，您必須將模型定型。

        開啟 [自訂] 頁面，然後按一下 [**動畫字元**] 索引標籤。如果您有付費帳戶，您可以按一下 [**管理客戶願景中的模型**] 連結（如下所示）。 然後您會在自訂視覺中，將您轉送到模型的頁面。 按一下 [**訓練**] 按鈕以訓練您的模型。 
        
        ![內容模型自訂](./media/animated-characters-recognition/content-model-customization-tab.png)

        如果您有試用帳戶，將不會顯示 [**在客戶視覺中管理模型**] 連結。 在此情況下，您可以直接在**內容模型自訂**頁面上按 [**定型**]。
    1. 定型之後，任何會使用該模型編制索引或重新建立索引的影片都會辨識已定型的字元。 
    可存取其自訂視覺帳戶的付費帳戶，可以在該處看到模型和已標記的影像。 深入瞭解如何[在自訂視覺中改善您的分類器](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-improving-your-classifier)。

1. 刪除動畫字元。

    1. 若要刪除影片深入解析中的動畫字元，請移至 [**深入**解析] 索引標籤，然後按一下視窗右上角的 [**編輯**] 按鈕。
    1. 選擇動畫字元，然後按一下其名稱底下的 [**刪除**] 按鈕。

    > [!NOTE]
    > 這會刪除這部影片的深入解析，但不會影響模型。

1. 刪除模型。

    1. 按一下頂端功能表上的 [**內容模型自訂**] 按鈕，然後移至 [**動畫字元**] 索引標籤。
    1. 按一下您想要刪除之模型右邊的省略號圖示，然後在 [刪除] 按鈕上。
    
    * 付費帳戶：此模型將與影片索引子中斷連線，而且您將無法重新連接。
    * 試用帳戶：此模型也會從海關願景中刪除。 
    
        > [!NOTE]
        > 在試用帳戶中，您只有一個可供使用的模型。 刪除之後，就無法訓練其他模型。

## <a name="use-the-animated-character-detection-with-api"></a>使用 API 的動畫字元偵測 

1. 連接自訂視覺帳戶。

    如果您擁有影片索引子付費帳戶，您必須先連接自訂視覺帳戶。 <br/>
    如果您還沒有自訂視覺帳戶，請建立一個。 如需詳細資訊，請參閱[自訂視覺](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/home)。

    [使用 API 連接您的自訂視覺帳戶](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Connect-Custom-Vision-Account?tags=&pattern=&groupBy=tag)。
1. 建立動畫字元模型。

    使用[建立動畫模型](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Animation-Model?&groupBy=tag)API。
1. 為影片編制索引或重新編制索引。

    使用[重新編制索引](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?)API。 
1. 自訂動畫字元模型。

    使用[訓練動畫模型](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Train-Animation-Model?&groupBy=tag)API。

### <a name="view-the-output"></a>檢視輸出

查看所產生 JSON 檔案中的動畫字元。

```json
"animatedCharacters": [
    {
    "videoId": "e867214582",
    "confidence": 0,
    "thumbnailId": "00000000-0000-0000-0000-000000000000",
    "seenDuration": 201.5,
    "seenDurationRatio": 0.3175,
    "isKnownCharacter": true,
    "id": 4,
    "name": "Bunny",
    "appearances": [
        {
            "startTime": "0:00:52.333",
            "endTime": "0:02:02.6",
            "startSeconds": 52.3,
            "endSeconds": 122.6
        },
        {
            "startTime": "0:02:40.633",
            "endTime": "0:03:16.6",
            "startSeconds": 160.6,
            "endSeconds": 196.6
        },
    ]
    },
]
```

## <a name="limitations"></a>限制

* 目前，東亞地區不支援「動畫識別」功能。
* 如果影片的品質不佳，則可能無法正確識別在影片中顯示為小型或遠的字元。
* 建議使用每一組動畫字元的模型（例如，每個動畫的數列）。

## <a name="next-steps"></a>後續步驟

[影片索引子概觀](video-indexer-overview.md)
