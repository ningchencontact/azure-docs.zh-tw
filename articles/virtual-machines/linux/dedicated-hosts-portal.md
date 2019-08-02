---
title: 使用 Azure 入口網站部署 Azure 專用主機 |Microsoft Docs
description: 使用 Azure 入口網站將 Vm 部署到專用主機。
services: virtual-machines-linux
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/25/2019
ms.author: cynthn
ms.openlocfilehash: 03d8b5353a2dc9840c8231978243e7f94a5bff56
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2019
ms.locfileid: "68700388"
---
# <a name="preview-deploy-vms-to-dedicated-hosts-using-the-portal"></a>預覽：使用入口網站將 Vm 部署至專用主機

本文會引導您瞭解如何建立 Azure[專用主機](dedicated-hosts.md)來裝載您的虛擬機器 (vm)。 

[!INCLUDE [virtual-machines-common-dedicated-hosts-portal](../../../includes/virtual-machines-common-dedicated-hosts-portal.md)]

## <a name="create-a-vm"></a>建立 VM

1. 選擇 Azure 入口網站左上角的 [建立資源]。
1. 在 Azure Marketplace 資源清單上方的搜尋方塊中，依 Canonical 搜尋並選取**Ubuntu Server 16.04 LTS**，然後選擇 [建立]。
1. 在 [**基本**] 索引標籤的 [**專案詳細資料**] 底下, 確認已選取正確的訂用帳戶, 然後選取 [ *myDedicatedHostsRG* ] 作為**資源群組**。 
1. 在 [執行個體詳細資料] 底下，輸入 myVM 作為 [虛擬機器名稱]，然後選擇 [美國東部] 作為您的 [位置]。
1. 在 [**可用性選項**] 中, 選取 [**可用性區域**], 從下拉式選單選取*1* 。
1. 針對 [大小], 選取 [**變更大小**]。 在可用大小清單中, 從 Esv3 系列選擇一個, 例如 [**標準 E2s v3**]。 您可能需要清除篩選準則, 才能看到所有可用的大小。
1. 在 [系統管理員帳戶] 下方選取 [SSH 公開金鑰]，輸入您的使用者名稱，然後將您的公開金鑰貼到文字方塊中。 請移除公開金鑰中的任何前置或尾端的空白字元。

    ![Administrator 帳戶](./media/quick-create-portal/administrator-account.png)

1. 在 [**輸入連接埠規則** > ] [**公用輸入埠**] 底下, 選擇 [**允許選取的埠**], 然後從下拉式選單選取 [ **SSH (22)** ]。 
1. 在頁面頂端,選取 [ **Advanced** ] 索引標籤, 然後在 [**主機**] 區段中, 針對 [**主機群組**] 和 [ *myhost 代表*] 選取 [ *myHostGroup* ]。 
    ![選取主機群組和主機](./media/dedicated-hosts-portal/advanced.png)
1. 保留其餘預設值，然後在頁面底部選取 [檢閱 + 建立] 按鈕。
1. 當您看到驗證已通過的訊息時, 請選取 [**建立**]。

可能需要幾分鐘的時間才能部署好 VM。

## <a name="next-steps"></a>後續步驟

- 如需詳細資訊, 請參閱[專用主機](dedicated-hosts.md)總覽。

- [這裡](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)有範例範本, 它會使用區域和容錯網域來取得區域中的最大復原。

- 您也可以使用[Azure CLI](dedicated-hosts-cli.md)部署專用主機。



