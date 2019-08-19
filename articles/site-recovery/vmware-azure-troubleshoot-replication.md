---
title: 針對使用 Azure Site Recovery 從 VMware VM 和實體伺服器至 Azure 的災害復原複寫問題進行疑難排解 | Microsoft Docs
description: 本文針對使用 Azure Site Recovery 將 VMware VM 和實體伺服器復原至 Azure 之災害復原期間發生的常見複寫問題，提供疑難排解資訊。
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/2/2019
ms.author: mayg
ms.openlocfilehash: 54686a96385532e17fe0ac6e59058b91b40c1342
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/02/2019
ms.locfileid: "68742556"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>針對 VMware VM 和實體伺服器的複寫問題進行疑難排解

本文說明當您使用[Site Recovery](site-recovery-overview.md)將內部部署 VMware vm 和實體伺服器複寫至 Azure 時, 可能會遇到的一些常見問題和特定錯誤。

## <a name="step-1-monitor-process-server-health"></a>步驟 1:監視進程伺服器健全狀況

Site Recovery 使用[進程伺服器](vmware-physical-azure-config-process-server-overview.md#process-server)來接收和優化複寫的資料, 並將其傳送至 Azure。

我們建議您在入口網站中監視進程伺服器的健全狀況, 以確保它們已連接且正常運作, 而且與進程伺服器相關聯的來源機器正在進行複寫。

- [瞭解](vmware-physical-azure-monitor-process-server.md)監視進程伺服器。
- [檢閱最佳作法](vmware-physical-azure-troubleshoot-process-server.md#best-practices-for-process-server-deployment)
- 針對進程伺服器健全狀況[進行疑難排解](vmware-physical-azure-troubleshoot-process-server.md#check-process-server-health)。

## <a name="step-2-troubleshoot-connectivity-and-replication-issues"></a>步驟 2:針對連線能力和複寫問題進行疑難排解

初始和進行中的複寫失敗通常是來源伺服器和進程伺服器之間或進程伺服器與 Azure 之間的連線問題所造成。 

若要解決這些問題, 請針對連線[能力和複寫進行疑難排解](vmware-physical-azure-troubleshoot-process-server.md#check-connectivity-and-replication)。




## <a name="step-3-troubleshoot-source-machines-that-arent-available-for-replication"></a>步驟 3：疑難排解不適用於複寫的來源機器

當您使用 Site Recovery 來嘗試選取來源機器以啟用複寫時，該機器可能會因下列其中一個原因而無法供您使用：

* **具有相同 UUID 的兩部虛擬機器**：如果 vCenter 底下的兩部虛擬機器具有相同的執行個體 UUID，則 Azure 入口網站中會顯示設定伺服器所探索到的第一部虛擬機器。 若要解決此問題，請確定沒有任何兩部虛擬機器具有相同的執行個體 UUID。 這種情況通常會出現在備份 VM 變成作用中並已登入探索記錄的實例中。 請參閱 [Azure Site Recovery VMware 至 Azure：如清除重複或過時的項目](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx)來解決問題。
* **不正確的 vCenter 使用者認證**：確定您在使用 OVF 範本或統一安裝來設定設定伺服器時，已新增正確的 vCenter 認證。 若要確認您在設定期間所新增的認證，請參閱[修改自動探索所需的認證](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery)。
* **vCenter 權限不足**：如果所提供來存取 vCenter 的權限沒有所需的權限，可能會導致探索虛擬機器失敗。 請確定將[準備帳戶以進行自動探索](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)中所述的權限新增至 vCenter 使用者帳戶。
* **Azure Site Recovery 管理伺服器**：如果虛擬機器作為一或多個下列角色-管理伺服器的組態伺服器 /scale-out 處理序伺服器 / 主要目標伺服器，則您無法從入口網站中選擇虛擬機器。 無法複寫管理伺服器。
* **已經透過 Azure Site Recovery 服務保護/容錯移轉**：如果虛擬機器已經透過 Site Recovery 保護或容錯移轉，則在入口網站中便無法選取該虛擬機器來進行保護。 請確定您在入口網站中尋找的虛擬機器，尚未受到任何其他使用者或不同訂用帳戶的保護。
* **vCenter 未連線**：檢查 vCenter 是否處於已連線狀態。 若要確認，請移至 [復原服務保存庫] > [Site Recovery 基礎結構] > [組態伺服器]，按一下個別的組態伺服器，刀鋒視窗隨即在右邊開啟，內含相關聯的伺服器詳細資料。 檢查 vCenter 是否已連線。 如果它處於「未連線」狀態, 請解決問題, 然後重新整理入口網站上[的設定伺服器](vmware-azure-manage-configuration-server.md#refresh-configuration-server)。 在此之後，虛擬機器將會列在入口網站上。
* **ESXi 已關閉**：如果虛擬機器所在的 ESXi 主機處於已關閉狀態，則虛擬機器不會列在 Azure 入口網站上或可在其上選取。 開啟 ESXi 主機電源，在入口網站上[重新整理組態伺服器](vmware-azure-manage-configuration-server.md#refresh-configuration-server)。 在此之後，虛擬機器將會列在入口網站上。
* **擱置的重新開機**：如果虛擬機器上有擱置的重新開機，您就無法在 Azure 入口網站上選取此機器。 務必完成擱置的重新開機活動，[重新整理組態伺服器](vmware-azure-manage-configuration-server.md#refresh-configuration-server)。 在此之後，虛擬機器將會列在入口網站上。
* **找不到 IP**：如果虛擬機器沒有與其相關聯的有效 IP 位址，您就無法在 Azure 入口網站上選取此機器。 務必將有效的 IP 位址指派給虛擬機器，[重新整理組態伺服器](vmware-azure-manage-configuration-server.md#refresh-configuration-server)。 在此之後，虛擬機器將會列在入口網站上。

### <a name="troubleshoot-protected-virtual-machines-greyed-out-in-the-portal"></a>在入口網站中針對受保護的虛擬機器進行灰色的疑難排解

如果系統中有重複項目，在 Azure 入口網站中便無法使用在 Site Recovery 底下複寫的虛擬機器。 若要了解如何刪除過時的項目並解決此問題，請參閱 [Azure Site Recovery VMware 至 Azure：如清除重複或過時的項目](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx) \(英文\)。

## <a name="common-errors-and-solutions"></a>常見的錯誤和解決方案

### <a name="initial-replication-issues-error-78169"></a>初始複寫問題 [錯誤 78169]

在上述情況下, 確保沒有連線能力、頻寬或時間同步相關問題, 請確定:

- 沒有防毒軟體封鎖 Azure Site Recovery。 深入[瞭解 ](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)Azure Site Recovery 所需的資料夾排除專案。

### <a name="missing-app-consistent-recovery-points-error-78144"></a>遺失應用程式一致復原點 [錯誤 78144]

 因為磁片區陰影複製服務 (VSS) 的問題, 所以會發生這種情況。 解決方式： 
 
- 確認已安裝的 Azure Site Recovery 代理程式版本至少為9.22.2。 
- 確認 VSS 提供者已在 Windows 服務中安裝為服務, 同時也請確認元件服務 MMC, 檢查是否已列出 Azure Site Recovery VSS 提供者。
- 如果未安裝 VSS 提供者, 請參閱[安裝失敗疑難排解一文](vmware-azure-troubleshoot-push-install.md#vss-installation-failures)。

- 如果停用 VSS,
    - 確認 VSS 提供者服務的 [啟動類型] 設定為 [**自動**]。
    - 重新開機下列服務:
        - VSS 服務
        - Azure Site Recovery VSS 提供者
        - VDS 服務

- 如果您執行的是 SQL 或 Exchange 工作負載, 請檢查這些應用程式寫入器的記錄是否有失敗。 在下列文章中會捕捉經常發生的錯誤及其解決方式:
    -  [SQL Server 資料庫的自動關閉選項設定為 TRUE](https://support.microsoft.com/help/4504104)
    - [SQL Server 2008 R2 擲回不可重試的錯誤](https://support.microsoft.com/help/4504103)
    - [SQL Server 2016 和2017中的已知問題](https://support.microsoft.com/help/4493364)
    - [Exchange 伺服器2013和2016的常見問題](https://support.microsoft.com/help/4037535)


### <a name="source-machines-with-high-churn-error-78188"></a>具有高變換率的來源機器 [錯誤 78188]

可能的原因:
- 虛擬機器列出的磁片上的資料變更率 (寫入位元組/秒) 超過複寫目標儲存體帳戶類型[Azure Site Recovery 支援的限制](site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits)。
- 因為有大量的資料要等待上傳, 所以變換率突然激增。

若要解決此問題：
- 請確定目標儲存體帳戶類型 (標準或高階) 是根據來源的流失率需求來布建。
- 如果您已複寫到高階受控磁片 (asrseeddisk 類型), 請確定磁片大小支援根據 Site Recovery 限制觀察到的變換率。 如有需要, 您可以增加 asrseeddisk 的大小。 請遵循下列步驟：
    - 流覽至受影響複寫機器的 [磁片] 分頁, 並複製複本磁片名稱
    - 流覽至此複本受控磁片
    - 您可能會在 [總覽] 分頁上看到一個橫幅, 指出已產生 SAS URL。 按一下此橫幅並取消匯出。 如果您看不到橫幅, 請忽略此步驟。
    - 一旦撤銷 SAS URL, 請移至受控磁片的 [設定] 分頁並增加大小, 讓 ASR 在來源磁片上支援觀察到的流失率
- 如果觀察到的變換是暫時性的, 請等候幾個小時讓擱置中的資料上傳趕上, 並建立復原點。
- 如果磁片包含非重要的資料, 例如暫存記錄檔、測試資料等, 請考慮將此資料移至別處, 或從複寫中完全排除此磁片
- 如果問題持續存在, 請使用 Site Recovery[部署規劃](site-recovery-deployment-planner.md#overview)工具來協助規劃複寫。

### <a name="source-machines-with-no-heartbeat-error-78174"></a>沒有任何信號的來源機器 [錯誤 78174]

當來源電腦上的 Azure Site Recovery 行動代理程式未與設定伺服器 (CS) 通訊時, 就會發生這種情況。

若要解決此問題, 請使用下列步驟來確認從來源 VM 到設定伺服器的網路連線能力:

1. 確認來源電腦正在執行。
2. 使用具有系統管理員許可權的帳戶來登入來源機器。
3. 請確認下列服務正在執行, 而且如果不重新開機服務:
   - Svagents (InMage Scout VX Agent)
   - InMage Scout 應用程式服務
4. 在來源機器上, 檢查位於位置的記錄, 以取得錯誤詳細資料:

       C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log
    
### <a name="process-server-with-no-heartbeat-error-806"></a>沒有任何信號的進程伺服器 [錯誤 806]
如果進程伺服器 (PS) 沒有任何信號, 請檢查:
1. PS VM 已啟動且正在執行
2. 檢查 PS 上的下列記錄檔, 以取得錯誤詳細資料:

       C:\ProgramData\ASR\home\svsystems\eventmanager*.log
       and
       C:\ProgramData\ASR\home\svsystems\monitor_protection*.log

### <a name="master-target-server-with-no-heartbeat-error-78022"></a>不含任何信號的主要目標伺服器 [錯誤 78022]

當主要目標上的 Azure Site Recovery 行動代理程式未與設定伺服器通訊時, 就會發生這種情況。

若要解決此問題, 請使用下列步驟來確認服務狀態:

1. 確認主要目標 VM 正在執行。
2. 使用具有系統管理員許可權的帳戶來登入主要目標 VM。
    - 確認 svagents 服務正在執行。 如果正在執行, 請重新開機服務
    - 檢查位置的記錄, 以取得錯誤詳細資料:
        
          C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log

## <a name="error-id-78144---no-app-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>錯誤識別碼 78144-過去 ' XXX ' 分鐘內沒有任何應用程式一致的 VM 可用復原點

以下列出一些最常見的問題

#### <a name="cause-1-known-issue-in-sql-server-20082008-r2"></a>原因 1：SQL server 2008/2008 R2 中的已知問題 
**修正方式**:SQL server 2008/2008 R2 有一個已知的問題。 [如需裝載 SQL Server 2008 R2 的伺服器, Azure Site Recovery 代理程式或其他非元件 VSS 備份失敗,](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2)請參閱此知識庫文章

#### <a name="cause-2-azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>原因 2：使用 AUTO_CLOSE Db 裝載任何版本的 SQL Server 實例之伺服器上的 Azure Site Recovery 作業失敗 
**修正方式**:請參閱知識庫[文章](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser) 


#### <a name="cause-3-known-issue-in-sql-server-2016-and-2017"></a>原因 3：SQL Server 2016 和2017中的已知問題
**修正方式**:請參閱知識庫[文章](https://support.microsoft.com/help/4493364/fix-error-occurs-when-you-back-up-a-virtual-machine-with-non-component) 


### <a name="more-causes-due-to-vss-related-issues"></a>造成 VSS 相關問題的其他原因:

若要進一步進行疑難排解, 請檢查來源電腦上的檔案, 以取得失敗的確切錯誤碼:
    
    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log

如何找出檔案中的錯誤？
在編輯器中開啟 vacp.exe 檔案, 以搜尋字串 "vacpError"
        
    Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|

在上述範例中, **2147754994**是告知您失敗的錯誤碼, 如下所示

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>未安裝 VSS 寫入器-錯誤2147221164 

*修正方式*:若要產生應用程式一致性標記, Azure Site Recovery 使用 Microsoft 磁片區陰影複製服務 (VSS)。 它會為其作業安裝 VSS 提供者, 以取得應用程式一致性快照集。 此 VSS 提供者會安裝為服務。 如果未安裝 VSS 提供者服務, 應用程式一致性快照集建立會失敗, 並出現錯誤識別碼0x80040154 「類別未註冊」。 </br>
[如需 VSS 寫入器安裝的疑難排解,](https://docs.microsoft.com/azure/site-recovery/vmware-azure-troubleshoot-push-install#vss-installation-failures)請參閱文章 

#### <a name="vss-writer-is-disabled---error-2147943458"></a>VSS 寫入器已停用-錯誤2147943458

**修正方式**:若要產生應用程式一致性標記, Azure Site Recovery 使用 Microsoft 磁片區陰影複製服務 (VSS)。 它會為其作業安裝 VSS 提供者, 以取得應用程式一致性快照集。 此 VSS 提供者會安裝為服務。 如果停用 VSS 提供者服務, 應用程式一致性快照集建立會失敗, 並出現錯誤識別碼「指定的服務已停用且無法啟動 (0x80070422)」。 </br>

- 如果停用 VSS,
    - 確認 VSS 提供者服務的 [啟動類型] 設定為 [**自動**]。
    - 重新開機下列服務:
        - VSS 服務
        - Azure Site Recovery VSS 提供者
        - VDS 服務

####  <a name="vss-provider-not_registered---error-2147754756"></a>VSS 提供者 NOT_REGISTERED-錯誤2147754756

**修正方式**:若要產生應用程式一致性標記, Azure Site Recovery 使用 Microsoft 磁片區陰影複製服務 (VSS)。 檢查是否已安裝 Azure Site Recovery VSS 提供者服務。 </br>

- 使用下列命令重試提供者安裝:
- 卸載現有提供者:C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Uninstall.cmd
- F1C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd
 
確認 VSS 提供者服務的 [啟動類型] 設定為 [**自動**]。
    - 重新開機下列服務:
        - VSS 服務
        - Azure Site Recovery VSS 提供者
        - VDS 服務

## <a name="next-steps"></a>後續步驟

如果您需要更多協助，請將您的問題貼到 [Azure Site Recovery 論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr) \(英文\) 中。 我們有一個使用中的社群，我們的其中一位工程師將可協助您。
