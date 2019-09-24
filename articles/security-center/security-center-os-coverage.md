---
title: Azure 資訊安全中心支援的平臺 |Microsoft Docs
description: 本檔提供 Azure 資訊安全中心支援的平臺清單。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2019
ms.author: memildin
ms.openlocfilehash: 1d13db922ae84e4032304a8865ba6fcdafa65748
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2019
ms.locfileid: "71201626"
---
# <a name="supported-platforms"></a>支援的平台 

## 虛擬機器/伺服器<a name="vm-server"></a>

資訊安全中心在不同類型的混合式環境中支援虛擬機器/伺服器：

* 僅限 Azure
* Azure 和內部部署
* Azure 和其他雲端
* Azure、其他雲端及內部部署

針對在 Azure 訂用帳戶上啟用的 Azure 環境，Azure 資訊安全中心會自動探索在訂用帳戶內部署的 IaaS 資源。

> [!NOTE]
> 若要接收一組完整的安全性功能，您必須擁有[Log Analytics 代理程式](../azure-monitor/platform/agents-overview.md#log-analytics-agent)（由 Azure 資訊安全中心所使用），並已安裝並正確設定，[以將資料傳送至 Azure 資訊安全中心](security-center-enable-data-collection.md#manual-agent)。


下列各節列出 Azure 資訊安全中心所使用之[Log Analytics 代理程式](../azure-monitor/platform/agents-overview.md#log-analytics-agent)可執行檔支援伺服器作業系統。

### Windows server 作業系統<a name="os-windows"></a>

* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2
* Windows Server 2012
* Windows Server 2008 R2
* Windows Server 2008

> [!NOTE]
> 與 Microsoft Defender ATP 的整合僅支援 Windows Server 2012 R2 和 Windows Server 2016。

若要深入瞭解上述 Windows 作業系統支援的功能，請參閱[虛擬機器/伺服器支援的功能](security-center-services.md##vm-server-features)。

### Linux 作業系統<a name="os-linux"></a>

64 位元

* CentOS 6 和 7
* Amazon Linux 2017.09
* Oracle Linux 6 和 7
* Red Hat Enterprise Linux Server 6 和 7
* Debian GNU/Linux 8 和 9
* Ubuntu Linux 14.04 LTS、16.04 LTS 和 18.04 LTS
* SUSE Linux Enterprise Server 12

32 位元
* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 和 9
* Ubuntu Linux 14.04 LTS 和 16.04 LTS

> [!NOTE]
> 由於支援的 Linux 作業系統清單經常變更, 如果您想要的話, 請按一下[這裡](https://github.com/microsoft/OMS-Agent-for-Linux#supported-linux-operating-systems), 以查看最新的支援版本清單, 以防上次發行此主題之後已經發生變更。

若要深入瞭解 Linux 作業系統支援的功能，請參閱[虛擬機器/伺服器支援的功能](security-center-services.md##vm-server-features)。

### 受管理的虛擬機器服務<a name="virtual-machine"></a>

此外，也會在客戶訂用帳戶中建立虛擬機器作為部分 Azure 受控服務的一部分，例如 Azure Kubernetes （AKS）、Azure Databricks 等等。 Azure 資訊安全中心也會探索這些虛擬機器，而且可以根據上列支援的[Windows/Linux 作業系統](#os-windows)來安裝和設定 Log analytics 代理程式。

### 雲端服務<a name="cloud-services"></a>

也支援在雲端服務中執行的虛擬機器。 只監視在生產位置中執行的雲端服務 Web 角色和背景工作角色。 若要深入了解雲端服務，請參閱 [Azure 雲端服務概觀](../cloud-services/cloud-services-choose-me.md)。

## PaaS 服務<a name="paas-services"></a>

Azure 資訊安全中心支援下列 Azure PaaS 資源：

* SQL
* 于 postgresql
* MySQL
* CosmosDB
* 儲存體帳戶
* App Service
* 函數
* 雲端服務
* VNet
* Subnet
* NIC
* NSG
* Batch 帳戶
* Service fabric 帳戶
* 自動化帳戶
* 負載平衡器
* 搜尋
* 服務匯流排命名空間
* 串流分析
* 事件中樞命名空間
* Logic Apps
* Redis
* Data Lake Analytics
* Data Lake Store
* 金鑰保存庫

若要深入瞭解上述 PaaS 資源清單所支援的功能，請參閱[paas 服務支援的功能](security-center-services.md#paas-services)。

## <a name="next-steps"></a>後續步驟

- 瞭解[資訊安全中心如何收集資料和 Log Analytics 代理程式](security-center-enable-data-collection.md)。
- 瞭解[資訊安全中心如何管理和保護資料](security-center-data-security.md)。
- 了解如何[規劃及了解採用 Azure 資訊安全中心的設計考量](security-center-planning-and-operations-guide.md)。
- 瞭解[適用于不同雲端環境的功能](security-center-services.md)。
- 深入瞭解[Azure 資訊安全中心中的 vm & 伺服器的威脅偵測](security-center-alerts-iaas.md)。
- 尋找[有關使用 Azure 資訊安全中心的常見問題](security-center-faq.md)。
- 尋找[有關 Azure 安全性與合規性的部落格文章](https://blogs.msdn.com/b/azuresecurity/)。
