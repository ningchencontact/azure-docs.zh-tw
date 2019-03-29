---
title: 升級至一般用途 v2 儲存體帳戶-Azure 儲存體 |Microsoft Docs
description: 升級到一般用途 v2 儲存體帳戶。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/26/2019
ms.author: tamram
ms.openlocfilehash: 2d6a5c96bf99439520e26fc905668835944cee29
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2019
ms.locfileid: "58578913"
---
# <a name="upgrade-to-a-general-purpose-v2-storage-account"></a>升級至一般用途 v2 儲存體帳戶

一般用途 v2 儲存體帳戶支援的最新的 Azure 儲存體功能，並合併所有的一般用途 v1 和 Blob 儲存體帳戶的功能。 針對大部分的儲存體案例，皆建議使用一般用途 v2 帳戶。 一般用途 v2 帳戶能針對 Azure 儲存體提供最低的每 GB 容量價格，以及極具業界競爭力的交易價格。

若要從您的一般用途 v1 的一般用途 v2 儲存體帳戶或 Blob 儲存體帳戶升級很簡單。 您可以使用 Azure 入口網站、PowerShell 或 Azure CLI 來執行升級。

> [!IMPORTANT]
> 升級到一般用途 v2 的一般用途 v1 或 Blob 儲存體帳戶是永久性的而且無法復原。

## <a name="upgrade-using-the-azure-portal"></a>使用 Azure 入口網站升級

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 瀏覽至儲存體帳戶。
3. 在 [設定] 區段中，按一下 [組態]。
4. 在 [帳戶類型] 下，按一下 [升級]。
5. 在 [確認升級] 下，輸入您的帳戶名稱。
6. 按一下刀鋒視窗底部的 [升級]。

    ![升級帳戶類型](../blobs/media/storage-blob-account-upgrade/upgrade-to-gpv2-account.png)

## <a name="upgrade-with-powershell"></a>使用 PowerShell 升級

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

若要升級的一般用途 v1 帳戶使用 PowerShell 在一般用途 v2 帳戶中，先更新 PowerShell 以使用最新版**Az.Storage**模組。 如需安裝 PowerShell 的相關資訊，請參閱[安裝和設定 Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps)。

接下來，請呼叫下列命令，並取代為您的資源群組與儲存體帳戶的名稱，以升級帳戶：

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group> -AccountName <storage-account> -UpgradeToStorageV2
```

## <a name="upgrade-with-azure-cli"></a>使用 Azure CLI 升級

若要升級的一般用途 v1 帳戶使用 Azure CLI 的一般用途 v2 帳戶，請先安裝最新版的 Azure CLI。 如需安裝 CLI 的相關資訊，請參閱[安裝 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

接下來，請呼叫下列命令，並取代為您的資源群組與儲存體帳戶的名稱，以升級帳戶：

```cli
az storage account update -g <resource-group> -n <storage-account> --set kind=StorageV2
```

## <a name="specify-an-access-tier-for-blob-data"></a>指定 Blob 資料的存取層

一般用途 v2 帳戶能支援所有 Azure 儲存體服務與資料物件，但存取層僅適用於 Blob 儲存體中的區塊 Blob。 當您升級到一般用途 v2 儲存體帳戶時，您可以指定存取層，為您的 blob 資料。

存取層可讓您根據預期的使用模式，選擇最符合成本效益的儲存體。 區塊 Blob 可以儲存於經常性存取層、非經常性存取層或封存存取層。 如需存取層的詳細資訊，請參閱 [Azure Blob 儲存體：經常性存取、非經常性存取和封存儲存層](../blobs/storage-blob-storage-tiers.md)。

根據預設，在經常性存取層中，建立新的儲存體帳戶，和一般用途 v1 儲存體帳戶升級為經常性存取層。 如果您是在已升級的情況下決定要針對資料使用哪個存取層，請考慮您的案例。 有兩個移轉至一般用途 v2 帳戶的一般使用者案例：

* 您有現有的一般用途 v1 儲存體帳戶，而且想要評估升級至具有 blob 資料的正確的儲存體存取層的一般用途 v2 儲存體帳戶。
* 您已決定使用一般用途 v2 儲存體帳戶，或已經有一個，想要評估您應該使用經常性存取或非經常性存取儲存體存取層 blob 資料。

在這兩種情況下，第一要務是估計儲存、 存取和操作資料的一般用途 v2 儲存體帳戶中儲存的成本，並與您目前的成本進行比較。

## <a name="pricing-and-billing"></a>價格和計費

升級至一般用途 v2 帳戶的 v1 儲存體帳戶是免費的。 但是，更改存储访问层可能会导致帐单更改。 

所有儲存體帳戶會對以每個 Blob 層為基礎的 Blob 儲存體使用價格模型。 使用儲存體帳戶時，需考量下列計費資訊：

* **儲存成本**：除了儲存的資料量以外，儲存資料的成本會因儲存體存取層而異。 每 GB 的成本會隨著儲存層存取頻率降低而減少。

* **資料存取成本**：資料存取費用會隨著儲存層存取頻率降低而增加。 對於非經常性儲存層與封存儲存體存取層中的資料，您需支付讀取的每 GB 資料存取費用。

* **交易成本**：所有層都有每筆交易的費用，該費用會隨著儲存層存取頻率降低而增加。

* **異地複寫資料傳輸成本**：此費用適用於已設定異地複寫的帳戶，包括 GRS 和 RA-GRS。 異地複寫資料傳輸會產生每 GB 費用。

* **輸出資料傳輸成本**：輸出資料傳輸 （會傳出 Azure 區域的資料） 頻寬使用量費用依每 gb，與一般用途的儲存體帳戶一致。

* **變更儲存體存取層**︰將帳戶儲存體存取層從非經常性存取層變更為經常性存取層，會產生相當於讀取儲存體帳戶中所有資料的費用。 不過，將帳戶存取層從經常性存取層變更為非經常性存取層，會產生相當於將所有資料寫入非經常性存取層的費用 (僅限 GPv2 帳戶)。

> [!NOTE]
> 如需儲存體帳戶的定價模型詳細資訊，請參閱 [Azure 儲存體定價](https://azure.microsoft.com/pricing/details/storage/)頁面。 如需輸出資料傳輸費用的詳細資訊，請參閱[資料傳輸定價詳細資料](https://azure.microsoft.com/pricing/details/data-transfers/)頁面。

### <a name="estimate-costs-for-your-current-usage-patterns"></a>針對目前的使用模式估計成本

若要估計儲存和存取特定的層中的一般用途 v2 儲存體帳戶中的 blob 資料的成本，評估您現有的使用模式或接近您預期的使用模式。 一般而言，您想要知道︰

* 您的 Blob 儲存體耗用量 (以 GB 為單位)，包括：
    - 有多少資料儲存在儲存體帳戶中？
    - 每月的資料量變更為何？新資料是否會不斷取代舊資料？
* 您 Blob 儲存體資料的主要存取模式，包括：
    - 儲存體帳戶的讀取和寫入資料量有多少？
    - 在儲存體帳戶中的資料上發生多少讀取作業和寫入作業？

為了協助決定符合您需求的最佳存取層，您應該判斷 Blob 資料容量，以及該資料的使用狀況。 最佳做法則是查看您帳戶的監視計量。

### <a name="monitoring-existing-storage-accounts"></a>監視現有的儲存體帳戶

若要監視現有的儲存體帳戶並收集此資料，您可以利用 Azure 儲存體分析來執行記錄及提供儲存體帳戶的度量資料。 儲存體分析可以儲存的計量包含與 GPv1、GPv2 和 Blob 儲存體帳戶之儲存體服務要求相關的彙總交易統計資料和容量資料。 此資料會儲存在相同儲存體帳戶的已知資料表中。

如需詳細資訊，請參閱[關於儲存體分析計量](https://msdn.microsoft.com/library/azure/hh343258.aspx)和[儲存體分析計量資料表結構描述](https://msdn.microsoft.com/library/azure/hh343264.aspx)

> [!NOTE]
> Blob 儲存體帳戶會公開僅適用於儲存和存取該帳戶計量資料的表格服務端點。

若要監視 Blob 儲存體的儲存體使用情況，您必須啟用容量計量。
啟用此度量後，系統會每日記錄儲存體帳戶的 Blob 服務容量資料，而該資料會以資料表項目形式記錄並寫入至相同儲存體帳戶內的 $MetricsCapacityBlob  資料表。

若要監視 Blob 儲存體的資料存取模式，您必須從 API 啟用每小時交易計量。 啟用每小時交易計量後，系統會每小時彙總每筆 API 交易，而該資料會以資料表項目形式記錄，並寫入至相同儲存體帳戶內的 *$MetricsHourPrimaryTransactionsBlob* 資料表。 使用 RA-GRS 儲存體帳戶時，$MetricsHourSecondaryTransactionsBlob  資料表會將交易記錄至次要端點。

> [!NOTE]
> 如果您有一般用途的儲存體帳戶中有儲存了分頁 blob 和虛擬機器磁碟、 或佇列、 檔案或資料表以及區塊和附加 blob 資料時，就不適用此估計程序。 容量資料不會區分區塊 Blob 與其他類型，因此並未取得其他資料類型的容量資料。 如果您使用這些類型，替代方法是查看最近帳單上的數量。

若要取得資料使用和存取模式的適當近似值，建議您針對代表一般使用情況的度量選擇保留期，並進行推斷。 其中一個選項是保留度量資料 7 天並每週收集資料，以便月底進行分析。 另一個選項是保留最近 30 天的度量資料，並在 30 天期間的結尾收集和分析資料。

如需啟用的詳細資訊，收集和檢視計量資料，請參閱[儲存體分析度量](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

> [!NOTE]
> 就如同一般使用者資料，儲存、存取和下載分析資料也需付費。

### <a name="utilizing-usage-metrics-to-estimate-costs"></a>利用使用度量來估計成本

#### <a name="capacity-costs"></a>容量成本

容量度量資料表 $MetricsCapacityBlob 中具有資料列索引鍵 'data' 的最新項目會顯示使用者資料所耗用的儲存體容量。 容量度量資料表 $MetricsCapacityBlob 中具有資料列索引鍵 'analytics' 的最新項目會顯示分析記錄檔所耗用的儲存體容量。

使用者資料和分析記錄檔 (若已啟用) 所耗用的總容量便可用來估計在儲存體帳戶中儲存資料的成本。 相同的方法也可用來估計 GPv1 儲存體帳戶的儲存成本。

#### <a name="transaction-costs"></a>交易成本

交易度量資料表中 API 的所有項目的 'TotalBillableRequests' 總和，會指出該特定 API 的交易總數。 例如，在給定期間的 'GetBlob' 交易總數計算方式為將所有包含 'user;GetBlob'列索引鍵的可計費要求總數進行加總。

若要估計 Blob 儲存體帳戶的交易成本，您必須將交易細分成三個群組，因為它們的定價方式不同。

* 寫入 'PutBlob'、'PutBlock'、'PutBlockList'、'AppendBlock'、'ListBlobs'、'ListContainers'、'CreateContainer'、'SnapshotBlob' 和 'CopyBlob' 等交易。
* 刪除 'DeleteBlob' 和 'DeleteContainer' 等交易。
* 所有其他交易

若要估計 GPv1 儲存體帳戶的交易成本，不論作業/API 為何，您必須彙總所有的交易。

#### <a name="data-access-and-geo-replication-data-transfer-costs"></a>資料存取和異地複寫資料傳輸費用

雖然儲存體分析不會提供讀取自和寫入至儲存體帳戶的資料量，但可藉由查看交易度量資料表大致估算。 交易度量資料表中 API 的所有項目的 'TotalIngress'  總和，會指出該特定 API 的輸入資料總量 (以位元組為單位)。 同樣地，'TotalEgress'  的總和會指出輸出資料總數 (以位元組為單位)。

若要估計 Blob 儲存體帳戶的資料存取成本，您必須將交易細分成兩個群組。

* 查看主要 'GetBlob' 和 'CopyBlob' 作業的 'TotalEgress' 總和，可以估計從儲存體帳戶擷取的資料量。

* 查看主要 'PutBlob'、'PutBlock'、'CopyBlob' 和 'AppendBlock' 作業的 'TotalIngress' 總和，可以估計寫入至儲存體帳戶的資料量。

使用 GRS 或 RA-GRS 儲存體帳戶時，使用寫入的資料量估計值，也可以計算 Blob 儲存體帳戶的異地複寫資料傳輸成本。

> [!NOTE]
> 關於計算使用經常性存取或非經常性儲存體存取層的成本，如需更詳細的範例，請查看標題為「經常性存取或非經常性存取層是什麼，以及如何判斷要使用哪一個？」的常見問題 於 [Azure 儲存體定價頁面](https://azure.microsoft.com/pricing/details/storage/)。

## <a name="next-steps"></a>後續步驟

- [建立儲存體帳戶](storage-quickstart-create-account.md)
- [管理 Azure 儲存體帳戶](storage-account-manage.md)
