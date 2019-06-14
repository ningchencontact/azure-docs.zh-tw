---
title: 針對使用 Azure Site Recovery 從 VMware VM 和實體伺服器至 Azure 的災害復原複寫問題進行疑難排解 | Microsoft Docs
description: 本文針對使用 Azure Site Recovery 將 VMware VM 和實體伺服器復原至 Azure 之災害復原期間發生的常見複寫問題，提供疑難排解資訊。
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/14/2019
ms.author: mayg
ms.openlocfilehash: 3221b2bd18b8b0756f280d88fffc6016d0498b8f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "64924817"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>針對 VMware VM 和實體伺服器的複寫問題進行疑難排解

本文說明一些常見的問題和當您將內部部署 VMware Vm 和實體伺服器複寫至 azure 時，可能會遇到的特定錯誤[Site Recovery](site-recovery-overview.md)。

## <a name="step-1-monitor-process-server-health"></a>步驟 1：監視處理序伺服器健全狀況

Site recovery[處理序伺服器](vmware-physical-azure-config-process-server-overview.md#process-server)來接收和最佳化複寫的資料，並將它傳送至 Azure。

我們建議您監視入口網站中，以確保它們已連線，且正常運作，而且該複寫正在進行的處理序伺服器相關聯的來源機器中的處理序伺服器的健全狀況。

- [深入了解](vmware-physical-azure-monitor-process-server.md)監視處理序伺服器。
- [檢閱最佳作法](vmware-physical-azure-troubleshoot-process-server.md#best-practices-for-process-server-deployment)
- [疑難排解](vmware-physical-azure-troubleshoot-process-server.md#check-process-server-health)process server 健康情況。

## <a name="step-2-troubleshoot-connectivity-and-replication-issues"></a>步驟 2：針對連線能力和複寫問題進行疑難排解

初始和持續進行的複寫失敗通常是由來源伺服器和處理序伺服器之間，或處理序伺服器與 Azure 之間的連線問題所造成。 

若要解決這些問題中，[疑難排解連線能力和複寫](vmware-physical-azure-troubleshoot-process-server.md#check-connectivity-and-replication)。




## <a name="step-3-troubleshoot-source-machines-that-arent-available-for-replication"></a>步驟 3：不適用於複寫的來源機器進行疑難排解

當您使用 Site Recovery 來嘗試選取來源機器以啟用複寫時，該機器可能會因下列其中一個原因而無法供您使用：

* **具有相同 UUID 的兩部虛擬機器**：如果 vCenter 底下的兩部虛擬機器具有相同的執行個體 UUID，則 Azure 入口網站中會顯示設定伺服器所探索到的第一部虛擬機器。 若要解決此問題，請確定沒有任何兩部虛擬機器具有相同的執行個體 UUID。 此案例中通常出現在其中備份的 VM 會變成作用中和已登入我們的探索記錄的執行個體。 請參閱 [Azure Site Recovery VMware 至 Azure：如清除重複或過時的項目](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx)來解決問題。
* **不正確的 vCenter 使用者認證**：確定您在使用 OVF 範本或統一安裝來設定設定伺服器時，已新增正確的 vCenter 認證。 若要確認您在設定期間所新增的認證，請參閱[修改自動探索所需的認證](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery)。
* **vCenter 權限不足**：如果所提供來存取 vCenter 的權限沒有所需的權限，可能會導致探索虛擬機器失敗。 請確定將[準備帳戶以進行自動探索](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)中所述的權限新增至 vCenter 使用者帳戶。
* **Azure Site Recovery 管理伺服器**：如果虛擬機器作為一或多個下列角色-管理伺服器的組態伺服器 /scale-out 處理序伺服器 / 主要目標伺服器，則您無法從入口網站中選擇虛擬機器。 無法複寫管理伺服器。
* **已經透過 Azure Site Recovery 服務保護/容錯移轉**：如果虛擬機器已經透過 Site Recovery 保護或容錯移轉，則在入口網站中便無法選取該虛擬機器來進行保護。 請確定您在入口網站中尋找的虛擬機器，尚未受到任何其他使用者或不同訂用帳戶的保護。
* **vCenter 未連線**：檢查 vCenter 是否處於已連線狀態。 若要確認，請移至 [復原服務保存庫] > [Site Recovery 基礎結構] > [組態伺服器]，按一下個別的組態伺服器，刀鋒視窗隨即在右邊開啟，內含相關聯的伺服器詳細資料。 檢查 vCenter 是否已連線。 如果它處於 「 未連線 」 狀態時，解決此問題，然後[重新整理設定伺服器](vmware-azure-manage-configuration-server.md#refresh-configuration-server)入口網站上。 在此之後，虛擬機器將會列在入口網站上。
* **ESXi 已關閉**：如果虛擬機器所在的 ESXi 主機處於已關閉狀態，則虛擬機器不會列在 Azure 入口網站上或可在其上選取。 開啟 ESXi 主機電源，在入口網站上[重新整理組態伺服器](vmware-azure-manage-configuration-server.md#refresh-configuration-server)。 在此之後，虛擬機器將會列在入口網站上。
* **擱置的重新開機**：如果虛擬機器上有擱置的重新開機，您就無法在 Azure 入口網站上選取此機器。 務必完成擱置的重新開機活動，[重新整理組態伺服器](vmware-azure-manage-configuration-server.md#refresh-configuration-server)。 在此之後，虛擬機器將會列在入口網站上。
* **找不到 IP**：如果虛擬機器沒有與其相關聯的有效 IP 位址，您就無法在 Azure 入口網站上選取此機器。 務必將有效的 IP 位址指派給虛擬機器，[重新整理組態伺服器](vmware-azure-manage-configuration-server.md#refresh-configuration-server)。 在此之後，虛擬機器將會列在入口網站上。

### <a name="troubleshoot-protected-virtual-machines-greyed-out-in-the-portal"></a>在入口網站中呈現灰色的受保護的虛擬機器進行疑難排解

如果系統中有重複項目，在 Azure 入口網站中便無法使用在 Site Recovery 底下複寫的虛擬機器。 若要了解如何刪除過時的項目並解決此問題，請參閱 [Azure Site Recovery VMware 至 Azure：如清除重複或過時的項目](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx) \(英文\)。

## <a name="common-errors-and-solutions"></a>常見的錯誤和解決方案

### <a name="initial-replication-issues-error-78169"></a>初始複寫問題 [error 78169]

透過上述如此可確保有沒有連線能力、 頻寬或時間同步處理相關的問題，請確認：

- 沒有防毒軟體封鎖 Azure Site Recovery。 了解[更多](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)上所需的 Azure Site Recovery 的資料夾排除項目。

### <a name="missing-app-consistent-recovery-points-error-78144"></a>遺漏應用程式一致復原點 [error 78144]

 這是因為磁碟區陰影複製服務 (VSS) 問題。 解決方式： 
 
- 請確認已安裝的 Azure Site Recovery 代理程式版本至少 9.22.2。 
- 請確認 VSS 提供者會安裝為 Windows 服務中的服務，同時確認 [元件服務] MMC 來檢查 Azure Site Recovery VSS 提供者，會列出。
- 如果未安裝 VSS 提供者，請參閱[疑難排解文章的安裝失敗](vmware-azure-troubleshoot-push-install.md#vss-installation-failures)。

- VSS 已停用，
    - 請確認 VSS 提供者服務的啟動類型設為**自動**。
    - 重新啟動下列服務：
        - VSS 服務
        - Azure Site Recovery VSS 提供者
        - VDS 服務

### <a name="source-machines-with-high-churn-error-78188"></a>來源機器具有高變換 [error 78188]

可能的原因：
- 列出虛擬機器的磁碟上的資料變更率 （寫入位元組/秒） 會高於[Azure Site Recovery 支援的限制](site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits)複寫目標儲存體帳戶類型。
- 沒有突然出現尖峰變換率因的大量資料而暫止的上傳。

若要解決此問題：
- 請確定目標儲存體帳戶類型 （標準或進階），會根據來源的變換率需求佈建。
- 如果觀察到的變換是暫時的等到趕上進度，並建立復原點的待處理的資料上傳的幾個小時。
- 如果問題持續發生，請使用 Site Recovery[部署規劃工具](site-recovery-deployment-planner.md#overview)，協助您規劃複寫。

### <a name="source-machines-with-no-heartbeat-error-78174"></a>使用 [錯誤 78174] 沒有活動訊號的來源機器

會發生這種情況是當來源電腦上的 Azure Site Recovery 行動代理程式不通訊與組態伺服器 (CS) 中。

若要解決此問題，請確認組態伺服器從來源 VM 的網路連線使用下列步驟：

1. 確認來源機器正在執行。
2. 來源機器使用具有系統管理員權限的帳戶登入。
3. 確認下列服務正在執行，才不會重新啟動服務：
   - Svagents (InMage Scout VX Agent)
   - InMage Scout 應用程式服務
4. 在來源電腦上，檢查記錄檔，取得錯誤詳細資料的位置：

       C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log
    
### <a name="process-server-with-no-heartbeat-error-806"></a>[Error 806] 沒有活動訊號處理序伺服器
如果沒有任何活動訊號處理序伺服器 (PS)，請確認︰
1. PS VM 已啟動並執行
2. 請檢查下列登入 PS 取得錯誤詳細資料：

       C:\ProgramData\ASR\home\svsystems\eventmanager*.log
       and
       C:\ProgramData\ASR\home\svsystems\monitor_protection*.log

### <a name="master-target-server-with-no-heartbeat-error-78022"></a>使用 [錯誤 78022] 沒有活動訊號的主要目標伺服器

發生這種情況的主要目標上的 Azure Site Recovery 行動代理程式不與組態伺服器通訊時。

若要解決此問題，使用下列步驟來確認服務狀態：

1. 確認在主要目標 VM 正在執行。
2. 主要目標 VM，使用具有系統管理員權限的帳戶登入。
    - 確認 svagents 服務正在執行。 如果它正在執行，重新啟動服務
    - 檢查記錄檔中的位置取得錯誤詳細資料：
        
          C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log



## <a name="next-steps"></a>後續步驟

如果您需要更多協助，請將您的問題貼到 [Azure Site Recovery 論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr) \(英文\) 中。 我們有一個使用中的社群，我們的其中一位工程師將可協助您。
