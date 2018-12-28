---
title: 使用影片索引器網站自訂人員模型 - Azure
titlesuffix: Azure Media Services
description: 本文說明如何使用影片索引器網站自訂人員模型。
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 12/03/2018
ms.author: anzaman
ms.openlocfilehash: 321db75b1a286e7de1e38ed3b382ee7dc9fe5902
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/12/2018
ms.locfileid: "53283744"
---
# <a name="customize-a-person-model-with-the-video-indexer-website"></a>使用影片索引器網站自訂人員模型

影片索引器支援針對視訊內容進行臉部偵測和名人辨識。 名人辨識功能能根據常被要求的資料來源 (例如 IMDB、維基百科以及 LinkedIn 最有影響力的人)，涵蓋大約 1 百萬個人臉。 名人辨識功能無法辨識的人臉仍會被偵測到，但不會被標註姓名。 將視訊上傳到影片索引器並取回結果之後，您可以返回，並命名無法辨認的人臉。 一旦您以姓名標示某個人臉之後，這張臉和姓名就會新增至您帳戶的人員模型中。 之後，影片索引器便能在未來和過去的視訊中辨識這張臉。

您可以使用影片索引器網站，編輯在視訊中偵測到的人臉，如本主題中所述。 您也可以使用 API，如[使用 API 自訂人員模型](customize-person-model-with-api.md)中所述。

## <a name="edit-a-face"></a>編輯人臉

> [!NOTE]
> 名稱對於人員模型而言是獨一無二的，因此如果您將兩個不同的人臉命名為相同的名稱，影片索引器會將這些人臉視為同一個人，並在您重新編製視訊索引時加以匯整。 如果您看到影片索引器偵測到相同的人臉出現多次，請為它們相提供同的名稱，並重新編製視訊索引。
> 您可以更新影片索引器辨識為使用新名稱之名人的人臉。 您提供的新名稱將會優先於內建的名人辨識。

1. 瀏覽至[影片索引子](https://www.videoindexer.ai/)網站並登入。
2. 在您的帳戶中搜尋您要檢視和編輯的視訊。
3. 若要編輯視訊中的人臉，請前往 [Insights] 索引標籤，然後按一下視窗右上角的鉛筆圖示。

    ![自訂人員模型](./media/customize-face-model/customize-face-model.png)

4. 按一下任何偵測到的人臉，並從 "Unknown #X" (或先前已指派給該人臉的名稱) 變更其名稱。
5. 輸入新名稱之後，按一下新名稱旁邊的核取圖示。 這將會儲存新的名稱，然後在其他目前視訊和您之後上傳的視訊中，辨識所有出現的這張人臉並加以命名。 您其他目前視訊中的臉部辨識可能需要一些時間才會生效，因為這是批次處理。 

    ![儲存更新](./media/customize-face-model/save-update.png)

## <a name="delete-a-face"></a>刪除人臉

若要刪除視訊中偵測到的人臉，請前往 [Insights] 窗格，然後按一下窗格右上角的鉛筆圖示。 按一下人臉名稱底下的 [刪除] 選項。 這將會從視訊中移除偵測到的該張人臉。 在這張人臉出現的其他視訊中仍然會偵測到這張人臉，但是您也可以在編製這些視訊的索引之後，從其中刪除這張人臉。 如果您在刪除這張人臉之前為其命名，則這張人臉也將繼續存在於您帳戶的人員模型中。

## <a name="next-steps"></a>後續步驟

[使用 API 自訂人員模型](customize-person-model-with-api.md)
