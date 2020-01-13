---
title: 支援使用 Azure Migrate 進行實體伺服器評估/遷移
description: 摘要說明使用 Azure Migrate 進行實體伺服器評量/遷移的支援。
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 9e749297d831aeae7d785a9a9a29bea1f8c6d5e3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454626"
---
# <a name="support-matrix-for-physical-server-assessment-and-migration"></a>實體伺服器評量和移轉的支援矩陣

您可以使用[Azure Migrate 服務](migrate-overview.md)來評估電腦，並將機器遷移至 Microsoft Azure 雲端。 本文摘要說明評估和遷移內部部署實體伺服器的支援設定和限制。



## <a name="physical-server-scenarios"></a>實體伺服器案例

下表摘要說明實體伺服器的支援案例。

**部署** | **詳細資料***
--- | ---
**評估內部部署實體伺服器** | [設定](tutorial-prepare-physical.md)您的第一個評估。<br/><br/> [執行](tutorial-assess-physical.md)評量。
**將實體伺服器移轉至 Azure** | [嘗試](tutorial-migrate-physical-virtual-machines.md)遷移至 Azure。


## <a name="azure-migrate-projects"></a>Azure Migrate 專案

**支援** | **詳細資料**
--- | ---
**Azure 許可權** | 您需要訂用帳戶中的「參與者」或「擁有者」許可權，才能建立 Azure Migrate 專案。
**實體伺服器** | 在單一專案中評估最多35000個實體伺服器。 您在 Azure 訂用帳戶中可以有多個專案。 專案可以包含實體伺服器、VMware Vm 和 Hyper-v Vm，最多可達評量限制。
**地理位置** | 您可以在數個地理位置中建立 Azure Migrate 專案。 雖然您可以在特定地區建立專案，但您可以針對其他目標位置評估或遷移電腦。 專案地理位置只會用來儲存探索到的資料。

  **地理位置** | **中繼資料儲存位置**
  --- | ---
  Azure 政府機構 | US Gov 維吉尼亞州
  亞太地區 | 東亞或東南亞
  澳洲 | 澳大利亞東部或澳大利亞東南部
  巴西 | 巴西南部
  加拿大 | 加拿大中部或加拿大東部
  歐洲 | 北歐或西歐
  法國 | 法國中部
  印度 | 印度中部或印度南部
  日本 |  日本東部或日本西部
  南韓 | 韓國中部或南韓南部
  英國 | 英國南部或英國西部
  美國 | 美國中部或美國西部2


 > [!NOTE]
 > Azure Government 的支援目前僅適用于[舊版](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions)的 Azure Migrate。


## <a name="assessment-physical-server-requirements"></a>評量-實體伺服器需求

| **支援**                | **詳細資料**               
| :-------------------       | :------------------- |
| **實體伺服器部署**       | 實體伺服器可以是獨立或部署在叢集中。 |
| **權限**           | **Windows：** 在您要包含在探索中的所有 Windows 伺服器上設定本機使用者帳戶。使用者帳戶必須新增至這些群組-遠端桌面使用者、效能監視器使用者和效能記錄使用者。 <br/> **Linux：** 您在要探索的 Linux 伺服器上需要根帳號。 |
| **作業系統** | 除了下列以外，所有的[Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)和[Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)作業系統都受到支援：<br/> Windows Server 2003 <br/> SUSE Linux|


## <a name="assessment-appliance-requirements"></a>評量-設備需求

針對評估，Azure Migrate 會執行輕量設備以探索實體伺服器，並將伺服器中繼資料和效能資料傳送給 Azure Migrate。 設備可以在實體伺服器或 VM 上執行，而且您可以使用從 Azure 入口網站下載的 PowerShell 腳本進行設定。 下表摘要說明設備需求。

| **支援**                | **詳細資料**               
| :-------------------       | :------------------- |
| **設備部署**   |  您可以從入口網站下載設備安裝程式腳本（在壓縮的資料夾中）。 <br/> 您可以將資料夾解壓縮，並在專用的實體伺服器或虛擬機器上執行 PowerShell 腳本（AzureMigrateInstaller），以設定設備。<br/>  選擇要安裝設備的電腦必須執行 Windows Server 2016。<br/> 機器需要足夠的空間來配置 16 GB RAM、8個 vcpu，大約 80 GB 的儲存空間，以及適用于設備 VM 的外部交換器。<br/> 設備需要靜態或動態 IP 位址，以及網際網路存取。
| **Azure Migrate 專案**  |  應用裝置可以與單一專案相關聯。<br/> 任何數目的設備都可以與單一專案相關聯。<br/> 您可以在專案中評估最多35000部機器。
| **探索              | 單一設備可以探索最多250部伺服器。
| **評量群組**       | 您最多可以在單一群組中新增35000部電腦。
| **評量**             | 您可以在單一評估中評估多達35000部機器。


## <a name="assessment-appliance-url-access"></a>評量-設備 URL 存取

若要評估 Vm，Azure Migrate 設備需要網際網路連線能力。

- 當您部署設備時，Azure Migrate 會對下表中摘要說明的 Url 進行連線檢查。
- 如果您使用以 URL 為基礎的 proxy，請允許存取資料表中的 Url，確保 proxy 會解析查詢 Url 時所收到的任何 CNAME 記錄。
- 如果您有攔截 proxy，可能需要將伺服器憑證從 proxy 伺服器匯入設備。


**URL** | **詳細資料**  
--- | ---
*.portal.azure.com | 導覽至 Azure 入口網站
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com  | 登入您的 Azure 訂用帳戶
*.microsoftonline.com <br/> *.microsoftonline-p.com | 建立設備對服務通訊的 Azure Active Directory 應用程式。
management.azure.com | 建立設備對服務通訊的 Azure Active Directory 應用程式。
dc.services.visualstudio.com | 記錄和監視
*.vault.azure.net | 在應用裝置與服務之間進行通訊時，管理 Azure Key Vault 中的秘密。
aka.ms/* | 允許存取稱為的連結。
https://download.microsoft.com/download/* | 允許從 Microsoft 下載網站下載。



## <a name="assessment-port-requirements"></a>評量-埠需求

下表摘要說明評量的通訊埠需求。

**裝置** | **[連接]**
--- | ---
**台** | TCP 通訊埠3389上的輸入連線，以允許應用裝置的遠端桌面連線。<br/> 埠44368上的輸入連線，可使用 URL 從遠端存取應用裝置管理應用程式： ``` https://<appliance-ip-or-name>:44368 ```<br/> 埠443、5671和5672上的輸出連線，以將探索和效能中繼資料傳送至 Azure Migrate。
**實體伺服器** | **Windows：** 埠443上的輸入連線、WinRM 埠5985（HTTP）和5986（HTTPS），以從 Windows 伺服器提取設定和效能中繼資料。 <br/> **Linux：** 埠22（UDP）上的輸入連線，可從 Linux 伺服器提取設定和效能中繼資料。 |


## <a name="next-steps"></a>後續步驟

針對實體伺服器評估和遷移[準備進行實體伺服器評估](tutorial-prepare-physical.md)。
