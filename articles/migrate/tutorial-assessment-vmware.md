---
title: 使用 Azure Migrate 探索及評估要移轉到 Azure 的內部部署 VMware VM | Microsoft Docs
description: 說明如何使用 Azure Migrate 服務，探索及評估要移轉到 Azure 的內部部署 VMware VM。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 01/31/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: dc2fb3faddc8af30edd66b926259c05580d9872a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/19/2019
ms.locfileid: "58104517"
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

## <a name="prerequisites"></a>必要條件

- **VMware**：您計劃要移轉的虛擬機器必須由執行 5.5、6.0、6.5 或 6.7 版的 vCenter Server 來管理。 此外，您還需要一部執行 5.5 版或更新版本的 ESXi 主機來部署收集器 VM。
- **vCenter Server 帳戶**：您需要一個唯讀帳戶來存取 vCenter Server。 Azure Migrate 會使用此帳戶來探索內部部署 VM。
- **權限**：在 vCenter Server 上，您需要權限，方可藉由匯入 .OVA 格式的檔案來建立 VM。

## <a name="create-an-account-for-vm-discovery"></a>建立用於 VM 探索的帳戶

Azure Migrate 需要存取 VMware 伺服器，才能自動探索 VM 以進行評量。 建立具有下列屬性的 VMware 帳戶。 您會在 Azure Migrate 設定過程中指定此帳戶。

- 使用者類型：至少是唯讀使用者
- 權限：資料中心物件 –> 傳播至子物件、role=Read-only
- 詳細資料：在資料中心層級指派的使用者，且能夠存取資料中心內的所有物件。
- 如果要限制存取權，請將具備 [傳播至子物件] 權限的 [沒有存取權] 角色指派給子物件 (vSphere 主機、資料存放區、VM 與網路)。


## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="create-a-project"></a>建立專案

1. 在 Azure 入口網站中，按一下 [建立資源]。
2. 搜尋 **Azure Migrate**，然後在搜尋結果中選取 [Azure Migrate] 服務。 接著，按一下 [建立]。
3. 指定專案名稱，以及專案的 Azure 訂用帳戶。
4. 建立新的資源群組。
5. 指定要建立專案的地理位置，然後按一下 [建立]。 您只能在下列地理位置建立 Azure Migrate 專案。 不過，您仍能針對任何目標 Azure 位置規劃移轉。 針對專案所指定的地理位置，僅會用於儲存從內部部署虛擬機器收集的中繼資料。

**地理位置** | **儲存位置**
--- | ---
Azure Government | 美國政府維吉尼亞州
亞洲 | 東南亞
歐洲 | 北歐或西歐
美國 | 美國東部或美國中西部

![Azure Migrate](./media/tutorial-assessment-vmware/project-1.png)


## <a name="download-the-collector-appliance"></a>下載收集器設備

Azure Migrate 會建立稱為「收集器設備」的內部部署 VM。 此 VM 會探索內部部署 VMware VM，並將其相關中繼資料傳送至 Azure Migrate 服務。 若要設定收集器設備，您可以下載 .OVA 檔案，並將它匯入內部部署 vCenter Server，以建立 VM。

1. 在 Azure Migrate 專案中，按一下 [開始使用] > [探索及評定] > [探索機器]。
2. 在 [探索機器] 中，按一下 [下載] 以下載設備。

    Azure Migrate 設備會 vCenter Server 與通訊，並持續剖析內部部署環境，以收集每部 VM 的即時使用量資料。 它會收集每個計量 (CPU 使用量、記憶體使用量等) 的尖峰計數器。 此模型並不需仰賴 vCenter Server 的統計資料設定來收集效能資料。 您可以隨時從設備停止持續性的剖析。

    > [!NOTE]
    > 一次性探索設備現在已被取代，因為這個方法依賴 vCenter Server 的統計資料設定來取得效能資料點可用性，而且收集到的平均效能計數器會導致 VM 大小不足，而無法遷移至 Azure。

    **快速評估：** 透過持續探索設備，探索完成後 (視 VM 數目而定，需要幾個小時)，您可以立即建立評量。 由於效能資料收集會在您開始探索時開始進行，如果您要尋求快速評估，則應將評量中的調整大小準則選取為「內部部署」。 對於以效能為基礎的評量，建議等待至少一天後開始探索，以取得可靠的大小建議。

    設備只會持續收集效能資料，不會偵測內部部署環境中的任何組態變更 (也就是，新增、刪除 VM 或新增磁碟等)。 如果內部部署環境中有組態變更，您可以執行下列動作，以在入口網站中反映變更：

    - 新增項目 (VM、磁碟、核心等)：若要在 Azure 入口網站中反映這些變更，您可以從設備停止探索，然後重新啟動。 這可確保所做的變更會在 Azure Migrate 專案中更新。

    - 刪除 VM：基於設備的設計方式，刪除 VM 並不會有所反映，即使您停止探索後再重新啟動也一樣。 這是因為後續探索中的資料會附加至較舊的探索，而且不會受到覆寫。 在此情況下，您可以藉由從群組中移除 VM 並重新計算評定，以直接忽略入口網站中的 VM。


3. 在 [複製專案認證] 中，複製專案識別碼和金鑰。 在設定收集器時，您會需要這些資料。

    ![下載 .ova 檔案](./media/tutorial-assessment-vmware/download-ova.png)

### <a name="verify-the-collector-appliance"></a>確認收集器設備

請先確認 .OVA 檔案是安全的，再進行部署。

1. 在存放下載檔案的目標電腦上，開啟系統管理員命令視窗。
2. 執行下列命令以產生 OVA 的雜湊：
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 使用方式範例：```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. 產生的雜湊應符合這些設定。

#### <a name="continuous-discovery"></a>連續探索

  OVA 1.0.10.11 版

  **演算法** | **雜湊值**
    --- | ---
    MD5 | 5f6b199d8272428ccfa23543b0b5f600
    SHA1 | daa530de6e8674a66a728885a7feb3b0a2e8ccb0
    SHA256 | 85da50a21a7a6ca684418a87ccc1dd4f8aab30152c438a17b216ec401ebb3a21

  OVA 1.0.10.9 版

  **演算法** | **雜湊值**
  --- | ---
  MD5 | 169f6449cc1955f1514059a4c30d138b
  SHA1 | f8d0a1d40c46bbbf78cd0caa594d979f1b587c8f
  SHA256 | d68fe7d94be3127eb35dd80fc5ebc60434c8571dcd0e114b87587f24d6b4ee4d

  OVA 1.0.10.4 版

  **演算法** | **雜湊值**
  --- | ---
  MD5 | 2ca5b1b93ee0675ca794dd3fd216e13d
  SHA1 | 8c46a52b18d36e91daeae62f412f5cb2a8198ee5
  SHA256 | 3b3dec0f995b3dd3c6ba218d436be003a687710abab9fcd17d4bdc90a11276be


#### <a name="one-time-discovery-deprecated-now"></a>單次探索 (現在已被取代)

此模型目前已被取代，將會提供現有設備的支援。

  OVA 1.0.9.15 版

  **演算法** | **雜湊值**
  --- | ---
  MD5 | e9ef16b0c837638c506b5fc0ef75ebfa
  SHA1 | 37b4b1e92b3c6ac2782ff5258450df6686c89864
  SHA256 | 8a86fc17f69b69968eb20a5c4c288c194cdcffb4ee6568d85ae5ba96835559ba

  OVA 1.0.9.14 版

  **演算法** | **雜湊值**
  --- | ---
  MD5 | 6d8446c0eeba3de3ecc9bc3713f9c8bd
  SHA1 | e9f5bdfdd1a746c11910ed917511b5d91b9f939f
  SHA256 | 7f7636d0959379502dfbda19b8e3f47f3a4744ee9453fc9ce548e6682a66f13c

  OVA 1.0.9.12 版

  **演算法** | **雜湊值**
  --- | ---
  MD5 | d0363e5d1b377a8eb08843cf034ac28a
  SHA1 | df4a0ada64bfa59c37acf521d15dcabe7f3f716b
  SHA256 | f677b6c255e3d4d529315a31b5947edfe46f45e4eb4dbc8019d68d1d1b337c2e

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
4. 按一下收集器 UI 頂端列中的 [檢查更新]，並確認收集器正在最新版本上執行。 如果沒有，您可以選擇從連結下載最新的升級套件並更新收集器。
5. 在 Azure Migrate 收集器中，開啟 [設定必要條件]。
   - 選取您打算作為遷移目的地的 Azure 雲端 (Azure Global 或 Azure Government)。
   - 接受授權條款，並閱讀第三方資訊。
   - 收集器會確認 VM 是否能夠存取網際網路。
   - 如果 VM 能夠透過 Proxy 存取網際網路，請按一下 [Proxy 設定]，然後指定 Proxy 位址和接聽連接埠。 如果 Proxy 需要驗證，請指定認證。 [深入了解](https://docs.microsoft.com/azure/migrate/concepts-collector#collector-prerequisites)網際網路連線需求和收集器存取的 [URL 清單](https://docs.microsoft.com/azure/migrate/concepts-collector)。

     > [!NOTE]
     > 必須在表單 <http://ProxyIPAddress> 或 <http://ProxyFQDN> 中輸入 Proxy 位址。 僅支援 HTTP Proxy。 如果您有攔截 Proxy，且您未匯入 Proxy 憑證，則初始的網際網路連線可能會失敗；請[深入了解](https://docs.microsoft.com/azure/migrate/concepts-collector)如何藉由將 Proxy 憑證匯入作為收集器 VM 上的信任憑證，以解決此問題。

   - 收集器會檢查收集器服務是否正在執行。 根據預設，收集器 VM 上會安裝此服務。
   - 下載並安裝 VMware PowerCLI。

6. 在 [指定 vCenter Server 詳細資料] 中，執行下列動作：
    - 指定 vCenter Server 的名稱 (FQDN) 或 IP 位址。
    - 在 [使用者名稱] 和 [密碼] 中，指定收集器要用來探索 vCenter Server 上之 VM 的唯讀帳戶認證。
    - 在 [收集範圍] 中，選取 VM 探索的範圍。 收集器只能探索指定範圍內的 VM。 範圍可以設定為特定資料夾、資料中心或叢集。 它不應包含超過 1500 個 VM。 [深入了解](how-to-scale-assessment.md)如何探索較大的環境。

7. 在 [指定移轉專案] 中，指定您從入口網站所複製的 Azure Migrate 專案識別碼和金鑰。 如果您之前未複製，請從收集器 VM 開啟 Azure 入口網站。 在專案的 [概觀] 頁面中，按一下 [探索機器]，然後複製值。  
8. 在 [檢視檢視收集進度] 中，監視探索狀態。 [深入了解](https://docs.microsoft.com/azure/migrate/concepts-collector) Azure Migrate 收集器會收集哪些資料。

> [!NOTE]
> 收集器只支援以「英文 (美國)」作為作業系統語言和收集器介面語言。
> 如果您對所要評估的機器變更設定，觸發程序將在您執行評估之前再次探索。 請在收集器中使用 [重新開始收集] 選項來執行此動作。 收集完成後，請在入口網站中選取 [重新計算] 選項進行評估，以取得更新的評估結果。


### <a name="verify-vms-in-the-portal"></a>在入口網站中確認 VM

收集器設備將會持續對內部部署環境進行剖析，並會持續以一小時為間隔傳送效能資料。 在開始探索的一小時後，您便可以在入口網站中檢視電腦。

1. 在移轉專案中，按一下 [管理] > [機器]。
2. 確認您想要探索的 VM 出現在入口網站內。


## <a name="create-and-view-an-assessment"></a>建立和檢視評估

在入口網站中探索到 VM 之後，您可以將它們分組並建立評量。 在入口網站中探索到 VM 後，您就可以立即建立內部評量。 建議您至少先等候一天的時間，再建立以效能為基礎的評量，以取得可靠的大小建議。

1. 在專案的 [概觀] 頁面中，按一下 [+建立評估]。
2. 按一下 [檢視全部] 來檢閱評估屬性。
3. 建立群組，並指定群組名稱。
4. 選取您想要新增至群組的機器。
5. 按一下 [建立評估] 以建立群組和評估。
6. 建立評估之後，在 [概觀] > [儀表板] 中檢視該評估。
7. 按一下 [匯出評估]，將其下載為 Excel 檔案。

> [!NOTE]
> 強烈建議您在開始探索後，至少先等候一天的時間再建立評量。 如果您想要以最新的效能資料更新現有的評量，則可以對評量使用 [重新計算] 命令來更新它。

### <a name="assessment-details"></a>評量詳細資料

評量包含內部部署虛擬機器是否與 Azure 相容、在 Azure 中執行虛擬機器的正確虛擬機器大小，以及預估的 Azure 每月成本之相關資訊。

![評估報告](./media/tutorial-assessment-vmware/assessment-report.png)

#### <a name="azure-readiness"></a>Azure 移轉整備程度

評量中的 Azure 移轉整備程度檢視會顯示每部虛擬機器的整備狀態。 根據虛擬機器的屬性，每部虛擬機器可以標示為：
- 可供 Azure 使用
- 可有條件地供 Azure 使用
- 未備妥供 Azure 使用
- 移轉整備程度未知

對於已準備好的 VM，Azure Migrate 會建議這些 VM 在 Azure 中該有的大小。 由 Azure Migrate 進行的大小建議取決於評量屬性中指定的調整大小準則。 如果調整大小準則是以效能為基礎來調整，則會藉由考量 VM (CPU 和記憶體) 和磁碟 (IOPS 和輸送量) 的效能歷程記錄來建議大小。 如果調整大小準則是「作為內部部署」，Azure Migrate 不會考慮 VM 和磁碟的效能資料。 藉由查看 VM 內部部署的大小來建議 Azure 中的 VM 大小，而磁碟大小調整則是以評估屬性中指定的儲存體類型 (預設值為進階磁碟) 為基礎。 [深入了解](concepts-assessment-calculation.md)有關 Azure Migrate 調整大小的方式。

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

Azure Migrate 中的每個效能型評量會與信賴評等相關聯，信賴評等的範圍從 1 顆星到 5 顆星 (1 顆星最低，5 顆星最高)。 根據計算評量所需的資料點可用性，每個評量都會指派信賴評等。 評量的信賴評等可協助您評估 Azure Migrate 提供的大小建議之可靠性。 信賴評等不適用於「現狀」的內部部署評估。

對於以效能為基礎的大小調整，Azure Migrate 需要 VM 的 CPU、記憶體使用量資料。 此外，對於連結至 VM 的每個磁碟，它都需要磁碟 IOPS 和輸送量資料。 同樣地，對於連接至虛擬機器的每個網路介面卡，Azure Migrate 需要輸入/輸出網路以進行以效能為基礎的大小調整。 如果上述的任何使用量數字在 vCenter Server 中無法取得，則 Azure Migrate 所完成的大小建議可能不可靠。 根據可用資料點的百分比提供評量的信賴評等如下：

   **資料點的可用性** | **信賴評等**
   --- | ---
   0%-20% | 1 顆星
   21%-40% | 2 顆星
   41%-60% | 3 顆星
   61%-80% | 4 顆星
   81%-100% | 5 顆星

由於下列原因，評量可能沒有所有可用資料點：

- 您未針對正在建立評量的持續時間剖析環境。 例如，如果您要建立的評量將效能持續時間設定為 1 天，您需要至少等待一天後再開始探索，才能收集到所有資料點。

- 少數虛擬機器在評量計算期間關閉。 如果有任何 VM 在某段期間內電源關閉，則無法收集這段時間的效能資料。

- 少數虛擬機器在評量計算期間才建立。 例如，如果您要建立過去一個月的效能記錄評量，但是少數虛擬機器在一週前才建立在環境中。 在這種情況下，新虛擬機器的效能記錄不會在整段期間中出現。

> [!NOTE]
> 如果任何評量的信賴評等低於 5 顆星，請至少等待一天，讓設備剖析環境，然後「重新計算」評量。 如果無法完成上述動作，則以效能為基礎的大小調整可能不可靠，且建議您變更評量屬性以切換至*內部部署大小調整*。

## <a name="next-steps"></a>後續步驟

- [深入了解](how-to-modify-assessment.md)如何根據您的需求自訂評估。
- 了解如何使用[機器相依性對應](how-to-create-group-machine-dependencies.md)來建立可信度高的評定群組
- [深入了解](concepts-assessment-calculation.md)評定的計算方式。
- [了解](how-to-scale-assessment.md)如何探索及評定大型 VMware 環境。
- [深入了解](resources-faq.md) Azure Migrate 常見問題集
