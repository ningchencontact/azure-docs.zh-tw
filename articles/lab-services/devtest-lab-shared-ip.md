---
title: 了解 Azure DevTest Labs 中的共用 IP 位址 | Microsoft Docs
description: 了解 Azure DevTest Labs 如何使用共用 IP 位址，將需要存取您的實驗室 VM 的公用 IP 位址減到最少。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/12/2019
ms.author: spelluru
ms.openlocfilehash: f7c9feedddab1aea031cb3a8879e868aae04df00
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2019
ms.locfileid: "65236889"
---
# <a name="understand-shared-ip-addresses-in-azure-devtest-labs"></a>了解 Azure DevTest Labs 中的共用 IP 位址

Azure DevTest Labs 讓實驗室 VM 共用相同公用 IP 位址，將需要存取您的個人實驗室 VM 的公用 IP 位址數目減到最少。  本文說明共用 IP 的運作方式和其相關的組態選項。

## <a name="shared-ip-setting"></a>共用 IP 設定

當您建立實驗室時，它會建立虛擬網路的子網路中。  根據預設，建立這個子網路時會將 [啟用共用公用 IP]設為 [是]。  此設定會為整個子網路建立一個公用 IP 位址。  如需設定虛擬網路和子網路的詳細資訊，請參閱[設定 Azure DevTest Labs 中的虛擬網路](devtest-lab-configure-vnet.md)。

![新的實驗室子網路](media/devtest-lab-shared-ip/lab-subnet.png)

針對現有的實驗室，您可以藉由選取 [設定和原則 > 虛擬網路] 來啟用這個選項。 然後，從清單中選取虛擬網路，並且針對選取的子網路選擇 [啟用共用公用 IP]。 如果您不想要在實驗室 Vm 之間共用的公用 IP 位址，您也可以停用任何實驗室中的這個選項。

此實驗室中建立任何 VM 會預設為使用共用 IP。  此設定在建立 VM 時，會出現在**進階設定**下方的頁面上**IP 位址設定**。

![新的 VM](media/devtest-lab-shared-ip/new-vm.png)

- **共用：** 建立的所有 Vm**共用**會歸類到一個資源群組 (RG)。 針對該 RG 指派單一 IP 位址，RG 中的所有 VM 將會使用該 IP 位址。
- **公用︰** 您所建立的每個 VM 都有它自己的 IP 位址，並建立自己的資源群組中。
- **私人：** 您所建立的每個 VM 會使用私人 IP 位址。 您無法直接從網際網路的遠端桌面連接到此 VM。

每當使用已啟用共用 IP 的 VM 新增至子網路時，DevTest Labs 自動將 VM 加入至負載平衡器，並指派公用 IP 位址的 TCP 連接埠號碼將轉送到 VM 的 RDP 連接埠。  

## <a name="using-the-shared-ip"></a>使用共用 IP

- **Linux 使用者：** Ssh 連線到 VM 所使用的 IP 位址或完整的網域名稱，後面接著冒號，後面接著連接埠。 例如，在下圖中，連線至 VM 的 RDP 位址是 `mydevtestlab597975021002.eastus.cloudapp.azure.com:50661`。

  ![VM 範例](media/devtest-lab-shared-ip/vm-info.png)

- **Windows 使用者：** 選取  **Connect**下載預先設定的 RDP 檔案，並存取虛擬機器在 Azure 入口網站上的按鈕。

## <a name="next-steps"></a>後續步驟

* [在 Azure DevTest Labs 中定義實驗室原則](devtest-lab-set-lab-policy.md)
* [在 Azure DevTest Labs 中設定虛擬網路](devtest-lab-configure-vnet.md)





