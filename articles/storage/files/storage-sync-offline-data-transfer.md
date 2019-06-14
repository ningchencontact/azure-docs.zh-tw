---
title: 使用 Azure 資料箱和其他方法，將資料移轉到 Azure 檔案同步
description: 適用於 Azure 檔案同步的方式的大量資料移轉。
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 02/12/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: d1ec5168b898d0aa75c12e6eb435e20c09de1929
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "64700279"
---
# <a name="migrate-bulk-data-to-azure-file-sync"></a>將大量資料移轉至 Azure 檔案同步
您可以移轉至 Azure 檔案同步的大量資料，有兩種：

* **使用 Azure 檔案同步上傳您的檔案。** 這是最簡單的方法。 移動您的檔案在本機 Windows Server 2012 R2 或更新版本，並安裝 Azure 檔案同步代理程式。 您設定同步處理之後，您的檔案將會上傳從伺服器中。 （我們的客戶目前遇到關於每隔兩天的 1 TiB 的平均的上傳速度）。若要確保您的伺服器不使用太多頻寬的資料中心，您可能想要設定[頻寬節流排程](storage-sync-files-server-registration.md#ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter)。
* **傳輸檔案離線。** 如果您沒有足夠的頻寬，您可能無法將檔案上傳至 Azure 中合理的時間。 所面臨的挑戰是檔案的完整集合的初始同步處理。 若要克服這項挑戰，使用離線大量移轉工具這類[Azure 資料箱系列](https://azure.microsoft.com/services/storage/databox)。 

這篇文章說明如何移轉適用於 Azure 檔案同步的方式離線檔案。遵循這些指示，以避免檔案衝突，並在您啟用同步處理之後，保留您的檔案和資料夾的存取控制清單 (Acl) 和時間戳記。

## <a name="online-migration-tools"></a>線上移轉工具
我們在此文章適用於中的資料 方塊不僅其他離線移轉工具說明程序。 它也適用於線上工具，例如 AzCopy、 Robocopy 或協力廠商工具和服務。 不過您克服初始上傳的挑戰，遵循這篇文章適用於 Azure 檔案同步的方式使用這些工具中的步驟。


## <a name="benefits-of-using-a-tool-to-transfer-data-offline"></a>將資料離線上使用工具的優點
以下是使用離線移轉等資料 方塊中的傳輸工具的主要優點：

- 您不需要透過網路上傳所有的檔案。 對於大型的命名空間，這項工具可以節省大量的網路頻寬和時間。
- 當您使用 Azure 檔案同步時，無論哪一種傳輸工具使用 （資料 方塊中，Azure 匯入/匯出服務等等），您的即時伺服器僅上傳檔案，您將資料移至 Azure 之後，變更。
- Azure 檔案同步會同步處理檔案和資料夾的 Acl，即使離線大量移轉工具不會傳輸 Acl。
- Data Box 和 Azure 檔案同步需要停機。 當您將資料傳輸至 Azure 中使用資料 方塊中時，您會有效率地使用網路頻寬，並保留檔案的精確度。 您也讓您的命名空間最新狀態上傳的變更之後您將資料移至 Azure 的檔案。

## <a name="prerequisites-for-the-offline-data-transfer"></a>離線的資料傳輸的必要條件
您不應該啟用您正在移轉之前完成您的離線資料傳輸在伺服器上的同步處理。 若要在您開始前，請考慮其他項目如下所示：

- 如果您打算使用資料箱進行大量移轉：檢閱[資料箱的部署必要條件](../../databox/data-box-deploy-ordered.md#prerequisites)。
- 規劃您最終的 Azure 檔案同步拓撲：[規劃 Azure 檔案同步部署](storage-sync-files-planning.md)
- 選取 Azure 儲存體帳戶，該帳戶將保留您要同步的檔案共用。 確定您的大量移轉發生於相同儲存體帳戶中的暫存預備共用。 大量移轉只能利用位於相同儲存體帳戶中的最終和預備共用來啟用。
- 當您建立與伺服器位置的新同步關聯性時，才可以利用大量移轉。 您無法啟用具有現有同步關聯性的大量移轉。


## <a name="process-for-offline-data-transfer"></a>離線的資料傳輸程序
以下是如何設定 Azure 檔案同步與大量移轉工具，例如 Azure 資料箱相容的方式：

![此圖顯示如何設定 Azure 檔案同步](media/storage-sync-files-offline-data-transfer/data-box-integration-1-600.png)

| 步驟 | Detail |
|---|---------------------------------------------------------------------------------------|
| ![步驟 1](media/storage-sync-files-offline-data-transfer/bullet_1.png) | [訂購資料箱](../../databox/data-box-deploy-ordered.md)。 系列提供的資料箱[有幾項產品](https://azure.microsoft.com/services/storage/databox/data)以符合您的需求。 當您收到您的資料箱時，請遵循其[說明文件，以將資料複製](../../databox/data-box-deploy-copy-data.md#copy-data-to-data-box)Data Box 上此 UNC 路徑：  *\\< DeviceIPAddres\>\<StorageAccountName_AzFile\>\<ShareName\>* 。 在這裡， *ShareName*是預備的共用名稱。 將資料箱送回 Azure。 |
| ![步驟 2](media/storage-sync-files-offline-data-transfer/bullet_2.png) | 等候，直到您的檔案顯示在您選擇做為暫存的預備共用的 Azure 檔案共用。 *請勿啟用同步至這些共用。* |
| ![步驟 3](media/storage-sync-files-offline-data-transfer/bullet_3.png) | 建立新的空白共用，為您的資料 方塊中建立每個檔案共用。 這個新的共用應該位於與資料 方塊中共用相同的儲存體帳戶。 [如何建立新的 Azure 檔案共用](storage-how-to-create-file-share.md)。 |
| ![步驟 4](media/storage-sync-files-offline-data-transfer/bullet_4.png) | [建立同步群組](storage-sync-files-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint)在儲存體同步服務。 空白的共用作為雲端端點的參考。 針對每個資料箱檔案共用，重複此步驟。 [設定 Azure 檔案同步](storage-sync-files-deployment-guide.md)。 |
| ![步驟 5](media/storage-sync-files-offline-data-transfer/bullet_5.png) | [將您的即時伺服器目錄新增為伺服器端點](storage-sync-files-deployment-guide.md#create-a-server-endpoint)。 在過程中，指定您將檔案移至 Azure，並參考暫存的共用。 您可以啟用或停用雲端階層處理所需。 在即時伺服器上建立伺服器端點，來參考暫存的共用。 上**新增伺服器端點**刀鋒視窗底下**離線資料傳輸**，選取**已啟用**，然後選取 必須是相同的儲存體帳戶，為雲端中的暫存共用端點。 在這裡，可用的共用資料夾的清單會篩選儲存體帳戶並不已同步處理的共用。 |

![螢幕擷取畫面的 Azure 入口網站使用者介面中，示範如何建立新的伺服器端點時啟用離線的資料傳輸](media/storage-sync-files-offline-data-transfer/data-box-integration-2-600.png)

## <a name="syncing-the-share"></a>同步共用
建立您的伺服器端點之後，會啟動同步處理。 同步處理程序會判斷是否在伺服器上的每個檔案也存在於預備的共用資料箱儲放檔案的位置。 如果檔案那里存在，同步處理程序會從預備的共用，而不是將它上傳從伺服器複製檔案。 如果檔案不存在於預備的共用，或同步處理程序是否可在本機伺服器上的較新版本，會從本機伺服器檔案上傳。

> [!IMPORTANT]
> 只有當您要建立伺服器端點時，您可以讓大量移轉模式。 建立伺服器端點之後，您無法大量移轉資料，從已同步處理伺服器整合之命名空間。

## <a name="acls-and-timestamps-on-files-and-folders"></a>檔案和資料夾的 ACL 和時間戳記
Azure 檔案同步可確保即使您使用大量移轉工具一開始未傳輸 Acl，會從即時伺服器同步檔案及資料夾的 Acl。 因為這個緣故，不需要預備共用包含檔案與資料夾任何 Acl。 當您啟用的離線資料移轉功能，當您建立新的伺服器端點時，所有的檔案 Acl 會同步處理伺服器上。 新建立和修改時間戳記也會進行同步處理。

## <a name="shape-of-the-namespace"></a>命名空間的形狀
當您啟用同步處理時，伺服器的內容會決定 圖形的命名空間。 如果資料 方塊中的快照集和移轉完成之後，會從本機伺服器刪除檔案，這些檔案不會移動到即時、 同步處理命名空間。 仍留在預備的共用，但它們不會複製。 這是必要的因為同步會根據即時伺服器的命名空間。 資料箱*快照集*是只是預備地面有效率的檔案複本。 它不是即時的命名空間的形狀的授權單位。

## <a name="cleaning-up-after-bulk-migration"></a>大量移轉後進行清除 
當伺服器完成其命名空間的初始同步處理時，資料箱大量移轉檔案就會使用暫存的檔案共用。 上**伺服器端點屬性**刀鋒視窗中，在 Azure 入口網站中**離線資料傳輸** 區段中，狀態會變更來自**進行中**至**已完成**. 

![伺服器端點屬性刀鋒視窗中，離線的資料傳輸的狀態和停用控制項的所在位置的螢幕擷取畫面](media/storage-sync-files-offline-data-transfer/data-box-integration-3-444.png)

現在您可以清除以節省成本的臨時共用：

1. 在 **伺服器端點屬性**刀鋒視窗中，當狀態為**已完成**，選取**停用離線的資料傳輸**。
2. 請考慮刪除預備共用，以節省成本。 預備共用可能不包含檔案和資料夾的 Acl，因此不是很有用。 進行時間點備份，建立真實[同步處理的 Azure 檔案共用快照集](storage-snapshots-files.md)。 您可以[設定 Azure 備份，若要建立快照集]( ../../backup/backup-azure-files.md)依排程。

停用離線資料的傳輸模式下，只有在狀態時，才**已完成**或者您想要取消，因為設定不正確。 如果您停用模式，在部署期間，檔案便會開始從伺服器上傳，即使您預備的共用是仍然可用。

> [!IMPORTANT]
> 停用離線的資料傳輸模式之後，您無法再次啟用它，即使從大量移轉預備的共用是仍然可用。

## <a name="next-steps"></a>後續步驟
- [規劃 Azure 檔案同步部署](storage-sync-files-planning.md)
- [部署 Azure 檔案同步](storage-sync-files-deployment-guide.md)
