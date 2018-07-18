---
title: 在 Azure 中建立和使用 Linux VM 的 SSH 金鑰組 | Microsoft Docs
description: 如何在 Azure 中建立和使用 Linux VM 的 SSH 公開和私密金鑰組，以改善驗證程序的安全性。
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 34ae9482-da3e-4b2d-9d0d-9d672aa42498
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: cynthn
ms.openlocfilehash: 2e3d86d776f44c47a33bf075cf7f2140a3940e5e
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2018
ms.locfileid: "37928449"
---
# <a name="quick-steps-create-and-use-an-ssh-public-private-key-pair-for-linux-vms-in-azure"></a>快速步驟：在 Azure 中建立和使用 Linux VM 的 SSH 公開和私密金鑰組
您可以利用安全殼層 (SSH) 金鑰組，在 Azure 上建立使用 SSH 金鑰來進行驗證的虛擬機器 (VM)，進而免除登入密碼的需求。 本文說明如何快速產生和使用 Linux VM 的 SSH 公開和私密金鑰檔案組。 您可以使用 Azure Cloud Shell、macOS 或 Linux 主機、Windows Subsystem for Linux 以及其他支援 OpenSSH 的工具，完成這些步驟。 

如需詳細的背景和範例，請參閱[建立 SSH 金鑰組的詳細步驟](create-ssh-keys-detailed.md)。

如需在 Windows 電腦上產生及使用 SSH 金鑰的其他方式，請參閱[如何在 Azure 上搭配 Windows 使用 SSH 金鑰](ssh-from-windows.md)。

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="create-an-ssh-key-pair"></a>建立 SSH 金鑰組
使用 `ssh-keygen` 命令來產生 SSH 公開和私密金鑰檔案，這些檔案預設建立於 `~/.ssh` 目錄中。 出現提示時，您可以指定不同位置和其他複雜密碼 (存取私密金鑰檔案的密碼)。 如果有 SSH 金鑰組存在於目前的位置，系統將會覆寫這些檔案。

```bash
ssh-keygen -t rsa -b 2048
```

如果您使用 [Azure CLI 2.0](/cli/azure) 來建立您的 VM，便可以執行 [az vm create](/cli/azure/vm#az_vm_create) 命令加上 `--generate-ssh-keys` 選項，即可選擇性地產生 SSH 公開金鑰和私密金鑰檔案。 金鑰儲存在 ~/.ssh 目錄中。 請注意，如果金鑰已經存在於該位置，則此命令選項不會覆寫金鑰。

## <a name="provide-ssh-public-key-when-deploying-a-vm"></a>部署 VM 時，提供 SSH 公開金鑰
若要建立使用 SSH 金鑰進行驗證的 Linux VM，請在使用 Azure 入口網站、CLI、Resource Manager 範本或其他方法建立 VM 時，指定您的 SSH 公開金鑰：

* [使用 Azure 入口網站建立 Linux 虛擬機器](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [使用 Azure CLI 建立 Linux 虛擬機器](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [使用 Azure 範本建立 Linux VM](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

如果您不熟悉 SSH 公開金鑰的格式，則可以如下所示執行 `cat`，並以自己的公開金鑰檔案位置取代 `~/.ssh/id_rsa.pub`，即可看到公開金鑰：

```bash
cat ~/.ssh/id_rsa.pub
```

如果您在 Azure 入口網站或 Resource Manager 範本中複製和貼上要使用之公開金鑰檔案的內容，請確定您並未複製任何額外的空白字元。 例如，如果您使用 macOS，則可以透過管道將公開金鑰檔案 (預設為 `~/.ssh/id_rsa.pub`) 傳送至 **pbcopy** 以複製內容 (另有其他 Linux 程式可執行相同作業，例如 **xclip**)。

您放置在 Azure 中 Linux VM 上的公開金鑰預設會儲存在 `~/.ssh/id_rsa.pub`，除非您在建立金鑰時變更了位置。 如果您使用 [Azure CLI 2.0](/cli/azure) 搭配現有公開金鑰來建立 VM，可執行 [az vm create](/cli/azure/vm#az_vm_create) 命令加上 `--ssh-key-value` 選項，來指定這個公開金鑰的值或位置。 

## <a name="ssh-to-your-vm"></a>透過 SSH 連線到您的 VM
公開金鑰已部署到您的 Azure VM 且私密金鑰儲存在本機系統中，此時可以使用 VM 的 IP 位址或 DNS 名稱，透過 SSH 連線到您的 VM。 將下列命令中的 *azureuser* 和 *myvm.westus.cloudapp.azure.com* 換成系統管理員使用者名稱和完整網域名稱 (或 IP 位址)：

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

如果您在建立金鑰組時提供了複雜密碼，請在登入程序期間出現提示時輸入複雜密碼  (伺服器會新增至 `~/.ssh/known_hosts` 資料夾，而且系統不會要求您重新連線，除非 Azure VM 上的公開金鑰有變更或伺服器名稱已從 `~/.ssh/known_hosts` 中移除)。

使用 SSH 金鑰建立的 VM 預設會設定為停用密碼，以大幅提高暴力猜測嘗試的成本，進而讓攻擊難以進行。 

## <a name="next-steps"></a>後續步驟

本文描述如何建立簡單的 SSH 金鑰組以供快速使用。 

* 如果您對於使用 SSH 金鑰組需要更多的協助，請參閱[建立及管理 SSH 金鑰組的詳細步驟](create-ssh-keys-detailed.md)。

* 如果您有 Azure VM 的 SSH 連線問題，請參閱[針對 SSH 連線至 Azure Linux VM 進行疑難排解](troubleshoot-ssh-connection.md)。


