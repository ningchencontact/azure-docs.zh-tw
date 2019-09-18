---
title: 準備 Azure 以使用 Azure Site Recovery 進行內部部署電腦的災害復原
description: 了解如何準備 Azure，以使用 Azure Site Recovery 進行內部部署電腦的災害復原。
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/09/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 1b8bdde64ee003d93ad15df8f1d4d8b1e3a2b5f9
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2019
ms.locfileid: "70814324"
---
# <a name="prepare-azure-resources-for-disaster-recovery-of-on-premises-machines"></a>準備 Azure 資源以進行內部部署電腦的災害復原

本文說明如何使用 [Azure Site Recovery](site-recovery-overview.md) 服務準備 Azure 資源和元件，以便設定內部部署 VMware VM、Hyper-V VM 或 Windows/Linux 實體伺服器至 Azure 的災害復原。

本文是一系列中的第一個教學課程，說明如何為內部部署 VM 設定災害復原。 


在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 確認 Azure 帳戶具有複寫權限。
> * 建立復原服務保存庫。 保存庫會保存 VM 的中繼資料和組態資訊，以及其他複寫元件。
> * 設定 Azure 虛擬網路 (VNet)。 在容錯移轉後建立的 Azure VM 會加入此網路。

> [!NOTE]
> 這些教學課程示範案例的最簡單部署路徑。 可能的話，會使用預設選項，而不會顯示所有可能的設定與路徑。 如需詳細指示，請檢閱 Site Recovery 目錄的「操作說明」區段中的文章。

## <a name="before-you-start"></a>開始之前

- 檢閱 [VMware](vmware-azure-architecture.md)、[Hyper-V](hyper-v-azure-architecture.md) 和[實體伺服器](physical-azure-architecture.md)災害復原的架構。
- 閱讀 [VMware](vmware-azure-common-questions.md) 和 [Hyper-V](hyper-v-azure-common-questions.md) 的常見問題

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/pricing/free-trial/) 。 然後登入 [Azure 入口網站](https://portal.azure.com)。


## <a name="verify-account-permissions"></a>確認帳戶權限

如果您剛建立免費的 Azure 帳戶，您就是您的訂用帳戶管理員且具備您所需的權限。 如果您不是訂用帳戶管理員，請與系統管理員合作以指派您所需的權限。 若要啟用新虛擬機器的複寫，您必須具有權限才能：

- 在所選的資源群組中建立 VM。
- 在所選的虛擬網路中建立 VM。
- 寫入至 Azure 儲存體帳戶。
- 寫入至 Azure 受控磁碟。

若要完成這些工作，應將虛擬機器參與者內建角色指派至您的帳戶。 此外，若要管理保存庫中的 Site Recovery 作業，應將 Site Recovery 參與者內建角色指派至您的帳戶。


## <a name="create-a-recovery-services-vault"></a>建立復原服務保存庫

1. 在 Azure 入口網站中按一下 [+建立資源]  ，然後在 Marketplace 中搜尋 [復原]  。
2. 按一下 [備份和 Site Recovery]  ，然後在 [備份和 Site Recovery] 頁面中按一下 [建立]  。 
1. 在 [復原服務保存庫]   > [名稱]  中，輸入用來識別保存庫的易記名稱。 在這一組教學課程中，我們會使用 **ContosoVMVault**。
2. 在 [資源群組]  中選取現有的資源群組，或建立新的資源群組。 在本教學課程中，我們將使用 **contosoRG**。
3. 在 [位置]  中，選取要設置保存庫的區域。 我們使用**西歐**。
4. 若要從儀表板快速存取保存庫，請按一下 [釘選到儀表板]   > [建立]  。

   ![建立新保存庫](./media/tutorial-prepare-azure/new-vault-settings.png)

   新的保存庫會出現在 [儀表板]   > [所有資源]  上，以及 [復原服務保存庫]  主頁面上。

## <a name="set-up-an-azure-network"></a>設定 Azure 網路

內部部署機器會複寫至 Azure 受控磁碟。 發生容錯移轉時，系統會從這些受控磁碟建立Azure VM，並將其加入您在此程序中指定的 Azure 網路。

1. 在 [Azure 入口網站](https://portal.azure.com)中，選取 [建立資源]   > [網路]   > [虛擬網路]  。
2. 保留選取 [Resource Manager]  作為部署模型。
3. 在 [名稱]  中，輸入網路名稱。 此名稱必須是 Azure 資源群組中的唯一名稱。 在本教學課程中，我們使用 **ContosoASRnet**。
4. 指定要在其中建立網路的資源群組。 我們使用現有的資源群組 **contosoRG**。
5. 在 [位址範圍]  中，輸入網路範圍。 我們正在使用 **10.1.0.0/24**，而不使用子網路。
6. 在 [訂用帳戶]  中，選取要在其中建立網路的訂用帳戶。
7. 在 [位置]  中，選取與在其中建立復原服務保存庫相同的區域。 在本教學課程中為 [西歐]  。 此網路必須位於與保存庫相同的區域中。
8. 我們保留基本 DDoS 保護的預設選項，且網路上沒有任何服務端點。
9. 按一下頁面底部的 [新增]  。

   ![建立虛擬網路](media/tutorial-prepare-azure/create-network.png)

虛擬網路的建立會耗用幾秒鐘時間。 建立後，您會在 Azure 入口網站儀表板中看到它。




## <a name="next-steps"></a>後續步驟

- 針對 VMware 災害復原，[準備內部部署 VMware 基礎結構](tutorial-prepare-on-premises-vmware.md)。
- 針對 Hyper-V 災害復原，[準備內部部署 Hyper-V 伺服器](hyper-v-prepare-on-premises-tutorial.md)。
- 針對實體伺服器災害復原，[設定組態伺服器和來源環境](physical-azure-disaster-recovery.md)。
- [了解](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) Azure 網路。
- [了解](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview)受控磁碟。
