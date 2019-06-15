---
title: 設定 Message Passing Interface for HPC-Azure 虛擬機器 |Microsoft Docs
description: 了解如何針對在 Azure 上的 HPC 設定 MPI 的設定。
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/15/2019
ms.author: amverma
ms.openlocfilehash: 1717c0d95c00e13ae0d87ab920bc88cd4f0df978
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66809838"
---
# <a name="set-up-message-passing-interface-for-hpc"></a>設定 Message Passing Interface for HPC

訊息傳遞介面 (MPI) 工作負載是傳統 HPC 工作負載的重要部分。 SR-IOV 啟用在 Azure 上的 VM 大小所允許的 MPI 用於幾乎任何類別。 

在 Vm 上執行 MPI 作業在租用戶需要設定的資料分割索引鍵 （p-索引鍵）。 請依照下列中的步驟[探索資料分割索引鍵](#discover-partition-keys)區段，如需有關決定 p 鍵的值。

## <a name="ucx"></a>UCX

[UCX](https://github.com/openucx/ucx)提供 IB 且適用於 MPICH 和 OpenMPI 的最佳效能。

```bash
wget https://github.com/openucx/ucx/releases/download/v1.4.0/ucx-1.4.0.tar.gz
tar -xvf ucx-1.4.0.tar.gz
cd ucx-1.4.0
./configure --prefix=<ucx-install-path>
make -j 8 && make install
```

## <a name="openmpi"></a>OpenMPI

安裝 UCX 如先前所述。

```bash
sudo yum install –y openmpi
```

建置 OpenMPI。

```bash
wget https://download.open-mpi.org/release/open-mpi/v4.0/openmpi-4.0.0.tar.gz
tar -xvf openmpi-4.0.0.tar.gz
cd openmpi-4.0.0
./configure --with-ucx=<ucx-install-path> --prefix=<ompi-install-path>
make -j 8 && make install
```

執行 OpenMPI。

```bash
<ompi-install-path>/bin/mpirun -np 2 --map-by node --hostfile ~/hostfile -mca pml ucx --mca btl ^vader,tcp,openib -x UCX_NET_DEVICES=mlx5_0:1  -x UCX_IB_PKEY=0x0003  ./osu_latency
```

如先前所述，請檢查您的資料分割索引鍵。

## <a name="mpich"></a>MPICH

安裝 UCX 如先前所述。

建置 MPICH。

```bash
wget http://www.mpich.org/static/downloads/3.3/mpich-3.3.tar.gz
tar -xvf mpich-3.3.tar.gz
cd mpich-3.3
./configure --with-ucx=<ucx-install-path> --prefix=<mpich-install-path> --with-device=ch4:ucx
make -j 8 && make install
```

執行 MPICH。

```bash
<mpich-install-path>/bin/mpiexec -n 2 -hostfile ~/hostfile -env UCX_IB_PKEY=0x0003 -bind-to hwthread ./osu_latency
```

如先前所述，請檢查您的資料分割索引鍵。

## <a name="mvapich2"></a>MVAPICH2

建置 MVAPICH2。

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/mv2/mvapich2-2.3.tar.gz
tar -xv mvapich2-2.3.tar.gz
cd mvapich2-2.3
./configure --prefix=<mvapich2-install-path>
make -j 8 && make install
```

執行 MVAPICH2。

```bash
<mvapich2-install-path>/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=48 ./osu_latency
```

## <a name="platform-mpi-community-edition"></a>平台 MPI Community Edition

平台 mpi 安裝必要的套件。

```bash
sudo yum install libstdc++.i686
sudo yum install glibc.i686
Download platform MPI at https://www.ibm.com/developerworks/downloads/im/mpi/index.html 
sudo ./platform_mpi-09.01.04.03r-ce.bin
```

請遵循安裝程序。

## <a name="intel-mpi"></a>Intel MPI

[下載 Intel MPI](https://software.intel.com/mpi-library/choose-download)。

變更 I_MPI_FABRICS 環境變數，視版本而定。 用於 Intel MPI 2018 `I_MPI_FABRICS=shm:ofa` 2019，針對使用`I_MPI_FABRICS=shm:ofi`。

釘選程序可正常使用 15、 30 和 60 PPN 預設。

## <a name="osu-mpi-benchmarks"></a>OSU MPI 基準測試

[下載 OSU MPI 基準測試][ http://mvapich.cse.ohio-state.edu/benchmarks/ ](http://mvapich.cse.ohio-state.edu/benchmarks/)和解壓縮。

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/osu-micro-benchmarks-5.5.tar.gz
tar –xvf osu-micro-benchmarks-5.5.tar.gz
cd osu-micro-benchmarks-5.5
```

建立基準測試使用特定 MPI 程式庫：

```bash
CC=<mpi-install-path/bin/mpicc>CXX=<mpi-install-path/bin/mpicxx> ./configure 
make
```

MPI 基準測試受到`mpi/`資料夾。


## <a name="discover-partition-keys"></a>探索資料分割索引鍵

探索資料分割索引鍵 （p-索引鍵） 與其他 Vm 通訊。

```bash
/sys/class/infiniband/mlx5_0/ports/1/pkeys/0
/sys/class/infiniband/mlx5_0/ports/1/pkeys/1
```

較大的兩個是應 MPI 搭配使用的租用戶金鑰。 範例：以下是 p 索引鍵，如果 0x800b 應該搭配 MPI。

```bash
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/0
0x800b
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/1
0x7fff
```

使用預設 (0x7fff) 資料分割索引鍵以外的磁碟分割。 UCX 需要 p 鍵來清除 MSB。 例如，設定 UCX_IB_PKEY 0x800b 的 0x000b 為。


## <a name="set-up-user-limits-for-mpi"></a>設定 mpi 的使用者限制

設定 MPI 的使用者限制。

```bash
cat << EOF >> /etc/security/limits.conf
*               hard    memlock         unlimited
*               soft    memlock         unlimited
*               hard    nofile          65535
*               soft    nofile          65535
EOF
```


## <a name="set-up-ssh-keys-for-mpi"></a>設定 mpi 的 SSH 金鑰

設定 MPI 類型需要它的 SSH 金鑰。

```bash
ssh-keygen -f /home/$USER/.ssh/id_rsa -t rsa -N ''
cat << EOF > /home/$USER/.ssh/config
Host *
    StrictHostKeyChecking no
EOF
cat /home/$USER/.ssh/id_rsa.pub >> /home/$USER/.ssh/authorized_keys
chmod 644 /home/$USER/.ssh/config
```

上述語法假設有共用的主目錄，其他的.ssh 目錄必須複製到每個節點。

## <a name="next-steps"></a>後續步驟

深入了解[HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/)在 Azure 上。
