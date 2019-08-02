---
title: Azure 澳大利亞的備份和嚴重損壞修復
description: 適用于澳大利亞政府機關 Microsoft Azure 中的備份和嚴重損壞修復, 因為它與 .ASD 的第8項相關。
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 078918cbfeffb1ff5f3626b5add96bc6622a90a7
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68571519"
---
# <a name="backup-and-disaster-recovery-in-azure-australia"></a>Azure 澳大利亞的備份和嚴重損壞修復

具有支援基礎結構的備份和嚴重損壞修復計畫, 對於所有組織而言都非常重要。 具有備份解決方案的重要性會透過其包含在[澳大利亞網路資訊安全中心的基本 8](https://acsc.gov.au/publications/protect/essential-eight-explained.htm)來反白顯示。

Microsoft Azure 提供兩個可啟用恢復功能的服務:Azure 備份和 Azure Site Recovery。 這些服務可讓您在各種不同的設計案例中, 保護內部部署和雲端中的資料。 Azure 備份和 Azure Site Recovery 都使用一般的儲存體和管理資源: Azure 復原服務保存庫。 這個保存庫用來管理、監視和隔離 Azure 備份和 Azure Site Recovery 資料。

本文詳細說明使用[澳大利亞信號情報局 (.asd) 資訊安全手冊 (ISM) 控制項](https://acsc.gov.au/infosec/ism/index.htm)來執行 Azure 備份和 Azure Site Recovery 的主要設計項目。

## <a name="azure-backup"></a>Azure 備份

![Azure 備份](media/backup-overview.png)

Azure 備份類似傳統內部部署備份解決方案, 可讓您同時備份內部部署和 Azure 託管的資料。 Azure 備份可以用來將下列資料類型備份至 Azure:

* 檔案和資料夾
* 支援的 Windows 和 Linux 作業系統裝載于:
  * Hyper-v 和 VMWare 虛擬機器
  * 實體硬體
* 支援的 Microsoft 應用程式

### <a name="azure-site-recovery"></a>Azure Site Recovery

![Azure Site Recovery](media/asr-overview.png)

Azure Site Recovery 會複寫由單一虛擬機器或多層式應用程式所組成的工作負載。 從內部部署到 Azure、在 Azure 區域之間, 或在 Azure Site Recovery 協調的內部部署位置之間, 都支援複寫。 內部部署虛擬機器可以複寫至 Azure, 或複寫至支援的內部部署管理元件。 一旦設定之後, Azure Site Recovery 會協調複寫、容錯回復和容錯回復。

## <a name="key-design-considerations"></a>主要設計考慮

在執行備份或嚴重損壞修復解決方案時, 建議的解決方案需要考慮:

* 要捕獲的資料範圍和數量
* 將保留資料的時間長度
* 如何安全地儲存和管理此資料
* 儲存資料的地理位置
* 例行的系統測試程式

ISM 提供設計解決方案時應進行之安全性考慮的指導方針。 Microsoft Azure 提供解決這些安全性考慮的方法。

### <a name="data-sovereignty"></a>資料主權

組織必須確保在使用以雲端為基礎的儲存位置時, 會維護資料主權。 Azure 原則提供方法來限制可以建立 Azure 資源的允許位置。 內建的 Azure 原則「允許的位置」是用來確保在指派的 Azure 原則範圍內建立的任何 Azure 資源, 只能在指定的地理位置中建立。

適用于 Azure 資源之地理限制的 Azure 原則專案如下:

* allowedLocations
* allowedSingleLocation

這些原則可讓 Azure 系統管理員限制建立一個或甚至是單一地理位置的已命名位置清單。

### <a name="redundant-and-geographically-dispersed-storage"></a>多餘和地理位置分散的儲存空間

儲存在 Azure 復原服務保存庫中的資料一律會儲存在多餘的儲存體上。 根據預設, 復原服務保存庫會使用 Azure 地理位置多餘的儲存體 (GRS)。 使用 GRS 儲存的資料會複寫至復原服務保存庫[次要配對區域](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)中的其他 Azure 資料中心。 此複寫的資料會儲存為唯讀, 而且只有在有 Azure 容錯移轉事件時, 才會成為可寫入。 在 Azure 資料中心內, 會在不同的容錯網域和升級網域之間複寫資料, 以將硬體或維修中斷的機會降到最低。 GRS 每年至少提供 99.99999999999999% 的可用性。

Azure 復原服務保存庫可設定為利用本機的多餘儲存體 (LRS)。 LRS 是成本較低的儲存體選項, 可降低可用性的取捨。 此冗余模型會在不同的容錯網域和升級網域之間使用相同的複寫, 但不會在地理區域之間複寫。 位在 LRS 儲存體上的資料, 而不是 GRS 的復原能力, 每年仍提供至少 99.999999999% 的可用性。

不同于傳統的異地儲存技術 (像是磁帶媒體), 系統會自動建立額外的資料複本, 而不需要任何額外的系統管理負擔。

### <a name="restricted-access-and-activity-monitoring"></a>限制存取與活動監視

備份資料必須受到保護, 避免損毀、修改和未核准的刪除。 Azure 備份和 Azure Site Recovery 都會使用一般的 Azure 管理網狀架構。 此網狀架構會針對位於 Azure 內的資源, 提供詳細的審核、記錄和角色型存取控制 (RBAC)。 備份資料的存取權可以限制為選取系統管理人員, 而且可以記錄和審核所有牽涉到備份資料的動作。

Azure 備份和 Azure Site Recovery 都有內建的記錄和報告功能。 在備份或複寫期間發生的任何問題, 都會向使用 Azure 管理網狀架構的系統管理員回報。

Azure 復原服務保存庫也具有下列額外的資料安全性措施:

* 在刪除作業發生後, 備份資料會保留14天
* 重大作業的警示和通知, 例如「停止包含刪除資料的備份」
* 關鍵作業的安全性 PIN 需求
* 保留範圍的最小值檢查已準備就緒

這些最低保留範圍的檢查包括:

* 針對每日保留, 至少七天保留期
* 若為每週保留, 至少四周的保留期
* 針對每月保留期, 最少三個月的保留期
* 針對每年保留, 最少一年的保留期

所有儲存在 Azure 內的備份資料, 都會使用 Azure 的儲存體服務加密 (SSE) 進行待用加密。 預設會針對所有新的和現有的儲存體帳戶啟用這項功能, 且無法停用。 加密的資料會在抓取期間自動解密。 根據預設, 使用 SSE 加密的資料會使用由 Microsoft 所提供和管理的金鑰來加密。 組織可以選擇提供和管理自己的加密金鑰, 以與 SSE 搭配使用。 這會為加密資料提供選擇性的額外安全性層級。 此金鑰可由客戶在內部部署或在 Azure 金鑰保存庫中安全地儲存。

### <a name="secure-data-transport"></a>保護資料傳輸

Azure 備份使用 AES 256 在傳輸中加密的資料。 當第一次設定備份時, 會使用系統管理人員所建立的複雜密碼來保護此資料。 Microsoft 無法存取此複雜密碼, 這表示客戶必須確保安全地儲存此複雜密碼。 然後, 資料傳輸會透過安全的 HTTPS 連線, 在內部部署環境與 Azure 復原服務保存庫之間進行。  然後, 復原服務保存庫中的資料會使用 Azure SSE 進行待用加密。

Azure Site Recovery 資料也一律會在傳輸過程中加密。 所有複寫的資料都會使用 HTTPS 和 TLS 安全地傳輸至 Azure。 當 Azure 客戶使用 ExpressRoute 連線來連線到 Azure 時, Azure Site Recovery 資料會透過此私人連線傳送。  當 Azure 客戶使用 VPN 連線來連線到 Azure 時, 資料會透過網際網路安全地在內部部署和復原服務保存庫之間進行複寫。

這種安全的網路資料傳輸會移除管理傳統異地備份儲存體解決方案 (例如磁帶媒體) 的安全性風險和緩和需求。

### <a name="data-retention-periods"></a>資料保留週期

建議使用最短的備份保留期限3個月, 不過, 通常需要較長的保留期。 Azure 備份最多可提供9999個備份複本。 如果每日取得受保護實例的單一 Azure 備份, 這可讓您保留27年的每日備份。 受保護實例的個別每月備份允許833年的保留期。 當備份資料已過時, 且在一段時間內保留較不細微的備份時, 備份資料的保留時間總計會成長。  Azure 不會限制資料可以保留在 Azure 復原服務保存庫中的時間長度, 僅限每個實例的備份總數。 從舊的或新的備份還原時, 也沒有任何效能上的差異, 每次還原都會花費相同的時間量。

Azure 復原服務保存庫有一些預設的備份和保留原則。  系統管理人員也可以建立自訂的備份和保留原則。

![Azure 備份原則](media/create-policy.png)

若要設定 Azure 備份和保留原則, 必須在備份頻率與長期保留需求之間取得平衡。

### <a name="backup-and-restore-testing"></a>備份與還原測試

ISM 建議測試備份資料, 以確保當需要還原或容錯移轉時, 受保護的資料是有效的。 Azure 備份和 Azure Site Recovery 也會提供在備份或複寫受保護的資料後進行測試的功能。 Azure 備份所管理的資料可以還原到已命名的位置, 然後就可以驗證資料的一致性。

Azure Site Recovery 具有可執行容錯移轉測試的內建功能。 複寫至復原服務保存庫的工作負載可以還原至提名的 Azure 環境。 目標還原環境可以完全與任何生產環境隔離, 以確保在執行測試時, 不會影響生產系統。 測試完成之後, 測試環境和所有資源都可以立即刪除, 以降低營運成本。

您可以使用 Azure 平臺內建的 Azure 自動化服務來自動化容錯移轉測試和驗證。 這可讓容錯移轉測試排程為自動進行, 以確保資料成功複寫到 Azure。

## <a name="next-steps"></a>後續步驟

請參閱[使用 Azure 原則確保安全性](azure-policy.md)的文章。
