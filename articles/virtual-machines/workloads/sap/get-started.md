---
title: 開始在 Azure VM 上使用 SAP | Microsoft Docs
description: 了解在 Microsoft Azure 中的虛擬機器 (VM) 上執行的 SAP 解決方案
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
ms.date: 01/22/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a30448580d394f83642de824f2d50115aaf964dc
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2019
ms.locfileid: "54448444"
---
# <a name="using-azure-for-hosting-and-running-sap-workload-scenarios"></a>使用 Azure 來裝載和執行 SAP 工作負載案例

藉由選擇 Microsoft Azure，您將能夠在可調整、符合規範且經企業證明的平台上，可靠地執行您的任務關鍵性 SAP 工作負載和案例。  Azure 不僅具備調整規模的能力，而且彈性十足，可以為您節省成本。 Microsoft 與 SAP 的合作讓您可以在 Azure 的多種開發/測試及生產案例中執行 SAP 應用程式，最重要是能夠獲得完整的支援。 從 SAP NetWeaver 到 SAP S4/HANA、SAP BI、Linux 到 Windows，乃至於 SAP HANA 到 SQL，全部含括在內。

除了在 Azure 上裝載具有不同 DBMS 的 SAP NetWeaver 案例外，您還可以在 Azure 上裝載其他不同的 SAP 工作負載案例，例如 SAP BI。 

Azure for SAP HANA 是獨一無二的供應項目，可讓 Azure 在競爭中顯得與眾不同。 為了讓涉及 SAP HANA 並裝載更嚴苛之記憶體和 CPU 資源的 SAP 案例能夠成真，Azure 可讓您使用客戶專用的裸機硬體來執行 SAP HANA 部署，以便應付最多需要 24 TB (可相應放大為 120 TB) 記憶體的 S/4HANA 或其他 SAP HANA 工作負載。 SAP HANA on Azure (大型執行個體) 的此一獨特 Azure 解決方案可讓您在專用的裸機硬體 (在原生 Azure 虛擬機器上裝載了 SAP 應用程式層或工作負載中介軟體層) 上執行 SAP HANA。 此解決方案記載於＜SAP HANA on Azure (大型執行個體)＞一節中的多份文件。   

在 Azure 中裝載 SAP 工作負載案例也會讓不同的 SAP 元件與 SAP SaaS (或 PaaS) 供應項目需要身分識別整合和單一登入 (使用 Azure Active Directory)。 這類使用 Azure Active Directory (AAD) 和 SAP 實體之整合和單一登入案例的清單會在＜AAD SAP 身分識別整合及單一登入＞一節中說明並加以記載。

## <a name="latest-changes"></a>最新變更

[SAP 工作負載規劃和部署檢查清單](sap-deployment-checklist.md)的版本

在 SAP 系統的延遲關鍵通訊路徑中，釐清 [Azure 網路虛擬設備](https://azure.microsoft.com/solutions/network-appliances/)的使用量和組態：

- [適用於 SAP 工作負載的 Azure 虛擬機器 DBMS 部署考量](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [SAP HANA 在 Azure 上的基礎結構組態和作業](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)



## <a name="sap-hana-on-sap-hana-on-azure-large-instances"></a>SAP HANA on Azure (大型執行個體) 上的 SAP HANA

一系列的文件將引導您透過 Azure 上的 SAP HANA (大型執行個體) 或在簡短的 HANA 大型執行個體中完成。 文件涵蓋 HANA 大型執行個體的所列區域：

- [SAP HANA on Azure (大型執行個體) 的概觀](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [SAP HANA on Azure (大型執行個體) 的架構](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-architecture)
- [SAP HANA on Azure (大型執行個體) 的基礎結構和連接](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity)
- [在 SAP HANA on Azure (大型執行個體) 上安裝 SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation)
- [SAP HANA on Azure (大型執行個體) 的高可用性和災害復原](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)
- [針對 SAP HANA on Azure (大型執行個體) 進行疑難排解和監視](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)

後續步驟：

- 參閱 [SAP HANA on Azure (大型執行個體) 的概觀與架構](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)



## <a name="sap-hana-on-azure-virtual-machines"></a>Azure 虛擬機器上的 SAP HANA
文件的本節涵蓋 SAP HANA 的不同層面。 先決條件是，您應該先熟悉提供 Azure IaaS 基礎服務的 Azure 主要服務，其中大部分是 Azure 計算、儲存和網路的知識。 其中許多主題會在 SAP NetWeaver 相關的 [Azure 規劃指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)中探討。 

Azure 上的 HANA on 特定的文件包含如下所列的文章及其子文章：

- [快速入門：在 Azure VM 上手動安裝單一執行個體 SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [在 Azure 上部署 SAP S/4HANA 或 BW/4HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)
- [SAP HANA 在 Azure 上的基礎結構組態和作業](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [Azure 虛擬機器的 SAP Hana 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [單一 Azure 區域中的 SAP HANA 可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)
- [跨 Azure 區域的 SAP HANA 可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions)
- [Azure 虛擬機器上 SAP HANA 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Azure 虛擬機器上的 SAP HANA 備份指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [檔案層級的 SAP HANA Azure 備份](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [以儲存體快照集為基礎的 SAP HANA 備份](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)

 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>在 Azure 虛擬機器上開發的 SAP NetWeaver
在本節中，您會找到 SAP NetWeaver Business One on Azure 的規劃和部署文件。 本章的文件著重於對 Azure 上的 SAP 工作負載使用非 HANA 資料庫的使用方式及基本項目。 HA 的文件和文章也是 Azure 中 HANA 高可用性的基礎。 文章清單如下：

- [Azure 虛擬機器上的 SAP Business One](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure)
- [在 Azure 上部署適用於 SAP ERP 6.0 的 SAP IDES EHP7 SP3](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-ides-erp6-erp7-sp3-sql)
- [在 Microsoft Azure SUSE Linux VM 上執行 SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart)
- [SAP NetWeaver 的 Azure 虛擬機器規劃和實作指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [適用於 SAP NetWeaver 的 Azure 虛擬機器部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [使用 Site Recovery 保護多層式 SAP NetWeaver 應用程式部署](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)
- [適用於 Azure 的 SAP LaMa 連接器](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/lama-installation)

關於 azure 上的 SAP 工作負載之下的非 HANA 資料庫，文件清單如下：

- [適用於 SAP 工作負載的 Azure 虛擬機器 DBMS 部署考量](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [適用於 SAP NetWeaver 的 SQL Server Azure 虛擬機器 DBMS 部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver)
- [適用於 SAP 工作負載的 Oracle Azure 虛擬機器 DBMS 部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_oracle)
- [適用於 SAP 工作負載的 IBM DB2 Azure 虛擬機器 DBMS 部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm)
- [適用於 SAP 工作負載的 SAP ASE Azure 虛擬機器 DBMS 部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- [Azure VM 上的 SAP MaxDB、liveCache 與內容伺服器部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)

對於 Azure 上的 SAP HANA 資料庫，請參閱「Azure 虛擬機器上的 SAP HANA」區段。

對於 Azure 上的 SAP 工作負載所達到的高可用性，入門文件是：

- [SAP NetWeaver 的 Azure 虛擬機器高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-start)

入門文件會指向其他各種架構和案例文件。 在後續的案例文件中，會提供詳細技術文件的連結，其中說明部署和設定不同高可用性方法。 建立和設定 SAP NetWeaver 工作負載高可用性的不同的文件涵蓋 Linux 和 Windows 作業系統。


關於 Azure Active Directory (AAD) 和 SAP 服務之間的整合及單一登入，文件清單如下：

- [教學課程：Azure Active Directory 與 SAP Cloud for Customer 整合](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-customer-cloud-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [教學課程：Azure Active Directory 與 SAP Cloud Platform Identity Authentication 整合](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [教學課程：Azure Active Directory 與 SAP 雲端平台整合](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [教學課程：Azure Active Directory 與 SAP NetWeaver 整合](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-netweaver-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [教學課程：Azure Active Directory 與 SAP Business ByDesign 整合](https://docs.microsoft.com/azure/active-directory/saas-apps/sapbusinessbydesign-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [教學課程：Azure Active Directory 與 SAP HANA 整合](https://docs.microsoft.com/azure/active-directory/saas-apps/saphana-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [您的 S/4HANA 環境 – Fiori Launchpad SAML 單一登入與 Azure AD](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

對於 Azure 服務整合到 SAP 元件，文件的清單如下：

- [在 Power BI Desktop 中使用 SAP HANA](https://docs.microsoft.com/power-bi/desktop-sap-hana)
- [DirectQuery 和 SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
- [在 Power BI Desktop 中使用 SAP BW 連接器](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector) 
- [Azure Data Factory 提供 SAP HANA 和 Business Warehouse 資料整合](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)




