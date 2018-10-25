---
title: 在區域備援儲存體 (ZRS) 上建置高可用性 Azure 儲存體應用程式 | Microsoft Docs
description: 區域備援儲存體 (ZRS) 提供一個建置高可用性應用程式的簡單方式。 ZRS 可以針對資料中心內的硬體故障提供防護，以及針對一些區域性災害提供防護。
services: storage
author: tolandmike
ms.service: storage
ms.topic: article
ms.date: 03/20/2018
ms.author: jeking
ms.component: common
ms.openlocfilehash: 0a4ebefd89414fa62ca69f2f9cc726f471694bee
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/17/2018
ms.locfileid: "45733611"
---
# <a name="zone-redundant-storage-zrs-highly-available-azure-storage-applications"></a>區域備援儲存體 (ZRS)：高可用性 Azure 儲存體應用程式
[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-zrs.md)]

## <a name="support-coverage-and-regional-availability"></a>支援涵蓋範圍和區域可用性
ZRS 目前支援標準的一般用途 v2 帳戶類型。 如需儲存體帳戶類型的詳細資訊，請參閱 [Azure 儲存體帳戶概觀](storage-account-overview.md)。

ZRS 可用於區塊 Blob、非磁碟分頁 Blob、檔案、資料表及佇列。

ZRS 一般會在以下地區推出：

- 美國東部
- 美國東部 2
- 美國西部 2
- 美國中部
- 北歐
- 西歐
- 法國中部
- 東南亞

Microsoft 會持續在其他 Azure 區域啟用 ZRS。 如需新區域的詳細資訊，請定期查看 [Azure 服務更新](https://azure.microsoft.com/updates/)頁面。

## <a name="what-happens-when-a-zone-becomes-unavailable"></a>當區域變成無法使用時，會發生什麼事？
如果變成無法使用，您的資料仍具有復原能力。 Microsoft 建議您持續遵循暫時性錯誤處理實務，例如以指數後退法實作重試原則。 當區域無法使用時，Azure 會執行網路更新，例如 DNS 重新指向。 如果您在這些更新完成之前存取資料，可能會影響您的應用程式。

發生多個區域受到永久影響的區域性災害時，ZRS 可能無法保護資料。 相反地，如果是暫時無法使用，ZRS 可為您的資料提供復原能力。 為了防範區域性災害，Microsoft 建議使用異地備援儲存體 (GRS)。 如需 GRS 的詳細資訊，請參閱[異地備援儲存體 (GRS)：適用於 Azure 儲存體的跨區域複寫](storage-redundancy-grs.md)。

## <a name="converting-to-zrs-replication"></a>轉換成 ZRS 複寫
現在，您可以使用 Azure 入口網站或儲存體資源提供者 API 變更帳戶的備援類型，只要您正在移轉至 LRS、GRS 與 RA-GRS 或從 LRS、GRS 與 RA-GRS 移轉。 不過，使用 ZRS 涉及將單一儲存體戳記中的實體資料移動到區域內的多個戳記，並非相當簡單。 

移轉至 ZRS 或從 ZRS 移轉有兩個主要選項。 您可以將現有帳戶的資料手動複製或移動到新的 ZRS 帳戶。 您也可以要求即時移轉。 Microsoft 強烈建議您執行手動移轉，原因是不保證即時移轉何時會完成。 手動移轉路由能提供比即時移轉更大的彈性，並可以控制移轉時機。

執行手動移轉的各種不同選項如下：
- 使用現有的工具，例如 AzCopy、儲存體 SDK、可靠的協力廠商工具等
- 如果您熟悉 Hadoop 或 HDInsight，可將來源與目的地 (ZRS) 帳戶兩者連結至叢集，然後使用像是 DistCp 的工具，巨量平行處理資料複製程序
- 運用一種儲存體 SDK 類別，建置您自己的工具

不過，如果手動移轉會導致一些應用程式停機時間，而您這邊無法承受時，Microsoft 提供即時移轉選項。 即時移轉即為就地移轉，可讓您在來源與目的地儲存體戳記之間移轉您的資料時，同時繼續使用現有的儲存體帳戶。 在移轉期間，持久性與可用性 SLA 仍如同往常一般。

不過，即時移轉本身確實有一些限制。 列出如下。

- 雖然 Microsoft 會儘快處理您的即時移轉要求，但不保證何時會移轉完成。 如果您需要資料在特定時間前置入 ZRS，則應該執行手動移轉。 一般而言，您的帳戶中有越多的資料，移轉資料所花費的時間就越長。 
- 您只能使用 LRS 或 GRS 複寫從帳戶來執行即時移轉。 如果您的帳戶使用 RA-GRS，則必須先移轉到其中一種複寫類型後，再繼續進行。 這個中繼步驟可確保在移轉之前，會將 RA-GRS 提供的次要唯讀端點移除。
- 您的帳戶必須包含資料。
- 僅支援區域間移轉。 如果您想要將資料移轉至的 ZRS 帳戶位於和來源帳戶不同的區域中，則必須執行手動移轉。
- 僅支援標準儲存體帳戶類型。 您無法從進階儲存體帳戶移轉。

請透過 Azure 支援入口網站要求即時移轉。 您可以從入口網站選取想要轉換成 ZRS 的儲存體帳戶。
1. 按一下 [新增支援要求]
2. 確認 [基本]。 按 [下一步] 。 
3. 在 [問題] 區段上， 
    - 將 [嚴重性] 維持不變。
    - 問題類型 = **資料移轉**
    - 類別 = **移轉至區域內的 ZRS**
    - 標題 = **ZRS 帳戶移轉** (或描述性項目)
    - 詳細資料 = 我想要從 ___ 區域中的 [LRS、GRS] 移轉至 ZRS。 
4. 按 [下一步] 。
5. 確認 [連絡人資訊] 刀鋒視窗上的連絡人資訊是正確的。
6. 按一下 [提交] 。

支援人員隨後會與您連絡。 該人員會提供您可能需要的任何協助。 

## <a name="zrs-classic-a-legacy-option-for-block-blobs-redundancy"></a>傳統 ZRS：適用於區塊 Blob 備援的舊版選項
> [!NOTE]
> Microsoft 會在 2021 年 3 月 31 日將傳統 ZRS 帳戶除役並進行移轉。 Microsoft 會在除役之前傳送詳細資訊給傳統 ZRS 客戶。 
>
> 在某個區域中[推出](#support-coverage-and-regional-availability) ZRS 後，客戶便無法在該區域的入口網站中建立傳統 ZRS 帳戶。 目前支援使用 Microsoft PowerShell 和 Azure CLI 來建立傳統 ZRS 帳戶，直到傳統 ZRS 除役為止。

傳統 ZRS 會以非同步方式在一至兩個區域內的資料中心間複寫資料。 除非 Microsoft 開始容錯移轉至次要區域，否則可能無法使用複本。 傳統 ZRS 帳戶無法與 LRS 或 GRS 相互轉換，而且沒有計量或記錄功能。

傳統 ZRS 僅適用於一般用途 V1 (GPv1) 儲存體帳戶中的**區塊 Blob**。 如需有關儲存體帳戶的詳細資訊，請參閱 [Azure 儲存體帳戶概觀](storage-account-overview.md)。

傳統 ZRS 帳戶無法與 LRS、GRS 或 RA-GRS 帳戶互相轉換。 傳統 ZRS 帳戶也不支援計量或記錄。

若要將 ZRS 帳戶資料手動移轉至 LRS、傳統 ZRS、GRS 或 RA-GRS，或從LRS、傳統 ZRS、GRS 或 RA-GRS 移轉，請使用 AzCopy、Azure 儲存體總管、Azure PowerShell 或 Azure CLI。 您也可以使用其中一個 Azure 儲存體用戶端程式庫，自行建置移轉解決方案。

## <a name="see-also"></a>另請參閱
- [Azure 儲存體複寫](storage-redundancy.md)
- [本地備援儲存體 (LRS)：適用於 Azure 儲存體的低成本資料備援](storage-redundancy-lrs.md)
- [異地備援儲存體 (GRS)：適用於 Azure 儲存體的跨區域複寫](storage-redundancy-grs.md)