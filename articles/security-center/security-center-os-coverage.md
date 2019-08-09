---
title: Azure 資訊安全中心所支援的功能和平台 | Microsoft Docs
description: 本文件提供 Azure 資訊安全中心所支援的功能和平台清單。
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 6/03/2019
ms.author: v-mohabe
ms.openlocfilehash: 7cd1d451b49faf2f8e3ad38f4ff780256ef2dc5d
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883592"
---
# <a name="platforms-and-features-supported-by-azure-security-center"></a>Azure 資訊安全中心所支援的平台和功能

針對電腦和使用傳統與 Resource Manager 部署模型建立的虛擬機器 (VM) ，提供安全性狀態監視和建議。

> [!NOTE]
> 深入了解 Azure 資源的[傳統和 Resource Manager 部署模型](../azure-classic-rm.md)。
>

## <a name="platforms-that-support-the-data-collection-agent"></a>可支援資料收集代理程式的平台 

本節列出 Azure 資訊安全中心代理程式可在其上執行並可從中收集資料的平台。

### <a name="supported-platforms-for-windows-computers-and-vms"></a>Windows 電腦和 VM 支援的平台
以下為支援的 Windows 作業系統：

* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2
* Windows Server 2012
* Windows Server 2008 R2
* Windows Server 2008

> [!NOTE]
> 與 Windows Defender ATP 整合僅支援 Windows Server 2012 R2 和 Windows Server 2016。

### <a name="supported-platforms-for-linux-computers-and-vms"></a>Linux 電腦和 VM 支援的平台

以下為支援的 Linux 作業系統：

> [!NOTE]
> 由於支援的 Linux 作業系統清單經常變更, 如果您想要的話, 請按一下[這裡](https://github.com/microsoft/OMS-Agent-for-Linux#supported-linux-operating-systems), 以查看最新的支援版本清單, 以防上次發行此主題之後已經發生變更。

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

## <a name="vms-and-cloud-services"></a>VM 和雲端服務
也支援在雲端服務中執行的 VM。 只監視在生產位置中執行的雲端服務 Web 角色和背景工作角色。 若要深入了解雲端服務，請參閱 [Azure 雲端服務概觀](../cloud-services/cloud-services-choose-me.md)。


## <a name="supported-iaas-features"></a>支援的 IaaS 功能

> [!div class="mx-tableFixed"]
> 

|伺服器|Windows|||Linux|||定價|
|----|----|----|----|----|----|----|----|
|**環境**|**Azure**||**Non-Azure**|**Azure**||**Non-Azure**||
||**虛擬機器**|**虛擬機器擴展集**||**虛擬機器**|**虛擬機器擴展集**|
|VMBA 威脅偵測警示|✔|✔|✔|✔ (在支援的版本上)|✔ (在支援的版本上)|✔|建議 (免費) 威脅偵測 (標準)|
|網路型威脅偵測警示|✔|✔|X|✔|✔|X|標準|
|Windows Defender ATP 整合|✔ (在支援的版本上)|✔ (在支援的版本上)|✔|X|X|X|標準|
|遺漏修補程式|✔|✔|✔|✔|✔|✔|免費|
|安全性設定|✔|✔|✔|✔|✔|✔|免費|
|Endpoint protection 評估|✔|✔|✔|X|X|X|免費|
|JIT VM 存取|✔|X|X|✔|X|X|標準|
|自適性應用程式控制|✔|X|✔|✔|X|✔|標準|
|FIM|✔|✔|✔|✔|✔|✔|標準|
|磁片加密評估|✔|✔|X|✔|✔|X|免費|
|第三方部署|✔|X|X|✔|X|X|免費|
|NSG 評量|✔|✔|X|✔|✔|X|免費|
|無檔案型威脅偵測|✔|✔|✔|X|X|X|標準|
|網路對應|✔|✔|X|✔|✔|X|標準|
|自適性網路控制措施|✔|✔|X|✔|✔|X|標準|
|法規合規性儀表板 & 報告|✔|✔|✔|✔|✔|✔|標準|
|Docker 託管 IaaS 容器上的建議和威脅偵測|X|X|X|✔|✔|✔|標準|

### <a name="supported-endpoint-protection-solutions"></a>支援的端點保護解決方案

下表提供的矩陣指出：
 - 您是否可以使用 Azure 資訊安全中心安裝適用於您的每個解決方案。
 - 資訊安全中心可以探索哪些端點保護解決方案。 如果探索到這些端點保護解決方案的其中一個，資訊安全中心不會建議安裝。

如需每一項保護何時產生建議的相關資訊, 請參閱[Endpoint Protection 評估和建議](security-center-endpoint-protection.md)。

| Endpoint Protection| 平台 | 資訊安全中心安裝 | 資訊安全中心探索 |
|------|------|-----|-----|
| Windows Defender (Microsoft 反惡意程式碼軟體)| Windows Server 2016| 否，內建於 OS| 是 |
| System Center Endpoint Protection (Microsoft 反惡意程式碼軟體) | Windows Server 2012 R2、2012、2008 R2 (請參閱下列附註) | 透過延伸模組 | 是 |
| Trend Micro – 所有版本 | Windows Server 系列  | 否 | 是 |
| Symantec v12.1.1100+| Windows Server 系列  | 否 | 是 |
| McAfee v10+ | Windows Server 系列  | 否 | 是 |
| McAfee v10+ | Linux 伺服器系列  | 否 | 是的 **\*** |
| Sophos V9 +| Linux 伺服器系列  | 否 | 是的 **\***  |

 **\*** 涵蓋範圍狀態和支援的資料目前僅適用于與受保護的訂用帳戶相關聯的 Log Analytics 工作區, 而且不會反映在 Azure 資訊安全中心入口網站中。

> [!NOTE]
> - 在 Windows Server 2008 R2 虛擬機器上偵測 System Center Endpoint Protection (SCEP) 需要安裝 SCEP (PowerShell 3.0 或更高版本)。

## <a name="supported-paas-features"></a>支援的 PaaS 功能


|服務|建議 (免費)|威脅偵測 (標準)|
|----|----|----|
|SQL|✔| ✔|
|PostGreSQL*|✔| ✔|
|MySQL*|✔| ✔|
|Azure Blob 儲存體帳戶|✔| ✔|
|應用程式服務|✔| ✔|
|雲端服務|✔| X|
|VNets|✔| NA|
|子網路|✔| NA|
|NIC|✔| NA|
|NSG|✔| NA|
|訂閱|✔ **| ✔|
|批次|✔| NA|
|Service Fabric|✔| NA|
|自動化帳戶|✔| NA|
|負載平衡器|✔| NA|
|搜尋|✔| NA|
|服務匯流排|✔| NA|
|串流分析|✔| NA|
|事件中樞|✔| NA|
|Logic Apps|✔| NA|
|儲存體帳戶|✔| NA|
|Redis|✔| NA|
|Data Lake 分析|✔| NA|
|金鑰保存庫|✔| NA|




\* 這些功能目前以公開預覽形式支援。

\*\*AAD 建議僅適用于標準訂用帳戶



## <a name="next-steps"></a>後續步驟

- 了解如何[規劃及了解採用 Azure 資訊安全中心的設計考量](security-center-planning-and-operations-guide.md)。
- 深入了解[資訊安全中心中的虛擬機器行為分析和損毀傾印記憶體分析](security-center-alerts-type.md#virtual-machine-behavioral-analysis)。
- 尋找[有關使用 Azure 資訊安全中心的常見問題](security-center-faq.md)。
- 尋找[有關 Azure 安全性與合規性的部落格文章](https://blogs.msdn.com/b/azuresecurity/)。
