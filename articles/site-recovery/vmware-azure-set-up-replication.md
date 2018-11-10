---
title: 使用 Azure Site Recovery 來設定及管理 VMware 災害復原的複寫原則 | Microsoft Docs
description: 說明如何使用 Azure Site Recovery 設定從 VMware 災害復原至 Azure 的複寫設定。
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: sutalasi
ms.openlocfilehash: fd987097c2ca7b1e7509a1a0e63905c36ec8fec8
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2018
ms.locfileid: "50212891"
---
# <a name="configure-and-manage-replication-policies-for-vmware-disaster-recovery-to-azure"></a>設定及管理「將 VMware 災害復原至 Azure」的複寫原則
本文說明使用 [Azure Site Recovery](site-recovery-overview.md) 將 VMware VM 複寫至 Azure 時，如何設定複寫原則。


## <a name="create-a-policy"></a>建立原則

1. 選取 [管理] > [Site Recovery 基礎結構]。
1. 在 [VMware 和實體機器] 中選取 [複寫原則]。 
1. 按一下 [+複寫原則]，並指定原則名稱。
1. 在 [RPO 臨界值] 中，指定 RPO 限制。 連續複寫超過此限制時，會產生警示。
1. 在 [復原點保留] 中，指定每個復原點的保留週期 (以小時為單位)。 受保護的機器可以復原到保留週期內的任意點。 針對複寫到進階儲存體的機器支援最多保留 24 小時。 標準儲存體支援最多 72 小時。
1. 在 [應用程式一致快照頻率] 中，指定建立包含應用程式一致快照之復原點的頻率 (以分鐘為單位)。
1. 按一下 [確定]。 原則應該會在 30 至 60 秒內建立。

當您建立複寫原則時，會自動建立相符的容錯回復複寫原則，並加上 "failback" 尾碼。 原則建立之後, 您可以選取並按一下 [編輯設定] 加以編輯。

## <a name="associate-a-configuration-server"></a>關聯組態伺服器 

將複寫原則與您的內部部署組態伺服器建立關聯。

1. 按一下 [關聯]，選取組態伺服器。

    ![關聯組態伺服器](./media/vmware-azure-set-up-replication/associate1.png)

1. 按一下 [確定]。 組態伺服器應該會在 1 至 2 分鐘內產生關聯。

    ![關聯組態伺服器](./media/vmware-azure-set-up-replication/associate2.png)


## <a name="disassociate-or-delete-a-replication-policy"></a>中斷關聯或刪除複寫原則
1. 選擇複寫原則。
    a. 若要中斷原則與組態伺服器的關聯，請確定沒有複寫機器使用該原則。 然後按一下 [中斷關聯]。
    b. 若要刪除原則，請確定它沒有與組態伺服器相關聯。 然後按一下 [刪除]。 刪除應需要 30-60 秒。
1. 按一下 [確定]。
