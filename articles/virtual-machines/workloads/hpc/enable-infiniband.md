---
title: 使用 SR-IOV 啟用 InifinBand-Azure 虛擬機器 |Microsoft Docs
description: 瞭解如何使用 SR-IOV 來啟用不限。
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/15/2019
ms.author: amverma
ms.openlocfilehash: 7218fceae71969f204c6c25ba4793a7c94341693
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2019
ms.locfileid: "70858478"
---
# <a name="enable-infiniband-with-sr-iov"></a>使用 SR-IOV 啟用不限

開始使用適用于 HPC 的 IaaS Vm 最簡單且建議的方式是使用 CentOS-HPC 7.6 VM OS 映射。 如果使用您的自訂 VM 映射，使用「不限」（IB）來設定它的最簡單方式，就是將 InfiniBandDriverLinux 或 InfiniBandDriverWindows VM 擴充功能新增至您的部署。
瞭解如何搭配[Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc#rdma-capable-instances)和[WINDOWS](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances)使用這些 VM 擴充功能

若要在已啟用 SR-IOV 的 Vm （目前為 HB 和 HC 系列）上手動設定「無法執行」，請遵循下列步驟。 這些步驟僅適用于 RHEL/CentOS。 對於 Ubuntu （16.04 和18.04）和 SLES （12 SP4 和15），收件匣驅動程式會運作良好。

## <a name="manually-install-ofed"></a>手動安裝 OFED

從[Mellanox](https://www.mellanox.com/page/products_dyn?product_family=26)安裝適用于 ConnectX-5 的最新 MLNX_OFED 驅動程式。

適用于 RHEL/CentOS （以下為7.6 的範例）：

```bash
sudo yum install -y kernel-devel python-devel
sudo yum install -y redhat-rpm-config rpm-build gcc-gfortran gcc-c++
sudo yum install -y gtk2 atk cairo tcl tk createrepo
wget --retry-connrefused --tries=3 --waitretry=5 http://content.mellanox.com/ofed/MLNX_OFED-4.5-1.0.1.0/MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64.tgz
tar zxvf MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64.tgz
sudo ./MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64/mlnxofedinstall --add-kernel-support
```

若是 Windows，請從[Mellanox](https://www.mellanox.com/page/products_dyn?product_family=32&menu_section=34)下載並安裝適用于 ConnectX-5 的 WinOF-2 驅動程式

## <a name="enable-ipoib"></a>啟用 IPoIB

```bash
sudo sed -i 's/LOAD_EIPOIB=no/LOAD_EIPOIB=yes/g' /etc/infiniband/openib.conf
sudo /etc/init.d/openibd restart
if [ $? -eq 1 ]
then
  sudo modprobe -rv  ib_isert rpcrdma ib_srpt
  sudo /etc/init.d/openibd restart
fi
```

## <a name="assign-an-ip-address"></a>指派 IP 位址

使用下列其中一種方式，將 IP 位址指派給 ib0 介面：

- 以手動方式將 IP 位址指派給 ib0 介面（以 root 身分）。

    ```bash
    ifconfig ib0 $(sed '/rdmaIPv4Address=/!d;s/.*rdmaIPv4Address="\([0-9.]*\)".*/\1/' /var/lib/waagent/SharedConfig.xml)/16
    ```

OR

- 使用 WALinuxAgent 來指派 IP 位址，並將它保存。

    ```bash
    yum install -y epel-release
    yum install -y python-pip
    python -m pip install --upgrade pip setuptools wheel
    wget "https://github.com/Azure/WALinuxAgent/archive/release-2.2.36.zip"
    unzip release-2.2.36.zip
    cd WALinuxAgent*
    python setup.py install --register-service --force
    sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
    sed -i -e 's/# AutoUpdate.Enabled=y/AutoUpdate.Enabled=y/g' /etc/waagent.conf
    systemctl restart waagent
    ```

## <a name="next-steps"></a>後續步驟

深入瞭解 Azure 上的[HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) 。
