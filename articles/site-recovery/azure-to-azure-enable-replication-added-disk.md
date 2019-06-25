---
title: 啟用磁碟新增至 Azure Site Recovery 所複寫的 Azure VM 的複寫 |Microsoft Docs
description: 本文說明如何將磁碟新增至 Azure VM 啟用災害復原，使用 Azure Site Recovery 的複寫
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/29/2018
ms.author: asgang
ms.openlocfilehash: 69122ffe9cefa3e1b9c6c8fbadfa80492ebebbde
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "64928058"
---
# <a name="enable-replication-for-a-disk-added-to-an-azure-vm"></a>啟用磁碟新增至 Azure VM 的複寫


本文說明如何啟用複寫的資料磁碟新增至 Azure VM 已啟用災害復原至另一個 Azure 區域中，使用[Azure Site Recovery](site-recovery-overview.md)。

啟用複寫，您將新增至 VM 的磁碟支援的 Azure Vm 使用受控磁碟。

當您將新的磁碟加入 Azure VM 複寫到另一個 Azure 區域時，發生下列情況：

-   Vm 的複寫健康狀態會顯示一則警告，並請注意，在入口網站會通知您的一或更多磁碟進行保護。
-   如果您啟用新增的磁碟保護時，警告就會消失之後初始複寫的磁碟。
-   如果您選擇不啟用磁碟的複寫，您可以選取要解除警告。

![加入新的磁碟](./media/azure-to-azure-enable-replication-added-disk/newdisk.png)



## <a name="before-you-start"></a>開始之前

本文假設您已已經設定災害復原，您要新增磁碟的 vm。 如果您還沒，請遵循[Azure 至 Azure 災害復原教學課程](azure-to-azure-tutorial-enable-replication.md)。 

## <a name="enable-replication-for-an-added-disk"></a>為新增的磁碟啟用複寫 

若要啟用新增的磁碟的複寫，執行下列作業：

1. 在保存庫 >**複寫的項目**，按一下您要加入之磁碟的 VM。
2. 按一下 **磁碟**，然後選取您要啟用複寫的資料磁碟 (這些磁碟都有**未受保護**狀態)。
3.  在 **磁碟詳細資料**，按一下**啟用複寫**。

    ![針對新增的磁碟啟用複寫](./media/azure-to-azure-enable-replication-added-disk/enabled-added.png)

啟用複寫作業執行，並在初始複寫完成之後，會移除磁碟問題的複寫健康情況警告。



# <a name="next-steps"></a>後續步驟

[深入了解](site-recovery-test-failover-to-azure.md)執行測試容錯移轉。
