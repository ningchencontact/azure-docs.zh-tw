---
title: "使用 Azure Migrate 探索及評估要移轉到 Azure 的內部部署 VMware VM | Microsoft Docs"
description: "說明如何使用 Azure Migrate 服務，探索及評估要移轉到 Azure 的內部部署 VMware VM。"
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 02/27/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 3c8d345d8846994ac1e286d977b62d9ae2b7d660
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2018
---
# <a name="discover-and-assess-on-premises-vmware-vms-for-migration-to-azure"></a>探索及評估要移轉到 Azure 的內部部署 VMware VM

[Azure Migrate](migrate-overview.md) 服務會評估要移轉至 Azure 的內部部署工作負載。

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 建立 Azure Migrate 用來探索內部部署 VM 的帳戶
> * 建立 Azure Migrate 專案。
> * 設定內部部署收集器虛擬機器 (VM)，以探索要評估的內部部署 VMware VM。
> * 將 VM 分組並建立評估。


如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/pricing/free-trial/) 。


## <a name="prerequisites"></a>先決條件

- **VMWare**：您計劃移轉的虛擬機器必須透過執行版本 5.5、6.0 或 6.5 的 vCenter Server 來管理。 此外，您還需要一部執行版本 5.0 或更新版本的 ESXi 主機來部署收集器 VM。 
 
> [!NOTE]
> Hyper-V 支援已在我們的藍圖中，將儘速啟用。 

- **vCenter Server 帳戶**：您需要一個唯讀帳戶來存取 vCenter Server。 Azure Migrate 會使用此帳戶來探索內部部署 VM。
- **權限**：在 vCenter Server 上，您需要權限，方可藉由匯入 .OVA 格式的檔案來建立 VM。 
- **統計資料設定**：vCenter Server 的統計資料設定應該先設為層級 3，再開始部署。 如果低於層級 3，還是能進行評估，但不會收集儲存體和網路的效能資料。 在此情況下，將根據 CPU 和記憶體的效能資料以及磁碟和網路介面卡的組態資料來提出大小建議。 

## <a name="create-an-account-for-vm-discovery"></a>建立用於 VM 探索的帳戶

Azure Migrate 需要存取 VMware 伺服器，才能自動探索 VM 以進行評量。 建立具有下列屬性的 VMware 帳戶。 您會在 Azure Migrate 設定過程中指定此帳戶。

- 使用者類型：至少是唯讀使用者
- 權限：資料中心物件 –> 傳播至子物件、角色=唯讀
- 詳細資訊：在資料中心層級指派的使用者，且能夠存取資料中心內的所有物件。
- 如果要限制存取權，請將具備 [傳播至子物件] 權限的 [沒有存取權] 角色指派給子物件 (vSphere 主機、資料存放區、VM 及網路)。

## <a name="log-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="create-a-project"></a>建立專案

1. 在 Azure 入口網站中，按一下 [建立資源]。
2. 搜尋 **Azure Migrate**，然後在搜尋結果中選取 [Azure Migrate] 服務。 接著，按一下 [建立]。
3. 指定專案名稱，以及專案的 Azure 訂用帳戶。
4. 建立新的資源群組。
5. 指定要建立專案的所在位置，然後按一下 [建立]。 您只能在「美國中西部」或「美國東部」區域建立 Azure Migrate 專案。 不過，您仍能針對任何目標 Azure 位置規劃移轉。 為專案指定的位置只用於儲存從內部部署虛擬機器收集的中繼資料。 

    ![Azure Migrate](./media/tutorial-assessment-vmware/project-1.png)
    


## <a name="download-the-collector-appliance"></a>下載收集器設備

Azure Migrate 會建立稱為「收集器設備」的內部部署 VM。 此 VM 會探索內部部署 VMware VM，並將其相關中繼資料傳送至 Azure Migrate 服務。 若要設定收集器設備，您可以下載 .OVA 檔案，並將它匯入內部部署 vCenter Server，以建立 VM。

1. 在 Azure Migrate 專案中，按一下 [使用者入門] > [探索及評估] > [探索機器]。
2. 在 [探索機器] 中，按一下 [下載] 以下載 .OVA 檔案。
3. 在 [複製專案認證] 中，複製專案識別碼和金鑰。 在設定收集器時，您會需要這些資料。

    ![下載 .ova 檔案](./media/tutorial-assessment-vmware/download-ova.png)

### <a name="verify-the-collector-appliance"></a>確認收集器設備

請先確認 .OVA 檔案是安全的，再進行部署。

1. 在存放下載檔案的目標電腦上，開啟系統管理員命令視窗。
2. 執行下列命令以產生 OVA 的雜湊：
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 使用方式範例：```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. 產生的雜湊應符合這些設定。
    
    若為 OVA 1.0.9.2 版

    **演算法** | **雜湊值**
    --- | ---
    MD5 | 7326020e3b83f225b794920b7cb421fc
    SHA1 | a2d8d496fdca4bd36bfa11ddf460602fa90e30be
    SHA256 | f3d9809dd977c689dda1e482324ecd3da0a6a9a74116c1b22710acc19bea7bb2  
    
    若為 OVA 1.0.8.59 版

    **演算法** | **雜湊值**
    --- | ---
    MD5 | 71139e24a532ca67669260b3062c3dad
    SHA1 | 1bdf0666b3c9c9a97a07255743d7c4a2f06d665e
    SHA256 | 6b886d23b24c543f8fc92ff8426cd782a77efb37750afac397591bda1eab8656  

    若為 OVA 1.0.8.49 版
    **演算法** | **雜湊值**
    --- | ---
    MD5 | cefd96394198b92870d650c975dbf3b8 
    SHA1 | 4367a1801cf79104b8cd801e4d17b70596481d6f
    SHA256 | fda59f076f1d7bd3ebf53c53d1691cc140c7ed54261d0dc4ed0b14d7efef0ed9

    若為 OVA 1.0.8.40 版：

    **演算法** | **雜湊值**
    --- | ---
    MD5 | afbae5a2e7142829659c21fd8a9def3f
    SHA1 | 1751849c1d709cdaef0b02a7350834a754b0e71d
    SHA256 | d093a940aebf6afdc6f616626049e97b1f9f70742a094511277c5f59eacc41ad

## <a name="create-the-collector-vm"></a>建立收集器 VM

將下載的檔案匯入 vCenter Server。

1. 在 vSphere 用戶端主控台中，按一下 [檔案] > [部署 OVF 範本]。

    ![部署 OVF](./media/tutorial-assessment-vmware/vcenter-wizard.png)

2. 在 [部署 OVF 範本精靈] > [來源] 中，指定 .ova 檔案的位置。
3. 在 [名稱] 和 [位置] 中，指定收集器 VM 的易記名稱，以及要裝載 VM 的所在清查物件。
5. 在 [主機/叢集] 中，指定收集器 VM 的執行所在主機或叢集。
7. 在儲存體中，指定收集器 VM 的儲存目的地。
8. 在 [磁碟格式] 中，指定磁碟類型和大小。
9. 在 [網路對應] 中，指定收集器 VM 所要連線的網路。 此網路必須能夠連線到網際網路，以將中繼資料傳送至 Azure。 
10. 檢閱並確認設定，然後按一下 [完成]。

## <a name="run-the-collector-to-discover-vms"></a>執行收集器來探索 VM

1. 在 vSphere 用戶端主控台中，以滑鼠右鍵按一下 [VM] > [開啟主控台]。
2. 提供設備的語言、時區和密碼喜好設定。
3. 在桌面上，按一下 [執行收集器] 捷徑。
4. 在 Azure Migrate 收集器中，開啟 [設定必要條件]。
    - 接受授權條款，並閱讀第三方資訊。
    - 收集器會確認 VM 是否能夠存取網際網路。
    - 如果 VM 能夠透過 Proxy 存取網際網路，請按一下 [Proxy 設定]，然後指定 Proxy 位址和接聽連接埠。 如果 Proxy 需要驗證，請指定認證。

    > [!NOTE]
    > 必須在表單 http://ProxyIPAddress 或 http://ProxyFQDN 中輸入 Proxy 位址。 僅支援 HTTP Proxy。

    - 收集器會檢查收集器服務是否正在執行。 根據預設，收集器 VM 上會安裝此服務。
    - 下載並安裝 VMware PowerCLI。

5. 在 [指定 vCenter Server 詳細資料] 中，執行下列動作：
    - 指定 vCenter Server 的名稱 (FQDN) 或 IP 位址。
    - 在 [使用者名稱] 和 [密碼] 中，指定收集器要用來探索 vCenter Server 上之 VM 的唯讀帳戶認證。
    - 在 [收集範圍] 中，選取 VM 探索的範圍。 收集器只能探索指定範圍內的 VM。 範圍可以設定為特定資料夾、資料中心或叢集。 其不應包含超過 1000 個 VM。 

6. 在 [指定移轉專案] 中，指定您從入口網站所複製的 Azure Migrate 專案識別碼和金鑰。 如果您之前未複製，請從收集器 VM 開啟 Azure 入口網站。 在專案的 [概觀] 頁面中，按一下 [探索機器]，然後複製值。  
7. 在 [檢視收集進度] 中監視探索，然後確認從虛擬機器收集到的中繼資料位於範圍內。 收集器會提供概略的探索時間。

> [!NOTE]
> 收集器只支援以「英文 (美國)」作為作業系統語言和收集器介面語言。 其他語言的支援很快就會推出。


### <a name="verify-vms-in-the-portal"></a>在入口網站中確認 VM

探索時間取決於您要探索多少 VM。 一般來說，若是 100 個 VM，當收集器完成執行後，大約會使用一小時來完成探索。 

1. 在 Migration Planner 專案中，按一下 [管理] > [機器]。
2. 確認您想要探索的 VM 有出現在入口網站內。


## <a name="create-and-view-an-assessment"></a>建立和檢視評估

探索到 VM 之後，您可以將它們分組，並建立評估。 

1. 在專案的 [概觀] 頁面中，按一下 [+建立評估]。
2. 按一下 [檢視全部] 來檢閱評估屬性。
3. 建立群組，並指定群組名稱。
4. 選取您想要新增至群組的機器。
5. 按一下 [建立評估] 以建立群組和評估。
6. 建立評估之後，在 [概觀] > [儀表板] 中檢視該評估。
7. 按一下 [匯出評估]，將其下載為 Excel 檔案。

### <a name="assessment-details"></a>評量詳細資料

評量包含內部部署虛擬機器是否與 Azure 相容、在 Azure 中執行虛擬機器的正確虛擬機器大小，以及預估的 Azure 每月成本之相關資訊。 

![評估報告](./media/tutorial-assessment-vmware/assessment-report.png)

#### <a name="azure-readiness"></a>Azure 移轉整備程度

評量中的 Azure 移轉整備程度檢視會顯示每部虛擬機器的整備狀態。 根據虛擬機器的屬性，每部虛擬機器可以標示為：
- 可供 Azure 使用
- 可有條件地供 Azure 使用
- 未備妥供 Azure 使用
- 移轉整備程度未知 

對於已準備好的 VM，Azure Migrate 會建議這些 VM 在 Azure 中該有的大小。 由 Azure Migrate 進行的大小建議取決於評量屬性中指定的調整大小準則。 如果調整大小準則是以效能為基礎來調整，則大小建議的方式是考量虛擬機器的效能記錄。 如果調整大小準則是「如內部部署」，則建議方式是藉由查看虛擬機器內部部署的大小 (以現狀調整大小)。 在此情況不考慮使用量資料。 [深入了解](concepts-assessment-calculation.md)有關 Azure Migrate 調整大小的方式。 

對於尚未就緒或者可有條件地供 Azure 使用的虛擬機器，Azure Migrate 會說明整備問題，並提供補救步驟。 

對於 Azure Migrate 無法識別 Azure 移轉整備程度 (因為資料無法使用) 的虛擬機器會標示為移轉整備程度未知。

除了 Azure 移轉整備程度和大小調整，Azure Migrate 也會建議可供移轉虛擬機器使用的工具。 這需要在內部部署環境進行更深入的探索。 [深入探索](how-to-get-migration-tool.md)如何藉由在內部部署電腦上安裝代理程式，以進行更深入的探索。 如果內部部署電腦上未安裝代理程式，則建議使用 [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) 進行原形移轉。 如果在內部部署電腦上安裝代理程式，則 Azure Migrate 會查看在電腦內部執行的程序，並識別電腦是否為資料庫電腦。 如果電腦是資料庫電腦，則建議使用 [Azure 資料庫移轉服務](https://docs.microsoft.com/azure/dms/dms-overview)，否則建議使用 Azure Site Recovery 作為移轉工具。

  ![評估整備](./media/tutorial-assessment-vmware/assessment-suitability.png)  

#### <a name="monthly-cost-estimate"></a>每月成本預估值

此檢視會顯示在 Azure 中執行 VM 的計算和儲存總成本，以及每部電腦的詳細資訊。 在計算成本估計值時，會考慮 Azure Migrate 針對一部電腦、其磁碟及評量屬性所做的大小建議。 

> [!NOTE]
> Azure Migrate 所提供的成本估計適用於執行內部部署 VM 以作為 Azure 基礎結構即服務 (IaaS) VM。 Azure Migrate 不會考慮任何平台即服務 (PaaS) 或軟體即服務 (SaaS) 成本。 

系統會彙總群組內所有 VM 之計算和儲存的每月預估成本。 

![評估 VM 成本](./media/tutorial-assessment-vmware/assessment-vm-cost.png) 

#### <a name="confidence-rating"></a>信賴評等

Azure Migrate 中的每個評量會與信賴評等連結，信賴評等的範圍從 1 顆星到 5 顆星 (1 顆星最低，5 顆星最高)。 根據計算評量所需的資料點可用性，每個評量都會指派信賴評等。 評量的信賴評等可協助您評估 Azure Migrate 提供的大小建議之可靠性。 

信賴評等在您進行「以效能為基礎的大小調整」時非常實用，因為 Azure Migrate 可能沒有足夠的資料點都可進行以使用率為基礎的大小調整。 對於*作為內部部署調整大小*，信賴評等一律是 5 顆星，因為 Azure Migrate 擁有調整 VM 大小所需的所有資料點。 

對於以效能為基礎的 VM 大小調整，Azure Migrate 需要 CPU 和記憶體的使用量資料。 此外，對於連結至虛擬機器的每個磁碟，它都需要讀取/寫入 IOPS 及輸送量。 同樣地，對於連接至虛擬機器的每個網路介面卡，Azure Migrate 需要輸入/輸出網路以進行以效能為基礎的大小調整。 如果上述的任何使用量數字在 vCenter Server 中無法取得，則 Azure Migrate 所完成的大小建議可能不可靠。 根據可用資料點的百分比提供評量的信賴評等：

   **資料點的可用性** | **信賴評等**
   --- | ---
   0%-20% | 1 顆星
   21%-40% | 2 顆星
   41%-60% | 3 顆星
   61%-80% | 4 顆星
   81%-100% | 5 顆星

由於下列原因，評量可能沒有所有可用資料點：
- vCenter Server 中的統計資料設定未設定為等級 3，且評量的調整大小準則是以效能為基礎的大小調整。 如果 vCenter Server 中的統計資料設定低於等級 3，磁碟和網路的效能資料並未從 vCenter Server 收集。 在此情況下，Azure Migrate 針對磁碟和網路所提供的建議是以使用率為基礎。 針對儲存體，Azure Migrate 會建議標準磁碟，因為若不考量磁碟的 IOPS/輸送量，Azure Migrate 便無法識別磁碟在 Azure 中是否需要進階磁碟。
- vCenter Server 中的統計資料設定會在開始探索前短期設定為等級 3。 例如，讓我們考慮以下案例：您今天將統計資料設定變更為等級 3，並在明天 (24 小時後) 使用收集器設備開始探索。 如果您要建立一天的評量，您可擁有所有資料點，且評量的信賴評等會是 5 顆星。 但如果您將評量屬性中的效能持續時間變更為一個月，信賴評等會關閉，因無法使用過去一個月的磁碟和網路效能資料。 如果您想考量過去一個月的效能資料，建議您將 vCenter Server 統計資料設定保留在等級 3 一個月，再開始進行探索。 
- 少數虛擬機器在評量計算期間關閉。 如果任何虛擬機器在某段期間內電源關閉，則 vCenter Server 不會有這段時間的效能資料。 
- 少數虛擬機器在評量計算期間才建立。 例如，如果您要建立過去一個月的效能記錄評量，但是少數虛擬機器在一週前才建立在環境中。 在這種情況下，新虛擬機器的效能記錄不會在整段期間中出現。

> [!NOTE]
> 如果任何評量的信賴評等低於 4 顆星，建議您將 vCenter Server 的統計資料設定等級變更為 3，等候您要評量的持續時間 (1 天/1 週/1 個月)，然後再執行探索及評量。 如果無法完成上述內容，則以效能為基礎的大小調整可能不可靠，建議您變更評量屬性以切換至「如內部部署大小調整」。
 
## <a name="next-steps"></a>後續步驟

- [了解](how-to-scale-assessment.md)如何探索及評定大型 VMware 環境。
- 了解如何使用[機器相依性對應](how-to-create-group-machine-dependencies.md)來建立可信度高的評定群組
- [深入了解](concepts-assessment-calculation.md)評定的計算方式。
