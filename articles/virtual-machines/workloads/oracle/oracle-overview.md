---
title: Microsoft Azure 上的 Oracle 解決方案 | Microsoft Docs
description: 了解部署 Oracle 應用程式和 Microsoft Azure，包括完全在 Azure 基礎結構上執行，或使用跨雲端連線能力與 Oracle 雲端基礎結構 (OCI) 解決方案的選項。
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: jeconnoc
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/04/2019
ms.author: rogirdh
ms.openlocfilehash: b5e40975fca491f289d949ee273d13053897fc6d
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/06/2019
ms.locfileid: "66743631"
---
# <a name="overview-of-oracle-applications-and-solutions-on-azure"></a>Oracle 應用程式和 Azure 上的解決方案概觀

這篇文章介紹執行使用 Azure 基礎結構的 Oracle 解決方案的功能。 另請參閱可用詳細的介紹[Oracle VM 映像](oracle-vm-solutions.md)在 Azure Marketplace 和預覽功能[互連 Azure 與 Oracle 雲端基礎結構 (OCI)](oracle-oci-overview.md)。

## <a name="oracle-databases-on-azure-infrastructure"></a>在 Azure 基礎結構上的 oracle 資料庫

使用 Azure Marketplace 中的 Linux 映像的 Azure 基礎結構上執行 Oracle 資料庫：

* Oracle Database 12.1、 12.2 和 18.3 Enterprise Edition 

* Oracle Database 12.1、 12.2 和 18.3 Standard Edition 

您也可以選擇根據您在 Azure 中從頭開始建立，或從您的內部部署環境上傳自訂映像的自訂映像的解決方案。

選擇性地使用多個連接的磁碟設定，並藉由安裝 Oracle Automated Storage Management (ASM) 改善資料庫效能。

## <a name="applications-on-oracle-linux-and-weblogic-server"></a>Oracle Linux 和 WebLogic Server 上的應用程式

在 Azure 中的企業應用程式執行受支援的 Oracle 作業系統上。 下列映像可在 Azure Marketplace 中：

* Oracle WebLogic Server 12.1.2

* Oracle Linux 6.8、 6.9、 6.10、 7.3、 7.4、 7.5 和 7.6 (UEK)

## <a name="high-availability-and-disaster-recovery-options"></a>高可用性和災害復原選項

* 搭配 Azure 基礎結構上設定 Oracle Data Guard、 Active Data Guard 或 GoldenGate[可用性區域](../../../availability-zones/az-overview.md)以獲得高可用性。

* 使用[Azure Site Recovery](../../../site-recovery/site-recovery-overview.md)來協調和管理 Azure 和內部部署或實體伺服器中的 Oracle Linux Vm 的災害復原。 

* 啟用 Oracle Real Application Clusters (RAC)，在 Azure 中使用[FlashGrid SkyCluster](https://www.flashgrid.io/oracle-rac-in-azure/)。

## <a name="integration-of-azure-with-oci-preview"></a>Azure 中使用 OCI （預覽） 的整合

在 Azure 基礎結構，連接到後端資料庫在 Oracle 雲端基礎結構 (OCI) 中執行 Oracle 應用程式。 此解決方案會使用下列功能： 

* **跨雲端網路**-使用直接的相互連線可用來建立應用程式和資料庫層級之間的高頻寬、 私用和低延遲連線的 Azure ExpressRoute 與 Oracle FastConnect 之間。
* **整合式身分識別**-設定 Azure AD 之間的同盟身分識別和 Oracle IDCS，若要建立之解決方案的單一身分識別來源。 啟用單一登入來管理跨 OCI 與 Azure 的資源。

### <a name="deploy-oracle-applications-on-azure"></a>部署在 Azure 上的 Oracle 應用程式

若要設定 Azure 基礎結構，並安裝 Oracle 應用程式進行驗證，並支援執行跨雲端組態中使用 Terraform 範本：

* E-business Suite
* JD Edwards EnterpriseOne
* PeopleSoft
* Oracle 零售應用程式
* Oracle Hyperion 財務管理

也部署在 Azure 中自訂 OCI 與其他 Azure 服務連線的應用程式。

### <a name="set-up-oracle-databases-in-oci"></a>設定 Oracle OCI 中的資料庫

在 Azure 中執行的 Oracle 應用程式搭配使用 Oracle Database 雲端服務 （自主資料庫，RAC、 Exadata、 DBaaS，單一節點）。 深入了解[OCI 資料庫選項](https://docs.cloud.oracle.com/iaas/Content/Database/Concepts/databaseoverview.htm)。 
 

## <a name="licensing"></a>授權

在 Azure 中的 Oracle 應用程式的部署是以 「 攜帶您自己的授權 」 模型為基礎。 它會假設您有正確授權使用 Oracle 軟體，而且您有目前的支援合約，在與 Oracle 的地方。 Oracle 已保證從內部部署至 Azure 的授權行動性。 請參閱 Oracle Azure[常見問題集](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html)。

## <a name="next-steps"></a>後續步驟

* 深入了解部署[Oracle VM 映像](oracle-vm-solutions.md)Azure 基礎結構中。

* 深入了解如何[互連 Azure OCI](oracle-oci-overview.md)。