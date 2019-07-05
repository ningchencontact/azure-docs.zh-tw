---
title: 高效能運算-Azure 虛擬機器 |Microsoft Docs
description: 深入了解在 Azure 上的高效能運算。
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/07/2019
ms.author: amverma
ms.openlocfilehash: e8ff4147130dfeff14be41ed292b51ed34966df0
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537684"
---
# <a name="optimization-for-linux"></a>針對 Linux 進行最佳化

本文說明一些重要的技巧，來最佳化您的 OS 映像。 深入了解[啟用 InfiniBand](enable-infiniband.md)和最佳化 OS 映像。

## <a name="update-lis"></a>更新 LIS

如果部署使用自訂映像 （例如，例如 CentOS/RHEL 7.4 或 7.5 舊版作業系統），更新 VM 上的 LIS。

```bash
wget https://aka.ms/lis
tar xzf lis
pushd LISISO
./upgrade.sh
```

## <a name="reclaim-memory"></a>回收記憶體

藉由自動回收以避免遠端記憶體存取的記憶體來改善效率。

```bash
echo 1 >/proc/sys/vm/zone_reclaim_mode
```

若要讓此 VM 重新開機後保存：

```bash
echo "vm.zone_reclaim_mode = 1" >> /etc/sysctl.conf sysctl -p
```

## <a name="disable-firewall-and-selinux"></a>停用防火牆和 SELinux

停用防火牆和 SELinux。

```bash
systemctl stop iptables.service
systemctl disable iptables.service
systemctl mask firewalld
systemctl stop firewalld.service
systemctl disable firewalld.service
iptables -nL
sed -i -e's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
```

## <a name="disable-cpupower"></a>停用 cpupower

停用 cpupower。

```bash
service cpupower status
if enabled, disable it:
service cpupower stop
sudo systemctl disable cpupower
```

## <a name="next-steps"></a>後續步驟

* 深入了解[啟用 InfiniBand](enable-infiniband.md)和最佳化 OS 映像。

* 深入了解[HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/)在 Azure 上。
