---
title: 疑難排解 Azure Site Recovery 處理序伺服器
description: 本文說明如何使用 Azure Site Recovery 處理序伺服器的問題進行疑難排解
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 04/29/2019
ms.author: raynew
ms.openlocfilehash: 6e31308800f72d60381f1e4ecd540482ba263851
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2019
ms.locfileid: "65969361"
---
# <a name="troubleshoot-the-process-server"></a>疑難排解處理序伺服器

[Site Recovery](site-recovery-overview.md)當您設定內部部署 VMware Vm 和實體伺服器災害復原至 Azure 時，會使用處理序伺服器。 本文說明如何針對問題處理序伺服器，包括複寫和連線問題進行疑難排解。

[了解更多](vmware-physical-azure-config-process-server-overview.md)相關處理序伺服器。

## <a name="before-you-start"></a>開始之前

在開始之前進行疑難排解：

1. 請確定您了解如何[監視處理序伺服器](vmware-physical-azure-monitor-process-server.md)。
2. 檢閱下面的最佳作法。
3. 請確定您遵循[容量考量](site-recovery-plan-capacity-vmware.md#capacity-considerations)，並使用調整大小指導方針[伺服器組態](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server)或是[獨立處理序伺服器](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server)。

## <a name="best-practices-for-process-server-deployment"></a>處理序伺服器部署的最佳作法

為了取得最佳的處理序伺服器的效能，我們已彙總一般最佳作法的數的字。

**最佳作法** | **詳細資料**
--- |---
**使用量** | 請確定 configuration server （獨立式） 處理序伺服器只會用於預定用途。 不在電腦上執行任何其他項目。
**IP 位址** | 請確定處理序伺服器具有靜態 IPv4 位址，而且不需要 NAT 設定。
**控制記憶體/CPU 使用量** |保留 CPU 和記憶體使用量在 70%。
**請確定可用空間** | 可用空間是指處理序伺服器上的快取磁碟空間。 複寫資料會儲存在快取中，才能上傳至 Azure。<br/><br/> 保留 25%以上的可用空間。 如果它低於 20%時，處理序伺服器相關聯的複寫機器的複寫會進行節流。

## <a name="check-process-server-health"></a>檢查處理序伺服器健全狀況

疑難排解的第一個步驟是檢查健全狀況和狀態的處理序伺服器。 若要這樣做，請檢閱所有的警示，請檢查所需的服務正在執行，以及確認處理序伺服器的活動訊號。 下圖中摘要說明這些步驟後面接著程序可協助您執行步驟。

![疑難排解處理序伺服器健全狀況](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-process-server-health.png)

## <a name="step-1-troubleshoot-process-server-health-alerts"></a>步驟 1：疑難排解處理序伺服器健康情況警示

處理序伺服器會產生健康狀態警示數目。 下表會摘要說明這些警示和建議的動作。

**警示類型** | **錯誤** | **疑難排解**
--- | --- | --- 
![狀況良好][green] | None  | 處理序伺服器已連線且狀況良好。
![警告][yellow] | 指定的服務未執行。 | 1.請確認服務正在執行。<br/> 2.如果服務正在執行，如預期般運作，請遵循下方的指示[針對連線能力和複寫問題進行疑難排解](#check-connectivity-and-replication)。
![警告][yellow]  | 過去 15 分鐘內的 CPU 使用率 > 80%。 | 1.請勿將新增新的機器。<br/>2.核取的 Vm 使用的處理序伺服器數目與對齊[定義限制](site-recovery-plan-capacity-vmware.md#capacity-considerations)，並考慮設定[額外處理序伺服器](vmware-azure-set-up-process-server-scale.md)。<br/>3.請遵循下方的指示[針對連線能力和複寫問題進行疑難排解](#check-connectivity-and-replication)。
![重要][red] |  過去 15 分鐘內的 CPU 使用量 > 95%。 | 1.請勿將新增新的機器。<br/>2.核取的 Vm 使用的處理序伺服器數目與對齊[定義限制](site-recovery-plan-capacity-vmware.md#capacity-considerations)，並考慮設定[額外處理序伺服器](vmware-azure-set-up-process-server-scale.md)。<br/>3.請遵循下方的指示[針對連線能力和複寫問題進行疑難排解](#check-connectivity-and-replication)。<br/> 4.如果問題持續發生，請執行[部署規劃工具](https://aka.ms/asr-v2a-deployment-planner)VMware/實體伺服器複寫。
![警告][yellow] | 過去 15 分鐘內的記憶體使用方式 > 80%。 |  1.請勿將新增新的機器。<br/>2.核取的 Vm 使用的處理序伺服器數目與對齊[定義限制](site-recovery-plan-capacity-vmware.md#capacity-considerations)，並考慮設定[額外處理序伺服器](vmware-azure-set-up-process-server-scale.md)。<br/>3.請遵循與警告相關的任何指示。<br/> 4.如果問題持續發生，請遵循下方的指示[針對連線能力和複寫問題進行疑難排解](#check-connectivity-and-replication)。
![重要][red] | 過去 15 分鐘內的記憶體使用量 > 95%。 | 1.不要加上新的機器，並考慮設定[額外的處理序伺服器](vmware-azure-set-up-process-server-scale.md)。<br/> 2.請遵循與警告相關的任何指示。<br/> 3. 4. 如果問題持續發生，請遵循下方的指示[針對連線能力和複寫問題進行疑難排解](#check-connectivity-and-replication)。<br/> 4.如果問題持續發生，請執行[部署規劃工具](https://aka.ms/asr-v2a-deployment-planner)的 VMware/實體伺服器複寫問題。
![警告][yellow] | 快取資料夾的可用空間 < 30%的過去 15 分鐘。 | 1.不加入新的機器，並設定，請考慮[額外的處理序伺服器](vmware-azure-set-up-process-server-scale.md)。<br/>2.核取的 Vm 使用的處理序伺服器數目與對齊[指導方針](site-recovery-plan-capacity-vmware.md#capacity-considerations)。<br/> 3.請遵循下方的指示[針對連線能力和複寫問題進行疑難排解](#check-connectivity-and-replication)。
![重要][red] |  過去 15 分鐘內的可用空間 < 25% | 1.遵循此問題的警告與相關聯的指示。<br/> 2. 3. 請遵循下方的指示[針對連線能力和複寫問題進行疑難排解](#check-connectivity-and-replication)。<br/> 3.如果問題持續發生，請執行[部署規劃工具](https://aka.ms/asr-v2a-deployment-planner)VMware/實體伺服器複寫。
![重要][red] | 15 分鐘以上的處理序伺服器沒有任何活動訊號。 Tmansvs 服務未與組態伺服器通訊。 | 1） 請確認處理序伺服器已啟動並執行。<br/> 2.請檢查 tmassvc 處理序伺服器上執行。<br/> 3.請遵循下方的指示[針對連線能力和複寫問題進行疑難排解](#check-connectivity-and-replication)。


![資料表索引鍵](./media/vmware-physical-azure-troubleshoot-process-server/table-key.png)


## <a name="step-2-check-process-server-services"></a>步驟 2：檢查處理序伺服器服務

下表摘要說明應處理序伺服器執行的服務。 有些許差異，在服務中，根據處理序伺服器部署的方式。 

Microsoft Azure 復原服務代理程式 (obengine) 以外的所有服務，請檢查 StartType 設為**自動**或是**自動 （延遲開始）**。
 
**部署** | **執行中的服務**
--- | ---
**在組態伺服器上的處理序伺服器** | ProcessServer;ProcessServerMonitor;cxprocessserver;InMage PushInstall;記錄檔上傳服務 (LogUpload);InMage Scout Application Service;Microsoft Azure 復原服務代理程式 」 (obengine);InMage Scout VX Agent-Sentinel/Outpost (svagents);tmansvc;World Wide Web Publishing 服務 (W3SVC);MySQL;Microsoft Azure Site Recovery 服務 (dra)
**當做獨立伺服器執行的處理序伺服器** | ProcessServer;ProcessServerMonitor;cxprocessserver;InMage PushInstall;記錄檔上傳服務 (LogUpload);InMage Scout Application Service;Microsoft Azure 復原服務代理程式 」 (obengine);InMage Scout VX Agent-Sentinel/Outpost (svagents);tmansvc。
**在 Azure 中部署容錯回復的處理序伺服器** | ProcessServer;ProcessServerMonitor;cxprocessserver;InMage PushInstall;記錄檔上傳服務 (LogUpload)


## <a name="step-3-check-the-process-server-heartbeat"></a>步驟 3：檢查處理序伺服器活動訊號

如果沒有處理序伺服器 （錯誤碼 806） 沒有任何活動訊號，執行下列作業：

1. 請確認處理序伺服器 VM 已啟動並執行。
2. 請檢查這些記錄檔的錯誤。

    C:\ProgramData\ASR\home\svsystems\eventmanager *.log  C\ProgramData\ASR\home\svsystems\monitor_protection*.log

## <a name="check-connectivity-and-replication"></a>請檢查連線和複寫

 初始和持續進行的複寫失敗通常是因為來源機器與處理序伺服器之間，或處理序伺服器與 Azure 之間的連線問題所造成。 下圖中摘要說明這些步驟後面接著程序可協助您執行步驟。

![連線能力和複寫的疑難排解](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-connectivity-replication.png)


## <a name="step-4-verify-time-sync-on-source-machine"></a>步驟 4：請確認來源機器上的時間同步處理

請確保複寫的機器系統日期/時間保持同步。[深入了解](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time)

## <a name="step-5-check-anti-virus-software-on-source-machine"></a>步驟 5：檢查來源電腦上的防毒軟體

在 複寫的機器上沒有防毒軟體封鎖站台復原的檢查。 如果您要排除 Site Recovery 從防毒程式，請檢閱[這篇文章](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)。

## <a name="step-6-check-connectivity-from-source-machine"></a>步驟 6：檢查從來源機器的連線


1. 安裝[Telnet 用戶端](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx)如果您需要在來源機器上。 請勿使用 Ping。
2. 從來源電腦上，偵測處理序伺服器以 Telnet 的 HTTPS 連接埠。 根據預設 9443 是複寫流量的 HTTPS 連接埠。

    `telnet <process server IP address> <port>`

3. 請確認連接是否成功。


**連線能力** | **詳細資料** | **動作**
--- | --- | ---
**Successful** | Telnet 顯示空白畫面，並處理序伺服器可供連線。 | 不需要採取進一步的動作。
**不成功** | 您無法連線 | 請確定輸入的連接埠 9443 允許處理序伺服器上。 例如，如果您有周邊網路或遮蔽式子網路。 再次檢查連線能力。
**部分成功** | 您可以連接，但來源電腦會回報無法處理序伺服器。 | 繼續進行下一步 的疑難排解程序。

## <a name="step-7-troubleshoot-an-unreachable-process-server"></a>步驟 7：疑難排解 「 無法連線到處理序伺服器

如果處理序伺服器無法連線到來源機器，就會顯示錯誤 78186。 若未提及，此問題會導致應用程式一致，而且損毀一致復原點不會產生如預期般運作。

藉由檢查來源電腦可以連線到處理序伺服器的 IP 位址與在來源電腦上，若要檢查的端對端連線執行 cxpsclient 工具進行疑難排解。


### <a name="check-the-ip-connection-on-the-process-server"></a>檢查處理序伺服器上的 IP 連線

如果 telnet 成功，但來源電腦會回報無法處理序伺服器，請檢查是否可以連線到處理序伺服器的 IP 位址。

1. 在網頁瀏覽器嘗試連線到 IP 位址 https://<PS_IP>:<PS_Data_Port>/。
2. 如果這項檢查會顯示 HTTPS 憑證錯誤，這是正常。 如果您選擇忽略錯誤，您應該會看到 400-不正確的要求。 這表示伺服器不能做的瀏覽器要求，且標準的 HTTPS 連線正常。
3. 如果這項檢查無法運作，然後記下的瀏覽器的錯誤訊息。 比方說，407 錯誤會指出 proxy 驗證的問題。

### <a name="check-the-connection-with-cxpsclient"></a>請檢查與 cxpsclient 連接

此外，您可以執行 cxpsclient 工具，以檢查的端對端連線。

1. 請執行此工具，如下所示：

    ```
    <install folder>\cxpsclient.exe -i <PS_IP> -l <PS_Data_Port> -y <timeout_in_secs:recommended 300>
    ```

2. 在處理序伺服器上，檢查這些資料夾中產生的記錄檔：

    C:\ProgramData\ASR\home\svsystems\transport\log\cxps.err  C:\ProgramData\ASR\home\svsystems\transport\log\cxps.xfer



### <a name="check-source-vm-logs-for-upload-failures-error-78028"></a>檢查來源 VM 記錄檔上傳失敗 （錯誤 78028）

封鎖從來源機器，以處理程序服務的資料上傳問題可能會導致不產生兩個損毀一致和應用程式一致復原點。 

1. 若要疑難排解網路上傳失敗，您可以尋找這個記錄檔中的錯誤：

    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\svagents*.log 

2. 使用這篇文章中的程序的其餘部分可以協助您解決資料上傳的問題。



## <a name="step-8-check-whether-the-process-server-is-pushing-data"></a>步驟 8：檢查處理序伺服器是否正在推送資料

檢查處理序伺服器是否正在主動推送資料至 Azure。

  1. 在處理伺服器上，開啟 [工作管理員] \(按 Ctrl+Shift+Esc\)。
  2. 選取 **效能**索引標籤 >**開啟資源監視器**。
  3. 在 [**資源監視器**頁面上，選取**網路**] 索引標籤。底下**網路活動的處理序**，查看 cbengine.exe 是否主動傳送資料的大型 vNotolume。

       ![網路活動的程序下的磁碟區](./media/vmware-physical-azure-troubleshoot-process-server/cbengine.png)

  如果 cbengine.exe 未傳送大量資料，請完成下列各節中的步驟。

## <a name="step-9-check-the-process-server-connection-to-azure-blob-storage"></a>步驟 9：檢查處理序伺服器連線到 Azure blob 儲存體

1. 在 [資源監視器]，選取**cbengine.exe**。
2. 底下**TCP 連線**，查看是否有從處理序伺服器到 Azure 儲存體的連線。

  ![Cbengine.exe 與 Azure Blob 儲存體 URL 之間的連線](./media/vmware-physical-azure-troubleshoot-process-server/rmonitor.png)

### <a name="check-services"></a>檢查服務

如果沒有來自處理伺服器連線至 Azure blob 儲存體 URL，請確認服務正在執行。

1. 在 [控制台] 中選取**Services**。
2. 確認下列服務正在執行：

    - cxprocessserver
    - InMage Scout VX Agent – Sentinel/Outpost
    - Microsoft Azure 復原服務代理程式
    - Microsoft Azure Site Recovery 服務
    - tmansvc

3. 啟動或重新啟動任何未執行的服務。
4. 確認處理序伺服器已連線且可存取。 

## <a name="step-10-check-the-process-server-connection-to-azure-public-ip-address"></a>步驟 10： 請檢查處理序伺服器連線至 Azure 的公用 IP 位址

1. 在處理序伺服器上，在 **%programfiles%\Microsoft Azure Recovery Services Agent\Temp**，開啟最新的 CBEngineCurr.errlog 檔案。
2. 在檔案中，搜尋**443**，或字串**連線嘗試失敗，**。

  ![在 [Temp] 資料夾中的錯誤記錄檔](./media/vmware-physical-azure-troubleshoot-process-server/logdetails1.png)

3. 如果您發現問題時，位於 CBEngineCurr.currLog 檔案中您的 Azure 公用 IP 位址，使用連接埠 443:

  `telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443`

5. 在命令列中處理序伺服器上，使用 Telnet 偵測您的 Azure 公用 IP 位址。
6. 如果您無法連線，請遵循下一個程序。

## <a name="step-11-check-process-server-firewall-settings"></a>步驟 11：檢查處理序伺服器的防火牆設定。 

檢查處理序伺服器上的 IP 位址型防火牆是否封鎖存取。

1. 針對 IP 位址為基礎的防火牆規則：

    a） 下載的完整清單[Microsoft Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。

    b） 新增至您的防火牆設定，以確保防火牆可讓 Azure （和預設 HTTPS 連接埠 443） 通訊的 IP 位址範圍。

    c） 允許 IP 位址範圍的 Azure 區域的訂用帳戶，並針對 Azure 美國西部區域 （用於存取控制和身分識別管理）。

2. URL 型防火牆新增至防火牆設定下表中列出的 Url。

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  


## <a name="step-12-verify-process-server-proxy-settings"></a>步驟 12：確認處理序伺服器 proxy 設定 

1. 如果您使用 Proxy 伺服器，請確定 DNS 伺服器可解析 Proxy 伺服器名稱。 檢查您設定登錄機碼中的組態伺服器時所提供的值**HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Site Recovery\ProxySettings**。
2. 請確定，Azure Site Recovery 代理程式所使用的相同設定，將資料傳送。

    a） 搜尋**Microsoft Azure 備份**。

    b） 開放**Microsoft Azure 備份**，然後選取**動作** > **變更屬性**。

    c） 在**Proxy 設定** 索引標籤，proxy 位址必須與所示的登錄設定的 proxy 位址相同。 如果不同，請將它變更為相同的位址。

## <a name="step-13-check-bandwidth"></a>步驟 13：檢查頻寬

增加處理序伺服器與 Azure 之間的頻寬，並檢查是否仍然發生此問題。


## <a name="next-steps"></a>後續步驟

如果您需要更多協助，請將您的問題貼到 [Azure Site Recovery 論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr) \(英文\) 中。 

[green]: ./media/vmware-physical-azure-troubleshoot-process-server/green.png
[yellow]: ./media/vmware-physical-azure-troubleshoot-process-server/yellow.png
[red]: ./media/vmware-physical-azure-troubleshoot-process-server/red.png