---
title: Azure HYPER-V 評定及移轉的移轉支援矩陣
description: 摘要說明設定和 HYPER-V 評定及使用 Azure Migrate 服務移轉的限制。
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 06/02/2019
ms.author: raynew
ms.openlocfilehash: f6edbe19429b38d68aea1f1ecfe426c9b2d194d0
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811345"
---
# <a name="support-matrix-for-hyper-v-assessment-and-migration"></a>HYPER-V 評定及移轉支援矩陣

您可以使用[Azure Migrate 服務](migrate-overview.md)評定，並將機器移轉至 Microsoft Azure 雲端。 本文摘要說明支援設定和評估和移轉內部部署 HYPER-V Vm 的限制。



## <a name="hyper-v-scenarios"></a>HYPER-V 案例

下表摘要說明支援的案例，適用於 HYPER-V Vm。

**部署** | **詳細資料*** 
--- | --- 
**評估內部部署 HYPER-V Vm** | [設定](tutorial-prepare-hyper-v.md)您第一次評估。<br/><br/> [執行](scale-hyper-v-assessment.md)大規模評估。
**將 HYPER-V Vm 移轉至 Azure** | [試試看](tutorial-migrate-hyper-v.md)移轉至 Azure。

    

## <a name="azure-migrate-projects"></a>Azure Migrate 專案

**支援** | **詳細資料**
--- | ---
Azure 權限 | 您需要 「 參與者 」 或 「 擁有者權限的訂用帳戶中建立 Azure Migrate 專案。
Hyper-V VM | 評估最多 10,000 個單一專案中的 HYPER-V Vm。

專案可以包含 VMware Vm 和 HYPER-V Vm 到評定的上限。


## <a name="assessment-hyper-v-host-requirements"></a>評估 Hyper V 主機需求

| **支援**                | **詳細資料**               
| :-------------------       | :------------------- |
| **主機部署**       | 可以是獨立 HYPER-V 主機，或部署在叢集中。 |
| **Permissions**           | 您必須在 HYPER-V 主機上的系統管理員權限。 |
| **主機作業系統** | Windows Server 2016 或 Windows Server 2012 R2。<br/> 您無法評估在執行 Windows Server 2019 的 HYPER-V 主機上的 Vm。 |
| **PowerShell 遠端執行功能**   | 必須啟用每個主機上。 |
| **Hyper-V Replica**       | 如果您使用 HYPER-V 複本 （或您有多個 Vm 使用相同的 VM 識別碼），並探索這兩個原始和複寫 Vm 使用 Azure Migrate，產生的 Azure Migrate 評估可能不正確。 |


## <a name="assessment-hyper-v-vm-requirements"></a>評估 HYPER-V VM 需求

| **支援**                  | **詳細資料**               
| :----------------------------- | :------------------- |
| **作業系統** | 所有[Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)並[Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) Azure 所支援的作業系統。 |
| **Permissions**           | 您需要在每個您想要評估的 HYPER-V VM 上的系統管理員權限。 |
| **Integration Services**       | [HYPER-V 整合服務](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services)必須評估為了擷取作業系統資訊的 Vm 上執行。 |
| **適用於 Azure 的必要的變更** | 某些 Vm 可能需要變更，以便它們可以在 Azure 中執行。 Azure Migrate 讓這些變更會自動針對下列作業系統：<br/> -Red Hat Enterprise Linux 6.5 + 7.0 +<br/> -CentOS 6.5 + 7.0 +</br> - SUSE Linux Enterprise Server 12 SP1+<br/> - Ubuntu 14.04LTS, 16.04LTS, 18.04LTS<br/> -Debian 7、 8<br/><br/> 對於其他作業系統中，您需要進行移轉之前以手動方式調整。 相關的文章包含有關如何執行這項操作的指示。 |
| **Linux 開機**                 | 如果 /boot 專用的磁碟分割上，它應該位於 OS 磁碟，並不會分散到多個磁碟。<br/> /Boot 是否屬於根 （/） 磁碟分割，然後 '/' 的分割區應該位於 OS 磁碟，並不會跨越其他磁碟。 |
| **UEFI 開機**                  | 移轉不支援使用 UEFI 開機的 Vm。 |
| **加密的磁碟/磁碟區**    | 不支援移轉具有加密的磁碟/磁碟區的 Vm。 |
| **RDM/穿透式磁碟**      | 如果 Vm 有 RDM 或傳遞磁碟，這些磁碟將不會複寫至 Azure。 |
| **NFS**                        | 不會複寫的 Vm 上掛接為磁碟區的 NFS 磁碟區。 |
| **目標磁碟**                | 使用僅限受控磁碟，azure Migrate 評估會建議移轉至 Azure Vm。 |


## <a name="assessment-appliance-requirements"></a>評估設備需求

進行評估，Azure Migrate 會執行輕量型的設備，以探索 HYPER-V Vm，並將 VM 的中繼資料和效能資料傳送至 Azure Migrate。 應用裝置上執行 HYPER-V VM，而您設定使用您從 Azure 入口網站下載的壓縮為 HYPER-V VHD。 下表摘要說明的設備需求。

| **支援**                | **詳細資料**               
| :-------------------       | :------------------- |
| **Azure Migrate 專案**  |  應用裝置可以是與單一專案建立關聯。<br/> 您可以探索最多 5000 的 HYPER-V Vm 與單一應用裝置。
| **HYPER-V 的限制**    |  您可以部署應用裝置做為 HYPER-V VM。<br/> 設備所提供的 VM 是 HYPER-V VM 5.0 版。<br/> VM 主機必須執行 Windows Server 2012 R2 或更新版本。<br/> 它需要足夠的空間可配置 16 GB RAM，4 個虛擬處理器，而且 1 外部切換設備 VM。<br/> 應用裝置需要靜態或動態 IP 位址和網際網路存取。
| **HYPER-V 應用裝置**      |  應用裝置會設為 HYPER-V VM。<br/> 供使用者下載的 VHD 是 HYPER-V VM 5.0 版。
| **Host**                   | VM 主機執行的應用裝置 VM 必須執行 Windows Server 2012 R2 或更新版本。<br/> 它必須以配置 16 GB RAM、 4 個虛擬處理器和一個外部交換器應用裝置 VM 所需的足夠空間。<br/> 應用裝置需要靜態或動態 IP 位址和網際網路存取。 |
| **移轉支援**      | 若要開始將機器複寫，移轉閘道應用裝置上的服務必須是 1.18.7141.12919 或更新版本。 若要檢查版本，以登入應用裝置的 web 應用程式。 |

## <a name="assessment-appliance-url-access"></a>評估設備 URL 存取

若要評估的 Vm，Azure Migrate 應用裝置會需要網際網路連線。

- 當您部署應用裝置時，Azure Migrate 會連線能力檢查，以在下表中摘要的 Url。
- 如果您使用以 URL 為基礎的 firewall.proxy，允許存取的 Url，在資料表中，確定 proxy 解析的查閱 Url 時收到的任何 CNAME 記錄。
- 如果您有攔截 proxy 時，您可能需要將憑證匯入伺服器的 proxy 伺服器從設備。 

    
**URL** | **詳細資料**  
--- | --- 
*.portal.azure.com | 瀏覽至 Azure 入口網站
*.windows.net | 登入您的 Azure 訂用帳戶：
*.microsoftonline.com | 以服務的通訊設備建立的 Azure Active Directory 應用程式。
management.azure.com | 以服務的通訊設備建立的 Azure Active Directory 應用程式。
dc.services.visualstudio.com | 記錄和監視 
*.vault.azure.net | 應用裝置與服務之間進行通訊時，管理在 Azure Key Vault 的祕密。


## <a name="assessment-port-requirements"></a>評估連接埠需求

下表摘要評估的連接埠需求。

**裝置** | **連接**
--- | --- 
**Appliance** | TCP 連接埠 3389，以允許遠端桌面連線至應用裝置上的傳入的連接。<br/> 輸入連接埠 44368 從遠端存取應用裝置管理應用程式使用的 URL 上的連線： https://<appliance-ip-or-name>:44368<br/> 若要將探索和效能的中繼資料傳送至 Azure Migrate 的連接埠 443 上的傳出連線。
**HYPER-V 主機/叢集** | 輸入在 WinRM 連接埠 5985 (HTTP) 和 5986 (HTTPS)，以提取設定和效能的中繼資料，使用通用訊息模型 (CIM) 工作階段的 HYPER-V vm 上的連線。

## <a name="migration-hyper-v-host-requirements"></a>移轉 Hyper V 主機需求

| **支援**                | **詳細資料**               
| :-------------------       | :------------------- |
| **主機部署**       | 可以是獨立 HYPER-V 主機，或部署在叢集中。 |
| **Permissions**           | 您必須在 HYPER-V 主機上的系統管理員權限。 |
| **主機作業系統** | Windows Server 2019、 Windows Server 2016 或 Windows Server 2012 R2。 |

## <a name="migration-hyper-v-vm-requirements"></a>移轉-HYPER-V VM 需求

| **支援**                  | **詳細資料**               
| :----------------------------- | :------------------- |
| **作業系統** | 所有[Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)並[Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) Azure 所支援的作業系統。 |
| **Permissions**           | 您需要在每個您想要評估的 HYPER-V VM 上的系統管理員權限。 |
| **Integration Services**       | [HYPER-V 整合服務](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services)必須評估為了擷取作業系統資訊的 Vm 上執行。 |
| **適用於 Azure 的必要的變更** | 某些 Vm 可能需要變更，以便它們可以在 Azure 中執行。 Azure Migrate 讓這些變更會自動針對下列作業系統：<br/> -Red Hat Enterprise Linux 6.5 + 7.0 +<br/> -CentOS 6.5 + 7.0 +</br> - SUSE Linux Enterprise Server 12 SP1+<br/> - Ubuntu 14.04LTS, 16.04LTS, 18.04LTS<br/> -Debian 7、 8<br/><br/> 對於其他作業系統中，您需要進行移轉之前以手動方式調整。 相關的文章包含有關如何執行這項操作的指示。 |
| **Linux 開機**                 | 如果 /boot 專用的磁碟分割上，它應該位於 OS 磁碟，並不會分散到多個磁碟。<br/> /Boot 是否屬於根 （/） 磁碟分割，然後 '/' 的分割區應該位於 OS 磁碟，並不會跨越其他磁碟。 |
| **UEFI 開機**                  | 移轉不支援使用 UEFI 開機的 Vm。 |
| **加密的磁碟/磁碟區**    | 不支援移轉具有加密的磁碟/磁碟區的 Vm。 |
| **RDM/穿透式磁碟**      | 如果 Vm 有 RDM 或傳遞磁碟，這些磁碟將不會複寫至 Azure。 |
| **NFS**                        | 不會複寫的 Vm 上掛接為磁碟區的 NFS 磁碟區。 |
| **目標磁碟**                | 您可以使用僅限受控磁碟移轉到 Azure Vm。 |




## <a name="migration-hyper-v-host-url-access"></a>移轉 Hyper V 主機 URL 存取

下表摘要說明 HYPER-V 主機的 URL 存取需求。

**URL** | **詳細資料**  
--- | ---
login.microsoftonline.com | 存取控制和身分識別管理使用 Active Directory。
*.backup.windowsazure.com | 複寫資料傳輸和協調。
*.hypervrecoverymanager.windowsazure.com | 連接到 Azure Migrate 服務的 Url。
*.blob.core.windows.net | 將資料上傳至儲存體帳戶。
dc.services.visualstudio.com | 上傳用於內部監視的應用程式記錄檔。
time.windows.com | 驗證系統時間與通用時間之間的時間同步處理。

## <a name="migration-port-access"></a>移轉連接埠存取

下表摘要說明在 HYPER-V 主機和 Vm 上的 VM 移轉的連接埠需求。

**裝置** | **連接**
--- | --- 
HYPER-V 主機 /vm | HTTPS 上的傳出連接埠 443 連線至 VM 的複寫資料傳送至 Azure Migrate。

  
## <a name="migration-vm-disk-support"></a>移轉 VM 的磁碟支援 

**支援** | **詳細資料**
--- | ---
已移轉的磁碟 | Vm 只能移轉至 Azure 中的受控磁碟 (標準 HHD，進階 SSD)。
   

## <a name="next-steps"></a>後續步驟

[準備 HYPER-V VM 評估](tutorial-prepare-hyper-v.md)進行移轉。




 
