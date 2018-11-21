---
title: 因 NIC 停用而無法將遠端桌面連線到 Azure 虛擬機器 | Microsoft Docs
description: 了解如何排解因 Azure VM 已停用 NIC 而導致 RDP 失敗的問題 | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/12/2018
ms.author: genli
ms.openlocfilehash: 6b14530bd6b4c1b6617cb1d5c88d710a32e5372c
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2018
ms.locfileid: "51634812"
---
#  <a name="cannot-remote-desktop-to-a-vm-because-the-network-interface-is-disabled"></a>因網路介面已停用而無法將遠端桌面連線到 VM

本文說明如何解決因網路介面已停用而無法將遠端桌面連線到 Azure Windows 虛擬機器 (VM) 的問題。

> [!NOTE] 
> Azure 建立和處理資源的部署模型有二種： [資源管理員和傳統](../../azure-resource-manager/resource-manager-deployment-model.md)。 本文說明如何使用 Resource Manager 部署模型，我們建議將它用於新部署，而非用於傳統部署模型。 

## <a name="symptoms"></a>徵兆 

您無法經由任何其他連接埠對 Azure 中的 VM 進行 RDP 連線或任何其他類型的連線，因為 VM 中的網路介面已停用。

## <a name="solution"></a>解決方法 

在遵循下列步驟之前，請擷取受影響虛擬機器作業系統磁碟的快照集作為備份。 如需詳細資訊，請參閱[擷取磁碟快照集](../windows/snapshot-copy-managed-disk.md)。

若要啟用 VM 的介面，請使用序列控制項或為 VM [重設網路介面](##reset-network-interface)。

### <a name="use-serial-control"></a>使用序列主控台

1. 連線至[序列主控台並開啟 CMD 執行個體](./serial-console-windows.md#open-cmd-or-powershell-in-serial-console
)。 如果未在 VM 上啟用序列主控台，請參閱[重設網路介面](#reset-network-interface)。
2. 檢查網路介面的狀態：

        netsh interface show interface

    記下停用的網路介面名稱。 

3. 啟用網路介面：

        netsh interface set interface name="interface Name" admin=enabled

    比方說，如果交互作用介面名稱為「乙太網路 2」，請執行下列命令：

        netsh interface set interface name=""Ethernet 2" admin=enabled
    

4.  再次檢查網路介面的狀態，確定網路介面已啟用。

        netsh interface show interface

    此時您不需要重新啟動 VM。 您可回頭連線 VM。
        
5.  連線至 VM 並查看問題是否已解決。

## <a name="reset-network-interface"></a>重設網路介面

若要重設網路介面，請使用 Azure 入口網站或 Azure PowerShell 將 IP 位址變更為子網路中其他可用的 IP 位址。 如需詳細資訊，請參閱[重設網路介面](reset-network-interface.md)。 