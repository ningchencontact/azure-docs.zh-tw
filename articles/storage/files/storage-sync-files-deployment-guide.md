---
title: "部署 Azure 檔案同步 (預覽) | Microsoft Docs"
description: "了解部署 Azure 檔案同步的完整程序。"
services: storage
documentationcenter: 
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2017
ms.author: wgries
ms.openlocfilehash: d5864b8df85a5b3cec086d4cb2edc6d288f1639a
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2018
---
# <a name="deploy-azure-file-sync-preview"></a>部署 Azure 檔案同步 (預覽)
使用 Azure 檔案同步 (預覽版)，將組織的檔案共用集中在 Azure 檔案服務中，同時保有內部部署檔案伺服器的靈活度、效能及相容性。 Azure 檔案同步會將 Windows Server 轉換成 Azure 檔案共用的快速快取。 您可以使用 Windows Server 上可用的任何通訊協定來從本機存取資料，包括 SMB、NFS 和 FTPS。 您可以視需要存取多個散佈於世界各地的快取。

強烈建議您先閱讀[規劃 Azure 檔案服務部署](storage-files-planning.md)和[規劃 Azure 檔案同步部署](storage-sync-files-planning.md)，再完成本文章中描述的步驟。

## <a name="prerequisites"></a>先決條件
* 位於和您要部署 Azure 檔案同步相同之區域中的 Azure 儲存體帳戶和 Azure 檔案共用。如需詳細資訊，請參閱
    - Azure 檔案同步的[區域可用性](storage-sync-files-planning.md#region-availability)。
    - 如需如何建立儲存體帳戶的逐步說明，請參閱[建立儲存體帳戶](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。
    - [建立檔案共用](storage-how-to-create-file-share.md)以取得如何建立檔案共用的逐步說明。
* 至少有一個 Windows Server 或 Windows Server 叢集的受支援執行個體，以與 Azure 檔案同步進行同步處理。如需 Windows Server 受支援版本的詳細資訊，請參閱[與 Windows Server 的互通性](storage-sync-files-planning.md#azure-file-sync-interoperability)。

## <a name="deploy-the-storage-sync-service"></a>部署儲存體同步服務 
儲存體同步服務是 Azure 檔案同步的最上層 Azure 資源。若要部署儲存體同步服務，請移至 [Azure 入口網站](https://portal.azure.com/)，按一下 [新增] 並搜尋 Azure 檔案同步。在搜尋結果中，選取 [Azure 檔案同步 (預覽)]，然後選取 [建立] 以開啟 [部署儲存體同步] 索引標籤。

在開啟的窗格中，輸入下列資訊：

- **名稱**：儲存體同步服務的唯一名稱 (每一訂用帳戶)。
- **訂用帳戶**：您要在其中建立儲存體同步服務的訂用帳戶。 視您組織的設定策略而定，您可能具有一或多個訂用帳戶的存取權限。 Azure 訂用帳戶是針對每個雲端服務 (例如 Azure 檔案服務) 計費的最基本容器。
- **資源群組**：資源群組是 Azure 資源的邏輯群組，例如儲存體帳戶或儲存體同步服務。 針對 Azure 檔案同步，您可以建立新的資源群組或使用現有的資源群組。(建議使用資源群組作為容器，來以邏輯方式為您的組織隔離資源，例如將 HR 資源或適用於特定專案的資源組成群組)。
- **位置**：您要在其中部署 Azure 檔案同步的區域。只有支援的區域會列在此清單中。

完成時，請選取 [建立] 來部署儲存體同步服務。

## <a name="prepare-windows-server-to-use-with-azure-file-sync"></a>準備 Windows Server 以搭配 Azure 檔案同步使用
針對您要搭配 Azure 檔案同步使用的每部伺服器 (包括容錯移轉叢集中的伺服器節點)，請完成下列步驟：

1. 停用 [Internet Explorer 增強式安全性設定]。 此動作只需要在初始伺服器註冊時執行。 您可以在註冊伺服器後重新啟用它。
    1. 開啟 [伺服器管理員]。
    2. 按一下 [本機伺服器]：  
        ![位於伺服器管理員 UI 左側的 [本機伺服器]](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
    3. 選取 [屬性] 子窗格上的 [IE 增強式安全性設定] 連結。  
        ![位於伺服器管理員 UI 中的 [IE 增強式安全性設定] 窗格](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG)
    4. 在 [Internet Explorer 增強式安全性設定] 對話方塊中，針對 [系統管理員] 和 [使用者] 選取 [關閉]：  
        ![已選取 [關閉] 的 [Internet Explorer 增強式安全性設定] 快顯視窗](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

2. 請確定您至少執行 PowerShell 5.1.\*(Windows Server 2016 上預設為 PowerShell 5.1)。 您可以查看 **$PSVersionTable** 物件的 **PSVersion** 屬性值，確定執行的是否為 PowerShell 5.1.\*：

    ```PowerShell
    $PSVersionTable.PSVersion
    ```

    如果您的 PSVersion 值小於 5.1.\* (這會發生於大部分的 Windows Server 2012 R2 安裝)，您可以下載並安裝 [Windows Management Framework (WMF) 5.1](https://www.microsoft.com/download/details.aspx?id=54616) \(英文\) 來輕鬆地升級。 適用於 Windows Server 2012 R2 下載和安裝的適當套件為 **Win8.1AndW2K12R2-KB\*\*\*\*\*\*\*-x64.msu**。

3. [安裝和設定 Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)。 建議您使用最新版的 Azure PowerShell 模組。

## <a name="install-the-azure-file-sync-agent"></a>安裝 Azure 檔案同步代理程式
Azure 檔案同步代理程式是可下載的套件，可讓 Windows Server 能夠和 Azure 檔案共用進行同步處理。 您可以從 [Microsoft 下載中心](https://go.microsoft.com/fwlink/?linkid=858257) \(英文\) 下載該代理程式。 下載之後，請按兩下 MSI 套件以啟動 Azure 檔案同步代理程式安裝。

> [!Important]  
> 如果您打算將 Azure 檔案同步搭配容錯移轉叢集使用，則必須在叢集中的每個節點上安裝 Azure 檔案同步代理程式。


Azure 檔案同步代理程式安裝套件的安裝速度應該相當快速，而不會出現太多其他提示。 建議您採取下列動作︰
- 保留預設的安裝路徑 (C:\Program Files\Azure\StorageSyncAgent)，以簡化疑難排解和伺服器維護。
- 啟用 Microsoft Update 以將 Azure 檔案同步保持在最新狀態。 Azure 檔案同步代理程式的所有更新 (包括功能更新和 Hotfix) 都會從 Microsoft Update 進行。 建議安裝最新的 Azure 檔案同步更新。如需詳細資訊，請參閱 [Azure 檔案同步更新原則](storage-sync-files-planning.md#azure-file-sync-agent-update-policy)。

當 Azure 檔案同步代理程式安裝結束時，伺服器註冊 UI 會自動開啟。 請參閱下一節以了解如何向 Azure 檔案同步註冊此伺服器。

## <a name="register-windows-server-with-storage-sync-service"></a>向儲存體同步服務註冊 Windows Server
向儲存體同步服務註冊 Windows Server，會在您的伺服器 (或叢集) 與儲存體同步服務之間建立信任關係。 安裝 Azure 檔案同步代理程式之後，伺服器註冊 UI 應該會自動開啟。 如果沒有，您可以從其檔案位置手動開啟它：C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe。 伺服器註冊 UI 開啟時，請選取 [登入] 以開始。

登入之後，系統會提示您輸入下列資訊：

![伺服器註冊 UI 的螢幕擷取畫面](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Azure 訂用帳戶**：包含儲存體同步服務的訂用帳戶 (請參閱[部署儲存體同步服務](#deploy-the-storage-sync-service))。 
- **資源群組**：包含儲存體同步服務的資源群組。
- **儲存體同步服務**：您要註冊的目標儲存體同步服務名稱。

選取適當的資訊之後，請選取 [註冊] 以完成伺服器註冊。 在註冊過程中，系統會提示您額外再登入一次。

## <a name="create-a-sync-group"></a>建立同步群組
同步群組定義一組檔案的同步拓撲。 同步群組內的端點會與彼此保持同步。 同步群組必須包含至少一個雲端端點 (代表 Azure 檔案共用) 和一個伺服器端點 (代表 Windows Server 上的路徑)。 若要建立同步群組，請在 [Azure 入口網站](https://portal.azure.com/)中移至您的儲存體同步服務，然後選取 [+ 同步群組]：

![在 Azure 入口網站中建立新的同步群組](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

在開啟的窗格中，輸入下列資訊以建立具有雲端端點的同步群組：

- **同步群組名稱**：要建立的同步群組名稱。 此名稱在儲存體同步服務中必須是唯一的，但可以是任何對您而言合理的名稱。
- **訂用帳戶**：您在[部署儲存體同步服務](#deploy-the-storage-sync-service)中部署儲存體同步服務的訂用帳戶。
- **儲存體帳戶**：如果您選取 [選取儲存體帳戶]，則會顯示另一個窗格，您可在其中選取具有您想要同步之 Azure 檔案共用的儲存體帳戶。
- **Azure 檔案共用**：您要同步處理的 Azure 檔案共用名稱。

若要新增伺服器端點，請移至新建立的同步群組，然後選取 [新增伺服器端點]。

![在 [同步群組] 窗格中新增新的伺服器端點](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

在 [新增伺服器端點] 窗格中，輸入下列資訊以建立伺服器端點：

- **已註冊的伺服器**：您要建立伺服器端點的伺服器或叢集名稱。
- **路徑**：要隨著同步群組同步處理的 Windows Server 路徑。
- **雲端階層處理**：啟用或停用雲端階層處理的開關。 透過雲端階層處理，可將不常使用或存取的檔案分層處理至 Azure 檔案服務。
- **磁碟區可用空間**：在伺服器端點所在磁碟區上要保留的可用空間數量。 例如，如果具有單一伺服器端點之磁碟區上的磁碟區可用空間是設定為 50%，則大約有一半的資料量會分層處理至 Azure 檔案服務。 無論雲端階層處理是否啟用，您的 Azure 檔案共用在同步群組中一律會有完整的資料複本。

若要新增伺服器端點，請選取 [建立]。 您的檔案現在會在 Azure 檔案共用和 Windows Server 之間保持同步。 

> [!Important]  
> 您可以對同步群組中的任何雲端端點或伺服器端點進行變更，您的檔案將會與同步群組中的其他端點同步。 如果直接對雲端端點 (Azure 檔案共用) 進行變更，則必須先由 Azure 檔案同步變更偵測作業探索到該變更。 針對雲端端點的變更偵測作業，每隔 24 小時才會起始一次。 如需詳細資訊，請參閱 [Azure 檔案服務常見問題集](storage-files-faq.md#afs-change-detection)。

## <a name="onboarding-with-azure-file-sync"></a>透過 Azure 檔案同步上架
第一次於 Azure 檔案同步上架時，如果不想要停機，同時還要保留完整檔案忠實性和存取控制清單 (ACL)，其建議步驟如下所示：
 
1.  部署儲存體同步服務。
2.  建立同步群組。
3.  在具有完整資料集的伺服器上安裝 Azure 檔案同步代理程式。
4.  註冊該伺服器，並在共用上建立伺服器端點。 
5.  讓同步服務將所有資料完整上傳至 Azure 檔案共用 (雲端端點)。  
6.  完成初始上傳之後，在其餘的各個伺服器上安裝 Azure 檔案同步代理程式。
7.  在其餘的各個伺服器上建立新的檔案共用。
8.  如有需要，請使用雲端階層處理原則，在新的檔案共用上建立伺服器端點。 (此步驟需有額外的儲存體以進行初始設定。)
9.  讓 Azure 檔案同步代理程式快速還原完整命名空間，而不需實際的資料傳輸。 在完整命名空間同步之後，同步引擎將會根據伺服器端點的雲端階層處理原則，填滿本機磁碟空間。 
10. 確定同步完成，並視需要測試拓撲。 
11. 將使用者和應用程式重新導向到這個新共用。
12. 您可以選擇性地刪除伺服器上任何重複的共用。
 
如果您沒有額外儲存體可供初始上架，而想要連結至現有的共用，您可以在 Azure 檔案共用中預先植入資料。 唯有您可接受停機時間，且絕對保證在初始上架程序期間，伺服器共用上的資料不會變更，才建議使用此方法。 
 
1.  確保在上架程序期間，任何伺服器上的資料都無法變更。
2.  透過 SMB 使用任何資料傳輸工具 (例如 Robocopy、直接的 SMB 複製)，以伺服器資料預先植入 Azure 檔案共用。 因為 AzCopy 不會透過 SMB 上傳資料，所以無法用於預先植入。
3.  使用指向現有共用的所需伺服器端點，建立 Azure 檔案同步拓撲。
4.  讓同步服務在所有端點上完成調整程序。 
5.  完成調整後，您可以開啟共用進行變更。
 
請留意目前預先植入的方法有一些限制： 
- 檔案不會保持完整不失真。 例如，檔案會失去 ACL 和時間戳記。
- 在同步拓撲完全啟動並執行之前，伺服器上的資料變更會導致伺服器端點發生衝突。  
- 建立雲端端點之後，Azure 檔案同步會先執行在雲端偵測檔案的程序，再開始初始同步處理。完成此程序所需的時間取決於各種因素，例如網路速度、可用頻寬，以及檔案和資料夾的數目。 根據預覽版本中的粗略估計，偵測程序會以大約每秒 10 個檔案的速度執行。因此，即使預先植入的執行速度快，但若在雲端預先植入資料，則系統完整執行的整體時間可能會更長。


## <a name="migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync"></a>將 DFS 複寫 (DFS-R) 部署移轉至 Azure 檔案同步
若要將 DFS-R 部署移轉至 Azure 檔案同步處理：

1. 建立同步群組來代表要取代的 DFS-R 拓樸。
2. 在要移轉 DFS R 拓撲中，於有一組完整資料的伺服器上啟動。 在該伺服器上安裝 Azure 檔案同步。
3. 註冊該伺服器，並為要移轉的第一個伺服器建立伺服器端點。 請勿啟用雲端階層。
4. 將所有資料同步至 Azure 檔案共用 (雲端端點)。
5. 在其餘的各個 DFS-R 伺服器上安裝並註冊 Azure 檔案同步代理程式。
6. 停用 DFS-R。 
7. 在每一個 DFS-R 伺服器上建立伺服器端點。 請勿啟用雲端階層。
8. 確定同步完成，並視需要測試拓撲。
9. 淘汰 DFS-R。
10. 現在可在所需的任何伺服器端點上啟用雲端階層。

如需詳細資訊，請參閱[Azure 檔案同步與分散式檔案系統 (DFS) 互通](storage-sync-files-planning.md#distributed-file-system-dfs)。

## <a name="next-steps"></a>後續步驟
- [新增或移除 Azure 檔案同步伺服器端點](storage-sync-files-server-endpoint.md)
- [向 Azure 檔案同步註冊或取消註冊伺服器](storage-sync-files-server-registration.md)
