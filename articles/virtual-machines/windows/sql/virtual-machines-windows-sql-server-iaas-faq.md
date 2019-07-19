---
title: Azure 中 Windows 虛擬機器上的 SQL Server 常見問題集 | Microsoft Docs
description: 本文章提供在 Azure VM 上執行 SQL Server 的常見問題解答。
services: virtual-machines-windows
documentationcenter: ''
author: v-shysun
manager: felixwu
editor: ''
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/12/2018
ms.author: mathoma
ms.openlocfilehash: 7f6ec1ee65727fb8c3c7d98f696c288e95ec880a
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876184"
---
# <a name="frequently-asked-questions-for-sql-server-running-on-windows-virtual-machines-in-azure"></a>在 Azure 中 Windows 虛擬機器上執行的 SQL Server 常見問題集

> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](../../linux/sql/sql-server-linux-faq.md)

本文提供一些最常見關於 [Azure 中 Windows 虛擬機器上的 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/) 執行問題的解答。

> [!NOTE]
> 本文將重點放在 Windows VM 上 SQL Server 的特定問題。 如果您在 Linux VM 上執行 SQL Server，請參閱 [Linux 常見問題集](../../linux/sql/sql-server-linux-faq.md)。

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a id="images"></a> 映像

1. **可用的 SQL Server 虛擬機器資源庫映像有哪些？** 

   Azure 會針對所有 Windows 和 Linux 版本上所有支援的 SQL Server 主要版本維護虛擬機器映像。 如需詳細資訊，請參閱完整的 [Windows VM 映像](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo)與 [Linux VM 映像](../../linux/sql/sql-server-linux-virtual-machines-overview.md#create)清單。

1. **現有的 SQL Server 虛擬機器資源庫映像是否已更新？**

   每隔兩個月，系統會以最新的 Windows 和 Linux 更新來更新虛擬機器資源庫中的 SQL Server 映像。 若為 Windows 映像，這包括 Windows Update 中標示為重要的任何更新，其中包含重要的 SQL Server 安全性更新和 Service Pack。 若為 Linux 映像，這包括最新的系統更新。 適用於 Linux 和 Windows 的 SQL Server 累計更新會以不同的方式處理。 針對 Linux，SQL Server 累計更新也會包含在重新整理中。 但此時，系統不會以 SQL Server 或 Windows Server 累計更新來更新 Windows VM。

1. **是否可以從資源庫中移除 SQL Server 虛擬機器映像？**

   是的。 Azure 只會為每個主要版本維護一個映像。 例如，發行新的 SQL Server Service Pack 後，Azure 會將新的映像新增至該 Service Pack 的資源庫。 前一個 Service Pack 的 SQL Server 映像會立即從 Azure 入口網站中移除。 不過，在接下來三個月仍可用於從 PowerShell 佈建。 三個月之後，便無法再使用前一個 Service Pack 映像。 如果 SQL Server 版本在達到其生命週期結尾時就不提供支援，也適用此移除原則。


1. **是否可以部署 Azure 入口網站中看不到 SQL Server 的舊版映射？**

   是，請使用 PowerShell。 如需使用 PowerShell 部署 SQL Server VM 的詳細資訊，請參閱[如何使用 Azure PowerShell 佈建 SQL Server 虛擬機器](virtual-machines-windows-ps-sql-create.md)。

1. **可以從 SQL Server VM 建立 VHD 映像嗎？**

   可以，但有幾點需要考量。 如果您將此 VHD 部署到 Azure 中的新 VM, 您就不會在入口網站中取得 SQL Server 設定 區段。 您接下來必須透過 PowerShell 管理 SQL Server 組態選項。 此外，將按照映像原先所在的 SQL VM 的費率向您收費。 即使您在部署前先從 VHD 移除 SQL Server 也是如此。 

1. **是否可以設定虛擬機器資源庫中未顯示的組態 (例如 Windows 2008 R2 + SQL Server 2012)？**

   資料分割 針對包含 SQL Server 的虛擬機器資源庫映像，您必須透過 Azure 入口網站或 [PowerShell](virtual-machines-windows-ps-sql-create.md) 選取其中一個提供的映像。 


## <a name="creation"></a>建立

1. **如何使用 SQL Server 建立 Azure 虛擬機器？**

   最簡單的解決方法是建立包含 SQL Server 的虛擬機器。 如需註冊 Azure 並從入口網站建立 SQL VM 的教學課程，請參閱[在 Azure 入口網站中佈建 SQL Server 虛擬機器](virtual-machines-windows-portal-sql-server-provision.md)。 您可以選取使用以秒鐘計費 SQL Server 授權的虛擬機器映像，或者您可以使用映像，讓您自備自己的 SQL Server 授權。 您也可以選擇使用免費授權版本 (Developer 或 Express) 或重複使用內部部署授權，在 VM 上手動安裝 SQL Server。 如果您自備授權，必須具備 [Azure 上透過軟體保證的授權流動性](https://azure.microsoft.com/pricing/license-mobility/)。 如需詳細資訊，請參閱 [SQL Server Azure VM 的定價指導方針](virtual-machines-windows-sql-server-pricing-guidance.md)。

1. **如何將內部部署 SQL Server 資料庫移轉至雲端？**

   首先，請使用 SQL Server 執行個體來建立 Azure 虛擬機器。 然後將您的內部部署資料庫移轉至該執行個體。 如需資料移轉策略，請參閱 [將 SQL Server 資料庫移轉至 Azure VM 中的 SQL Server](virtual-machines-windows-migrate-sql.md)。

## <a name="licensing"></a>授權

1. **如何在 Azure VM 上安裝 SQL Server 授權版本？**

   做法有二種。 您可以佈建其中一個[支援授權的虛擬機器映像](virtual-machines-windows-sql-server-iaas-overview.md#BYOL)，也就是所謂的自備授權 (BYOL)。 另一個選項是將 SQL Server 安裝媒體複製到 Windows Server VM 上，然後在 VM 上安裝 SQL Server。 不過，如果您手動安裝 SQL Server，則不會進行入口網站整合，且不支援 SQL Server IaaS 代理程式擴充功能，因此「自動備份」和「自動修補」等功能在此情況下將無法運作。 基於此原因，建議您使用其中一個 BYOL 資源庫映像。 若要在 Azure VM 上使用 BYOL 或您自己的 SQL Server 媒體，您必須具備 [Azure 上透過軟體保證的授權流動性](https://azure.microsoft.com/pricing/license-mobility/)。 如需詳細資訊，請參閱 [SQL Server Azure VM 的定價指導方針](virtual-machines-windows-sql-server-pricing-guidance.md)。

1. **如果只是用於待命/容錯移轉，需要對 Azure VM 上的 SQL Server 授權付費嗎？**

   如果您有軟體保證並使用如[虛擬機器授權常見問題](https://azure.microsoft.com/pricing/licensing-faq/)中所述的授權行動性, 則您不需要支付授權一個 SQL SERVER 參與 HA 部署的被動次要複本。 否則，您必須支付它的授權費用。

1. **如果是從其中一個隨用隨付資源庫映像建立，可以將 VM 變更為使用自己的 SQL Server 授權嗎？**

   是的。 如果您最初開始使用隨用隨付資源庫映像，則可輕鬆地在兩個授權模型中切換。 不過，如果您最初開始使用 BYOL 映像，則無法將您的授權切換為 PAYG。 如需詳細資訊，請參閱[如何變更 SQL Server VM 的授權模式](virtual-machines-windows-sql-ahb.md)。

   > [!Note]
   > 目前，此功能僅提供給公用雲端客戶。

1. **我應該使用 BYOL 映像或 SQL VM RP 來建立新 SQL VM？**

   自備授權 (BYOL) 映像只針對 EA 客戶提供。 擁有軟體保證的其他客戶應該使用 SQL VM 資源提供者來搭配 [Azure Hybrid Benefit (AHB)](https://azure.microsoft.com/pricing/licensing-faq/) 建立 SQL VM。 

1. **切換授權模型是否必須讓 SQL Server 停機？**

   資料分割 [變更授權模型](virtual-machines-windows-sql-ahb.md)不需要將 SQL Server 停機，因為變更會立即生效，且不需要重新啟動 VM。 不過，若要向 SQL VM 資源提供者註冊您的 SQL Server VM，[SQL IaaS 擴充功能](virtual-machines-windows-sql-server-agent-extension.md)是必要條件，而且安裝 SQL IaaS 擴充功能會重新啟動 SQL Server 服務。 因此，如果需要安裝 SQL IaaS 擴充功能，然後應該在維護期間完成此作業。 

1. **CSP 訂用帳戶是否能啟用 Azure Hybrid Benefit？**

   是，Azure Hybrid Benefit 適用於 CSP 訂用帳戶。 CSP 客戶應該先部署隨用隨付映像，然後將[授權模式變更](virtual-machines-windows-sql-ahb.md)為自備授權。  

1. **使用新的 SQL VM 資源提供者註冊我的 VM 是否會產生額外的成本？**

   資料分割 SQL VM 資源提供者只會在 Azure VM 上啟用額外的 SQL Server 管理，而不會產生任何額外費用。 

1. **SQL VM 資源提供者是否適用於所有客戶？**
 
   是的。 所有客戶都能向新的 SQL VM 資源提供者註冊。 不過，只有具有軟體保證權益的客戶可以在 SQL Server VM 上啟用 [Azure Hybrid Benefit (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) (或 BYOL)。 

1. **如果移動或卸載 VM 資源, _microsoft.sqlvirtualmachine_資源會發生什麼事？** 

   當 Microsoft.Compute/VirtualMachine 資源被捨棄或移動時，系統會通知關聯的 Microsoft.SqlVirtualMachine 資源，讓其以非同步方式複寫作業。

1. **如果卸載_microsoft.sqlvirtualmachine_資源, VM 會發生什麼事？**

    當 Microsoft.SqlVirtualMachine 資源被卸除時，Microsoft.Compute/VirtualMachine 資源不會受影響。 不過，授權變更將會還原為預設的原始映像來源。 

1. **是否可以向 SQL VM 資源提供者註冊自我部署 SQL Server VM？**

    是的。 若您從自己的媒體部署 SQL Server，而且已安裝 SQL IaaS 延伸模組，您可以向資源提供者註冊您自己的 SQL Server VM，以取得 SQL IaaS 延伸模組所提供的管理能力優點。 不過，您無法將自我部署 SQL VM 轉換為隨用隨付。

## <a name="administration"></a>系統管理

1. **是否可以在相同的 VM 上安裝第二個 SQL Server 執行個體？是否可以變更預設執行個體的已安裝功能？**

   是的。 SQL Server 安裝媒體位於 **C** 磁碟機的資料夾中。 您可從該位置執行 **Setup.exe** 來新增新的 SQL Server 執行個體，或變更機器上 SQL Server 的其他已安裝功能。 請注意, 某些功能 (例如自動備份、自動修補和 Azure Key Vault 整合) 只會針對預設實例或已正確設定的已命名實例進行操作 (請參閱問題 3)。 

1. **是否可以將 SQL Server 的預設執行個體解除安裝**

   可以，但有幾點考量。 如先前的答案所述, 有一些功能依賴[SQL Server IaaS 代理程式延伸](virtual-machines-windows-sql-server-agent-extension.md)模組。  如果您在未移除 IaaS 擴充功能的情況下卸載預設實例, 延伸模組會繼續尋找它, 而且可能會產生事件記錄檔錯誤。 這些錯誤來自下列兩個來源：**Microsoft SQL Server 認證管理**和 **Microsoft SQL Server IaaS 代理程式**。 其中一個錯誤應如下所示：

      和 SQL Server 建立連線時，發生與網路相關或執行個體特定的錯誤。 找不到或無法存取伺服器。

   如果您決定要將預設執行個體解除安裝，則也會將 [SQL Server IaaS 代理程式擴充功能](virtual-machines-windows-sql-server-agent-extension.md)解除安裝。

1. **是否可以搭配 IaaS 擴充功能使用 SQL Server 的已命名實例**？
   
   是, 如果命名的實例是 SQL Server 上唯一的實例, 而且原始的預設實例已[正確卸載](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md#installation), 則為是。 如果沒有預設實例, 且單一 SQL Server VM 上有多個已命名的實例, 則 IaaS 擴充功能將無法安裝。 

1. **可以從 SQL VM 完全移除 SQL Server 嗎？**

   可以，不過如 [SQL Server Azure VM 的定價指導方針](virtual-machines-windows-sql-server-pricing-guidance.md)所述，仍會繼續向您收取 SQL VM 的費用。 如果您不再需要 SQL Server，則可以部署新的虛擬機器，並將資料和應用程式移轉到新的虛擬機器。 接著，您可以移除 SQL Server 虛擬機器。
   
## <a name="updating-and-patching"></a>更新和修補

1. **如何? 在 Azure VM 中變更為不同版本的 SQL Server 嗎？**

   客戶可以使用安裝媒體 (其中包含其所需的 SQL Server 版本或版本), 來變更其版本/版本的 SQL Server。 變更版本之後, 請使用 Azure 入口網站來修改 VM 的版本屬性, 以精確反映 VM 的計費。 如需詳細資訊, 請參閱[SQL SERVER VM 的變更版本](virtual-machines-windows-sql-change-edition.md)。 


1. **如何將更新和 Service Pack 套用到 SQL Server VM 上？**

   虛擬機器可讓您控制主機電腦，包括套用更新的時間與方法。 針對作業系統，您可以手動套用 Windows 更新，或是啟用名為 [自動修補](virtual-machines-windows-sql-automated-patching.md)的排程服務。 自動修補會安裝任何標示為重要的更新，包括該類別目錄中的 SQL Server 更新。 其他選擇性的 SQL Server 更新，則必須手動安裝。

## <a name="general"></a>一般

1. **Azure VM 上是否支援 SQL Server 容錯移轉叢集執行個體 (FCI)？**

   是的。 您可以[在 Windows Server 2016 上建立 Windows 容錯移轉叢集](virtual-machines-windows-portal-sql-create-failover-cluster.md)，並使用儲存空間直接存取 (S2D) 來連結叢集儲存體。 或者，您可以使用 [Azure 虛擬機器中的 SQL Server 的高可用性和災害復原](virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions)中所述的協力廠商叢集或儲存體解決方案。

   > [!IMPORTANT]
   > 目前不支援將 [SQL Server IaaS 代理程式擴充功能](virtual-machines-windows-sql-server-agent-extension.md)用於 Azure 上的 SQL Server FCI。 建議您在參與 FCI 的 VM 上解除安裝此擴充功能。 針對 SQL，此擴充功能支援自動備份和修補之類的功能，以及部分入口網站功能。 在代理程式解除安裝後，這些功能即不適用於 SQL VM。

1. **SQL VM 和 SQL Database 服務之間的差異為何？**

   從概念上來說，在 Azure 虛擬機器上執行 SQL Server 與在遠端資料中心中執行 SQL Server 並沒什麼不同。 對比之下， [SQL Database](../../../sql-database/sql-database-technical-overview.md) 可提供資料庫即服務的功能。 使用 SQL Database 時，您無法存取主控資料庫的機器。 如需完整的比較，請參閱[選擇雲端 SQL Server 選項：Azure SQL (PaaS) Database 或 Azure VM (IaaS)](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md) 上的 SQL Server。

1. **如何在 Azure VM 上安裝 SQL 資料工具？**

    請從 [Microsoft SQL Server Data Tools - Business Intelligence for Visual Studio 2013](https://www.microsoft.com/download/details.aspx?id=42313) 下載並安裝 SQL 資料工具。

1. **SQL Server Vm 上是否支援具有 MSDTC 的分散式交易？**
   
    是的。 SQL Server 2016 SP2 和更新版本支援本機 DTC。 不過, 使用 Always On 可用性群組時, 必須測試應用程式, 因為容錯移轉期間進行中的交易將會失敗, 而且必須重試。 從 Windows Server 2019 開始提供叢集 DTC。 

## <a name="resources"></a>資源

**Windows VM**：

* [Windows VM 上的 SQL Server 概觀](virtual-machines-windows-sql-server-iaas-overview.md)。
* [佈建 SQL Server Windows VM](virtual-machines-windows-portal-sql-server-provision.md)
* [將資料庫移轉至 Azure VM 上的 SQL Server](virtual-machines-windows-migrate-sql.md)
* [Azure 虛擬機器中的 SQL Server 高可用性和災害復原](virtual-machines-windows-sql-high-availability-dr.md)
* [Azure 虛擬機器中的 SQL Server 效能最佳作法](virtual-machines-windows-sql-performance.md)
* [Azure 虛擬機器中的 SQL Server 應用程式模式和開發策略](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Linux VM**：

* [Linux VM 上的 SQL Server 概觀](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [佈建 SQL Server Linux VM](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [常見問題集 (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [Linux 上的 SQL Server 文件](https://docs.microsoft.com/sql/linux/sql-server-linux-overview) \(機器翻譯\)
