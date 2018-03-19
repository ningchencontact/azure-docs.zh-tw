---
title: "Azure 入口網站：建立 SQL Database 受控執行個體 | Microsoft Docs"
description: "在 VNet 中建立 Azure SQL Database 受控執行個體，並使用 SSMS 來還原 Wide World Importers 資料庫備份。"
keywords: "SQL Database 教學課程, 建立 SQL Database 受控執行個體"
services: sql-database
author: bonova
ms.reviewer: carlrab, srbozovi
ms.service: sql-database
ms.custom: managed instance
ms.workload: Active
ms.tgt_pltfrm: portal
ms.devlang: 
ms.topic: tutorial
ms.date: 03/07/2018
ms.author: bonova
manager: cguyer
ms.openlocfilehash: 0d6261392dfdab0d48cb0c524d1fcf416c85d72c
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2018
---
# <a name="create-an-azure-sql-database-managed-instance-in-the-azure-portal"></a>在 Azure 入口網站中建立 Azure SQL Database 受控執行個體

本教學課程會示範如何使用 Azure 入口網站，在虛擬網路 (VNet) 的專用子網路中建立 Azure SQL Database 受控執行個體 (預覽)。 接著示範如何在相同 VNet 中的虛擬機器上使用 SQL Server Management Studio 連線到此受控執行個體，然後將 Azure blob 儲存體中儲存的資料庫備份還原為受控執行個體。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/) 。

## <a name="log-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/#create/Microsoft.SQLManagedInstance)。

## <a name="whitelist-your-subscription"></a>將您的訂用帳戶列入白名單

受控執行個體一開始是以閘道公開預覽版的形式發行，其要求將您的訂用帳戶列入白名單中。 如果您的訂用帳戶尚未列入白名單，請使用以下提供的步驟並接受預覽條款及傳送列入白名單的要求。

1. 按一下 Azure 入口網站左上角的 [建立資源]。
2. 找出 [受控執行個體]，然後選取 [Azure SQL Database 受控執行個體 (預覽)]。
3. 按一下頁面底部的 [新增] 。

   ![受控執行個體 - 建立](./media/sql-database-managed-instance-tutorial/managed-instance-create.png)

3. 選取您的訂用帳戶，按一下 [預覽條款]，然後提供所要求的資訊。

   ![受控執行個體預覽條款](./media/sql-database-managed-instance-tutorial/preview-terms.png)

5. 完成時按一下 [確定]。

   ![受控執行個體預覽條款](./media/sql-database-managed-instance-tutorial/preview-approval-pending.png)

   > [!NOTE]
   > 等待預覽核准時，您可以繼續執行並完成本教學課程的後續幾節。

## <a name="configure-a-virtual-network-vnet"></a>設定虛擬網路 (VNet)

下列步驟說明如何建立新的 [Azure Resource Manager](../azure-resource-manager/resource-manager-deployment-model.md) 虛擬網路 (VNet)，以供您的受控執行個體使用。 如需 VPN 組態的詳細資訊，請參閱[受控執行個體 VNet 組態](sql-database-managed-instance-vnet-configuration.md)。

1. 按一下 Azure 入口網站左上角的 [建立資源]。
2. 尋找然後按一下 [虛擬網路]，確認 [Resource Manager] 已選取作為部署模式，然後按一下 [建立]。

   ![虛擬網路 - 建立](./media/sql-database-managed-instance-tutorial/virtual-network-create.png)

3. 使用下表中的資訊，填妥虛擬網路表單中所要求的資訊：

   | 設定| 建議的值 | 說明 |
   | ------ | --------------- | ----------- |
   |**名稱**|任何有效名稱|如需有效的名稱，請參閱[命名規則和限制](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)。|
   |**位址空間**|任何有效的位址範圍，例如 10.14.0.0/24|採用 CIDR 標記法的虛擬網路位址名稱。|
   |**訂用帳戶**|您的訂用帳戶|如需訂用帳戶的詳細資訊，請參閱[訂用帳戶](https://account.windowsazure.com/Subscriptions)。|
   |**資源群組**|任何有效的資源群組 (新的或現有)|如需有效的名稱，請參閱[命名規則和限制](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)。|
   |**位置**|任何有效位置| 如需區域的相關資訊，請參閱 [Azure 區域](https://azure.microsoft.com/regions/)。|
   |**子網路名稱**|任何有效的子網路名稱，例如 mi_subnet|如需有效的名稱，請參閱[命名規則和限制](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)。|
   |**子網路位址範圍**|任何有效的子網路位址，例如 10.14.0.0/28。 使用小於位址空間本身的子網路位址空間，以提供空間在相同的 VNet 中建立其他子網路，例如用於裝載測試 / 用戶端應用程式的子網路，或可從內部部署或其他 Vnet 連線的閘道子網路。|採用 CIDR 標記法的子網路位址範圍。 它必須包含在虛擬網路的位址空間中|
   |**服務端點**|已停用|為此子網路啟用一個或多個服務端點|
   ||||

   ![虛擬網路建立表單](./media/sql-database-managed-instance-tutorial/virtual-network-create-form.png)

4. 按一下頁面底部的 [新增] 。

## <a name="create-new-route-table-and-a-route"></a>建立新的路由表和路由

下列步驟顯示如何建立 0.0.0.0/0 下一個躍點網際網路路由。

1. 按一下 Azure 入口網站左上角的 [建立資源]。
2. 尋找而後按一下 [路由表]，然後在路由表頁面上按一下 [建立]。 

   ![路由表 - 建立](./media/sql-database-managed-instance-tutorial/route-table-create.png)

3. 使用下表中的資訊，填妥路由表表單中所要求的資訊：

   | 設定| 建議的值 | 說明 |
   | ------ | --------------- | ----------- |
   |**名稱**|任何有效名稱|如需有效的名稱，請參閱[命名規則和限制](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)。|
   |**訂用帳戶**|您的訂用帳戶|如需訂用帳戶的詳細資訊，請參閱[訂用帳戶](https://account.windowsazure.com/Subscriptions)。|
   |**資源群組**|選取您在上一個程序中建立的資源群組|如需有效的名稱，請參閱[命名規則和限制](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)。|
   |**位置**|選取您在上一個程序中指定的位置| 如需區域的相關資訊，請參閱 [Azure 區域](https://azure.microsoft.com/regions/)。|
   |**停用 BCP 路由傳播**|已停用||
   ||||

   ![路由表建立表單](./media/sql-database-managed-instance-tutorial/route-table-create-form.png)

4. 按一下頁面底部的 [新增] 。
5. 建立路由表之後，請開啟新建立的路由表。

   ![路由表](./media/sql-database-managed-instance-tutorial/route-table.png)

6. 按一下 [路由]，然後按一下 [新增]。

   ![路由表 - 新增](./media/sql-database-managed-instance-tutorial/route-table-add.png)

7.  請使用下表中的資訊，將 **0.0.0.0/0 下一個躍點網際網路路由**新增為**唯一的**路由：

    | 設定| 建議的值 | 說明 |
    | ------ | --------------- | ----------- |
    |**路由名稱**|任何有效名稱|如需有效的名稱，請參閱[命名規則和限制](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)。|
    |**位址前置詞**|0.0.0.0/0|此路由要套用至的目的地 IP 位址 (採用 CIDR 標記法)。|
    |**下一個躍點類型**|Internet|下一個躍點會處理此路由的相符封包|
    |||

    ![路由](./media/sql-database-managed-instance-tutorial/route.png)

8. 按一下 [SERVICEPRINCIPAL] 。

## <a name="to-apply-the-route-table-to-the-managed-instance-subnet"></a>將路由表套用到受控執行個體子網路

下列步驟顯示如何在受控執行個體子網路上設定新的路由表。

1. 若要在受控執行個體子網路上設定路由表，請開啟您稍早建立的虛擬網路。
2. 按一下 [子網路]，然後按一下 [受控執行個體子網路] (下列螢幕擷取畫面中的 **mi_subnet**)。

    ![子網路](./media/sql-database-managed-instance-tutorial/subnet.png)

11. 按一下 [路由表]，然後選取 [myMI_route_table]。

    ![設定路由表](./media/sql-database-managed-instance-tutorial/set-route-table.png)

12. 按一下 [儲存] 

    ![設定路由表 - 儲存](./media/sql-database-managed-instance-tutorial/set-route-table-save.png)

## <a name="create-a-managed-instance"></a>建立受控執行個體

下列步驟顯示在您的預覽版獲得核准之後，如何建立受控執行個體。

1. 按一下 Azure 入口網站左上角的 [建立資源]。
2. 找出 [受控執行個體]，然後選取 [Azure SQL Database 受控執行個體 (預覽)]。
3. 按一下頁面底部的 [新增] 。

   ![受控執行個體 - 建立](./media/sql-database-managed-instance-tutorial/managed-instance-create.png)

3. 選取您的訂用帳戶並確認預覽條款顯示 [已接受]。

   ![受控執行個體預覽 - 已接受](./media/sql-database-managed-instance-tutorial/preview-accepted.png)

4. 使用下表中的資訊，填妥受控執行個體表單中所要求的資訊：

   | 設定| 建議的值 | 說明 |
   | ------ | --------------- | ----------- |
   |**受控執行個體名稱**|任何有效名稱|如需有效的名稱，請參閱[命名規則和限制](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)。|
   |**受控執行個體系統管理員登入**|任何有效的使用者名稱|如需有效的名稱，請參閱[命名規則和限制](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)。| 
   |**密碼**|任何有效密碼|密碼長度至少必須有 12 個字元，而且符合[定義的複雜度需求](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)。|
   |**資源群組**|您先前建立的資源群組||
   |**位置**|您先前選取的位置|如需區域的相關資訊，請參閱 [Azure 區域](https://azure.microsoft.com/regions/)。|
   |**虛擬網路**|您先前建立的虛擬網路|

   ![受控執行個體建立表單](./media/sql-database-managed-instance-tutorial/managed-instance-create-form.png)

5. 按一下 [定價層] 來調整計算和儲存體資源的大小，以及檢閱定價層選項。 根據預設，您的執行個體會取得 32 GB 的免費儲存空間，這對您的應用程式而言可能不夠。
6. 使用滑桿或文字方塊來指定儲存體數量和虛擬核心數目。 
   ![受控執行個體建立表單](./media/sql-database-managed-instance-tutorial/managed-instance-pricing-tier.png)

7. 完成時，按一下 [套用] 以儲存您的選取項目。  
8. 按一下 [建立] 以部署受控執行個體。
9. 按一下 [通知] 圖示以檢視部署的狀態。
 
   ![部署進度](./media/sql-database-managed-instance-tutorial/deployment-progress.png)

9. 按一下 [部署正在進行中] 來開啟受控執行個體視窗，以進一步監視部署進度。
 
   ![部署進度 2](./media/sql-database-managed-instance-tutorial/managed-instance.png)

進行部署時，請繼續下一個程序。

> [!IMPORTANT]
> 如果是子網路中的第一個執行個體，部署時間通常遠超過後續執行個體的部署時間 - 有時超過 24 小時才能完成。 請勿取消部署作業，因為它持續的時間超過您的預期。 用來部署您第一個執行個體的時間長度是臨時狀況。 在公開預覽版開始不久後，部署時間預計會大幅減少。

## <a name="create-a-new-subnet-in-the-vnet-for-a-virtual-machine"></a>在虛擬機器的 VNet 中建立新的子網路

下列步驟顯示如何在您安裝 SQL Server Management Studio 之虛擬機器的 VNet 中建立第二個子網路，並連線到您的受控執行個體。

1. 開啟您的虛擬網路資源。
 
   ![VNet](./media/sql-database-managed-instance-tutorial/vnet.png)

2. 按一下 [子網路]，然後按一下 [+子網路]。
 
   ![新增子網路](./media/sql-database-managed-instance-tutorial/add-subnet.png)

3. 使用下表中的資訊，填妥子網路表單中所要求的資訊：

   | 設定| 建議的值 | 說明 |
   | ------ | --------------- | ----------- |
   |**名稱**|任何有效名稱|如需有效的名稱，請參閱[命名規則和限制](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)。|
   |**位址範圍 (CIDR 區塊)**|VNet 中任何有效的位址範圍 (使用預設值)||
   |**網路安全性群組**|None||
   |**路由表**|None||
   |**服務端點**|None||

   ![VM 子網路詳細資料](./media/sql-database-managed-instance-tutorial/vm-subnet-details.png)

4. 按一下 [SERVICEPRINCIPAL] 。

## <a name="create-a-virtual-machine-in-the-new-subnet-in-the-vnet"></a>在 VNet 的新子網路中建立虛擬機器

下列步驟顯示如何在建立受控執行個體的相同 VNet 中建立虛擬機器。 

1. 按一下 Azure 入口網站左上角的 [建立資源]。
2. 選取 [計算]，然後選取 [Windows Server 2016 Datacenter] 或 [Windows 10]。 本教學課程的這一節使用 Windows Server。 設定 Windows 10 大致類似。 

   ![計算](./media/sql-database-managed-instance-tutorial/compute.png)

3. 使用下表中的資訊，填妥虛擬機器表單中所要求的資訊：

   | 設定| 建議的值 | 說明 |
   | ------ | --------------- | ----------- |
   |**名稱**|任何有效名稱|如需有效的名稱，請參閱[命名規則和限制](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)。|
   | **VM 磁碟類型**|SSD|SSD 提供價格與效能之間的最佳平衡。|   
   |**使用者名稱**|任何有效的使用者名稱|如需有效的名稱，請參閱[命名規則和限制](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)。| 
   |**密碼**|任何有效密碼|密碼長度至少必須有 12 個字元，而且符合[定義的複雜度需求](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)。| 
   |**訂用帳戶**|您的訂用帳戶|如需訂用帳戶的詳細資訊，請參閱[訂用帳戶](https://account.windowsazure.com/Subscriptions)。|
   |**資源群組**|您先前建立的資源群組||
   |**位置**|您先前選取的位置||
   |**已經有 Windows 授權**|否|如果您擁有 Windows 授權與使用中的軟體保證 (SA)，即可使用 Azure Hybrid Benefit 節省計算成本|
   ||||

   ![虛擬機器建立表單](./media/sql-database-managed-instance-tutorial/virtual-machine-create-form.png)

3. 按一下 [SERVICEPRINCIPAL] 。
4. 選取 VM 的大小。 若要查看更多大小，請選取 [檢視全部] 或變更 [支援的磁碟類型] 篩選條件。 在本教學課程中，您只需要小型虛擬機器。

    ![VM 大小](./media/sql-database-managed-instance-tutorial/virtual-machine-size.png)  

5. 按一下 [選取] 。
6. 在 [設定] 表單上，按一下 [子網路]，然後選取 [vm_subnet]。 請勿選擇受控執行個體佈建所在的子網路，而是相同 Vnet 中的另一個子網路。

    ![VM 設定](./media/sql-database-managed-instance-tutorial/virtual-machine-settings.png)  

7. 按一下 [SERVICEPRINCIPAL] 。
8. 在摘要頁面上，檢閱供應項目詳細資料，然後按一下 [建立] 開始進行虛擬機器部署。
 
## <a name="connect-to-virtual-machine"></a>連線至虛擬機器

下列步驟顯示如何使用遠端桌面連線來連線到新建立的虛擬機器。

1. 部署完成之後，請移至虛擬機器資源。

    ![VM](./media/sql-database-managed-instance-tutorial/vm.png)  

2. 按一下虛擬機器屬性上的 [連線] 按鈕。 隨即建立並下載遠端桌面通訊協定檔案 (.rdp 檔案)。
3. 若要連線至您的 VM，請開啟下載的 RDP 檔案。 
4. 出現提示時，按一下 [連線]。 在 Mac 上，您需要 RDP 用戶端，例如來自 Mac App Store 的[遠端桌面用戶端](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12)。

5. 輸入在建立虛擬機器時指定的使用者名稱和密碼，然後按一下 [確定]。

6. 您可能會在登入過程中收到憑證警告。 按一下 [是] 或 [繼續] 以繼續進行連線。

您已連線到 [伺服器管理員] 儀表板中的虛擬機器。

> [!IMPORTANT]
> 直到您的受控執行個體佈建成功，才能繼續執行。 佈建之後，在您受控執行個體之 [概觀] 索引標籤上的 [受控執行個體] 欄位中，擷取您執行個體的主機名稱。 此名稱類似於：**drfadfadsfd.tr23.westus1-a.worker.database.windows.net**。

## <a name="install-ssms-and-connect-to-the-managed-instance"></a>安裝 SSMS 並連線到受控執行個體

下列步驟顯示如何下載並安裝 SSMS，然後連線到您的受控執行個體。

1. 在 [伺服器管理員] 中，按一下左窗格中的 [本機伺服器] 。

    ![伺服器管理員屬性](./media/sql-database-managed-instance-tutorial/server-manager-properties.png)  

2. 按一下 [屬性] 窗格中的 [開啟]，以修改 [IE 增強式安全性組態]。
3. 在 [Internet Explorer 增強式安全性組態] 對話方塊中，按一下 [系統管理員] 區段中的 [關閉]，然後按一下 [確定]。

    ![Internet Explorer 增強式安全性組態](./media/sql-database-managed-instance-tutorial/internet-explorer-security-configuration.png)  
4. 從工作列開啟 [Internet Explorer]。
5. 選取 [使用建議的安全性和相容性設定]，然後按一下 [確定] 以完成 Internet Explorer 11 的設定。
6. 在 URL 網址方塊中輸入 https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms，然後按一下 **Enter**。 
7. 下載最新版的 SQL Server Management Studio，然後在出現提示時按一下 [執行]。
8. 出現提示時，按一下 [安裝] 開始進行安裝。
9. 當安裝完成時，按一下 [關閉]。
10. 開啟 SSMS。
11. 在 [連線到伺服器] 對話方塊中，於 [伺服器名稱] 方塊中輸入您受控執行個體的 [主機名稱]，選取 [SQL Server 驗證]，提供您的登入和密碼，然後按一下 [連線]。

    ![SSMS 連線](./media/sql-database-managed-instance-tutorial/ssms-connect.png)  

連線之後，您可以檢視 [資料庫] 節點中的系統和使用者資料庫，以及 [安全性]、[伺服器物件]、[複寫]、[管理]、[SQL Server 代理程式] 和 [XEvent Profiler] 節點中的各種物件。

## <a name="download-the-wide-world-importers---standard-backup-file"></a>下載 Wide World Importers - 標準備份檔案

使用下列步驟來下載 Wide World Importers - 標準備份檔案。

使用 Internet Explorer，在 URL 網址方塊中輸入 https://github.com/Microsoft/sql-server-samples/releases/download/wide-world-importers-v1.0/WideWorldImporters-Standard.bak，然後在出現提示時按一下 [儲存] 將此檔案儲存在 [下載] 資料夾中。

## <a name="create-azure-storage-account-and-upload-backup-file"></a>建立 Azure 儲存體帳戶及上傳備份檔案

1. 按一下 Azure 入口網站左上角的 [建立資源]。
2. 找出 [儲存體]，然後按一下 [儲存體帳戶] 以開啟儲存體帳戶表單。

   ![storage account](./media/sql-database-managed-instance-tutorial/storage-account.png)

3. 使用下表中的資訊，填妥儲存體帳戶表單中所要求的資訊：

   | 設定| 建議的值 | 說明 |
   | ------ | --------------- | ----------- |
   |**名稱**|任何有效名稱|如需有效的名稱，請參閱[命名規則和限制](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)。|
   |**部署模型**|資源模型||
   |**帳戶類型**|Blob 儲存體||
   |**效能**|標準或進階|磁碟機或 SSD|
   |**複寫**|本地備援儲存體||
   |**存取層 (預設值)|非經常性或經常性||
   |**需要安全傳輸**|已停用||
   |**訂用帳戶**|您的訂用帳戶|如需訂用帳戶的詳細資訊，請參閱[訂用帳戶](https://account.windowsazure.com/Subscriptions)。|
   |**資源群組**|您先前建立的資源群組|| 
   |**位置**|您先前選取的位置||
   |**虛擬網路**|已停用||

4. 按一下頁面底部的 [新增] 。

   ![儲存體帳戶詳細資料](./media/sql-database-managed-instance-tutorial/storage-account-details.png)

5. 在儲存體帳戶部署成功之後，開啟新的儲存體帳戶。
6. 按一下 [設定] 之下的 [共用存取簽章]，以開啟共用存取簽章 (SAS) 表單。

   ![sas 表單](./media/sql-database-managed-instance-tutorial/sas-form.png)

7. 在 SAS 表單上，視需要修改預設值。 請注意，到期日期/時間預設只有 8 小時。
8. 按一下 [產生 SAS]。

   ![完成的 sas 表單](./media/sql-database-managed-instance-tutorial/sas-generate.png)

9. 複製並儲存 [SAS 權杖] 和 [Blob 伺服器 SAS URL]。
10. 按一下 [設定] 下方的 [容器]。

    ![containers](./media/sql-database-managed-instance-tutorial/containers.png)

11. 按一下 [+容器]，建立容器來保存備份檔案。
12. 使用下表中的資訊，填妥容器表單中所要求的資訊：

    | 設定| 建議的值 | 說明 |
   | ------ | --------------- | ----------- |
   |**名稱**|任何有效名稱|如需有效的名稱，請參閱[命名規則和限制](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)。|
   |**公用存取層級**|私人 (沒有匿名存取)||

    ![容器詳細資料](./media/sql-database-managed-instance-tutorial/container-detail.png)

13. 按一下 [SERVICEPRINCIPAL] 。
14. 建立容器之後，請開啟該容器。

    ![container](./media/sql-database-managed-instance-tutorial/container.png)

15. 按一下 [容器屬性]，然後將 URL 複製到容器。

    ![容器 URL](./media/sql-database-managed-instance-tutorial/container-url.png)

16. 按一下 [上傳] 以開啟 [上傳 Blob] 表單。

    ![upload](./media/sql-database-managed-instance-tutorial/upload.png)

17. 瀏覽至您的下載資料夾，然後選取 **AdventureWorks2016.bak** 檔案。

    ![upload](./media/sql-database-managed-instance-tutorial/upload-bak.png)

18. 按一下 [上傳] 。
19. 直到上傳完成，才能繼續執行。

    ![上傳完成](./media/sql-database-managed-instance-tutorial/upload-complete.png)


## <a name="restore-the-wide-world-importers-database-from-a-backup-file"></a>從備份檔案還原 Wide World Importers 資料庫

透過 SSMS，使用下列步驟將 Wide World Importers 資料庫從備份檔案還原到您的受控執行個體。

1. 在 SSMS 中，開啟新的查詢視窗。
2. 使用下列指令碼來建立 SAS 認證 - 依照指示提供儲存體帳戶容器的 URL 和 SAS 金鑰。

   ```
CREATE CREDENTIAL [https://<storage_account_name>.blob.core.windows.net/<container>] 
WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
, SECRET = '<shared_access_signature_key_with_removed_first_?_symbol>' 
   ```

    ![認證](./media/sql-database-managed-instance-tutorial/credential.png)

3. 使用下列指令碼來檢查 SAS 認證和備份有效性 - 提供含有備份檔案之容器的 URL：

   ```
   RESTORE FILELISTONLY FROM URL = 
   'https://<storage_account_name>.blob.core.windows.net/<container>/WideWorldImporters-Standard.bak'
   ```

    ![檔案清單](./media/sql-database-managed-instance-tutorial/file-list.png)

4. 使用下列指令碼從備份檔案還原 Adventure Works 2012 資料庫 - 提供含有備份檔案之容器的 URL：

   ```
   RESTORE DATABASE [Wide World Importers] FROM URL =
  'https://<storage_account_name>.blob.core.windows.net/<container>/WideWorldImporters-Standard.bak'`
   ```

    ![還原執行中](./media/sql-database-managed-instance-tutorial/restore-executing.png)

5. 若要追蹤還原的狀態，請在新的查詢工作階段中執行下列查詢：

   ```
SELECT session_id as SPID, command, a.text AS Query, start_time, percent_complete
, dateadd(second,estimated_completion_time/1000, getdate()) as estimated_completion_time 
FROM sys.dm_exec_requests r 
CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) a 
WHERE r.command in ('BACKUP DATABASE','RESTORE DATABASE')`
   ```

    ![還原完成百分比](./media/sql-database-managed-instance-tutorial/restore-percent-complete.png)

6. 當還原完成時，請在 [物件總管] 加以檢視。 

    ![還原完成](./media/sql-database-managed-instance-tutorial/restore-complete.png)

## <a name="next-steps"></a>後續步驟

- 如需受控執行個體的詳細資訊，請參閱[受控執行個體是什麼？](sql-database-managed-instance.md)。
- 如需 VPN 組態的詳細資訊，請參閱[受控執行個體 VNet 組態](sql-database-managed-instance-vnet-configuration.md)。
- 如需應用程式連線的相關資訊，請參閱[應用程式連線](sql-database-managed-instance-connect-app.md)。
- 如需使用 Azure 資料庫移轉服務 (DMS) 進行移轉的教學課程，請參閱[使用 DMS 的受控執行個體移轉](../dms/tutorial-sql-server-to-managed-instance.md)。
