---
title: "準備內部部署 HYPER-V 伺服器到 Azure 的 HYPER-V Vm 的災害復原 |Microsoft 文件"
description: "了解如何準備內部部署 HYPER-V Vm 不受 System Center VMM 與 Azure Site Recovery 服務至 Azure 的災害復原。"
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 12/31/2017
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 6e2837341e16f5077cfff18d4a34c097c165ef09
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/03/2018
---
# <a name="prepare-on-premises-hyper-v-servers-for-disaster-recovery-to-azure"></a>準備內部部署 HYPER-V 伺服器到 Azure 的嚴重損壞修復

本教學課程會示範如何準備內部部署 HYPER-V 基礎結構，當您想要複寫至 Azure 時，HYPER-V Vm 進行災害復原。 由 System Center Virtual Machine Manager (VMM)，可以管理 HYPER-V 主機，但它不需要。  在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 檢閱 HYPER-V 需求和 VMM 需求，如果適用的話。
> * 準備 VMM，如果適用的話
> * 確認網際網路存取 Azure 位置
> * 準備 Vm，以便您可以存取它們容錯移轉至 Azure 之後

這是系列中的第二個教學課程。 確定您已如上一個教學課程中所述[設定 Azure 元件](tutorial-prepare-azure.md)。



## <a name="review-server-requirements"></a>檢閱伺服器需求

請確定 HYPER-V 主機符合下列需求。 如果您管理 System Center Virtual Machine Manager (VMM) 雲端中的主機，請確認 VMM 需求。


**元件** | **Hyper-v 由 VMM 管理** | **不具備 VMM Hyper-v**
--- | --- | ---
**HYPER-V 主機作業系統** | Windows Server 2016，2012 R2 | NA
**VMM** | VMM 2012、 VMM 2012 R2 | NA


## <a name="review-hyper-v-vm-requirements"></a>檢閱需求，HYPER-V 虛擬機器

請確定 VM 符合下表摘要說明的需求。

**VM 的需求** | **詳細資料**
--- | ---
**客體作業系統** | 任何客體 OS [Azure 支援](https://technet.microsoft.com/library/cc794868.aspx)。
**Azure 需求** | 內部部署 HYPER-V Vm 必須符合 Azure VM requirements(site-recovery-support-matrix-to-azure.md)。

## <a name="prepare-vmm-optional"></a>準備 VMM （選擇性）

如果 Hyper-V 主機由 VMM 管理，您需要準備內部部署 VMM 伺服器。 

- 請確定 VMM 伺服器至少具有一個雲端，以及一或多個主機群組。 執行 VM 的 Hyper-V 主機應該位於雲端。
- 準備 VMM 伺服器以進行網路對應。

### <a name="prepare-vmm-for-network-mapping"></a>準備 VMM 以進行網路對應

如果您使用 VMM，[網路對應](site-recovery-network-mapping.md)在內部部署 VMM VM 網路與 Azure 虛擬網路之間的對應。 對應可確保容錯移轉之後建立的 Azure VM 會連線到正確的網路。

準備 VMM 以進行網路對應，如下所示：

1. 請確定 Hyper-V 主機所在雲端有相關聯的 [VMM 邏輯網路](https://docs.microsoft.com/system-center/vmm/network-logical)。
2. 請確定您有 [VM 網路](https://docs.microsoft.com/system-center/vmm/network-virtual)連結至邏輯網路。
3. 在 VMM 中，連接到 VM 網路的 Vm。

## <a name="verify-internet-access"></a>確認網際網路存取

1. 基於本教學課程的目的，最簡單的組態適用於 HYPER-V 主機和 VMM 伺服器上，如果適用的話，可以直接存取網際網路，而不使用 proxy。 
2. 請確定該 HYPER-V 主機和 VMM 伺服器相關，如果可以存取這些 Url: 

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
3. 請確定：
    - 任何以 IP 位址為基礎的防火牆規則都應該允許對 Azure 的通訊。
    - 允許 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/confirmation.aspx?id=41653)和 HTTPS (443) 連接埠。
    - 允許訂用帳戶的 Azure 區域和美國西部 (用於存取控制和身分識別管理) 使用 IP 位址範圍。


## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>準備在容錯移轉後連接到 Azure VM

在容錯移轉狀況時，您可以連接到複寫在內部部署網路。

若要在容錯移轉後使用 RDP 連線到 Windows VM，請執行下列作業：

1. 若要透過網際網路存取，請在內部部署 VM 上啟用 RDP，再進行容錯移轉。 確定已針對 [公用] 設定檔新增 TCP 和 UDP 規則，且在 [Windows 防火牆] > [允許的應用程式] 中已針對所有設定檔允許 RDP。
2. 若要透過站對站 VPN 存取，請在內部部署機器上啟用 RDP。 您應該在 [Windows 防火牆] -> [允許的應用程式與功能] 中，針對 [網域] 和 [私人] 網路允許 RDP。
   確認作業系統的 SAN 原則已設為 [OnlineAll]。 [深入了解](https://support.microsoft.com/kb/3031135)。 觸發容錯移轉時，VM 上不應該有任何擱置的 Windows 更新。 如果有，在更新完成之前，您將無法登入虛擬機器。
3. 在容錯移轉之後，於 Windows Azure VM 上，勾選 [開機診斷] 以檢視 VM 的螢幕擷取畫面。 如果您無法連線，請檢查 VM 是否正在執行，並檢閱這些[疑難排解祕訣](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)。


## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [設定災害復原至 Azure 之 HYPER-V Vm](tutorial-hyper-v-to-azure.md)
> [災害復原至 Azure 之 HYPER-V Vm 中設定 VMM 雲端](tutorial-hyper-v-vmm-to-azure.md)
