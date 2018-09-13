---
title: Azure 入口網站：建立 SQL 受控執行個體 | Microsoft Docs
description: 建立 SQL 受控執行個體、網路環境，以及用於存取的用戶端 VM。
keywords: SQL Database 教學課程, 建立 SQL 受控執行個體
services: sql-database
author: jovanpop-msft
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: quickstart
ms.date: 08/31/2018
ms.author: jovanpop-msft
ms.openlocfilehash: 4271f0cef31b0e028ed1f9408166c37d4cbbe109
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/01/2018
ms.locfileid: "43381993"
---
# <a name="create-an-azure-sql-managed-instance"></a>建立 Azure SQL 受控執行個體

本快速入門將逐步解說如何在 Azure 中建立 SQL 受控執行個體。 Azure SQL Database 受控執行個體是一個「平台即服務」(PaaS) SQL Server Database Engine 執行個體，可讓您在 Azure 雲端中執行及調整高可用性 SQL Server 資料庫。 本快速入門說明如何建立 SQL 受控執行個體以開始使用產品。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="prepare-network-environment"></a>準備網路環境

SQL 受控執行個體是一項放置在您自身 Azure 虛擬網路 (VNet) 中的安全服務。 若要建立受控執行個體，您必須準備 Azure 網路環境，其中包括：
 - 將放置受控執行個體的 Azure VNet。
 - Azure VNet 中將用來放置受控執行個體的子網路。
 - 可讓受控執行個體與控制和管理執行個體的 Azure 服務進行通訊的使用者定義路由。

子網路專門供受控執行個體使用，您無法在該子網路中建立的任何其他資源 (例如 Azure 虛擬機器)。 本快速入門會在您的 Azure VNet 中建立兩個子網路，以讓您將受控執行個體放在受控執行個體專用的子網路中，並將其他資源放在預設子網路中。

1. 按一下以下按鈕，以部署為 Azure SQL Database 受控執行個體準備的 Azure 網路環境：

    <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener"> <img src="http://azuredeploy.net/deploybutton.png"> </a>

    此按鈕會在 Azure 入口網站中開啟一個表單，讓您可在實際部署之前設定您的網路環境。

2. 您可以選擇性地變更 VNet 與子網路的名稱，以及調整與您的網路資源關聯的 IP 位址。 然後，按下 [購買] 按鈕，以建立並設定您的環境：

    ![建立受控執行個體環境](./media/sql-database-managed-instance-get-started/create-mi-network-arm.png)

    > [!Note]
    > 此 Azure Resource Manager 部署會在 VNet 中建立兩個子網路 - 一個用於受控執行個體 (名為 **ManagedInstances**)，另一個名為 **Default**，用於其他資源，例如可用來連線至受控執行個體的用戶端虛擬機器。 如果您不需要兩個子網路，您可於後續刪除預設子網路；但在此情況下，您將無法完成本快速入門指南中的步驟 3 - [準備用戶端機器](#prepare-client-machine)。

    > [!Note]
    > 如果您變更 VNet 和子網路的名稱，請確實記住新名稱，因為在後續章節中將會用到這些名稱。 本教學課程的其餘部分將假設您已建立名為 **MyNewVNet** 的 VNet、SQL 受控執行個體的 **ManagedInstances** 子網路，以及虛擬機器和其他資源的 **Default** 子網路。

## <a name="create-a-managed-instance"></a>建立受控執行個體

下列步驟顯示在您的預覽版獲得核准之後，如何建立受控執行個體。

1. 按一下 Azure 入口網站左上角的 [建立資源]。
2. 找出 [受控執行個體]，然後選取 [Azure SQL Database 受控執行個體 (預覽)]。
3. 按一下頁面底部的 [新增] 。

   ![建立受控執行個體](./media/sql-database-managed-instance-get-started/managed-instance-create.png)

4. 選取您的訂用帳戶並確認預覽條款顯示 [已接受]。

   ![受控執行個體預覽 - 已接受](./media/sql-database-managed-instance-tutorial/preview-accepted.png)

5. 使用下表中的資訊，填妥受控執行個體表單中所要求的資訊：

   | 設定| 建議的值 | 說明 |
   | ------ | --------------- | ----------- |
   |**受控執行個體名稱**|任何有效名稱|如需有效的名稱，請參閱[命名規則和限制](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)。|
   |**受控執行個體系統管理員登入**|任何有效的使用者名稱|如需有效的名稱，請參閱[命名規則和限制](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)。 請勿使用 "serveradmin"，因為這是保留的伺服器層級角色。| 
   |**密碼**|任何有效密碼|密碼長度至少必須有 16 個字元，而且符合[定義的複雜度需求](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)。|
   |**資源群組**|您先前建立的資源群組||
   |**位置**|您先前選取的位置|如需區域的相關資訊，請參閱 [Azure 區域](https://azure.microsoft.com/regions/)。|
   |**虛擬網路**|您先前建立的虛擬網路| 如果您未變更上一個步驟中的名稱，請選擇 **MyNewVNet/ManagedInstances** 項目。 否則，請選擇您在上一節中輸入的 VNet 名稱和受控執行個體子網路。 **請勿使用預設子網路，因為它不會設定用來裝載受控執行個體**。 |

   ![受控執行個體建立表單](./media/sql-database-managed-instance-get-started/managed-instance-create-form.png)

6. 按一下 [定價層] 來調整計算和儲存體資源的大小，以及檢閱定價層選項。 根據預設，您的執行個體會取得 32 GB 的免費儲存空間，**這對您的應用程式而言可能不夠**。
7. 使用滑桿或文字方塊來指定儲存體數量和虛擬核心數目。 
   ![受控執行個體定價層](./media/sql-database-managed-instance-get-started/managed-instance-pricing-tier.png)

8. 完成時，按一下 [套用] 以儲存您的選取項目。  
9. 按一下 [建立] 以部署受控執行個體。
10. 按一下 [通知] 圖示以檢視部署的狀態。
11. 按一下 [部署正在進行中] 來開啟受控執行個體視窗，以進一步監視部署進度。

進行部署時，請繼續下一個程序。

> [!IMPORTANT]
> 如果是子網路中的第一個執行個體，部署時間通常遠超過後續執行個體的部署時間。 請勿取消部署作業，因為它持續的時間超過您的預期。 在子網路中建立第二個受控執行個體將需要幾分鐘的時間。

## <a name="prepare-client-machine"></a>準備用戶端機器

由於 SQL 受控執行個體會放置在您的私人虛擬網路中，因此您必須使用已安裝的 SQL 用戶端工具 (例如 SQL Server Management Studio 或 SQL Operations Studio) 建立 Azure VM，以連線至受控執行個體並執行查詢。

> [!Note]
> 您也可以不要使用用戶端 Azure 虛擬機器，而改為設定[點對站](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)連線，並從您的本機電腦連線至受控執行個體。

要使用所有的必要工具建立用戶端虛擬機器，最簡單的方式是使用 Azure Resource Manager 範本。

1. 按一下以下按鈕 (請確定您會在另一個瀏覽器索引標籤中登入 Azure 入口網站)：

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjovanpop-msft%2Fazure-quickstart-templates%2Fsql-win-vm-w-tools%2F201-vm-win-vnet-sql-tools%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

2. 在開啟的表單中，輸入您將用來連線至 VM 的虛擬機器名稱、系統管理員的使用者名稱和密碼。

    ![建立用戶端 VM](./media/sql-database-managed-instance-get-started/create-client-sql-vm.png)

    如果您未變更 VNet 名稱和預設子網路，則無須變更最後兩個參數；否則，您應將這些值變更為您在設定網路環境時所輸入的值。

3. 按一下 [購買] 按鈕，Azure VM 即會部署在您備妥的網路中。

4. 使用遠端桌面連線連接到您的 VM，並尋找自動安裝在 VM 上的 SQL Server Management Studio 或 SQL Operation Studio。

5. 開啟 SSMS，並在 [伺服器名稱] 方塊中輸入您受控執行個體的 [主機名稱]，選取 [SQL Server 驗證]，在 [連線到伺服器] 對話方塊中提供您的登入和密碼，然後按一下 [連線]。

    ![SSMS 連線](./media/sql-database-managed-instance-tutorial/ssms-connect.png)  

連線之後，您可以檢視 [資料庫] 節點中的系統和使用者資料庫，以及 [安全性]、[伺服器物件]、[複寫]、[管理]、[SQL Server 代理程式] 和 [XEvent Profiler] 節點中的各種物件。

## <a name="next-steps"></a>後續步驟

 - [將您的應用程式連線至受控執行個體](sql-database-managed-instance-connect-app.md)。
 - [將資料庫從內部部署移轉至受控執行個體](sql-database-managed-instance-migrate.md)。


