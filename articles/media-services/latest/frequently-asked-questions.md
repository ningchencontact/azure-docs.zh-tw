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
ms.openlocfilehash: be4c08bc31c8811655230ab89b48271f4c2b3164
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/06/2019
ms.locfileid: "55756575"
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

媒體服務可對支援 OData 的資源支援 $top，但傳遞至 $top 的值必須小於 1000 (例如，分頁的頁面大小)。

這可讓您取得使用 $top 的小型項目範例 (例如，100 個最新項目)，或使用分頁功能逐頁查看所有項目。 

媒體服務不支援以使用者指定的頁面大小逐頁查看資料。

如需詳細資訊，請參閱[篩選、排序、分頁](entities-overview.md)。

### <a name="how-to-retrieve-an-entity-in-media-services-v3"></a>如何擷取媒體服務 v3 中的實體？

v3 是以統一 API 表面為基礎的，會公開建置於 **Azure Resource Manager** 上的管理和操作功能。 根據 **Azure Resource Manager**，資源名稱永遠是唯一的。 因此，您可以對資源使用任何唯一識別碼字串 (例如，GUID)。

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
