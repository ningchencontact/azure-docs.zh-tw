---
title: "Linux Azure 虛擬機器上的 SQL Server 常見問題集 | Microsoft Docs"
description: "本文章提供在 Linux Azure VM 上執行 SQL Server 的常見問題解答。"
services: virtual-machines-linux
documentationcenter: 
author: rothja
manager: jhubbard
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: troubleshooting
ms.workload: iaas-sql-server
ms.date: 12/13/2017
ms.author: jroth
ms.openlocfilehash: 8b556b01aa47aeb3588138dfa61e517c00dc44dc
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/21/2017
---
# <a name="frequently-asked-questions-for-sql-server-on-linux-azure-virtual-machines"></a>Linux Azure 虛擬機器上 SQL Server 的常見問題集

> [!div class="op_single_selector"]
> * [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](sql-server-linux-faq.md)

本文章提供有關執行的最常見的問題的解答[Linux Azure 虛擬機器上的 SQL Server](sql-server-linux-virtual-machines-overview.md)。

> [!NOTE]
> 本文著重在 Linux Vm 上的 SQL Server 的特定問題。 如果您在 Windows VM 上執行 SQL Server，請參閱 [Windows 常見問題集](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)。

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a id="images"></a>映像

1. **哪些 SQL Server 虛擬機器圖庫映像可供使用？**

   Azure 會在所有版本上的 SQL Server 支援所有主要版本的虛擬機器映像維護適用於 Linux 和 Windows。 如需詳細資訊，請參閱的完整清單[Linux VM 映像](sql-server-linux-virtual-machines-overview.md#create)和[Windows VM 映像](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md#payasyougo)。

1. **更新現有的 SQL Server 虛擬機器圖庫映像嗎？**

   每隔兩個月，虛擬機器庫中的 SQL Server 映像會更新最新的 Linux 和 Windows 更新。 針對 Linux 映像，這包括最新的系統更新。 針對 Windows 映像，這包括標示為重要 Windows 更新，包括重要的 SQL Server 安全性更新和 service pack 中的任何更新。 SQL Server 累計更新適用於 Linux 和 Windows 的處理方式不同。 適用於 Linux，SQL Server 累計更新也會包含在重新整理。 但在這個階段中，Windows Vm 不會更新 SQL Server 或 Windows Server 的累計更新。

1. **還安裝了哪些相關的 SQL Server 套件？**

   若要查看預設安裝在 SQL Server Linux VM 上的 SQL Server 套件，請參閱[已安裝的套件](sql-server-linux-virtual-machines-overview.md#packages)。

1. **可以從資源庫移除 SQL Server 虛擬機器映像嗎？**

   可以。 Azure 只會維護每個主要版本和版的一個映像。 比方說，新的 SQL Server service pack 發行時，Azure 會將新的映像加入至該服務的組件庫。 Azure 入口網站會立即移除前一個 service pack 的 SQL Server 映像。 不過，它是仍能從 PowerShell 佈建在下一步的三個月。 三個月之後，已無法再使用先前的服務組件映像。 如果 SQL Server 版本不支援它達到其生命週期結束時，也會適用於此移除原則。

## <a name="creation"></a>建立

1. **如何建立具有 SQL Server 的 Linux Azure 虛擬機器？**

   最簡單的解決方法是建立包含 SQL Server 的 Linux 虛擬機器。 如需註冊 Azure 並從入口網站建立 Linux SQL VM 的教學課程，請參閱[在 Azure 入口網站中佈建 Linux SQL Server 虛擬機器](provision-sql-server-linux-virtual-machine.md)。 您也可以選擇使用免費授權版本 (Developer 或 Express) 或重複使用內部部署授權，在 VM 上手動安裝 SQL Server。 如果您自備授權，必須具備 [Azure 上透過軟體保證的授權流動性](https://azure.microsoft.com/pricing/license-mobility)。

1. **我為什麼不能使用有消費限制的 Azure 訂用帳戶來佈建 RHEL 或 SLES SQL Server VM？**

   RHEL 與 SLES 虛擬機器需要沒有消費限制的訂用帳戶，以及與該訂用帳戶相關聯的已驗證付款方式 (通常為信用卡)。 如果您佈建 RHEL 或 SLES VM，但未移除消費限制，您的訂用帳戶將會被停用，而且所有 VM/服務都會停止。 如果您進入此狀態，若要重新啟用該訂用帳戶，請[移除消費限制](https://account.windowsazure.com/subscriptions)。 將會還原您目前計費週期剩餘的信用額度，但如果您選擇重新開始並繼續執行 RHEL 或 SLES VM 映像，則會向您的信用卡收取其額外費用。

## <a name="licensing"></a>授權

1. **如何在 Azure VM 上安裝 SQL Server 授權版本？**

   首先，建立只有 Linux OS 的虛擬機器。 然後針對您的 Linux 發行版本執行 [SQL Server 安裝步驟](https://docs.microsoft.com/sql/linux/sql-server-linux-setup#platforms)。 除非您安裝其中一個免費授權的 SQL Server 版本，否則您也必須要有 SQL Server 授權與 [Azure 上透過軟體保證的授權流動性](https://azure.microsoft.com/pricing/license-mobility/)。

1. **是否有適用於 SQL Server 的自備授權 (BYOL) Linux 虛擬機器映像？**

   目前，沒有適用於 SQL Server 的 BYOL Linux 虛擬機器映像。 不過，您可以在僅有 Linux 的 VM 上手動安裝 SQL Server，如先前的問題中所述。

1. **如果是從其中一個隨收隨付資源庫映像建立，可以將 VM 變更為使用自己的 SQL Server 授權嗎？**

   編號 您不能從以分鐘計費授權切換為使用您自己的授權。 您必須建立新的 Linux VM，安裝 SQL Server，然後移轉資料。 如需自備授權的詳細資訊，請參閱上一個問題。

## <a name="administration"></a>系統管理

1. **可以管理 Linux SQL Server 虛擬機器與 SQL Server Management Studio (SSMS) 嗎？**

   是，但 SSMS 目前僅限 Windows 的工具。 您必須從 Windows 電腦使用 Linux 的 SQL Server Vm 的 SSMS 從遠端連接。 Linux 上的本機新[mssql conf](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf)工具可以執行許多管理工作。 若要預覽的跨平台的資料庫管理工具，請參閱[SQL Server 作業 Studio （預覽）](https://docs.microsoft.com/sql/sql-operations-studio/what-is)。

## <a name="updating-and-patching"></a>更新和修補程式

1. **如何將 Azure VM 中的 SQL Server 升級至新版本？**

   目前，在 Azure VM 中執行的 SQL Server 不提供任何就地升級。 因此，請建立具有所需 SQL Server 版本的 Azure 虛擬機器，然後使用[標準資料移轉技術](https://docs.microsoft.com/sql/linux/sql-server-linux-migrate-overview)，將資料庫移轉到新的伺服器。

## <a name="general"></a>一般

1. **Azure VM 上是否支援 SQL Server 高可用性解決方案？**

   目前沒有。 在 Linux 中，Always On 可用性群組和容錯移轉叢集都需要叢集解決方案，例如 Pacemaker。 SQL Server 支援的 Linux 發行版本不支援其在雲端的高可用性附加元件。

## <a name="resources"></a>資源

**Linux VM**：

* [Linux VM 上的 SQL Server 概觀](sql-server-linux-virtual-machines-overview.md)
* [佈建 SQL Server Linux VM](provision-sql-server-linux-virtual-machine.md)
* [Linux 上的 SQL Server 文件](https://docs.microsoft.com/sql/linux/sql-server-linux-overview) \(機器翻譯\)

**Windows VM**：

* [Windows VM 上的 SQL Server 概觀](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)
* [佈建 SQL Server Windows VM](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md)
* [常見問題集 (Windows)](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)