---
title: 使用資料箱和其他方法進行離線內嵌至 Azure 檔案同步。
description: 啟用同步相容大量移轉支援的程序和最佳做法。
services: storage
author: fauhse
ms.service: storage
ms.topic: article
ms.date: 02/12/2019
ms.author: fauhse
ms.component: files
ms.openlocfilehash: a184e0563d1ad26671c38cabe07f42d97cbe2885
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56212445"
---
# <a name="migrate-to-azure-file-sync"></a>遷移至 Azure 檔案同步
有數個選項可移至 Azure 檔案同步：

### <a name="uploading-files-via-azure-file-sync"></a>透過 Azure 檔案同步上傳檔案
最簡單的選項是在本機將檔案移至 Windows Server 2012 R2 或更新版本，並安裝 Azure 檔案同步代理程式。 設定同步之後，您的檔案會從伺服器上傳。 我們目前觀察到所有客戶的平均上傳速度大約為每 2 天 1 TB。
請考慮[頻寬節流排程](storage-sync-files-server-registration.md#ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter)，以確保您的伺服器在資料中心表現良好。

### <a name="offline-bulk-transfer"></a>離線大量轉送
雖然上傳必定是最簡單的選項，但如果您可用的頻寬不允許在合理的時間內將檔案同步至 Azure，您就不適用該選項。 此處要克服的挑戰是整組檔案的初始同步。 Azure 檔案同步後續只會移動命名空間中發生的變更，通常會使用較少的頻寬。
若要克服這個初始上傳挑戰，可以使用離線大量移轉工具，例如 Azure [資料箱系列](https://azure.microsoft.com/services/storage/databox)。 下列文章著重於您要以 Azure 檔案同步相容的方式受惠於離線移轉時所需遵循的程序。 它會說明在您啟用同步後，協助您避免衝突檔案及保留檔案和資料夾 ACL 和時間戳記的最佳做法。

### <a name="online-migration-tools"></a>線上移轉工具
以下所述的程序不只適用於資料箱。 它適用於任何離線移轉工具 (例如資料箱) 或線上工具 (例如 AzCopy、Robocopy 或第三方工具和服務)。 因此不論克服初始上傳項挑戰的方法為何，仍必須遵循下列步驟，才能以同步相容的方式利用這些工具。


## <a name="offline-data-transfer-benefits"></a>離線資料轉送優點
使用資料箱時離線移轉的主要優點如下所示：

- 透過離線大量轉送程序 (例如資料箱) 將檔案遷移至 Azure 時，您不必透過網路從您的伺服器上傳所有檔案。 對於大型命名空間，這可能表示大幅節省網路頻寬與時間。
- 當您使用 Azure 檔案同步時，不論使用的傳輸模式為何 (資料箱、Azure 匯入等)，您的即時伺服器只會上傳您將資料寄送至 Azure 後變更的檔案。
- Azure 檔案同步也可確保您的檔案和資料夾的 ACL 已同步 - 即使離線大量移轉產品不會傳輸 ACL。
- 使用 Azure 資料箱和 Azure 檔案同步時，沒有停機時間。 使用資料箱將資料轉送至 Azure，可有效地使用網路頻寬，同時保留檔案逼真度。 只上傳資料箱傳送後變更的檔案，也可讓您的命名空間保持最新狀態。

## <a name="plan-your-offline-data-transfer"></a>規劃離線資料轉送
開始之前，請檢閱下列資訊：

- 先完成大量移轉到一或多個 Azure 檔案共用，才能夠與 Azure 檔案同步進行同步處理。
- 如果您打算使用資料箱進行大量移轉：檢閱[資料箱的部署必要條件](../../databox/data-box-deploy-ordered.md#prerequisites)。
- 規劃您最終的 Azure 檔案同步拓撲：[規劃 Azure 檔案同步部署](storage-sync-files-planning.md)
- 選取 Azure 儲存體帳戶，該帳戶將保留您要同步的檔案共用。 確定您的大量移轉發生於相同儲存體帳戶中的暫存預備共用。 大量移轉只能利用位於相同儲存體帳戶中的最終和預備共用來啟用。
- 當您建立與伺服器位置的新同步關聯性時，才可以利用大量移轉。 您無法啟用具有現有同步關聯性的大量移轉。

## <a name="offline-data-transfer-process"></a>離線資料轉送程序
本節說明以與大量移轉工具 (例如 Azure 資料箱) 相容的方式設定 Azure 檔案同步的程序。

![也會在下列段落中詳細說明的視覺化程序步驟](media/storage-sync-files-offline-data-transfer/data-box-integration-1-600.png)

| 步驟 | 詳細資料 |
|---|---------------------------------------------------------------------------------------|
| ![程序步驟 1](media/storage-sync-files-offline-data-transfer/bullet_1.png) | [訂購資料箱](../../databox/data-box-deploy-ordered.md)。 [資料箱系列中有數個供應項目](https://azure.microsoft.com/services/storage/databox/data)可符合您的需求。 接收您的資料箱並遵循資料箱[文件來複製您的資料](../../databox/data-box-deploy-copy-data.md#copy-data-to-data-box)。 請確定資料已複製到資料箱上的這個 UNC 路徑：`\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>`，其中 `ShareName` 是預備共用的名稱。 將資料箱送回 Azure。 |
| ![程序步驟 2](media/storage-sync-files-offline-data-transfer/bullet_2.png) | 等到您的檔案顯示在您指定為暫存預備共用的 Azure 檔案共用。 **請勿啟用同步至這些共用！** |
| ![程序步驟 3](media/storage-sync-files-offline-data-transfer/bullet_3.png) | 針對資料箱為您建立的每個檔案共用，建立空的新共用。 確定這個新的共用位於與資料箱共用相同的儲存體帳戶中。 [如何建立新的 Azure 檔案共用](storage-how-to-create-file-share.md)。 |
| ![程序步驟 4](media/storage-sync-files-offline-data-transfer/bullet_4.png) | 在儲存體同步服務中[建立同步群組](storage-sync-files-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint)，並將空的共用當作雲端端點。 針對每個資料箱檔案共用，重複此步驟。 檢閱[部署 Azure 檔案同步](storage-sync-files-deployment-guide.md)指南，並遵循設定 Azure 檔案同步所需的步驟。 |
| ![程序步驟 5](media/storage-sync-files-offline-data-transfer/bullet_5.png) | [將您的即時伺服器目錄新增為伺服器端點](storage-sync-files-deployment-guide.md#create-a-server-endpoint)。 在程序中指定您已經將檔案移至 Azure，並參考預備共用。 您可以視需要選擇啟用或停用雲端階層處理。 在即時伺服器上建立伺服器端點時，您必須參考預備共用。 在新的伺服器端點刀鋒視窗中啟用「離線資料轉送」(如下圖所示)，然後參考必須位於與雲端端點相同儲存體帳戶中的預備共用。 可用的共用清單會依照儲存體帳戶和尚未同步的共用進行篩選。 |

![視覺化 Azure 入口網站使用者介面，以便在建立新的伺服器端點時啟用 [離線資料轉送]。](media/storage-sync-files-offline-data-transfer/data-box-integration-2-600.png)

## <a name="syncing-the-share"></a>同步共用
在您建立伺服器端點之後，就會開始同步處理。 對於伺服器上的每個檔案，同步處理會判斷此檔案是否也存在於資料箱儲放檔案的預備共用中，若是如此，同步處理會從預備共用複製檔案，而不是從伺服器上傳該檔案。 如果檔案不存在於預備共用，或本機伺服器上有較新的版本可用，則同步處理會從本機伺服器上傳檔案。

> [!IMPORTANT]
> 您只能在建立伺服器端點期間啟用大量移轉模式。 一旦建立伺服器端點，目前沒有任何方法可將大量遷移的資料從已經同步的伺服器整合到命名空間中。

## <a name="acls-and-timestamps-on-files-and-folders"></a>檔案和資料夾的 ACL 和時間戳記
Azure 檔案同步可確保即使所用的大量移轉工具一開始未傳輸 ACL，也會從即時伺服器同步檔案和資料夾 ACL。 這表示預備共用可以不包含任何檔案和資料夾的 ACL。 當您在建立新伺服器端點時啟用離線資料移轉功能，ACL 會同時針對伺服器上的所有檔案進行同步處理。 這也適用於已建立和修改的時間戳記。

## <a name="shape-of-the-namespace"></a>命名空間的形狀
命名空間的形狀取決於啟用同步時伺服器上的內容。 如果在資料箱「快照」和移轉之後，刪除本機伺服器上的檔案，這些檔案便不會放入即時、同步命名空間中。 它們仍在預備共用中，且永遠不會複製。 這就是所要的行為，因為同步處理會根據即時伺服器保留命名空間。 資料箱「快照集」只是有效檔案複製的預備區域，而不是即時命名空間的形狀授權單位。

## <a name="finishing-bulk-migration-and-clean-up"></a>完成大量移轉和清除
以下螢幕擷取畫面顯示 Azure 入口網站中的伺服器端點屬性刀鋒視窗。 在 [離線資料轉送] 區段中，您可以觀察程序的狀態。 它會顯示「進行中」或「已完成」。

在伺服器完成整個命名空間的初始同步處理之後，它就會充分利用預備檔案共用與資料箱大量遷移檔案。 在伺服器端點屬性中觀察狀態正要變更為「完成」的離線資料轉送。 此時，您可以清除預備共用，以節省成本：

1. 當狀態為「已完成」時，叫用伺服器端點屬性中的 [停用離線資料轉送]。
2. 請考慮刪除預備共用，以節省成本。 預備共用不太可能包含檔案和資料夾 ACL，因此用途有限。 針對備份「時間點」目的，建立[同步 Azure 檔案共用的真實快照集](storage-snapshots-files.md)。 您可以[讓 Azure 備份依照排程建立快照集]( ../../backup/backup-azure-files.md)。

![針對離線資料轉送的狀態和停用控制項所在的伺服器端點屬性，視覺化 Azure 入口網站使用者介面。](media/storage-sync-files-offline-data-transfer/data-box-integration-3-444.png)

當狀態為「已完成」或您因為設定不正確而真的想要中止時，您應該只能停用此模式。 如果您在合法部署途中停用此模式，即使您的預備共用仍然可用，仍會開始從伺服器上傳檔案。

> [!IMPORTANT]
> 在您停用離線資料轉送之後，即使大量移轉中的預備共用仍然可用，仍無法重新啟用它。

## <a name="next-steps"></a>後續步驟
- [規劃 Azure 檔案同步部署](storage-sync-files-planning.md)
- [部署 Azure 檔案同步](storage-sync-files-deployment-guide.md)
