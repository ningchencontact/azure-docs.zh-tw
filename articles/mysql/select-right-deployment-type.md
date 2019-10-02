---
title: 為您的適用於 MySQL 的 Azure 資料庫選取正確的部署類型
description: 本文說明在針對您的適用於 MySQL 的 Azure 資料庫繼續進行基礎結構即服務（IaaS）或平臺即服務（PaaS）之前，需要完成的考慮事項。
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 08/05/2019
ms.openlocfilehash: 7bcbf379ea8d046c8c477dc716711a6a6ffa1dc9
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/02/2019
ms.locfileid: "71813830"
---
# <a name="choose-the-right-mysql-server-option-in-azure"></a>在 Azure 中選擇正確的 MySQL 伺服器選項

有了 Azure，您的 MySQL 伺服器工作負載就可以在裝載的基礎結構 VM （IaaS）中執行，或做為託管服務（PaaS）。 PaaS 有多個部署選項，而且每個部署選項中都有服務層級。 在決定 PaaS 或 IaaS 之間時，您需要決定是否要管理資料庫、套用修補程式，以及進行備份，或您想要將這些作業委派給 Azure。</br>

根據您對該問題的答案，請考慮下列選項： <br/>

**適用於 MySQL 的 Azure 資料庫**是完全受控的 MySQL 資料庫引擎，以其穩定版本的「社區版」為基礎。 此關係資料庫即服務（DBaaS）裝載于 Azure 雲端中，屬於業界類別的平臺即服務（PaaS）。 透過 Azure 上的 MySQL 受控實例，您可以使用在使用 MySQL Server （內部部署或 Azure 虛擬機器）時需要大量設定的內建特性和功能。 當使用 MySQL 即服務時，您可以使用隨用隨付選項來相應增加或相應放大，而不會有中斷的能力。 此外，不同于獨立的 MySQL server，適用於 MySQL 的 Azure 資料庫還有額外的功能，例如內建的高可用性、智慧和管理。 <br/><br/>
**AZURE VM 上的 MySQL**屬於業界類別的基礎結構即服務（IaaS），可讓您在 azure 雲端中完全受控的虛擬機器內執行 mysql 伺服器。 所有最新版本的 MySQL 都可以安裝在 IaaS 虛擬機器上。 與適用於 MySQL 的 Azure 資料庫的最大差異在於，Azure Vm 上的 MySQL 允許對資料庫引擎進行控制。 不過，此控制是管理虛擬機器和許多 DBA 工作（例如資料庫伺服器維護/修補、復原和高可用性設計等）的額外責任成本。

下表列出這些選項之間的主要差異：

|            | **適用於 MySQL 的 Azure 資料庫** | **Azure Vm 上的 MySQL**    |
|:-------------------|:-----------------------------|:--------------------|
| **SLA**                | 提供 99.99% 可用性的 SLA| 同一個可用性設定組中有2個以上的實例，最高可達 99.95% 的可用性。 <br/>使用 Premium 儲存體的 99.9% 單一實例 VM <br/> 具有2個或更多可用性設定組中2個或多個實例的可用性區域 99.99%。<br/> 附注-[虛擬機器 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) |
| **作業系統修補**        | 自動  | 由客戶管理 |
|**MySQL 修補**     | 自動  | 由客戶管理 |
| **高可用性** | 高可用性 (HA) 模型依據的是發生節點層級中斷時的內建容錯移轉機制。 在這種情況下，服務會自動建立實例，並將儲存體附加到這個新的實例。 | 高可用性由客戶進行架構、實施、測試及維護。 這可能包括 Always On （容錯移轉叢集或群組複寫）、記錄傳送，以及異動複寫（視使用中的 MySQL 引擎版本而定）。|
| **區域冗余** | 目前不支援。 | Azure Vm 可設定為在不同的可用性區域中執行。 針對內部部署解決方案，客戶必須建立、管理及維護自己的次要資料中心。|
| **混合式案例** | 「[資料](https://docs.microsoft.com/azure/mysql/concepts-data-in-replication)輸入複寫」可讓您將外部 MySQL 伺服器的資料同步處理到適用於 MySQL 的 Azure 資料庫服務。 外部伺服器可以位於內部部署環境、虛擬機器中或其他雲端提供者所裝載的資料庫服務內。  <br/> <br/> 「[讀取複本](https://docs.microsoft.com/azure/postgresql/concepts-read-replicas)」功能可讓您將適用於 MySQL 的 Azure 資料庫伺服器（master）中的資料複寫到相同 Azure 區域內或跨區域的最多五個唯讀伺服器（複本）。 唯讀複本會使用 binlog 複寫技術以非同步方式更新。   <br/> <br/> 注意-跨區域讀取複寫目前為公開預覽狀態。| 由客戶管理 <br/>
| **備份與還原** | 會自動建立[伺服器備份](https://docs.microsoft.com/azure/mysql/concepts-backup#backups)，並將它們儲存在使用者設定的本機多餘或異地多餘儲存體中。 此服務會取得完整、差異和交易記錄備份 | 由客戶管理 |
| **監視資料庫作業** | 可讓客戶[設定](https://docs.microsoft.com/azure/mysql/concepts-monitoring)資料庫作業的警示，並在達到臨界值時採取行動。 | 由客戶管理 |
| **先進的執行緒保護** | 提供[先進的威脅防護](https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal)，它會偵測異常活動，指出不尋常且可能有害的存取或惡意探索資料庫嘗試。 | 客戶必須自行建立。
| **備份（嚴重損壞修復）** | 將使用者設定的自動備份儲存在[本機的多餘或異地多餘儲存體](https://docs.microsoft.com/azure/mysql/howto-restore-server-portal)中。 備份也可以用來將伺服器還原至某個時間點。 保留期限可設定為7-35 天。 您可以使用 Azure 入口網站來完成還原。 <br/> | 完全由客戶管理，包括但不限於排程、測試、封存、儲存和保留。 另一個選項是使用 Azure 復原服務保存庫來備份 Vm 上的 Azure Vm 和資料庫（處於預覽狀態）。 |
| **效能建議** | 根據使用量遙測，為客戶提供主動式[效能建議](https://techcommunity.microsoft.com/t5/Azure-Database-for-MySQL/Azure-brings-intelligence-and-high-performance-to-Azure-Database/ba-p/769110)，以協助優化工作負載。 | 由客戶管理 |


## <a name="business-motivations-for-choosing-paas-or-iaas"></a>選擇 PaaS 或 IaaS 的商業動機

有數個因素會影響您決定選擇 PaaS 或 IaaS 來裝載您的 MySQL 資料庫：

### <a name="cost"></a>成本

無論您是現金或小組在嚴格預算限制之下運作的錢，在決定裝載資料庫的最佳解決方案時，有限的資金通常是主要的考慮。 本節說明 azure 中關於 Azure Vm 上適用於 MySQL 的 Azure 資料庫和 MySQL 的計費和授權基本概念：

#### <a name="billing"></a>帳務

目前，適用於 MySQL 的 Azure 資料庫在數個層級中都是以服務的形式提供，且資源的價格不同，而每小時都會以固定費率計費。 如需目前支援的服務層級、計算大小和儲存體數量的最新資訊，請參閱[vCore 為基礎的購買模型](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers)。 您可以動態調整服務層級和計算大小，以滿足應用程式的不同輸送量需求。 以一般[資料傳輸費率](https://azure.microsoft.com/pricing/details/data-transfers/)向您收取連出網際網路流量的費用。

有了適用於 MySQL 的 Azure 資料庫，Microsoft 便會自動設定、修補和升級資料庫軟體，以降低您的系統管理成本。 此外，它 [內建的備份](https://docs.microsoft.com/azure/mysql/concepts-backup) 功能可協助您達到有效節省成本，尤其是當您擁有為數眾多的資料庫時效果更為顯著。 透過 Azure Vm 上的 MySQL，您可以選擇並執行任何 MySQL 版本。 無論您使用的 MySQL 版本為何，您都必須支付布建 VM 的費用，以及用於 MySQL 的特定授權類型的成本。

適用於 MySQL 的 Azure 資料庫為任何種類的節點層級中斷提供內建的高可用性，而且仍會維護服務的 99.99% SLA。 不過，針對 Vm 內的資料庫高可用性（HA），客戶應流覽 MySQL 資料庫上可用的高可用性選項，例如[mysql](https://dev.mysql.com/doc/refman/8.0/en/replication.html)複寫。 使用支援的高可用性選項並不提供額外的 SLA，但可讓您以額外的成本和管理額外負荷達到 > 99.99% 的資料庫可用性。

如需價格的詳細資訊，請參閱下列資源：
* [適用於 MySQL 的 Azure 資料庫定價](https://azure.microsoft.com/pricing/details/mysql/)
* [虛擬機器定價](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Azure 價格計算機](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>管理

對於許多企業而言，轉換至雲端服務的決策，與卸載系統管理的複雜性有關。 透過 IaaS 和 PaaS，Microsoft 會管理基礎結構並自動複寫所有資料，以提供嚴重損壞修復、設定和升級資料庫軟體、管理負載平衡，以及在發生錯誤時進行透明故障切換伺服器失敗。

* **使用適用於 MySQL 的 Azure 資料庫**，您可以繼續管理您的資料庫，但不再需要管理資料庫引擎、作業系統或硬體。 您可以繼續管理的項目範例包括資料庫和登入、索引和查詢微調，以及稽核和安全性。 此外，將高可用性設定為另一個資料中心需要最少或無設定或管理。<br/><br/>
* 透過**Azure vm 上的 MySQL**，您可以完全掌控作業系統和 MySQL 伺服器實例設定。 有了 VM，您可以決定何時更新/升級作業系統與資料庫軟體，以及何時安裝任何其他軟體，例如防毒程式。 提供某些自動化功能以大幅簡化修補、備份及高可用性。 此外，您還可以控制 VM 的大小、磁碟的數目及其儲存體組態。 如需相關資訊，請參閱 Azure 的虛擬機器和雲端服務大小。

### <a name="time-to-move-to-azure-br"></a>移至 Azure 的時間 <br/>
* 當新解決方案的開發人員生產力和快速上市時間很重要時，**適用於 MySQL 的 Azure 資料庫**是雲端設計應用程式的正確解決方案。 透過程式設計 DBA 的功能，服務非常適合雲端架構設計人員和開發人員，因為它可降低管理基礎作業系統和資料庫的需求。<br/><br/>
* **Azure vm 上的 mysql**非常適合需要 mysql 資料庫的現有或新應用程式，或在 Windows/Linux 上存取 mysql 資料庫中的功能，而且您想要避免取得新內部部署硬體的時間和費用。 此選項也非常適合用來將現有的內部部署應用程式和資料庫就地遷移至 Azure，適用于適用於 MySQL 的 Azure 資料庫實例不適用的情況。 由於不需要變更簡報、應用程式和資料層，因此您可以節省重新架構現有解決方案的時間和預算。 相反地，您可以專注于將所有解決方案遷移至 Azure，並解決 Azure 平臺可能需要的一些效能優化。

## <a name="next-steps"></a>後續步驟

* 請參閱[適用於 MySQL 的 Azure 資料庫定價](https://azure.microsoft.com/pricing/details/mysql/)
* 從[建立您的第一部伺服器](https://review.docs.microsoft.com/azure/mysql/quickstart-create-mysql-server-database-using-azure-portal)開始。

