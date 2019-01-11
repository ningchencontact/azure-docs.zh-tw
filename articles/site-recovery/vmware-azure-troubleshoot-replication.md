---
title: 針對使用 Azure Site Recovery 從 VMware VM 和實體伺服器至 Azure 的災害復原複寫問題進行疑難排解 | Microsoft Docs
description: 本文提供使用 Azure Site Recovery 在 VMware VM 和實體伺服器的災害復原期間，其常見複寫問題的疑難排解資訊。
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 12/17/2018
ms.author: ramamill
ms.openlocfilehash: 1c37b764b47856d3a369228d3f224f2a464029bb
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/27/2018
ms.locfileid: "53790651"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>針對 VMware VM 和實體伺服器的複寫問題進行疑難排解

使用 Azure Site Recovery 保護您的 VMware 虛擬機器或實體伺服器時，您可能會收到特定的錯誤訊息。 本文說明當您使用 [Azure Site Recovery](site-recovery-overview.md) 將內部部署 VMware VM 和實體伺服器複寫至 Azure 時，可能會遇到的一些常見問題。


## <a name="initial-replication-issues"></a>初始複寫問題

在許多案例中，我們支援部門遇到的初始複寫失敗，都是來自來源伺服器到處理序伺服器或處理序伺服器到 Azure 之間的連線問題。 在大部分情況下，您可以依照下面所列的步驟針對這些問題進行疑難排解。

### <a name="verify-the-source-machine"></a>確認來源電腦
* 在來源伺服器機器的命令列中，如下所示使用 Telnet 來偵測搭配 https 連接埠 (預設值 9443) 的處理序伺服器，以查看是否有任何網路連線問題或防火牆連接埠封鎖問題。

    `telnet <PS IP address> <port>`
> [!NOTE]
    > 使用 Telnet，不要使用 PING 來測試連線。  如未安裝 Telnet，請依照[這裡](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx)的步驟操作。

如果無法連線，允許處理序伺服器上的輸入連接埠 9443，然後檢查問題是否仍然存在。 曾有案例是處理序伺服器位在 DMZ 之後而造成此問題。

* 檢查服務 `InMage Scout VX Agent – Sentinel/OutpostStart` 的狀態，是否沒有在執行，以及檢查問題是否仍然存在。   

### <a name="verify-the-process-server"></a>確認處理序伺服器

* **檢查處理序伺服器是否主動將資料推送至 Azure**

在處理序伺服器機器上，開啟 [工作管理員] \(按 Ctrl-Shift-Esc\)。 移至 [效能] 索引標籤，按一下 [開啟資源監視器] 連結。 在 Resource Manager 中，移至 [網路] 索引標籤。檢查「網路活動的處理序」中的 cbengine.exe 是否主動傳送大量資料 (以 MB 為單位)。

![啟用複寫](./media/vmware-azure-troubleshoot-replication/cbengine.png)

如果沒有，依照下列步驟操作：

* **檢查處理序伺服器是否能夠連線至 Azure Blob**：選擇並勾選 cbengine.exe 以檢視 TCP 連線，查看是否有處理序伺服器到 Azure 儲存體 blob URL 的連線。

![啟用複寫](./media/vmware-azure-troubleshoot-replication/rmonitor.png)

如果沒有，則移至 [控制台] > [服務]，檢查下列服務是否已啟動並執行：

     * cxprocessserver
     * InMage Scout VX Agent – Sentinel/Outpost
     * Microsoft Azure Recovery Services Agent
     * Microsoft Azure Site Recovery Service
     * tmansvc
     *
啟動 (或重新啟動) 任何沒有在執行的服務，然後檢查問題是否仍然存在。

* **檢查處理序伺服器是否能夠使用連接埠 443 連線到 Azure 公用 IP 位址**

從 `%programfiles%\Microsoft Azure Recovery Services Agent\Temp` 開啟最新的 CBEngineCurr.errlog，並搜尋：443 和 connection attempt failed。

![啟用複寫](./media/vmware-azure-troubleshoot-replication/logdetails1.png)

如果有問題，則從處理序伺服器的命令列，使用 telnet 偵測您 Azure 公用 IP 位址 (上圖中遮蔽的部分，也就是在 CBEngineCurr.currLog 中以連接埠 443 找到的位址)。

      telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443
如果您無法連線，則檢查存取問題是否由於防火牆或 Proxy (如下一個步驟中所述)。


* **檢查處理序伺服器上的 IP 位址型防火牆是否封鎖存取**：如果您的伺服器使用 IP 位址型防火牆規則，請從[這裡](https://www.microsoft.com/download/details.aspx?id=41653)下載 Microsoft Azure Datacenter 的 IP 範圍完整清單，將它們新增到您的防火牆組態中，以確保它們允許對 Azure (和 HTTPS (443) 連接埠) 的通訊。  允許訂用帳戶的 Azure 區域和美國西部使用 IP 位址範圍 (用於存取控制和身分識別管理)。

* **檢查處理序伺服器上的 URL 型防火牆是否封鎖存取**：如果您的伺服器使用 URL 型防火牆規則，請確定下列 URL 已新增至防火牆組態。

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

* **檢查處理序伺服器上的 Proxy 設定是否封鎖存取**。  如果您使用 Proxy 伺服器，請確定 DNS 伺服器可解析 Proxy 伺服器名稱。
若要查看您在設定伺服器安裝時所提供的資訊， 請移至登錄機碼

    `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Site Recovery\ProxySettings`

現在，請確保 Azure Site Recovery 代理程式使用相同設定來傳送資料。
搜尋 Microsoft Azure 備份

開啟它，然後按一下 [動作] > [變更屬性]。 在 [Proxy 設定] 索引標籤上，您會看到 Proxy 位址，這應該和登錄設定顯示的相同。 如果沒有，請將它變更為相同的位址。


* **檢查處理序伺服器是否限制節流頻寬**：增加頻寬，然後檢查問題是否仍然存在。

## <a name="source-machine-to-be-protected-through-site-recovery-is-not-listed-on-azure-portal"></a>要以 Site Recovery 保護的來源機器未列於 Azure 入口網站中

嘗試選擇要啟用「透過 Azure Site Recovery 進行複寫」功能的來源機器時，機器可能因下列原因而無法讓您繼續使用

* 如果 vCenter 下有兩個虛擬機器具有相同的執行個體 UUID，組態伺服器所探索到的第一個虛擬機器將會顯示於入口網站中。 若要解決此問題，請確定沒有兩個虛擬機器具有相同的執行個體 UUID。
* 確定您在透過 OVF 範本設定組態或進行統一設定期間，已新增正確的 vCenter 認證。 若要確認已新增認證，請參閱[這裡](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery)提供的指導方針。
* 如果提供用來存取 vCenter 的使用權限沒有足夠的權限，可能會導致探索虛擬機器失敗。 請確定在[這裡](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)提供的權限已新增至 vCenter 使用者帳戶。
* 如果虛擬機器已透過 Site Recovery 受到保護，則無法再受到其他保護。 請確定您在入口網站中尋找的虛擬機器，尚未受到任何其他使用者或其他訂用帳戶的保護。

## <a name="protected-virtual-machines-are-greyed-out-in-the-portal"></a>受保護的虛擬機器在入口網站中會顯示為灰色

如果系統中有重複的項目，在 Site Recovery 下複寫的虛擬機器就會呈現灰色。 請參閱[這裡](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx)提供的指導方針以刪除過時的項目，並解決問題。

## <a name="next-steps"></a>後續步驟
如果您需要更多說明，請在 [Azure Site Recovery 論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr) \(英文\) 上張貼您的問題。 我們有一個使用中的社群，我們的其中一位工程師將協助您。
