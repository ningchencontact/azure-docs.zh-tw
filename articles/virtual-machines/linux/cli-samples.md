---
title: Azure CLI 範例 | Microsoft Docs
description: Azure CLI 範例
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
ms.custom: mvc
ms.openlocfilehash: fdbf402d14d3f1b3565866045a697212b6b76492
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60387140"
---
# <a name="azure-cli-samples-for-linux-virtual-machines"></a>適用於 Linux 虛擬機器的 Azure CLI 範例

下表包含指向使用 Azure CLI 生成的 bash 脚本的链接。

| | |
|---|---|
|**建立虛擬機器**||
| [创建虚拟机](./../scripts/virtual-machines-linux-cli-sample-create-vm-quick-create.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 以最低限度的組態建立 Linux 虛擬機器。 |
| [创建完全配置的虚拟机](./../scripts/virtual-machines-linux-cli-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 建立資源群組、虛擬機器和所有相關資源。|
| [建立高可用性的虛擬機器](./../scripts/virtual-machines-linux-cli-sample-nlb.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 使用高度可用且负载均衡的配置创建多个虚拟机。 |
| [创建 VM 并运行配置脚本](./../scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 建立虛擬機器，並使用「Azure 自訂指令碼」擴充功能來安裝 NGINX。 |
| [建立已安裝 WordPress 的 VM](./../scripts/virtual-machines-linux-cli-sample-create-vm-wordpress.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 建立虛擬機器，並使用「Azure 自訂指令碼」擴充功能來安裝 WordPress。 |
| [从托管 OS 磁盘创建 VM](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 連結現有受控磁碟作為 OS 磁碟，以建立虛擬機器。 |
| [從快照集建立 VM](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 先從快照集建立受控磁碟，然後連結新的受控磁碟作為 OS 磁碟，以從快照集建立虛擬機器。 |
|**管理儲存體**||
| [從 VHD 建立受控磁碟](../scripts/virtual-machines-linux-cli-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 從作為 OS 磁碟的特殊化 VHD 或從作為資料磁碟的 VHD，請建立受控的磁碟。  |
| [從快照集建立受控磁碟](../scripts/virtual-machines-linux-cli-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 從快照集建立受控磁碟。 |
| [將受控磁碟複製到相同或不同的訂用帳戶](../scripts/virtual-machines-linux-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 將受控磁碟複製到與父受控磁碟相同區域中相同或不同的訂用帳戶。 
| [將快照集以 VHD 格式匯出至儲存體帳戶](../scripts/virtual-machines-linux-cli-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 將受控快照集以 VDH 格式匯出至不同區域中的儲存體帳戶。 |
| [將受控磁碟的 VHD 匯出到儲存體帳戶](../scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 將受控磁碟的底層 VHD 匯出到不同區域中的儲存體帳戶。 |
| [將快照集複製到相同或不同的訂用帳戶](../scripts/virtual-machines-linux-cli-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 將快照集複製到與父快照集相同區域中相同或不同的訂用帳戶。 |
|**網路虛擬機器**||
| [保護虛擬機器之間的網路流量](./../scripts/virtual-machines-linux-cli-sample-create-vm-nsg.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 建立兩部虛擬機器、所有相關資源以及內部和外部網路安全性群組 (NSG)。 |
|**保護虛擬機器**||
| [加密 VM 和資料磁碟](./../scripts/virtual-machines-linux-cli-sample-encrypt-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 建立 Azure Key Vault、加密金鑰和服務主體，然後加密 VM。 |
|**監視虛擬機器**||
| [使用 Azure 監視器記錄檔監視 VM](./../scripts/virtual-machines-linux-cli-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 建立虛擬機器、安裝 Azure Log Analytics 代理程式，並在 Log Analytics 工作區中註冊 VM。  |
|**針對虛擬機器進行疑難排解**||
| [針對 VM 作業系統磁碟進行疑難排解](./../scripts/virtual-machines-linux-cli-sample-mount-os-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 掛接一個 VM 的作業系統磁碟作為第二個 VM 上的資料磁碟。 |
| | |
