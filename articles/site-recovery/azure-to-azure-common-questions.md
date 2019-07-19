---
title: 使用 Azure Site Recovery 的 Azure 到 Azure 嚴重損壞修復的常見問題
description: 本文將使用 Azure Site Recovery, 回答有關 Azure Vm 至另一個 Azure 區域的嚴重損壞修復常見問題
author: asgang
manager: rochakm
ms.service: site-recovery
ms.date: 04/29/2019
ms.topic: conceptual
ms.author: asgang
ms.openlocfilehash: d479a568ddeac29be88d0709b7544ba645274afa
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875667"
---
# <a name="common-questions-azure-to-azure-disaster-recovery"></a>常見問題：Azure 至 Azure 災害復原

本文提供有關使用[Site Recovery](site-recovery-overview.md)將 Azure vm 嚴重損壞修復至另一個 Azure 區域的常見問題的解答。 


## <a name="general"></a>一般

### <a name="how-is-site-recovery-priced"></a>Site Recovery 是如何定價的？
請檢閱[Azure Site Recovery 定價](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/)詳細資料。
### <a name="how-does-the-free-tier-for-azure-site-recovery-work"></a>Azure Site Recovery 的免費層如何運作？
每個受 Azure Site Recovery 保護的執行個體，在受保護的前 31 天皆為免費。 自第 32 天起，就會依上述費率，針對為該執行個體提供的保護進行收費。
### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>在這前 31 天，我是否須負擔任何其他 Azure 費用？
是，即使受保護的執行個體前 31 天的 Azure Site Recovery 免費，您仍有可能需要負擔 Azure 儲存體、儲存體交易與資料傳輸的費用。 復原的虛擬機器也可能會產生 Azure 計算費用。 在[這裡](https://azure.microsoft.com/pricing/details/site-recovery)取得有關定價的完整詳細資料

### <a name="where-can-i-find-best-practices-for-azure-vm-disaster-recovery"></a>哪裡可以找到 Azure VM 嚴重損壞修復的最佳做法？ 
1. [了解 Azure 至 Azure 架構](azure-to-azure-architecture.md)
2. [檢閱支援和不支援的組態](azure-to-azure-support-matrix.md)
3. [設定適用於 Azure VM 的災害復原](azure-to-azure-how-to-enable-replication.md)
4. [執行測試容錯移轉](azure-to-azure-tutorial-dr-drill.md)
5. [容錯移轉及容錯回復至主要區域](azure-to-azure-tutorial-failover-failback.md)

### <a name="how-is-capacity-guaranteed-in-the-target-region"></a>如何在目的地區域中保證容量？
Site Recovery 小組會與 Azure 容量管理小組合作, 以規劃足夠的基礎結構容量, 並協助確保在起始容錯移轉時, 會成功地將受 Site Recovery 保護的 Vm 部署到目的地區域。

## <a name="replication"></a>複寫

### <a name="can-i-replicate-vms-enabled-through-azure-disk-encryption"></a>我可以複寫透過 Azure 磁碟加密啟用的 VM 嗎？
是的，您可以複寫這些 VM。 請參閱[將已啟用 Azure 磁碟加密 (ADE) 的虛擬機器複寫到另一個 Azure 區域](azure-to-azure-how-to-enable-replication-ade-vms.md)一文。 Azure Site Recovery 目前僅支援執行 Windows OS 及可以使用 Azure Active Directory (Azure AD) 應用程式進行加密的 Azure VM。

### <a name="can-i-replicate-vms-to-another-subscription"></a>我可以將 VM 複寫至另一個訂用帳戶嗎？
是的，您可以將 Azure VM 複寫到相同 Azure AD 租用戶內的不同訂用帳戶。
在[訂用帳戶之間](https://azure.microsoft.com/blog/cross-subscription-dr)設定 DR 很簡單。 您可以在複寫時選取另一個訂用帳戶。

### <a name="can-i-replicate-zone-pinned-azure-vms-to-another-region"></a>我是否可以將區域固定的 Azure VM 複寫到另一個區域？
是的，您可以[將區域固定的 VM 複寫到另一個區域](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region)。

### <a name="can-i-exclude-disks"></a>是否可排除磁碟嗎？

是的，您可以使用 Power Shell 在設定防護時排除磁碟。 如需詳細資訊, 請參閱[文章](azure-to-azure-exclude-disks.md)

### <a name="can-i-add-new-disks-to-replicated-vms-and-enable-replication-for-them"></a>我可以將新磁片新增至複寫的 Vm, 並為它們啟用複寫嗎？

是, 具有受控磁片的 Azure Vm 可支援此情況。 當您將新的磁片新增至已啟用複寫的 Azure VM 時, VM 的複寫健康情況會顯示警告, 並說明如何指定 VM 上的一或多個磁片可供保護。 您可以為新增的磁片啟用複寫。
- 如果您為新增的磁片啟用保護, 在初始複寫之後, 警告將會消失。
- 如果您選擇不啟用磁片的複寫, 您可以選擇關閉警告。
- 當您故障切換要新增磁片並為其啟用複寫的 VM 時, 複寫點會顯示可供復原的磁片。 例如，如果 VM 有單一磁碟，而且您新增磁碟，則新增磁碟之前建立的複寫點就會顯示複寫點包含「1 個磁碟，共 2 個」。

Site Recovery 不支援從複寫的 VM 對磁片進行「熱移除」。 如果您移除 VM 磁片, 則必須停用 VM 的複寫, 然後再重新啟用。


### <a name="how-often-can-i-replicate-to-azure"></a>複寫到 Azure 的頻率為何？
當您從 Azure VM 複寫到另一個 Azure 區域時，會以持續方式執行複寫。 如需詳細資訊，請參閱 [Azure 至 Azure 複寫架構](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-architecture#replication-process)。

### <a name="can-i-replicate-virtual-machines-within-a-region-i-need-this-to-migrate-vms"></a>我是否可以在一個區域內複寫虛擬機器？ 我需要以此方式移轉 VM。
您無法使用 Azure 至 Azure DR 解決方案在一個區域內複寫 VM。

### <a name="can-i-replicate-vms-to-any-azure-region"></a>我可以將 VM 複寫至任何 Azure 區域嗎？
透過 Site Recovery，您可以複寫和復原相同地理叢集內任何兩個區域之間的 VM。 定義地理叢集時皆已考量資料延遲和主權範圍。 如需詳細資訊，請參閱 Site Recovery [區域支援對照表](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support)。

### <a name="does-site-recovery-require-internet-connectivity"></a>Site Recovery 是否需要網際網路連線？

否，Site Recovery 不需要網際網路連線能力。 但它確實需要能夠存取 Site Recovery URL 和 IP 範圍，如[這篇文章](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges)所述。

### <a name="can-i-replicate-the-application-having-separate-resource-group-for-separate-tiers"></a>是否可以複寫有個別層之個別資源群組的應用程式？
是，您可以複寫應用程式，並保持災害復原設定也在個別資源群組。
例如，如果您的應用程式的每層應用程式、DB 和 Web 都在個別資源群組中，則您需要按[複寫精靈](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication#enable-replication)三次來保護所有層。 Site Recovery 會在三個不同的資源群組中複寫這三個層級。

## <a name="replication-policy"></a>複寫原則

### <a name="what-is-a-replication-policy"></a>什麼是複寫原則？
它定義了復原點保留期歷程記錄和應用程式一致快照集頻率的設定。 Azure Site Recovery 預設會建立具有下列預設設定的新複寫原則：

* 復原點的保留歷程記錄為 24 小時。
* 應用程式一致快照集的頻率為 60 分鐘。

[深入了解](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#configure-replication-settings)。

### <a name="what-is-a-crash-consistent-recovery-point"></a>什麼是當機時保持一致復原點？
當機時保持一致復原點會呈現就像建立快照集時 VM 發生當機或從伺服器拔掉電源線時一樣的磁碟上資料。 這不包含擷取快照時記憶體內的任何資料。

現今，大多數應用程式都可以妥善地從當機時保持一致快照集復原。 對無資料庫作業系統及檔案伺服器、DHCP 伺服器和列印伺服器之類的應用程式而言，通常使用當機時保持一致復原點就已足夠。

### <a name="what-is-the-frequency-of-crash-consistent-recovery-point-generation"></a>當機時保持一致復原點產生的頻率為何？
Site Recovery 會每 5 分鐘建立一次當機時保持一致復原點。

### <a name="what-is-an-application-consistent-recovery-point"></a>什麼是應用程式一致復原點？
應用程式一致復原點是從應用程式一致快照集建立的。 應用程式一致快照集所擷取的資料與當機時保持一致復原點相同，但多了記憶體內的所有資料，以及所有進行中的交易。
由於有這些額外的內容，因此應用程式一致快照集包含最多內容，且需要最長的執行時間。 建議您針對資料庫作業系統和 SQL Server 之類的應用程式，使用應用程式一致復原點。

### <a name="what-is-the-impact-of-application-consistent-recovery-points-on-application-performance"></a>應用程式一致復原點對應用程式效能有何影響？
請考慮到應用程式一致復原點為了靜止應用程式，它會擷取記憶體中和處理序中架構所需的所有資料。 如果工作負載已經非常忙碌，若非常頻繁地這麼做，可能會有效能影響。 針對應用程式一致復原點，對於非資料庫工作負載通常建議不要使用低頻率，甚至對於資料庫工作負載 1 小時就足夠了。

### <a name="what-is-the-minimum-frequency-of-application-consistent-recovery-point-generation"></a>應用程式一致復原點產生的最小頻率為何？
Site Recovery 可以建立應用程式一致復原點, 且最小頻率為1小時。

### <a name="how-are-recovery-points-generated-and-saved"></a>復原點要如何產生和儲存？
為了了解 Site Recovery 如何產生復原點，我們將以一個複寫原則為例，此複寫原則的復原點保留週期為 24 小時，而應用程式一致頻率快照集為 1 小時。

Site Recovery 會每 5 分鐘建立一次當機時保持一致復原點。 使用者無法變更此頻率。 因此，在過去 1 小時的時間內，使用者會有 12 個當機時保持一致復原點和 1 個應用程式一致復原點可供選擇。 隨著時間過去，Site Recovery 會將過去 1 小時以外的所有復原點剪除，而只儲存每小時 1 個復原點。

以下螢幕擷取畫面說明此範例。 在螢幕擷取畫面中：

1. 對於過去 1 小時內的時間，每 5 分鐘就有一個復原點。
2. 對於超出過去 1 小時的時間，Site Recovery 則只會保留 1 個復原點。

   ![產生的復原點清單](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)


### <a name="how-far-back-can-i-recover"></a>最多可復原至多久之前？
您可以使用的最舊復原點為 72 小時。

### <a name="what-will-happen-if-i-have-a-replication-policy-of-24-hours-and-a-problem-prevents-site-recovery-from-generating-recovery-points-for-more-than-24-hours-will-my-previous-recovery-points-be-lost"></a>如果我的複寫原則為 24 小時，但某個問題導致 Site Recovery 超過 24 小時無法產生復原點，會發生什麼情況？ 我先前的復原點是否會遺失？
不會，Site Recovery 會保留您先前所有的復原點。 根據復原點保留期間 (在此案例中為 24 小時)，只有在新的復原點產生時，Site Recovery 才會替換掉最舊的復原點。 在此情況下，由於不會因某些問題而產生任何新的復原點，因此在到達保留期間後，舊的復原點仍將維持不變。

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>在 VM 上啟用複寫之後，我要如何變更複寫原則？
移至 [Site Recovery 保存庫]   > [Site Recovery 基礎結構]   > [複寫原則]  。 選取您想要編輯的原則，然後儲存變更。 此外，任何變更都會套用到現有的所有複寫。

### <a name="are-all-the-recovery-points-a-complete-copy-of-the-vm-or-a-differential"></a>所有復原點都是 VM 的完整複本？還是會有差異？
所產生的第一個復原點會具有完整複本。 所有後續復原點則具有差異變更。

### <a name="does-increasing-the-retention-period-of-recovery-points-increase-the-storage-cost"></a>增長復原點的保留週期是否會增加儲存體成本？
是的。 如果您將保留週期從 24 小時增加到 72 小時，Site Recovery 將把復原點再多儲存 48 小時。 增加的時間將產生儲存體費用。 例如，如果單一復原點有 10 GB 的差異變更，而每 GB 的成本為每個月 $0.16 美元，則每個月就會有 $1.6 * 48 美元的額外費用。

## <a name="multi-vm-consistency"></a>多 VM 一致性

### <a name="what-is-multi-vm-consistency"></a>什麼是多 VM 一致性？
這意謂著確保復原點在所有複寫的虛擬機器之間都保持一致。
Site Recovery 提供 [多 VM 一致性] 選項，當您選取此選項時，會建立一個複寫群組將屬於此群組的所有機器一起複寫。
所有虛擬機器在容錯移轉後，都會有共用的當機時保持一致和應用程式一致復原點。
請瀏覽教學課程來[啟用多 VM 一致性](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#enable-replication-for-a-vm)。

### <a name="can-i-failover-single-virtual-machine-within-a-multi-vm-consistency-replication-group"></a>我是否可以容錯移轉位於多 VM 一致性複寫群組內的單一虛擬機器？
選取 [多 VM 一致性] 選項後，即表示應用程式與群組內的所有虛擬機器相依。 因此，不允許進行單一虛擬機器容錯移轉。

### <a name="how-many-virtual-machines-can-i-replicate-as-a-part-of-a-multi-vm-consistency-replication-group"></a>我可以透過一個多 VM 一致性複寫群組複寫多少部虛擬機器？
您可以透過一個複寫群組一起複寫 16 部虛擬機器。

### <a name="when-should-i-enable-multi-vm-consistency-"></a>何時應該啟用多 VM 一致性？
由於多 VM 一致性會耗用大量 CPU，因此啟用此功能可能會影響工作負載校能。 應該只有在機器執行相同工作負載，且多部機器之間需要一致性時，才使用此功能。 例如，如果您的應用程式中有兩個 SQL Server 執行個體和兩個 Web 伺服器，則應該只針對 SQL Server 執行個體使用多 VM 一致性。


## <a name="failover"></a>容錯移轉

### <a name="how-is-capacity-assured-in-target-region-for-azure-vms"></a>如何在 Azure Vm 的目的地區域中保證容量？
Site Recovery 小組會與 Azure 容量管理小組合作, 以規劃足夠的基礎結構容量, 以協助確保在起始容錯移轉時, 在目的地區域中成功部署已啟用用於嚴重損壞修復的 Vm。

### <a name="is-failover-automatic"></a>容錯移轉是自動發生的嗎？

容錯移轉並非自動發生。 您可以在入口網站中按一下適當按鈕來開始容錯移轉，或是使用 [PowerShell](azure-to-azure-powershell.md) 來觸發容錯移轉。

### <a name="can-i-retain-a-public-ip-address-after-failover"></a>我可以在容錯移轉之後保留公用 IP 位址嗎？

在容錯移轉之後, 無法保留實際執行應用程式的公用 IP 位址。
- 針對容錯移轉程序中產生的工作負載，必須指派一個目標區域中可用的 Azure 公用 IP 資源。
- 您可以手動執行此動作, 或使用復原方案將它自動化。
- 瞭解如何[在容錯移轉之後設定公用 IP 位址](concepts-public-ip-address-with-site-recovery.md#public-ip-address-assignment-using-recovery-plan)。  

### <a name="can-i-retain-a-private-ip-address-during-failover"></a>我可以在容錯移轉期間保留私人 IP 位址嗎？
是, 您可以保留私人 IP 位址。 根據預設，當您為 Azure VM 啟用災害復原時，Site Recovery 會根據來源資源設定建立目標資源。 -若為使用靜態 IP 位址設定的 Azure Vm, Site Recovery 會嘗試針對目標 VM 布建相同的 IP 位址 (如果未使用的話)。
瞭解如何[在容錯移轉期間保留 IP 位址](site-recovery-retain-ip-azure-vm-failover.md)。

### <a name="after-failover-why-is-the-server-assigned-a-new-ip-address"></a>在容錯移轉之後, 為什麼伺服器會指派新的 IP 位址？

Site Recovery 會在容錯移轉時嘗試提供 IP 位址。 如果另一部虛擬機器正在使用該位址，Site Recovery 就會將下一個可用的 IP 位址設定為目標。
深入瞭解如何[設定 vnet 的網路對應和 IP 位址](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms)。

### <a name="what-are-latest-lowest-rpo-recovery-points"></a>什麼是**最新 (最低 RPO)** 復原點？
[最新 (最低 RPO)]  選項會先處理已傳送到 Site Recovery 服務的所有資料來為每部 VM 建立復原點，然後才進行容錯移轉。 此選項會提供最低的復原點目標 (RPO)，因為在容錯移轉後建立的 VM 具有在觸發容錯移轉時複寫到 Site Recovery 的所有資料。

### <a name="do-latest-lowest-rpo-recovery-points-have-an-impact-on-failover-rto"></a>**最新 (最低 RPO)** 復原點對容錯移轉 RTO 是否有影響？
是的。 Site Recovery 會在容錯移轉前處理所有待處理的資料，因此與其他選項相比，此選項具有較高的復原時間目標 (RTO)。

### <a name="what-does-the-latest-processed-option-in-recovery-points-mean"></a>復原點中的**最新處理**選項是什麼意思？
[最近處理]  選項會將方案中的所有 VM 容錯移轉至 Site Recovery 所處理的最新復原點。 若要查看特定 VM 的最新復原點，請檢查 VM 設定中的 [最新復原點]  。 此選項提供低 RTO，因為不會花費任何時間來處理未處理的資料。

### <a name="what-happens-if-my-primary-region-experiences-an-unexpected-outage"></a>如果我的主要區域遇到非預期的中斷, 會發生什麼事？
您可以在中斷之後觸發容錯移轉。 Site Recovery 不需要來自主要區域的連線即可執行容錯移轉。

### <a name="what-is-a-rto-of-a-vm-failover-"></a>VM 容錯移轉的 RTO 是什麼？
Site Recovery 有 [2 小時的 RTO SLA](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/)。 不過在大部分情況下，Site Recovery 會在幾分鐘內容錯移轉虛擬機器。 若要計算 RTO，您可以移置容錯移轉作業查看它啟動 VM 所花的時間。 針對復原計畫 RTO，請參閱下節。

## <a name="recovery-plans"></a>復原方案

### <a name="what-is-a-recovery-plan"></a>什麼是復原方案？
Site Recovery 中的復原方案會協調 VM 的容錯移轉復原。 這有助於讓復原「保持一致精確」、「可重複執行」及「自動化」。 復原方案可滿足使用者的下列需求：

- 定義一組會一起容錯移轉的虛擬機器
- 定義虛擬機器間的相依性，讓應用程式能夠正確地啟動
- 搭配自訂的手動動作自動執行復原，以完成虛擬機器容錯移轉以外的工作

[深入了解](site-recovery-create-recovery-plans.md) 復原計劃。

### <a name="how-is-sequencing-achieved-in-a-recovery-plan"></a>復原方案中是如何進行排序的？

在復原方案中，您可以建立多個群組來進行排序。 每個群組會各在一個時間容錯移轉。 屬於相同群組的 VM 會一起容錯移轉，後面再接著另一個群組。 若要了解如何使用復原方案來建立應用程式模型，請參閱[關於復原方案](recovery-plan-overview.md#model-apps)。

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>如何找到復原方案的 RTO？
若要檢查復原方案的 RTO，請為復原方案進行測試容錯移轉，然後移至 [Site Recovery 作業]  。
在以下範例中，名為 SAPTestRecoveryPlan 的作業花費了 8 分鐘 59 秒來容錯移轉所有虛擬機器及執行指定的動作。

![Site Recovery 作業清單](./media/azure-to-azure-troubleshoot-errors/recoveryplanrto.PNG)

### <a name="can-i-add-automation-runbooks-to-the-recovery-plan"></a>我是否可以將自動化 Runbook 新增至復原方案？
是的，您可以將「Azure 自動化」Runbook 整合至您的復原方案。 [深入了解](site-recovery-runbook-automation.md)。

## <a name="reprotection-and-failback"></a>重新保護和容錯回復

### <a name="after-a-failover-from-the-primary-region-to-a-disaster-recovery-region-are-vms-in-a-dr-region-protected-automatically"></a>從主要區域容錯移轉至災害復原區域後，DR 區域中的 VM 是否會自動受到保護？
資料分割 當您將 Azure VM 從一個區域[容錯移轉](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback)至另一個區域時，VM 會在 DR 區域中以未受保護的狀態啟動。 若要將 VM 容錯回復到到主要區域，您必須[重新保護](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect)次要區域中的 VM。

### <a name="at-the-time-of-reprotection-does-site-recovery-replicate-complete-data-from-the-secondary-region-to-the-primary-region"></a>進行重新保護時，Site Recovery 是否會從次要區域將完整的資料複寫到主要區域？
這需視情況而定。 例如，如果來源區域 VM 存在，便只會同步來源磁碟與目標磁碟之間的變更。 Site Recovery 會藉由比較磁碟來計算出差異，然後才傳輸資料。 此程序通常需要幾小時的時間。 如需有關在重新保護期間會發生之情況的詳細資訊，請參閱[重新保護已容錯移轉到主要區域的 Azure VM]( https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection)。

### <a name="how-much-time-does-it-take-to-fail-back"></a>容錯回復需要花費多久時間？
重新保護之後, 容錯回復的時間通常與從主要區域容錯移轉到次要區域所需的時間類似。

## <a name="capacity"></a>存儲

### <a name="how-is-capacity-assured-in-target-region-for-azure-vms"></a>如何在 Azure Vm 的目的地區域中保證容量？
Site Recovery 小組會與 Azure 容量管理小組合作, 規劃足夠的基礎結構容量, 以協助確保在起始容錯移轉時, 已成功在目的地區域中部署啟用進行嚴重損壞修復的 Vm。

### <a name="does-site-recovery-work-with-reserved-instances"></a>Site Recovery 與保留實例搭配使用嗎？
是, 您可以在嚴重損壞修復區域中購買[保留實例](https://azure.microsoft.com/pricing/reserved-vm-instances/), Site Recovery 容錯移轉作業將會使用它們。 </br> 不需要任何其他設定。


## <a name="security"></a>安全性

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>複寫資料會傳送到 Site Recovery 服務嗎？
否, Site Recovery 不會攔截複寫的資料, 也不會有任何有關您 Vm 上執行之內容的資訊。 只有協調複寫與容錯移轉所需的中繼資料會被傳送給 Site Recovery 服務。  
Site Recovery 已通過 ISO 27001:2013、27018、HIPAA、DPA 認證，並且正在進行 SOC2 和 FedRAMP JAB 評定程序。

### <a name="does-site-recovery-encrypt-replication"></a>Site Recovery 會將複寫加密嗎？
是的, Azure 中的加密傳輸和待用[加密](https://docs.microsoft.com/azure/storage/storage-service-encryption)都受到支援。

## <a name="next-steps"></a>後續步驟
* [檢閱](azure-to-azure-support-matrix.md)支援需求。
* [設定](azure-to-azure-tutorial-enable-replication.md) Azure 至 Azure 的複寫。
- 如果您在閱讀本文後有問題，請將問題張貼在 [Azure 復原服務論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)。
