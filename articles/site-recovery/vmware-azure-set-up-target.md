---
title: 準備將 VMware 複寫至 Azure 的目標環境 | Microsoft Docs
description: 本文說明如何準備您的目標 Azure 環境，用於將 VMware 虛擬機器複寫至 Azure。
services: site-recovery
author: bsiva
manager: abhemraj
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: bsiva
ms.openlocfilehash: 4b428dff1cebf353cc081696649494e6e4ec9b92
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2018
ms.locfileid: "37921105"
---
# <a name="prepare-the-target-environment-for-vmware-replication-to-azure"></a>準備將 VMware 複寫至 Azure 的目標環境

本文將說明如何準備您的目標 Azure 環境，以開始將 VMware 虛擬機器複寫至 Azure。

## <a name="prerequisites"></a>先決條件

本文假設：
- 您已建立復原服務保存庫，用以保護 VMware 虛擬機器。 您可以從 [Azure 入口網站] (http://portal.azure.com "Azure 入口網站")建立復原服務保存庫。
- 您已[設定內部部署環境](vmware-azure-set-up-source.md)以將 VMware 虛擬機器複寫至 Azure。

## <a name="prepare-target"></a>準備目標

完成**步驟 1：選取保護目標**和**步驟 2︰準備來源**之後，即會進入**步驟 3︰目標**

![準備目標](./media/vmware-azure-set-up-target/prepare-target-vmware-to-azure.png)

1. **訂用帳戶︰** 從下拉式功能表中選取您想要的訂用帳戶，以將虛擬機器複寫至該訂用帳戶。
2. **部署模型︰** 選取部署模型 (傳統或資源管理員)

根據所選的部署模型，會執行驗證以確保您的目標訂用帳戶中有至少一個相容的儲存體帳戶和虛擬網路，以便將您的虛擬機器複寫和容錯移轉至其中。

驗證成功完成後，按一下 [確定] 以移至下一個步驟。

如果您沒有相容的資源管理員儲存體帳戶或虛擬網路，您可以按一下頁面頂端的 [+ 儲存體帳戶] 或 [+ 網路] 按鈕來建立一個。

## <a name="next-steps"></a>後續步驟
[設定複寫設定](vmware-azure-set-up-replication.md)。
