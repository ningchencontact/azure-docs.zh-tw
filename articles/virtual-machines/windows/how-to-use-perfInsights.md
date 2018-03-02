---
title: "如何在 Microsoft Azure 中使用 PerfInsights | Microsoft Docs"
description: "了解如何使用 PerfInsights 針對 Windows VM 效能問題進行疑難排解。"
services: virtual-machines-windows'
documentationcenter: 
author: genlin
manager: cshepard
editor: na
tags: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: f15875610e2035c6f4c10c36e19c02f3e045b3ea
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2017
---
# <a name="how-to-use-perfinsights"></a>如何使用 PerfInsights 

[PerfInsights](http://aka.ms/perfinsightsdownload) 是自動化指令碼，可收集有用的診斷資訊。 也會執行 I/O 壓力負載，並提供分析報告以協助針對 Azure 中的 Windows 虛擬機器效能問題進行疑難排解。 藉由安裝 [Azure 效能診斷 VM 擴充功能](performance-diagnostics-vm-extension.md)，您可以在虛擬機器上將它當作獨立指令碼執行，或直接從入口網站執行。

如果您遇到虛擬機器的效能問題，請在連絡支援人員之前，先執行這個指令碼。

## <a name="supported-troubleshooting-scenarios"></a>支援的疑難排解案例

PerfInsights 可以收集並分析多種資訊。 下列幾節會說明常見案例。

### <a name="collect-basic-configuration"></a>收集基本組態 

此案例會收集磁碟設定和其他重要資訊，包括：

-   事件記錄檔

-   所有傳入和傳出連線的網路狀態

-   網路和防火牆組態設定

-   目前正在系統上執行之所有應用程式的工作清單

-   虛擬機器的 msinfo32 所建立的資訊檔案

-   Microsoft SQL Server 資料庫組態設定 (如果 VM 被識別為執行 SQL Server 的伺服器)

-   儲存體可靠性計數器

-   重要的 Windows Hotfix

-   已安裝的篩選器驅動程式

這是被動的資訊集合，應該不會影響系統。 

>[!Note]
>下列每一個案例都會自動包含此案例。

### <a name="benchmarking"></a>效能評定

此案例會為連接至 VM 的所有磁碟機執行 [Diskspd](https://github.com/Microsoft/diskspd) 基準測試 (IOPS 和 MBPS)。 

> [!Note]
> 此案例會影響系統，所以不應該在實際的生產系統上執行。 如有必要，請在專用的維護視窗中執行此案例，以免發生任何問題。 因追蹤或基準測試所增加的工作負載，可能會對 VM 的效能造成負面影響。
>

### <a name="slow-vm-analysis"></a>慢速虛擬機器分析 

此案例會使用 Generalcounters.txt 檔案中指定的計數器來執行[效能計數器](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx)追蹤。 如果 VM 被識別為執行 SQL Server 的伺服器，它會執行效能計數器追蹤。 其使用在 Sqlcounters.txt 檔案中找到的計數器來達成，也包含效能診斷資料。

### <a name="slow-vm-analysis-and-benchmarking"></a>慢速虛擬機器分析和效能評定

此案例會執行[效能計數器](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx)追蹤，後面接著 [Diskspd](https://github.com/Microsoft/diskspd) 基準測試。 

> [!Note]
> 此案例會影響系統，所以不應該在實際的生產系統上執行。 如有必要，請在專用的維護視窗中執行此案例，以免發生任何問題。 因追蹤或基準測試所增加的工作負載，可能會對 VM 的效能造成負面影響。
>

### <a name="azure-files-analysis"></a>Azure 檔案分析 

此案例會同時執行特殊的效能計數器擷取與網路追蹤。 擷取包含所有伺服器訊息區塊 (SMB) 用戶端共用計數器。 以下是屬於擷取的一些重要 SMB 用戶端共用效能計數器：

| **類型**     | **SMB 用戶端共用計數器** |
|--------------|-------------------------------|
| IOPS         | 資料要求/秒             |
|              | 讀取要求/秒             |
|              | 寫入要求/秒            |
| Latency      | 平均秒/資料要求         |
|              | 平均秒/讀取                 |
|              | 平均秒/寫入                |
| IO 大小      | Avg.位元組/資料要求       |
|              | Avg.位元組/讀取               |
|              | Avg.位元組/寫入              |
| Throughput   | 資料位元組/秒                |
|              | 讀取位元組/秒                |
|              | 寫入位元組/秒               |
| 佇列長度 | Avg.讀取佇列長度        |
|              | Avg.寫入佇列長度       |
|              | Avg.資料佇列長度        |

### <a name="custom-slow-vm-analysis"></a>自訂慢速虛擬機器分析 

當您執行自訂慢速 VM 分析時，您可選取要平行執行的追蹤。 您可以視需要全部執行 (效能計數器、Xperf、網路和 StorPort)。 如已選取，此工具會在完成追蹤之後，執行 Diskspd 基準測試。 

> [!Note]
> 此案例會影響系統，所以不應該在實際的生產系統上執行。 如有必要，請在專用的維護視窗中執行此案例，以免發生任何問題。 因追蹤或基準測試所增加的工作負載，可能會對 VM 的效能造成負面影響。
>

## <a name="what-kind-of-information-is-collected-by-the-script"></a>指令碼收集何種資訊？

收集的資訊包括 Windows VM、磁碟或儲存集區設定、效能計數器、記錄和各種追蹤。 這取決於您所使用的效能案例。 下表提供詳細資料：

|收集的資料                              |  |  | 效能測試案例 |  |  | |
|----------------------------------|----------------------------|------------------------------------|--------------------------|--------------------------------|----------------------|----------------------|
|                              | 收集基本組態 | 效能評定 | 慢速虛擬機器分析 | 慢速虛擬機器分析和效能評定 | Azure 檔案分析 | 自訂慢速虛擬機器分析 |
| 來自事件記錄的資訊      | yes                        | yes                                | yes                      | yes                            | yes                  | yes                  |
| 系統資訊               | yes                        | yes                                | yes                      | yes                            | yes                  | yes                  |
| 磁碟區對應                       | yes                        | yes                                | yes                      | yes                            | yes                  | yes                  |
| 磁碟對應                         | yes                        | yes                                | yes                      | yes                            | yes                  | yes                  |
| 正在執行的工作                    | yes                        | yes                                | yes                      | yes                            | yes                  | yes                  |
| 儲存體可靠性計數器     | yes                        | yes                                | yes                      | yes                            | yes                  | yes                  |
| 儲存體資訊              | yes                        | yes                                | yes                      | yes                            | yes                  | yes                  |
| Fsutil 輸出                    | yes                        | yes                                | yes                      | yes                            | yes                  | yes                  |
| 篩選器驅動程式資訊               | yes                        | yes                                | yes                      | yes                            | yes                  | yes                  |
| Netstat 輸出                   | yes                        | yes                                | yes                      | yes                            | yes                  | yes                  |
| 網路組態            | yes                        | yes                                | yes                      | yes                            | yes                  | yes                  |
| 防火牆設定           | yes                        | yes                                | yes                      | yes                            | yes                  | yes                  |
| SQL Server 設定         | yes                        | yes                                | yes                      | yes                            | yes                  | yes                  |
| 效能診斷追蹤 * | yes                        | yes                                | yes                      |                                | yes                  | yes                  |
| 效能計數器追蹤 **     |                            |                                    |                          |                                |                      | yes                  |
| SMB 計數器追蹤 **             |                            |                                    |                          |                                | yes                  |                      |
| SQL Server 計數器追蹤 **      |                            |                                    |                          |                                |                      | yes                  |
| XPerf 追蹤                      |                            |                                    |                          |                                |                      | yes                  |
| StorPort 追蹤                   |                            |                                    |                          |                                |                      | yes                  |
| 網路追蹤                    |                            |                                    |                          |                                | yes                  | yes                  |
| Diskspd 基準追蹤 ***      |                            | yes                                |                          | yes                            |                      |                      |
|       |                            |                         |                                                   |                      |                      |

### <a name="performance-diagnostics-trace-"></a>效能診斷追蹤 (*)

在背景中執行規則引擎來收集資料並診斷進行中的效能問題。 目前支援下列規則：

- HighCpuUsage 規則：偵測高 CPU 使用量時段，並顯示在這些時段內前幾名的 CPU 使用量取用者。
- HighDiskUsage 規則：偵測實體磁碟的高磁碟使用量時段，並顯示在這些時段內前幾名的磁碟使用量取用者。
- HighResolutionDiskMetric 規則：顯示每個實體磁碟每 50 毫秒的 IOPS、輸送量和 I/O 延遲計量。 它有助於快速找出磁碟節流時段。
- HighMemoryUsage 規則：偵測高記憶體使用量時段，並顯示在這些時段內前幾名的記憶體使用量取用者。

> [!NOTE] 
> 目前支援包含 .NET Framework 3.5 或更新版本的 Windows 版本。

### <a name="performance-counter-trace-"></a>效能計數器追蹤 (**)

收集下列效能計數器︰

- \Process、\Processor、\Memory、\Thread、\PhysicalDisk 和 \LogicalDisk
- \Cache\Dirty Pages、\Cache\Lazy Write Flushes/sec、\Server\Pool Nonpaged、Failures 和 \Server\Pool Paged Failures
- \Network Interface、\IPv4\Datagrams、\IPv6\Datagrams、\TCPv4\Segments、\TCPv6\Segments、\Network Adapter、\WFPv4\Packets、\WFPv6\Packets、\UDPv4\Datagrams、\UDPv6\Datagrams、\TCPv4\Connection、\TCPv6\Connection、\Network QoS Policy\Packets、\Per Processor Network Interface Card Activity 和 \Microsoft Winsock BSP 下的已選取計數器

#### <a name="for-sql-server-instances"></a>SQL Server 執行個體
- \SQL Server:Buffer Manager、\SQLServer:Resource Pool Stats 和 \SQLServer:SQL Statistics\
- \SQLServer:Locks、\SQLServer:General、Statistics
- \SQLServer:Access 方法

#### <a name="for-azure-files"></a>Azure 檔案
\SMB 用戶端共用

### <a name="diskspd-benchmark-trace-"></a>Diskspd 基準追蹤 (***)
Diskspd I/O 工作負載測試 (OS 磁碟 [寫入] 和集區磁碟 [讀取/寫入])

## <a name="run-the-perfinsights-script-on-your-vm"></a>在 VM 上執行 PerfInsights 指令碼

### <a name="what-do-i-have-to-know-before-i-run-the-script"></a>執行指令碼之前應該知道什麼？ 

#### <a name="script-requirements"></a>指令碼需求

-  此指令碼必須在發生效能問題的 VM 上執行。 

-  支援下列作業系統：Windows Server 2008 R2、Windows Server 2012、Windows Server 2012 R2 和 Windows Server 2016；Windows 8.1 和 Windows 10。

#### <a name="possible-problems-when-you-run-the-script-on-production-vms"></a>當您在生產 VM 上執行指令碼時可能發生的問題

-  對於設定為使用 Xperf 或 Diskspd 的任何基準案例或「自訂慢速虛擬機器分析」案例，指令碼可能會對虛擬機器效能產生不良的影響。 您不得在生產環境中執行這些案例。

-  對於設定為使用 Diskspd 的任何基準案例或「自訂慢速虛擬機器分析」案例，請確定 I/O 工作負載不受其他背景活動干擾。

-  根據預設，指令碼會使用暫存磁碟機收集資料。 如果追蹤長時間保持啟用，則收集的資料量會有所影響。 這會降低暫存磁碟的可用空間，從而影響依賴此磁碟機的任何應用程式。

### <a name="how-do-i-run-perfinsights"></a>如何執行 PerfInsights？ 

藉由安裝 [Azure 效能診斷 VM 擴充功能](performance-diagnostics-vm-extension.md)，您可以在虛擬機器上執行 PerfInsights。 也可以將它當作獨立的指令碼執行。 

**從 Azure 入口網站安裝及執行 PerfInsights**

如需此選項的詳細資訊，請參閱[安裝 Azure 效能診斷 VM 擴充功能](performance-diagnostics-vm-extension.md#install-the-extension)。  

**在獨立模式中執行 PerfInsights 指令碼**

若要執行 PerfInsights 指令碼，請遵循下列步驟：


1. 下載 [PerfInsights.zip](http://aka.ms/perfinsightsdownload)。

2. 解除封鎖 PerfInsights.zip 檔案。 若要這樣做，請以滑鼠右鍵按一下 PerfInsights.zip 檔案，然後選取 [屬性]。 在 [一般] 索引標籤上，選取 [解除封鎖]，然後選取 [確定]。 如此可確保指令碼在執行時不會出現任何額外的安全性提示。  

    ![PerfInsights 屬性的螢幕擷取畫面 (已醒目提示 [解除封鎖])](media/how-to-use-perfInsights/unlock-file.png)

3.  將 PerfInsights.zip 檔案解壓縮至暫存磁碟機 (根據預設，這通常為 D 磁碟機)。 壓縮的檔案應該包含下列檔案和資料夾：

    ![zip 資料夾中的檔案螢幕擷取畫面](media/how-to-use-perfInsights/file-folder.png)

4.  以系統管理員身分開啟 Windows PowerShell，然後執行 PerfInsights.ps1 指令碼。

    ```
    cd <the path of PerfInsights folder >
    Powershell.exe -ExecutionPolicy UnRestricted -NoProfile -File .\\PerfInsights.ps1
    ```

    您可能必須輸入 "y"，確認您想要變更執行原則。

    在 [注意事項與同意] 對話方塊中，您可以選擇與 Microsoft 支援服務共用診斷資訊。 您也必須同意授權合約才能繼續。 確認選項後，選取 [執行指令碼]。

    ![[注意事項與同意] 對話方塊的螢幕擷取畫面](media/how-to-use-perfInsights/disclaimer.png)

5.  如可用，請在執行指令碼時，提交案例編號。 然後選取 [確定]。
    
    ![支援識別碼對話方塊的螢幕擷取畫面](media/how-to-use-perfInsights/enter-support-number.png)

6.  選取暫存磁碟機。 指令碼可以自動偵測磁碟機的磁碟機代號。 如果在這個階段發生任何問題，系統可能會提示您選取磁碟機 (預設磁碟機 D)。 產生的記錄會儲存在 log\_collection 資料夾。 在您輸入或接受磁碟機代號後，請選取 [確定]。

    ![暫存磁碟機對話方塊的螢幕擷取畫面](media/how-to-use-perfInsights/enter-drive.png)

7.  從提供的清單中選取疑難排解案例。

       ![疑難排解案例清單的螢幕擷取畫面](media/how-to-use-perfInsights/select-scenarios.png)

您也可以執行 PerfInsights 不顯示 UI。 下列命令會執行「慢速虛擬機器分析」疑難排解案例 (不顯示 UI)。 它會提示您同意與步驟 4 中所述的相同免責聲明和授權條款。

        powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -NoGui -Scenario vmslow -TracingDuration 30"

如果您希望以無訊息模式執行 PerfInsights，請使用 **-AcceptDisclaimerAndShareDiagnostics** 參數。 例如，使用下列命令：

        powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -NoGui -Scenario vmslow -TracingDuration 30 -AcceptDisclaimerAndShareDiagnostics"

### <a name="how-do-i-troubleshoot-issues-while-running-the-script"></a>如何在執行指令碼時針對問題進行疑難排解？

如果指令碼異常終止，您可以執行指令碼搭配 cleanup 參數，以回到一致狀態，如下所示：

    powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -Cleanup"

如果暫存磁碟機在自動偵測期間發生任何問題，系統可能會提示您選取磁碟機 (預設磁碟機 D)。

![暫存磁碟機對話方塊的螢幕擷取畫面](media/how-to-use-perfInsights/enter-drive.png)

指令碼會解除安裝公用程式工具，並移除暫存資料夾。

### <a name="troubleshoot-other-script-issues"></a>針對其他指令碼問題進行疑難排解 

如果在執行指令碼時發生任何問題，請按 Ctrl+C 中斷指令碼。 如果在嘗試幾次之後指令碼仍持續失敗，請在啟動時使用 "-Debug" 參數選項，在偵錯模式中執行指令碼。

失敗發生之後，請複製完整的 PowerShell 主控台輸出，將它傳送給協助您疑難排解問題的 Microsoft 支援專員。

### <a name="how-do-i-run-the-script-in-custom-slow-vm-analysis-mode"></a>如何在「自訂慢速虛擬機器分析」模式中執行指令碼？

藉由選取 [自訂慢速虛擬機器分析]，您可以平行啟用數個追蹤 (以使用 Shift 鍵選取多項追蹤)：

![案例清單的螢幕擷取畫面](media/how-to-use-perfInsights/select-scenario.png)

當您選取效能計數器追蹤、XPerf 追蹤、網路追蹤或 Storport 追蹤案例時，請遵循後續對話方塊中的指示。 嘗試重現啟動追蹤之後的低效能問題。

您可透過下列對話方塊啟動追蹤：

![[啟動效能計數器追蹤] 對話方塊的螢幕擷取畫面](media/how-to-use-perfInsights/start-trace-message.png)

若要停止追蹤，您必須確認第二個對話方塊中的命令。

![[停止效能計數器追蹤] 對話方塊的螢幕擷取畫面](media/how-to-use-perfInsights/stop-trace-message.png)
![[停止所有追蹤] 對話方塊的螢幕擷取畫面](media/how-to-use-perfInsights/ok-trace-message.png)

當追蹤或作業都完成之後，D:\\log\_collection (或暫存磁碟機) 中會出現一個新檔案。 檔案名稱為 **CollectedData\_yyyy-MM-dd\_hh\_mm\_ss.zip。** 您可以將此檔案傳送給支援專員進行分析。

## <a name="review-the-diagnostics-report"></a>檢閱診斷報告

在 **CollectedData\_yyyy-MM-dd\_hh\_mm\_ss.zip** 檔案內，您可找到詳細說明 PerfInsights 結果的 HTML 報告。 若要檢閱報表，請展開 **CollectedData\_yyyy-MM-dd\_hh\_mm\_ss.zip** 檔案，然後開啟 **PerfInsights Report.html** 檔案。

選取 [Findings] \(結果) 索引標籤。

![PerfInsights 報告的螢幕擷取畫面](media/how-to-use-perfInsights/findingtab.png)
![PerfInsights 報告的螢幕擷取畫面](media/how-to-use-perfInsights/findings.PNG)

> [!NOTE] 
> 分類為重大的結果是可能會造成效能問題的已知問題。 分類為重要的結果表示不一定會造成效能問題的非最佳化設定。 分類為資訊的結果是僅用來參考的資訊說明。

檢閱所有重大和重要結果的建議和連結。 了解這些結果如何影響效能，以及效能最佳化組態的最佳做法。

### <a name="storage-tab"></a>儲存體索引標籤

**結果**區段會顯示有關儲存體的各種結果與建議。

[DiskMap] 和 [VolumeMap] 區段說明邏輯磁碟區和實體磁碟如何彼此相關。

在實體磁碟檢視方塊 (磁碟對應) 中，資料表會顯示在磁碟上執行的所有邏輯磁碟區。 在下列範例中，**PhysicalDrive2** 執行在多個分割區建立的 2 個邏輯磁碟區 (J 和 H)：

![磁碟索引標籤的螢幕擷取畫面](media/how-to-use-perfInsights/disktab.png)

在磁碟區檢視方塊中 (磁碟區對應)，資料表會顯示每個邏輯磁碟區下的所有實體磁碟。 請注意，對於 RAID/動態磁碟，您可能會在多個實體磁碟上執行邏輯磁碟區。 在下列範例中，*C:\\mount* 是在實體磁碟 2 和 3 上設定為 SpannedDisk 的掛接點：

![磁碟區索引標籤的螢幕擷取畫面](media/how-to-use-perfInsights/volumetab.png)

### <a name="sql-tab"></a>SQL 索引標籤

如果目標 VM 裝載任何 SQL Server 執行個體，您將在報表中看到名為 **SQL** 的其他索引標籤：

![SQL 索引標籤的螢幕擷取畫面](media/how-to-use-perfInsights/sqltab.png)

本區段包含 [結果] 索引標籤，以及裝載在虛擬機器上的每個 SQL Server 執行個體的其他索引標籤。

[結果] 索引標籤包含所有發現的 SQL 效能相關問題清單及建議事項。

下列範例中會顯示 **PhysicalDrive0** (執行 C 磁碟機)。 這是因為 **modeldev** 和 **modellog** 檔案都位於 C 磁碟機上，且為不同的類型 (例如它們分別為資料檔和交易記錄)。

![記錄資訊的螢幕擷取畫面](media/how-to-use-perfInsights/loginfo.png)

SQL Server 的特定執行個體索引標籤包含一般區段，其中顯示所選執行個體的基本資訊。 這些索引標籤也包含進階資訊的其他區段，包括設定、組態和使用者選項。

### <a name="diagnostic-tab"></a>診斷索引標籤
[診斷] 索引標籤包含 PerfInsights 執行期間電腦上的最上層 CPU、磁碟和記憶體取用相關資訊。 您也可以找到以下相關資訊：可能缺少系統的重大修補程式、工作清單和重要系統事件。 

## <a name="references-to-the-external-tools-used"></a>所用外部工具的參考

### <a name="diskspd"></a>Diskspd

Diskspd 是來自 Microsoft 的儲存體負載產生器和效能測試工具。 如需詳細資訊，請參閱 [Diskspd](https://github.com/Microsoft/diskspd)。

### <a name="xperf"></a>Xperf

Xperf 是命令列工具，會從 Windows 效能工具組擷取追蹤。 如需詳細資訊，請參閱 [Windows 效能工具組 – Xperf](https://blogs.msdn.microsoft.com/ntdebugging/2008/04/03/windows-performance-toolkit-xperf/)。

## <a name="next-steps"></a>後續步驟

您可以將診斷記錄和報告上傳至 Microsoft 支援服務，以供進一步檢閱。 支援服務人員可能會要求您傳送 PerfInsights 產生的輸出，以協助進行疑難排解程序。

下列螢幕擷取畫面會顯示類似您可能收到的一則訊息：

![來自 Microsoft 支援服務之範例訊息的螢幕擷取畫面](media/how-to-use-perfInsights/supportemail.png)

請依照訊息中的指示來存取檔案傳輸工作區。 為增加安全性，您必須在第一次使用時變更密碼。

登入之後，您會發現一個對話方塊，可上傳 PerfInsights 收集的 **CollectedData\_yyyy-MM-dd\_hh\_mm\_ss.zip** 檔案。
