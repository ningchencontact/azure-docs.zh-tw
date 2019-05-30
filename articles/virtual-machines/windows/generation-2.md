---
title: 在 Azure 上的第 2 代 Vm （預覽） |Microsoft Docs
description: Azure 第 2 代 Vm 的概觀
services: virtual-machines-windows
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/23/2019
ms.author: lahugh
ms.openlocfilehash: 1dcc0d3a652ccbf365a18ce734a54dc78515b1a7
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/30/2019
ms.locfileid: "66388358"
---
# <a name="generation-2-vms-preview-on-azure"></a>在 Azure 上的第 2 代 Vm （預覽）

> [!IMPORTANT]
> 第 2 代 Vm 目前處於公開預覽階段。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

第 2 代虛擬機器 (Vm) 現已支援在 Azure 上的公開預覽狀態。 您無法變更它建立之後，虛擬機器的世代。 因此，我們建議您檢閱的考量[此處](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)以及此頁面上選擇的產生之前的資訊。

第 2 代 Vm 支援主要功能，不支援第 1 代 Vm，例如： 增加記憶體，Intel® 軟體防護擴充功能 (SGX) 和虛擬的持續性記憶體 (vPMEM)。 第 2 代 Vm 也有一些尚不支援在 Azure 的功能。 如需詳細資訊，請參閱 <<c0> [ 特性與功能](#features-and-capabilities)一節。

第 2 代 Vm 會使用新的 (UEFI） 型開機架構與第 1 代 Vm 所使用的 BIOS 型架構。 相較於第 1 代 Vm，第 2 代 Vm 可能已改進開機和安裝時間。 如需第 2 代 Vm 的概觀和一些第 1 代和第 2 代的主要差異，請參閱[應該在 HYPER-V 中建立 1 或 2 代虛擬機器？](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)。

## <a name="generation-2-vm-sizes"></a>第 2 代 VM 大小

在 Azure 中的所有 VM 大小都支援第 1 代 Vm。 Azure 現在提供公開預覽下列選取的 VM 系列的第 2 代支援：

* [Dsv2](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv2-series)和[Dsv3 系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)
* [Esv3 系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#esv3-series)
* [Fsv2 系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-compute#fsv2-series-1)
* [GS-series](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#gs-series)
* [Ls 系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-previous-gen#ls-series)和[Lsv2 系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-storage#lsv2-series)
* [Mv2-series](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#mv2-series)

## <a name="generation-2-vm-images-in-azure-marketplace"></a>Azure Marketplace 中的第 2 代 VM 映像

第 2 代 Vm 支援下列 Azure Marketplace 映像：

* Windows Server 2019 Datacenter
* Windows Server 2016 Datacenter
* Windows Server 2012 R2 Datacenter
* Windows Server 2012 Datacenter

## <a name="on-premises-vs-azure-generation-2-vms"></a>在內部部署環境與 Azure 第 2 代 Vm

Azure 目前不支援的一些功能，內部部署 HYPER-V 支援的第 2 代 Vm。

| 第 2 代功能                | 在內部部署 Hyper-v | Azure |
|-------------------------------------|---------------------|-------|
| 安全開機                         | :heavy_check_mark:  | :x:   |
| 受防護的 VM                         | :heavy_check_mark:  | :x:   |
| vTPM                                | :heavy_check_mark:  | :x:   |
| 虛擬化型安全性 (VBS) | :heavy_check_mark:  | :x:   |
| VHDX 格式                         | :heavy_check_mark:  | :x:   |

## <a name="features-and-capabilities"></a>特色與功能

### <a name="generation-1-vs-generation-2-features"></a>第 1 代與第 2 代功能

| 功能 | 第 1 代 | 第 2 代 |
|---------|--------------|--------------|
| 開機             | PCAT                      | UEFI                               |
| 磁碟控制卡 | IDE                       | SCSI                               |
| VM 大小         | 適用於所有 VM 大小 | 僅限進階儲存體 」 支援 Vm |

### <a name="generation-1-vs-generation-2-capabilities"></a>第 1 代與第 2 代功能

| 功能 | 第 1 代 | 第 2 代 |
|------------|--------------|--------------|
| OS 磁碟 > 2 TB                    | :x:                        | :heavy_check_mark: |
| 自訂磁碟/映像/交換 OS         | :heavy_check_mark:         | :heavy_check_mark: |
| 虛擬機器擴展集支援 | :heavy_check_mark:         | :heavy_check_mark: |
| ASR/備份                        | :heavy_check_mark:         | :x:                |
| 共用映像庫              | :heavy_check_mark:         | :x:                |
| Azure 磁碟加密             | :heavy_check_mark:         | :x:                |

## <a name="creating-a-generation-2-vm"></a>建立第 2 代 VM

### <a name="marketplace-image"></a>Marketplace 映像

從 marketplace 映像 （其支援 UEFI 開機） 透過 Azure 入口網站或 Azure CLI，就可以建立第 2 代 Vm。

`windowsserver-gen2preview`供應項目包含 Windows 第 2 代映像。 這可避免混淆，與第 1 代與第 2 代映像。 若要建立層代 2 部 Vm，請選取**映像**從此提供，並遵循標準的 VM 建立程序。

目前，下列的 Windows 層代 2 的映像發佈 Azure Marketplace 中：

* 2019-datacenter-gen2
* 2016-datacenter-gen2
* 2012-r2-datacenter-gen2
* 2012-datacenter-gen2

因為我們會持續新增其他支援第 2 代的映像，請參閱支援的 marketplace 映像清單的功能區段。

### <a name="managed-image-or-managed-disk"></a>受管理的映像或受控的磁碟

層代 2 部 Vm 可以建立從受控映像或受控的磁碟相同的方式，您會建立從第 1 代 VM。

### <a name="virtual-machine-scale-sets"></a>虛擬機器擴展集

2 部 Vm 也可以建立使用虛擬機器擴展集的產生。 您可以建立層代 2 個 Vm 使用 Azure CLI 透過 Azure 虛擬機器擴展集。

## <a name="frequently-asked-questions"></a>常見問題集

* **會產生 2 個 Vm 可在所有 Azure 區域？**  
    [否]。不過，並非所有[第 2 代 VM 大小](#generation-2-vm-sizes)都是在每個區域。 層代 2 部 Vm 所依存的 VM 大小可用性的可用性。

* **是否有價格之間的差異層代 1 和層代 2 部 Vm？**  
    沒有任何差異，在第 1 代和第 2 代 Vm 之間的價格。

* **如何增加，作業系統磁碟大小？**  
  OS 磁碟大於 2 TB 是新手第 2 代 Vm。 根據預設，大部分的作業系統磁碟是第 2 代 vm 不超過 2 TB，但可以增加磁碟大小，以建議的上限為 4 TB。 您可以增加透過 Azure CLI 或 Azure 入口網站將 OS 磁碟大小。 如需有關以程式設計方式擴充磁碟的詳細資訊，請參閱 <<c0> [ 調整磁碟大小](expand-os-disk.md)。

  若要增加透過 Azure 入口網站將 OS 磁碟大小：

  * 瀏覽至 Azure 入口網站上的 [VM 屬性] 頁面。

  * 關閉及解除配置 VM using**停止**在 Azure 入口網站上的按鈕。

  * 在 **磁碟**區段中，選取您想要增加的 OS 磁碟。

  * 選取 **組態**中**磁碟**區段，並更新**大小**所要的值。

  * 瀏覽回到 VM 的 [屬性] 頁面並**啟動**VM。
  
  您可能會看到大於 2 TB 的 OS 磁碟的警告。 警告可能不適用於第 2 代 Vm;不過，大於 4 TB 的 OS 磁碟大小是**不建議這樣做。**

* **第 2 代 Vm 是否支援加速網路？**  
    是的第 2 代 Vm 支援[加速網路](../../virtual-network/create-vm-accelerated-networking-cli.md)。

* **是否支援第 2 代.vhdx？**  
    否，只有.vhd 被支援第 2 代 Vm。

* **第 2 代 Vm 支援超級固態磁碟機 (SSD)？**  
    是，第 2 代 Vm 支援強力的 SSD。

* **我可以移轉從層代 1 到第 2 代 Vm？**  
    否，您無法在建立它之後，變更 VM 世代。 如果您需要在 VM 層代之間切換，您需要建立新的 VM 不同的層代。

## <a name="next-steps"></a>後續步驟

* 深入了解[在 HYPER-V 中的第 2 代虛擬機器](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)。

* 了解如何[準備 VHD](prepare-for-upload-vhd-image.md)從內部部署上傳至 Azure。
