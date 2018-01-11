---
title: "適用於 Linux Vm 設定 DHCPv6 |Microsoft 文件"
description: "如何設定 Linux VM 的 DHCPv6。"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
keywords: "ipv6, azure load balancer, 雙重堆疊, 公用 ip, 原生 ipv6, 行動, iot"
ms.assetid: b32719b6-00e8-4cd0-ba7f-e60e8146084b
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: b46c2107dcfda5f02407e08daf08bd42d722dfda
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2017
---
# <a name="configure-dhcpv6-for-linux-vms"></a>設定 DHCPv6 適用於 Linux Vm

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

有些 Linux 虛擬機器映像，在 Azure Marketplace 中沒有動態主機設定通訊協定版本 6 (DHCPv6) 預設設定。 若要支援 IPv6，必須設定 DHCPv6 Linux 作業系統發佈您正在使用中。 各種 Linux 發行套件設定 DHCPv6 各種不同的方式，因為它們使用不同的套件。

> [!NOTE]
> Azure Marketplace 中最新的 SUSE Linux 和 CoreOS 映像已有預先設定 DHCPv6。 當您使用這些映像時，不需要其他變更。

本文件說明如何啟用 DHCPv6 使您的 Linux 虛擬機器取得 IPv6 位址。

> [!WARNING]
> 藉由不正確編輯網路組態檔，可能無法在 VM 網路存取。 我們建議您先在非生產系統上測試組態變更。 本文中的指示已經過在 Azure Marketplace 中最新版 Linux 映像上測試過。 如需更詳細的指示，請參閱您自己的 Linux 版本的文件。

## <a name="ubuntu"></a>Ubuntu

1. 編輯*/etc/dhcp/dhclient6.conf*檔並加入下行：

        timeout 10;

2. 編輯下列組態的 eth0 介面網路組態︰

   * 在**Ubuntu 12.04 和 14.04**，編輯*/etc/network/interfaces.d/eth0.cfg*檔案。 
   * 在**Ubuntu 16.04**，編輯*/etc/network/interfaces.d/50-cloud-init.cfg*檔案。

         iface eth0 inet6 auto
             up sleep 5
             up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. 更新 IPv6 位址︰

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="debian"></a>Debian

1. 編輯*/etc/dhcp/dhclient6.conf*檔並加入下行：

        timeout 10;

2. 編輯*/etc/network/interfaces*檔並加入下列組態：

        iface eth0 inet6 auto
            up sleep 5
            up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. 更新 IPv6 位址︰

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="rhel-centos-and-oracle-linux"></a>RHEL、 CentOS 和 Oracle Linux

1. 編輯*/etc/sysconfig/network*檔案，然後加入下列參數：

        NETWORKING_IPV6=yes

2. 編輯*/etc/sysconfig/network-scripts/ifcfg-eth0*檔並加入下列兩個參數：

        IPV6INIT=yes
        DHCPV6C=yes

3. 更新 IPv6 位址︰

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-11-and-opensuse-13"></a>SLES 11 和 openSUSE 13

最近 SUSE Linux Enterprise Server (SLES) 與 Azure 中的 openSUSE 映像已預先設定 DHCPv6。 當您使用這些映像時，不需要其他變更。 如果您有較舊或自訂 SUSE 映像為基礎的 VM 時，執行下列作業：

1. 如有需要，安裝 `dhcp-client` 套件：

    ```bash
    sudo zypper install dhcp-client
    ```

2. 編輯*/etc/sysconfig/network/ifcfg-eth0*檔案，然後加入下列參數：

        DHCLIENT6_MODE='managed'

3. 更新 IPv6 位址︰

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-12-and-opensuse-leap"></a>SLES 12 和 openSUSE Leap

在 Azure 中最新的 SLES 和 openSUSE 映像已預先設定 DHCPv6。 當您使用這些映像時，不需要其他變更。 如果您有較舊或自訂 SUSE 映像為基礎的 VM 時，執行下列作業：

1. 編輯*/etc/sysconfig/network/ifcfg-eth0*檔案，並取代`#BOOTPROTO='dhcp4'`參數具有下列值：

        BOOTPROTO='dhcp'

2. 若要*/etc/sysconfig/network/ifcfg-eth0*檔案中，加入下列參數：

        DHCLIENT6_MODE='managed'

3. 更新 IPv6 位址︰

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="coreos"></a>CoreOS

在 Azure 中最新的 SLES 映像已預先設定 DHCPv6。 當您使用這些映像時，不需要其他變更。 如果您有根據舊或自訂 CoreOS 映像的 VM 時，執行下列作業：

1. 編輯*/etc/systemd/network/10_dhcp.network*檔案：

        [Match]
        eth0

        [Network]
        DHCP=ipv6

2. 更新 IPv6 位址︰

    ```bash
    sudo systemctl restart systemd-networkd
    ```
