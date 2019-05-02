---
title: Azure Linux VM 大小 - HPC | Microsoft Docs
description: 列出 Azure 中可用的不同 Linux 高效能運算虛擬機器大小。 列出 vCPU 數目、資料磁碟和 NIC 的相關資訊，以及此服務中各種大小之儲存體輸送量和網路頻寬的相關資訊。
services: virtual-machines-linux
documentationcenter: ''
author: jonbeck7
manager: jeconnoc
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
ms.openlocfilehash: 44b965bd60d976d4d28dc5e31d78a1c838d4ee02
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2019
ms.locfileid: "64704671"
---
# <a name="high-performance-compute-virtual-machine-sizes"></a>高效能運算的虛擬機器大小

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


### <a name="mpi"></a>MPI 

只支援 Intel MPI 5.x 版本。

> [!NOTE]
> 更新版本 （2017年和 2018年） 的 Intel MPI 執行階段程式庫可能會或可能不相容的 Azure Linux RDMA 驅動程式。

### <a name="distributions"></a>散發
 
從 Azure Marketplace 中支援 RDMA 連線的其中一個映像，部署計算密集的 VM：
  
* **Ubuntu**：Ubuntu Server 16.04 LTS。 設定 VM 上的 RDMA 驅動程式，並向 Intel 註冊以下載 Intel MPI：

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* **SUSE Linux Enterprise Server**：SLES 12 SP3 for HPC、SLES 12 SP3 for HPC (Premium)、SLES 12 SP1 for HPC、SLES 12 SP1 for HPC (Premium)。 已在 VM 上安裝 RDMA 驅動程式並散發 Intel MPI 套件。 執行下列命令來安裝 MPI：

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
    
* **以 CentOS 為基礎的 HPC** - 以 CentOS 為基礎的 6.5 HPC 或更新版本 (針對 H 系列，建議使用 7.1 版或更新版本)。 已在 VM 上安裝 RDMA 驅動程式和 Intel MPI 5.1。  
 
  > [!NOTE]
  > 在 CentOS 型 HPC 映像上， **yum** 組態檔中已停用核心更新。 這是因為 Linux RDMA 驅動程式以 RPM 封裝散發，如果更新核心，驅動程式更新可能無法運作。
  > 
 
### <a name="cluster-configuration-options"></a>叢集組態選項

Azure 提供數個選項來建立 Linux HPC VM 的叢集，而這些 VM 可以使用 RDMA 網路進行通訊，包括： 

* **虛擬機器** - 在相同的可用性設定組中部署支援 RDMA 的 HPC VM (當您使用 Azure Resource Manager 部署模型時)。 如果您使用傳統部署模型，請將 VM 部署在相同的雲端服務中。 

* **虛擬機器擴展集** - 在 VM 擴展集中，確定您將部署限制為單一放置群組。 例如，在 Resource Manager 範本中，將 `singlePlacementGroup` 屬性設定為 `true`。 

* **Azure CycleCloud** - 在 [Azure CycleCloud](/azure/cyclecloud/) 中建立 HPC 叢集，以在 Linux 節點上執行 MPI 作業。

* **Azure Batch** -建立 [Azure Batch](/azure/batch/)集區以在 Linux 計算節點上執行 MPI 工作負載。 如需詳細資訊，請參閱[在 Batch 集區中使用支援 RDMA 或已啟用 GPU 功能的執行個體](../../batch/batch-pool-compute-intensive-sizes.md)。 另請參閱 [Batch Shipyard](https://github.com/Azure/batch-shipyard) 專案，以便在 Batch 上執行以容器為基礎的工作負載。

* **Microsoft HPC Pack** - [HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) 支援讓數個 Linux 散發套件在部署於支援 RDMA 的 Azure VM 中、由 Windows Server 前端節點管理的計算節點上執行。 如需範例部署，請參閱[在 Azure 中建立 HPC Pack Linux RDMA 叢集](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam)。

視您選擇的叢集管理工具而定，可能需要額外系統設定才能執行 MPI 作業。 例如，在 VM 叢集上，您可能需要產生 SSH 金鑰或建立無密碼 SSH 信任，以建立叢集節點之間的信任。

### <a name="network-topology-considerations"></a>網路拓撲考量
* 在 Azure 中具備 RDMA 功能的 Linux VM 上，Eth1 會保留給 RDMA 網路流量使用。 請勿變更任何 Eth1 設定或參考到此網路之組態檔中的任何資訊。 Eth0 會保留給一般 Azure 網路流量。

* Azure 中的 RDMA 網路會保留位址空間 172.16.0.0/16。 




## <a name="other-sizes"></a>其他大小
- [一般用途](sizes-general.md)
- [计算优化](sizes-compute.md)
- [記憶體最佳化](sizes-memory.md)
- [儲存體最佳化](sizes-storage.md)
- [GPU](../windows/sizes-gpu.md)
- [前幾代](sizes-previous-gen.md)

## <a name="next-steps"></a>後續步驟

- 深入了解 [Azure 計算單位 (ACU)](acu.md) 如何協助您比較各個 Azure SKU 的計算效能。




