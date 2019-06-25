---
title: Azure 支援第 2 代 vm （預覽） |Microsoft Docs
description: 第 2 代 vm 的 Azure 支援的概觀
services: virtual-machines-linux
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/23/2019
ms.author: lahugh
ms.openlocfilehash: 352df275742c38307065252d2f65bb4253d78e5d
ms.sourcegitcommit: 6e6813f8e5fa1f6f4661a640a49dc4c864f8a6cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/17/2019
ms.locfileid: "67151266"
---
# <a name="support-for-generation-2-vms-preview-on-azure"></a>支援的層代 2 部 Vm （預覽） 在 Azure 上

> [!IMPORTANT]
> 第 2 代 vm 的 azure 支援目前處於預覽狀態。 此預覽版不提供的服務等級協定，且不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 <<c0> [ 使用 Microsoft Azure 預覽補充規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 

第 2 代虛擬機器 (Vm) 現已支援在 Azure 中預覽。 您無法變更虛擬機器的世代，建立它之後，需要檢閱的考量，在此頁面上，選擇某個層代之前。 

第 2 代 Vm 支援的第 1 代 Vm 不支援的主要功能。 這些功能包括增加的記憶體，Intel 軟體防護擴充功能 (Intel SGX)，以及虛擬化的持續性記憶體 (vPMEM)。 第 2 代 Vm 也有一些尚不支援在 Azure 中的功能。 如需詳細資訊，請參閱 <<c0> [ 特性與功能](#features-and-capabilities)一節。

第 2 代 Vm 會使用新的 (UEFI） 型開機架構，而不是 BIOS 架構使用層代 1 的 Vm。 相較於第 1 代 Vm，第 2 代 Vm 可能已有改善開機和安裝時間。 如需第 2 代 Vm 的概觀和一些第 1 代和第 2 代之間的差異，請參閱[應該在 HYPER-V 中建立 1 或 2 代虛擬機器？](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)。

## <a name="generation-2-vm-sizes"></a>第 2 代 VM 大小

在 Azure 中的所有 VM 大小都支援第 1 代 Vm。 Azure 現在提供預覽下列選取的 VM 系列的第 2 代支援：

* [Dsv2 系列](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dsv2-series)和[Dsv3 系列](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dsv3-series-1)
* [Esv3 系列](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#esv3-series)
* [Fsv2 系列](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-compute#fsv2-series-1)
* [GS-series](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#gs-series)
* [Ls 系列](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-previous-gen#ls-series)和[Lsv2 系列](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-storage#lsv2-series)
* [Mv2-series](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#mv2-series)

## <a name="generation-2-vm-images-in-azure-marketplace"></a>Azure Marketplace 中的第 2 代 VM 映像

第 2 代 Vm 支援下列 Marketplace 映像：

* Windows Server 2019 Datacenter
* Windows Server 2016 Datacenter
* Windows Server 2012 R2 Datacenter
* Windows Server 2012 Datacenter

## <a name="on-premises-vs-azure-generation-2-vms"></a>在內部部署環境與Azure 第 2 代 Vm

Azure 目前不支援的一些功能，內部部署 HYPER-V 支援第 2 代 vm。

| 第 2 代功能                | 在內部部署 Hyper-v | Azure |
|-------------------------------------|---------------------|-------|
| 安全開機                         | :heavy_check_mark:  | :x:   |
| 受防護的 VM                         | :heavy_check_mark:  | :x:   |
| vTPM                                | :heavy_check_mark:  | :x:   |
| 虛擬化型安全性 (VBS) | :heavy_check_mark:  | :x:   |
| VHDX 格式                         | :heavy_check_mark:  | :x:   |

## <a name="features-and-capabilities"></a>特色與功能

### <a name="generation-1-vs-generation-2-features"></a>第 1 代和第 2 代功能比較

| 功能 | 第 1 代 | 第 2 代 |
|---------|--------------|--------------|
| 開機             | PCAT                      | UEFI                               |
| 磁碟控制卡 | IDE                       | SCSI                               |
| VM 大小         | 所有 VM 大小 | 支援進階儲存體的 Vm |

### <a name="generation-1-vs-generation-2-capabilities"></a>第 1 代和第 2 代功能比較

| 功能 | 第 1 代 | 第 2 代 |
|------------|--------------|--------------|
| OS 磁碟 > 2 TB                    | :x:                        | :heavy_check_mark: |
| 自訂磁碟/映像/交換 OS         | :heavy_check_mark:         | :heavy_check_mark: |
| 虛擬機器擴展集支援 | :heavy_check_mark:         | :heavy_check_mark: |
| ASR/backup                        | :heavy_check_mark:         | :x:                |
| 共用映像庫              | :heavy_check_mark:         | :x:                |
| Azure 磁碟加密             | :heavy_check_mark:         | :x:                |

## <a name="creating-a-generation-2-vm"></a>建立第 2 代 VM

### <a name="marketplace-image"></a>Marketplace 映像

在 Azure 入口網站或 Azure CLI 中，您可以建立層代 2 部 Vm 的 Marketplace 映像，支援 UEFI 開機。

`windowsserver-gen2preview`供應項目包含 Windows 第 2 代映像。 此封裝可避免混淆第 1 代和第 2 代映像。 若要建立第 2 代 VM，選取**映像**從此提供並遵循標準程序來建立 VM。

目前，Marketplace 提供下列 Windows 層代 2 的映像：

* 2019-datacenter-gen2
* 2016-datacenter-gen2
* 2012-r2-datacenter-gen2
* 2012-datacenter-gen2

請參閱[特性與功能](#features-and-capabilities)支援的 Marketplace 映像的最新清單一節。

### <a name="managed-image-or-managed-disk"></a>受管理的映像或受控的磁碟

您可以建立第 2 代 VM 的受控映像或受控的磁碟相同的方式，您會建立從第 1 代 VM。

### <a name="virtual-machine-scale-sets"></a>虛擬機器擴展集

您也可以建立層代 2 部 Vm 使用虛擬機器擴展集。 在 Azure CLI 中，使用 Azure 擴展集來建立層代 2 部 Vm。

## <a name="frequently-asked-questions"></a>常見問題集

* **會產生 2 個 Vm 可在所有 Azure 區域？**  
    是。 但並非全部[第 2 代 VM 大小](#generation-2-vm-sizes)都是在每個區域。 產生的 VM 大小的可用性取決於 2 個 VM 的可用性。

* **是否有價格之間的差異層代 1 和層代 2 部 Vm？**  
    沒有。

* **如何增加，作業系統磁碟大小？**  
  OS 磁碟大於 2 TB 是新手第 2 代 Vm。 根據預設，作業系統磁碟是小於 2 TB，第 2 代 vm。 您可以增加磁碟大小最多 4 TB 的建議。 使用 Azure CLI 或 Azure 入口網站來增加的 OS 磁碟大小。 如需如何以程式設計方式擴充磁碟資訊，請參閱[調整磁碟大小](expand-disks.md)。

  若要增加從 Azure 入口網站將 OS 磁碟大小：

  1. 在 Azure 入口網站中，移至 VM 的 [屬性] 頁面。
  1. 若要關閉並解除配置 VM，選取**停止** 按鈕。
  1. 在 **磁碟**區段中，選取您想要增加的 OS 磁碟。
  1. 在 **磁碟**區段中，選取**組態**，並更新**大小**您想要的值。
  1. 返回至 VM 的 [屬性] 頁面並**啟動**VM。

  您可能會看到大於 2 TB 的 OS 磁碟的警告。 此警告不適用於第 2 代 Vm。 不過，大於 4 TB 的 OS 磁碟大小是*不建議這樣做。*

* **執行層代 2 的 Vm 支援加速網路？**  
    是。 如需詳細資訊，請參閱 <<c0> [ 建立具有加速網路的 VM](../../virtual-network/create-vm-accelerated-networking-cli.md)。

* **是否支援第 2 代 VHDX？**  
    否，第 2 代 Vm 支援只 VHD。

* **第 2 代 Vm 支援 Azure 強力的磁碟儲存體？**  
    是。

* **我可以移轉的 VM 從層代 1 到層代 2 嗎？**  
    否，您無法在建立後變更 VM 世代。 如果您需要在 VM 層代之間切換，建立新的 VM 不同的層代。

## <a name="next-steps"></a>後續步驟

* 深入了解[在 HYPER-V 中的第 2 代虛擬機器](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)。
