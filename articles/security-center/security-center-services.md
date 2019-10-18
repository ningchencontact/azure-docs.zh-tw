---
title: Azure 資訊安全中心提供支援的功能 |Microsoft Docs
description: 本檔提供 Azure 資訊安全中心所支援的服務清單。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 870ebc8d-1fad-435b-9bf9-c477f472ab17
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: memildin
ms.openlocfilehash: 275598aa50c252512348f4a04543e1beaf538626
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2019
ms.locfileid: "72529475"
---
# <a name="supported-features-available-in-azure-security-center"></a>Azure 資訊安全中心提供支援的功能

> [!NOTE]
>某些功能僅適用于標準層。 如果您尚未註冊資訊安全中心的標準層，則可以使用免費試用期間。 如需詳細資訊，請參閱[資訊安全中心定價頁面](https://azure.microsoft.com/pricing/details/security-center/)。

下列各節顯示[支援的平臺](security-center-os-coverage.md)可用的資訊安全中心功能。

* [虛擬機器/伺服器](#vm-server-features)
* [PaaS 服務](#paas-services)


## 虛擬機器/伺服器支援的功能<a name="vm-server-features"></a>

> [!div class="mx-tableFixed"]

|伺服器|Windows|||Linux|||價格|
|----|----|----|----|----|----|----|----|
|**環境**|**Azure**||**非 Azure**|**Azure**||**非 Azure**||
||**虛擬機器**|**虛擬機器擴展集**||**虛擬機器**|**虛擬機器擴展集**|
|[Microsoft Defender ATP 整合](https://docs.microsoft.com/azure/security-center/security-center-wdatp)|✔ (在支援的版本上)|✔ (在支援的版本上)|✔|X|X|X|Standard|
|[虛擬機器行為分析威脅偵測警示](https://docs.microsoft.com/azure/security-center/security-center-alerts-iaas)|✔|✔|✔|✔ (在支援的版本上)|✔ (在支援的版本上)|✔|建議（免費）威脅偵測（標準）|
|[Fileless 威脅偵測警示](https://docs.microsoft.com/azure/security-center/security-center-alerts-iaas#fileless-attack-detection-)|✔|✔|✔|X|X|X|Standard|
|[以網路為基礎的威脅偵測警示](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer#azure-network-layer)|✔|✔|X|✔|✔|X|Standard|
|[即時 VM 存取](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)|✔|X|X|✔|X|X|Standard|
|[檔案完整性監視](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring)|✔|✔|✔|✔|✔|✔|Standard|
|[自動調整應用程式控制項](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)|✔|X|✔|✔|X|✔|Standard|
|[網路地圖](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations#network-map)|✔|✔|X|✔|✔|X|Standard|
|[彈性網路強化](https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening)|✔|X|X|✔|X|X|Standard|
|自適性網路控制措施|✔|✔|X|✔|✔|X|Standard|
|[法規合規性儀表板 & 報告](https://docs.microsoft.com/azure/security-center/security-center-compliance-dashboard)|✔|✔|✔|✔|✔|✔|Standard|
|Docker 託管 IaaS 容器上的建議和威脅偵測|X|X|X|✔|✔|✔|Standard|
|缺少 OS 修補程式評估|✔|✔|✔|✔|✔|✔|免費|
|安全性錯誤的評估|✔|✔|✔|✔|✔|✔|免費|
|[Endpoint protection 評估](https://docs.microsoft.com/azure/security-center/security-center-services#supported-endpoint-protection-solutions-)|✔|✔|✔|X|X|X|免費|
|磁片加密評估|✔|✔|X|✔|✔|X|免費|
|協力廠商的弱點評估|✔|X|X|✔|X|X|免費|
|[網路安全性評量](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)|✔|✔|X|✔|✔|X|免費|

### 支援的端點保護解決方案<a name="endpoint-supported"></a>

下表提供的矩陣指出：

 - 您是否可以使用 Azure 資訊安全中心安裝適用於您的每個解決方案。
 - 資訊安全中心可以探索哪些端點保護解決方案。 如果探索到此清單中的 endpoint protection 解決方案，資訊安全中心不建議安裝一個。

如需每一項保護何時產生建議的相關資訊，請參閱[Endpoint Protection 評估和建議](security-center-endpoint-protection.md)。

| Endpoint Protection| 平台 | 資訊安全中心安裝 | 資訊安全中心探索 |
|------|------|-----|-----|
| Windows Defender (Microsoft 反惡意程式碼軟體)| Windows Server 2016| 否，內建於 OS| 是 |
| System Center Endpoint Protection (Microsoft 反惡意程式碼軟體) | Windows Server 2012 R2、2012、2008 R2 (請參閱下列附註) | 透過延伸模組 | 是 |
| 趨勢微–所有版本 * | Windows Server 系列  | 否 | 是 |
| Symantec v12.1.1100+| Windows Server 系列  | 否 | 是 |
| McAfee v10+ | Windows Server 系列  | 否 | 是 |
| McAfee v10+ | Linux 伺服器系列  | 否 | 是 **\*** |
| Sophos V9 +| Linux 伺服器系列  | 否 | 是 **\***  |

 **\*** 涵蓋範圍狀態和支援的資料目前僅適用于與受保護的訂用帳戶相關聯的 Log Analytics 工作區。 它不會反映在 Azure 資訊安全中心入口網站中。

> [!NOTE]
>
> - 在 Windows Server 2008 R2 虛擬機器上偵測 System Center Endpoint Protection (SCEP) 需要安裝 SCEP (PowerShell 3.0 或更高版本)。
> - 深度安全性代理程式支援偵測趨勢微保護。  不支援 OfficeScan 代理程式。


## PaaS 服務支援的<a name="paas-services"></a>功能

Azure 資訊安全中心支援下列 PaaS 資源：

|服務|建議（免費）|威脅偵測（標準）|
|----|----|----|
|SQL|✔| ✔|
|PostGreSQL*|✔| ✔|
|MySQL*|✔| ✔|
|CosmosDB|X| ✔|
|Blob 儲存體|✔| ✔|
|儲存體帳戶|✔| X|
|App Service|✔| ✔|
|函式|✔| X|
|雲端服務|✔| X|
|VNet|✔| X|
|子網路|✔| X|
|NIC|✔| X|
|NSG|✔| X|
|Subscription|✔ * *| ✔|
|批次帳戶|✔| X|
|Service fabric 帳戶|✔| X|
|自動化帳戶|✔| X|
|負載平衡器|✔| X|
|Search|✔| X|
|服務匯流排命名空間|✔| X|
|串流分析|✔| X|
|事件中樞命名空間|✔| X|
|邏輯應用程式|✔| X|
|Redis|✔| X|
|Data Lake Analytics|✔| X|
|Data Lake Store|✔| X|
|金鑰保存庫|✔| X|

\* 這些功能目前以公開預覽形式支援。

\* \* Azure Active Directory （Azure AD）建議僅適用于標準訂閱。

## <a name="next-steps"></a>後續步驟

- 瞭解[資訊安全中心如何收集資料和 Log Analytics 代理程式](security-center-enable-data-collection.md)。
- 瞭解[資訊安全中心如何管理和保護資料](security-center-data-security.md)。
- 了解如何[規劃及了解採用 Azure 資訊安全中心的設計考量](security-center-planning-and-operations-guide.md)。
- 查看[支援安全中心的平臺](security-center-os-coverage.md)。
- 深入瞭解[Azure 資訊安全中心中的 vm & 伺服器的威脅偵測](security-center-alerts-iaas.md)。
- 尋找[有關使用 Azure 資訊安全中心的常見問題](security-center-faq.md)。
- 尋找[有關 Azure 安全性與合規性的部落格文章](https://blogs.msdn.com/b/azuresecurity/)。
