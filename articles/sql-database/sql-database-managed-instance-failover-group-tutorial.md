---
title: 教學課程：將 SQL Database 受控實例新增至容錯移轉群組
description: 瞭解如何為您的 Azure SQL Database 受控實例設定容錯移轉群組。
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sashan, carlrab
manager: jroth
ms.date: 06/27/2019
ms.openlocfilehash: 04802b8b25ca21cc0099874e5a9ea69748868f6e
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/18/2019
ms.locfileid: "71103202"
---
# <a name="tutorial-add-a-sql-database-managed-instance-to-a-failover-group"></a>教學課程：將 SQL Database 受控實例新增至容錯移轉群組

將 SQL Database 受控實例新增至容錯移轉群組。 在本文中，您將了解如何：

> [!div class="checklist"]
> - 建立主要受控實例
> - 建立次要受控實例作為[容錯移轉群組](sql-database-auto-failover-group.md)的一部分。 
> - 測試容錯移轉

  > [!NOTE]
  > - 進行本教學課程時，請確定您是使用[為受控實例設定容錯移轉群組的必要條件來設定](sql-database-auto-failover-group.md#enabling-geo-replication-between-managed-instances-and-their-vnets)您的資源。 
  > - 建立受控實例可能需要很長的時間。 因此，本教學課程可能需要數小時才能完成。 如需布建時間的詳細資訊，請參閱[受控實例管理作業](sql-database-managed-instance.md#managed-instance-management-operations)。 


## <a name="prerequisites"></a>必要條件

若要完成本教學課程，請確定您具有下列項目︰ 

- 如果您還沒有 Azure 訂用帳戶，請[建立一個免費帳戶](https://azure.microsoft.com/free/)。 


## <a name="1---create-resource-group-and-primary-managed-instance"></a>1-建立資源群組和主要受控實例
在此步驟中，您將使用 Azure 入口網站，為您的容錯移轉群組建立資源群組和主要受控實例。 

1. 在 Azure 入口網站的左側功能表中，選取 [Azure SQL]。 如果**AZURE SQL**不在清單中，請選取 [**所有服務**]，然後在搜尋方塊中輸入 azure sql。 (選用) 選取 **Azure SQL** 旁的星號將其設為最愛，並新增為左側導覽中的項目。 
1. 選取 [+ 新增] 以開啟 [選取 SQL 部署選項] 頁面。 您可以選取 [資料庫] 磚上的 [顯示詳細資料]，以查看不同資料庫的其他資訊。
1. 選取 [ **SQL 受控實例**] 圖格上的 [**建立**]。 

    ![選取受控實例](media/sql-database-managed-instance-failover-group-tutorial/select-managed-instance.png)

1. 在 [**建立 Azure SQL Database 受控執行個體**] 頁面上的 [**基本**] 索引標籤
    1. 在 [**專案詳細資料**] 下，從下拉式選單選取您的**訂**用帳戶，然後選擇 [**建立新**的資源群組]。 輸入資源群組的名稱，例如`myResourceGroup`。 
    1. 在 [**受控執行個體詳細資料**] 下，提供受控實例的名稱，以及您想要部署受控實例的區域。 將 [**計算 + 儲存體**] 保留為 [預設值]。 
    1. 在 [**系統管理員帳戶**] 下，提供管理員登`azureuser`入（例如），以及複雜的管理員密碼。 

    ![建立主要 MI](media/sql-database-managed-instance-failover-group-tutorial/primary-sql-mi-values.png)

1. 將其餘設定保留為預設值，然後選取 [**審查 + 建立**] 來檢查您的受控實例設定。 
1. 選取 [**建立**] 以建立您的主要受控實例。 


## <a name="2---create-a-virtual-network"></a>2-建立虛擬網路
在此步驟中，您將為次要受控實例建立虛擬網路。 這是必要步驟，因為主要和次要受控實例的子網必須有非重迭的位址範圍。 

若要確認主要虛擬網路的子網範圍，請遵循下列步驟：
1. 在  [Azure 入口網站](https://portal.azure.com)中，流覽至您的資源群組，然後選取主要實例的虛擬網路。 
1. 選取 [**設定**] 底下的 [**子網**]，並記下**位址範圍**。 次要受控實例之虛擬網路的子網位址範圍不能重迭。 


   ![主要子網路](media/sql-database-managed-instance-failover-group-tutorial/verify-primary-subnet-range.png)

若要建立虛擬網路，請遵循下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com)中，選取 [**建立資源**] 並搜尋 [*虛擬網路*]。 
1. 選取 Microsoft 所發行的**虛擬網路**選項，然後在下一頁選取 [**建立**]。 
1. 填寫必要欄位，為您的次要受控實例設定虛擬網路，然後選取 [**建立**]。 

   下表顯示次要虛擬網路所需的值：

    | **欄位** | 值 |
    | --- | --- |
    | **名稱** |  次要受控實例所要使用之虛擬網路的名稱，例如`vnet-sql-mi-secondary`。 |
    | **位址空間** | 虛擬網路的位址空間，例如`10.128.0.0/16`。 | 
    | **訂用帳戶** | 主要受控實例和資源群組所在的訂用帳戶。 |
    | **區域** | 您將部署次要受控實例的位置。 |
    | **子網路** | 子網的名稱。 `default`預設會為您提供。 |
    | **位址範圍**| 子網的位址範圍。 這必須與主要受控實例的虛擬網路所使用的子網位址範圍不同，例如`10.128.0.0/24`。  |
    | &nbsp; | &nbsp; |

    ![次要虛擬網路值](media/sql-database-managed-instance-failover-group-tutorial/secondary-virtual-network.png)


## <a name="3---create-a-secondary-managed-instance"></a>3-建立次要受控實例
在此步驟中，您將在 Azure 入口網站中建立次要受控實例，這也會設定兩個受控實例之間的網路功能。 

您的第二個受控實例必須：
- 是空的。 
- 具有與主要受控實例不同的子網和 IP 範圍。 

若要建立次要受控實例，請遵循下列步驟： 

1. 在 Azure 入口網站的左側功能表中，選取 [Azure SQL]。 如果**AZURE SQL**不在清單中，請選取 [**所有服務**]，然後在搜尋方塊中輸入 azure sql。 (選用) 選取 **Azure SQL** 旁的星號將其設為最愛，並新增為左側導覽中的項目。 
1. 選取 [+ 新增] 以開啟 [選取 SQL 部署選項] 頁面。 您可以選取 [資料庫] 磚上的 [顯示詳細資料]，以查看不同資料庫的其他資訊。
1. 選取 [ **SQL 受控實例**] 圖格上的 [**建立**]。 

    ![選取受控實例](media/sql-database-managed-instance-failover-group-tutorial/select-managed-instance.png)

1. 在 [**建立 Azure SQL Database 受控執行個體**] 頁面的 [**基本**] 索引標籤上，填寫必要的欄位以設定次要受控實例。 

   下表顯示次要受控實例所需的值：
 
    | **欄位** | 值 |
    | --- | --- |
    | **訂用帳戶** |  您的主要受控實例所在的訂用帳戶。 |
    | **資源群組**| 您的主要受控實例所在的資源群組。 |
    | **受控執行個體名稱** | 新次要受控實例的名稱，例如`sql-mi-secondary`  | 
    | **區域**| 次要受控實例的位置。  |
    | **受控執行個體系統管理員登入** | 您想要用於新的次要受控實例的登入，例如`azureuser`。 |
    | **密碼** | 新的次要受控實例的系統管理員登入所使用的複雜密碼。  |
    | &nbsp; | &nbsp; |

1. 在 **網路**功能 索引標籤下，針對 **虛擬網路**，從下拉式選單選取您為次要受控實例所建立的虛擬網路。

   ![次要 MI 網路](media/sql-database-managed-instance-failover-group-tutorial/networking-settings-for-secondary-mi.png)

1. 在 [**其他設定**] 索引標籤下，針對 [**異地**複寫] 選擇 **[是]** ，_做為容錯移轉次要資料庫使用_。 從下拉式選單中選取 [主要受控實例]。 
    1. 請確定定序和時區符合主要受控實例的範圍。 在本教學課程中建立的主要受控實例會使用`SQL_Latin1_General_CP1_CI_AS`定序`(UTC) Coordinated Universal Time`和時區的預設值。 

   ![次要 MI 網路](media/sql-database-managed-instance-failover-group-tutorial/secondary-mi-failover.png)

1. 選取 [審核] [ **+ 建立**] 來檢查次要受控實例的設定。 
1. 選取 [**建立**] 以建立次要受控實例。 


## <a name="4---create-primary-virtual-network-gateway"></a>4-建立主要虛擬網路閘道 

若要讓兩個受控實例參與容錯移轉群組，必須在兩個受控實例的虛擬網路之間設定閘道，以允許網路通訊。 您可以使用 Azure 入口網站建立主要受控實例的閘道：

1. 在  [Azure 入口網站](https://portal.azure.com)中，移至您的資源群組，然後選取主要受控實例的**虛擬網路**資源。 
1. 選取 [**設定**] 底下的 [**子網**]，然後選取以新增新的**閘道子網**。 保留預設值。 

   ![為主要受控實例新增閘道](media/sql-database-managed-instance-failover-group-tutorial/add-subnet-gateway-primary-vnet.png)

1. 建立子網閘道之後，從左側流覽窗格中選取 [**建立資源**]，然後`Virtual network gateway`在 [搜尋] 方塊中輸入。 選取**Microsoft**發佈的**虛擬網路閘道**資源。 

   ![建立新的虛擬網路閘道](media/sql-database-managed-instance-failover-group-tutorial/create-virtual-network-gateway.png)

1. 填寫必要欄位，以設定您的主要受控實例閘道。 

   下表顯示主要受控實例的閘道所需的值：
 
    | **欄位** | 值 |
    | --- | --- |
    | **訂用帳戶** |  您的主要受控實例所在的訂用帳戶。 |
    | **名稱** | 虛擬網路閘道的名稱，例如`primary-mi-gateway`。 | 
    | **區域** | 次要受控實例所在的區域。 |
    | **閘道類型** | 選取 [VPN]。 |
    | **VPN 類型** | 選取以**路由為基礎的** |
    | **SKU**| 保留預設值`VpnGw1`。 |
    | **位置**| 您的主要受控實例和主要虛擬網路所在的位置。   |
    | **虛擬網路**| 選取在第2節中建立的虛擬網路，例如`vnet-sql-mi-primary`。 |
    | **公用 IP 位址**| 選取 [建立新的]。 |
    | **公用 IP 位址名稱**| 輸入 IP 位址的名稱，例如`primary-gateway-IP`。 |
    | &nbsp; | &nbsp; |
1. 將其他值保留為 [預設]，然後選取 [**檢查 + 建立**] 來檢查虛擬網路閘道的設定。

   ![主要閘道設定](media/sql-database-managed-instance-failover-group-tutorial/settings-for-primary-gateway.png)

1. 選取 [**建立**] 以建立新的虛擬網路閘道。 

## <a name="5---configure-secondary-virtual-network-gateway"></a>5-設定次要虛擬網路閘道 

重複上一節中的步驟，以建立次要受控實例的虛擬網路子網和閘道。 填寫必要欄位，為您的次要受控實例設定閘道。 

   下表顯示次要受控實例的閘道所需的值：

   | **欄位** | 值 |
   | --- | --- |
   | **訂用帳戶** |  次要受控實例所在的訂用帳戶。 |
   | **名稱** | 虛擬網路閘道的名稱，例如`secondary-mi-gateway`。 | 
   | **區域** | 次要受控實例所在的區域。 |
   | **閘道類型** | 選取 [VPN]。 |
   | **VPN 類型** | 選取以**路由為基礎的** |
   | **SKU**| 保留預設值`VpnGw1`。 |
   | **位置**| 次要受控實例和次要虛擬網路所在的位置。   |
   | **虛擬網路**| 選取在第2節中建立的虛擬網路，例如`vnet-sql-mi-secondary`。 |
   | **公用 IP 位址**| 選取 [建立新的]。 |
   | **公用 IP 位址名稱**| 輸入 IP 位址的名稱，例如`secondary-gateway-IP`。 |
   | &nbsp; | &nbsp; |

   ![次要閘道設定](media/sql-database-managed-instance-failover-group-tutorial/settings-for-secondary-gateway.png)

## <a name="6---connect-the-gateways"></a>6-連接閘道
在此步驟中，請建立閘道之間的連線。 必須從主要閘道建立連線，然後必須在次要與主要閘道之間建立不同的連接。 設定兩個閘道之間的連線時，請務必使用相同的**共用金鑰**。 

若要設定連線能力，請遵循下列步驟：

1. 在  [Azure 入口網站](https://portal.azure.com)中流覽至您的資源群組，然後選取您在步驟4中建立的主要閘道。 
1. 選取 [**設定**] 底下的 [連線]，**然後選取 [** **新增**] 以建立新的連接。 

   ![新增連至主要閘道的連線](media/sql-database-managed-instance-failover-group-tutorial/add-primary-gateway-connection.png)

1. 輸入連接的名稱（例如`Primary-connection`），然後輸入**共用** `mi1mi2psk`金鑰的值，例如。 
1. 選取**第二個虛擬網路閘道**，然後選取次要受控實例的閘道，例如`secondary-mi-gateway`。 

   ![建立主要到次要連線](media/sql-database-managed-instance-failover-group-tutorial/create-primary-to-secondary-connection.png)

1. 選取 **[確定]** 以新增新的主要對次要閘道連線。
1. 重複這些步驟，以建立從次要受控實例的閘道到主要受控實例閘道的連線。 

   ![建立次要到主要連線](media/sql-database-managed-instance-failover-group-tutorial/create-secondary-to-primary-connection.png)


## <a name="7---create-a-failover-group"></a>7-建立容錯移轉群組
在此步驟中，您將建立容錯移轉群組，並將這兩個受控實例新增至其中。 

1. 在 [Azure 入口網站](https://portal.azure.com)的左側功能表中，選取 [Azure SQL]。 如果**AZURE SQL**不在清單中，請選取 [**所有服務**]，然後在搜尋方塊中輸入 azure sql。 (選用) 選取 **Azure SQL** 旁的星號將其設為最愛，並新增為左側導覽中的項目。 
1. 選取您在第一節中建立的主要受控實例，例如`sql-mi-primary`。 
1. 在 [**設定**] 下，流覽至 [**實例容錯移轉群組**]，然後選擇 [**新增群組**] 以開啟 [**實例容錯移轉群組**] 頁面。 

   ![新增容錯移轉群組](media/sql-database-managed-instance-failover-group-tutorial/add-failover-group.png)

1. 在 [**實例容錯移轉群組**] 頁面上，輸入容錯移轉群組的名稱（ `failovergrouptutorial`例如），然後選擇次要受控`sql-mi-secondary`實例，例如從下拉式選單。 選取 [**建立**] 以建立您的容錯移轉群組。 

   ![建立容錯移轉群組](media/sql-database-managed-instance-failover-group-tutorial/create-failover-group.png)

1. 容錯移轉群組部署完成後，您會回到 [**容錯移轉群組**] 頁面。 

## <a name="8---test-failover"></a>8-測試容錯移轉
在此步驟中，您會將容錯移轉群組容錯移轉到次要伺服器，然後使用 Azure 入口網站進行容錯回復。 

1. 流覽至[Azure 入口網站](https://portal.azure.com)內您的受控實例，然後選取 [設定] 底下的 [**實例容錯移轉群組**]。 
1. 檢查哪個受控實例是主要複本，以及哪個受控實例是次要複本。 
1. 選取 [**容錯移轉**]，然後在關於 TDS 會話中斷連線的警告上選取 **[是]** 。 

   ![容錯移轉群組](media/sql-database-managed-instance-failover-group-tutorial/failover-mi-failover-group.png)

1. 檢查哪一個受控實例是主要複本，哪個實例是次要複本。 如果故障轉換成功，這兩個實例應該已切換角色。 

   ![受控實例在容錯移轉後已切換角色](media/sql-database-managed-instance-failover-group-tutorial/mi-switched-after-failover.png)

1. 再次選取 [**容錯移轉**]，使主要實例無法回到主要角色。 


## <a name="clean-up-resources"></a>清除資源
藉由先刪除受控實例、虛擬叢集、任何剩餘的資源，以及最後的資源群組來清除資源。 

1. 在[Azure 入口網站](https://portal.azure.com)中，流覽至您的資源群組。 
1. 選取 [受控實例]，然後選取 [**刪除**]。 在`yes`文字方塊中輸入，確認您想要刪除資源，然後選取 [**刪除**]。 此程式可能需要一些時間才能在背景中完成，而且在完成之前，您將無法刪除*虛擬叢集*或任何其他相依資源。 監視 [活動] 索引標籤中的 [刪除]，確認已刪除您的受控實例。 
1. 刪除受控實例之後，請在您的資源群組中選取*虛擬*叢集，然後選擇 [**刪除**]，將它刪除。 在`yes`文字方塊中輸入，確認您想要刪除資源，然後選取 [**刪除**]。 
1. 刪除任何剩餘的資源。 在`yes`文字方塊中輸入，確認您想要刪除資源，然後選取 [**刪除**]。 
1. 若要刪除資源群組，請選取 [**刪除資源群組**]，輸入資源群組的名稱`myResourceGroup`，然後選取 [**刪除**]。 

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已設定兩個受控實例之間的容錯移轉群組。 您已了解如何︰

> [!div class="checklist"]
> - 建立主要受控實例
> - 建立次要受控實例作為[容錯移轉群組](sql-database-auto-failover-group.md)的一部分。 
> - 測試容錯移轉

前往下一個快速入門，以瞭解如何連線到您的受控實例，以及如何將資料庫還原到您的受控實例： 

> [!div class="nextstepaction"]
> [連線到您的受控實例](sql-database-managed-instance-configure-vm.md)
> 將[資料庫還原到受控實例](sql-database-managed-instance-get-started-restore.md)


