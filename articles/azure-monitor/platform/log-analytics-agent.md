---
title: 使用 Azure Log Analytics 代理程式收集記錄資料 | Microsoft Docs
description: 此主題可協助您了解如何使用 Log Analytics 來收集資料，並監視 Azure、內部部署或其他雲端環境中裝載的電腦。
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: magoedte
ms.openlocfilehash: b410dab40d5434a6f23950a9f151e50240ace63b
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2019
ms.locfileid: "64916369"
---
# <a name="collect-log-data-with-the-azure-log-analytics-agent"></a>使用 Azure Log Analytics 代理程式收集記錄資料

Azure Log Analytics 代理程式先前稱為 Microsoft Monitoring Agent (MMA) 或 OMS Linux 代理程式，其開發目的，是為了要全方位管理內部部署機器、[System Center Operations Manager](https://docs.microsoft.com/system-center/scom/) 所監視的電腦，和任何雲端中的虛擬機器。 Windows 和 Linux 代理程式連接至 Azure 監視器，並將來自不同來源收集的記錄資料儲存在您的 Log Analytics 工作區，以及任何唯一的記錄檔或監視解決方案中所定義的計量。 

本文將詳細說明代理程式的概觀、系統和網路需求以及不同的部署方法。   

## <a name="overview"></a>概觀

![Log Analytics 代理程式通訊圖表](./media/log-analytics-agent/log-analytics-agent-01.png)

分析及處理收集的資料之前，您必須安裝並連接所有您想要將資料傳送至 Azure 監視器服務機器的代理程式。 您可以使用安裝程式、命令列或 Azure 自動化中的期望狀態設定 (DSC)，在使用 Windows 和 Linux 的 Azure Log Analytics VM 擴充功能的 Azure VM 上安裝代理程式，以及為混合式環境中的機器安裝。 

適用於 Linux 和 Windows 代理程式 TCP 連接埠 443，透過 Azure 監視器服務來對外通訊，如果機器透過防火牆或 proxy 伺服器進行通訊，透過網際網路連線檢閱下方的需求，以了解網路組態必要的。 如果您的 IT 安全性原則不允許網路連線到網際網路上的電腦，您可以設定[Log Analytics 閘道](gateway.md)然後設定要透過 Azure 監視器記錄閘道連線的代理程式。 代理程式接著可以接收組態資訊和傳送根據哪些資料收集的資料集合規則和監視解決方案，您已啟用您的工作區中。 

如果您要監視的 System Center Operations Manager 2012 R2 或更新版本的電腦，它可以是多重主目錄與 Azure 監視器服務，以收集資料並轉寄至該服務，且仍然受到[Operations Manager](../../azure-monitor/platform/om-agents.md)。 Linux 電腦，如 Windows 代理程式，並會收集資訊，並代表管理伺服器所處理的代理程式時，不包含健全狀況服務元件。 Linux 電腦會以不同的方式監視，與 Operations Manager，因為它們，請勿接收組態或直接收集資料和向前逐步執行管理群組，類似 Windows 代理程式管理系統。 如此一來，不支援此案例，向 Operations Manager 報告的 Linux 電腦。  

Windows 代理程式可以回報到最多四個 Log Analytics 工作區，而 Linux 代理程式只支援回報到單一工作區。  

適用於 Linux 和 Windows 的代理程式也不只是用來連接到 Azure 監視器，它也支援這類裝載混合式 Runbook 背景工作角色和其他服務的 Azure 自動化[變更追蹤](../../automation/change-tracking.md)和[的更新管理](../../automation/automation-update-management.md). 如需有關「混合式 Runbook」背景工作角色的詳細資訊，請參閱 [Azure 自動化混合式 Runbook 背景工作](../../automation/automation-hybrid-runbook-worker.md)。  

## <a name="supported-windows-operating-systems"></a>支援的 Windows 作業系統
Windows 代理程式正式支援下列 Windows 作業系統版本：

* Windows Server 2019
* Windows Server 2008 R2、2012、2012 R2、2016，版本 1709 和 1803
* Windows 7 SP1 及更高版本

## <a name="supported-linux-operating-systems"></a>支援的 Linux 作業系統
本節提供有關所支援 Linux 散發套件的詳細資料。    

從 2018 年 8 月之後所發行的版本開始，我們會對支援模型進行下列變更：  

* 只支援伺服器版本，不支援用戶端版本。  
* 一律支援新版的 [Azure Linux 背書散發版本](../../virtual-machines/linux/endorsed-distros.md)。  
* 列出的每個主要版本都支援所有次要版本。
* 不支援製造商結束支援日期已過的版本。  
* 不支援新版 AMI。  
* 只支援預設執行 SSL 1.x 的版本。

如果您使用的散發版本或版本目前不受支援，且不符合支援模型，建議您為此存放庫建立分支，以認可 Microsoft 支援服務將不會對分支代理程式版本提供協助。

* Amazon Linux 2017.09 (x64)
* CentOS Linux 6 (x86/x64) 和 7 (x64)  
* Oracle Linux 6 和 7 (x86/x64) 
* Red Hat Enterprise Linux Server 6 (x86/x64) 和 7 (x64)
* Debian GNU/Linux 8 和 9 (x86/x64)
* Ubuntu 14.04 LTS (x86/x64)、16.04 LTS (x86/x64) 和 18.04 LTS (x64)
* SUSE Linux Enterprise Server 12 (x64)

>[!NOTE]
>OpenSSL 1.1.0 只支援用於 x86_x64 平台 (64 位元)，1.x 之前的 OpenSSL 則不支援用於任何平台。
>

## <a name="tls-12-protocol"></a>TLS 1.2 通訊協定
為了確保資料在傳輸至 Azure 監視器記錄檔的安全性，強烈建議您設定代理程式至少使用傳輸層安全性 (TLS) 1.2。 我們已發現較舊版本的 TLS/安全通訊端層 (SSL) 較易受到攻擊，而且在其目前的運作中仍允許回溯相容性，因此並**不建議使用**這些版本。  如需其它資訊，請檢閱[使用 TLS 1.2 安全地傳送](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12)。 

## <a name="network-firewall-requirements"></a>網路防火牆需求
下列資訊列出 Linux 和 Windows Azure 監視器記錄檔與通訊的代理程式所需的 proxy 和防火牆組態資訊。  

|代理程式資源|連接埠 |Direction |略過 HTTPS 檢查|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |連接埠 443 |輸出|是 |  
|*.oms.opinsights.azure.com |連接埠 443 |輸出|是 |  
|*.blob.core.windows.net |連接埠 443 |輸出|是 |  
|*.azure-automation.net |連接埠 443 |輸出|是 |  

Azure Government 所需的防火牆資訊，請參閱[Azure Government 管理](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs)。 

如果您打算使用 Azure 自動化混合式 Runbook 背景工作角色連線到自動化服務並向其註冊，以便在您的環境中使用 Runbook，它必須具有[設定適用於混合式 Runbook 背景工作角色的網路](../../automation/automation-hybrid-runbook-worker.md#network-planning)中所述的連接埠號碼和 URL 存取權。 

Windows 和 Linux 代理程式支援透過 proxy 伺服器或 Azure 監視器使用 HTTPS 通訊協定的 Log Analytics 閘道進行通訊。  不支援匿名和基本驗證 (使用者名稱/密碼)。  若是直接連線到服務的 Windows 代理程式，請在安裝期間或[部署後](agent-manage.md#update-proxy-settings)從控制台或使用 PowerShell 來指定 Proxy 設定。  

若是 Linux 代理程式，請在安裝期間或[安裝後](agent-manage.md#update-proxy-settings)透過修改 proxy.conf 設定檔來指定 Proxy 伺服器。  Linux 代理程式 Proxy 設定值的語法如下：

`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
> 若您的 Proxy 伺服器不要求您進行驗證，Linux 代理程式仍會要求提供虛擬使用者/密碼。 這可以是任何使用者名稱或密碼。

|屬性| 描述 |
|--------|-------------|
|Protocol | https |
|user | 用於驗證 Proxy 的選擇性使用者名稱 |
|password | 用於驗證 Proxy 的選擇性密碼 |
|proxyhost | Proxy 伺服器/Log Analytics 閘道的位址或 FQDN |
|連接埠 | Proxy 伺服器/Log Analytics 閘道的選擇性連接埠號碼 |

例如：`https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> 若您在您的密碼中使用特殊字元 (例如 "\@")，您會收到 Proxy 連線錯誤訊息，因為系統無法正確剖析該值。  若要暫時解決此問題，請使用 [URLDecode](https://www.urldecoder.org/) 之類的工具將 URL 中的密碼編碼。  

## <a name="install-and-configure-agent"></a>安裝及設定代理程式 
在您的 Azure 訂用帳戶或混合式環境中的機器連接直接與 Azure 監視器記錄檔即可使用不同的方法，根據您的需求。 下表說明每個方法，您可以判斷哪個方法最適合您的組織。

|來源 | 方法 | 描述|
|-------|-------------|-------------|
|Azure VM| - 適用於 [Windows](../../virtual-machines/extensions/oms-windows.md) 或 [Linux](../../virtual-machines/extensions/oms-linux.md) 的 Log Analytics VM 擴充功能，使用 Azure CLI 或 Azure Resource Manager 範本<br>- [從 Azure 入口網站手動執行](../../azure-monitor/learn/quick-collect-azurevm.md?toc=/azure/azure-monitor/toc.json)。 | 擴充功能會在 Azure 虛擬機器上安裝 Log Analytics 代理程式，並且在現有的 Azure 監視器工作區中註冊這些機器。|
| 混合式 Windows 電腦|- [手動安裝](agent-windows.md)<br>- [Azure 動化 DSC](agent-windows.md#install-the-agent-using-dsc-in-azure-automation)<br>- [搭配 Azure Stack 的資源管理員範本](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) |從命令列或使用自動化方法 (例如 Azure 自動化 DSC、[System Center Configuration Manager](https://docs.microsoft.com/sccm/apps/deploy-use/deploy-applications)) 來安裝 Microsoft Monitoring 代理程式，或者如果您已經在您的資料中心部署 Microsoft Azure Stack，請使用 Azure Resource Manager 範本。| 
| 混合式 Linux 電腦| [手動安裝](../../azure-monitor/learn/quick-collect-linux-computer.md)|安裝適用於 Linux 的代理程式需要 GitHub 上裝載的包裝函式指令碼。 | 
| System Center Operations Manager|[將 Operations Manager 與 Log Analytics 整合](../../azure-monitor/platform/om-agents.md) | 設定 Operations Manager 和 Azure 監視器記錄之間的整合將從向管理群組回報的 Windows 電腦收集資料。|  

## <a name="next-steps"></a>後續步驟

* 檢閱[資料來源](../../azure-monitor/platform/agent-data-sources.md)以了解可用於從您的 Windows 或 Linux 系統收集資料的資料來源。 

* 了解[記錄查詢](../../azure-monitor/log-query/log-query-overview.md)，以分析從資料來源和解決方案收集到的資料。 

* 了解可將功能新增至 Azure 監視器，並會將資料收集到 Log Analytics 工作區的[監視解決方案](../../azure-monitor/insights/solutions.md)。
