---
title: 第2代 Vm 的 Azure 支援 (預覽) |Microsoft Docs
description: 第2代 Vm 的 Azure 支援總覽
services: virtual-machines-windows
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 05/23/2019
ms.author: lahugh
ms.openlocfilehash: fd794662ef41112cb04bdfde087253c8abdb6983
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70079375"
---
# <a name="support-for-generation-2-vms-preview-on-azure"></a>在 Azure 上支援第2代 Vm (預覽)

> [!IMPORTANT]
> 第2代 Vm 的 Azure 支援目前為預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供, 不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊, 請參閱[Microsoft Azure 預覽的補充使用](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)規定。

第2代虛擬機器 (Vm) 的支援現已在 Azure 中提供預覽。 建立虛擬機器之後, 您就無法變更其層代, 因此請先參閱此頁面上的考慮, 再選擇世代。 

第2代 Vm 支援第1代 Vm 中不支援的重要功能。 這些功能包括增加的記憶體、Intel 軟體防護延伸模組 (Intel SGX) 和虛擬化的持續性記憶體 (vPMEM)。 第2代 Vm 也有一些在 Azure 中不支援的功能。 如需詳細資訊, 請參閱[特性和功能](#features-and-capabilities)一節。

第2代 Vm 使用新的 UEFI 型開機架構, 而不是第1代 Vm 所使用的 BIOS 架構。 相較于第1代 Vm, 第2代 Vm 可能已改善開機和安裝時間。 如需第2代 Vm 的總覽, 以及第1代與第2代的差異, 請參閱[我應該在 hyper-v 中建立第1代或第2代虛擬機器嗎？](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)。

## <a name="generation-2-vm-sizes"></a>第2代 VM 大小

Azure 中的所有 VM 大小都支援第1代 Vm。 Azure 現在為下列選取的 VM 系列提供預覽第2代支援:

* [B 系列](https://docs.microsoft.com/azure/virtual-machines/windows/b-series-burstable)
* [Dsv2 系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv2-series)和[Dsv3 系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)
* [Esv3 系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#esv3-series)
* [Fsv2 系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-compute#fsv2-series-1)
* [GS 系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-previous-gen#gs-series)
* [Ls 系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-previous-gen#ls-series)和[Lsv2 系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-storage#lsv2-series)
* [Mv2 系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#mv2-series)
* [NCv2 系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu#ncv2-series)和[NCv3 系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu#ncv3-series)
* [ND 系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu#nd-series)

## <a name="generation-2-vm-images-in-azure-marketplace"></a>Azure Marketplace 中的第2代 VM 映射

第2代 Vm 支援下列 Marketplace 映射:

* Windows Server 2019 Datacenter
* Windows Server 2016 Datacenter
* Windows Server 2012 R2 資料中心
* Windows Server 2012 Datacenter

## <a name="on-premises-vs-azure-generation-2-vms"></a>內部部署與Azure 第2代 Vm

Azure 目前不支援內部部署 Hyper-v 針對第2代 Vm 所支援的部分功能。

| 第2代功能                | 內部部署 Hyper-v | Azure |
|-------------------------------------|---------------------|-------|
| 安全開機                         | :heavy_check_mark:  | :x:   |
| 受防護的 VM                         | :heavy_check_mark:  | :x:   |
| vTPM                                | :heavy_check_mark:  | :x:   |
| 以虛擬化為基礎的安全性 (VBS) | :heavy_check_mark:  | :x:   |
| VHDX 格式                         | :heavy_check_mark:  | :x:   |

## <a name="features-and-capabilities"></a>功能

### <a name="generation-1-vs-generation-2-features"></a>層代1與第2代功能

| 功能 | 第 1 代 | 第2代 |
|---------|--------------|--------------|
| 開機             | PCAT                      | UEFI                               |
| 磁碟控制卡 | IDE                       | SCSI                               |
| VM 大小         | 所有 VM 大小 | 只有支援 premium 儲存體的 Vm |

### <a name="generation-1-vs-generation-2-capabilities"></a>層代1和層代2功能

| 功能 | 第 1 代 | 第2代 |
|------------|--------------|--------------|
| OS 磁片 > 2 TB                    | :x:                        | :heavy_check_mark: |
| 自訂磁片/映射/交換 OS         | :heavy_check_mark:         | :heavy_check_mark: |
| 虛擬機器擴展集支援 | :heavy_check_mark:         | :heavy_check_mark: |
| ASR/backup                        | :heavy_check_mark:         | :x:                |
| 共用映像資源庫              | :heavy_check_mark:         | :x:                |
| Azure 磁碟加密             | :heavy_check_mark:         | :x:                |

## <a name="creating-a-generation-2-vm"></a>建立第2代 VM

### <a name="marketplace-image"></a>Marketplace 映射

在 [Azure 入口網站] 或 [Azure CLI] 中, 您可以從支援 UEFI 開機的 Marketplace 映射建立第2代 Vm。

此`windowsserver-gen2preview`供應專案僅包含 Windows 第2代映射。 此封裝可避免第1代和第2代映射之間的混淆。 若要建立第2代 VM, 請選取此供應專案中的**映射**, 並遵循標準程式來建立 VM。

Marketplace 目前提供下列 Windows 第2代映射:

* 2019-datacenter-gen2
* 2016-datacenter-gen2
* 2012-r2-datacenter-gen2
* 2012-datacenter-gen2

如需目前支援的 Marketplace 映射清單, 請參閱[特性和功能](#features-and-capabilities)一節。

### <a name="managed-image-or-managed-disk"></a>受控映射或受控磁片

您可以使用與建立第1代 VM 相同的方式, 從受控映射或受控磁片建立第2代 VM。

### <a name="virtual-machine-scale-sets"></a>虛擬機器擴展集

您也可以使用虛擬機器擴展集來建立第2代 Vm。 在 Azure CLI 中, 使用 Azure 擴展集來建立第2代 Vm。

## <a name="frequently-asked-questions"></a>常見問題集

* **第2代 Vm 可以在所有 Azure 區域中使用嗎？**  
    是的。 但並非所有層[代 2 VM 大小](#generation-2-vm-sizes)都可以在每個區域中使用。 第2代 VM 的可用性取決於 VM 大小的可用性。

* **第1代和第2代 Vm 之間是否有價格差異？**  
   資料分割

* **如何? 增加 OS 磁片大小嗎？**  
  大於 2 TB 的 OS 磁片是第2代 Vm 的新手。 根據預設, 第2代 Vm 的 OS 磁片小於 2 TB。 您可以將磁片大小增加到最多 4 TB 的建議。 使用 Azure CLI 或 Azure 入口網站來增加 OS 磁片大小。 如需如何以程式設計方式擴充磁片的相關資訊, 請參閱[調整磁片大小](expand-os-disk.md)。

  若要增加 Azure 入口網站的 OS 磁片大小:

  1. 在 Azure 入口網站中, 移至 [VM 屬性] 頁面。
  1. 若要關閉和解除配置 VM, 請選取 [**停止**] 按鈕。
  1. 在 [**磁片**] 區段中, 選取您想要增加的 OS 磁片。
  1. 在 [**磁片**] 區段中, 選取 [設定], 並將**大小**更新為您想要的值。
  1. 回到 VM 的 [屬性] 頁面並**啟動**vm。
  
  對於大於 2 TB 的 OS 磁片, 您可能會看到警告。 此警告不適用於第2代 Vm。 不過,*不建議*大於 4 TB 的 OS 磁片大小。

* **第2代 Vm 是否支援加速網路？**  
    是的。 如需詳細資訊, 請參閱[建立具有加速網路的 VM](../../virtual-network/create-vm-accelerated-networking-cli.md)。

* **第2代支援 VHDX 嗎？**  
    否, 第2代 Vm 僅支援 VHD。

* **第2代 Vm 是否支援 Azure Ultra 磁碟儲存體？**  
    是的。

* **我可以將 VM 從第1代遷移到第2代嗎？**  
    否, 您無法在建立 VM 之後變更其產生。 如果您需要在 VM 層代之間切換, 請建立不同世代的新 VM。

## <a name="next-steps"></a>後續步驟

* 瞭解[hyper-v 中的第2代虛擬機器](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)。

* 瞭解如何[準備 VHD](prepare-for-upload-vhd-image.md)以從內部部署系統上傳至 Azure。
