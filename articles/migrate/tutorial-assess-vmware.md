---
title: 使用 Azure Migrate評估 VMware VM 以移轉至 Azure
description: 說明如何使用 Azure Migrate 來評估內部部署 VMware VM 是否可移轉至 Azure。
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/12/2019
ms.author: hamusa
ms.openlocfilehash: 5f70037b1e6ce284b55ff5ff0ae38eb50c320122
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/13/2019
ms.locfileid: "67868666"
---
# <a name="assess-vmware-vms-with-azure-migrate-server-assessment"></a>使用 Azure Migrate 評估 VMware VM：伺服器評量

本文說明如何使用 Azure Migrate 來評估內部部署 VMware VM：伺服器評量工具。

[Azure Migrate](migrate-services-overview.md) 會提供工具中樞，協助您探索和評估應用程式、基礎結構和工作負載，並且將這些項目遷移至 Microsoft Azure。 此中樞包含 Azure Migrate 工具和第三方獨立軟體廠商 (ISV) 供應項目。



本教學課程是一個系列中的第二篇，會示範如何評估 VMware VM 並將其遷移至 Azure。 在本教學課程中，您了解如何：
> [!div class="checklist"]
> * 設定 Azure Migrate 專案。
> * 設定內部部署環境中執行的 Azure Migrate 設備來評估 VM。
> * 開始連續探索內部部署 VM。 設備會將探索到的 VM 組態和效能資料傳送至 Azure。
> * 將探索到的 VM 群組在一起，然後評估該 VM 群組。
> * 檢閱評估結果。



> [!NOTE]
> 教學課程將會針對案例示範最簡單的部署路徑，讓您可以快速設定概念證明。 教學課程會在情況允許時都使用預設選項，且不會顯示所有可能的設定與路徑。 如需詳細指示，請檢閱操作說明文章。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/pricing/free-trial/) 。


## <a name="prerequisites"></a>必要條件

- [完成](tutorial-prepare-vmware.md)本系列的第一個教學課程。 如果未完成，本教學課程中的指示便沒有作用。
- 您在第一個教學課程中應該已完成下列作業：
    - 針對 Azure Migrate [設定 Azure 權限](tutorial-prepare-vmware.md#prepare-azure)。
    - [準備 VMware](tutorial-prepare-vmware.md#prepare-for-vmware-vm-assessment) 以進行評估。 VMware 設定應經過驗證，而且您應該具有使用 OVA 範本建立 VMware VM 的權限。 您也應該設定用於 VM 探索的帳戶。 必要的連接埠應可供使用，而且您應該知道存取 Azure 所需的 URL。


## <a name="set-up-an-azure-migrate-project"></a>設定 Azure Migrate 專案

設定新的 Azure Migrate 專案，如下所示。

1. 在 Azure 入口網站 > [所有服務]  中搜尋 **Azure Migrate**。
2. 在 [服務]  下，選取 [Azure Migrate]  。
3. 在 [概觀]  的 [探索、評估和遷移伺服器]  底下，按一下 [評估和遷移伺服器]  。

    ![探索和評估伺服器](./media/tutorial-assess-vmware/assess-migrate.png)

4. 在 [開始使用]  中，按一下 [新增工具]  。
5. 在 [遷移專案]  中，選取您的 Azure 訂用帳戶，並建立資源群組 (如果您還沒有資源群組的話)。     
6. 在 [專案詳細資料]  中，指定專案名稱以及要在其中建立專案的地理位置。 支援亞洲、歐洲、英國和美國。

    - 專案地理區域只會用來儲存從內部部署 VM 收集到的中繼資料。
    - 當您執行移轉時，可以選取任何目的地區域。

    ![建立 Azure Migrate 專案](./media/tutorial-assess-vmware/migrate-project.png)


7. 按 [下一步]  。
8. 在 [選取評量工具]  中，選取 **[Azure Migrate：伺服器評量]**  > [下一步]  。

    ![建立 Azure Migrate 專案](./media/tutorial-assess-vmware/assessment-tool.png)

9. 在 [選取移轉工具]  中，選取 [暫時跳過新增移轉工具]   > [下一步]  。
10. 在 [檢閱 + 新增工具]  中檢閱設定，然後按一下 [新增工具]  。
11. 等候幾分鐘讓 Azure Migrate 專案完成部署。 您會進入專案頁面。 如果您沒有看到專案，則可以從 Azure Migrate 儀表板中的 [伺服器]  來存取。


## <a name="set-up-the-appliance-vm"></a>設定設備 VM

Azure Migrate：伺服器評量會執行輕量型 VMware VM 設備。

- 此設備會執行 VM 探索，並將 VM 的中繼資料和效能資料傳送至「Azure Migrate 伺服器評量」。
- 若要設定設備，請：
    - 下載 OVA 範本檔案，並將其匯入 vCenter Server。
    - 建立設備，並確認其可以連線至 Azure Migrate 伺服器評估。
    - 第一次設定設備，並向 Azure Migrate 專案進行註冊。
- 您可以為單一 Azure Migrate 專案設定多個設備。 在所有設備中，最多可支援對 35,000 個 VM 進行探索。 每個設備最多可探索 10,000 部伺服器。

### <a name="download-the-ova-template"></a>下載 OVA 範本

1. 在 [移轉目標]   > [伺服器]   >  **[Azure Migrate：伺服器評量]** 中，按一下 [探索]  。
2. 在 [探索機器]   > [機器是否已虛擬化?]  中，按一下 [是，使用 VMWare vSphere Hypervisor]  。
3. 按一下 [下載]  以下載 .OVA 範本檔案。

    ![下載 .ova 檔案](./media/tutorial-assess-vmware/download-ova.png)


### <a name="verify-security"></a>確認安全性

請先確認 OVA 檔案是安全的，再進行部署。

1. 在存放下載檔案的目標電腦上，開啟系統管理員命令視窗。
2. 執行下列命令以產生 OVA 的雜湊：
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 使用方式範例：```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. 針對版本 1.19.06.27，產生的雜湊應符合這些值。 

  **演算法** | **雜湊值**
  --- | ---
  MD5 | 605d208ac5f4173383f616913441144e
  SHA256 | 447d16bd55f20f945164a1189381ef6e98475b573d6d1c694f3e5c172cfc30d4


### <a name="create-the-appliance-vm"></a>建立設備 VM

匯入所下載的檔案，並建立 VM。

1. 在 vSphere 用戶端主控台中，按一下 [檔案]   > [部署 OVF 範本]  。

    ![部署 OVF](./media/tutorial-assess-vmware/deploy-ovf.png)

2. 在 [部署 OVF 範本精靈] > [來源]  中，指定 OVA 檔案的位置。
3. 在 [名稱]   和 [位置] 中，指定 VM 的易記名稱。 選取將裝載 VM 的庫存物件。
5. 在 [主機/叢集]  中，指定 VM 的執行所在主機或叢集。
6. 在**儲存體**中，指定 VM 的儲存目的地。
7. 在 [磁碟格式]  中，指定磁碟類型和大小。
8. 在 [網路對應]  中，指定 VM 所要連線的網路。 此網路必須能夠連線到網際網路，以將中繼資料傳送至 Azure Migrate 伺服器評量。
9. 檢閱並確認設定，然後按一下 [完成]  。


### <a name="verify-appliance-access-to-azure"></a>確認設備是否能存取 Azure

請確定設備 VM 可以連線至 [Azure URL](migrate-support-matrix-vmware.md#assessment-url-access-requirements)。


### <a name="configure-the-appliance"></a>設定設備

請使用下列步驟來設定設備。

1. 在 vSphere 用戶端主控台中，以滑鼠右鍵按一下 [VM] > [開啟主控台]  。
2. 提供設備的語言、時區和密碼。
3. 在任何可連線至 VM 的機器上開啟瀏覽器，並開啟設備 Web 應用程式的 URL：**https://設備名稱或 IP 位址  :44368**。

   或者，您也可以按一下應用程式捷徑，從設備桌面開啟應用程式。
4. 在 [Web 應用程式] > [設定必要條件]  中，執行下列動作：
    - **授權**：接受授權條款，並閱讀第三方資訊。
    - **連線能力**：應用程式會確認 VM 是否能夠存取網際網路。 如果 VM 使用 Proxy：
        - 按一下 [Proxy 設定]  ，然後以 http://ProxyIPAddress 或 http://ProxyFQDN 格式指定 Proxy 位址和接聽連接埠。
        - 如果 Proxy 需要驗證，請指定認證。
        - 僅支援 HTTP Proxy。
    - **時間同步**：設備上的時間應該與網際網路時間同步，探索才能正常運作。
    - **安裝更新**：設備會確保已安裝最新的更新。
    - **安裝 VDDK**：設備會檢查是否已安裝 VMWare vSphere 虛擬磁碟開發套件 (VDDK)。
        - Azure Migrate：在遷移至 Azure 期間，伺服器移轉會使用 VDDK 來複寫機器。
        - 從 VMware 下載 VDDK 6.7，並將下載的 zip 內容解壓縮到設備上的指定位置。

### <a name="register-the-appliance-with-azure-migrate"></a>向 Azure Migrate 註冊設備

1. 按一下 [登入]  。 如果未出現，請確定您已在瀏覽器中停用快顯封鎖程式。
2. 在新的索引標籤上，使用您的 Azure 認證登入。
    - 以您的使用者名稱和密碼登入。
    - 不支援使用 PIN 登入。
3. 成功登入後，返回 Web 應用程式。
2. 選取 Azure Migrate 專案建立所在的訂用帳戶，然後選取專案。
3. 指定設備的名稱。 名稱應該是英數位元，且長度不超過 14 個字元。
4. 按一下 [註冊]  。


## <a name="start-continuous-discovery"></a>開始連續探索

現在，從設備連線到 vCenter Server，然後啟動 VM 探索。

1. 在 [指定 vCenter Server 詳細資料]  中，指定 vCenter Server 的名稱 (FQDN) 或 IP 位址。 您可以保留預設的連接埠，或指定您 vCenter Server 接聽的自訂連接埠。
2. 在 [使用者名稱]  和 [密碼]  中，指定設備要用來探索 vCenter Server 上之 VM 的唯讀帳戶認證。 請確定帳戶具有[探索所需的權限](migrate-support-matrix-vmware.md#assessment-vcenter-server-permissions)。 您可以藉由限制 vCenter 帳戶的存取來界定探索的範圍；[請從此處](tutorial-assess-vmware.md#scoping-discovery)深入了解探索範圍。
3. 按一下 [驗證連線]  以確定設備可以連線到 vCenter Server。
4. 建立連線之後，請按一下 [儲存並啟動探索]  。

這會開始探索。 所探索到 VM 的中繼資料會在大約 15 分鐘後出現在入口網站中。

### <a name="scoping-discovery"></a>界定探索的範圍

藉由限制用於探索的 vCenter 帳戶存取權，即可界定探索範圍。 您可以將範圍設定為 vCenter Server 資料中心、叢集、叢集的資料夾、主機、主機的資料夾或個別 VM。 

> [!NOTE]
> 目前，如果 vCenter 帳戶具有在 vCenter VM 資料夾層級上授與的存取權，則伺服器評量無法探索 VM。 如果您想要依據 VM 資料夾來界定探索範圍，只要確保 vCenter 帳戶在 VM 層級上已獲派唯讀存取權即可。  以下是您可以執行此動作的指示：
>
> 1. 在您要設定探索範圍的 VM 資料夾中，為所有 VM 指派唯讀權限。 
> 2. 對所有裝載 VM 的父物件授與唯讀存取權。 到資料中心為止的階層中，所有父物件 (主機、主機的資料夾、叢集、叢集的資料夾) 都會包含在內。 您不需要將權限傳播給所有子物件。
> 3. 使用用於探索的認證，將資料中心選取為「集合範圍」  。 RBAC 設定會確保對應的 vCenter 使用者只能存取租用戶特定 VM。
>
> 請注意，主機和叢集的資料夾都可支援。

### <a name="verify-vms-in-the-portal"></a>在入口網站中確認 VM

探索完成後，便可以確認 VM 是否出現在 Azure 入口網站中。

1. 開啟 Azure Migrate 儀表板。
2. 在 Azure Migrate - 伺服器   >  **Azure Migrate：伺服器評量** 頁面中，按一下圖示以顯示 探索到的伺服器  計數。

## <a name="set-up-an-assessment"></a>設定評估

您可以使用 Azure Migrate：伺服器評量來建立的評量有兩種。

**評量** | **詳細資料** | **資料**
--- | --- | ---
**以效能為基礎** | 以所收集效能資料為基礎的評估 | **建議的 VM 大小**：以 CPU 和記憶體使用量資料為基礎。<br/><br/> **建議的磁碟類型 (標準或進階受控磁碟**)：以內部部署磁碟的 IOPS 和輸送量為基礎。
**作為內部部署** | 以內部部署大小調整為基礎的評估。 | **建議的 VM 大小**：以內部部署 VM 大小為基礎<br/><br> **建議的磁碟類型**：以您為評估選取的儲存體類型設定為基礎。


### <a name="run-an-assessment"></a>執行評估

執行評估，如下所示：

1. 檢閱適用於建立評估的[最佳做法](best-practices-assessment.md)。
2. 在 [伺服器]  索引標籤的 **[Azure Migrate：伺服器評量]** 磚中，按一下 [評估]  。

    ![評估](./media/tutorial-assess-vmware/assess.png)

2. 在 [評估伺服器]  中，指定評估的名稱。
3. 按一下 [檢視全部]  來檢閱評估屬性。

    ![評量屬性](./media/tutorial-assess-vmware/view-all.png)

3. 在 [選取或建立群組]  中，選取 [新建]  ，然後指定群組名稱。 群組會將一或多個 VM 收集在一起以進行評估。
4. 在 [將機器新增至群組]  中，選取要新增至群組的 VM。
5. 按一下 [建立評估]  以建立群組，然後執行評估。

    ![建立評估](./media/tutorial-assess-vmware/assessment-create.png)

6. 評量建立好之後，可在 [伺服器]   >  **[Azure Migrate：伺服器評量]**  > [評量]  中加以檢視。
7. 按一下 [匯出評估]  ，將其下載為 Excel 檔案。



## <a name="review-an-assessment"></a>檢閱評量

評量會說明：

- **Azure 移轉整備程度**：VM 是否適合移轉至 Azure。
- **每月成本預估**：在 Azure 中執行 VM 的預估每月計算和儲存體成本。
- **每月儲存體成本預估**：移轉之後的磁碟儲存體預估成本。

### <a name="view-an-assessment"></a>檢視評估

1. 在 [移轉目標]   >  [伺服器]  中，按一下 [Azure Migrate：伺服器評量] **中的 [評量] ** 。
2. 在 [評量]  中，按一下評量來加以開啟。

    ![評量摘要](./media/tutorial-assess-vmware/assessment-summary.png)

### <a name="review-azure-readiness"></a>檢閱 Azure 移轉整備程度

1. 在 [Azure 移轉整備程度]  中，確認 VM 是否已準備好移轉至 Azure。
2. 檢查 VM 狀態：
    - **可供 Azure 使用**：Azure Migrate 會針對評估中的 VM 建議 VM大小和成本估計值。
    - **有條件的備妥**：顯示問題和建議的補救措施。
    - **未備妥供 Azure 使用**：顯示問題和建議的補救措施。
    - **移轉整備程度未知**：當 Azure Migrate 因資料可用性問題而無法評估整備程度時，便會使用此狀態。

2. 按一下 [Azure 移轉整備程度]  狀態。 您可以檢視 VM 的整備程度詳細資料，並向下切入以查看 VM 詳細資料，包括計算、儲存體和網路設定。



### <a name="review-cost-details"></a>檢閱成本詳細資料

此檢視會顯示在 Azure 中執行 VM 的計算和儲存體預估成本。

1. 檢閱每月的計算和儲存體成本。 系統會針對評估群組中的所有 VM 匯總成本。

    - 系統會根據機器的大小建議和其磁碟與屬性來預估成本。
    - 系統會顯示計算和儲存體的每月預估成本。
    - 以 IaaS VM 的形式來執行內部部署 VM 時，才能估計成本。 Azure Migrate 伺服器評估不會考慮 PaaS 或 SaaS 的成本。

2. 您可以檢閱每月的儲存體成本估計值。 此檢視會顯示評估群組的彙總儲存體成本，並分割為不同類型的儲存體磁碟。
3. 您可以向下切入以查看特定 VM 的詳細資料。


### <a name="review-confidence-rating"></a>檢閱信賴評等

當您執行以效能為基礎的評估時，系統會對評估指派信賴評等。

![信賴評等](./media/tutorial-assess-vmware/confidence-rating.png)

- 會給予 1 星 (最低) 到 5 星 (最高) 的評等。
- 信賴評等可協助您預估評估作業所提供的大小建議是否可靠。
- 信賴評等會以計算評估所需的資料點可用性為基礎。

評估的信賴評等如下所示。

**資料點可用性** | **信賴評等**
--- | ---
0%-20% | 1 顆星
21%-40% | 2 顆星
41%-60% | 3 顆星
61%-80% | 4 顆星
81%-100% | 5 顆星

[深入了解](best-practices-assessment.md#best-practices-for-confidence-ratings)信賴評等的最佳做法。


## <a name="next-steps"></a>後續步驟

在本教學課程中，您：

> [!div class="checklist"]
> * 已設定 Azure Migrate 設備
> * 已建立和檢閱評估

請繼續進行本系列的第三個教學課程，以了解如何使用 Azure Migrate 伺服器移轉將 VMware VM 遷移至 Azure。

> [!div class="nextstepaction"]
> [遷移 VMware VM](./tutorial-migrate-vmware.md)
