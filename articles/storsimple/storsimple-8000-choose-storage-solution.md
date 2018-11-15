---
title: 使用設備針對 Azure 進行資料傳輸的選項 | Microsoft Docs
description: 了解如何選擇正確的設備以將資料傳輸至 Azure
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 11/01/2018
ms.author: alkohli
ms.openlocfilehash: 0cb1a0bccbb989506988f36c515d59cddb832265
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2018
ms.locfileid: "51263393"
---
# <a name="compare-storsimple-with-azure-file-sync-and-data-box-edge-data-transfer-options"></a>比較 StorSimple、Azure 檔案同步及 Data Box Edge 資料傳輸選項 
 
本文能提供內部部署資料傳輸至 Azure 之選項的概觀，並會比較 Data Box Edge、Azure 檔案同步 (AFS)，以及 StorSimple 8000 系列之間的差異。

- **[Data Box Edge](/azure/databox-online/data-box-edge-overview.md)**：Data Box Edge 是內部部署網路設備，可將資料移入和移出 Azure，並具有能在上傳期間預先處理資料的 AI 功能 Edge 計算能力。 此裝置已在 Ignite 2018 宣布，並處於公開預覽狀態。 「資料箱閘道」是該裝置的虛擬版本，並具備相同的資料傳輸功能。
- **[Azure 檔案同步](/azure/storage/files/storage-sync-files-deployment-guide.md)**：您可使用 Azure 檔案同步將組織的檔案共用集中在 Azure 檔案服務中，同時保有內部部署檔案伺服器的靈活度、效能及相容性。 Azure 檔案同步會將 Windows Server 轉換成 Azure 檔案共用的快速快取。 AFS 正式推出的消息已於 2018 前半年宣布。
- **[StorSimple](/azure/storsimple/storsimple-overview.md)**：StorSimple 是混合式裝置，可透過與 Azure 儲存體緊密整合，協助企業合併其儲存體基礎結構，以在單一解決方案上取得主要儲存體、資料保護、封存，以及災害復原功能。 如需 StorSimple 的產品生命週期，請造訪[這裡](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series)。

## <a name="comparison-summary"></a>比較摘要

|                           |StorSimple 8000   |Azure 檔案同步   |Data Box Edge (預覽)           |
|---------------------------|----------------------------------------|-------------------------------|-----------------------------------------|
|概觀         |分層式的混合式儲存體及封存|一般檔案伺服器儲存體，具備雲端階層和多站台同步。  |儲存體解決方案以預先處理資料，並將它透過網路傳送至 Azure。        |
|案例        |檔案伺服器、封存、備份目標 |檔案伺服器、封存 (多站台)   |資料傳輸、資料預先處理 (包括 ML 推斷)、IoT、封存    |
|Edge 計算     |尚未提供 |尚未提供 |支援使用 Azure IoT Edge 執行容器    |
|板型規格      |實體裝置   |安裝在 Windows Server 上的代理程式 |實體裝置   |
|硬體         |由 Microsoft 作為服務的一部分提供的實體裝置 | 由客戶提供 |由 Microsoft 作為服務的一部分提供的實體裝置  |
|資料格式      |自訂格式   |檔案         |Blob 或檔案    |
|通訊協定支援 |iSCSI          |SMB、NFS    | SMB 或 NFS      |
|價格          |[StorSimple 定價](https://azure.microsoft.com/pricing/details/storsimple/) |[AFS 定價](https://azure.microsoft.com/pricing/details/storage/files/)  |[Data Box Edge 定價](https://azure.microsoft.com/pricing/details/storage/databox/edge/)  |

## <a name="next-steps"></a>後續步驟

- 了解 [Azure Data Box Edge](/azure/databox-online/data-box-edge-overview.md) 和 [Azure 資料箱閘道](/azure/databox-online/data-box-gateway-overview.md)
- 了解 [Azure 檔案同步](/azure/storage/files/storage-sync-files-deployment-guide.md)