---
title: Azure Site Recovery 更新 | Microsoft Docs
description: 提供服務更新及如何升級 Azure Site Recovery 中所用元件的概觀。
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 01/8/2019
ms.author: rajanaki
ms.openlocfilehash: 3e5f84a6f05e451b1eafa98c373f9d838421016e
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2019
ms.locfileid: "55229294"
---
# <a name="service-updates-in-azure-site-recovery"></a>Azure Site Recovery 中的服務更新
作為一個組織，您必須了解如何維持資料安全，以及發生計劃性和非計劃性中斷時，如何讓應用程式/工作負載持續執行。 Azure Site Recovery 可在網站關閉時，讓您的應用程式持續在可用的 VM 和實體伺服器上執行，藉此參與您的 BCDR 策略。 Site Recovery 會複寫在 VM 和實體伺服器上執行的工作負載，因此如果主要網站無法使用，仍可在次要位置中使用工作負載。 當主要網站再次開始運作及執行時，它會將工作負載復原至其中。

Site Recovery 可以管理複寫：

- [在 Azure 區域間複寫 Azure VM](azure-to-azure-tutorial-dr-drill.md)。
- 將內部部署虛擬機器和實體伺服器複寫至 Azure 或次要網站。
若要深入了解，請參閱[這裡](https://docs.microsoft.com/azure/site-recovery)的文件。

Azure Site Recovery 會定期發佈服務更新，包括加入新功能、改善支援矩陣，以及錯誤 (bug) 修正。 若要保持最新狀態，請利用所有的最新功能、增強功能和錯誤 (bug) 修正 (如果有的話)，建議使用者一律更新至最新版的 Azure SIte Recovery 元件。 
 
## <a name="support-statement-for-azure-site-recovery"></a>Azure Site Recovery 的支援聲明 

> [!IMPORTANT]
> **每當發行新的 Azure Site Recovery 元件版本 'N' 時，'N-4' 以下的所有版本就會被視為不支援的版本**。 因此，我們一律建議您升級到可用的最新版本。

> [!IMPORTANT]
> 正式的升級支援是從 > N-4 版本升級至 N 版本 (N 是最新版本)。 如果您位於 N-6，則必須先升級至 N-4，再升級至 N。

### <a name="upgrading-when-the-difference-between-current-version-and-latest-released-version-is-greater-than-4"></a>當目前版本與最新發行的版本之間的差異大於 4 時升級

1. 第一個步驟，將目前安裝的元件從版本 N 升級至 N+4，然後移至下一個相容版本。 假設目前版本為 9.24，而且您位於 9.16 上，請先升級至 9.20，再升級至 9.24。
2. 視情節而定，對所有元件遵循相同的程序。

### <a name="support-for-latest-oskernel-versions"></a>支援最新 OS/核心版本

> [!NOTE]
> 如果您已排定維護期間，而且重新開機屬於相同期間，建議您先升級 Site Recovery 元件，再繼續進行其餘的排定活動。

1. 在升級核心/OS 版本之前，請先確認 Azure Site Recovery 是否支援目標版本。 您可以在我們的 Azure VM、[VMware VM](vmware-physical-azure-support-matrix.md) 和 Hyper-V VM 文件中找到此資訊。
2. 請參閱我們的[服務更新](https://azure.microsoft.com/updates/?product=site-recovery)，找出哪個版本的 Site Recovery 元件支援您要升級至的特定版本。
3. 首先，升級至最新的 Site Recovery 版本。
4. 現在，將 OS/核心升級至所需的版本。
5. 執行重新開機。
6. 這可確保您機器上的 OS/核心版本已升級至最新版本，而且也在來源機器上載入支援新版本所需的最新 Site Recovery 變更。



## <a name="azure-vm-disaster-recovery-to-azure"></a>Azure VM 至 Azure 災害復原
在此案例中，強烈建議您[啟用](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-autoupdate)自動更新。 您可以選擇允許 Site Recovery 透過下列方式管理更新：

- 在啟用複寫步驟的過程
- 切換保存庫內的擴充功能更新設定

如果您已選擇手動管理更新，請遵循下列步驟：

1. 移至 Azure 入口網站，然後瀏覽至您的「復原服務保存庫」。
2. 移至 Azure 入口網站中「復原服務保存庫」的 [複寫的項目] 窗格。
3. 按一下畫面頂端的下列通知：
    
    *有新的 Site Recovery 複寫代理程式更新可用*
    
    *按一下以安裝 ->*

4. 選取您要套用更新的 VM，然後按一下 [確定]。

## <a name="between-two-on-premises-vmm-sites"></a>在兩個內部部署 VMM 網站之間
1. 下載 Microsoft Azure Site Recovery 提供者的最新更新彙總套件。
2. 先在管理復原網站在內部部署 VMM 伺服器上安裝更新彙總套件。
3. 更新復原網站之後，在管理主要網站的 VMM 伺服器上安裝更新彙總套件。

> [!NOTE]
> 如果 VMM 是高可用性 VMM (叢集式 VMM)，請務必在 VMM 服務安裝所在叢集的所有節點上安裝升級。

## <a name="between-an-on-premises-vmm-site-and-azure"></a>在內部部署 VMM 網站及 Azure 之間
1. 下載 Microsoft Azure Site Recovery 提供者的更新彙總套件。
2. 在內部部署 VMM 伺服器上安裝更新彙總套件。
3. 在所有 Hyper-V 主機上安裝最新代理程式 MARS 代理程式。

> [!NOTE]
> 如果 VMM 是高可用性 VMM (叢集式 VMM)，請務必在 VMM 服務安裝所在叢集的所有節點上安裝升級。

## <a name="between-an-on-premises-hyper-v-site-and-azure"></a>在內部部署 Hyper-V 網站與 Azure 之間

1. 下載 Microsoft Azure Site Recovery 提供者的更新彙總套件。
2. 在您於 Azure Site Recovery 中註冊的 Hyper-V 伺服器的每個節點上安裝提供者。

> [!NOTE]
> 如果 Hyper-V 是主機叢集式 Hyper-V 伺服器，請務必在叢集的所有節點上安裝升級

## <a name="between-an-on-premises-vmware-or-physical-site-to-azure"></a>在內部部署 VMware 或 Azure 實體網站之間

1. 先在內部部署管理伺服器上安裝更新。 這是具備組態伺服器和處理序伺服器角色的伺服器。 
2. 如果您已相應放大處理序伺服器，請接著更新這些伺服器。
3. 移至 Azure 入口網站，然後移至 [受保護的項目] > [複寫的項目] 頁面。
在此頁面上選取 VM。 選取出現在每部 VM 頁面底部的 [更新代理程式] 按鈕。 這會在所有受保護的 VM 上更新行動服務代理程式。

建議在每次升級行動代理程式之後重新開機，以確保在來源機器上載入所有的最新變更。 不過，**並非強制**。 如果上次重新開機期間的代理程式版本與目前版本之間的差異大於 4，則必須重新開機。 請參考下表中的詳細說明。

|**上次重新開機期間的代理程式版本** | **升級至** | **是否強制重新啟動？**|
|---------|---------|---------|--------|
|9.16 |  9.18 | 非強制性|
|9.16 | 9.19 | 非強制性|
| 9.16 | 9.20 | 非強制性
 | 9.16 | 9.21 | 是，先升級至 9.20，然後在升級至 9.21 之前重新開機，因為版本 (執行上次重新開機的 9.16 與目標版本 9.21) 之間的差異小於 4。



## <a name="links-to-currently-supported-update-rollups"></a>目前支援的更新彙總套件連結


|更新彙總套件  |提供者  |統一安裝| OVF  |MARS|
|---------|---------|---------|---------|--------|
|[更新彙總套件 32](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)     |   5.1.3800.0  |  9.21.5091.1   |  5.1.3800.0  |2.0.9144.0
|[更新彙總套件 31](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)     |     5.1.3700.0      |   9.20.5051.1      |     5.1.3700.0    |2.0.9144.0
|[更新彙總套件 30](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30)     |    5.1.3650.0   |   9.19.5007.1    |     5.1.3650.0    |2.0.9139.0
|[更新彙總套件 29](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery)     |   5.1.3650.0      |   9.19.4973.1     |     5.1.3700.0    |2.0.9131.0
|[更新彙總套件 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery)     |  5.1.3600 .0      |    9.19.4973.1     |  5.1.3600.0       |2.0.9131.0
| [更新彙總套件 27](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery)       |   5.1.3550.0      |    9.18.4946.1     | 5.1.3550.0         |2.0.9125.0


## <a name="previous-update-rollups"></a>先前的更新彙總套件
- [更新彙總套件 26](https://support.microsoft.com/help/4344054/update-rollup-26-for-azure-site-recovery)  
- [更新彙總套件 25](https://support.microsoft.com/help/4278275/update-rollup-25-for-azure-site-recovery) 
- [更新彙總套件 23](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery) 
- [更新彙總套件 22](https://support.microsoft.com/help/4072852/update-rollup-22-for-azure-site-recovery) 
- [更新彙總套件21](https://support.microsoft.com/help/4051380/update-rollup-21-for-azure-site-recovery) 
- [更新彙總套件 20](https://support.microsoft.com/help/4041105/update-rollup-20-for-azure-site-recovery) 
- [更新彙總套件 19](https://support.microsoft.com/help/4034599/update-rollup-19-for-azure-site-recovery) 
