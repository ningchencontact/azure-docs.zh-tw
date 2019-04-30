---
author: rockboyfor
ms.service: virtual-machines
ms.topic: include
origin.date: 10/26/2018
ms.date: 11/26/2018
ms.author: v-yeche
ms.openlocfilehash: 394b242ab46da7821f77e8d008836753f4e358e2
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097657"
---
在此步驟中，您會在容錯移轉叢集管理員和 SQL Server Management Studio 中手動建立可用性群組接聽程式。

1. 從裝載主要複本的節點開啟容錯移轉叢集管理員。

2. 選取 [網路] 節點，然後記下叢集網路名稱。 這個名稱會用於 PowerShell 指令碼中的 $ClusterNetworkName 變數。

3. 展開叢集名稱，然後按一下 [角色] 。

4. 在 [角色] 窗格中，以滑鼠右鍵按一下可用性群組名稱，然後選取 [新增資源] > [用戶端存取點]。

    ![新增可用性群組的用戶端存取點](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678769.gif)

5. 在 [名稱] 方塊中，建立這個新接聽程式的名稱，按兩次 [下一步]，然後按一下 [完成]。  
    不要在此时使侦听程序或资源联机。

6. 按一下 [資源] 索引標籤，然後展開您剛才建立的用戶端存取點。 
    叢集中每個叢集網路的 IP 位址資源會顯示出來。 如果這是僅限 Azure 的解決方案，只會顯示一個 IP 位址資源。

7. 執行下列其中一個動作：

   * 若要設定混合式解決方案：

        a. 以滑鼠右鍵按一下對應至您內部部署子網路的 IP 位址資源，然後選取 [屬性]。 記下 IP 位址名稱和網路名稱。

        b. 選取 [靜態 IP 位址]、指派未使用的 IP 位址，然後按一下 [確定]。

   * 若要設定僅限 Azure 的解決方案︰

        a. 以滑鼠右鍵按一下對應至您 Azure 子網路的 IP 位址資源，然後選取 [屬性]。

       > [!NOTE]
       > 如果接聽程式稍後因為 DHCP 所選取的 IP 位址衝突而無法上線，您可以在此屬性視窗中設定有效的靜態 IP 位址。
       > 
       > 

       b. 在同一個 [IP 位址屬性] 視窗中，變更 [IP 位址名稱]。  
        此名稱使用於 PowerShell 指令碼的 $IPResourceName 變數。 如果您的解決方案跨越多個 Azure 虛擬網路，請針對每個 IP 資源重複此步驟。
        
<!-- Update_Description: update meta properties -->