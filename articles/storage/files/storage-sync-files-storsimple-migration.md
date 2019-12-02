---
title: 從 StorSimple 遷移至 Azure 檔案同步
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: fauhse
ms.subservice: files
description: StorSimple 是生命週期結束產品，Azure 檔案同步是要遷移至的解決方案。 瞭解遷移概念，並與自訂的遷移說明 AzureFiles@microsoft.com。
ms.openlocfilehash: edad4d1c6be2c39dbf8150b6ab8979ae3924fb53
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2019
ms.locfileid: "74666659"
---
# <a name="storsimple-migration-to-azure-file-sync"></a>StorSimple 遷移至 Azure 檔案同步

StorSimple 是已終止的 Microsoft 產品。 本產品和其雲端服務的延伸支援將持續到2022結束為止。
請務必立即開始規劃從 StorSimple 進行遷移。

預設和策略性長期的 Azure 服務 StorSimple 設備可以遷移至，Azure 檔案同步。這是一項已正式運作的 Azure 服務，具有透過 StorSimple 的一組功能。

## <a name="full-cloud-side-migration-with-limited-downtime"></a>以有限停機時間進行完整的雲端端遷移
本文將說明如何開始進行遷移的概念。
請務必注意，需要從 StorSimple 和 Azure 檔案同步遷移至的客戶不需要自行進行。

> [!IMPORTANT]
> Microsoft 致力於協助客戶進行遷移。 在遷移期間，針對自訂的遷移計畫和協助提供電子郵件 AzureFiles@microsoft.com。

## <a name="migration-approach"></a>遷移方法
遷移至 Azure 檔案同步將會開始雲端端，對內部部署的影響降至最低，而且停機時間有限。
下列概念是以 StorSimple 8000 系列設備為目標。
如果您需要從 StorSimple 7000 系列進行遷移，第一個步驟必須從 Microsoft 免費升級至相符的8000系列租借裝置。
AzureFiles@microsoft.com，我們將協助您組織適當數目的租借裝置。

### <a name="general-approach"></a>一般方法
![同時](media/storage-sync-files-storsimple-migration/storsimple-docs-overview-concept.png "說明如何透過暫時的虛擬裝置和 Windows 伺服器，將雲端端遷移至新的內部部署 Windows Server，以取代內部部署 StorSimple 應用裝置")

1. 建立內部部署 StorSimple 應用裝置的磁片區複製，並將其掛接至暫存的 StorSimple 虛擬裝置。
2. 透過 iSCSI 將虛擬裝置連線至暫存 Azure VM。
3. 在暫存的 Windows Server VM 上安裝 Azure 檔案同步-此遷移的特定登錄機碼也必須在伺服器上設定同步處理之前，先進行設定。
    * 視 StorSimple 磁片區上的共用數目而定，部署的 Azure 檔案共用數量就多了。 （建議您為每個儲存體帳戶部署一個 Azure 檔案共用。）
    * 設定 Windows Server 雲端 VM 上的個別共用與 Azure 檔案共用之間的同步處理。 （1:1 對應）
    * （選擇性）在啟用雲端階層處理的情況下，將本機伺服器新增為內部部署效能快取。 如果您想要以更豐富的本機快取來取代您的內部部署 StorSimple，而這是由 Windows Server 和 Azure 檔案同步的雲端階層處理所提供的功能，則這是必要步驟。 您的內部部署 Windows Server 可以是實體機器或叢集，或虛擬機器。 它不一定要有部署為資料集大小的儲存量。 它只需要足夠的儲存空間，即可在本機快取最常存取的檔案。

## <a name="minimizing-downtime"></a>將停機時間降到最低
在上述步驟3之後，使用者和應用程式仍會積極使用 StorSimple 內部部署設備。 因此，在同步處理完成時，從初始磁片區複製同步處理的檔案集合會稍微過時。
將停機時間降到最低的方法是，從磁片區複製程式重複同步處理，如此一來，當磁片區複製間的變更變得較少且較少時，就能更快速且更快速地完成同步。
您可以重複此程式，直到磁片區複製的同步處理在您發現停機時間可接受的時間量完成為止。
一旦發生這種情況，請封鎖使用者和應用程式，使其無法對您的 StorSimple 應用裝置進行任何變更。 停機時間開始。
建立另一個磁片區複製，並讓它同步處理到已連線的伺服器。
將使用者和應用程式的存取權，建立到新的、Azure 檔案同步的 Windows Server。
請考慮部署/調整 DFS 命名空間，以從舊的 StorSimple 應用裝置到應用程式和使用者透明化的新 Windows Server。
您的遷移已完成。

## <a name="migration-goal"></a>遷移目標
完成遷移之後，即可取消布建暫存的 StorSimple 虛擬裝置和 Azure VM。

此外，StorSimple 內部部署設備可以取消布建，因為您的使用者和應用程式已改為存取 Windows Server。
下圖說明您的剩餘內容。 標準 Azure 檔案同步部署會透過 Azure 檔案同步，為許多 Azure 檔案共用和連線到它們的 Windows 伺服器提供功能。請記住，單一伺服器可以同時將不同的本機資料夾連接到不同的檔案共用。
此外，一個 Azure 檔案共用可以同步到許多不同的伺服器，在此情況下，您需要在分公司中快取資料。 此外，請檢查您是否可以優化雲端階層處理原則，以更有效率地使用內部部署儲存空間。

![同時](media/storage-sync-files-storsimple-migration/storsimple-docs-goal.PNG "此圖顯示完成遷移之後的目標。它描述了一些檔案共用，可同步處理至內部部署 Windows 伺服器，而使用者和應用程式會存取雲端或 Windows Server 上的檔案。")

## <a name="next-steps"></a>後續步驟
熟悉 Azure 檔案儲存體和 Azure 檔案同步。請務必瞭解成功遷移的 Azure 檔案同步術語和部署模式。 此總覽文章中的每個步驟都有更詳細的資訊可供使用。 在規劃和執行您的遷移期間，請務必與 Microsoft 聯繫以取得自訂的協助。

> [!IMPORTANT]
> Microsoft 致力於協助客戶進行遷移。 在遷移期間，針對自訂的遷移計畫和協助提供電子郵件 AzureFiles@microsoft.com。

## <a name="additional-resources"></a>其他資源
如目標服務 Azure 檔案同步，如果您不熟悉 Azure 檔案同步，則會有兩個我們建議您閱讀的基本檔。
* [Azure 檔案同步-總覽](storage-sync-files-planning.md)
* [Azure 檔案同步-部署指南](storage-sync-files-deployment-guide.md)

Azure 檔案儲存體是 Azure 中的儲存體服務，以服務的形式提供檔案共用。 不需要支付或維護 VM 或相關聯的 VM 儲存體。
* [Azure 檔案儲存體-總覽](storage-files-introduction.md)
* [Azure 檔案儲存體-如何部署 Azure 檔案共用](storage-how-to-create-file-share.md)