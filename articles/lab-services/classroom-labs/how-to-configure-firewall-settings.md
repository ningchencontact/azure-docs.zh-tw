---
title: Azure 實驗室服務的防火牆設定
description: 瞭解如何判斷實驗室中虛擬機器的公用 IP 位址和埠號碼範圍，讓資訊可以新增至防火牆規則。
author: emaher
ms.author: enewman
ms.date: 12/12/2019
ms.topic: article
ms.service: lab-services
ms.openlocfilehash: da1614e4a3e02ed91ef2d3c59ac4eb3eac0dcc7c
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/07/2020
ms.locfileid: "75692767"
---
# <a name="firewall-settings-for-azure-lab-services"></a>Azure 實驗室服務的防火牆設定

每個組織或學校都會以最符合其需求的方式設定自己的網路。  有時，包括設定防火牆規則，以封鎖遠端桌面通訊協定（rdp）或安全殼層（ssh）連線到自己網路外的電腦。  因為 Azure 實驗室服務是在公用雲端中執行，所以可能需要一些額外的設定，才能讓學生在從校園網路連線時存取其 VM。

每個實驗室都使用單一公用 IP 位址和多個埠。  所有 Vm （範本 VM 和學生 Vm）都會使用此公用 IP 位址。  在實驗室的生命週期中，公用 IP 位址不會變更。  不過，每個 VM 都會有不同的埠號碼。  埠號碼的範圍從49152到65535。  公用 IP 位址和埠號碼的組合是用來將講師和學生連線到正確的 VM。  本文將討論如何尋找實驗室所使用的特定公用 IP 位址。  該資訊可以用來更新輸入和輸出防火牆規則，讓學生可以存取其 Vm。

>[!IMPORTANT]
>每個實驗室都有不同的公用 IP 位址。

## <a name="find-public-ip-for-a-lab"></a>尋找實驗室的公用 IP

每個實驗室的公用 IP 位址會列在實驗室服務實驗室帳戶的 [**所有實驗室**] 分頁中。  如需如何尋找 [**所有實驗室**] 分頁的指示，請參閱[如何在實驗室帳戶中管理實驗室](how-to-manage-lab-accounts.md#view-and-manage-labs-in-the-lab-account)。  

> [!div class="mx-imgBorder"]
> ![所有實驗室 分頁](../media/how-to-configure-firewall-settings/all-labs-properties.png)

>[!NOTE]
>如果您實驗室的範本電腦尚未發佈，您將不會看到公用 IP 位址。

## <a name="conclusion"></a>結論

現在我們知道實驗室的公用 IP 位址。  您可以為組織的防火牆建立公用 ip 位址和埠範圍49152-65535 的輸入和輸出規則。  更新規則之後，學生就可以存取其 Vm，而不會有網路防火牆封鎖存取。
