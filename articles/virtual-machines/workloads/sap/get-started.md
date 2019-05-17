---
title: 開始使用 Azure Vm 上的 SAP |Microsoft Docs
description: 深入了解 Microsoft Azure 中的虛擬機器 (Vm) 執行的 SAP 解決方案
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ad8e5c75-0cf6-4564-ae62-ea1246b4e5f2
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f14110cec0650927f74dfe3a38f06187c6df39de
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2019
ms.locfileid: "65792653"
---
# <a name="use-azure-to-host-and-run-sap-workload-scenarios"></a>使用 Azure 來裝載和執行 SAP 工作負載案例

當您使用 Microsoft Azure 時，可以可靠地在可調整、 符合規範且經企業證明的平台上執行您任務關鍵性 SAP 工作負載和案例。 您的延展性、 彈性，而且 Azure 的節省成本。 使用 Microsoft 和 SAP 的合作，您可以在開發、 測試及生產案例，在 Azure 中執行 SAP 應用程式並獲得完整的支援。 從 SAP NetWeaver 到 SAP S/4HANA，SAP BI，在 Windows、 Linux 以及 SAP HANA 到 SQL，我們為您分憂解。

除了裝載在 Azure 上的不同 dbms 的 SAP NetWeaver 案例，您可以主控其他的 SAP 工作負載案例，例如在 Azure 上的 SAP BI。 

Azure 的 SAP HANA 是供應項目，可讓 Azure 分開的。 若要啟用裝載更多的記憶體和 CPU 高資源 SAP 的情況下，SAP HANA，Azure 會提供客戶專屬的裸機硬體的使用。 使用此解決方案來執行 SAP HANA 部署，S/4HANA 或其他 SAP HANA 工作負載需要最多 24 TB （120 TB 向外延展） 的記憶體。 

裝載在 Azure 中的 SAP 工作負載案例也可以建立身分識別整合和單一登入的需求。 當您使用 Azure Active Directory (Azure AD) 連接不同的 SAP 元件和 SAP 軟體做為服務 (SaaS) 或平台為-即服務 (PaaS) 供應項目時，可以發生這種情況。 這類的整合和與 Azure AD 的單一登入案例的清單和 SAP 實體會描述與 < AAD SAP 身分識別整合和單一登入。 > 一節所述

## <a name="latest-changes"></a>最新變更

- 新版[Azure HANA 大型執行個體控制透過 Azure 入口網站](hana-li-portal.md)

- 新版[適用於 SUSE Linux Enterprise Server for SAP 應用程式的 Azure NetApp 檔案上的 Azure Vm 上的 SAP NetWeaver 的高可用性](high-availability-guide-suse-netapp-files.md)

- 釐清**Linux OS 參數 net.ipv4.tcp_timestamps**搭配 Azure 中的設定負載平衡器

- 新版[與 Azure 可用性區域的 SAP 工作負載組態](sap-ha-availability-zones.md)

- [SAP 工作負載規劃和部署檢查清單](sap-deployment-checklist.md)的版本




## <a name="sap-hana-on-azure-large-instances"></a>SAP HANA on Azure (大型執行個體)

一系列的文件將引導您完成 SAP HANA on Azure （大型執行個體），或簡稱，HANA 大型執行個體。 於下列領域的 HANA 大型執行個體的資訊，請參閱：

- [SAP HANA on Azure (大型執行個體) 的概觀](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [SAP HANA on Azure (大型執行個體) 的架構](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-architecture)
- [基礎結構和連接到 SAP HANA on Azure （大型執行個體）](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity)
- [安裝 SAP HANA on Azure （大型執行個體）](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation)
- [SAP HANA on Azure （大型執行個體） 的高可用性和災害復原](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)
- [進行疑難排解及監視 SAP HANA on Azure （大型執行個體）](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)

後續步驟：

- 讀取[的 SAP HANA on Azure （大型執行個體） 概觀和架構](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)



## <a name="sap-hana-on-azure-virtual-machines"></a>Azure 虛擬機器上的 SAP HANA
文件的本節涵蓋 SAP HANA 的不同層面。 先決條件是，您應該先熟悉與提供的 Azure IaaS 的基礎服務的服務主體的 Azure 程式碼。 因此，您需要 Azure 計算、 儲存體和網路功能的知識。 許多這些主題會處理在 SAP NetWeaver 相關[Azure 的規劃指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)。 

HANA on Azure 上的資訊，請參閱下列文章和其 subarticles:

- [快速入門：在 Azure VM 上手動安裝單一執行個體 SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [在 Azure 上部署 SAP S/4HANA 或 BW/4HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)
- [SAP HANA 在 Azure 上的基礎結構組態和作業](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [Azure 虛擬機器的 SAP Hana 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [單一 Azure 區域中的 SAP HANA 可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)
- [跨 Azure 區域的 SAP HANA 可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions)
- [Azure 虛擬機器上 SAP HANA 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Azure 虛擬機器上的 SAP HANA 的備份指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [檔案層級的 SAP HANA Azure 備份](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [以儲存體快照集為基礎的 SAP HANA 備份](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)

 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>Azure 虛擬機器上部署的 SAP NetWeaver
此區段會列出規劃和部署的 SAP NetWeaver Business One on Azure 的文件。 文件著重於使用非 HANA 資料庫及基本項目與 Azure 上 SAP 工作負載。 文件和文章，以獲得高可用性也是基礎 HANA 的高可用性，在 Azure 中，例如：

- [SAP Business One Azure 虛擬機器上](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure)
- [在 Azure 上部署適用於 SAP ERP 6.0 的 SAP IDES EHP7 SP3](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-ides-erp6-erp7-sp3-sql)
- [Microsoft Azure SUSE Linux Vm 上執行 SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart)
- [SAP NetWeaver 的 Azure 虛擬機器規劃和實作指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [適用於 SAP NetWeaver 的 Azure 虛擬機器部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [使用 Site Recovery 保護多層式 SAP NetWeaver 應用程式部署](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)
- [適用於 Azure 的 SAP LaMa 連接器](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/lama-installation)

如需在 Azure 上 SAP 工作負載下的非 HANA 資料庫的資訊，請參閱：

- [適用於 SAP 工作負載的 Azure 虛擬機器 DBMS 部署考量](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [適用於 SAP NetWeaver 的 SQL Server Azure 虛擬機器 DBMS 部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver)
- [適用於 SAP 工作負載的 Oracle Azure 虛擬機器 DBMS 部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_oracle)
- [適用於 SAP 工作負載的 IBM DB2 Azure 虛擬機器 DBMS 部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm)
- [適用於 SAP 工作負載的 SAP ASE Azure 虛擬機器 DBMS 部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- [在 Azure Vm 上的 SAP MaxDB、 即時快取和內容伺服器部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)

如需在 Azure 上的 SAP HANA 資料庫的資訊，請參閱一節 < SAP HANA on Azure 虛擬機器 」。

如需高可用性的 SAP 工作負載在 Azure 上的資訊，請參閱：

- [SAP NetWeaver 的 Azure 虛擬機器高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-start)

這份文件會指向各種其他架構和案例的文件。 在稍後的案例文件中會提供詳細說明部署和設定不同的高可用性方法的技術文件的連結。 示範如何建立及設定高可用性的 SAP NetWeaver 工作負載的不同文件涵蓋 Linux 和 Windows 作業系統。


如需有關 Azure Active Directory (Azure AD) 和 SAP 服務和單一登入之間的整合，請參閱：

- [教學課程：Azure Active Directory 與 SAP Cloud for Customer 整合](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-customer-cloud-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [教學課程：Azure Active Directory 與 SAP Cloud Platform Identity Authentication 整合](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [教學課程：Azure Active Directory 與 SAP 雲端平台整合](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [教學課程：Azure Active Directory 與 SAP NetWeaver 整合](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-netweaver-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [教學課程：Azure Active Directory 與 SAP Business ByDesign 整合](https://docs.microsoft.com/azure/active-directory/saas-apps/sapbusinessbydesign-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [教學課程：Azure Active Directory 與 SAP HANA 整合](https://docs.microsoft.com/azure/active-directory/saas-apps/saphana-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [S/4HANA 環境：Fiori Launchpad SAML 單一登入與 Azure AD](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

如需有關 SAP 元件與 Azure 服務整合，請參閱：

- [在 Power BI Desktop 中使用 SAP HANA](https://docs.microsoft.com/power-bi/desktop-sap-hana)
- [DirectQuery 和 SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
- [在 Power BI Desktop 中使用 SAP BW 連接器](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector) 
- [Azure Data Factory 提供 SAP HANA 和 Business Warehouse 資料整合](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)




