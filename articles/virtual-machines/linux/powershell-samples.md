---
title: Azure 虛擬機器 PowerShell 範例 | Microsoft Docs
description: Azure 虛擬機器 PowerShell 範例
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.openlocfilehash: 53784c3d74f9e6af5f1e84cc098194113e81333b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60771255"
---
# <a name="azure-virtual-machine-powershell-samples"></a>Azure 虛擬機器 PowerShell 範例

下表包含可供建立和管理 Linux 虛擬機器之 PowerShell 指令碼範例的連結。

| | |
|---|---|
|**建立虛擬機器**||
| [建立完整設定的虛擬機器](./../scripts/virtual-machines-linux-powershell-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 建立資源群組、虛擬機器和所有相關資源。|
| [建立已啟用 Docker 功能的 VM](./../scripts/virtual-machines-linux-powershell-sample-create-docker-host.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 建立虛擬機器、設定此 VM 作為 Docker 主機，然後執行 NGINX 容器。 |
| [建立 VM 並執行組態指令碼](./../scripts/virtual-machines-linux-powershell-sample-create-vm-nginx.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 建立虛擬機器，並使用「Azure 自訂指令碼」擴充功能來安裝 NGINX。 |
| [建立已安裝 WordPress 的 VM](./../scripts/virtual-machines-linux-powershell-sample-create-vm-wordpress.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 建立虛擬機器，並使用「Azure 自訂指令碼」擴充功能來安裝 WordPress。 |
| [从托管 OS 磁盘创建 VM](./../scripts/virtual-machines-linux-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 連結現有受控磁碟作為 OS 磁碟，以建立虛擬機器。 |
| [從快照集建立 VM](./../scripts/virtual-machines-linux-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 藉由先從快照集建立受控磁碟，然後連結新的受控磁碟作為 OS 磁碟，從快照集建立虛擬機器。 |
|**管理儲存體**||
| [在相同或不同的訂用帳戶中從 VHD 建立受控磁碟](../scripts/virtual-machines-linux-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 在相同或不同的訂用帳戶中，從作為 OS 磁碟的特製化 VHD 或從作為資料磁碟的資料 VHD 建立受控磁碟。  |
| [從快照集建立受控磁碟](../scripts/virtual-machines-linux-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 從快照集建立受控磁碟。 |
| [將快照集以 VHD 形式匯出到儲存體帳戶](../scripts/virtual-machines-linux-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 將受控快照集以 VHD 形式匯出到不同區域中的儲存體帳戶。 |
| [將受控磁碟的 VHD 匯出到儲存體帳戶](../scripts/virtual-machines-linux-powershell-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 將受控磁碟的底層 VHD 匯出到不同區域中的儲存體帳戶。 |
| [從 VDH 建立快照集](../scripts/virtual-machines-linux-powershell-sample-create-snapshot-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 從 VHD 建立快照集，然後使用該快照集來快速建立多個相同的受控磁碟。  |
| [將快照集複製到相同或不同的訂用帳戶](../scripts/virtual-machines-linux-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 將快照集複製到與父快照集相同區域中相同或不同的訂用帳戶。 |
|**監視虛擬機器**||
| [使用 Azure 監視器記錄檔監視 VM](./../scripts/virtual-machines-linux-powershell-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 建立虛擬機器、安裝 Azure Log Analytics 代理程式，並在 Log Analytics 工作區中註冊 VM。  |
| [將受控磁碟複製到相同或不同的訂用帳戶](../scripts/virtual-machines-linux-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 將受控磁碟複製到與父受控磁碟相同區域中相同或不同的訂用帳戶。
| | |
