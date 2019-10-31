---
title: 啟用虛擬機器擴展集的 Azure 磁碟加密
description: 本文提供針對虛擬機器擴展集啟用 Microsoft Azure 磁片加密的指示
author: msmbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: article
ms.author: mbaldwin
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 0097d0e1d5ea7de092da14683d4bab3d673b2219
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2019
ms.locfileid: "73177772"
---
# <a name="azure-disk-encryption-for-virtual-machine-scale-sets"></a>虛擬機器擴展集的 Azure 磁碟加密

Azure 磁碟加密提供虛擬機器的 OS 和資料磁片的磁片區加密，協助保護和保護您的資料，以符合組織的安全性和合規性承諾。 若要深入瞭解，請參閱[Azure 磁碟加密： Linux vm](../virtual-machines/linux/disk-encryption-overview.md)和[Azure 磁碟加密： Windows vm](../virtual-machines/windows/disk-encryption-overview.md)  

在這些情況下，Azure 磁碟加密也可以套用至 Windows 和 Linux 虛擬機器擴展集：
- 使用受控磁片建立的擴展集。 不支援原生（或非受控）磁片擴展集的 Azure 磁片加密。
- Windows 擴展集中的 OS 和資料磁片區。
- Linux 擴展集內的資料磁片區。 Linux 擴展集目前不支援 OS 磁片加密。

只有幾分鐘的時間，您可以使用[Azure CLI 來加密虛擬機器](disk-encryption-cli.md)擴展集，或[使用 Azure PowerShell 來加密](disk-encryption-powershell.md)虛擬機器擴展集，以瞭解虛擬機器擴展集 Azure 磁碟加密的基本概念教程.

## <a name="next-steps"></a>後續步驟

- [使用 Azure Resource Manager 加密虛擬機器擴展集](disk-encryption-azure-resource-manager.md)
- [針對 Azure 磁碟加密建立及設定金鑰保存庫](disk-encryption-key-vault.md)
- [使用搭配虛擬機器擴展集擴充功能排序的 Azure 磁碟加密](disk-encryption-extension-sequencing.md)
