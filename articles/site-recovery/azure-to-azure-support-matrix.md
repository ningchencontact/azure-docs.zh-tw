---
title: 使用 Azure Site Recovery 的 Azure 區域之間的 Azure Vm 的災害復原的支援矩陣 |Microsoft Docs
description: 摘要說明必要條件和支援災害復原 Azure Vm 從一個區域到另一個使用 Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: 928d741132623bd92dae1097724295691d7f3808
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/30/2019
ms.locfileid: "66398341"
---
# <a name="support-matrix-for-replicating-azure-vms-from-one-region-to-another"></a>將 Azure VM 從一個區域複寫至另一個區域的支援矩陣

當您設定的災害復原 Azure Vm 從一個 Azure 區域到另一個，並使用此文章摘要說明其支援和必要條件[Azure Site Recovery](site-recovery-overview.md)服務。


## <a name="deployment-method-support"></a>部署方法的支援

**部署** |  **支援**
--- | ---
**Azure 入口網站** | 支援。
**PowerShell** | 支援。 [深入了解](azure-to-azure-powershell.md)
**REST API** | 支援。
**CLI** | 目前不支援


## <a name="resource-support"></a>資源支援

**資源動作** | **詳細資料**
--- | --- | ---
**資源群組間移動保存庫** | 不支援
**跨資源群組移動計算/儲存體/網路資源** | 不支援。<br/><br/> 如果您在 VM 複寫之後移動 VM 或是相關聯的元件 (例如儲存體/網路)，您必須停用該 VM 的複寫，然後再重新啟用複寫。
**將 Azure VM 從某個訂用帳戶複寫至另一個以進行災害復原** | 在相同的 Azure Active Directory 租用戶中支援。
**在支援的地理叢集 (在訂用帳戶內或跨訂用帳戶) 內移轉區域之間的 VM** | 在相同的 Azure Active Directory 租用戶中支援。
**在相同區域內移轉 VM** | 不支援。

## <a name="region-support"></a>區域支援

您可以複製和復原相同的地理叢集內任何兩個區域之間的 VM。 地理叢集會是以資料延遲及主權範圍定義而成。


**地理叢集** | **Azure 區域**
-- | --
美洲 | 加拿大東部、加拿大中部、美國中南部、美國中西部、美國東部、美國東部 2、美國西部、美國西部 2、美國中部、美國中北部
歐洲 | 英國西部、 英國南部、 北歐、 西歐、 法國中部、 法國南部、 南非西部、 南非北部
亞洲 | 印度南部、印度中部、東南亞、東亞、日本東部、日本西部、南韓中部、南韓南部
澳大利亞   | 澳大利亞東部、澳大利亞東南部、澳大利亞中部、澳大利亞中部 2
Azure Government    | US Gov 維吉尼亞州、US Gov 愛荷華州、US Gov 亞利桑那州、US Gov 德克薩斯州、US DoD 東部、US DoD 中部
德國 | 德國中部、德國東北部
中國 | 中國東部、中國北部、中國北部 2、中國東部 2

>[!NOTE]
>
> - 針對**巴西南部**，您可以複寫並容錯移轉至這些區域：美國中南部、 美國中西部、 美國東部、 美國東部 2、 美國西部、 美國西部 2 和 North Central US。
> - 巴西南部只可用來當做來源區域的 Vm 可以複寫使用 Site Recovery。 它無法做為目標區域。 這是因為延遲問題，因為地理距離。
> - 您可以使用您擁有適當的存取權的區域內。
> - 如果未顯示您要在其中建立保存庫的區域，請確定您的訂用帳戶具有在該區域中建立資源的存取。
> - 如果您啟用複寫時，您無法看到地理叢集內的區域，，請確定您的訂用帳戶具有在該區域中建立 Vm 的權限。



## <a name="cache-storage"></a>快取儲存體

下表摘要說明複寫期間 Site Recovery 使用的快取儲存體帳戶支援。

**設定** | **支援** | **詳細資料**
--- | --- | ---
一般用途 V2 儲存體帳戶 (經常性存取層和非經常性存取層) | 不支援。 | 快取儲存體中存有限制，因為 V2 的交易成本明顯高於 V1 儲存體帳戶。
適用於虛擬網路的 Azure 儲存體防火牆  | 支援 | 如果您使用啟用防火牆的快取儲存體帳戶或目標儲存體帳戶，請確定您[「允許信任的 Microsoft 服務」](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)。


## <a name="replicated-machine-operating-systems"></a>複寫的機器作業系統

Site Recovery 可對執行本節所列作業系統的 Azure VM 進行複寫。

### <a name="windows"></a> Windows

**作業系統** | **詳細資料**
--- | ---
Windows Server 2019 |
Windows Server 2016  | 伺服器核心、含有桌面體驗的伺服器
Windows Server 2012 R2 |
Windows Server 2012 |
Windows Server 2008 R2 | 執行 SP1 或更新版本
Windows 10 (x64) |
Windows 8.1 (x64) |
Windows 8 (x64) |
Windows 7 (x64) | 執行 SP1 或更新版本 （Windows 7 RTM 不支援）

#### <a name="linux"></a> Linux

**作業系統** | **詳細資料**
--- | ---
Red Hat Enterprise Linux | 6.7、6.8、6.9、6.10、7.0、7.1、7.2、7.3、7.4、7.5、7.6  
CentOS | 6.5、6.6、6.7、6.8、6.9、6.10、7.0、7.1、7.2、7.3、7.4、7.5、7.6
Ubuntu 14.04 LTS 伺服器 | [支援的核心版本](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Ubuntu 16.04 LTS 伺服器 | [支援的核心版本](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> 在容錯移轉 （取決於 cloudinit 組態） 的停用，以使用密碼型驗證和登入，Ubuntu 伺服器和雲端 init 封裝來設定雲端的 Vm，可能會有密碼型登。 密碼型登入即可重新啟用虛擬機器上重設密碼，從支援 > 疑難排解 > （在容錯移轉之 VM 在 Azure 入口網站中 [設定] 功能表。
Debian 7 | [支援的核心版本](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 8 | [支援的核心版本](#supported-debian-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 12 | SP1、 SP2、 SP3、 SP4。 [(支援的核心版本)](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 11 | SP3<br/><br/> 不支援將複寫機器從 SP3 升級至 SP4。 如果已升級複寫的機器，您需要在升級後停用複寫，然後再重新啟用複寫。
SUSE Linux Enterprise Server 11 | SP4
Oracle Linux | 6.4、6.5、6.6、6.7、6.8、6.9、6.10、7.0、7.1、7.2、7.3、7.4、7.5 <br/><br/> 執行 Red Hat 相容核心或 Unbreakable Enterprise Kernel 第 3 版 (UEK3)。


#### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Azure 虛擬機器支援的 Ubuntu 核心版本

**版本** | **行動服務版本** | **核心版本** |
--- | --- | --- |
14.04 LTS | 9.24 | 3.13.0-167-generic 的 3.13.0-24-generic<br/>3.16.0-25-generic 至 3.16.0-77-generic、<br/>3.19.0-18-generic 至 3.19.0-80-generic、<br/>4.2.0-18-generic 至 4.2.0-42-generic、<br/>4.4.0-143-generic 的 4.4.0-21-generic<br/>若要 4.15.0-1040-azure 4.15.0-1023-azure |
14.04 LTS | 9.23 | 3.13.0-165-generic 的 3.13.0-24-generic<br/>3.16.0-25-generic 至 3.16.0-77-generic、<br/>3.19.0-18-generic 至 3.19.0-80-generic、<br/>4.2.0-18-generic 至 4.2.0-42-generic、<br/>4.4.0-142-generic 的 4.4.0-21-generic<br/>若要 4.15.0-1037-azure 4.15.0-1023-azure |
14.04 LTS | 9.22 | 3.13.0-24-generic 至 3.13.0-164-generic、<br/>3.16.0-25-generic 至 3.16.0-77-generic、<br/>3.19.0-18-generic 至 3.19.0-80-generic、<br/>4.2.0-18-generic 至 4.2.0-42-generic、<br/>4.4.0-21-generic 至 4.4.0-140-generic、<br/>4.15.0-1023-azure 至 4.15.0-1036-azure |
14.04 LTS | 9.21 | 3.13.0-24-generic 至 3.13.0-163-generic、<br/>3.16.0-25-generic 至 3.16.0-77-generic、<br/>3.19.0-18-generic 至 3.19.0-80-generic、<br/>4.2.0-18-generic 至 4.2.0-42-generic、<br/>4.4.0-21-generic 至 4.4.0-140-generic、<br/>4.15.0-1023-azure 至 4.15.0-1035-azure |
|||
16.04 LTS | 9.24 | 4.4.0-143-generic 的 4.4.0-21-generic<br/>4.8.0-34-generic 至 4.8.0-58-generic、<br/>4.10.0-14-generic 至 4.10.0-42-generic、<br/>4.11.0-13-generic 至 4.11.0-14-generic、<br/>4.13.0-16-generic 至 4.13.0-45-generic、<br/>若要 4.15.0-46-generic 4.15.0-13-generic<br/>4.11.0-1018-azure 的 4.11.0-1009-azure<br/>4.13.0-1005-azure 至 4.13.0-1018-azure <br/>若要 4.15.0-1040-azure 4.15.0-1012-azure|
16.04 LTS | 9.23 | 4.4.0-142-generic 的 4.4.0-21-generic<br/>4.8.0-34-generic 至 4.8.0-58-generic、<br/>4.10.0-14-generic 至 4.10.0-42-generic、<br/>4.11.0-13-generic 至 4.11.0-14-generic、<br/>4.13.0-16-generic 至 4.13.0-45-generic、<br/>若要 4.15.0-45-generic 4.15.0-13-generic<br/>4.11.0-1009-azure 至 4.11.0-1016-azure、<br/>4.13.0-1005-azure 至 4.13.0-1018-azure <br/>若要 4.15.0-1037-azure 4.15.0-1012-azure|
16.04 LTS | 9.22 | 4.4.0-21-generic 至 4.4.0-140-generic、<br/>4.8.0-34-generic 至 4.8.0-58-generic、<br/>4.10.0-14-generic 至 4.10.0-42-generic、<br/>4.11.0-13-generic 至 4.11.0-14-generic、<br/>4.13.0-16-generic 至 4.13.0-45-generic、<br/>4.15.0-13-generic 至 4.15.0-43-generic<br/>4.11.0-1009-azure 至 4.11.0-1016-azure、<br/>4.13.0-1005-azure 至 4.13.0-1018-azure <br/>4.15.0-1012-azure 至 4.15.0-1036-azure|
16.04 LTS | 9.21 | 4.4.0-21-generic 至 4.4.0-140-generic、<br/>4.8.0-34-generic 至 4.8.0-58-generic、<br/>4.10.0-14-generic 至 4.10.0-42-generic、<br/>4.11.0-13-generic 至 4.11.0-14-generic、<br/>4.13.0-16-generic 至 4.13.0-45-generic、<br/>4.15.0-13-generic 至 4.15.0-42-generic<br/>4.11.0-1009-azure 至 4.11.0-1016-azure、<br/>4.13.0-1005-azure 至 4.13.0-1018-azure <br/>4.15.0-1012-azure 至 4.15.0-1035-azure|

#### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Azure 虛擬機器支援的 Debian 核心版本

**版本** | **行動服務版本** | **核心版本** |
--- | --- | --- |
Debian 7 | 9.21,9.22,9.23,9.24 | 3.2.0-4-amd64 至 3.2.0-6-amd64、3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | 9.21, 9.22, 9.23, 9.24 | 3.16.0-4-amd64 至 3.16.0-7-amd64、4.9.0-0.bpo.4-amd64 至 4.9.0-0.bpo.8-amd64 |

#### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>Azure 虛擬機器支援的 SUSE Linux Enterprise Server 12 核心版本

**版本** | **行動服務版本** | **核心版本** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 SP1、 SP2、 SP3 (SP4） | 9.24 | SP1 3.12.49-11-default 至 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default 至 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default 至 4.4.120-92.70-default</br></br>若要 4.4.121-92.104-default SP2(LTSS) 4.4.121-92.73-default</br></br>若要 4.4.176-94.88-default SP3 4.4.73-5-default</br></br>若要 4.12.14-95.13-default SP4 4.12.14-94.41-default |
SUSE Linux Enterprise Server 12 SP1、 SP2、 SP3 (SP4） | 9.23 | SP1 3.12.49-11-default 至 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default 至 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default 至 4.4.120-92.70-default</br></br>若要 4.4.121-92.101-default SP2(LTSS) 4.4.121-92.73-default</br></br>SP3 4.4.73-5-default 至 4.4.162-94.69-default</br></br>若要 4.12.14-95.6-default SP4 4.12.14-94.41-default |
SUSE Linux Enterprise Server 12 (SP1、 SP2 SP3) | 9.22 | SP1 3.12.49-11-default 至 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default 至 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default 至 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default 至 4.4.121-92.98-default</br></br>SP3 4.4.73-5-default 至 4.4.162-94.72-default |
SUSE Linux Enterprise Server 12 (SP1、SP2、SP3) | 9.21 | SP1 3.12.49-11-default 至 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default 至 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default 至 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default 至 4.4.121-92.98-default</br></br>SP3 4.4.73-5-default 至 4.4.162-94.72-default |

## <a name="replicated-machines---linux-file-systemguest-storage"></a>複寫機器 - Linux 檔案系統/客體儲存體

* 檔案系統：ext3、ext4、ReiserFS (僅 Suse Linux Enterprise Server)、XFS、BTRFS
* 磁碟區管理員：LVM2
* 多重路徑軟體：裝置對應程式


## <a name="replicated-machines---compute-settings"></a>複寫的機器 - 計算設定

**設定** | **支援** | **詳細資料**
--- | --- | ---
大小 | 至少 2 顆 CPU 核心和 1 GB RAM 的任何 Azure VM 大小 | 確認 [Azure 虛擬機器大小](../virtual-machines/windows/sizes.md)。
可用性設定組 | 支援 | 如果您啟用使用預設選項為 Azure 虛擬機器的複寫時，可用性設定組會自動建立，根據來源區域設定。 您可以修改這些設定。
可用性區域 | 支援 |
Hybrid Use Benefit (HUB) | 支援 | 如果來源 VM 已啟用 HUB 授權，測試容錯移轉或容錯移轉 VM 也會使用 HUB 授權。
VM 擴展集 | 不支援 |
Azure 資源庫映像 - Microsoft 發行 | 支援 | 只要 VM 在支援的作業系統上執行即支援。
Azure 資源庫映像 - 第三方發行 | 支援 | 只要 VM 在支援的作業系統上執行即支援。
自訂映像 - 第三方發行 | 支援 | 只要 VM 在支援的作業系統上執行即支援。
使用 Site Recovery 移轉 VM | 支援 | 如果使用 Site Recovery 將 VMware VM 或實體機器遷移到 Azure，您需要將機器上執行的舊版行動服務解除安裝，然後重新啟動機器，再複寫到另一個 Azure 區域。
RBAC 原則 | 不支援 | 角色型的存取控制 (RBAC) 原則，在 Vm 上的不會複寫容錯移轉目標區域中的 VM。
擴充功能 | 不支援 | 延伸模組不會複寫容錯移轉目標區域中的 VM。 它必須在容錯移轉之後以手動方式安裝。

## <a name="replicated-machines---disk-actions"></a>複寫的機器 - 磁碟動作

**動作** | **詳細資料**
-- | ---
在複寫的 VM 上調整磁碟大小 | 支援
在複寫的 VM 上新增磁碟 | 不支援。<br/><br/> 您需要停用 VM 的複寫、新增磁碟，然後重新啟用複寫。

## <a name="replicated-machines---storage"></a>複寫的機器 - 儲存體

下表摘要說明 Azure VM OS 磁碟、資料磁碟和暫存磁碟的支援。

- 請務必注意 [Linux](../virtual-machines/linux/disk-scalability-targets.md) 和 [Windows](../virtual-machines/windows/disk-scalability-targets.md) VM 的 VM 磁碟限制和目標，以避免發生任何效能問題。
- 如果您使用預設設定來部署，Site Recovery 會以來源設定作為基礎，自動建立磁碟與儲存體帳戶。
- 如果您使用自訂的設定，請務必遵循指導方針。

**元件** | **支援** | **詳細資料**
--- | --- | ---
OS 磁碟的大小上限 | 2048 GB | [深入了解](../virtual-machines/windows/managed-disks-overview.md) VM 磁碟。
暫存磁碟 | 不支援 | 暫存磁碟一律排除在複寫之外。<br/><br/> 請不要將任何永續性資料儲存於暫存磁碟上。 [深入了解](../virtual-machines/windows/managed-disks-overview.md)。
資料磁碟的大小上限 | 4095 GB |
資料磁碟大小下限 | 針對非受控磁碟沒有限制。 受控磁碟的 2 GB | 
資料磁碟的數目上限 | 最多 64 個 (根據特定的 Azure VM 大小支援) | [深入了解](../virtual-machines/windows/sizes.md) VM 大小。
資料磁碟的變更率 | 進階儲存體的每個磁碟最多 10 MBps。 標準儲存體的每個磁碟最多 2 MBps。 | 如果磁碟的平均資料變更率持續高於最大值，複寫將趕不上進度。<br/><br/>  不過，如果是偶而超過最大值，則複寫可以趕上進度，但您可能會看到稍有延遲的復原點。
資料磁碟 - 標準儲存體帳戶 | 支援 |
資料磁碟 - 進階儲存體帳戶 | 支援 | 如果 VM 的磁碟分散於進階和標準儲存體帳戶，您可以對於各個磁碟選取不同的目標儲存體帳戶，以確保目標區域有相同的儲存體設定。
受控磁碟 - 標準 | 在支援 Azure Site Recovery 的 Azure 區域中會支援。 |
受控磁碟 - 進階 | 在支援 Azure Site Recovery 的 Azure 區域中會支援。 |
標準 SSD | 支援 |
備援性 | 支援 LRS 和 GRS。<br/><br/> 不支援 ZRS。
非經常性和經常性儲存體 | 不支援 | 非經常性和經常性儲存體不支援 VM 磁碟
儲存空間 | 支援 |
待用加密 (SSE) | 支援 | SSE 是儲存體帳戶上的預設設定。   
適用於 Windows OS 的 Azure 磁碟加密 (ADE) | 支援啟用[使用 Azure AD 應用程式加密](https://aka.ms/ade-aad-app)的 VM |
適用於 Linux OS 的 Azure 磁碟加密 (ADE) | 不支援 |
熱新增 | 支援 | 啟用資料磁碟加入至複寫的 Azure VM 的複寫支援使用受控的磁碟的 Vm。
熱移除磁碟 | 不支援 | 如果您移除 VM 上的資料磁碟時，您要停用複寫，然後再次為 VM 啟用複寫。
排除磁碟 | 支援。 您必須使用[Powershell](azure-to-azure-exclude-disks.md)設定。 |  根據預設排除暫存磁碟。
儲存空間直接存取  | 支援損毀一致復原點。 不支援應用程式一致復原點。 |
向外延展檔案伺服器  | 支援損毀一致復原點。 不支援應用程式一致復原點。 |
LRS | 支援 |
GRS | 支援 |
RA-GRS | 支援 |
ZRS | 不支援 |
非經常性和經常性儲存體 | 不支援 | 非經常性和經常性儲存體不支援虛擬機器磁碟
適用於虛擬網路的 Azure 儲存體防火牆  | 支援 | 如果虛擬網路的存取限於儲存體帳戶，啟用[允許信任的 Microsoft 服務](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)。
一般用途 V2 儲存體帳戶 (經常性存取層和非經常性存取層) | 否 | 與一般用途 V1 儲存體帳戶相比，交易成本大幅增加

>[!IMPORTANT]
> 若要避免效能問題，請確定您遵循 VM 磁碟延展性和效能目標[Linux](../virtual-machines/linux/disk-scalability-targets.md)或是[Windows](../virtual-machines/windows/disk-scalability-targets.md) Vm。 如果您使用預設設定，Site Recovery 會建立所需的磁碟和儲存體帳戶，根據來源組態。 如果您自訂並選取您自己的設定，請依照您的來源 Vm 磁碟延展性和效能目標。

## <a name="limits-and-data-change-rates"></a>限制和資料變更率

下表摘要說明 Site Recovery 限制。

- 這些限制會根據我們的測試，但很明顯地不涵蓋所有可能的應用程式 I/O 組合。
- 實際的結果可以根據您的應用程式 I/O 混合而而有所不同。
- 有兩個需要考量，每個磁碟資料變換和每個虛擬機器資料變換限制。
- 例如，如果我們使用下表中所述的進階 P20 磁碟 Site Recovery 可以處理 5 Mb 的每個磁碟，變換與最多五個這類磁碟的每個 VM，因為 25 MB/秒的變換總計每個 VM 限制。

**儲存體目標** | **平均來源磁碟 I/O** |**平均來源磁碟資料變換** | **每日的來源磁碟資料變換總計**
---|---|---|---
標準儲存體 | 8 KB | 2 MB/秒 | 每個磁碟 168 GB
進階 P10 或 P15 磁碟 | 8 KB  | 2 MB/秒 | 每個磁碟 168 GB
進階 P10 或 P15 磁碟 | 16 KB | 4 MB/秒 |  每個磁碟 336 GB
進階 P10 或 P15 磁碟 | 32 KB 或更大 | 8 MB/秒 | 每個磁碟 672 GB
進階 P20、P30、P40 或 P50 磁碟 | 8 KB    | 5 MB/秒 | 每個磁碟 421 GB
進階 P20、P30、P40 或 P50 磁碟 | 16 KB 或更大 |20 MB/秒 | 每個磁碟的 1684 GB

## <a name="replicated-machines---networking"></a>複寫的機器 - 網路
**設定** | **支援** | **詳細資料**
--- | --- | ---
NIC | 針對特定 Azure VM 大小支援的數目上限 | 在容錯移轉期間建立 VM 時，系統會建立 NIC。<br/><br/> 容錯移轉 VM 的 NIC 數目取決於啟用複寫時來源 VM 具有的 NIC 數量。 如果您在啟用複寫後新增或移除 NIC，不會影響容錯移轉後複寫 VM 上的 NIC 數目。 也請注意，在容錯移轉之後的 Nic 順序並不保證會與原始的順序相同。
網際網路負載平衡器 | 支援 | 使用復原方案中的 Azure 自動化指令碼，使預先設定的負載平衡器產生關聯。
內部負載平衡器 | 支援 | 使用復原方案中的 Azure 自動化指令碼，使預先設定的負載平衡器產生關聯。
公用 IP 位址 | 支援 | 將現有公用 IP 位址與 NIC 產生關聯。 或者，建立公用 IP 位址，然後使用復原方案中的 Azure 自動化指令碼讓它與 NIC 產生關聯。
NIC 上的 NSG | 支援 | 使用復原方案中的 Azure 自動化指令碼，使 NSG 與 NIC 產生關聯。
子網路上的 NSG | 支援 | 使用復原方案中的 Azure 自動化指令碼，使 NSG 與子網路產生關聯。
保留 (靜態) IP 位址 | 支援 | 如果來源 VM 上的 NIC 有靜態 IP 位址，而目標子網路有相同的 IP 位址可用，則會將它指派給容錯移轉 VM。<br/><br/> 如果目標子網路沒有相同的 IP 位址，子網路中一個可用的 IP 位址會被保留供 VM 使用。<br/><br/> 您也可以在 [複寫的項目]   > [設定]   > [計算與網路]   > [網路介面]  中指定固定的 IP 位址和子網路。
動態 IP 位址 | 支援 | 如果來源上的 NIC 有動態 IP 位址，則容錯移轉 VM 上的 NIC 預設也是動態。<br/><br/> 如有需要，您可以將此修改為固定的 IP 位址。
多個 IP 位址 | 不支援 | 當您容錯移轉具有多個 IP 位址的 NIC 的 VM 時，則會保留在來源區域中的 nic 只能主要 IP 位址。 若要指派多個 IP 位址，您可以新增 Vm 來[復原計劃](recovery-plan-overview.md)以及附加的指令碼，以將其他 IP 位址指派給計劃，或您可以在容錯移轉之後進行變更以手動方式或使用指令碼。 
流量管理員     | 支援 | 您可以預先設定流量管理員，定期將流量傳輸到來源區域中的端點，如果發生容錯移轉，則傳輸到目標區域中的端點。
Azure DNS | 支援 |
自訂 DNS  | 支援 |
未經驗證的 Proxy | 支援 | [深入瞭解]。(site-recovery-azure-to-azure-networking-guidance.md)   
經驗證的 Proxy | 不支援 | 如果 VM 對於輸出連線能力使用經驗證的 Proxy，則無法使用 Azure Site Recovery 加以複寫。    
在內部部署 VPN 站對站連線<br/><br/>（不論有無 ExpressRoute）| 支援 | 請確定設定 Udr 和 Nsg，站台復原流量不會路由傳送至內部部署的方式。 [深入了解](site-recovery-azure-to-azure-networking-guidance.md)    
VNET 對 VNET 連線 | 支援 | [深入了解](site-recovery-azure-to-azure-networking-guidance.md)  
虛擬網路服務端點 | 支援 | 如果您要限制只有儲存體帳戶可以存取虛擬網路，請確定受信任的 Microsoft 服務可以存取儲存體帳戶。
加速網路 | 支援 | 必須在來源 VM 上啟用加速網路。 [深入了解](azure-vm-disaster-recovery-with-accelerated-networking.md)。



## <a name="next-steps"></a>後續步驟
- 讀取[網路指引](site-recovery-azure-to-azure-networking-guidance.md)針對複寫 Azure Vm。
- 透過[複寫 Azure VM](site-recovery-azure-to-azure.md) 來部署災害復原。
