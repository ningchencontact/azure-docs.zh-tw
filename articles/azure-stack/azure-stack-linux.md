---
title: "將 Linux 映像新增到 Azure Stack"
description: "了解如何將 Linux 映像新增到 Azure Stack。"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 6e6f9ca3b314ee2f58d8007e7ddc93ddd213e361
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/24/2018
---
# <a name="add-linux-images-to-azure-stack"></a>將 Linux 映像新增到 Azure Stack

*適用於：Azure Stack 整合系統和 Azure Stack 開發套件*

您可以透過將 Linux 型映像新增到「Azure Stack 市集」中，在 Azure Stack 上部署 Linux 虛擬機器 (VM)。 將 Linux 映像新增到 Azure Stack 的最簡單方式就是透過「市集管理」。 這些映像已備妥，並已針對與 Azure Stack 的相容性進行測試。

## <a name="marketplace-management"></a>市集管理

若要從 Azure Marketplace 下載 Linux 映像，請使用下列文章中的程序。 選取您想要在 Azure Stack 上提供給使用者的 Linux 映像。

[將市集項目從 Azure 下載到 Azure Stack](azure-stack-download-azure-marketplace-item.md).

## <a name="prepare-your-own-image"></a>準備您自己的映像

您可以使用下列其中一個指示來準備自己的 Linux 映像：

   * [CentOS 型發行版本](../virtual-machines/linux/create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Debian Linux](../virtual-machines/linux/debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [SLES 和 openSUSE](../virtual-machines/linux/suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Ubuntu](../virtual-machines/linux/create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

1. 下載並安裝 [Azure Linux 代理程式](https://github.com/Azure/WALinuxAgent/)。

    需要 Azure Linux 代理程式版本 2.2.2 或更高版本，才能在 Azure Stack 上佈建 Linux VM，但某些版本無法運作 (例如，2.2.12 和 2.2.13)。 先前所列的許多發佈已包含代理程式的版本 (通常稱為 `WALinuxAgent` 或 `walinuxagent`)。 如果您建立自訂映像，就必須手動安裝代理程式。 可從套件名稱或在虛擬機器上執行 `/usr/sbin/waagent -version` 來判斷已安裝的版本。

    請遵循下列指示，手動安裝 Azure Linux 代理程式：

   a. 首先，從 [GitHub](https://github.com/Azure/WALinuxAgent/releases) 下載最新的 Azure Linux 代理程式，例如：

            # wget https://github.com/Azure/WALinuxAgent/archive/v2.2.21.tar.gz
   b. 解壓縮 Azure 代理程式：

            # tar -vzxf v2.2.21.tar.gz
   c. 安裝 python 安裝工具

        **Debian / Ubuntu**

            # sudo apt-get update
            # sudo apt-get install python-setuptools

        **Ubuntu 16.04+**

            # sudo apt-get install python3-setuptools

        **RHEL / CentOS / Oracle Linux**

            # sudo yum install python-setuptools
   d. 安裝 Azure 代理程式：

            # cd WALinuxAgent-2.2.21
            # sudo python3 setup.py install --register-service

     已並存安裝 Python 2.x 和 Python 3.x 的系統可能會需要執行下列命令：

         sudo python3 setup.py install --register-service
     如需詳細資訊，請參閱 Azure Linux 代理程式[讀我檔案](https://github.com/Azure/WALinuxAgent/blob/master/README.md)。
2. [將映像新增到 Marketplace 中](azure-stack-add-vm-image.md)。 請確定已將 `OSType` 參數設定為 `Linux`。
3. 將映像新增到「市集」之後，便會建立「市集」項目，使用者就可以部署 Linux 虛擬機器。

## <a name="next-steps"></a>後續步驟
[Azure Stack 中的服務提供概觀](azure-stack-offer-services-overview.md)
