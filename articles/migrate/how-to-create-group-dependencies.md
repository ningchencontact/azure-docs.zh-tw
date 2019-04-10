---
title: 在 Azure Migrate 中使用群組相依性對應調整評量群組 | Microsoft Docs
description: 說明如何在 Azure Migrate 服務中使用群組相依性對應調整評量。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/05/2018
ms.author: raynew
ms.openlocfilehash: 3ee528cc68a2a5637e85dc1d5ef68203916138e7
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2019
ms.locfileid: "59357185"
---
# <a name="refine-a-group-using-group-dependency-mapping"></a>使用群組相依性對應調整群組

本文描述如何藉由將群組中所有機器的相依性視覺化來調整群組。 當您想要為現有群組調整成員資格時，通常會使用此方法，先交叉檢查群組相依性，然後再進行評量。 使用相依性視覺效果來精簡群組，可協助您有效地規劃 Azure 移轉。 您可以探索所有需要一起移轉的互相依存系統。 它可協助您確保不會遺留任何項目，且當您在移轉至 Azure 時，不會發生意外的中斷。


> [!NOTE]
> 您想要將相依性視覺化的群組不應該包含超過 10 部的機器。 如果群組中有超過 10 部的機器，我們建議您將它分割成較小的群組，以利用相依性視覺效果功能。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prepare-for-dependency-visualization"></a>為相依性視覺效果做準備
Azure Migrate 會利用 Azure 監視器記錄檔，以啟用機器的相依性視覺效果中的服務對應解決方案。

> [!NOTE]
> 在 Azure Government 中無法使用相依性視覺效果功能。

### <a name="associate-a-log-analytics-workspace"></a>與 Log Analytics 工作區建立關聯
若要利用相依性視覺效果，您需要將新的或現有的 Log Analytics 工作區與 Azure Migrate 專案建立關聯。 您只能在建立移轉專案的相同訂用帳戶中，建立或連結工作區。

- 若要將 Log Analytics 工作區連結至專案，請在 [概觀] 中，移至專案的 [基本資訊] 區段，然後按一下 [需要設定]

    ![與 Log Analytics 工作區建立關聯](./media/concepts-dependency-visualization/associate-workspace.png)

- 與工作區建立關聯時，您可以選擇建立新的工作區，或連結現有的工作區：
    - 建立新工作區時，您必須指定工作區的名稱。 然後會在和移轉專案相同之 [Azure 地理區](https://azure.microsoft.com/global-infrastructure/geographies/)的區域中建立工作區。
    - 當您連結現有的工作區時，您能以和移轉專案相同的方式，從相同訂用帳戶中所有的可用工作區中挑選。 請注意，系統只會列出那些在[支援服務對應](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-configure#supported-azure-regions)的區域中建立的工作區。 若要能夠連結工作區，請確定您有該工作區的「讀取者」存取權。

> [!NOTE]
> 您無法變更與移轉專案相關聯的工作區。

### <a name="download-and-install-the-vm-agents"></a>下載並安裝虛擬機器代理程式
若要檢視群組的相依性，您需要下載代理程式，並將它安裝在屬於群組的每個內部部署機器上。 此外，如果您的機器沒有網際網路連線，則需要下載並安裝 [Log Analytics 閘道](../azure-monitor/platform/gateway.md)。

1. 在 [概觀] 中，按一下 [管理] > [群組]，移至必要的群組。
2. 在機器清單中，在 [相依性代理程式] 欄中，按一下 [需要安裝] 以查看有關如何下載並安裝代理程式的指示。
3. 在 [相依性] 頁面上，下載 Microsoft Monitoring Agent (MMA) 和相依性代理程式，並安裝在屬於群組的每部 VM 上。
4. 複製工作區識別碼與金鑰。 在內部部署機器上安裝 MMA 時，需要用到這些識別碼與金鑰。

### <a name="install-the-mma"></a>安裝 MMA

#### <a name="install-the-agent-on-a-windows-machine"></a>在 Windows 電腦上安裝代理程式

在 Windows 電腦上安裝代理程式：

1. 按兩下下載的代理程式。
2. 在 [歡迎] 頁面中按 [下一步]。 在 [授權條款] 頁面上，按一下 [我同意] 以接受授權。
3. 在 [目的地資料夾] 中，保留或修改預設的安裝資料夾 > [下一步]。
4. 在 [代理程式安裝選項] 中，選取 [Azure Log Analytics] > [下一步]。
5. 按一下 [新增] 以新增 Log Analytics 工作區。 貼上您從入口網站複製的工作區識別碼和金鑰。 单击“下一步”。

您可以從命令列或使用 System Center Configuration Manager 等自動化的方法來安裝代理程式。 [了解更多](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent)有關如何使用這些方法來安裝 MMA 代理程式。

#### <a name="install-the-agent-on-a-linux-machine"></a>在 Linux 電腦上安裝代理程式

在 Linux 電腦上安裝代理程式：

1. 使用 scp/sftp 將適當的套件組合 (x86 或 x64) 傳輸到 Linux 電腦。
2. 使用 --安裝引數安裝套件組合。

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

#### <a name="install-the-agent-on-a-machine-monitored-by-system-center-operations-manager"></a>將代理程式安裝在 System Center Operations Manager 監視的電腦上

對於受 Operations Manager 2012 R2 或更新版本監視的電腦，不需要安裝 MMA 代理程式。 服務對應已與 Operations Manager 整合，此整合可利用 Operations Manager MMA 收集必要的相依性資料。 您可以使用[此處](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites)的指導方針來啟用整合。 但請注意，在這些電腦上必須安裝相依性代理程式。

### <a name="install-the-dependency-agent"></a>安裝相依性代理程式
1. 若要在 Windows 電腦上安裝相依性代理程式，請按兩下安裝檔案，並遵循精靈的指示。
2. 若要在 Linux 電腦上安裝相依性代理程式，請使用下列命令以 root 身分安裝：

    ```sh InstallDependencyAgent-Linux64.bin```

深入了解 [Windows](../azure-monitor/insights/service-map-configure.md#supported-windows-operating-systems) 與 [Linux](../azure-monitor/insights/service-map-configure.md#supported-linux-operating-systems) 作業系統的相依性代理程式支援。

[深入了解](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples)如何使用指令碼安裝 Dependency 代理程式。

## <a name="refine-the-group-based-on-dependency-visualization"></a>根據相依性視覺效果調整群組
一旦您已在群組的所有機器上安裝代理程式，您可以將群組的相依性視覺化，並按照下列步驟進行調整。

1. 在 Azure Migrate 專案中的 **管理** 下方，按一下  **群組**，然後選取群組。
2. 在群組頁面中，按一下  **[檢視相依性]**， 即會開啟群組相依性對應。
3. 群組的相依性對應會顯示下列詳細資料：
   - 至/從屬於群組的所有機器之輸入 (用戶端) 和輸出 (伺服器) TCP 連線
       - 未安裝 MMA 和相依性代理程式的相依機器會依連接埠號碼分組
       - 已安裝 MMA 和相依性代理程式的相依機器會以不同的方塊顯示
   - 在機器內執行的處理序，您可以展開每個機器方塊，以檢視處理序
   - 每部機器的完整網域名稱、作業系統、MAC 位址等屬性，您可以按一下每個機器方塊來檢視這些詳細資料

     ![檢視群組相依性](./media/how-to-create-group-dependencies/view-group-dependencies.png)

3. 若要檢視更細微的相依性，請按一下時間範圍以進行修改。 根據預設，範圍是一小時。 您可以修改時間範圍，或指定開始和結束日期，以及持續時間。

   > [!NOTE]
   >    目前，相依性視覺效果 UI 不支援選取超過一小時的時間範圍。 使用 Azure 監視器記錄到[查詢相依性資料](https://docs.microsoft.com/azure/migrate/how-to-create-a-group)較長的持續期間內。

4. 確認相依機器、在每部機器內部執行的處理序，並識別應該新增到群組或從中移除的機器。
5. 使用「Ctrl+按一下」選取對應上的多部機器，以將它們新增到群組或從中移除。
    - 您只能新增已探索到的機器。
    - 新增和移除群組中的機器會讓先前所做的評量失效。
    - 修改群組時，可以選擇性地建立新評量。
5. 按一下 [確定] 以儲存群組。

    ![新增或移除機器](./media/how-to-create-group-dependencies/add-remove.png)

如果您要檢查群組相依性對應中顯示之特定機器的相依性，[請設定機器相依性對應](how-to-create-group-machine-dependencies.md)。

## <a name="query-dependency-data-from-azure-monitor-logs"></a>查詢 Azure 監視器記錄檔的相依性資料

使用 Azure Migrate 專案相關聯的 Log Analytics 工作區中的查詢擷取服務對應相依性資料。 [了解更多](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records)查詢 Azure 監視器中的服務對應資料表的相關記錄。 

若要執行的 Kusto 查詢：

1. 安裝代理程式之後，請移至入口網站，然後按一下 [概觀]。
2. 在 [概觀] 中，移至專案的 [Essentials] 區域，然後按一下 [OMS 工作區] 旁提供的工作區名稱。
3. 在 Log Analytics 工作區頁面上，按一下 [一般] > [記錄]。
4. 撰寫查詢，以蒐集使用 Azure 監視器記錄檔的相依性資料。 在下一節中找到查詢範例。
5. 按一下 [執行] 以執行查詢。 

[了解更多](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)關於如何撰寫 Kusto 查詢。 

## <a name="sample-azure-monitor-logs-queries"></a>範例 Azure 監視器的記錄查詢

以下是可用來擷取相依性資料的範例查詢。 您可以修改查詢以擷取您慣用的資料點。 相依性資料記錄中的欄位的完整清單可[此處](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records)。 尋找更多的範例查詢[此處](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches)。

### <a name="summarize-inbound-connections-on-a-set-of-machines"></a>彙總一組機器上的輸入的連線

請注意，對於連線計量，VMConnection，資料表中的記錄並不代表個別的實體網路連線。 多個實體網路連線會分組為邏輯連接。 [了解更多](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#connections)如何實體網路連線的相關資料會彙總成單一的邏輯記錄中 VMConnection。 

```
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z); 
VMConnection
| where Direction == 'inbound' 
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(LinksEstablished) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

#### <a name="summarize-volume-of-data-sent-and-received-on-inbound-connections-between-a-set-of-machines"></a>彙總資料傳送和接收的一組機器之間的傳入連接的磁碟的區

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z); 
VMConnection
| where Direction == 'inbound' 
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(BytesSent), sum(BytesReceived) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

## <a name="next-steps"></a>後續步驟
- [深入了解](https://docs.microsoft.com/azure/migrate/resources-faq#dependency-visualization)相依性視覺效果的常見問題集。
- [深入了解](concepts-assessment-calculation.md)評定的計算方式。
