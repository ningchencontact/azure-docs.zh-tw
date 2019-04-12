---
title: Azure 檔案服務延展性和效能目標 | Microsoft Docs
description: 了解 Azure 檔案服務的延展性和效能目標，包括容量、要求率以及輸入和輸出頻寬限制。
services: storage
author: wmgries
ms.service: storage
ms.topic: article
ms.date: 7/19/2018
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 630ad1e0558fc089d79eee50175e497b771a0a8a
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/11/2019
ms.locfileid: "59494964"
---
# <a name="azure-files-scalability-and-performance-targets"></a>Azure 檔案服務延展性和效能目標

[Azure 檔案服務](storage-files-introduction.md)可提供在雲端中完全受控的檔案共用，可透過業界標準 SMB 通訊協定加以存取。 本文討論 Azure 檔案服務和 Azure 檔案同步的延展性和效能目標。

此處所列的延展性和效能目標是高階的目標，但可能會受到部署中的其他變數影響。 例如，檔案的輸送量可能也受限於可用的網路頻寬，而不只是裝載 Azure 檔案服務的伺服器。 我們強烈建議您測試您的使用模式，以判斷 Azure 檔案服務的延展性和效能是否符合您的需求。 我們也保證會隨時間提高這些限制。 歡迎您在底下留言或前往 [Azure 檔案服務 UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) \(英文\)，提供想要我們提高哪些限制的意見反應。

## <a name="azure-storage-account-scale-targets"></a>Azure 儲存體帳戶擴展目標

Azure 檔案共用的父資源是 Azure 儲存體帳戶。 儲存體帳戶代表 Azure 中可供多個儲存體服務 (包括 Azure 檔案服務) 儲存資料的儲存體集區。 將資料儲存在儲存體帳戶的其他服務有 Azure Blob 儲存體、Azure 佇列儲存體和 Azure 資料表儲存體。 下列目標適用於在儲存體帳戶中儲存資料的所有儲存體服務：

[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

> [!Important]  
> 從其他儲存體服務的一般用途儲存體帳戶使用量會影響您的 Azure 檔案共用，您的儲存體帳戶中。 比方說，如果達到 Azure Blob 儲存體的最大儲存體帳戶容量，您將無法在 Azure 檔案共用上建立新檔案，即使您的 Azure 檔案共用低於最大共用大小也一樣。

## <a name="azure-files-scale-targets"></a>Azure 檔案擴展目標

### <a name="premium-files-scale-targets"></a>進階檔案調整目標

有三種類別的限制時應考量的進階檔案： 儲存體帳戶、 共用和檔案。

例如︰單一共用可達到 100,000 IOPS 和單一檔案可以調整最多 5,000 個 IOPS。 因此，比方說，如果您有一個共用中的三個檔案，您可以從共用取得的最大 IOPS 是 15000。

### <a name="premium-filestorage-account-limits"></a>進階 filestorage 帳戶限制

進階檔案會使用名為唯一的儲存體帳戶**filestorage （預覽）**，此帳戶具有比標準的檔案所使用的儲存體帳戶的稍有不同的擴展目標。 儲存體帳戶擴展目標，請參閱中的資料表[Azure 儲存體帳戶擴展目標](#azure-storage-account-scale-targets)一節。

> [!IMPORTANT]
> 儲存體帳戶限制套用到所有的共用。 最多調整儲存體帳戶的最大值才可達成，如果沒有每個儲存體帳戶只能有一個共用。

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

## <a name="azure-file-sync-scale-targets"></a>Azure 檔案同步擴展目標

藉由 Azure 檔案同步，我們已儘可能嘗試設計沒有限制的使用方式，但並非總是可行。 下表指出我們測試的界限，及哪些目標實際上是固定限制：

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

### <a name="azure-file-sync-performance-metrics"></a>Azure 檔案同步效能計量

由於 Azure 檔案同步代理程式在連線到 Azure 檔案共用的 Windows Server 電腦上執行，有效的同步處理效能取決於基礎結構中的許多因素：Windows Server 和基礎結構磁碟設定、伺服器與 Azure 儲存體之間的網路頻寬、檔案大小、總資料集大小和資料集上的活動。 由於 Azure 檔案同步會在檔案層級上運作，因此 Azure 檔案同步解決方案的效能特性應以每秒處理的物件 (檔案和目錄) 數來測量，以獲得較精準的結果。

在下列兩個階段中，Azure 檔案同步必須達到高效能：

1. **初始一次性佈建**：若要讓初始佈建達到最佳效能，請參閱[透過 Azure 檔案同步上架](storage-sync-files-deployment-guide.md#onboarding-with-azure-file-sync)以取得最佳部署的詳細資料。
2. **持續同步**：在 Azure 檔案共用中初次植入資料之後，Azure 檔案同步會將多個端點保持在同步狀態。

為了協助您規劃每個階段的部署，以下提供在採用某種組態的系統上進行內部測試期間所觀察到的結果

| 系統組態 |  |
|-|-|
| CPU | 具有 64 MiB L3 快取的 64 個虛擬核心 |
| 記憶體 | 128 GB |
| 磁碟 | 具有電池供電式快取記憶體 RAID 10 的 SAS 磁碟 |
| 網路 | 1 Gbps 的網路 |
| 工作負載 | 一般用途檔案伺服器|

| 初始一次性佈建  |  |
|-|-|
| 物件數目 | 2500 萬個物件 |
| 資料集大小| ~4.7 TiB |
| 平均檔案大小 | ~ 200 KiB (最大的檔案：100 GiB) |
| 上傳輸送量 | 每秒 20 個物件 |
| 命名空間下載輸送量* | 每秒 400 個物件 |

*在建立新的伺服器端點時，Azure 檔案同步代理程式不會下載任何檔案內容。 它會先同步完整命名空間，然後再觸發背景回復以下載檔案；有可能是下載完整檔案，或者，如果已啟用雲端分層處理，則會根據伺服器端點上設定的雲端分層處理原則進行下載。

| 持續同步  |   |
|-|--|
| 已同步的物件數目| 125,000 個物件 (~1% 變換) |
| 資料集大小| 50 GiB |
| 平均檔案大小 | ~500 KiB |
| 上傳輸送量 | 每秒 30 個物件 |
| 完整下載輸送量* | 每秒 60 個物件 |

*如果雲端分層處理已啟用，您應該會發現效能有所提升，因為只會下載部分檔案資料。 只有在任何端點上的快取檔案資料有所變更時，Azure 檔案同步才會下載這些資料。 對於任何分層或新建的檔案，代理程式並不會下載檔案資料，而只會將命名空間同步至所有伺服器端點。 代理程式也支援在使用者存取分層的檔案時進行檔案的部分下載。 

> [!Note]  
> 上述數字不代表您將實際體驗到的效能。 如本節開頭所述，實際效能將取決於多項因素。

以下提供部署的一般指南，有幾件事您應謹記在心：

- 物件輸送量的消長大致上會與伺服器上的同步群組數目成正比。 在伺服器上將資料分割到多個同步群組時，會產生較佳的輸送量，但仍受限於伺服器和網路。
- 物件輸送量與每秒 MiB 輸送量成反比。 檔案較小時，在每秒處理的物件數方面會呈現較高的輸送量，但每秒的 MiB 輸送量則會降低。 相反地，若檔案較大，每秒處理的物件數將會降低，但每秒的 MiB 輸送量則會提高。 每秒的 MiB 輸送量會受限於 Azure 檔案擴展目標。

## <a name="see-also"></a>請參閱

- [規劃 Azure 檔案服務部署](storage-files-planning.md)
- [規劃 Azure 檔案同步部署](storage-sync-files-planning.md)
- [其他儲存體服務的延展性和效能目標](../common/storage-scalability-targets.md)
