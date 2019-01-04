---
title: 準備將 VMware 複寫至 Azure 的目標環境 | Microsoft Docs
description: 本文說明如何準備您的目標 Azure 環境，用於將 VMware 虛擬機器複寫至 Azure。
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: cbf9c1a49481de49fb8d9c6599acec7bc3600df3
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2018
ms.locfileid: "52837506"
---
# <a name="prepare-the-target-environment-for-disaster-recovery-of-vmware-vms-or-physical-servers-to-azure"></a>準備目標環境以進行從 VMware VM 或實體伺服器至 Azure 的災害復原

此文章將說明如何準備您的目標 Azure 環境，以開始將 VMware 虛擬機器或實體伺服器複寫至 Azure。

## <a name="prerequisites"></a>必要條件

本文假設：
- 您已經在 [Azure 入口網站](http://portal.azure.com "Azure 入口網站")上建立復原服務保存庫以保護您的來源機器
- 您已經將內部部署環境設定為將來源 [VMware 虛擬機器](vmware-azure-set-up-source.md)或[實體伺服器](physical-azure-set-up-source.md)複寫至 Azure。

## <a name="prepare-target"></a>準備目標

完成**步驟 1：選取保護目標**和**步驟 2：準備來源**之後，您會前往**步驟 3：目標**

![準備目標](./media/vmware-azure-set-up-target/prepare-target-vmware-to-azure.png)

1. **訂用帳戶：** 從下拉式功能表中選取您想要的訂用帳戶，以將虛擬機器或實體伺服器複寫至該訂用帳戶。
2. **部署模型：** 選取部署模型 (傳統或 Resource Manager)

根據所選的部署模型，會執行驗證以確保您的目標訂用帳戶中有至少一個相容的儲存體帳戶和虛擬網路，以便將您的虛擬機器或實體伺服器複寫和容錯移轉至其中。

驗證成功完成後，按一下 [確定] 以移至下一個步驟。

如果您沒有相容的資源管理員儲存體帳戶或虛擬網路，您可以按一下頁面頂端的 [+ 儲存體帳戶] 或 [+ 網路] 按鈕來建立一個。

## <a name="next-steps"></a>後續步驟
[設定複寫設定](vmware-azure-set-up-replication.md)。
