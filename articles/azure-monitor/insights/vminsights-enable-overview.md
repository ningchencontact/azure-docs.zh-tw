---
title: 啟用 Azure 監視器的 Vm （預覽） 概觀 |Microsoft Docs
description: 了解如何部署和設定 Vm 的 Azure 監視器。 了解系統需求。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/22/2019
ms.author: magoedte
ms.openlocfilehash: 3f93318dedb8a4667d32bcc97eb6a697ccebfcc4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67122584"
---
# <a name="enable-azure-monitor-for-vms-preview-overview"></a>啟用 Azure 監視器的 Vm （預覽） 概觀

這篇文章概述可用來設定 Vm 的 Azure 監視器的選項。 用於 Vm 的 Azure 監視器來監視健康情況和效能。 探索 Azure 虛擬機器 (Vm) 執行的應用程式相依性和虛擬機器擴展集，在內部部署 Vm 或其他雲端環境中裝載的 Vm。  

若要設定 Azure 監視器的 Vm:

* 啟用單一 Azure VM 或虛擬機器擴展集藉**Insights （預覽）** 直接從 VM 或虛擬機器擴展集。
* 啟用兩個或多個 Azure Vm 和虛擬機器擴展集使用 Azure 原則。 此方法可確保在現有和新的 Vm 和擴展集，必要的相依性，會安裝並在正確設定。 不相容的 Vm 和擴展集報告，因此您可以決定是否要啟用它們，並予以修復。
* 使用 PowerShell，跨指定的訂用帳戶或資源群組啟用兩個或多個 Azure VM 或虛擬機器擴展集。
* 啟用 Azure 監視器來監視 Vm 或實體電腦，在您的公司網路或其他雲端環境中裝載的 vm。

## <a name="prerequisites"></a>必要條件

開始之前，請確定您了解下列各節中的資訊。

### <a name="log-analytics"></a>Log Analytics

適用於 Vm 的 azure 監視器支援 Log Analytics 工作區，在下列區域：

- 美國中西部
- 美國西部 2<sup>1</sup>
- 美國東部
- 加拿大中部<sup>1</sup>
- 英國南部<sup>1</sup>
- 西歐
- 東南亞<sup>1</sup>

<sup>1</sup> 此區域目前不支援適用於 VM 的 Azure 監視器的健康情況功能。

>[!NOTE]
>您可以部署的任何區域的 Azure Vm。 這些 Vm 不限於 Log Analytics 工作區所支援的區域。
>

如果您沒有工作區，您可以建立一個使用其中一個這些資源：
* [Azure CLI](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md)
* [Azure 入口網站](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)

您也可以建立工作區，而您想要啟用單一 Azure VM 或虛擬機器擴展集在 Azure 入口網站中的監視。

若要設定使用 Azure 原則、 Azure PowerShell 或 Azure Resource Manager 範本，在您的 Log Analytics 工作區中的大規模案例：

* 安裝 ServiceMap 和 InfrastructureInsights 解決方案。 您可以使用提供的 Azure Resource Manager 範本來完成這項安裝。 或在**開始**索引標籤上，選取**設定的工作區**。
* 設定 Log Analytics 工作區，以收集效能計數器。

若要設定您的工作區，規模案例，請使用下列方法之一：

* 使用[Azure PowerShell](vminsights-enable-at-scale-powershell.md#set-up-a-log-analytics-workspace)。
* 在 Vm 的 Azure 監視器[**原則涵蓋範圍**](vminsights-enable-at-scale-policy.md#manage-policy-coverage-feature-overview)頁面上，選取**設定工作區**。 

### <a name="supported-operating-systems"></a>受支援的作業系統

下表列出 Windows 和 Linux 作業系統的 Vm 的 Azure 監視器支援。 稍後在本節中，您會發現完整的清單，以詳細說明主要和次要的 Linux OS 版本和支援的核心版本。

|作業系統版本 |效能 |地圖 |健康情況 |
|-----------|------------|-----|-------|
|Windows Server 2019 | X | X | X |
|Windows Server 2016 1803 | X | X | X |
|Windows Server 2016 | X | X | X |
|Windows Server 2012 R2 | X | X | X |
|Windows Server 2012 | X | X | |
|Windows Server 2008 R2 | X | X| |
|Red Hat Enterprise Linux (RHEL) 6 7| X | X| X |
|Ubuntu 14.04、 16.04、 18.04 | X | X | X |
|CentOS Linux 6、 7 | X | X | X |
|SUSE Linux Enterprise Server (SLES) 11、 12 | X | X | X |
|Debian 8、 9.4 | X<sup>1</sup> | | X |

<sup>1</sup> 適用於 VM 的 Azure 監視器的效能功能僅能從 Azure 監視器使用。 不可以直接從 Azure VM 的左窗格。

>[!NOTE]
>在 Linux 作業系統中：
> - 只支援預設版本和 SMP Linux 核心版本。
> - 所有 Linux 散發套件皆不支援非標準的核心版本，例如實體位址擴充 (PAE) 和 Xen。 例如，不支援版本字串為 *2.6.16.21-0.8-xen* 的系統。
> - 不支援自訂核心，包括重新編譯的標準核心。
> - 支援 CentOSPlus 核心。

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| 作業系統版本 | 核心版本 |
|:--|:--|
| 7.4 | 3.10.0-693 |
| 7.5 | 3.10.0-862 |
| 7.6 | 3.10.0-957 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| 作業系統版本 | 核心版本 |
|:--|:--|
| 6.9 | 2.6.32-696 |
| 6.10 | 2.6.32-754 |

#### <a name="centosplus"></a>CentOSPlus
| 作業系統版本 | 核心版本 |
|:--|:--|
| 6.9 | 2.6.32-696.18.7<br>2.6.32-696.30.1 |
| 6.10 | 2.6.32-696.30.1<br>2.6.32-754.3.5 |

#### <a name="ubuntu-server"></a>Ubuntu Server

| 作業系統版本 | 核心版本 |
|:--|:--|
| Ubuntu 18.04 | 核心 4.15.* |
| Ubuntu 16.04.3 | 核心 4.15.* |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

#### <a name="suse-linux-11-enterprise-server"></a>SUSE Linux 11 Enterprise Server

| 作業系統版本 | 核心版本
|:--|:--|
|11 SP4 | 3.0.* |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| 作業系統版本 | 核心版本
|:--|:--|
|12 SP2 | 4.4.* |
|12 SP3 | 4.4.* |

### <a name="the-microsoft-dependency-agent"></a>Microsoft Dependency Agent

適用於 Vm 的 Azure 監視器中的 [地圖] 功能從 Microsoft 相依性代理程式取得其資料。 Dependency Agent 須憑藉 Log Analytics 代理程式才能連線至 Log Analytics。 因此您的系統必須有 Log Analytics 代理程式安裝並設定相依性代理程式。

無論您啟用虛擬機器 Azure 監視單一的 Azure 虛擬機器，或您使用的大規模部署方法，可用於 Azure VM 的相依性代理程式擴充功能安裝代理程式作為體驗的一部分。

在混合式環境中，您可以手動下載並安裝相依性代理程式。 如果您的 Vm 會裝載於 Azure 外部，使用自動化的部署方法。

下表說明對應功能在混合式環境中支援的連線來源。

| 連線的來源 | 支援 | 描述 |
|:--|:--|:--|
| Windows 代理程式 | 是 | 連同[Log Analytics 代理程式的 Windows](../../azure-monitor/platform/log-analytics-agent.md)，Windows 代理程式需要的相依性代理程式。 如需詳細資訊，請參閱 <<c0> [ 支援的作業系統](#supported-operating-systems)。 |
| Linux 代理程式 | 是 | 連同[適用於 Linux 的 Log Analytics 代理程式](../../azure-monitor/platform/log-analytics-agent.md)，Linux 代理程式需要的相依性代理程式。 如需詳細資訊，請參閱 <<c0> [ 支援的作業系統](#supported-operating-systems)。 |
| System Center Operations Manager 管理群組 | 否 | |

您可以從這些位置下載相依性代理程式：

| 檔案 | 作業系統 | 版本 | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.8.1 | 622C99924385CBF539988D759BCFDC9146BB157E7D577C997CDD2674E27E08DD |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.8.1 | 3037934A5D3FB7911D5840A9744AE9F980F87F620A7F7B407F05E276FE7AE4A8 |

## <a name="role-based-access-control"></a>角色型存取控制

若要啟用的 Vm 存取 Azure 監視器中的功能，您必須擁有*Log Analytics 參與者*角色。 若要檢視效能、 健全狀況，並對應資料，您必須擁有*監視讀取器*Azure 虛擬機器角色。 您必須針對適用於 VM 的 Azure 監視器設定 Log Analytics 工作區。

如需有關如何控制 Log Analytics 工作區存取的詳細資訊，請參閱[管理工作區](../../azure-monitor/platform/manage-access.md)。

## <a name="how-to-enable-azure-monitor-for-vms-preview"></a>如何啟用 Azure 監視器的 Vm （預覽）

啟用 vm 的 Azure 監視器，使用其中一種在本表中所述的方法：

| 部署狀態 | 方法 | 描述 |
|------------------|--------|-------------|
| 單一 Azure VM 或虛擬機器擴展集 | [啟用從 VM](vminsights-enable-single-vm.md) | 您可以選取來啟用單一 Azure VM **Insights （預覽）** 直接從 VM 或虛擬機器擴展集。 |
| 多個 Azure Vm 或虛擬機器擴展集 | [啟用透過 Azure 原則](vminsights-enable-at-scale-policy.md) | 您可以使用 Azure 原則和可用的原則定義，以啟用多個 Azure Vm。 |
| 多個 Azure Vm 或虛擬機器擴展集 | [透過 Azure PowerShell 或 Azure Resource Manager 範本啟用](vminsights-enable-at-scale-powershell.md) | 您可以使用 Azure PowerShell 或 Azure Resource Manager 範本在指定的訂用帳戶或資源群組的檔案，以啟用多個 Azure Vm 或虛擬機器擴展集。 |
| 混合式雲端 | [啟用混合式環境](vminsights-enable-hybrid-cloud.md) | 您可以在您的資料中心或其他雲端環境中部署 Vm 或實體電腦所裝載。 |

## <a name="performance-counters-enabled"></a>已啟用效能計數器 

適用於 Vm 的 azure 監視器會設定要收集的效能計數器，它會使用 Log Analytics 工作區。 下表列出的物件和計數器收集每隔 60 秒。

### <a name="windows-performance-counters"></a>Windows 效能計數器

|物件名稱 |計數器名稱 |
|------------|-------------|
|LogicalDisk |% Free Space |
|LogicalDisk |Avg.Disk sec/Read |
|LogicalDisk |Avg.Disk sec/Transfer |
|LogicalDisk |Avg.Disk sec/Write |
|LogicalDisk |Disk Bytes/sec |
|LogicalDisk |Disk Read Bytes/sec |
|LogicalDisk |Disk Reads/sec |
|LogicalDisk |Disk Transfers/sec |
|LogicalDisk |Disk Write Bytes/sec |
|LogicalDisk |Disk Writes/sec |
|LogicalDisk |Free Megabytes |
|記憶體 |可用的 MB |
|網路介面卡 |Bytes Received/sec |
|網路介面卡 |Bytes Sent/sec |
|處理器 |% Processor Time |

### <a name="linux-performance-counters"></a>Linux 效能計數器

|物件名稱 |計數器名稱 |
|------------|-------------|
|Logical Disk |% Used Space |
|Logical Disk |Disk Read Bytes/sec |
|Logical Disk |Disk Reads/sec |
|Logical Disk |Disk Transfers/sec |
|Logical Disk |Disk Write Bytes/sec |
|Logical Disk |Disk Writes/sec |
|Logical Disk |Free Megabytes |
|Logical Disk |Logical Disk Bytes/sec |
|記憶體 |Available MBytes Memory |
|網路 |Total Bytes Received |
|網路 |Total Bytes Transmitted |
|處理器 |% Processor Time |

## <a name="diagnostic-and-usage-data"></a>診斷和使用量資料

當您使用 Azure 監視器服務時，Microsoft 會自動收集使用量和效能資料。 Microsoft 會使用這項資料來改善品質、 安全性及完整性服務。 

若要提供精確且有效率的疑難排解功能，[地圖] 功能會包含軟體組態的相關資料。 資料會提供作業系統和版本、 IP 位址、 DNS 名稱和工作站名稱等資訊。 Microsoft 不會收集姓名、地址或其他連絡資訊。

如需有關資料收集與使用方式的詳細資訊，請參閱 [Microsoft 線上服務隱私權聲明](https://go.microsoft.com/fwlink/?LinkId=512132)。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

既然您已啟用監視您的 VM，監視資訊可供適用於 Vm 的 Azure 監視器中的分析。

## <a name="next-steps"></a>後續步驟

若要了解如何使用健康情況功能，請參閱[檢視適用於 VM 的 Azure 監視器健康情況](vminsights-health.md)。 若要檢視探索到的應用程式相依性，請參閱[檢視適用於 VM 的 Azure 監視器對應](vminsights-maps.md)。
