---
title: Azure Stack 中的配額類型 | Microsoft Docs
description: 檢閱及編輯可供 Azure Stack 中服務和資源使用的各種不同配額類型。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: sethm
ms.reviewer: xiaofmao
ms.lastreviewed: 12/07/2018
ms.openlocfilehash: aff9dade7fe0238c0ea8ccc3ae5bba57437c6f89
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/21/2019
ms.locfileid: "58339539"
---
# <a name="quota-types-in-azure-stack"></a>Azure Stack 中的配額類型

*適用於：Azure Stack 整合式系統和 Azure Stack 開發套件*

[配額](azure-stack-plan-offer-quota-overview.md#plans)會定義使用者訂用帳戶可以佈建或取用的資源限制。 例如，配額可能會允許使用者建立最多五個 VM。 每個資源都可以有自己的配額類型。

## <a name="compute-quota-types"></a>計算配額類型

| **類型** | **預設值** | **說明** |
| --- | --- | --- |
| 虛擬機器的數目上限 | 50 | 訂用帳戶可以在這個位置建立的虛擬機器數目上限。 |
| 虛擬機器核心的數目上限 | 100 | 訂用帳戶可以在這個位置建立的核心數目上限 (例如，A3 VM 有四個核心)。 |
| 可用性設定組的數目上限 | 10 | 可以在這個位置建立的可用性設定組數目上限。 |
| 虛擬機器擴展集的數目上限 | 100 | 可以在這個位置建立的虛擬機器擴展集數目上限。 |
| 標準受控磁碟的容量上限 (以 GB 為單位) | 2048 | 可以在這個位置建立的標準受控磁碟容量上限。 |
| 進階受控磁碟的容量上限 (以 GB 為單位) | 2048 | 可以在這個位置建立的進階受控磁碟容量上限。 |

## <a name="storage-quota-types"></a>儲存體配額類型

| **Item** | **預設值** | **說明** |
| --- | --- | --- |
| 最大容量 (GB) |2048 |訂用帳戶可以在此位置中使用的儲存容量總計 (包括 Blob 和所有相關聯的快照集、資料表、佇列)。 |
| 儲存體帳戶的總數 |20 |訂用帳戶可以在這個位置建立的儲存體帳戶數目上限。 |

> [!NOTE]  
> 最多可能需要兩個小時，才會強制使用儲存體配額。

## <a name="network-quota-types"></a>網路配額類型

| **Item** | **預設值** | **說明** |
| --- | --- | --- |
| 公用 IP 數目上限 |50 |訂用帳戶可以在這個位置建立的公用 IP 數目上限。 |
| 虛擬網路數目上限 |50 |訂用帳戶可以在這個位置建立的虛擬網路數目上限。 |
| 虛擬網路閘道數目上限 |1 |訂用帳戶可以在這個位置建立的虛擬網路閘道 (VPN 閘道) 數目上限。 |
| 網路連線數目上限 |2 |訂用帳戶可以在這個位置所有虛擬網路閘道上建立的網路連線 (點對點或站台對站台) 數目上限。 |
| 負載平衡器數目上限 |50 |訂用帳戶可以在這個位置建立的負載平衡器數目上限。 |
| NIC 數目上限 |100 |訂用帳戶可以在這個位置建立的網路介面數目上限。 |
| 網路安全性群組數目上限 |50 |訂用帳戶可以在這個位置建立的網路安全性群組數目上限。 |

## <a name="view-an-existing-quota"></a>檢視現有的配額

有兩種不同的方式可檢視現有的配額：

### <a name="plans"></a>方案

1. 在系統管理員入口網站的左側導覽窗格中，選取 [方案]。
2. 選取您想要檢視詳細資料的方案，方法是按一下其名稱。
3. 在開啟的刀鋒視窗中，選取 [服務和配額]。
4. 選取您想要查看的配額，方法是在 [名稱] 資料行中按一下該配額。

    [![配額](media/azure-stack-quota-types/quotas1sm.png "檢視配額")](media/azure-stack-quota-types/quotas1.png#lightbox)

### <a name="resource-providers"></a>資源提供者

1. 在系統管理員入口網站的預設儀表板上，尋找 [資源提供者] 圖格。
2. 選取您想要檢視配額的服務，例如 [計算]、[網路] 或 [儲存體]。
3. 選取 [配額]，然後選取要檢視的配額。

## <a name="edit-a-quota"></a>編輯配額

有兩種不同的方式可編輯配額：

### <a name="edit-a-plan"></a>編輯方案

1. 在系統管理員入口網站的左側導覽窗格中，選取 [方案]。
2. 選取您想要編輯配額的方案，方法是按一下其名稱。
3. 在開啟的刀鋒視窗中，選取 [服務和配額]。
4. 選取您想要編輯的配額，方法是在 [名稱] 資料行中按一下該配額。
    [![配額](media/azure-stack-quota-types/quotas1sm.png "檢視配額")](media/azure-stack-quota-types/quotas1.png#lightbox)

5. 在開啟的刀鋒視窗中，選取 [Edit in Compute] \(在計算中編輯\)、[Edit in Network] \(在網路中編輯\) 或 [Edit in Storage] \(在儲存體中編輯\)。
    ![配額](media/azure-stack-quota-types/quotas3.png "檢視配額")

或者，您也可以依照下列程序來編輯配額：

1. 在系統管理員入口網站的預設儀表板上，尋找 [資源提供者] 圖格。
2. 選取您要修改其配額的服務，像是 [計算]、[網路] 或 [儲存體]。
3. 接下來，選取 [配額]，然後選取您要變更的配額。
4. 在 [設定儲存體配額]、[設定計算配額] 或 [設定網路配額] 窗格上 (視您選擇的要編輯的配額類型而定)，編輯值，然後選取 [儲存]。

### <a name="edit-original-configuration"></a>編輯原始設定
  
您可以選擇編輯配額的原始組態，而不是[使用附加方案](create-add-on-plan.md)。 當您編輯配額時，新的組態會自動全面套用至使用該配額的所有方案，以及使用這些方案的所有現有訂用帳戶。 配額的編輯方式不同於當您使用附加方案來提供修改過的配額時，這是使用者選擇要訂閱的方式。

此配額的新值會全面套用至使用已修改配額的所有方案，以及使用這些方案的所有現有訂用帳戶。

## <a name="next-steps"></a>後續步驟

- [深入了解方案、供應項目與配額。](azure-stack-plan-offer-quota-overview.md)
- [建立方案時建立配額。](azure-stack-create-plan.md)
