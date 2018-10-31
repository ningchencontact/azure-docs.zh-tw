---
title: Azure CLI 範例 Windows | Microsoft Docs
description: Azure CLI 範例 Windows
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/01/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 6861399b63b7f06bac7599704a6dd1aa87800ebf
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2018
ms.locfileid: "49403333"
---
# <a name="azure-cli-samples-for-windows-virtual-machines"></a>適用於 Windows 虛擬機器的 Azure CLI 範例

下表包含使用 Azure CLI 所建置、可部署 Windows 虛擬機器之 Bash 指令碼的連結。

| | |
|---|---|
|**建立虛擬機器**||
| [建立虛擬機器](./../scripts/virtual-machines-windows-cli-sample-create-vm-quick-create.md?toc=%2fcli%2fazure%2ftoc.json) | 以最低限度的組態建立 Windows 虛擬機器。 |
| [建立完整設定的虛擬機器](./../scripts/virtual-machines-windows-cli-sample-create-vm.md?toc=%2fcli%2fazure%2ftoc.json) | 建立資源群組、虛擬機器和所有相關資源。|
| [建立高可用性的虛擬機器](./../scripts/virtual-machines-windows-cli-sample-nlb.md?toc=%2fcli%2fazure%2ftoc.json) | 依據高可用性和負載平衡組態建立數個虛擬機器。 |
| [建立 VM 並執行組態指令碼](./../scripts/virtual-machines-windows-cli-sample-create-vm-iis.md?toc=%2fcli%2fazure%2ftoc.json) | 建立虛擬機器，並使用 Azure 自訂指令碼擴充功能來安裝 IIS。 |
| [建立 VM 並執行 DSC 組態](./../scripts/virtual-machines-windows-cli-sample-create-iis-using-dsc.md?toc=%2fcli%2fazure%2ftoc.json) | 建立虛擬機器，並使用 Azure 預期狀態設定 (DSC) 擴充功能來安裝 IIS。 |
|**網路虛擬機器**||
| [保護虛擬機器之間的網路流量](./../scripts/virtual-machines-windows-cli-sample-create-vm-nsg.md?toc=%2fcli%2fazure%2ftoc.json) | 建立兩部虛擬機器、所有相關資源以及內部和外部網路安全性群組 (NSG)。 |
|**保護虛擬機器**||
| [加密 VM 和資料磁碟](./../scripts/virtual-machines-windows-cli-sample-encrypt-vm.md?toc=%2fcli%2fazure%2ftoc.json) | 建立 Azure Key Vault、加密金鑰和服務主體，然後加密 VM。 |
|**監視虛擬機器**||
| [透過 Log Analytics 監視 VM](./../scripts/virtual-machines-windows-cli-sample-create-vm-oms.md?toc=%2fcli%2fazure%2ftoc.json) | 建立虛擬機器、安裝 Azure Log Analytics 代理程式，並在 Log Analytics 工作區中註冊 VM。  |
| | |
