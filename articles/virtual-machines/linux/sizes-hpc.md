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
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/12/2018
ms.author: jonbeck
ms.openlocfilehash: ee99869c2b7a7b3ab38fdd9eae0687862ea53819
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100869"
---
# <a name="high-performance-compute-virtual-machine-sizes"></a>高效能運算的虛擬機器大小

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


### <a name="mpi"></a>MPI 

Azure 上已啟用 SR-IOV 的 VM 大小幾乎可讓您使用任何 MPI 的類別。
在未啟用 SR-IOV 的 Vm 上, 只支援 Intel MPI 5.x 版本。 Intel MPI 執行時間程式庫的較新版本 (2017、2018) 可能會與 Azure Linux RDMA 驅動程式不相容。


### <a name="supported-os-images"></a>支援的作業系統映像
 
Azure Marketplace 有許多支援 RDMA 連線能力的 Linux 散發套件:
  
* **CentOS 為基礎的 hpc** -針對非 sr-iov 啟用的 Vm, CentOS 型版本 6.5 HPC 或更新版本, 最多可達7.5。 針對 H 系列 Vm, 建議使用7.1 到7.5 版。 已在 VM 上安裝 RDMA 驅動程式和 Intel MPI 5.1。
  針對 SR-IOV Vm, CentOS-HPC 7.6 已針對 RDMA 驅動程式和安裝的各種 MPI 套件進行優化和預先載入。
  若為其他 RHEL/CentOS VM 映射, 請新增 InfiniBandLinux 延伸模組以啟用 [未使用]。 此 Linux VM 擴充功能會安裝適用于 RDMA 連線的 Mellanox OFED 驅動程式 (在 SR-IOV Vm 上)。 下列 PowerShell Cmdlet 會在現有具備 RDMA 功能的 VM 上安裝 InfiniBandDriverLinux 延伸模組的最新版本 (版本 1.0)。 具備 RDMA 功能的 VM 會命名為*myVM* , 並部署在*美國西部*區域中名為*myResourceGroup*的資源群組中, 如下所示:

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  ```
  或者, 您可以將 VM 擴充功能包含在 Azure Resource Manager 範本中, 以便使用下列 JSON 元素進行輕鬆部署:
  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverLinux",
  "typeHandlerVersion": "1.0",
  } 
  ```
  
  下列命令會在名為*myResourceGroup*的資源群組中名為*MYVMSS*的現有 VM 擴展集內的所有支援 RDMA 的 vm 上, 安裝最新版本 1.0 InfiniBandDriverLinux 延伸模組:
  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```
  
  > [!NOTE]
  > 在 CentOS 型 HPC 映像上， **yum** 組態檔中已停用核心更新。 這是因為 Linux RDMA 驅動程式是以 RPM 套件的形式散發, 如果核心已更新, 驅動程式更新可能無法正常執行。
  >
  

* **SUSE Linux Enterprise Server** -SLES 12 SP3 for HPC、SLES 12 SP3 for Hpc (premium)、SLES 12 SP1 for HPC、SLES 12 SP1 for Hpc (Premium)、SLES 12 SP4 和 sles 15。 已在 VM 上安裝 RDMA 驅動程式並散發 Intel MPI 套件。 執行下列命令來安裝 MPI：

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
  
* **Ubuntu** -ubuntu SERVER 16.04 LTS、18.04 LTS。 設定 VM 上的 RDMA 驅動程式，並向 Intel 註冊以下載 Intel MPI：

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]  

  如需有關啟用「未[使用](../workloads/hpc/enable-infiniband.md)」的詳細資訊, 請參閱設定 MPI。


### <a name="cluster-configuration-options"></a>叢集組態選項

Azure 提供數個選項來建立 Linux HPC VM 的叢集，而這些 VM 可以使用 RDMA 網路進行通訊，包括： 

* **虛擬機器** - 在相同的可用性設定組中部署支援 RDMA 的 HPC VM (當您使用 Azure Resource Manager 部署模型時)。 如果您使用傳統部署模型，請將 VM 部署在相同的雲端服務中。 

* **虛擬機器擴展集**-在虛擬機器擴展集中, 請確定您將部署限制為單一放置群組。 例如，在 Resource Manager 範本中，將 `singlePlacementGroup` 屬性設定為 `true`。 

* **虛擬機器之間的 mpi** -如果虛擬機器 (vm) 之間需要 mpi 通訊, 請確定 vm 位於相同的可用性設定組或虛擬機器相同的擴展集內。

* **Azure CycleCloud** - 在 [Azure CycleCloud](/azure/cyclecloud/) 中建立 HPC 叢集，以在 Linux 節點上執行 MPI 作業。

* **Azure Batch** -建立 [Azure Batch](/azure/batch/)集區以在 Linux 計算節點上執行 MPI 工作負載。 如需詳細資訊，請參閱[在 Batch 集區中使用支援 RDMA 或已啟用 GPU 功能的執行個體](../../batch/batch-pool-compute-intensive-sizes.md)。 另請參閱 [Batch Shipyard](https://github.com/Azure/batch-shipyard) 專案，以便在 Batch 上執行以容器為基礎的工作負載。

* **Microsoft HPC Pack** - [HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) 支援讓數個 Linux 散發套件在部署於支援 RDMA 的 Azure VM 中、由 Windows Server 前端節點管理的計算節點上執行。 如需範例部署，請參閱[在 Azure 中建立 HPC Pack Linux RDMA 叢集](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam)。


### <a name="network-considerations"></a>網路考量事項
* 在非 SR-IOV 的 Azure 中啟用 RDMA 的 Linux Vm 上, eth1 會保留給 RDMA 網路流量。 請勿變更任何 eth1 設定或參考此網路之設定檔中的任何資訊。
* 在啟用 SR-IOV 的 Vm (HB 和 HC 系列) 上, ib0 會保留給 RDMA 網路流量。
* Azure 中的 RDMA 網路會保留位址空間 172.16.0.0/16。 若要在 Azure 虛擬網路中已部署的執行個體上執行 MPI 應用程式，請確定虛擬網路位址空間不會與 RDMA 網路重疊。
* 視您選擇的叢集管理工具而定，可能需要額外系統設定才能執行 MPI 作業。 例如, 在 Vm 叢集上, 您可能需要藉由產生 SSH 金鑰或建立無密碼 SSH 登入, 在叢集節點之間建立信任。


## <a name="other-sizes"></a>其他大小
- [一般用途](sizes-general.md)
- [計算最佳化](sizes-compute.md)
- [記憶體最佳化](sizes-memory.md)
- [儲存體最佳化](sizes-storage.md)
- [GPU](../windows/sizes-gpu.md)
- [前幾代](sizes-previous-gen.md)

## <a name="next-steps"></a>後續步驟

- 深入瞭解如何在 Azure 上設定、優化和調整[HPC 工作負載](../workloads/hpc/configure.md)。
- 深入了解 [Azure 計算單位 (ACU)](acu.md) 如何協助您比較各個 Azure SKU 的計算效能。
