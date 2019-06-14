---
title: 使用影片索引器網站自訂品牌模型 - Azure
titlesuffix: Azure Media Services
description: 本文說明如何使用影片索引器網站自訂品牌模型。
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 721fde63aeae8704761b3c21f489dcad77cb89e3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65799604"
---
# <a name="customize-a-brands-model-with-the-video-indexer-website"></a>使用影片索引器網站自訂品牌模型

影片索引器支援在視訊和音訊內容的編製索引及重新編製索引期間，從語音和視覺文字偵測品牌。 品牌偵測功能可識別 Bing 的品牌資料庫建議所提及的產品、服務以及公司。 例如，如果在視訊或音訊內容中提及 Microsoft，或者視訊或音訊內容出現在視訊的視覺文字中，影片索引器會將其偵測為內容中的品牌。 自訂品牌模型可讓您選取影片索引器是否要從 Bing 品牌資料庫偵測品牌、從偵測到的品牌排除特定品牌 (基本上就是建立品牌的封鎖清單)，以及包含應該是模型的一部分但可能不在 Bing 品牌資料庫的品牌 (基本上就是建立品牌的允許清單)。

如需詳細概觀，請參閱[概觀](customize-brands-model-overview.md)。

您可以使用影片索引器網站建立、使用和編輯在視訊中偵測到的自訂品牌模型，如本主題中所述。 您也可以使用 API，如[使用 API 自訂品牌模型](customize-brands-model-with-api.md)中所述。

## <a name="edit-the-settings-of-the-brands-model"></a>編輯品牌模型的設定  

您可以選擇設定是否要從 Bing 品牌資料庫偵測品牌。 因此，您必須編輯品牌模型的設定。

1. 瀏覽至[影片索引子](https://www.videoindexer.ai/)網站並登入。
2. 若要在您的帳戶中自訂模型，按一下頁面右上角的 [內容模型自訂]  按鈕。
 
   ![自訂內容模型](./media/content-model-customization/content-model-customization.png) 
3. 若要編輯品牌，選取 [品牌]  索引標籤。

    ![自訂品牌模型](./media/customize-brand-model/customize-brand-model.png)
4. 如果您希望影片索引器包含 Bing 所建議的品牌，請核取 [顯示 Bing 建議的品牌]  選項。 如果您不希望影片索引器在內容中偵測 Bing 所建議的品牌，請不要核取此選項。 

## <a name="include-brands-in-the-model"></a>包含模型中的品牌

[包含品牌]  區段代表您希望影片索引器偵測的自訂品牌 (即使不是 Bing 建議的品牌)。  

### <a name="add-a-brand"></a>新增品牌

1. 按一下 [+ 新增品牌]。

    ![自訂品牌模型](./media/customize-brand-model/add-brand.png)

    提供名稱 (必要)、類別 (選擇性)、描述 (選擇性) 以及參考 URL (選擇性)。
    [類別] 欄位是用來協助您標記您的品牌。 此欄位會在使用影片索引器 API 時，顯示為品牌的*標記*。 例如，您可以將品牌 "Azure" 標記或分類為「雲端」。

    [參考 URL] 欄位以是品牌的任何參考網站，例如其維基百科頁面的連結。
2. 按一下 [新增品牌]，您將會看到該品牌已新增至 [包含品牌]  清單。

### <a name="edit-a-brand"></a>編輯品牌

1. 按一下您想要編輯之品牌旁的鉛筆圖示。

    您可以更新品牌的類別、描述或參考 URL。 您無法變更品牌的名稱，因為品牌的名稱是獨一無二的。 如果您要變更品牌名稱，請刪除整個品牌 (請參閱下一節)，並以新的名稱建立新的品牌。
2. 按一下 [更新]  按鈕，以新的資訊更新品牌。

### <a name="delete-a-brand"></a>刪除品牌

1. 按一下您想要刪除之品牌旁的垃圾桶圖示。
2. 按一下 [刪除]，品牌將不會再出現在您的 [包含品牌]  清單中。

## <a name="exclude-brands-from-the-model"></a>從模型中排除品牌

[排除品牌]  區段代表您希望影片索引器不要偵測的品牌。

### <a name="add-a-brand"></a>新增品牌

1. 按一下 [+ 新增品牌]。

    提供名稱 (必要)、類別 (選擇性)。
2. 按一下 [新增品牌]，您將會看到該品牌已新增至 [排除品牌]  清單。

### <a name="edit-a-brand"></a>編輯品牌

1. 按一下您想要編輯之品牌旁的鉛筆圖示。

    您只能更新品牌的類別。 您無法變更品牌的名稱，因為品牌的名稱是獨一無二的。 如果您要變更品牌名稱，請刪除整個品牌 (請參閱下一節)，並以新的名稱建立新的品牌。
2. 按一下 [更新]  按鈕，以新的資訊更新品牌。

### <a name="delete-a-brand"></a>刪除品牌

1. 按一下您想要刪除之品牌旁的垃圾桶圖示。
2. 按一下 [刪除]，品牌將不會再出現在您的 [排除品牌]  清單中。

## <a name="next-steps"></a>後續步驟

[使用 API 自訂品牌模型](customize-brands-model-with-api.md)
