---
title: 啟用與 SR-IOV-Azure 虛擬機器的 InifinBand |Microsoft Docs
description: 了解如何啟用與 SR-IOV InfiniBand。
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
ms.openlocfilehash: 2e28627359f339a3bf818a15d6a5c8e456fb554a
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797532"
---
# <a name="enable-infiniband-with-sr-iov"></a>啟用與 SR-IOV 的 InfiniBand

設定您的自訂 VM 映像使用 InfiniBand (IB) 的最簡單且建議的方式是將 InfiniBandDriverLinux 或 InfiniBandDriverWindows VM 擴充功能新增至您的部署。
了解如何使用這些 VM 延伸模組[Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc#rdma-capable-instances)和[Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances)

若要手動設定需 SR-IOV 的 InfiniBand 已啟用虛擬機器 （目前 HB 和 HC 系列），請遵循下列步驟。 上述步驟只適用於 RHEL/CentOS。 適用於 Ubuntu （16.04 和 18.04） 及 SLES （12 SP4 和 15），收件匣驅動程式會正常運作。

## <a name="manually-install-ofed"></a>手動安裝 OFED

安裝最新的 MLNX_OFED 驅動程式從 ConnectX-5 [Mellanox](https://www.mellanox.com/page/products_dyn?product_family=26)。

RHEL/centos （例如，以下為 7.6）：

```bash
sudo yum install -y kernel-devel python-devel
sudo yum install -y redhat-rpm-config rpm-build gcc-gfortran gcc-c++
sudo yum install -y gtk2 atk cairo tcl tk createrepo
wget --retry-connrefused --tries=3 --waitretry=5 http://content.mellanox.com/ofed/MLNX_OFED-4.5-1.0.1.0/MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64.tgz
tar zxvf MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64.tgz
sudo ./MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64/mlnxofedinstall --add-kernel-support
```

對於 Windows，下載並安裝 WinOF 2 驅動 ConnectX 到 5 [Mellanox](https://www.mellanox.com/page/products_dyn?product_family=32&menu_section=34)

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

將 IP 位址指派給 ib0 介面，使用：

- 以手動方式指派 IP 位址給 ib0 介面 （以 root 身分）。

    ```bash
    ifconfig ib0 $(sed '/rdmaIPv4Address=/!d;s/.*rdmaIPv4Address="\([0-9.]*\)".*/\1/' /var/lib/waagent/SharedConfig.xml)/16
    ```

或

- 使用 WALinuxAgent 指派 IP 位址，並將其保存。

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

深入了解[HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/)在 Azure 上。
