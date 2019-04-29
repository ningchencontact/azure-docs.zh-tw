---
title: 針對使用 Azure Site Recovery 從 VMware VM 和實體伺服器至 Azure 的災害復原複寫問題進行疑難排解 | Microsoft Docs
description: 本文針對使用 Azure Site Recovery 將 VMware VM 和實體伺服器復原至 Azure 之災害復原期間發生的常見複寫問題，提供疑難排解資訊。
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/14/2019
ms.author: mayg
ms.openlocfilehash: 1aaf13f01c7e7197001f3099fabd4b8be8545f0d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60565027"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>針對 VMware VM 和實體伺服器的複寫問題進行疑難排解

使用 Azure Site Recovery 來保護您的 VMware 虛擬機器或實體伺服器時，您可能會看到特定的錯誤訊息。 本文說明使用 [Site Recovery](site-recovery-overview.md) 將內部部署 VMware VM 和實體伺服器複寫至 Azure 時，可能會遇到的一些常見問題。

## <a name="monitor-process-server-health-to-avoid-replication-issues"></a>監視處理序伺服器健康情況以避免複寫問題

建議您在入口網站上監視處理序伺服器 (PS) 健康情況，以確保您相關聯來源機器的複寫正在進行。 在保存庫中，移至 [管理 > Site Recovery 基礎結構 > 組態伺服器]。 在 [組態伺服器] 刀鋒視窗中，按一下 [相關聯伺服器] 底下的 [處理序伺服器]。 [處理序伺服器] 刀鋒視窗隨即開啟，具有其健康情況統計資料。 您可以追蹤 CPU 使用率、記憶體使用量、複寫所需 PS 服務的狀態、憑證到期日和可用空間。 所有統計資料的狀態應該是綠色。 

**建議您的記憶體和 CPU 使用量低於 70%，可用空間高於 25%**。 可用空間是指處理序伺服器中的快取磁碟空間，用來在來源機器的複寫資料上傳至 Azure 之前進行儲存。 如果它降低為低於 20%，則會針對所有相關聯來源機器的複寫進行節流。 請遵循[容量指引](./site-recovery-plan-capacity-vmware.md#capacity-considerations)以了解複寫來源機器的必要組態。

請確定下列服務正在 PS 機器上執行。 啟動或重新啟動任何未執行的服務。

**內建處理序伺服器**

* ProcessServer
* ProcessServerMonitor
* cxprocessserver
* InMage PushInstall
* 記錄上傳服務 (LogUpload)
* InMage Scout 應用程式服務
* Microsoft Azure 復原服務代理程式 (obengine)
* InMage Scout VX Agent - Sentinel/Outpost (svagents)
* tmansvc
* World Wide Web Publishing 服務 (W3SVC)
* MySQL
* Microsoft Azure Site Recovery 服務 (dra)

**相應放大處理序伺服器**

* ProcessServer
* ProcessServerMonitor
* cxprocessserver
* InMage PushInstall
* 記錄上傳服務 (LogUpload)
* InMage Scout 應用程式服務
* Microsoft Azure 復原服務代理程式 (obengine)
* InMage Scout VX Agent - Sentinel/Outpost (svagents)
* tmansvc

**Azure 中容錯回復的處理序伺服器**

* ProcessServer
* ProcessServerMonitor
* cxprocessserver
* InMage PushInstall
* 記錄上傳服務 (LogUpload)

確定所有服務的 StartType 已設定為 [自動] 或 [自動 (延遲啟動)]。 Microsoft Azure 復原服務代理程式 (obengine) 服務的 StartType 不需要如上述設定。

## <a name="replication-issues"></a>複寫問題

初始和持續進行的複寫失敗通常是由來源伺服器和處理序伺服器之間，或處理序伺服器與 Azure 之間的連線問題所造成。 在大部分情況下，您可以藉由完成下列各節中的步驟，針對這些問題進行移難排解。

>[!Note]
>請確定：
>1. 系統日期時間為受保護的項目會在同步處理。
>2. 沒有防毒軟體封鎖 Azure Site Recovery。 了解[更多](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)上所需的 Azure Site Recovery 的資料夾排除項目。

### <a name="check-the-source-machine-for-connectivity-issues"></a>檢查來源電腦連線問題

您可以檢查來源電腦以下列清單顯示方式。

*  在來源伺服器上的命令列中，執行下列命令以使用 Telnet 透過 HTTPS 連接埠來偵測處理伺服器。 HTTPS 連接埠 9443 是處理序伺服器用來傳送和接收複寫流量的預設連接埠。 您可以在註冊時修改此連接埠。 下列命令會檢查是否有網路連線問題，以及是否有會封鎖防火牆連接埠的問題。


   `telnet <process server IP address> <port>`


   > [!NOTE]
   > 使用 Telnet 來測試連線能力。 請勿使用 `ping`。 如果未安裝 Telnet，請完成[安裝 Telnet 用戶端](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx) \(英文\) 中所列的步驟。

   如果 telnet 可以成功連線到 PS 連接埠，就會出現空白畫面。

   如果您無法連線至處理伺服器，請在處理伺服器上允許連入連接埠 9443。 例如，如果您的網路有周邊網路或遮蔽式子網路，您可能需要在處理伺服器上允許連入連接埠 9443。 接著，檢查是否仍然發生問題。

*  如果 telnet 成功但是來源機器報告無法與 PS 連線，請在來源機器上開啟網頁瀏覽器並且檢查是否可連線至位址 https://<PS_IP>:<PS_Data_Port>/。

    連線到這個位址時預期有 HTTPS 憑證錯誤。 忽略憑證錯誤並且繼續，最後應該會出現「400 – 不正確的要求」，這表示伺服器無法為瀏覽器的要求提供服務，而且與伺服器的標準 HTTPS 連線運作正常良好。

    如果不成功，則瀏覽器上的錯誤訊息詳細資料會提供指引。 舉例來說，如果 Proxy 驗證不正確，Proxy 伺服器會在錯誤訊息中傳回「407 – 需要 Proxy 驗證」與必要的動作。 

*  請檢查來源 VM 上的下列記錄是否有與網路上傳失敗相關的錯誤：

       C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\svagents*.log 

### <a name="check-the-process-server-for-connectivity-issues"></a>檢查處理序伺服器有連線問題

下列清單顯示您可以檢查處理伺服器的方式：

> [!NOTE]
> 處理序伺服器必須具有靜態 IPv4 位址，且不應該設定 NAT IP。

* **檢查來源機器與處理序伺服器之間的連線**
* 如果您可以從來源機器進行 telnet，但是無法從來源連線到 PS，請藉由在來源 VM 上執行 cxpsclient 工具，檢查來源 VM 與 cxprocessserver 的端對端連線：

      <install folder>\cxpsclient.exe -i <PS_IP> -l <PS_Data_Port> -y <timeout_in_secs:recommended 300>

   檢查 PS 上下列目錄中產生的記錄，以取得對應錯誤的詳細資料：

      C:\ProgramData\ASR\home\svsystems\transport\log\cxps.err
      and
      C:\ProgramData\ASR\home\svsystems\transport\log\cxps.xfer
* 如果從 PS 沒有活動訊號檢查 PS 的下列記錄檔 這由**錯誤碼 806**入口網站上。

      C:\ProgramData\ASR\home\svsystems\eventmanager*.log
      and
      C:\ProgramData\ASR\home\svsystems\monitor_protection*.log

* **檢查處理伺服器是否主動將資料推送至 Azure**。

  1. 在處理伺服器上，開啟 [工作管理員] \(按 Ctrl+Shift+Esc\)。
  2. 選取 [效能] 索引標籤，然後選取 [開啟資源監視器] 連結。 
  3. 在 [資源監視器] 頁面上，選取 [網路] 索引標籤。在 [具有網路活動的處理程序] 底下，檢查 **cbengine.exe** 是否正主動傳送大量資料。

       ![顯示 [具有網路活動的處理程序] 底下資料量的螢幕擷取畫面](./media/vmware-azure-troubleshoot-replication/cbengine.png)

  如果 cbengine.exe 未傳送大量資料，請完成下列各節中的步驟。

* **檢查處理伺服器是否可以連線至 Azure Blob 儲存體**。

  選取 [cbengine.exe]。 在 [TCP 連線] 底下，檢查是否有從處理伺服器到 Azure Blog 儲存體 URL 的連線。

  ![顯示 cbengine.exe 與 Azure Blob 儲存體 URL 之間連線的螢幕擷取畫面](./media/vmware-azure-troubleshoot-replication/rmonitor.png)

  如果沒有從處理伺服器到 Azure Blog 儲存體 URL 的連線，請在 [控制台] 中選取 [服務]。 檢查下列服務是否正在執行：

  *  cxprocessserver
  *  InMage Scout VX Agent – Sentinel/Outpost
  *  Microsoft Azure 復原服務代理程式
  *  Microsoft Azure Site Recovery 服務
  *  tmansvc

  啟動或重新啟動任何未執行的服務。 檢查是否仍然發生問題。

* **檢查處理伺服器是否能夠使用連接埠 443 來連線到 Azure 公用 IP 位址**。

  在 %programfiles%\Microsoft Azure Recovery Services Agent\Temp 中，開啟最新的 CBEngineCurr.errlog 檔案。 在此檔案中，搜尋 **443**，或搜尋**連線嘗試失敗**字串。

  ![顯示 [Temp] 資料夾中錯誤記錄的螢幕擷取畫面](./media/vmware-azure-troubleshoot-replication/logdetails1.png)

  如果有顯示出問題，請在處理伺服器的命令列中，使用 Telnet 來偵測您的 Azure 公用 IP 位址 (此 IP 位址在上圖中已遮罩)。 您可以在 CBEngineCurr.currLog 檔案中使用連接埠 443 來尋找您的 Azure 公用 IP 位址：

  `telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443`

  如果您無法連線，請檢查存取問題是否是由防火牆或 Proxy 設定所造成，如下一個步驟所述。

* **檢查處理伺服器上的 IP 位址型防火牆是否會封鎖存取**。

  如果您在伺服器上使用 IP 位址型防火牆規則，請下載完整的 [Microsoft Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653) \(英文\) 清單。 將 IP 位址範圍新增至您的防火牆設定，以確保防火牆允許與 Azure (以及與預設的 HTTPS 連接埠 443) 進行通訊。 允許您訂用帳戶之 Azure 區域及 Azure 美國西部區域 (用於存取控制和身分識別管理) 的 IP 位址範圍。

* **檢查處理伺服器上的 URL 位址型防火牆是否會封鎖存取**。

  如果您在伺服器上使用 URL 型防火牆規則，請將下表所列的 URL 新增至防火牆設定：

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

*  **檢查處理伺服器上的 Proxy 設定是否會封鎖存取**。

   如果您使用 Proxy 伺服器，請確定 DNS 伺服器可解析 Proxy 伺服器名稱。 若要檢查您設定設定伺服器時所提供的值，請移至登錄機碼 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Site Recovery\ProxySettings**。

   接著，確保 Azure Site Recovery 代理程式使用相同的設定來傳送資料： 
      
   1. 搜尋 **Microsoft Azure 備份**。 
   2. 開啟 [Microsoft Azure 備份]，然後選取 [動作] > [變更內容]。 
   3. 在 [Proxy 組態] 索引標籤上，您應該會看到 Proxy 位址。 此 Proxy 位址應該與登錄設定中顯示的 Proxy 位址相同。 如果不同，請將它變更為相同的位址。

*  **檢查處理伺服器上是否限制節流頻寬**。

   增加頻寬，然後檢查是否仍然發生問題。


## <a name="source-machine-isnt-listed-in-the-azure-portal"></a>Azure 入口網站中未列出來源機器

當您使用 Site Recovery 來嘗試選取來源機器以啟用複寫時，該機器可能會因下列其中一個原因而無法供您使用：

* **具有相同 UUID 的兩部虛擬機器**：如果 vCenter 底下的兩部虛擬機器具有相同的執行個體 UUID，則 Azure 入口網站中會顯示設定伺服器所探索到的第一部虛擬機器。 若要解決此問題，請確定沒有任何兩部虛擬機器具有相同的執行個體 UUID。 在備份 VM 變成作用中並已登入我們的探索記錄的執行個體中，此案例很常見。 請參閱 [Azure Site Recovery VMware 至 Azure：如清除重複或過時的項目](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx)來解決問題。
* **不正確的 vCenter 使用者認證**：確定您在使用 OVF 範本或統一安裝來設定設定伺服器時，已新增正確的 vCenter 認證。 若要確認您在設定期間所新增的認證，請參閱[修改自動探索所需的認證](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery)。
* **vCenter 權限不足**：如果所提供來存取 vCenter 的權限沒有所需的權限，可能會導致探索虛擬機器失敗。 請確定將[準備帳戶以進行自動探索](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)中所述的權限新增至 vCenter 使用者帳戶。
* **Azure Site Recovery 管理伺服器**：如果虛擬機器作為一或多個下列角色-管理伺服器的組態伺服器 /scale-out 處理序伺服器 / 主要目標伺服器，則您無法從入口網站中選擇虛擬機器。 無法複寫管理伺服器。
* **已經透過 Azure Site Recovery 服務保護/容錯移轉**：如果虛擬機器已經透過 Site Recovery 保護或容錯移轉，則在入口網站中便無法選取該虛擬機器來進行保護。 請確定您在入口網站中尋找的虛擬機器，尚未受到任何其他使用者或不同訂用帳戶的保護。
* **vCenter 未連線**：檢查 vCenter 是否處於已連線狀態。 若要確認，請移至 [復原服務保存庫] > [Site Recovery 基礎結構] > [組態伺服器]，按一下個別的組態伺服器，刀鋒視窗隨即在右邊開啟，內含相關聯的伺服器詳細資料。 檢查 vCenter 是否已連線。 如果處於「未連線」狀態，解決此問題，然後在入口網站上[重新整理組態伺服器](vmware-azure-manage-configuration-server.md#refresh-configuration-server)。 在此之後，虛擬機器將會列在入口網站上。
* **ESXi 已關閉**：如果虛擬機器所在的 ESXi 主機處於已關閉狀態，則虛擬機器不會列在 Azure 入口網站上或可在其上選取。 開啟 ESXi 主機電源，在入口網站上[重新整理組態伺服器](vmware-azure-manage-configuration-server.md#refresh-configuration-server)。 在此之後，虛擬機器將會列在入口網站上。
* **擱置的重新開機**：如果虛擬機器上有擱置的重新開機，您就無法在 Azure 入口網站上選取此機器。 務必完成擱置的重新開機活動，[重新整理組態伺服器](vmware-azure-manage-configuration-server.md#refresh-configuration-server)。 在此之後，虛擬機器將會列在入口網站上。
* **找不到 IP**：如果虛擬機器沒有與其相關聯的有效 IP 位址，您就無法在 Azure 入口網站上選取此機器。 務必將有效的 IP 位址指派給虛擬機器，[重新整理組態伺服器](vmware-azure-manage-configuration-server.md#refresh-configuration-server)。 在此之後，虛擬機器將會列在入口網站上。

## <a name="protected-virtual-machines-are-greyed-out-in-the-portal"></a>受保護的虛擬機器在入口網站中會顯示為灰色

如果系統中有重複項目，在 Azure 入口網站中便無法使用在 Site Recovery 底下複寫的虛擬機器。 若要了解如何刪除過時的項目並解決此問題，請參閱 [Azure Site Recovery VMware 至 Azure：如清除重複或過時的項目](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx) \(英文\)。

## <a name="common-errors-and-recommended-steps-for-resolution"></a>常見的錯誤和建議的步驟進行解析

### <a name="initial-replication-issues-error-78169"></a>初始複寫問題 [Error 78169]

透過上述如此可確保有沒有連線能力、 頻寬或時間同步處理相關的問題，請確認：

- 沒有防毒軟體封鎖 Azure Site Recovery。 了解[更多](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)上所需的 Azure Site Recovery 的資料夾排除項目。

### <a name="application-consistency-recovery-point-missing-error-78144"></a>遺漏 [Error 78144] 應用程式一致性復原點

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

### <a name="high-churn-on-source-machine-error-78188"></a>來源電腦 [Error 78188] 上的高變換

可能的原因：
- 列出虛擬機器的磁碟上的資料變更率 （寫入位元組/秒） 會高於[Azure Site Recovery 支援的限制](site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits)複寫目標儲存體帳戶類型。
- 沒有突然出現尖峰變換率因的大量資料而暫止的上傳。

若要解決此問題：
- 請確定目標儲存體帳戶類型 （標準或進階），會根據來源的變換率需求佈建。
- 如果觀察到的變換是暫時的等到趕上進度，並建立復原點的待處理的資料上傳的幾個小時。
- 如果問題持續發生，請使用 ASR[部署規劃工具](site-recovery-deployment-planner.md#overview)，協助您規劃複寫。

### <a name="no-heartbeat-from-source-machine-error-78174"></a>來源電腦 [Error 78174] 沒有任何活動訊號

會發生這種情況是當來源電腦上的 Azure Site Recovery 行動代理程式不通訊與組態伺服器 (CS) 中。

若要解決此問題，請確認組態伺服器從來源 VM 的網路連線使用下列步驟：

1. 確認來源機器正在執行。
2. 來源機器使用具有系統管理員權限的帳戶登入。
3. 確認下列服務正在執行，才不會重新啟動服務：
   - Svagents (InMage Scout VX Agent)
   - InMage Scout 應用程式服務
4. 在來源電腦上，檢查記錄檔，取得錯誤詳細資料的位置：

       C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log
    
### <a name="no-heartbeat-from-process-server-error-806"></a>處理序伺服器 [錯誤 806] 沒有任何活動訊號
如果沒有任何活動訊號處理序伺服器 (PS)，請確認︰
1. PS VM 已啟動並執行
2. 請檢查下列登入 PS 取得錯誤詳細資料：

       C:\ProgramData\ASR\home\svsystems\eventmanager*.log
       and
       C:\ProgramData\ASR\home\svsystems\monitor_protection*.log

### <a name="no-heartbeat-from-master-target-error-78022"></a>主要目標 [Error 78022] 沒有任何活動訊號

發生這種情況的主要目標上的 Azure Site Recovery 行動代理程式不與組態伺服器通訊時。

若要解決此問題，使用下列步驟來確認服務狀態：

1. 確認在主要目標 VM 正在執行。
2. 主要目標 VM，使用具有系統管理員權限的帳戶登入。
    - 確認 svagents 服務正在執行。 如果它正在執行，重新啟動服務
    - 檢查記錄檔中的位置取得錯誤詳細資料：
        
          C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log

### <a name="process-server-is-not-reachable-from-the-source-machine-error-78186"></a>從來源電腦 [Error 78186] 找不到 處理序伺服器

如果不解決，不產生的應用程式和損毀一致的點會導致此錯誤。 若要解決此問題，請遵循下列疑難排解連結：
1. 請確認[PS 服務正在執行](vmware-azure-troubleshoot-replication.md#monitor-process-server-health-to-avoid-replication-issues)
2. [檢查來源機器的連線問題](vmware-azure-troubleshoot-replication.md#check-the-source-machine-for-connectivity-issues)
3. [檢查處理序伺服器連線問題](vmware-azure-troubleshoot-replication.md#check-the-process-server-for-connectivity-issues)然後遵循提供的指引：
    - 正在檢查與來源的連線
    - 防火牆和 proxy 的問題

### <a name="data-upload-blocked-from-source-machine-to-process-server-error-78028"></a>處理序伺服器 [Error 78028] 來封鎖從來源機器的資料上傳

如果不解決，不產生的應用程式和損毀一致的點會導致此錯誤。 若要解決此問題，請遵循下列疑難排解連結：

1. 請確認[PS 服務正在執行](vmware-azure-troubleshoot-replication.md#monitor-process-server-health-to-avoid-replication-issues)
2. [檢查來源機器的連線問題](vmware-azure-troubleshoot-replication.md#check-the-source-machine-for-connectivity-issues)
3. [檢查處理序伺服器連線問題](vmware-azure-troubleshoot-replication.md#check-the-process-server-for-connectivity-issues)然後遵循提供的指引：
    - 正在檢查與來源的連線
    - 防火牆和 proxy 的問題

## <a name="next-steps"></a>後續步驟

如果您需要更多協助，請將您的問題貼到 [Azure Site Recovery 論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr) \(英文\) 中。 我們有一個使用中的社群，我們的其中一位工程師將可協助您。
