---
title: 在區域備援儲存體 (ZRS) 上建置高可用性 Azure 儲存體應用程式 | Microsoft Docs
description: 區域備援儲存體 (ZRS) 提供一個建置高可用性應用程式的簡單方式。 ZRS 可以針對資料中心內的硬體故障提供防護，以及針對一些區域性災害提供防護。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 10/24/2018
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 111167584fb2e0e2ee5977e0e24b3ebf07b170c1
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237996"
---
# <a name="zone-redundant-storage-zrs-highly-available-azure-storage-applications"></a>區域備援儲存體 (ZRS)：高可用性 Azure 儲存體應用程式
[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-zrs.md)]

## <a name="support-coverage-and-regional-availability"></a>支援涵蓋範圍和區域可用性
ZRS 目前支援標準的一般用途 v2 帳戶類型。 如需有關儲存體帳戶類型的詳細資訊，請參閱 [Azure 儲存體帳戶概觀](storage-account-overview.md)。

ZRS 可用於區塊 Blob、非磁碟分頁 Blob、檔案、資料表及佇列。

ZRS 一般會在以下地區推出：

- 東南亞
- 西歐
- 北歐
- 法國中部
- 日本東部
- 英國南部
- 美國中部
- 美國東部
- 美國東部 2
- 美國西部 2

Microsoft 會持續在其他 Azure 區域啟用 ZRS。 如需新區域的詳細資訊，請定期查看 [Azure 服務更新](https://azure.microsoft.com/updates/)頁面。

## <a name="what-happens-when-a-zone-becomes-unavailable"></a>當區域變成無法使用時，會發生什麼事？
即使區域變成無法使用，您的資料仍然可供讀取和寫入作業存取。 Microsoft 建議您繼續依照暫時性錯誤處理做法進行操作。 這些做法包括實作具有指數輪詢的重試原則。

當區域無法使用時，Azure 會執行網路更新，例如 DNS 重新指向。 如果您在更新完成之前存取資料，這些更新可能會影響您的應用程式。

發生多個區域受到永久影響的區域性災害時，ZRS 可能無法保護資料。 相反地，如果是暫時無法使用，ZRS 可為您的資料提供復原能力。 為了防範區域性災害，Microsoft 建議使用異地備援儲存體 (GRS)。 如需 GRS 的詳細資訊，請參閱[異地備援儲存體 (GRS)：適用於 Azure 儲存體的跨區域複寫](storage-redundancy-grs.md)。

## <a name="converting-to-zrs-replication"></a>轉換成 ZRS 複寫
以 LRS、GRS 及 RA-GRS 作為目標或來源進行移轉相當簡單。 請使用 Azure 入口網站或「儲存體資源提供者 API」來變更帳戶的備援類型。 接著，Azure 便會相應地複寫您的資料。 

將資料移轉至 ZRS 需要不同的策略。 ZRS 移轉涉及將資料從單一儲存體戳記實際移至區域內的多個戳記。

有兩個移轉至 ZRS 的主要選項： 

- 手動將資料從現有帳戶複製或移動到新的 ZRS 帳戶。
- 要求即時移轉。

Microsoft 強烈建議您執行手動移轉。 手動移轉比即時移轉更具彈性。 使用手動移轉時，您可以控制時間。

若要執行手動移轉，您有下列選項：
- 使用現有的工具，例如 AzCopy、其中一個「Azure 儲存體」用戶端程式庫或可靠的第三方工具。
- 如果您熟悉 Hadoop 或 HDInsight，請將來源和目的地 (ZRS) 帳戶都連結至叢集。 然後，使用 DistCp 之類的工具來平行處理資料複製程序。
- 使用其中一個「Azure 儲存體」用戶端程式庫來建置您自己的工具。

手動移轉可能造成應用程式停機。 如果您的應用程式要求高可用性，Microsoft 也提供即時移轉選項。 即時移轉是一種就地移轉。 

在即時移轉期間，您可以在資料於來源與目的地儲存體戳記之間移轉的同時，使用您的儲存體帳戶。 在移轉過程中，您會有相同層級的持久性和可用性 SLA 為您平常。

請記住下列即時移轉限制：

- 雖然 Microsoft 會迅速處理您的即時移轉要求，但不保證移轉何時會完成。 如果您需要在特定日期之前將資料移轉至 ZRS，則 Microsoft 建議您改為執行手動移轉。 一般而言，您帳戶中的資料越多，移轉該資料所需的時間就越長。 
- 只有針對使用 LRS 或 GRS 複寫的儲存體帳戶，才支援即時移轉。 如果您的帳戶使用 RA-GRS，則必須先將帳戶複寫類型變更為 LRS 或 GRS，再繼續進行操作。 這個中繼步驟會在移轉之前，先將 RA-GRS 所提供的次要唯讀端點移除。
- 您的帳戶必須包含資料。
- 您只能在相同的區域內移轉資料。 如果您想要將資料移轉至的 ZRS 帳戶位於和來源帳戶不同的區域中，則必須執行手動移轉。
- 只有標準儲存體帳戶類型支援即時移轉。 進階儲存體帳戶必須以手動方式移轉。
- 不支援 ZRS 從 LRS、 GRS 或 RA-GRS 的費用一直到的即時移轉。 您必須以手動方式將資料移至新的或現有的儲存體帳戶。
- 受控的磁碟僅適用於 LRS，且無法移轉至 ZRS。 針對可用性的整合設定，請參閱[Introduction to Azure 受控磁碟](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#integration-with-availability-sets)。 您可以對於標準 SSD 受控磁碟儲存快照集和映像，在標準的 HDD 儲存體和[LRS 與 ZRS 選項當中作選擇](https://azure.microsoft.com/pricing/details/managed-disks/)。 

您可以透過 [Azure 支援入口網站](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)要求即時移轉。 從入口網站中，選取您想要轉換成 ZRS 的儲存體帳戶。
1. 選取 [新增支援要求] 
2. 根據您的帳戶資訊，完成 [基本]  資訊。 在 [服務]  區段中，選取 [儲存體帳戶管理]  和您想要轉換成 ZRS 的資源。 
3. 選取 [下一步]  。 
4. 在 [問題]  區段中，指定下列值︰ 
    - **嚴重性**：將預設值保持不變。
    - **問題類型**：選取 [資料移轉]  。
    - **類別**：選取 [移轉至區域內的 ZRS]  。
    - **標題**：輸入描述性標題，例如 **ZRS 帳戶移轉**。
    - **詳細資料**：在 [詳細資料]  方塊中輸入額外的詳細資料，例如：我想要從 \_\_ 區域中的 [LRS、GRS] 移轉至 ZRS。 
5. 選取 [下一步]  。
6. 確認 [連絡人資訊]  刀鋒視窗上的連絡人資訊正確。
7. 選取 [建立]  。

支援人員將會與您連絡，並提供您所需的一切協助。

## <a name="live-migration-to-zrs-faq"></a>即時移轉至 ZRS 常見問題集

**應規劃在移轉期間任何停機時間？**

沒有任何移轉作業所造成的停機時間。 在即時移轉時，您可以繼續使用您的儲存體帳戶，而您的資料來源和目的地儲存體戳記之間移轉。 在移轉過程中，您會有相同層級的持久性和可用性 SLA 為您平常。

**是否有任何移轉作業相關聯的資料遺失？**

不沒有與移轉相關聯的任何資料遺失。 在移轉過程中，您會有相同層級的持久性和可用性 SLA 為您平常。

**移轉完成後，請為應用程式所需的任何更新？**

一旦完成移轉帳戶的複寫類型會變更為 「 區域備援儲存體 (ZRS) 」 中。 服務端點存取金鑰，SAS 與任何其他帳戶設定選項保持不變，而且保持不變。

**要求的即時移轉至 ZRS 我一般用途 v1 帳戶？**

ZRS 僅支援一般用途 v2 帳戶，因此再送出要求的即時移轉至 ZRS 請務必將您的帳戶升級為一般用途 v2。 請參閱[Azure 儲存體帳戶概觀](https://docs.microsoft.com/azure/storage/common/storage-account-overview)並[升級至一般用途 v2 儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade)如需詳細資訊。

**要求的即時移轉至 ZRS 我讀取權限異地備援儲存體 (RA-GRS) 帳戶？**

再送出即時移轉至 ZRS 的要求，請確定您的應用程式或工作負載不再需要次要唯讀端點的存取權，並將儲存體帳戶的複寫類型變更為異地備援儲存體 (GRS)。 請參閱[變更複寫策略](https://docs.microsoft.com/azure/storage/common/storage-redundancy#changing-replication-strategy)如需詳細資訊。

**要求的即時移轉至 ZRS 我儲存體帳戶到另一個區域？**

如果您想要將資料移轉至傳統 ZRS 帳戶位於來源帳戶的區域不同區域中，您必須執行手動移轉。

## <a name="zrs-classic-a-legacy-option-for-block-blobs-redundancy"></a>傳統 ZRS：適用於區塊 Blob 備援的舊版選項
> [!NOTE]
> Microsoft 會在 2021 年 3 月 31 日將傳統 ZRS 帳戶除役並進行移轉。 在淘汰之前，會對傳統 ZRS 客戶提供更多詳細資料。 
>
> 一旦 ZRS[正式](#support-coverage-and-regional-availability)在區域中，客戶無法再從該區域中的入口網站建立傳統 ZRS 帳戶。 在傳統 ZRS 被淘汰之前，可選擇使用 Microsoft PowerShell 和 Azure CLI 來建立傳統 ZRS 帳戶。

傳統 ZRS 會以非同步方式在一至兩個區域內的資料中心間複寫資料。 除非 Microsoft 起始對次要區域的容錯移轉，否則複寫的資料可能無法供使用。 傳統 ZRS 帳戶無法以 LRS、GRS 或 RA-GRS 作為轉換目標或來源。 傳統 ZRS 帳戶也不支援計量或記錄。

傳統 ZRS 僅適用於一般用途 V1 (GPv1) 儲存體帳戶中的**區塊 Blob**。 如需有關儲存體帳戶的詳細資訊，請參閱 [Azure 儲存體帳戶概觀](storage-account-overview.md)。

若要以 LRS、傳統 ZRS、GRS 或 RA-GRS 帳戶作為手動移轉 ZRS 帳戶資料時的目標或來源，請使用下列其中一種工具：AzCopy、Azure 儲存體總管、Azure PowerShell 或 Azure CLI。 您也可以使用其中一個 Azure 儲存體用戶端程式庫，自行建置移轉解決方案。

您也可以在入口網站或使用 Azure PowerShell 的 ZRS 」 或 「 ZRS 可使用的區域中的 Azure CLI 來升級您的傳統 ZRS 帳戶。

若要升級至 ZRS 入口網站中的移至帳戶的 設定 區段，然後選擇 升級：![ZRS 傳統升級至入口網站中的 ZRS](media/storage-redundancy-zrs/portal-zrs-classic-upgrade.jpg)

若要升級至使用 ZRS PowerShell 會呼叫下列命令：
```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> -AccountName <storage_account> -UpgradeToStorageV2
```

若要升級至使用 ZRS CLI 會呼叫下列命令：
```cli
az storage account update -g <resource_group> -n <storage_account> --set kind=StorageV2
```

## <a name="see-also"></a>請參閱
- [Azure 儲存體複寫](storage-redundancy.md)
- [本地備援儲存體 (LRS)：適用於 Azure 儲存體的低成本資料備援](storage-redundancy-lrs.md)
- [異地備援儲存體 (GRS)：適用於 Azure 儲存體的跨區域複寫](storage-redundancy-grs.md)
