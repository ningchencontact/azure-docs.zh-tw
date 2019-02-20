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
ms.date: 02/05/2019
ms.author: juliako
ms.openlocfilehash: a447c359c38c2173ea42b6d717067fc8b3a88f9a
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55875486"
---
# <a name="azure-media-services-v3-frequently-asked-questions"></a>Azure 媒體服務 v3 常見問題集

本文提供 Azure 媒體服務 (AMS) v3 常見問題集的解答。

## <a name="v3-apis"></a>v3 API

### <a name="how-do-i-configure-media-reserved-units"></a>如何設定編碼保留單元？

針對由媒體服務 v3 或影片索引子觸發的音訊分析和影片分析作業，強烈建議您使用 10 個 S3 MRU 佈建帳戶。 如果您需要 10 個以上的 S3 MRU，請使用 [Azure 入口網站](https://portal.azure.com/)開立支援票證。

如需詳細資訊，請參閱[使用 CLI 調整媒體處理的規模](media-reserved-units-cli-how-to.md)。

### <a name="what-is-the-recommended-method-to-process-videos"></a>建議用來處理影片的方法為何？

建議您使用指向影片的 HTTP(s) URL 來提交作業。 如需詳細資訊，請參閱 [HTTP(s) 內嵌](job-input-from-http-how-to.md)。 您不需要建立具有輸入影片的資產就能對資產進行處理。

### <a name="how-does-pagination-work"></a>分頁的運作方式為何？

使用分頁時，您應一律使用下一頁連結來列舉集合，而不應依存於特定頁面大小。 如需詳細資訊和範例，請參閱[篩選、排序、分頁](entities-overview.md)。

## <a name="live-streaming"></a>即時串流 

###  <a name="how-to-insert-breaksvideos-and-image-slates-during-live-stream"></a>如何在即時資料流進行期間插入中斷/影片和影像靜態圖像？

媒體服務 v3 即時編碼尚未支援在即時資料流進行期間插入影片或影像靜態圖像。 

您可以使用[即時內部部署編碼器](recommended-on-premises-live-encoders.md)來切換來源影片。 許多應用程式都可讓您切換來源，包括 Telestream Wirecast、Switcher Studio (在 iOS 上)、OBS Studio (免費應用程式) 等等。

## <a name="media-services-v2-vs-v3"></a>媒體服務 v2 和 v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>是否可以使用 Azure 入口網站來管理 v3 資源？

尚未提供。 您可以使用其中一個受支援的 SDK。 請參閱此文件集內的教學課程和範例。

### <a name="is-there-an-assetfile-concept-in-v3"></a>v3 中是否有 AssetFile 概念？

AssetFile 已從 AMS API 中移除，以便讓媒體服務與儲存體 SDK 相依性分開。 現在是由儲存體 (而非媒體服務) 保留屬於儲存體的資訊。 

如需詳細資訊，請參閱[遷移至媒體服務 v3](migrate-from-v2-to-v3.md)。

### <a name="where-did-client-side-storage-encryption-go"></a>用戶端儲存體加密怎麼不見了？

現在會建議您使用伺服器端儲存體加密 (預設為開啟)。 如需詳細資訊，請參閱[待用資料的 Azure 儲存體服務加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [媒體服務 v3 概觀](media-services-overview.md)
