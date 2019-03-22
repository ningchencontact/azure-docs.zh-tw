---
title: Azure 媒體服務 v3 常見問題集 | Microsoft Docs
description: 本文提供 Azure 媒體服務 v3 常見問題集的解答。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/20/2019
ms.author: juliako
ms.openlocfilehash: 5b49db8d7e8360837dc209e98123eeccd5542769
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "57837740"
---
# <a name="azure-media-services-v3-frequently-asked-questions"></a>Azure 媒體服務 v3 常見問題集

本文提供 Azure 媒體服務 (AMS) v3 常見問題集的解答。

## <a name="v3-apis"></a>v3 API

### <a name="how-do-i-configure-media-reserved-units"></a>如何設定編碼保留單元？

針對由媒體服務 v3 或影片索引子觸發的音訊分析和影片分析作業，強烈建議您使用 10 個 S3 MRU 佈建帳戶。 如果您需要 10 個以上的 S3 MRU，請使用 [Azure 入口網站](https://portal.azure.com/)開立支援票證。

如需詳細資訊，請參閱[使用 CLI 調整媒體處理的規模](media-reserved-units-cli-how-to.md)。

### <a name="what-is-the-recommended-method-to-process-videos"></a>建議用來處理影片的方法為何？

使用[轉換](https://docs.microsoft.com/rest/api/media/transforms)可設定視訊編碼或分析的一般工作。 每個**轉換**都會描述配方或工作流程，以便處理您的視訊或音訊檔案。 A[作業](https://docs.microsoft.com/rest/api/media/jobs)是要套用到媒體服務的實際要求**轉換**為給定的輸入視訊或音訊內容。 一旦建立轉換，您就可以使用媒體服務 API 或使用任何已發佈的 SDK 提交作業。 如需詳細資訊，請參閱[轉換和作業](transforms-jobs-concept.md)。

### <a name="how-does-pagination-work"></a>分頁的運作方式為何？

使用分頁時，您應一律使用下一頁連結來列舉集合，而不應依存於特定頁面大小。 如需詳細資訊和範例，請參閱[篩選、排序、分頁](entities-overview.md)。

## <a name="live-streaming"></a>即時串流 

###  <a name="how-to-insert-breaksvideos-and-image-slates-during-live-stream"></a>如何在即時資料流進行期間插入中斷/影片和影像靜態圖像？

媒體服務 v3 即時編碼尚未支援在即時資料流進行期間插入影片或影像靜態圖像。 

您可以使用[即時內部部署編碼器](recommended-on-premises-live-encoders.md)來切換來源影片。 許多應用程式都可讓您切換來源，包括 Telestream Wirecast、Switcher Studio (在 iOS 上)、OBS Studio (免費應用程式) 等等。

## <a name="content-protection"></a>內容保護

### <a name="how-and-where-to-get-jwt-token-before-using-it-to-request-license-or-key"></a>如何以及在何處取得 JWT 權杖，再用來要求授權或金鑰？

1. 用於生產時，您需要有 Security Token Service (STS) (Web 服務)，才能根據 HTTPS 要求發出 JWT 權杖。 用於測試時，您可以使用 [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs) 中定義的 **GetTokenAsync** 方法所示的程式碼。
2. 播放器必須在使用者經過驗證之後，向 STS 提出此類權杖的要求，並將它指派為權杖的值。 您可以使用 [Azure 媒體播放器 API](https://amp.azure.net/libs/amp/latest/docs/)。

* 如需執行 STS (使用對稱和非對稱金鑰) 的範例，請參閱 [https://aka.ms/jwt](https://aka.ms/jwt)。 
* 如需以使用這類 JWT 權杖的 Azure 媒體播放器為基礎的播放器範例，請參閱 [https://aka.ms/amtest](https://aka.ms/amtest) (展開 "player_settings" 連結以查看權杖輸入)。

### <a name="how-do-you-authorize-requests-to-stream-videos-with-aes-encryption"></a>如何授權要求以使用 AES 加密來串流處理影片？

正確的方法是利用 STS (Secure Token Service)：

在 STS 中，根據使用者設定檔，新增不同的宣告 (例如「進階使用者」、「基本使用者」、「免費試用使用者」)。 在 JWT 中使用不同的宣告，使用者可以看見不同的內容。 當然，對於不同的內容/資產，ContentKeyPolicyRestriction 會有對應的 RequiredClaims。

使用 Azure 媒體服務 Api 來設定授權/金鑰傳遞和加密您的資產 (如中所示[本例](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs))。

如需詳細資訊，請參閱

- [內容保護概觀](content-protection-overview.md)
- [設計具有存取控制的多重 DRM 內容保護系統](design-multi-drm-system-with-access-control.md)

## <a name="media-services-v2-vs-v3"></a>媒體服務 v2 和 v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>是否可以使用 Azure 入口網站來管理 v3 資源？

尚未提供。 您可以使用其中一個受支援的 SDK。 請參閱此文件集內的教學課程和範例。

### <a name="is-there-an-assetfile-concept-in-v3"></a>v3 中是否有 AssetFile 概念？

AssetFile 已從 AMS API 中移除，以便讓媒體服務與儲存體 SDK 相依性分開。 現在是由儲存體 (而非媒體服務) 保留屬於儲存體的資訊。 

如需詳細資訊，請參閱[遷移至媒體服務 v3](migrate-from-v2-to-v3.md)。

### <a name="where-did-client-side-storage-encryption-go"></a>用戶端儲存體加密怎麼不見了？

現在會建議您使用伺服器端儲存體加密 (預設為開啟)。 如需詳細資訊，請參閱[待用資料的 Azure 儲存體服務加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)。

## <a name="next-steps"></a>後續步驟

[媒體服務 v3 概觀](media-services-overview.md)
