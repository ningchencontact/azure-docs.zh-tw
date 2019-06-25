---
title: Azure Windows VM 大小 - HPC | Microsoft Docs
description: 列出 Azure 中可用的不同 Windows 高效能運算虛擬機器大小。 列出 vCPU 數目、資料磁碟和 NIC 的相關資訊，以及此服務中各種大小之儲存體輸送量和網路頻寬的相關資訊。
services: virtual-machines-windows
documentationcenter: ''
author: vermagit
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/12/2018
ms.author: jonbeck;amverma
ms.openlocfilehash: ad490084b34a8bf6e89c7feb14d5cd2e70a8138f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66755314"
---
# <a name="high-performance-compute-vm-sizes"></a>高效能運算 VM 大小

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


* **作業系統** - Windows Server 2016、Windows Server 2012 R2、Windows Server 2012

* **MPI** - Microsoft MPI (MS-MPI) 2012 R2 或更新版本、Intel MPI Library 5.x

  在非 SR-IOV 啟用 Vm 上支援的 MPI 實作會使用 Microsoft 網路直接存取 (ND) 介面執行個體之間進行通訊。 SR-IOV 啟用的 VM 大小 （HB 和 HC 系列） 在 Azure 上所允許的 MPI 搭配 Mellanox OFED 幾乎任何版本。 

* **InfiniBandDriverWindows VM 延伸模組**-在支援 RDMA 的 Vm 上將啟用 InfiniBand InfiniBandDriverWindows 擴充功能。 此 Windows VM 延伸模組會安裝 Windows Network Direct 的驅動程式 （非 SR-IOV 在 Vm 上） 或 （在 SR-IOV Vm) 上進行 RDMA 連線的 Mellanox OFED 驅動程式。
在某些部署 A8 和 A9 執行個體中，會自動新增 HpcVmDrivers 擴充功能。 請注意，HpcVmDrivers VM 擴充功能已被取代;它將不會更新。 若要將 VM 擴充功能新增至 VM，您可以使用 [Azure PowerShell](/powershell/azure/overview) Cmdlet。 

  下列命令在現有具備 RDMA 功能的 VM 上安裝最新版本 1.0 InfiniBandDriverWindows 擴充功能*myVM*部署在資源群組中名為*myResourceGroup* 中*美國西部*區域：

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  ```
  或者，VM 延伸模組可以包含在 Azure Resource Manager 範本，以便於部署，使用下列 JSON 項目：
  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverWindows",
  "typeHandlerVersion": "1.0",
  } 
  ```
  
  如需詳細資訊，請參閱[虛擬機器擴充功能和功能](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 您也可以針對已在[傳統部署模型](classic/manage-extensions.md)中部署的 VM 使用擴充功能。

* **RDMA 網路位址空間** - Azure 中的 RDMA 網路會保留位址空間 172.16.0.0/16。 若要在 Azure 虛擬網路中已部署的執行個體上執行 MPI 應用程式，請確定虛擬網路位址空間不會與 RDMA 網路重疊。


### <a name="cluster-configuration-options"></a>叢集組態選項

Azure 提供數個選項來建立 Windows HPC VM 的叢集，而這些 VM 可以使用 RDMA 網路進行通訊，包括： 

* **虛擬機器** - 在相同的可用性設定組中部署支援 RDMA 的 HPC VM (當您使用 Azure Resource Manager 部署模型時)。 如果您使用傳統部署模型，請將 VM 部署在相同的雲端服務中。 

* **虛擬機器擴展集**-在虛擬機器擴展集，請確定您限制為單一放置群組部署。 例如，在 Resource Manager 範本中，將 `singlePlacementGroup` 屬性設定為 `true`。 

* **Azure CycleCloud** - 在 [Azure CycleCloud](/azure/cyclecloud/) 中建立 HPC 叢集，以在 Windows 節點上執行 MPI 作業。

* **Azure Batch** -建立 [Azure Batch](/azure/batch/)集區以在 Windows Server 計算節點上執行 MPI 工作負載。 如需詳細資訊，請參閱[在 Batch 集區中使用支援 RDMA 或已啟用 GPU 功能的執行個體](../../batch/batch-pool-compute-intensive-sizes.md)。 另請參閱 [Batch Shipyard](https://github.com/Azure/batch-shipyard) 專案，以便在 Batch 上執行以容器為基礎的工作負載。

* **Microsoft HPC Pack** - [HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) 包含 MS-MPI 的執行階段環境，此 MS-MPI 若部署在支援 RDMA 的 VM 上，即可使用 Azure RDMA 網路。 如需範例部署，請參閱 [使用 HPC Pack 設定 Windows RDMA 叢集以執行 MPI 應用程式](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。

## <a name="other-sizes"></a>其他大小
- [一般用途](sizes-general.md)
- [計算最佳化](sizes-compute.md)
- [記憶體最佳化](../virtual-machines-windows-sizes-memory.md)
- [儲存體最佳化](../virtual-machines-windows-sizes-storage.md)
- [GPU 最佳化](sizes-gpu.md)
- [前幾代](sizes-previous-gen.md)

## <a name="next-steps"></a>後續步驟

- 如需在 Windows 伺服器上使用大量計算執行個體和 HPC Pack 的檢查清單，請參閱[使用 HPC Pack 設定 Windows RDMA 叢集以執行 MPI 應用程式](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。

- 若要在以 Azure Batch 執行 MPI 應用程式時使用計算密集型執行個體，請參閱[在 Azure Batch 中使用多重執行個體工作來執行訊息傳遞介面 (MPI) 應用程式](../../batch/batch-mpi.md)。

- 深入了解 [Azure 計算單位 (ACU)](acu.md) 如何協助您比較各個 Azure SKU 的計算效能。
