---
title: Azure 資訊安全中心所支援的功能和平台 | Microsoft Docs
description: 本文件提供 Azure 資訊安全中心所支援的功能和平台清單。
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2018
ms.author: rkarlin
ms.openlocfilehash: 279818e6b43e53206deb9e33591f75ef381a8962
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2018
ms.locfileid: "49319977"
---
# <a name="platforms-and-features-supported-by-azure-security-center"></a>Azure 資訊安全中心所支援的平台和功能

針對使用傳統與 Resource Manager 部署模型建立的虛擬機器 (VM) 與電腦，提供安全性狀態監視和建議。

> [!NOTE]
> 深入了解 Azure 資源的[傳統和 Resource Manager 部署模型](../azure-classic-rm.md)。
>
>

## <a name="supported-platforms"></a>支援的平台 

本節列出 Azure 資訊安全中心代理程式可在其上執行並可從中收集資料的平台。

### <a name="supported-platforms-for-windows-computers-and-vms"></a>Windows 電腦和 VM 支援的平台
支援的 Windows 作業系統：

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016


### <a name="supported-platforms-for-linux-computers-and-vms"></a>Linux 電腦和 VM 支援的平台
支援的 Linux 作業系統：

* Ubuntu 版本 12.04 LTS、14.04 LTS、16.04 LTS
* Debian 版本 6、7、8、9
* CentOS 版本 5、6、7
* Red Hat Enterprise Linux (RHEL) 版本 5、6、7
* SUSE Linux Enterprise Server (SLES) 版本 11、12
* Oracle Linux 版本 5、6、7
* Amazon Linux 2012.09 到 2017
* 只有 x86_64 平台 (64 位元) 支援 Openssl 1.1.0

> [!NOTE]
> 尚未提供 Linux 作業系統的虛擬機器行為分析。
>
>

## <a name="vms-and-cloud-services"></a>VM 和雲端服務
也支援雲端服務中執行的 VM。 只監視生產位置中執行的雲端服務 Web 角色和背景工作角色。 若要深入了解雲端服務，請參閱[雲端服務概觀](../cloud-services/cloud-services-choose-me.md)。


## <a name="supported-iaas-features"></a>支援的 IaaS 功能

> [!div class="mx-tableFixed"]
> 

|伺服器|Windows||Linux||
|----|----|----|----|----|
|環境|Azure|非 Azure|Azure|非 Azure|
|VMBA 威脅偵測警示|✔|✔|✔ (在支援的版本上)|✔|
|網路型威脅偵測警示|✔|X|✔|X|
|Windows Defender ATP 整合*|✔ (在支援的版本上)|✔|X|X|
|遺漏修補程式|✔|✔|✔|✔|
|安全性設定|✔|✔|✔|✔|
|反惡意程式碼|✔|✔|X|X|
|JIT VM 存取|✔|X|✔|X|
|自適性應用程式控制|✔ (僅限 Azure)|X|X|X|
|FIM|✔|✔|✔|✔|
|磁碟加密|✔|X|✔|X|
|第三方部署|✔|X|✔|X|
|NSG|✔|X|✔|X|
|Filess V1|✔|✔|X|X|
|網路地圖|✔|X|✔|X|
|自適性網路強化|✔|X|✔|X|

* 這些功能目前以公開預覽形式支援。


## <a name="supported-paas-features"></a>支援的 PaaS 功能


|服務|建議|威脅偵測|
|----|----|----|
|SQL|✔| ✔|
|PostGreSQL*|✔| ✔|
|MySQL*|✔| ✔|
|Blob 儲存體帳戶*|✔| ✔|
|應用程式服務|✔| ✔|
|雲端服務|✔| X|
|Redis 快取|✔| X|
|Service Fabric|✔| X|
|Azure 自動化|✔| X|
|Data Lake |✔| X|
|金鑰保存庫|✔| X|
|服務匯流排|✔| X|
|串流分析|✔| X|
|Batch|✔| X|
|邏輯應用程式|✔| X|
|Vnet|✔| NA|
|子網路|✔| NA|
|NIC|✔| ✔|
|NSG|✔| NA|
|訂用帳戶|✔| ✔|

* 這些功能目前以公開預覽形式支援。

## <a name="next-steps"></a>後續步驟

- [Azure 資訊安全中心規劃和操作指南](security-center-planning-and-operations-guide.md) - 了解如何規劃及了解採用 Azure 資訊安全中心的設計考量
- [Azure 資訊安全中心不同類型的安全性警示](security-center-alerts-type.md#virtual-machine-behavioral-analysis) - 深入了解資訊安全中心中的虛擬機器行為分析和損毀傾印記憶體分析
- [Azure 資訊安全中心常見問題集](security-center-faq.md) — 尋找有關使用服務的常見問題。
- [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/) - 尋找有關 Azure 安全性與合規性的部落格文章
