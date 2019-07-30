---
title: Windows VM 的 Azure 磁碟儲存體受控磁碟概觀 | Microsoft Docs
description: 使用 Azure Windows VM 時為您處理儲存體帳戶的 Azure 受控磁碟概觀
services: virtual-machines-windows,storage
author: roygara
ms.service: virtual-machines-windows
ms.workload: storage
ms.tgt_pltfrm: vm-windows
ms.topic: overview
ms.date: 04/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: fb1ee8556935b141dfee6a18c96ecafb476aa584
ms.sourcegitcommit: 57a7d4f67635212f5bf0c56e58fd87c8ec366f2c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2019
ms.locfileid: "64725809"
---
# <a name="introduction-to-azure-managed-disks"></a>Azure 受控磁碟簡介

Azure 受控磁碟是虛擬硬碟 (VHD)。 您可以將它視為內部部署伺服器中虛擬化的實體磁碟。 Azure 受控磁碟會儲存為分頁 Blob，它是 Azure 中的隨機 IO 儲存物件。 我們稱受控磁碟為「受控」，因為它是對分頁 Blob、Blob 容器和 Azure 儲存體帳戶的抽象概念。 使用受控磁碟，您所要做的就是佈建磁碟，Azure 會負責執行剩餘的部分。

當您選擇將 Azure 受控磁碟與工作負載一起使用時，Azure 會為您建立並管理磁碟。 這些可用的磁碟類型為超固態硬碟 (SSD) (預覽)、進階 SSD、標準 SSD 和標準硬碟磁碟機 (HDD)。 如需每種個別磁碟類型的相關詳細資訊，請參閱[選取適用於 IaaS VM 的磁碟類型](disks-types.md)。

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [選取適用於 IaaS VM 的磁碟類型](disks-types.md)