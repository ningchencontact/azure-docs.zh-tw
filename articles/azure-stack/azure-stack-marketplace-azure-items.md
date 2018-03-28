---
title: Azure Stack 可用的 Azure Marketplace 項目清單 | Microsoft Docs
description: 這些 Azure Marketplace 項目可用於 Azure Stack。
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: ae269c532773862e99884d84a05235cb904d13d4
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/16/2018
---
# <a name="azure-marketplace-items-available-for-azure-stack"></a>Azure Stack 可用的 Azure Marketplace 項目

*適用於：Azure Stack 整合系統和 Azure Stack 開發套件*


## <a name="virtual-machine-extensions"></a>虛擬機器擴充功能
只要您所使用的虛擬機器 (VM) 擴充功能有可用的更新，您即應加以下載。 產品隨附的擴充功能不會在正常修補程式和更新流程中進行更新；因此請經常檢查更新。 其他擴充功能只會透過 Marketplace Management 提供。

|  | 項目名稱 | 說明 | 發行者 | OS 類型 |
| --- | --- | --- | --- | --- |
|![](media/azure-stack-marketplace-azure-items/cse.png) | [SQL IaaS 擴充功能](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension)| <b>請下載此擴充功能以部署 Windows Marketplace 項目上的任何 SQL Server - 這是必要的。</b> | Microsoft | Windows |
|![](media/azure-stack-marketplace-azure-items/cse.png) | [自訂指令碼擴充功能](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/extensions-customscript)| <b>請將此更新下載到附隨版的 Windows 自訂指令碼擴充功能中。</b> | Microsoft | Windows |
|![](media/azure-stack-marketplace-azure-items/dsc.png) | [PowerShell DSC 擴充功能](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-dsc-overview)| <b>請將此更新下載到附隨版的 PowerShell DSC 擴充功能中。</b> | Microsoft | Windows |
| ![](media/azure-stack-marketplace-azure-items/cse.png) | [Microsoft 反惡意程式碼軟體擴充功能](https://docs.microsoft.com/en-us/azure/security/azure-security-antimalware)| 適用於 Azure 的 Microsoft Antimalware 是一個針對應用程式和租用戶環境所提供的單一代理程式解決方案，其設計可於無人為介入的情況下在背景中執行。 | Microsoft | Windows |
| ![](media/azure-stack-marketplace-azure-items/dockerextension.png) | [Docker](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft.docker-arm) | 適用於 Linux 虛擬機器的 Docker 擴充功能。 | Microsoft | Linux |
| ![](media/azure-stack-marketplace-azure-items/cse.png) | [ VM Access for Linux ](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/)| <b>請將此更新下載到附隨版的 VMAccess for Linux 擴充功能中。如果您打算使用 Debian Linux VM，這個更新很重要。</b> | Microsoft | Linux |
| ![](media/azure-stack-marketplace-azure-items/acronis.png) | [適用於 Linux 的 Acronis Backup 擴充功能](https://azuremarketplace.microsoft.com/marketplace/apps/Acronis.acronis-backup-lin-arm) | 適用於 Microsoft Azure 的 Acronis Backup 擴充功能，是 Acronis Backup 資料保護產品系列的一部分。 | Acronis International GmbH. | Linux |
| ![](media/azure-stack-marketplace-azure-items/acronis.png) | [適用於 Windows 的 Acronis Backup 擴充功能](https://azuremarketplace.microsoft.com/marketplace/apps/Acronis.acronis-backup-win-arm) | 適用於 Microsoft Azure 的 Acronis Backup 擴充功能，是 Acronis Backup 資料保護產品系列的一部分。 | Acronis International GmbH. | Windows |
| ![](media/azure-stack-marketplace-azure-items/cloudlink.png) | [適用於 Linux 的 CloudLink SecureVM 擴充功能](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/cloudlink.cloudlink-securevm)  | 輕鬆而有自信地控制、監視及加密 VM。 | Dell EMC | Linux |
| ![](media/azure-stack-marketplace-azure-items/cloudlink.png) | [適用於 Windows 的 CloudLink SecureVM 擴充功能](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/cloudlink.cloudlink-securevm)  | 輕鬆而有自信地控制、監視及加密 VM。 | Dell EMC | Windows |

## <a name="microsoft-virtual-machine-images-and-solution-templates"></a>Microsoft 虛擬機器映像和解決方案範本

Microsoft Azure Stack 支援下列 Azure Marketplace 虛擬機器和解決方案範本。 如前所述，請個別下載任何相依性。 除了標示為免費或試用版以外的應用程式，SQL Server 和 Machine Learning Server 等應用程式會需要適當的授權。

|  | 項目名稱 | 說明 | 發行者 |
| --- | --- | --- | --- |
| ![](media/azure-stack-marketplace-azure-items/windowsserver.png) | [Windows Server](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WindowsServer) | 簡單部署、符合成本效益、以應用程式為主並以使用者為中心的企業級解決方案。 我們會定期加入最新的修補程式來更新這些映像。 <b>重要資訊：必須將 2018 年 1 月 18 日前下載的映像刪除並以最新版本取代。</b> | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/remotedesktopservicesdeployment.png) | [遠端桌面服務 (RDS) 部署](https://azuremarketplace.microsoft.com/marketplace/apps/rds.remote-desktop-services-basic-deployment) | 建立基本遠端桌面服務 (RDS) 部署。 | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sharepoint.png) | [SharePoint Server 2013 試用版](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SharePointServer2013Trial) | Windows Server 2012 Datacenter 和 Visual Studio 2017 社群版上的 SharePoint Server 2013 試用版。 | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sharepoint.png) | [SharePoint Server 2016 試用版](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SharePointServer2016Trial) | Windows Server 2016 Datacenter 上的 SharePoint Server 2016 試用版。 | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2012 R2 上的 SQL Server 2014 SP2](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SQL2014SP2-WS2012R2) | SQL Server 2014 Service Pack 2。  **所需的下載：**SQL IaaS 延伸模組。 | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2016 上的 SQL Server 2016 SP1 Standard](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SQL2016SP1-WS2016) | 適用於智慧型、任務關鍵式應用程式的資料庫平台。 **所需的下載：**SQL IaaS 延伸模組。 | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2016 上的 SQL Server 2016 SP1 Developer 版本](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeLicenseSQLServer2016SP1DeveloperWindowsServer2016) | SQL Server 2016 SP1 免費開發人員版本，適用於交易、資料倉儲、商業智慧和分析工作負載。  **所需的下載：**SQL IaaS 延伸模組。 | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2016 上的 SQL Server 2016 SP1 Express 版本](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeLicenseSQLServer2016SP1ExpressWindowsServer2016) | 免費 Express 版本的 SQL Server 2016 SP1。 **所需的下載：**SQL IaaS 延伸模組。 | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [SQL Server 2016 SP1 Enterprise on Windows Server 2016](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SQL2016SP1-WS2016) | 適用於智慧型、任務關鍵式應用程式的資料庫平台。  **所需的下載：**SQL IaaS 延伸模組。 | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2016 上的 SQL Server 2016 SP1 Web](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SQL2016SP1-WS2016) | 適用於智慧型、任務關鍵式應用程式的資料庫平台。  **所需的下載：**SQL IaaS 延伸模組。 | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2016 上的 SQL Server 2017 Standard](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SQLServer2017StandardonWindowsServer2016) | 適用於智慧型、任務關鍵式應用程式的資料庫平台。 **所需的下載：**SQL IaaS 延伸模組。 | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2016 上的 SQL Server 2017 Developer](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016) | SQL Server 2016 SP1 免費開發人員版本，適用於交易、資料倉儲、商業智慧和分析工作負載。  **所需的下載：**SQL IaaS 延伸模組。 | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2016 上的 SQL Server 2017 Express](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016) | 免費 Express 版本的 SQL Server 2016 SP1。 **所需的下載：**SQL IaaS 延伸模組。 | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2016 上的 SQL Server 2017 Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SQLServer2017EnterpriseWindowsServer2016) | 適用於智慧型、任務關鍵式應用程式的資料庫平台。  **所需的下載：**SQL IaaS 延伸模組。 | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2016 上的 SQL Server 2017 Web](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SQLServer2017WebonWindowsServer2016) | 適用於智慧型、任務關鍵式應用程式的資料庫平台。  **所需的下載：**SQL IaaS 延伸模組。 | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Ubuntu Server 16.04 LTS 上的 SQL Server 2017 Standard](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS) | 適用於智慧型、任務關鍵式應用程式的資料庫平台。 | Microsoft + Canonical |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Ubuntu Server 16.04 LTS 上的 SQL Server 2017 Developer](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS) | SQL Server 2016 SP1 免費開發人員版本，適用於交易、資料倉儲、商業智慧和分析工作負載。 | Microsoft + Canonical |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Ubuntu Server 16.04 LTS 上的 SQL Server 2017 Express](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS) | 免費 Express 版本的 SQL Server 2016 SP1。 | Microsoft + Canonical |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Ubuntu Server 16.04 LTS 上的 SQL Server 2017 Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SQLServer2017EnterpriseUbuntuServer1604LTS) | 適用於智慧型、任務關鍵式應用程式的資料庫平台。 | Microsoft + Canonical |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Ubuntu Server 16.04 LTS 上的 SQL Server 2017 Web](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SQLServer2017WebonUbuntuServer1604LTS) | 適用於智慧型、任務關鍵式應用程式的資料庫平台。 | Microsoft + Canonical |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [SUSE Linux Enterprise Server (SLES) 12 SP2 上的 SQL Server 2017 Standard](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.Microsoft.SQLServer2017StandardonSLES12SP2) | 適用於智慧型、任務關鍵式應用程式的資料庫平台。 | Microsoft + SUSE |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [SUSE Linux Enterprise Server (SLES) 12 SP2 上的 SQL Server 2017 Developer](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP) | SQL Server 2016 SP1 免費開發人員版本，適用於交易、資料倉儲、商業智慧和分析工作負載。 | Microsoft + SUSE |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [SUSE Linux Enterprise Server (SLES) 12 SP2 上的 SQL Server 2017 Express](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2) | 免費 Express 版本的 SQL Server 2016 SP1。 | Microsoft + SUSE |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [SUSE Linux Enterprise Server (SLES) 12 SP2 上的 SQL Server 2017 Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.Microsoft.SQLServer2017EnterpriseonSLES12SP2) | 適用於智慧型、任務關鍵式應用程式的資料庫平台。 | Microsoft + SUSE |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [SUSE Linux Enterprise Server (SLES) 12 SP2 上的 SQL Server 2017 Web](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.Microsoft.SQLServer2017WebonSLES12SP2) | 適用於智慧型、任務關鍵式應用程式的資料庫平台。 | Microsoft + SUSE |
| ![](media/azure-stack-marketplace-azure-items/microsoft.png) | [Windows Server 2016 上的 Microsoft Machine Learning Server 9.3.0](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.MicrosoftMachineLearningServer930onWindowsServer2016) | Windows Server 2016 上的 Microsoft Machine Learning Server 9.3.0。 | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/microsoft.png) | [Ubuntu 16.04 上的 Microsoft Machine Learning Server 9.3.0](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.MicrosoftMachineLearningServer930onUbuntu1604) | Ubuntu 16.04 上的 Microsoft Machine Learning Server 9.3.0。 | Microsoft + Canonical |
| ![](media/azure-stack-marketplace-azure-items/microsoft.png) | [CentOS Linux 7.2 上的 Microsoft Machine Learning Server 9.3.0](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.MicrosoftMachineLearningServer930onCentOSLinux72) | CentOS Linux 7.2 上的 Microsoft Machine Learning Server 9.3.0。 | Microsoft + Rogue Wave |


## <a name="linux-distributions"></a>Linux 散發套件
|  | 項目名稱 | 說明 | 發行者 |
| --- | --- | --- | --- |
| ![](media/azure-stack-marketplace-azure-items/coreos.png) | [由 CoreOS 提供的 Container Linux](https://azuremarketplace.microsoft.com/marketplace/apps/CoreOS.CoreOS) | Container Linux 是最新最小的 Linux 發佈版本，提供簡單的方式來執行容器、管理叢集、順暢更新您的伺服器 — 所有元件都可進行倉儲規模的計算。 | CoreOS |
| ![](media/azure-stack-marketplace-azure-items/ubuntu.png) | [Ubuntu Server](https://azuremarketplace.microsoft.com/marketplace/apps/Canonical.UbuntuServer) | Ubuntu Server 是全世界最受歡迎的雲端 Linux 環境。 | Canonical |
| ![](media/azure-stack-marketplace-azure-items/debian8.png) | [Debian 8 "Jessie"](https://azuremarketplace.microsoft.com/marketplace/apps/credativ.Debian) | Debian GNU/Linux 是最受歡迎其中一個 Linux 散發套件。 | credativ |
| ![](media/azure-stack-marketplace-azure-items/debian8.png) | [Debian 9 "Stretch"](https://azuremarketplace.microsoft.com/marketplace/apps/credativ.Debian9) | Debian GNU/Linux 是最受歡迎其中一個 Linux 散發套件。 | credativ |
| ![](media/azure-stack-marketplace-azure-items/roguewave.png) | [CentOS 型 6.9](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased69) | 這個 Linux 的配送映像是以 CentOS 版為基礎，由 Rogue Wave Software 所提供。 | Rogue Wave Software (先前稱為 OpenLogic)  |
| ![](media/azure-stack-marketplace-azure-items/roguewave.png) | [CentOS 型 7.4](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased74) | 這個 Linux 的配送映像是以 CentOS 版為基礎，由 Rogue Wave Software 所提供。 | Rogue Wave Software (先前稱為 OpenLogic) |
| ![](media/azure-stack-marketplace-azure-items/roguewave.png) | [CentOS 型 7.4-LVM](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased74LVM) | 這個 Linux 的配送映像是以 CentOS 版為基礎，由 Rogue Wave Software 所提供。 | Rogue Wave Software (先前稱為 OpenLogic) |
| ![](media/azure-stack-marketplace-azure-items/suse.png) | [SLES 12 SP3 (BYOS) ](https://azuremarketplace.microsoft.com/marketplace/apps/SUSE.SUSELinuxEnterpriseServer12SP3BringYourOwnSubscription) | SUSE Linux Enterprise Server 12 SP3。 | SUSE |

## <a name="third-party-byol-free-and-trial-images-and-solution-templates"></a>第三方自備授權、免費和試用版映像以及解決方案範本

|  | 項目名稱 | 說明 | 發行者 |
| --- | --- | --- | --- |
| ![](media/azure-stack-marketplace-azure-items/abantecart.png) | [AbanteCart](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.abantecart) | 開放原始碼電子商務購物車。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/activemq.png) | [ActiveMQ](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.activemq) | JAVA 中的開放原始碼訊息代理程式。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/akeneo.png) | [Akeneo](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.akeneo) | 功能強大的 PIM，旨在簡化管理流程。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/alfrescocommunity.png) | [Alfresco Community](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.alfrescocommunity) | 可輕鬆管理文件的 ECM 系統。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/apachesolr.png) | [Apache Solr](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.apachesolr) | 可靠的開放原始碼企業搜尋平台。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/canvaslms.png) | [Canvas LMS](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.canvaslms) | 開放原始碼學習管理系統。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/cassandra.png) | [Cassandra](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.cassandra) | 具有高可用性的可擴充開放原始碼資料庫。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/civicrm.png) | [CiviCRM](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.civicrm) | 以網路為基礎的簡易關聯管理系統。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/cmsmadesimple.png) | [CMS Made Simple](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.cmsmadesimple) | 快速、輕鬆地建立和管理網站的方式。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/codedx.png) | [Code Dx](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.codedx) | 程式碼分析與弱點管理系統。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/codiad.png) | [Codiad](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.codiad) | 具有最少使用量的開放原始碼雲端整合式開發環境。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/concrete5.png) | [Concrete5](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.concrete5) | 可輕鬆部署 Web 應用程式、網站、商店和論壇。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/coppermine.png) | [Coppermine](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.coppermine) | 多用途、功能完整的 Web 資源庫。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/couchdb.png) | [CouchDB](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.couchdb) | 便於使用的開放原始碼資料庫系統。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/diaspora.png) | [Diaspora](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.diaspora) | 熱門的個人網頁伺服器。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/discourse.png) | [Discourse](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.discourse) | 高解析度的開放原始碼討論平台。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/django.png) | [Django](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.djangostack) | 高等級的 Python Web 架構。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/dolibarr.png) | [Dolibarr](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.dolibarr) | 免費的開放原始碼軟體套件。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/dokuwiki.png) | [DokuWiki](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.dokuwiki) | 多功能的開放原始碼 wiki 軟體。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/dreamfactory.png) | [DreamFactory](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.dreamfactory) | 具有 SQL、NoSQL 和 BLOB 等服務的開放原始碼 REST API。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/elasticsearch.png) | [Elasticsearch](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.elastic-search) | 彈性且功能強大的開放原始碼分析引擎。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/elk.png) | [ELK](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.elk) | 由 Elasticsearch、Kibana 和 Logstash 所組成的巨量資料套件。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/erpnext.png) | [ERPNext](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.erpnext) | 開放原始碼企業資源規劃 (ERP) 平台。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/espocrm.png) | [EspoCRM](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.espocrm) | 簡易 CRM 系統，可協助管理客戶關係。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/exoplatform.png) | [eXo Platform](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.exoplatform) | 開放原始碼、為企業而設計的社交軟體。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/fatfreecrm.png) | [Fat Free CRM](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.fatfreecrm) | 開放原始碼、以 Ruby on Rails 為基礎的 CRM。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/bitnami.png) | [GitLab Community Edition](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.gitlab) | 快速而安全的 Git 管理軟體。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/ghost.png) | [Ghost](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.ghost) | 專門用來發佈的平台。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/hadoop.png) | [Hadoop](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.hadoop) | 用於可靠、可擴充及分散式運算的架構。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/hhvm.png) | [HHVM](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.hhvmstack) | 完全整合且隨時可執行的開發環境。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/hordegroupwarewebmail.png) | [Horde Groupware Webmail](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.hordegroupwarewebmail) | 免費、適合企業使用、以瀏覽器為基礎的通訊套件。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/jenkins.png) | [Jenkins](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.jenkins) | 支援 SCM 工具 (CVS、Subversion 和 Git) 的整合伺服器。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/jfrogartifactory.png) | [由 Bitnami 認證的 JFrog Artifactory](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.artifactory) | 來自領導發行者的二進位存放庫軟體。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/joomla.png) | [Joomla!](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.joomla) | 方便使用的 CMS，可輕鬆建立網站。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/jruby.png) | [JRuby](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.jrubystack) | Ruby 的高效能 JAVA 實作。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/kafka.png) | [Kafka](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.kafka) | 功能強大的分散式發佈訂閱傳訊系統。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/lamp.png) | [LAMP](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.lampstack) | 完全整合且隨時可執行的開發環境。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/lapp.png) | [LAPP](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.lappstack) | 完整的 PHP、PostgreSQL 和 Apache 開發環境。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/letschat.png) | [Let's Chat](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.letschat) | 開放原始碼的持續傳訊應用程式。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/limesurvey.png) | [LimeSurvey](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.limesurvey) | 問題和答案輪詢管理系統。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/livehelperchat.png) | [Live Helper Chat](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.livehelperchat) | 開放原始碼的即時聊天支援。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/mahara.png) | [Mahara](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mahara) | 熱門的開放原始碼 ePortfolio 和社交網路 Web 應用程式。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/magento.png) | [Magento](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.magento) | 熱門的電子商務軟體和平台。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/mantis.png) | [Mantis](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mantis) | 進階的錯誤追蹤系統。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/mattermostteamedition.png) | [Mattermost Team Edition](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mattermost) | 開放原始碼的工作場所傳訊解決方案。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/mautic.png) | [Mautic](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mautic) | 開放原始碼的企業行銷自動化平台。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/mean.png) | [MEAN](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mean) | 適用於 MongoDB 及 Node.js 的熱門開發環境。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/mediawiki.png) | [MediaWiki](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mediawiki) | 強大、可擴充的 wiki 實作。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/memcached.png) | [Memcached](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.memcached) | 高效能、分散式記憶體物件快取系統。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/modx.png) | [MODX](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.modx) | 直覺式 Web CMS。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/mongodb.png) | [MongoDB](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mongodb) | 以 C++ 撰寫的高效能開放原始碼 NoSQL 資料庫。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/moodle.png) | [Moodle](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.moodle) | 針對線上學習社群設計的有效 CMS。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/multicraft.png) | [Multicraft](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.multicraft) | Minecraft 伺服器裝載解決方案和控制台。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/mybb.png) | [MyBB](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mybb) | 免費的開放原始碼論壇軟體。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/mysql.png) | [MySQL](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mysql) | 最熱門的資料庫系統。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/neos.png) | [Neos](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.neos) | 多功能的開放原始碼內容管理系統。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/nginx.png) | [Nginx](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.nginxstack) | 完整的 PHP、MySQL 和 Nginx 開發環境。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/noalyss.png) | [Noalyss](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.noalyss) | 功能強大的複式記帳會計系統。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/nodejs.png) | [node.js](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.nodejs) | 以 Javascript 撰寫的開放原始碼環境，可讓您輕鬆組建。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/odoo.png) | [Odoo](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.odoo) | 可有效連接商務流程的 ERP 和 CRM 系統。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/openatrium.png) | [Open Atrium](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.openatrium) | 彈性、多面向的內部網路平台。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/opencart.png) | [OpenCart](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.opencart) | 適用於線上商家的免費電子商務平台。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/openedx.png) | [由 Bitnami 認證的 Open edX](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.openedx) | 來自領導發行者的數位化學習軟體。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/openfire.png) | [OpenFire](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.openfire) | 具有 XMPP 的開放原始碼即時共同作業伺服器。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/openproject.png) | [OpenProject](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.openproject) | 熱門的開放原始碼專案管理軟體。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/orangehrm.png) | [OrangeHRM](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.orangehrm) | 具有豐富模組的人力資源管理系統。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/orocrm.png) | [OroCRM](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.orocrm) | 彈性的開放原始碼 CRM 應用程式。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/osclass.png) | [Osclass](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.osclass) | 不需具備專業技能，即可建立和管理免費的分類廣告。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/owncloud.png) | [ownCloud](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.owncloud) | 熱門的開放原始碼檔案同步處理和分享解決方案。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/oxideshop.png) | [OXID eShop](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.oxid-eshop) | 受信任的開放原始碼電子商務系統。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/phpbb.png) | [phpBB](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.phpbb) | 可自訂的公告欄解決方案。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/phplist.png) | [phpList](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.phplist) | 單向電子郵件宣告傳送系統。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/pimcore.png) | [Pimcore](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.pimcore) | 功能強大的業務開發管理平台 (CEM/CXM)。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/piwik.png) | [Piwik](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.piwik) | 即時 Web 分析軟體程式。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/plone.png) | [Plone](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.plone) | 免費的開放原始碼虛擬設備。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/pootle.png) | [Pootle](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.pootle) | 適用於轉譯專案的易用 Web 入口網站。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/postgresql.png) | [PostgreSQL](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.postgresql) | 非常進階的開放原始碼資料庫。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/prestashop.png) | [PrestaShop](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.prestashop) | 開放原始碼電子商務網站建立器。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/processmaker.png) | [Process Maker Community Edition](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.processmakeropensourceedition) | 商務流程管理和工作流程自動化平台。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/processmaker.png) | [Process Maker Enterprise Edition](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.processmakerenterprise) | 開放原始碼工作流程和商務流程管理軟體。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/processwire.png) | [ProcessWire](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.processwire) | 熱門的 PHP5 開放原始碼 CMS。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/publify.png) | [Publify](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.publify) | 以 Ruby on Rails 為基礎的部落格平台。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/rabbitmq.png) | [RabbitMQ](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.rabbitmq) | 提供通用平台而有效率的傳訊代理程式。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/redash.png) | [Re:dash](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.redash) | 開放原始碼的資料視覺效果和共同作業平台。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/redis.png) | [Redis](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.redis) | 功能強大的開放原始碼機碼值組存放區。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/redmine.png) | [Redmine](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.redmine) | 功能強大的專案管理 Web 應用程式 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/redmineagile.png) | [Redmine+Agile](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.redmineplusagile) | 已預先設定使用敏捷式外掛程式的專案管理應用程式。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/reportserver.png) | [ReportServer Community](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.reportserver) | 開放原始碼商業智慧平台。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/reportserverenterprise.png) | [ReportServer Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.reportserverenterprise) | 企業商業智慧平台。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/resourcespace.png) | [ResourceSpace](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.resourcespace) | 已改善共同作業的數位資產管理系統。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/roundcube.png) | [Roundcube](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.roundcube) | 以瀏覽器為基礎的 IMAP 用戶端，具有 MIME 支援等功能。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/ruby.png) | [Ruby](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.rubystack) | 便於使用的 Ruby on Rails 開發環境。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/seopanel.png) | [SEO Panel](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.seopanel) | 開放原始碼 SEO 管理應用程式，可追蹤多個網站。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/shopware.png) | [Shopware](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.shopware) | 開放原始碼電子商務平台。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/simplemachinesforum.png) | [Simple Machines Forum](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.simplemachinesforum) | 簡易論壇軟體，可組建您自己的線上社群。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/spree.png) | [Spree](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.spree) | 便於使用的電子商務平台。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/subversion.png) | [Subversion](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.subversion) | 開放原始碼的版本控制系統。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/sugarcrm.png) | [SugarCRM](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.sugarcrm) | 彈性的客戶關係管理解決方案。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/suitecrm.png) | [SuiteCRM](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.suitecrm) | 熱門的企業級 CRM 應用程式。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/testlink.png) | [TestLink](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.testlink) | 測試管理軟體，可促進品質保證。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/tikiwikicmsgroupware.png) | [Tiki Wiki CMS Groupware](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.tikiwikicmsgroupware) | 功能完整的 wiki 平台。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/tinytinyrss.png) | [Tiny Tiny RSS](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.tinytinyrss) | 具有彈性的開放原始碼網頁式新聞摘要和彙總工具。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/tomcat.png) | [Tomcat](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.tom-cat) | 可從 JAVA 社群實作規格的熱門平台。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/trac.png) | [Trac](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.trac) | 增強型 wiki 和問題追蹤系統。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/typo3.png) | [Typo3](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.typo3) | 具有完整彈性的 CMS。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/weblate.png) | [Weblate](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.weblate) | 以網路為基礎的翻譯管理系統。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/webmailprophp.png) | [WebMail Pro PHP](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.webmailpro) | 具有企業功能的 Web 郵件系統。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/wildfly.png) | [WildFly](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.wildfly) | 包含 Apache、WildFly、MySQL 和 JAVA 的應用程式伺服器。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/wordpress.png) | [WordPress](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.wordpress) | 最熱門且隨時可開始的 CMS。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/x2enginesalescrm.png) | [X2Engine Sales CRM](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.x2enginesalescrm) | 小型的行銷、銷售與客戶服務應用程式。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/xoops.png) | [Xoops](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.xoops) | 可建立動態網站的 CMS 和 Web 入口網站程式。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/zurmo.png) | [Zurmo](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.zurmo) | 開放原始碼的 CRM 系統：具有行動性、社交性且已遊戲化。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/barracuda.png) | [Barracuda 應用程式安全性控制中心](https://azuremarketplace.microsoft.com/marketplace/apps/barracudanetworks.barracuda-app-sec-control-center) | 集中管理多個 Barracuda Web 應用程式防火牆 (WAF)。 | Barracuda Networks, Inc. |
| ![](media/azure-stack-marketplace-azure-items/barracuda.png) | [Barracuda 電子郵件安全性閘道](https://azuremarketplace.microsoft.com/marketplace/apps/barracudanetworks.barracuda-email-security-gateway) | 電子郵件安全性閘道可用來防止輸入電子郵件所夾帶的威脅。 | Barracuda Networks, Inc. |
| ![](media/azure-stack-marketplace-azure-items/barracuda.png) | [Barracuda Web 應用程式防火牆 (WAF)](https://azuremarketplace.microsoft.com/marketplace/apps/barracudanetworks.waf) | 抵禦自動化和鎖定目標攻擊的安全性與 DDoS 保護。 | Barracuda Networks, Inc. |
| ![](media/azure-stack-marketplace-azure-items/checkpoint.png) | [檢查點 vSEC 安全性管理](https://azuremarketplace.microsoft.com/marketplace/apps/checkpoint.vsec) | 檢查點 vSEC 提供進階、多層式威脅防護，可保護客戶 Azure 中的資產免於惡意程式碼和複雜威脅的攻擊。 <b>下載兩個 Check Point vSEC 市集項目。</b> | Check Point |
| ![](media/azure-stack-marketplace-azure-items/chefautomate.png) | [Chef Automate](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate) | 使用 Chef Automate (連續自動化平台) 進行建置、部署及管理。 <b>下載兩個 Chef 市集項目</b> | Chef Software, Inc |
| ![](media/azure-stack-marketplace-azure-items/cloudlink.png) | [CloudLink SecureVM](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/cloudlink.cloudlink-securevm)  | 輕鬆而有自信地控制、監視及加密 VM。 <b>下載所有 CloudLink SecureVM 項目。<b> | Dell EMC |
| ![](media/azure-stack-marketplace-azure-items/hortonworks.png) | [Hortonworks Data Platform (HDP) 沙箱](https://azuremarketplace.microsoft.com/marketplace/apps/hortonworks.hortonworks-sandbox) | 由 HDP 2.5 所提供的 100% 開放原始碼平台，適用於 Hadoop、Spark、Storm、HBase、Kafka、Hive、Ambari。 | Hortonworks |
| ![](media/azure-stack-marketplace-azure-items/kemp.png) | [LoadMaster 負載平衡器 ADC 內容切換](https://azuremarketplace.microsoft.com/marketplace/apps/kemptech.vlm-azure) | 第 4-7 層應用程式傳遞控制器 (ADC) 負載平衡器、內容切換和流量管理員。 | KEMP Technologies Inc. |
| ![](media/azure-stack-marketplace-azure-items/noobaa.png) | [NooBaa Hybird AWS S3 相容 - 社群版](https://azuremarketplace.microsoft.com/marketplace/apps/noobaa.noobaa-hybrid-s3-archive-05) | 橫跨公用和內部部署容量資源的 S3 相容存體服務。 | NooBaa |
| ![](media/azure-stack-marketplace-azure-items/puppet.png) | [Puppet Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/puppet.puppet-enterprise-2017-2) | Puppet Enterprise 可讓您將整個 Azure Stack 基礎結構的生命週期自動化。 <b>下載兩個 Puppet Marketplace 項目。</b> | Puppet |
| ![](media/azure-stack-marketplace-azure-items/suse.png) | [SUSE Manager 3.0 Proxy (BYOS)](https://azuremarketplace.microsoft.com/marketplace/apps/SUSE.SUSEManager30ProxyBringYourOwnSubscription) | 同級產品中最佳的開放原始碼基礎結構管理。 | SUSE |
| ![](media/azure-stack-marketplace-azure-items/zerodown.png) | [ZeroDown Software Tool ](https://azuremarketplace.microsoft.com/marketplace/apps/zerodown_software.zerodown-software-tool-7_1) | ZeroDown Software Tool v7.1 - Migrate + HA. | ZeroDown Software |
