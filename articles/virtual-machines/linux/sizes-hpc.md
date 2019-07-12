---
title: Azure Linux VM 大小 - HPC | Microsoft Docs
description: 列出 Azure 中可用的不同 Linux 高效能運算虛擬機器大小。 列出 vCPU 數目、資料磁碟和 NIC 的相關資訊，以及此服務中各種大小之儲存體輸送量和網路頻寬的相關資訊。
services: virtual-machines-linux
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/12/2018
ms.author: jonbeck
ms.openlocfilehash: 847f25d9be1a8654bbc0435d7874acb0ff793304
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67695589"
---
# <a name="high-performance-compute-virtual-machine-sizes"></a>高效能運算的虛擬機器大小

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


### <a name="mpi"></a>MPI 

SR-IOV 啟用在 Azure 上的 VM 大小所允許的 MPI 用於幾乎任何類別。
在非 SR-IOV 啟用 Vm 上支援 Intel MPI 5.x 版本。 更新版本 （2017年和 2018年） 的 Intel MPI 執行階段程式庫可能會或可能不相容的 Azure Linux RDMA 驅動程式。


### <a name="supported-os-images"></a>支援的作業系統映像
 
Azure Marketplace 有許多 Linux 散發套件支援 RDMA 連線能力：
  
* **CentOS 型 HPC** -非 SR-IOV 啟用 CentOS 型 6.5 版的 Vm，HPC 或更新版本 7.5 最適合。 H 系列 Vm 的建議版本 7.1 以 7.5。 已在 VM 上安裝 RDMA 驅動程式和 Intel MPI 5.1。
  對於 SR-IOV 的 Vm，CentOS HPC 7.6 會隨附最佳化並預先載入的 RDMA 驅動程式與各種 MPI 套件安裝。
  對於其他 RHEL/CentOS VM 映像中，新增 InfiniBandLinux 擴充功能，以啟用 InfiniBand。 這個的 Linux VM 擴充功能安裝 （在 SR-IOV Vm) 上進行 RDMA 連線的 Mellanox OFED 驅動程式。 下列 PowerShell cmdlet 會在現有具備 RDMA 功能的 VM 上安裝 InfiniBandDriverLinux 延伸模組的最新版本 （1.0 版）。 支援 RDMA 的 VM 會命名為*myVM*並部署在資源群組中名為*myResourceGroup*中*美國西部*區域，如下所示：

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  ```
  或者，VM 延伸模組可以包含在 Azure Resource Manager 範本，以便於部署，使用下列 JSON 項目：
  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverLinux",
  "typeHandlerVersion": "1.0",
  } 
  ```
  
  下列命令中現有的 VM 擴展集的所有處理支援 RDMA 之 Vm 上安裝最新版本 1.0 InfiniBandDriverLinux 擴充功能*myVMSS*部署在資源群組中名為*myResourceGroup*:
  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```
  
  > [!NOTE]
  > 在 CentOS 型 HPC 映像上， **yum** 組態檔中已停用核心更新。 這是因為 Linux RDMA 驅動程式以 RPM 封裝中，散發，而且如果更新核心，驅動程式更新可能無法運作。
  >
  

* **SUSE Linux Enterprise Server** -SLES 12 SP3 for HPC、 SLES 12 SP3 for HPC (Premium)、 SLES 12 SP1 for HPC、 SLES 12 SP1 for HPC (Premium)、 SLES 12 SP4 和 SLES 15。 已在 VM 上安裝 RDMA 驅動程式並散發 Intel MPI 套件。 執行下列命令來安裝 MPI：

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
  
* **Ubuntu** -Ubuntu Server 16.04 LTS、 18.04 LTS。 設定 VM 上的 RDMA 驅動程式，並向 Intel 註冊以下載 Intel MPI：

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]  

  如需啟用設定 MPI 的 InfiniBand 的更多詳細資料請參閱 <<c0> [ 啟用 InfiniBand](../workloads/hpc/enable-infiniband.md)。


### <a name="cluster-configuration-options"></a>叢集組態選項

Azure 提供數個選項來建立 Linux HPC VM 的叢集，而這些 VM 可以使用 RDMA 網路進行通訊，包括： 

* **虛擬機器** - 在相同的可用性設定組中部署支援 RDMA 的 HPC VM (當您使用 Azure Resource Manager 部署模型時)。 如果您使用傳統部署模型，請將 VM 部署在相同的雲端服務中。 

* **虛擬機器擴展集**-在虛擬機器擴展集，請確定您限制為單一放置群組部署。 例如，在 Resource Manager 範本中，將 `singlePlacementGroup` 屬性設定為 `true`。 

* **在虛擬機器之間的 MPI** -如果 MPI 通訊如果虛擬機器 (Vm) 之間所需確保 Vm 會在相同的可用性設定組，或相同的虛擬機器擴展集。

* **Azure CycleCloud** - 在 [Azure CycleCloud](/azure/cyclecloud/) 中建立 HPC 叢集，以在 Linux 節點上執行 MPI 作業。

* **Azure Batch** -建立 [Azure Batch](/azure/batch/)集區以在 Linux 計算節點上執行 MPI 工作負載。 如需詳細資訊，請參閱[在 Batch 集區中使用支援 RDMA 或已啟用 GPU 功能的執行個體](../../batch/batch-pool-compute-intensive-sizes.md)。 另請參閱 [Batch Shipyard](https://github.com/Azure/batch-shipyard) 專案，以便在 Batch 上執行以容器為基礎的工作負載。

* **Microsoft HPC Pack** - [HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) 支援讓數個 Linux 散發套件在部署於支援 RDMA 的 Azure VM 中、由 Windows Server 前端節點管理的計算節點上執行。 如需範例部署，請參閱[在 Azure 中建立 HPC Pack Linux RDMA 叢集](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam)。


### <a name="network-considerations"></a>網路考量事項
* 在非-SR-IOV，啟用 RDMA 的 Linux Vm 在 Azure 中，eth1 會保留給 RDMA 網路流量。 請勿變更任何 eth1 設定或參考到此網路組態檔中的任何資訊。
* 在 SR-IOV 啟用 Vm （HB 和 HC 系列），ib0 會保留給 RDMA 網路流量。
* Azure 中的 RDMA 網路會保留位址空間 172.16.0.0/16。 若要在 Azure 虛擬網路中已部署的執行個體上執行 MPI 應用程式，請確定虛擬網路位址空間不會與 RDMA 網路重疊。
* 視您選擇的叢集管理工具而定，可能需要額外系統設定才能執行 MPI 作業。 例如，在叢集上的 Vm，您可能需要建立的叢集節點之間的信任，產生 SSH 金鑰，或建立無密碼 SSH 登入。


## <a name="other-sizes"></a>其他大小
- [一般用途](sizes-general.md)
- [計算最佳化](sizes-compute.md)
- [記憶體最佳化](sizes-memory.md)
- [儲存體最佳化](sizes-storage.md)
- [GPU](../windows/sizes-gpu.md)
- [前幾代](sizes-previous-gen.md)

## <a name="next-steps"></a>後續步驟

- 深入了解如何設定、 最佳化及調整[HPC 工作負載](../workloads/hpc/configure.md)在 Azure 上。
- 深入了解 [Azure 計算單位 (ACU)](acu.md) 如何協助您比較各個 Azure SKU 的計算效能。
