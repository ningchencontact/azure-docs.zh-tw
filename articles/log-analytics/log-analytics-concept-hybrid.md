---
title: "收集資料，從您的環境與 Azure Log Analytics |Microsoft 文件"
description: "本主題可協助您了解如何收集資料和監視您內部部署或其他記錄分析的雲端環境中裝載的電腦。"
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: magoedte
ms.openlocfilehash: 513855084c8b89d97b049f1df2ec24d0f9789afe
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/13/2017
---
# <a name="collect-data-from-computers-in-your-environment-with-log-analytics"></a>從記錄分析環境中的電腦收集資料

Azure 記錄分析可以收集並處理的資料從位於 Windows 或 Linux 電腦：

* [Azure 虛擬機器](log-analytics-quick-collect-azurevm.md)使用記錄檔分析 VM 延伸模組 
* 您為實體伺服器或虛擬機器的資料中心
* 雲端託管服務中的虛擬機器例如 Amazon Web Services (AWS)

在您的環境中裝載的電腦可以直接連接到記錄分析，或如果您已經監視與 System Center Operations Manager 2012 R2 或 2016年這些電腦，您可以整合作業管理管理群組與記錄分析和繼續維護您的服務作業的程序和策略。  

## <a name="overview"></a>概觀

![log-analytics-agent-direct-connect-diagram](media/log-analytics-concept-hybrid/log-analytics-on-prem-comms.png)

分析和之前作用於收集的資料，您需要安裝並連接所有您想要將資料傳送到記錄分析服務的電腦的代理程式。 使用安裝程式，命令列中，由內部部署電腦上或使用預期狀態設定 (DSC) 在 Azure 自動化中，您可以安裝代理程式。 

適用於 Linux 和 Windows 代理程式透過 TCP 連接埠 443，進行通訊輸出的記錄分析服務，以及如果電腦連線到網際網路的通訊的防火牆或 proxy 伺服器檢閱[與 proxy 伺服器設定使用的代理程式OMS 閘道或](#configuring-the-agent-for-use-with-a-proxy-server-or-oms-gateway)來了解哪些設定變更必須套用。 如果您要監視的 System Center 2016-Operations Manager 或 Operations Manager 2012 R2 的電腦可以是多重主目錄，與記錄分析服務來收集資料並轉寄給服務，仍會受到[Operations Manager](log-analytics-om-agents.md). 記錄分析與整合的 Operations Manager 管理群組所監視的 Linux 電腦不會收到的資料來源和轉寄收集的資料，透過管理群組的組態。 Windows 代理程式可以報告最多四個工作區，雖然 Linux 代理程式只支援報告為單一工作區。  

適用於 Linux 和 Windows 代理程式不只是用來連接到記錄分析，它也支援連接至主機的混合式 Runbook 背景工作角色，並管理解決方案，例如變更追蹤和更新管理 Azure 自動化。  如需混合式 Runbook 背景工作角色的詳細資訊，請參閱[Azure 自動化 Hybrid Runbook Worker](../automation/automation-offering-get-started.md#automation-architecture-overview)。  

如果 IT 安全性原則不允許您網路上的電腦連線到網際網路，則可以將代理程式設定為連線到 OMS 閘道，以根據您已啟用的解決方案來接收組態資訊和傳送收集到的資料。 如需詳細資訊和如何設定 Linux 或 Windows 代理程式會透過閘道 OMS 記錄分析服務進行通訊的步驟，請參閱[電腦連線到 OMS 使用 OMS 閘道](log-analytics-oms-gateway.md)。 

## <a name="prerequisites"></a>必要條件
開始之前，請檢閱下列詳細資料，以確認您符合最低系統需求。

### <a name="windows-operating-system"></a>Windows 作業系統
下列 Windows 作業系統版本正式支援的 Windows 代理程式：

* Windows Server 2008 Service Pack 1 (SP1) 或更新版本
* Windows 7 SP1 和更新版本。

#### <a name="network-configuration"></a>網路組態
下列清單 proxy 和防火牆設定所需的資訊與記錄分析進行 Windows 代理程式資訊。 已從您的網路輸出到記錄分析服務的流量。 

| 代理程式資源 | 連接埠 | 略過 HTTPS 檢查|
|----------------|-------|------------------------|
|*.ods.opinsights.azure.com |443 | 是 |
|*.oms.opinsights.azure.com | 443 | 是 | 
|*.blob.core.windows.net | 443 | 是 | 
|*.azure-automation.net | 443 | 是 | 

### <a name="linux-operating-systems"></a>Linux 作業系統
以下為正式支援的 Linux 散發套件。  不過，其他未列出的發佈也可能執行的 Linux 代理程式。

* Amazon Linux 2012.09 至 2015.09 (x86/x64)
* CentOS Linux 5、6 和 7 (x86/x64)
* Oracle Linux 5、6 和 7 (x86/x64)
* Red Hat Enterprise Linux Server 5、6 和 7 (x86/x64)
* Debian GNU/Linux 6、7 和 8 (x86/x64)
* Ubuntu 12.04 LTS、14.04 LTS、16.04 LTS (x86/x64)
* SUSE Linux Enterprise Server 11 和 12 (x86/x64)

#### <a name="network-configuration"></a>網路組態
下列 Linux 代理程式與記錄分析通訊所需的 proxy 和防火牆設定資訊清單資訊。 已從您的網路輸出到記錄分析服務的流量。 

|代理程式資源| 連接埠 |  
|------|---------|  
|*.ods.opinsights.azure.com | 連接埠 443|   
|*.oms.opinsights.azure.com | 連接埠 443|   
|*.blob.core.windows.net | 連接埠 443|   
|*.azure-automation.net | 連接埠 443|  

Linux 代理程式支援透過 proxy 伺服器或閘道使用 HTTPS 通訊協定的記錄分析服務，OMS 的通訊。  支援匿名和基本驗證 （使用者名稱/密碼）。  可在安裝期間指定或在安裝後透過修改 proxy.conf 組態檔案指定的 Proxy 伺服器。  

Proxy 組態值的語法如下︰

`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
> 如果您的 proxy 伺服器不需要驗證，Linux 代理程式仍然需要提供虛擬使用者密碼。 這可以是任何使用者名稱或密碼。

|屬性| 說明 |
|--------|-------------|
|通訊協定 | https |
|user | 用於驗證 Proxy 的選擇性使用者名稱 |
|password | 用於驗證 Proxy 的選擇性密碼 |
|proxyhost | Proxy 伺服器/OMS 閘道的位址或 FQDN |
|連接埠 | Proxy 伺服器/OMS 閘道的選擇性連接埠號碼 |

例如：`https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> 如果您使用特殊字元，例如"@"在您的密碼，您會收到 proxy 連接錯誤因為值會不正確剖析。  若要解決此問題，編碼的密碼，例如使用工具在 URL 中[URLDecode](https://www.urldecoder.org/)。  

## <a name="install-and-configure-agent"></a>安裝及設定代理程式 
直接與記錄分析連線在內部部署電腦可以透過不同的方法，根據您的需求。 下表說明每個方法來判斷何者最適合您組織中。

|來源 | 方法 | 說明|
|-------|-------------|-------------|
| Windows 電腦|- [手動安裝](log-analytics-agent-windows.md)<br>- [Azure 自動化 DSC](log-analytics-agent-windows.md#install-the-agent-using-dsc-in-azure-automation)<br>- [資源管理員範本與 Azure 堆疊](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) |從命令列或使用自動化的方法，例如 Azure Automation DSC，安裝 Microsoft Monitoring agent [System Center Configuration Manager](https://docs.microsoft.com/sccm/apps/deploy-use/deploy-applications)，或如果您已部署 Microsoft Azure Resource Manager 範本Azure 資料中心中的堆疊。| 
|Linux 電腦| [手動安裝](log-analytics-quick-collect-linux-computer.md)|安裝適用於呼叫包裝函式的指令碼裝載於 GitHub 上的 Linux 代理程式。 | 
| System Center Operations Manager|[整合 Operations Manager 與記錄分析](log-analytics-om-agents.md) | 設定 Operations Manager，並記錄分析將轉送之間的整合來自報表至管理群組的 Linux 和 Windows 電腦收集資料。|  

## <a name="next-steps"></a>後續步驟

* 檢閱[資料來源](log-analytics-data-sources.md)來了解可用來從 Windows 或 Linux 電腦收集資料的資料來源。 

* 了解 [記錄搜尋](log-analytics-log-searches.md) ，其可分析從資料來源和方案所收集的資料。 

* 了解可將功能加入 Log Analytics 並會將資料收集到 OMS 儲存機制的 [方案](log-analytics-add-solutions.md) 。