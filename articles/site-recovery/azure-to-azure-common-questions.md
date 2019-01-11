---
title: 常見問題 - 使用 Azure Site Recovery 進行從 Azure 至 Azure 的災害復原 | Microsoft Docs
description: 本文摘錄使用 Azure Site Recovery 設定災害復原 (從 Azure VM 至另一個 Azure 區域) 的常見問題
author: asgang
manager: rochakm
ms.service: site-recovery
ms.date: 12/12/2018
ms.topic: conceptual
ms.author: asgang
ms.openlocfilehash: 6fe7152e43640a809ab9f4de39b1c6b599975a20
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/02/2019
ms.locfileid: "53969942"
---
# <a name="common-questions---azure-to-azure-replication"></a>常見問題 - Azure 到 Azure 的複寫

本文將解答部署災害復原 (從 Azure VM 至另一個 Azure 區域) 的常見問題。 如果您在閱讀本文後有問題，請將問題張貼在 [Azure 復原服務論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)。


## <a name="general"></a>一般
### <a name="how-is-site-recovery-priced"></a>Site Recovery 是如何定價的？
請檢閱[Azure Site Recovery 定價](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/)詳細資料。

### <a name="how-to-configure-site-recovery-on-azure-vms-what-are-the-best-practices"></a>如何在 Azure VM 上設定 Site Recovery。 最佳做法為何？
1. [了解 Azure 至 Azure 架構](azure-to-azure-architecture.md)
2. [檢閱支援和不支援的組態](azure-to-azure-support-matrix.md)
3. [設定適用於 Azure VM 的災害復原](azure-to-azure-how-to-enable-replication.md)
4. [執行測試容錯移轉](azure-to-azure-tutorial-dr-drill.md)
5. [容錯移轉和容錯回復到主要區域](azure-to-azure-tutorial-failover-failback.md)

## <a name="replication"></a>複寫

### <a name="can-i-replicate-azure-disk-encryption-enabled-vms"></a>我可以複寫啟用 Azure 磁碟加密的 VM 嗎？
是的，您可以複寫這些 VM。 請參閱[本文](azure-to-azure-how-to-enable-replication-ade-vms.md)來複寫已啟用 Azure 磁碟加密 (ADE) 的 VM。 請注意，Azure Site Recovery 目前僅支援執行 Windows OS 及可以使用 Azure AD 應用程式進行加密的 Azure VM。

### <a name="can-i-replicate-vms-to-another-subscription"></a>我可以將 VM 複寫至另一個訂用帳戶嗎？
是的，您可以將 Azure VM 複寫到相同 Azure Active Directory 租用戶的不同訂用帳戶。
在[訂用帳戶之間](https://azure.microsoft.com/blog/cross-subscription-dr)設定 DR 很簡單。 您可以在複寫時選取另一個訂用帳戶。

### <a name="can-i-replicate-zone-pinned-azure-vms-to-another-region"></a>我是否可以將區域固定的 Azure VM 複寫到另一個區域。
是的，您可以[將區域固定的 VM 複寫到另一個區域](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region)。

### <a name="can-i-exclude-disks"></a>是否可排除磁碟嗎？

是的，您可以在使用 Power Shell 設定防護時排除磁碟。 請參閱 [owerShell 指引](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#replicate-azure-virtual-machine)來排除磁碟

###<a name="how-often-can-i-replicate-to-azure"></a>複寫到 Azure 的頻率為何？
從 Azure VM 到另一個 Azure 區域的複寫會持續執行。 請查看 [Azure 至 Azure](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-architecture#replication-process) 複寫架構來了解詳細資料。

### <a name="can-i-replicate-virtual-machines-within-a-same-region-i-need-this-to-migrate-vms"></a>我可以在相同區域內複寫虛擬機器嗎？ 我需要用此方式來遷移 VM 嗎？
您無法使用 Azure 至 Azure DR 解決方案在相同區域內複寫 VM。

### <a name="can-i-replicate-vms-to-any-azure-region"></a>我可以將 VM 複寫至任何 Azure 區域嗎？
透過 Site Recovery，您可以複寫和復原相同地理叢集內任何兩個區域之間的 VM。 地理叢集會是以資料延遲及主權範圍定義而成。 如需詳細資訊，請參閱 Site Recovery [區域支援矩陣](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support)。

### <a name="does-site-recovery-require-internet-connectivity"></a>Site Recovery 是否需要網際網路連線？

否，Site Recovery 不需要網際網路連線，但需要存取此[文章](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges)所述的 Site Recovery URL 和 IP 範圍

## <a name="replication-policy"></a>複寫原則

### <a name="what-is-a-replication-policy"></a>什麼是複寫原則？
這會定義復原點保留期歷程記錄和應用程式一致快照集頻率的設定。 根據預設，Azure Site Recovery 會對於復原點保留期使用「24 小時」的預設設定來建立新的複寫原則，並對於應用程式一致快照集頻率使用「60 分鐘」。

### <a name="what-is-crash-consistent-recovery-point"></a>什麼是損毀一致復原點？
如果 VM 在擷取快照時發生損毀或電源線從伺服器上拔開，損毀一致復原點可讓您復原磁碟上的資料。 這不包含擷取快照時記憶體內的任何資料。 現在，大部分的應用程式都可以從損毀一致快照集中復原。 對無資料庫的作業系統及檔案伺服器、DHCP 伺服器和列印伺服器之類的應用程式而言，通常使用損毀一致復原點就已足夠。

### <a name="what-is-application-consistent-recovery-point"></a>什麼是應用程式一致復原點？ 
應用程式一致復原點會從應用程式一致快照集中建立，這些快照集擷取的資料與損毀一致快照集相同，但多了記憶體內的所有資料，以及進行中的交易。 由於有這些額外的內容，因此應用程式一致快照集包含最多內容，且需要最長的執行時間。 建議您針對資料庫系統和 SQL 之類的應用程式使用應用程式一致復原點。

### <a name="how-are-recovery-points-generated-and-saved"></a>復原點要如何產生和儲存？
若要了解 Site Recovery 產生復原點的方式，可以從複寫原則的範例著手，例如有一個原則的復原點保留週期為 24 小時，而應用程式一致的頻率快照為 1 小時。
Site Recovery 會每隔 5 分鐘建立損毀一致復原點，而使用者無法變更此頻率。 因此，在過去 1 小時的時間內，使用者會有 12 個損毀一致復原點和 1 個應用程式一致復原點可選擇。 隨著時間過去，Site Recovery 會將過去 1 小時以外的所有復原點刪除，只保留每小時一個復原點。
請參閱下列螢幕擷取畫面中所示的圖例：


1. 對於過去 1 小時內的時間，每 5 分鐘就有一個復原點。
2. 對於過去 1 小時以外的時間，我們可以看到每小時中只保留一個復原點。

  ![復原點產生](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)


### <a name="how-far-back-can-i-recover"></a>最多可復原至多久之前？
您可以使用的最舊復原點為 72 小時。

### <a name="what-will-happen-if-i-have-a-replication-policy-of-24-hours-and-there-is-no-recovery-points-generation-due-to-some-issue-for-more-than-24-hours-does-my-previous-recovery-points-will-be-pruned"></a>如果我的複寫原則為 24 小時，但因為某些問題造成超過 24 小時的時間內沒有產生任何復原點，這會發生什麼事？ 我先前的復原點會遭到刪除嗎？
不會，在此情況下，Site Recovery 會保留您先前的所有復原點。 

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>在 VM 上啟用複寫之後，我要如何變更複寫原則？ 
移至 [Site Recovery 保存庫] > [Site Recovery 基礎結構] > [複寫原則]。 選取您想要編輯的原則並儲存變更。 此外，任何變更都會套用到現有的所有複寫。 

### <a name="are-all-the-recovery-points-complete-copy-of-the-vm-or-differential"></a>所有復原點都是完整的 VM 複本嗎？或是會有差異？
如果是初次複寫，所產生的第一個復原點將會擁有完整複本，而後續任何復原點都將會有差異。

### <a name="does-increasing-recovery-points-retention-windows-increases-the-storage-cost"></a>增加復原點保留週期會增加儲存體成本嗎？
是的，如果您將保留期間從 24 小時增加到 72 小時，則 Site Recovery 將需儲存另外 48 小時的復原點，而這會產生儲存體費用。 例如，如果單一復原點會有 10 GB 的變更差異，而每 GB 的成本為每個月 $0.16 美元，則每個月就會有 $1.6 * 48 美元的額外費用。

## <a name="failover"></a>容錯移轉

### <a name="is-failover-automatic"></a>容錯移轉是自動發生的嗎？

容錯移轉並非自動發生。 您可以在入口網站中按一下某個按鈕來起始容錯移轉，或是使用 Site Recovery [PowerShell](azure-to-azure-powershell.md) 來觸發容錯移轉。 

### <a name="can-i-retain-public-ip-address-after-failover"></a>我可以在容錯移轉之後保留公用 IP 位址嗎？

**容錯移轉時無法保留**實際執行應用程式的公用 IP 位址。 目標區域中必須為容錯移轉程序中產生的工作負載指派一個可用的 Azure 公用 IP 資源。 此步驟可以手動進行或使用復原方案自動完成。 請參閱[本文](https://docs.microsoft.com/azure/site-recovery/concepts-public-ip-address-with-site-recovery#public-ip-address-assignment-using-recovery-plan)來使用復原方案指派公用 IP 位址。  

### <a name="can-i-retain-private-ip-address-during-failover"></a>我可以在容錯移轉期間保留私人 IP 位址嗎？
是的，您可以保留私人 IP 位址。 根據預設，當您為 Azure VM 啟用災害復原時，Site Recovery 會根據來源資源設定建立目標資源。 針對使用靜態 IP 位址設定的 Azure VM，Site Recovery 會嘗試為目標 VM 佈建相同的 IP 位址 (如果它未在使用中)。 請參閱[本文](site-recovery-retain-ip-azure-vm-failover.md)，以在不同情況下保留私人 IP 位址。

### <a name="after-failover-the-server-does-not-have-the-same-ip-address-as-the-source-vm-why-is-it-assigned-with-a-new-ip-address"></a>容錯移轉之後，伺服器的 IP 位址與來源 VM 不同。 為什麼要指派新的 IP 位址？

Site Recovery 會在容錯移轉時盡力提供最佳 IP 位址。 如果該位址正由其他虛擬機器使用，則會將下一個可用的 IP 位址設為目標。 如需 Site Recovery 如何處理定址的完整說明，請[檢閱這篇文章](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-network-mapping#set-up-ip-addressing-for-target-vms)。

### <a name="what-does-latestlowest-rpo-recovery-points-means"></a>最新 (最低 RPO) 復原點是什麼意思？
此選項會先處理已傳送到 Site Recovery 服務的所有資料，先為每部 VM 建立復原點後再進行容錯移轉。 此選項會提供最低的 RPO (復原點目標)，因為在容錯移轉後建立的 VM 具有在觸發容錯移轉時複寫到 Site Recovery 的所有資料。

### <a name="does-latest-lowest-rpo-recovery-points-have-impact-on-failover-rto"></a>最新 (最低 RPO) 復原點對容錯移轉 RTO 有影響嗎？
有，因為 Site Recovery 會在容錯移轉之前處理所有擱置的資料，所以相較於其他項目，此選項會有較高的 RTO。

### <a name="what-does-latest-processed-option-in-recovery-points-mean"></a>復原點中的最新處理選項是什麼意思？
此選項會將計劃中所有 VM 容錯移轉到 Site Recovery 所處理的最新復原點。 若要查看特定 VM 的最新復原點，請檢查 VM 設定中的 [最新復原點]。 此選項提供低 RTO (復原時間目標)，因為無須花費時間處理未處理的資料。

### <a name="if-im-replicating-between-two-azure-regions-what-happens-if-my-primary-region-experiences-an-unexpected-outage"></a>如果我在兩個 Azure 區域之間進行複寫，當我的主要區域發生意外中斷時，會發生什麼情況？
您可以在中斷之後觸發容錯移轉。 Site Recovery 不需要來自主要區域的連線即可執行容錯移轉。

## <a name="recovery-plan"></a>復原方案

### <a name="what-is-a-recovery-plan-"></a>什麼是復原方案？
Site Recovery 中的復原方案會協調 VM 的容錯移轉。 這有助於讓復原「保持一致精確」、「可重複執行」及「自動化」。 復原方案可滿足使用者的下列需求：

- 定義一組一起進行容錯移轉的虛擬機器。
- 定義虛擬機器之間的相依性，讓應用程式能正確地啟動。
- 自動化復原搭配自訂手動動作，以便完成虛擬機器容錯移轉以外的工作。

[深入了解](site-recovery-create-recovery-plans.md) 復原計劃。

### <a name="how-does-sequencing-is-achieved-in-a-recovery-plan"></a>復原方案中的排序是如何進行的？

在復原方案中，您可以建立多個群組來進行排序。 一次進行一個群組的容錯移轉，這表示屬於相同群組的 VM 會在另一個群組之後一起進行容錯移轉。 請查看如何[使用復原方案建立應用程式模型。](https://review.docs.microsoft.com/azure/site-recovery/recovery-plan-overview?branch=pr-en-us-61681#model-apps) 

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>如何找到復原方案的 RTO？
若要檢查復原方案的 RTO，請對復原方案進行容錯移轉測試，並移至 [Site Recovery 作業]。
例如，如下所示，SAP Test Recovery Plan 用了 8 分鐘 59 秒來容錯移轉所有虛擬機器並執行指定的任何動作。

  ![com-error](./media/azure-to-azure-troubleshoot-errors/recoveryplanrto.PNG)

### <a name="can-i-add-automation-runbooks-to-the-recovery-plan"></a>我可以將自動化 Runbook 新增至復原方案嗎？
是的，您可以將 Azure 自動化 Runbook 整合至您的復原方案。 [深入了解](site-recovery-runbook-automation.md)

## <a name="reprotect-and-failback"></a>重新保護與容錯回復 

### <a name="after-doing-a-failover-from-primary-region-to-disaster-recovery-region-does-vms-in-a-dr-region-gets-protected-automatically"></a>從主要區域容錯移轉至災害復原區域後，DR 區域中的 VM 會自動受到保護嗎？
不會，當您將 Azure VM 從一個區域[容錯移轉](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback)到另一個區域時，VM 會以未受保護狀態在 DR 區域中開機。 若要將 VM 容錯回復到到主要區域，您必須[重新保護](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect)次要區域中的 VM。

### <a name="at-the-time-of-reprotection-does-site-recovery-replicate-complete-data-from-secondary-region-to-primary-region"></a>進行重新保護時，Site Recovery 會完整地將資料從次要區域複寫到主要區域嗎？
視情況而定，例如，如果來源區域 VM 存在，則只會同步來源磁碟與目標磁碟之間的變更部分。 系統會藉由比較這兩個磁碟來計算出差異，然後進行傳輸。 這通常需要幾個小時的時間才能完成。 請參閱[本文]( https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection)以進一步了解重新保護期間的細節。

### <a name="how-much-time-does-it-take-to-failback"></a>容錯回復需要耗費多久時間？
完成重新保護之後，容錯回復的時間大概與從主要區域容錯移轉到次要區域的時間一樣。 

## <a name="security"></a>安全性
### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>複寫資料會傳送到 Site Recovery 服務嗎？
否，Site Recovery 不會攔截複寫的資料，也不會擁有任何關於您虛擬機器上執行哪些項目的資訊。 只有協調複寫與容錯移轉所需的中繼資料會被傳送給 Site Recovery 服務。  
Site Recovery 已通過 ISO 27001:2013、27018、HIPAA、DPA 認證，並且正在進行 SOC2 和 FedRAMP JAB 評定程序。

### <a name="does-site-recovery-encrypt-replication"></a>Site Recovery 會將複寫加密嗎？
是，傳輸中加密和 [Azure 中的加密](https://docs.microsoft.com/azure/storage/storage-service-encryption)均受支援。

## <a name="next-steps"></a>後續步驟
* [檢閱](azure-to-azure-support-matrix.md)支援需求。
* [設定](azure-to-azure-tutorial-enable-replication.md) Azure 至 Azure 的複寫。
