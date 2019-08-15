---
title: Hyper-v 評估和遷移的 Azure Migrate 支援對照表
description: 摘要說明使用 Azure Migrate 服務進行 Hyper-v 評估和遷移的設定和限制。
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: raynew
ms.openlocfilehash: 00f222472a9b41c7f95ae90bdca57f13175b2b5d
ms.sourcegitcommit: acffa72239413c62662febd4e39ebcb6c6c0dd00
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/12/2019
ms.locfileid: "68952131"
---
# <a name="support-matrix-for-hyper-v-assessment-and-migration"></a>Hyper-V 評量和移轉的支援矩陣

您可以使用[Azure Migrate 服務](migrate-overview.md)來評估電腦, 並將機器遷移至 Microsoft Azure 雲端。 本文摘要說明評估和遷移內部部署 Hyper-v Vm 的支援設定和限制。



## <a name="hyper-v-scenarios"></a>Hyper-v 案例

下表摘要說明 Hyper-v Vm 的支援案例。

**部署** | **說明***
--- | ---
**評估內部部署 Hyper-v Vm** | [設定](tutorial-prepare-hyper-v.md)您的第一個評估。<br/><br/> [執行](scale-hyper-v-assessment.md)大規模的評量。
**將 Hyper-V VM 遷移至 Azure** | [嘗試](tutorial-migrate-hyper-v.md)遷移至 Azure。 

Azure Migrate 伺服器遷移不支援遷移以 System Center Virtual Machine Manager (VMM) 管理的 Hyper-v 伺服器。 

## <a name="azure-migrate-projects"></a>Azure Migrate 專案

**支援** | **詳細資料**
--- | ---
Azure 權限 | 您需要訂用帳戶中的「參與者」或「擁有者」許可權, 才能建立 Azure Migrate 專案。
Hyper-V VM | 在單一專案中評估最多35000個 Hyper-v Vm。 您在 Azure 訂用帳戶中可以有多個專案。 專案可以包含 VMware Vm 和 Hyper-v Vm, 最多可達評量限制。
地理 | 您可以在數個地理位置中建立 Azure Migrate 專案。 雖然您可以在特定 ographies 中建立專案, 但您可以評估或遷移其他目標位置的機器。 專案地理位置只會用來儲存探索到的資料。

  **地理位置** | **中繼資料儲存位置**
  --- | ---
  Azure Government | US Gov 維吉尼亞州
  亞太地區 | 東亞或東南亞
  澳洲 | 澳大利亞東部或澳大利亞東南部
  加拿大 | 加拿大中部或加拿大東部
  歐洲 | 北歐或西歐
  印度 | 印度中部或印度南部
  日本 |  日本東部或日本西部
  英國 | 英國南部或英國西部
  美國 | 美國中部或美國西部2


 > [!NOTE]
 > Azure Government 的支援目前僅適用于[舊版](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions)的 Azure Migrate。


## <a name="assessment-hyper-v-host-requirements"></a>評量-Hyper-v 主機需求

| **支援**                | **詳細資料**               
| :-------------------       | :------------------- |
| **主機部署**       | Hyper-v 主機可以是獨立或部署在叢集中。 |
| **Permissions**           | 您需要 Hyper-v 主機的系統管理員許可權。 <br/> 或者, 如果您不想要指派系統管理員許可權, 請建立本機或網域使用者帳戶, 並將使用者新增至這些群組-遠端系統管理使用者、Hyper-v 系統管理員和效能監視器使用者。 |
| **主機作業系統** | Windows Server 2019、Windows Server 2016 或 Windows Server 2012 R2。<br/> 您無法評估位於執行 Windows Server 2012 的 Hyper-v 主機上的 Vm。 |
| **PowerShell 遠端處理**   | 必須在每部主機上啟用。 |
| **Hyper-v 複本**       | 如果您使用 Hyper-v 複本 (或您有多個 vm 具有相同的 VM 識別碼), 並使用 Azure Migrate 探索原始和複寫的 Vm, 則 Azure Migrate 所產生的評量可能不正確。 |


## <a name="assessment-hyper-v-vm-requirements"></a>評量-Hyper-v VM 需求

| **支援**                  | **詳細資料**               
| :----------------------------- | :------------------- |
| **作業系統** | Azure 支援的所有[Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)和[Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)作業系統。 |
| **Integration Services**       | [Hyper-v Integration Services](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services)必須在您評估的 vm 上執行, 才能捕捉作業系統資訊。 |



## <a name="assessment-appliance-requirements"></a>評量-設備需求

針對評估, Azure Migrate 會執行輕量設備以探索 Hyper-v Vm, 並將 VM 中繼資料和效能資料傳送至 Azure Migrate。 設備會在 Hyper-v VM 上執行, 而且您會使用從 Azure 入口網站下載的壓縮 Hyper-v VHD 進行設定。 下表摘要說明設備需求。

| **支援**                | **詳細資料**               
| :-------------------       | :------------------- |
| **Azure Migrate 專案**  |  應用裝置可以與單一專案相關聯。<br/> 您可以使用單一設備探索最多5000個 Hyper-v Vm。
| **Hyper-V**    |  您會將設備部署為 Hyper-v VM。<br/> 提供的設備 VM 是 Hyper-v VM 5.0 版。<br/> VM 主機必須執行 Windows Server 2012 R2 或更新版本。<br/> 它需要足夠的空間來配置 16 GB RAM、8個 vcpu 和1部適用于設備 VM 的外部交換器。<br/> 設備需要靜態或動態 IP 位址, 以及網際網路存取。


## <a name="assessment-appliance-url-access"></a>評量-設備 URL 存取

若要評估 Vm, Azure Migrate 設備需要網際網路連線能力。

- 當您部署設備時, Azure Migrate 會對下表中摘要說明的 Url 進行連線檢查。
- 如果您使用以 URL 為基礎的 proxy, 請允許存取資料表中的 Url, 確保 proxy 會解析查詢 Url 時所收到的任何 CNAME 記錄。
- 如果您有攔截 proxy, 可能需要將伺服器憑證從 proxy 伺服器匯入設備。


**URL** | **詳細資料**  
--- | ---
*.portal.azure.com | 導覽至 Azure 入口網站
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *. microsoft.com <br/> *. live.com  | 登入您的 Azure 訂用帳戶：
*.microsoftonline.com <br/> *.microsoftonline-p.com | 建立設備對服務通訊的 Azure Active Directory 應用程式。
management.azure.com | 建立設備對服務通訊的 Azure Active Directory 應用程式。
dc.services.visualstudio.com | 記錄和監視
*.vault.azure.net | 在應用裝置與服務之間進行通訊時, 管理 Azure Key Vault 中的秘密。
aka.ms/* | 允許存取稱為的連結。
https://download.microsoft.com/download/* | 允許從 Microsoft 下載網站下載。



## <a name="assessment-port-requirements"></a>評量-埠需求

下表摘要說明評量的通訊埠需求。

**裝置** | **連接**
--- | ---
**台** | TCP 通訊埠3389上的輸入連線, 以允許應用裝置的遠端桌面連線。<br/> 埠44368上的輸入連線, 可使用 URL 從遠端存取應用裝置管理應用程式:``` https://<appliance-ip-or-name>:44368 ```<br/> 埠443、5671和5672上的輸出連線, 以將探索和效能中繼資料傳送至 Azure Migrate。
**Hyper-v 主機/叢集** | WinRM 埠 5985 (HTTP) 和 5986 (HTTPS) 上的輸入連線, 可使用通用訊息模型 (CIM) 會話提取 Hyper-v Vm 的設定和效能中繼資料。

## <a name="migration-hyper-v-host-requirements"></a>遷移-Hyper-v 主機需求

| **支援**                | **詳細資料**               
| :-------------------       | :------------------- |
| **主機部署**       | Hyper-v 主機可以是獨立或部署在叢集中。 |
| **Permissions**           | 您需要 Hyper-v 主機的系統管理員許可權。 |
| **主機作業系統** | Windows Server 2019、Windows Server 2016 或 Windows Server 2012 R2。 |

## <a name="migration-hyper-v-vm-requirements"></a>遷移-Hyper-v VM 需求

| **支援**                  | **詳細資料**               
| :----------------------------- | :------------------- |
| **作業系統** | Azure 支援的所有[Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)和[Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)作業系統。 |
| **Permissions**           | 您需要您想要評估的每部 Hyper-v VM 上的系統管理員許可權。 |
| **Integration Services**       | [Hyper-v Integration Services](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services)必須在您評估的 vm 上執行, 才能捕捉作業系統資訊。 |
| **Azure 的必要變更** | 有些 Vm 可能需要變更, 才能在 Azure 中執行。 Azure Migrate 會針對下列作業系統自動進行這些變更:<br/> -Red Hat Enterprise Linux 6.5 +、7.0 +<br/> -CentOS 6.5 +、7.0 +</br> -SUSE Linux Enterprise Server 12 SP1 +<br/> -Ubuntu 14.04 LTS、16.04 LTS、18.04 LTS<br/> -Debian 7、8<br/><br/> 如果是其他作業系統, 您必須在進行遷移之前手動進行調整。 相關文章包含如何執行這項操作的指示。 |
| **Linux 開機**                 | 如果/boot 是在專用磁碟分割上, 它應該位於 OS 磁片上, 而不會散佈到多個磁片上。<br/> 如果/boot 是根 (/) 分割區的一部分, 則 '/' 磁碟分割應該位於 OS 磁片上, 而不是跨越其他磁片。 |
| **UEFI 開機**                  | 不支援使用 UEFI 開機的 Vm 進行遷移。  |
| **磁碟大小**                  | 2 TB 適用于 OS 磁片, 4 TB 適用于資料磁片。
| **磁片編號** | 每個 VM 最多16個磁片。
| **加密的磁片/磁片區**    | 不支援遷移。 |
| **RDM/傳遞磁片**      | 不支援遷移。 |
| **共用磁片** | 不支援使用共用磁片的 Vm 進行遷移。
| **NFS**                        | 裝載為 Vm 上之磁片區的 NFS 磁片區不會複寫。 |
| **ISCSI**                      | 不支援具有 iSCSI 目標的 Vm 進行遷移。
| **目標磁片**                | 您只能使用受控磁片遷移至 Azure Vm。 |
| **IPv6** | 不支援。
| **NIC Teaming** | 不支援。
| **Azure Site Recovery** | 如果 VM 已啟用 Azure Site Recovery 的複寫, 您就無法使用 Azure Migrate Server 遷移進行複寫。





## <a name="migration-hyper-v-host-url-access"></a>遷移-Hyper-v 主機 URL 存取

下表摘要說明 Hyper-v 主機的 URL 存取需求。

**URL** | **詳細資料**  
--- | ---
login.microsoftonline.com | 使用 Active Directory 的存取控制和身分識別管理。
*.backup.windowsazure.com | 複寫資料傳輸和協調。
*.hypervrecoverymanager.windowsazure.com | 連接到 Azure Migrate 服務 Url。
*.blob.core.windows.net | 將資料上傳至儲存體帳戶。
dc.services.visualstudio.com | 上傳用於內部監視的應用程式記錄。
time.windows.com | 驗證系統與通用時間之間的時間同步處理。

## <a name="migration-port-access"></a>遷移-埠存取

下表摘要說明 Hyper-v 主機上的埠需求和 VM 遷移的 Vm。

**裝置** | **連接**
--- | ---
Hyper-v 主機/Vm | HTTPS 埠443上的輸出連線, 可將 VM 複寫資料傳送至 Azure Migrate。




## <a name="next-steps"></a>後續步驟

[準備進行 HYPER-V VM 評估](tutorial-prepare-hyper-v.md)以進行遷移。
