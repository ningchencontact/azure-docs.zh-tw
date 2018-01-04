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
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2017
---
# <a name="how-to-use-perfinsights"></a>如何使用 PerfInsights 

[PerfInsights](http://aka.ms/perfinsightsdownload)是自動化的指令碼收集診斷資訊。 也會執行 I/O 壓力負載，並提供可協助疑難排解在 Azure 中的 Windows 虛擬機器的效能問題分析報表。 這可以是虛擬機器上執行做為獨立指令碼，或直接從入口網站安裝[Azure 效能診斷 VM 延伸模組](performance-diagnostics-vm-extension.md)。

如果您之前連絡支援人員遇到與虛擬機器的效能問題，請執行這個指令碼。

## <a name="supported-troubleshooting-scenarios"></a>支援的疑難排解案例

PerfInsights 可以收集並分析資訊的數種。 下列各節涵蓋常見案例。

### <a name="collect-basic-configuration"></a>收集基本組態 

此案例，收集的磁碟組態和其他重要資訊，包括：

-   事件記錄檔

-   所有傳入和傳出連線的網路狀態

-   網路和防火牆組態設定

-   目前正在系統上執行之所有應用程式的工作清單

-   建立虛擬機器 msinfo32 資訊檔案

-   Microsoft SQL Server 資料庫組態設定 (如果 VM 被識別為執行 SQL Server 的伺服器)

-   儲存體可靠性計數器

-   重要的 Windows Hotfix

-   已安裝的篩選器驅動程式

這是被動的資訊集合，應該不會影響系統。 

>[!Note]
>下列每一個案例都會自動包含此案例。

### <a name="benchmarking"></a>效能評定

這種情況下執行[Diskspd](https://github.com/Microsoft/diskspd)基準測試 （IOPS 和 MBPS） 為連接至 VM 的所有磁碟機。 

> [!Note]
> 此案例可能會影響系統中，而且不應該在實際生產系統上執行。 如有必要，請在專用的維護視窗中執行此案例，以免發生任何問題。 增加的工作負載所造成的追蹤或基準測試的測試可能會影響您 VM 的效能。
>

### <a name="slow-vm-analysis"></a>慢速虛擬機器分析 

此案例會使用 Generalcounters.txt 檔案中指定的計數器來執行[效能計數器](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx)追蹤。 如果 VM 識別為執行 SQL Server 的伺服器，它會執行效能計數器追蹤。 它是藉由使用 Sqlcounters.txt 檔案中找到的計數器，它也包含診斷的效能資料。

### <a name="slow-vm-analysis-and-benchmarking"></a>慢速虛擬機器分析和效能評定

這種情況下執行[效能計數器](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx)追蹤，後面接著[Diskspd](https://github.com/Microsoft/diskspd)基準測試。 

> [!Note]
> 此案例可能會影響系統中，而且不應該在實際生產系統上執行。 如有必要，請在專用的維護視窗中執行此案例，以免發生任何問題。 增加的工作負載所造成的追蹤或基準測試的測試可能會影響您 VM 的效能。
>

### <a name="azure-files-analysis"></a>Azure 檔案分析 

此案例會同時執行特殊的效能計數器擷取與網路追蹤。 擷取包含所有伺服器訊息區塊 (SMB) 用戶端共用的計數器。 以下是屬於擷取的一些重要 SMB 用戶端共用效能計數器：

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

當您執行自訂的慢速 VM 分析時，您會選取追蹤以平行方式執行。 您可以執行其所有 （效能計數器、 Xperf、 網路和 StorPort） 如果您想要。 完成追蹤之後，此工具會執行 Diskspd benchmark 中，如果已選取。 

> [!Note]
> 此案例可能會影響系統中，而且不應該在實際生產系統上執行。 如有必要，請在專用的維護視窗中執行此案例，以免發生任何問題。 增加的工作負載所造成的追蹤或基準測試的測試可能會影響您 VM 的效能。
>

## <a name="what-kind-of-information-is-collected-by-the-script"></a>指令碼收集何種資訊？

記錄檔的 Windows VM、 磁碟或儲存體集區設定效能計數器的相關資訊，並在各種追蹤收集。 這取決於您使用的效能案例。 下表提供詳細資料：

|收集的資料                              |  |  | 效能測試的案例 |  |  | |
|----------------------------------|----------------------------|------------------------------------|--------------------------|--------------------------------|----------------------|----------------------|
|                              | 收集基本組態 | 效能評定 | 慢速虛擬機器分析 | 慢速虛擬機器分析和效能評定 | Azure 檔案分析 | 自訂慢速虛擬機器分析 |
| 從事件記錄檔的資訊      | 是                        | 是                                | 是                      | 是                            | 是                  | 是                  |
| 系統資訊               | 是                        | 是                                | 是                      | 是                            | 是                  | 是                  |
| 磁碟區對應                       | 是                        | 是                                | 是                      | 是                            | 是                  | 是                  |
| 磁碟網站導覽                         | 是                        | 是                                | 是                      | 是                            | 是                  | 是                  |
| 正在執行的工作                    | 是                        | 是                                | 是                      | 是                            | 是                  | 是                  |
| 儲存體可靠性計數器     | 是                        | 是                                | 是                      | 是                            | 是                  | 是                  |
| 儲存體資訊              | 是                        | 是                                | 是                      | 是                            | 是                  | 是                  |
| Fsutil 輸出                    | 是                        | 是                                | 是                      | 是                            | 是                  | 是                  |
| 篩選器驅動程式資訊               | 是                        | 是                                | 是                      | 是                            | 是                  | 是                  |
| Netstat 輸出                   | 是                        | 是                                | 是                      | 是                            | 是                  | 是                  |
| 網路組態            | 是                        | 是                                | 是                      | 是                            | 是                  | 是                  |
| 防火牆設定           | 是                        | 是                                | 是                      | 是                            | 是                  | 是                  |
| SQL Server 設定         | 是                        | 是                                | 是                      | 是                            | 是                  | 是                  |
| 效能診斷追蹤 * | 是                        | 是                                | 是                      |                                | 是                  | 是                  |
| 效能計數器追蹤 * *     |                            |                                    |                          |                                |                      | 是                  |
| SMB 計數器追蹤 * *             |                            |                                    |                          |                                | 是                  |                      |
| SQL Server 計數器追蹤 * *      |                            |                                    |                          |                                |                      | 是                  |
| Xperf 追蹤                      |                            |                                    |                          |                                |                      | 是                  |
| StorPort 追蹤                   |                            |                                    |                          |                                |                      | 是                  |
| 網路追蹤                    |                            |                                    |                          |                                | 是                  | 是                  |
| Diskspd 基準追蹤 * * *      |                            | 是                                |                          | 是                            |                      |                      |
|       |                            |                         |                                                   |                      |                      |

### <a name="performance-diagnostics-trace-"></a>效能診斷追蹤 （*）

在背景中執行規則引擎來收集資料並診斷進行中的效能問題。 目前支援下列規則：

- HighCpuUsage 規則： 偵測到高 CPU 使用量的期間，並在這些時間週期期間顯示的最上層的 CPU 使用量取用者。
- HighDiskUsage 規則： 偵測到高磁碟使用量期間實體磁碟上，並在這些時間週期期間顯示最大的磁碟使用量取用者。
- HighResolutionDiskMetric 規則： 顯示每秒 50 毫秒，每個實體磁碟的 IOPS、 輸送量和 I/O 延遲度量資訊。 它有助於快速找出磁碟節流時段。
- HighMemoryUsage 規則：偵測高記憶體使用量時段，並顯示在這些時段內前幾名的記憶體使用量取用者。

> [!NOTE] 
> 目前支援包含 .NET Framework 3.5 或更新版本的 Windows 版本。

### <a name="performance-counter-trace-"></a>效能計數器追蹤 （*）

會收集下列效能計數器：

- \Process、 \Processor、 \Memory、 \Thread、 \PhysicalDisk 和 \LogicalDisk
- \Cache\Dirty 頁面，\Cache\Lazy 寫入 Flushes/sec，\Server\Pool 未分頁，失敗和 \Server\Pool 分頁失敗
- 選取的計數器下 \Network 介面介面 \IPv4\Datagrams、 \IPv6\Datagrams、 \TCPv4\Segments、 \TCPv6\Segments、 \Network 配接器、 \WFPv4\Packets、 \WFPv6\Packets、 \UDPv4\Datagrams、 \UDPv6\Datagrams、 \TCPv4\Connection、 \TCPv6\Connection，\網路 QoS Policy\Packets、 \Per 處理器網路介面卡活動和 \Microsoft Winsock BSP

#### <a name="for-sql-server-instances"></a>SQL Server 執行個體
- \SQL server: Buffer Manager、 \SQLServer:Resource 集區統計資料和 \SQLServer:SQL Statistics\
- \SQLServer:Locks、\SQLServer:General、Statistics
- \SQLServer:Access 方法

#### <a name="for-azure-files"></a>Azure 檔案
\SMB 用戶端共用

### <a name="diskspd-benchmark-trace-"></a>Diskspd 基準追蹤 （*）
Diskspd I/O 工作負載測試 （作業系統磁碟 [寫入] 及 [讀取/寫入] 的集區磁碟機）

## <a name="run-the-perfinsights-script-on-your-vm"></a>在您的 VM 上執行 PerfInsights 指令碼

### <a name="what-do-i-have-to-know-before-i-run-the-script"></a>執行指令碼之前應該知道什麼？ 

#### <a name="script-requirements"></a>指令碼需求

-  此指令碼必須在發生效能問題的 VM 上執行。 

-  支援下列作業系統： Windows Server 2008 R2、 Windows Server 2012、 Windows Server 2012 R2 和 Windows Server 2016。Windows 8.1 和 Windows 10。

#### <a name="possible-problems-when-you-run-the-script-on-production-vms"></a>當您在實際執行的 Vm 上執行指令碼時可能發生的問題

-  對於效能評定的任何情況或設定要使用 Xperf 或 Diskspd 的 「 自訂緩慢 VM 分析 」 案例，指令碼可能產生不良影響 VM 的效能。 您不應該在生產環境中執行這些案例。

-  任何效能評定的案例或 「 自訂緩慢 VM 分析 」 案例中，設定為使用 Diskspd，請確定沒有其他背景活動干擾 I/O 工作負載。

-  根據預設，指令碼會使用暫存磁碟機收集資料。 如果追蹤長時間保持啟用，則收集的資料量會有所影響。 這可以減少在暫存磁碟空間的可用性，並因此可能會影響依賴此磁碟機的任何應用程式。

### <a name="how-do-i-run-perfinsights"></a>如何執行 PerfInsights？ 

您也可以安裝在虛擬機器上執行 PerfInsights [Azure 效能診斷 VM 延伸模組](performance-diagnostics-vm-extension.md)。 您也可以執行為獨立的指令碼。 

**從 Azure 入口網站安裝及執行 PerfInsights**

如需有關這個選項的詳細資訊，請參閱[安裝 Azure 效能診斷 VM 延伸模組](performance-diagnostics-vm-extension.md#install-the-extension)。  

**在獨立模式中執行 PerfInsights 指令碼**

若要執行 PerfInsights 指令碼，請遵循下列步驟：


1. 下載 [PerfInsights.zip](http://aka.ms/perfinsightsdownload)。

2. 解除封鎖 PerfInsights.zip 檔案。 若要這樣做，以滑鼠右鍵按一下 PerfInsights.zip 檔案，並選取**屬性**。 在**一般**索引標籤上，選取**解除封鎖**，然後選取**確定**。 這可確保沒有任何額外的安全性提示，指令碼會執行。  

    ![螢幕擷取畫面的 PerfInsights 屬性，其中包含反白顯示的解除封鎖](media/how-to-use-perfInsights/unlock-file.png)

3.  展開壓縮的 PerfInsights.zip 檔案至暫存磁碟機 （根據預設，這通常是在 D 磁碟機）。 壓縮的檔案應該包含下列檔案和資料夾：

    ![Zip 資料夾中檔案的螢幕擷取畫面](media/how-to-use-perfInsights/file-folder.png)

4.  以系統管理員身分開啟 Windows PowerShell，然後執行 PerfInsights.ps1 指令碼。

    ```
    cd <the path of PerfInsights folder >
    Powershell.exe -ExecutionPolicy UnRestricted -NoProfile -File .\\PerfInsights.ps1
    ```

    您可能必須輸入"y"，以確認您想要變更執行原則。

    在**請注意，且同意**對話方塊中，您可以選擇與 Microsoft 支援服務共用的診斷資訊。 您也必須同意授權合約才能繼續。 您的選取項目，，然後選取 **執行指令碼**。

    ![請注意螢幕擷取畫面和同意對話方塊](media/how-to-use-perfInsights/disclaimer.png)

5.  如果可用的話，當您執行指令碼，提交案例數。 然後選取 [確定]。
    
    ![支援 [識別碼] 對話方塊的螢幕擷取畫面](media/how-to-use-perfInsights/enter-support-number.png)

6.  選取暫存磁碟機。 指令碼可以自動偵測該磁碟機的磁碟機代號。 如果在這個階段發生任何問題，系統可能會提示您選取磁碟機 (預設磁碟機 D)。 產生的記錄檔會儲存在這裡，記錄檔中\_集合資料夾。 您輸入或接受磁碟機代號後，請選取**確定**。

    ![暫存磁碟機 對話方塊的螢幕擷取畫面](media/how-to-use-perfInsights/enter-drive.png)

7.  從提供的清單中選取疑難排解案例。

       ![疑難排解案例清單的螢幕擷取畫面](media/how-to-use-perfInsights/select-scenarios.png)

您也可以執行 PerfInsights 不顯示 UI。 下列命令會執行 「 慢速 VM 分析 」 疑難排解案例中的不顯示 UI。 它會提示您同意與步驟 4 中所述的相同免責聲明和授權條款。

        powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -NoGui -Scenario vmslow -TracingDuration 30"

如果您希望以無訊息模式執行 PerfInsights，請使用 **-AcceptDisclaimerAndShareDiagnostics** 參數。 例如，使用下列命令：

        powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -NoGui -Scenario vmslow -TracingDuration 30 -AcceptDisclaimerAndShareDiagnostics"

### <a name="how-do-i-troubleshoot-issues-while-running-the-script"></a>如何在執行指令碼時針對問題進行疑難排解？

如果指令碼異常終止，您可以執行指令碼，以及清除參數，如下所示來傳回一致的狀態：

    powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -Cleanup"

如果暫存磁碟機在自動偵測期間發生任何問題，系統可能會提示您選取磁碟機 (預設磁碟機 D)。

![暫存磁碟機 對話方塊的螢幕擷取畫面](media/how-to-use-perfInsights/enter-drive.png)

指令碼會解除安裝公用程式工具，並移除暫存資料夾。

### <a name="troubleshoot-other-script-issues"></a>疑難排解其他指令碼問題 

如果您執行指令碼時，就會發生任何問題，請，按 Ctrl + C 中斷指令碼。 如果您持續嘗試幾次之後，即使發生指令碼失敗，在偵錯模式中執行指令碼使用"-偵錯 」 參數選項，在啟動時。

失敗發生之後，請複製完整的 PowerShell 主控台輸出，將它傳送給協助您疑難排解問題的 Microsoft 支援專員。

### <a name="how-do-i-run-the-script-in-custom-slow-vm-analysis-mode"></a>如何在自訂緩慢 VM 分析 」 模式中執行指令碼？

藉由選取**自訂緩慢 VM 分析**，您可以啟用數個追蹤，以平行方式 （若要選取多個追蹤，請使用 Shift 鍵）：

![案例清單的螢幕擷取畫面](media/how-to-use-perfInsights/select-scenario.png)

當您選取效能計數器追蹤、 Xperf 追蹤、 網路追蹤或 Storport 追蹤案例時，請遵循後續的對話方塊中的指示。 嘗試啟動追蹤之後重現效能緩慢的問題。

您啟動追蹤，以透過下列對話方塊：

![螢幕擷取畫面的啟動效能計數器追蹤對話方塊](media/how-to-use-perfInsights/start-trace-message.png)

若要停止追蹤，您必須確認第二個對話方塊中的命令。

![螢幕擷取畫面的停止效能計數器追蹤對話方塊](media/how-to-use-perfInsights/stop-trace-message.png)
![螢幕擷取畫面的停止所有追蹤對話方塊](media/how-to-use-perfInsights/ok-trace-message.png)

當追蹤或作業都完成之後時，新的檔案會出現在 d:\\記錄\_集合 （或暫存磁碟機）。 檔案的名稱是**CollectedData\_yyyy MM dd\_hh\_公釐\_ss.zip。** 您可以將此檔案傳送給分析的支援代理程式。

## <a name="review-the-diagnostics-report"></a>檢閱診斷報告

內**CollectedData\_yyyy MM dd\_hh\_公釐\_ss.zip**檔案中，您可以找到詳細資料的 PerfInsights 發現 HTML 報表。 若要檢閱報表，請展開 **CollectedData\_yyyy-MM-dd\_hh\_mm\_ss.zip** 檔案，然後開啟 **PerfInsights Report.html** 檔案。

選取 [Findings] \(結果) 索引標籤。

![螢幕擷取畫面的 PerfInsights 報表](media/how-to-use-perfInsights/findingtab.png)
![PerfInsights 報表的螢幕擷取畫面](media/how-to-use-perfInsights/findings.PNG)

> [!NOTE] 
> 發現分類為重要的已知問題可能會造成效能問題。 分類為重要的結果表示不一定會造成效能問題的非最佳化設定。 分類為資訊的結果是僅用來參考的資訊說明。

檢閱建議和所有重大和重要的研究結果的連結。 了解它們如何影響效能，也有關以及效能最佳化組態的最佳作法。

### <a name="storage-tab"></a>[儲存體] 索引標籤

**結果**區段會顯示有關儲存體的各種結果與建議。

**磁碟網站導覽**和**磁碟區對應**章節說明如何邏輯磁碟區和實體磁碟與彼此相關。

在實體磁碟的檢視方塊 （磁碟對應） 下, 表會顯示在磁碟執行的所有邏輯磁碟區。 在下列範例中， **PhysicalDrive2**執行兩個邏輯磁碟區建立多個資料分割 （J 和 H）：

![磁碟索引標籤的螢幕擷取畫面](media/how-to-use-perfInsights/disktab.png)

磁碟區來說 （磁碟區對應表） 中的表格會顯示在每個邏輯磁碟區下的所有實體磁碟。 請注意，對於 RAID/動態磁碟，您可能會在多個實體磁碟上執行的邏輯磁碟區。 在下列範例中， *c:\\掛接*掛接點設定為*SpannedDisk* 2 和 3 的實體磁碟上：

![磁碟區 索引標籤的螢幕擷取畫面](media/how-to-use-perfInsights/volumetab.png)

### <a name="sql-tab"></a>[SQL] 索引標籤

如果目標 VM 裝載任何 SQL Server 執行個體，您會看到其他的索引標籤，在報表中，名為**SQL**:

![螢幕擷取畫面的 SQL 索引標籤](media/how-to-use-perfInsights/sqltab.png)

此章節包含**發現**索引標籤，然後針對每個 SQL Server 執行個體的 VM 上裝載的其他索引標籤。

**發現** 索引標籤包含一份所有 SQL 相關效能問題，找不到，以及建議。

在下列範例中， **PhysicalDrive0** （執行在 C 磁碟機） 會顯示。 這是因為同時**modeldev**和**modellog**檔案位於 C 磁碟機上，它們都是不同類型 (例如資料檔案和交易記錄檔，分別)。

![記錄資訊的螢幕擷取畫面](media/how-to-use-perfInsights/loginfo.png)

SQL Server 的特定執行個體的索引標籤包含一般的區段會顯示所選執行個體的基本資訊。 索引標籤也包含如進階資訊，包括設定、 組態和使用者選項的其他區段。

### <a name="diagnostic-tab"></a>[診斷] 索引標籤
**診斷** 索引標籤包含最上層的 CPU、 磁碟和記憶體取用者在電腦上執行的持續時間的資訊 PerfInsights。 您也可以找到的重大修補程式的相關資訊，系統可能遺失、 工作清單和重要系統事件。 

## <a name="references-to-the-external-tools-used"></a>所用外部工具的參考

### <a name="diskspd"></a>Diskspd

Diskspd 是儲存體負載產生器和效能測試 Microsoft 工具。 如需詳細資訊，請參閱 [Diskspd](https://github.com/Microsoft/diskspd)。

### <a name="xperf"></a>Xperf

Xperf 是擷取追蹤，從 Windows 效能工具組的命令列工具。 如需詳細資訊，請參閱 [Windows 效能工具組 – Xperf](https://blogs.msdn.microsoft.com/ntdebugging/2008/04/03/windows-performance-toolkit-xperf/)。

## <a name="next-steps"></a>後續步驟

您可以上傳診斷記錄及報表給 Microsoft 支援服務供進一步檢閱。 支援可能會要求您傳送 PerfInsights 用來協助疑難排解程序所產生的輸出。

下列螢幕擷取畫面會顯示類似於您可能會收到一則訊息：

![Microsoft 支援服務的範例訊息的螢幕擷取畫面](media/how-to-use-perfInsights/supportemail.png)

依照訊息來存取檔案傳送工作區中的指示。 為了增加安全性，您必須變更您在第一次使用的密碼。

登入之後，您會發現對話方塊來上傳**CollectedData\_yyyy MM dd\_hh\_公釐\_ss.zip** PerfInsights 收集的檔案。
