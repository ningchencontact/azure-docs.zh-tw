---
title: Azure Automation State Configuration 概觀
description: Azure Automation State Configuration (DSC)、其條款和已知問題的概觀
keywords: powershell dsc, 需要的狀態組態, powershell dsc azure
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 926629660c9593c59362bd1bc49c5115ac5e3187
ms.sourcegitcommit: 4891f404c1816ebd247467a12d7789b9a38cee7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2019
ms.locfileid: "65441062"
---
# <a name="azure-automation-state-configuration-overview"></a>Azure Automation State Configuration 概觀

Azure Automation State Configuration 是一種 Azure 服務，可讓您撰寫、管理和編譯 PowerShell Desired State Configuration (DSC) [組態](/powershell/dsc/configurations)、匯入 [DSC 資源](/powershell/dsc/resources)，以及將組態指派給目標節點，而這些作業都是在雲端中進行。

## <a name="why-use-azure-automation-state-configuration"></a>為何使用 Azure Automation State Configuration

Azure Automation State Configuration 提供在 Azure 外部使用 DSC 的數個優點。

### <a name="built-in-pull-server"></a>內建提取伺服器

Azure Automation State Configuration 提供類似於 [Windows 功能 DSC-Service](/powershell/dsc/pullserver) 的 DSC 提取伺服器，因此目標節點會自動接收組態、符合期望狀態，並回報其相容性。 Azure 自動化中的內建提取伺服器可讓您不需要設定和維護自己的提取伺服器。 Azure 自動化會以位於雲端或內部部署的虛擬或實體 Windows 或 Linux 機器為目標。

### <a name="management-of-all-your-dsc-artifacts"></a>所有 DSC 構件的管理

Azure Automation State Configuration 為 [PowerShell Desired State Configuration](/powershell/dsc/overview) 帶來與 Azure 自動化提供的 PowerShell 指令碼相同的管理層。

從 Azure 入口網站或 PowerShell 中，您可以管理所有 DSC 設定、資源和目標節點。

![Azure 自動化頁面的螢幕擷取畫面](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-azure-monitor-logs"></a>将报表数据导入到 Azure Monitor 日志中

使用 Azure Automation State Configuration 所管理的節點會將詳細報告狀態資料傳送至內建提取伺服器。 您可以設定 Azure Automation State Configuration，將此資料傳送至 Log Analytics 工作區。 若要了解如何将 State Configuration 状态数据发送到 Log Analytics 工作区，请参阅[将 Azure Automation State Configuration 报表数据转发到 Azure Monitor 日志](automation-dsc-diagnostics.md)。

## <a name="prerequisites"></a>必要條件

在使用 Azure Automation State Configuration (DSC) 时，请考虑以下要求。

### <a name="operating-system-requirements"></a>操作系统要求

运行 Windows 的节点支持以下版本：

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012R2
- Windows Server 2012
- Windows Server 2008 R2 SP1
- Windows 10
- Windows 8.1
- Windows 7

运行 Linux 的节点支持以下发行版/版本：

DSC Linux 扩展支持所有[在 Azure 上认可的](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) Linux 发行版，除了以下这些：

配送映像 | 版本
-|-
Debian  | 所有版本
Ubuntu  | 18.04

### <a name="dsc-requirements"></a>DSC 要求

对于在 Azure 中运行的所有 Windows 节点，[WMF 5.1](https://docs.microsoft.com/powershell/wmf/5.1/install-configure) 将在载入时安装。  对于运行 Windows Server 2012 和 Windows 7 的节点，[将会启用 WinRM](https://docs.microsoft.com/powershell/dsc/troubleshooting/troubleshooting#winrm-dependency)。

对于在 Azure 中运行的所有 Linux 节点，[PowerShell DSC for Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) 将在载入时安装。

### <a name="network-planning"></a>設定私人網路

如果您的節點位於私人網路內，下列連接埠和 Url 是必要的狀態組態 (DSC) 與自動化進行通訊：

* 連接埠:只需要 TCP 443 以便進行傳出網際網路存取。
* 全域 URL：*.azure-automation.net
* US Gov 維吉尼亞州的全域 URL： *.azure automation.us
* 代理程式服務： https://\<workspaceId\>.agentsvc.azure-automation.net

#### <a name="proxy-support"></a>Proxy 支援

DSC 代理程式 proxy 支援適用於 Windows 版 1809年和更新版本。
若要設定此選項，將設定的值**ProxyURL**並**ProxyCredential**中[中繼設定指令碼](automation-dsc-onboarding.md#generating-dsc-metaconfigurations)用來註冊節點。
無法針對先前版本的 Windows 在 DSC 中使用 proxy。

對於 Linux 節點，DSC 代理程式支援 proxy，並利用 http_proxy 變數，以判斷 url。

#### <a name="azure-state-configuration-network-ranges-and-namespace"></a>Azure 狀態設定網路範圍和命名空間

建議使用定義例外狀況時所列出的位址。 針對 IP 位址，您可以下載 [Microsoft Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。 此檔案會每週更新，並具有目前已部署的範圍及任何即將進行的 IP 範圍變更。

如果您有針對特定區域定義的自動化帳戶，您可以將通訊限制為該區域資料中心。 下表提供每個區域的 DNS 記錄：

| **區域** | **DNS 記錄** |
| --- | --- |
| 美國中西部 | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| 美國中南部 |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| 美國東部 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| 加拿大中部 |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| 西歐 |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| 北歐 |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| 東南亞 |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| 印度中部 |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| 日本東部 |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| 澳大利亞東南部 |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| 英國南部 | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| 美國政府維吉尼亞州 | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

如需區域 IP 位址 (而非名稱) 的清單，請從「Microsoft 下載中心」下載 [Azure 資料中心 IP 位址](https://www.microsoft.com/download/details.aspx?id=41653) XML 檔案。

> [!NOTE]
> Azure 資料中心 IP 位址 XML 檔會列出在 Microsoft Azure 資料中心使用的 IP 位址範圍。 此檔案包含計算、SQL 和儲存體範圍。
>
>每週會公佈已更新的檔案。 檔案會反映目前已部署的範圍及任何即將進行的 IP 範圍變更。 出現在檔案中的新範圍至少有一週的時間不會在資料中心中使用。
>
> 最好是每週下載新的 XML 檔案。 接著，更新您的網站以便正確地識別在 Azure 中執行的服務。 Azure ExpressRoute 使用者應該注意到，在每個月的第一週，此檔案會用來更新 Azure 空間的邊界閘道協定 (BGP) 公告。

## <a name="introduction-video"></a>簡介影片

寧可觀賞也不要閱讀？ 看看下列 2015 年 5 月 Azure Automation State Configuration 推出當時的影片。

> [!NOTE]
> 雖然這段影片中所討論的概念和週期都是正確的，但是自從錄製這段影片之後，Azure Automation State Configuration 已有長足進步。 它現在已正式發行，在 Azure 入口網站中具有更豐富的 UI，並支援額外的功能。

> [!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3467/player]

## <a name="next-steps"></a>後續步驟

- 若要開始使用，請參閱[開始使用 Azure 自動化狀態設定](automation-dsc-getting-started.md)
- 若要深入了解如何將節點上架，請參閱[將機器上架交由 Azure Automation State Configuration 管理](automation-dsc-onboarding.md)
- 若要了解如何編譯 DSC 組態，以將它們指派給目標節點，請參閱[編譯 Azure Automation State Configuration 中的組態](automation-dsc-compile.md)
- 如需 PowerShell Cmdlet 參考，請參閱 [Azure 自動化狀態設定 Cmdlet](/powershell/module/azurerm.automation/#automation)
- 如需定價資訊，請參閱 [Azure 自動化狀態設定的定價](https://azure.microsoft.com/pricing/details/automation/)
- 若要查看在持續部署管線中使用 Azure 自動化狀態設定的範例，請參閱[使用 Azure 自動化狀態設定和 Chocolatey 的持續部署](automation-dsc-cd-chocolatey.md)
