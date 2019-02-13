---
title: 使用 Azure 快速入門範本在 SQL Server VM 上建立 WSFC 和接聽程式，並設定 Always On 可用性群組的 ILB
description: 使用 Azure 快速入門範本，可讓您以更簡單的程序使用範本建立叢集、將 SQL VM 加入該叢集、建立接聽程式並設定 ILB，而為 Azure 中的 SQL Server VM 建立可用性群組。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/04/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 9db6736813b6d99efad687581f19d23023e1593a
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55814532"
---
# <a name="create-wsfc-listener-and-configure-ilb-for-an-always-on-availability-group-on-a-sql-server-vm-with-azure-quickstart-template"></a>使用 Azure 快速入門範本在 SQL Server VM 上建立 WSFC 和接聽程式，並設定 Always On 可用性群組的 ILB
本文說明如何使用 Azure 快速入門範本，以半自動化的方式為 Azure 中的 SQL Server 虛擬機器部署 Always On 可用性群組設定。 此程序中會使用兩個 Azure 快速入門範本。 

   | 範本 | 說明 |
   | --- | --- |
   | [101-sql-vm-ag-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) | 建立 Windows 容錯移轉叢集，並將 SQL Server VM 加入該叢集。 |
   | [101-sql-vm-aglistener-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) | 建立可用性群組接聽程式，並設定內部負載平衡器。 只有在使用 **101-sql-vm-ag-setup** 範本建立 Windows 容錯移轉叢集的情況下，才能使用此範本。 |
   | &nbsp; | &nbsp; |

可用性群組設定的其他部分必須手動完成，例如建立可用性群組，以及建立內部負載平衡器。 本文提供自動和手動步驟的順序。
 

## <a name="prerequisites"></a>必要條件 
若要使用快速入門範本自動設定 Always On 可用性群組，您必須已具備下列必要項目： 
- [Azure 訂用帳戶](https://azure.microsoft.com/free/)。
- 具有網域控制站的資源群組。 
- [Azure 中一或多個執行 SQL Server 2016 (或更新版本) Enterprise Edition 的 VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision)，且這些 VM 必須已加入網域，並且位於已[向 SQL VM 資源提供者註冊](virtual-machines-windows-sql-ahb.md#register-existing-sql-server-vm-with-sql-resource-provider)的相同可用性設定組或可用性區域中。  


## <a name="step-1---create-the-wsfc-and-join-sql-server-vms-to-the-cluster-using-quickstart-template"></a>步驟 1 - 使用快速入門範本建立 WSFC 並將 SQL Server VM 加入叢集 
在您的 SQL Server VM 向新的 SQL VM 資源提供者註冊之後，您即可將 SQL Server VM 加入 SqlVirtualMachineGroup 中。 這項資源會定義 Windows 容錯移轉叢集的中繼資料，包括版本、版次、完整網域名稱，用來管理叢集和 SQL 服務的 AD 帳戶，以及做為雲端見證的儲存體帳戶。 將 SQL Server VM 新增至 SqlVirtualMachineGroup 資源群組，會啟動 Windows 容錯移轉叢集服務建立叢集，然後將 SQL Server VM 加入該叢集。 此步驟可透過 **101-sql-vm-ag-setup** 快速入門範本自動化執行，並且可依照下列步驟實作：

1. 瀏覽至 [**101-sql-vm-ag-setup**](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) 快速入門範本，然後選取 [部署至 Azure]，在 Azure 入口網站中啟動快速入門範本。
1. 填寫必要欄位，以設定 Windows 容錯移轉叢集的中繼資料。 選擇性欄位可保留為空白。

    下表顯示範本所需的值： 

   | **欄位** | 值 |
   | --- | --- |
   | **訂用帳戶** |  您的 SQL Server VM 所在的訂用帳戶。 |
   |**資源群組** | 您的 SQL Server VM 所屬的資源群組。 | 
   |**容錯移轉叢集名稱** | 新的 Windows 容錯移轉叢集的適當名稱。 |
   | **現有 VM 清單** | 要加入可用性群組、進而納入這個新叢集中的 SQL Server VM。 請以逗號和空格分隔這些值 (例如：SQLVM1, SQLVM2)。 |
   | **SQL Server 版本** | 從下拉式清單中選取 SQL Server VM 的 SQL Server 版本。 目前僅支援 SQL 2016 和 SQL 2017 映像。 |
   | **現有完整網域名稱** | SQL Server VM 所在網域的現有 FQDN。 |
   | **現有網域帳戶** | 在範本部署期間建立 [CNO](/windows-server/failover-clustering/prestage-cluster-adds) 時，在網域中擁有「建立電腦物件」權限的現有網域使用者帳戶。 例如，網域系統管理員帳戶通常會有足夠的權限 (例如：account@domain.com)。 *此帳戶也應該屬於建立叢集的每個 VM 上的本機系統管理員群組一部分。*| 
   | **網域帳戶密碼** | 前述網域使用者帳戶的密碼。 | 
   | **現有 SQL 服務帳戶** | 可用性群組部署期間控制 [SQL Server 服務](/sql/database-engine/configure-windows/configure-windows-service-accounts-and-permissions)的網域使用者帳戶 (例如：account@domain.com)。 |
   | **SQL 服務密碼** | 控制 SQL Server 服務的網域使用者帳戶所使用的密碼。 |
   | **雲端見證名稱** | 這是新的 Azure 儲存體帳戶，建立後將用於雲端見證。 無法修改這個名稱。 |
   | **\_成品位置** | 此欄位為預設設定，不應該修改。 |
   | **\_成品位置 SAS 權杖** | 此欄位已刻意留下空白。 |
   | &nbsp; | &nbsp; |

1. 如果您同意條款及條件，請選取 [我同意上方所述的條款及條件] 旁的核取方塊，然後選取 [購買] 以完成快速入門範本部署。 
1. 若要監視您的部署，請從頂端瀏覽橫幅中的 [通知] 鈴鐺圖示選取部署，或瀏覽至您在 Azure 入口網站中的 [資源群組]，並選取 [設定] 欄位中的 [部署]，然後選擇 'Microsoft.Template' 部署。 

  >[!NOTE]
  > 範本部署期間提供的認證僅於部署期間儲存。 部署完成之後，這些密碼會予以移除，如果需新增更多 SQL Server VM 到叢集，則會要求您再次提供這些密碼。 


## <a name="step-2---manually-create-the-availability-group"></a>步驟 2 - 手動建立可用性群組 
像平常一樣，使用 [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell?view=sql-server-2017)、[SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio?view=sql-server-2017) 或 [Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql?view=sql-server-2017) 手動建立可用性群組。 

  >[!IMPORTANT]
  > 此時**請勿**建立接聽程式，因為 **101-sql-vm-aglistener-setup** 快速入門範本會在步驟 4 中自動加以建立。 

## <a name="step-3---manually-create-the-internal-load-balancer-ilb"></a>步驟 3 - 手動建立內部負載平衡器 (ILB)
Always On 可用性群組 (AG) 接聽程式需要內部 Azure 負載平衡器 (ILB)。 ILB 會為 AG 接聽程式提供「浮動」IP 位址，以加快容錯移轉和重新連線的速度。 如果可用性群組中的 SQL Server VM 屬於相同的可用性設定組，則您可以使用基本的負載平衡器；否則就必須使用標準負載平衡器。  **ILB 應位於與 SQL Server VM 執行個體相同的 vNet 中。** 您只需建立 ILB 即可，其餘設定 (例如後端集區、健康情況探查和負載平衡規則) 都可由 **101-sql-vm-aglistener-setup** 快速入門範本在步驟 4 中執行。 

1. 在 Azure 入口網站中，開啟包含 SQL Server 虛擬機器的資源群組。 
2. 在資源群組中，按一下 [新增] 。
3. 搜尋**負載平衡器**，然後在搜尋結果中，選取由 **Microsoft** 發佈的 [負載平衡器]。
4. 在 [負載平衡器] 刀鋒視窗上，按一下 [建立]。
5. 在 [建立負載平衡器] 對話方塊中，依下列方式設定負載平衡器︰

   | 設定 | 值 |
   | --- | --- |
   | **名稱** |代表負載平衡器的文字名稱。 例如 **sqlLB**。 |
   | **類型** |**內部**：大部分的實作都會使用內部負載平衡器，這可讓相同虛擬網路內的應用程式連線到可用性群組。  </br> **外部**：可讓應用程式透過公用網際網路連線來連線到可用性群組。 |
   | **虛擬網路** | 選取 SQL Server 執行個體所在的虛擬網路。 |
   | **子網路** | 選取 SQL Server 執行個體所在的子網路。 |
   | **IP 位址指派** |**靜態** |
   | **私人 IP 位址** | 從子網路指定可用的 IP 位址。 |
   | **訂用帳戶** |如果您有多個訂用帳戶，此欄位才會出現。 選取您想要與此資源相關聯的訂用帳戶。 通常是與可用性群組的所有資源相同的訂用帳戶。 |
   | **資源群組** |選取 SQL Server 執行個體所在的資源群組。 |
   | **位置** |選取 SQL Server 執行個體所在的 Azure 位置。 |
   | &nbsp; | &nbsp; |

6. 選取 [建立] 。 


  >[!NOTE]
  > 每個 SQL Server VM 的公用 IP 資源都應有標準 SKU，以便與標準負載平衡器相容。 若要確認 VM 公用 IP 資源的 SKU，請瀏覽至您的 [資源群組]，並為您所需的 SQL Server VM 選取 [公用 IP 位址] 資源，然後在 [概觀]窗格的 [SKU] 下方找出其值。 

## <a name="step-4---create-the-ag-listener-and-configure-the-ilb-with-the-quickstart-template"></a>步驟 4 - 使用快速入門範本建立 AG 接聽程式並設定 ILB

請使用 **101-sql-vm-aglistener-setup** 快速入門範本自動建立可用性群組接聽程式，並設定內部負載平衡器 (ILB)，因為此範本會佈建 Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/AvailabilityGroupListener 資源。 **101-sql-vm-aglistener-setup** 快速入門範本會透過 SQL VM 資源提供者執行下列動作：

 - 為接聽程式建立新的前端 IP 資源 (根據在部署期間提供的 IP 位址值)。 
 - 設定叢集和 ILB 的網路設定。 
 - 設定 ILB 後端集區、健康情況探查和負載平衡規則。
 - 使用指定的 IP 位址和名稱建立 AG 接聽程式。
 
   >[!NOTE]
   > 只有在使用 **101-sql-vm-ag-setup** 範本建立 Windows 容錯移轉叢集的情況下，才能使用 **101-sql-vm-aglistener-setup**。
   
   
若要設定 ILB 並建立 AG 接聽程式，請執行下列作業：
1. 瀏覽至 [**101-sql-vm-aglistener-setup**](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) 快速入門範本，然後選取 [部署至 Azure]，在 Azure 入口網站中啟動快速入門範本。
1. 填寫必要欄位以設定 ILB，並建立可用性群組接聽程式。 選擇性欄位可保留為空白。 

    下表顯示範本所需的值： 

   | **欄位** | 值 |
   | --- | --- |
   |**資源群組** | 您的 SQL Server VM 和可用性群組所屬的資源群組。 | 
   |**現有容錯移轉叢集名稱** | 您的 SQL Server VM 加入的叢集名稱。 |
   | **現有 SQL 可用性群組**| SQL Server VM 所屬可用性群組的名稱。 |
   | **現有 VM 清單** | 屬於前述可用性群組的 SQL Server VM 名稱。 名稱應以逗號和空格分隔 (例如：SQLVM1, SQLVM2)。 |
   | **接聽程式** | 您要指派給接聽程式的 DNS 名稱。 此範本依預設指定的名稱為 'aglistener'，但您可加以變更。 名稱長度不應該超過 15 個字元。 |
   | **接聽程式連接埠** | 您想要讓接聽程式使用的連接埠。 一般而言，此連接埠應為預設連接埠 1433，若是如此，這就是此範本所指定的連接埠號碼。 不過，如果您的預設連接埠已變更，接聽程式連接埠就應改用該值。 | 
   | **接聽程式 IP** | 您想要接聽程式使用的 IP。  此 IP 位址會在範本部署期間建立，因此請提供未使用的 IP 位址。  |
   | **現有子網路** | SQL Server VM 的內部子網路名稱 (例如：default)。 您可以瀏覽至您的 [資源群組]，並依序選取 [vNet] 和 [設定] 窗格下方的 [子網路]，後複製 [名稱] 下方的值，以確認此值。 |
   | **現有內部負載平衡器** | 您在步驟 3 中建立的 ILB 名稱。 |
   | **探查連接埠** | 您想要讓 ILB 使用的探查連接埠。 範本依預設會使用 59999，但您可以變更此值。 |
   | &nbsp; | &nbsp; |

1. 如果您同意條款及條件，請選取 [我同意上方所述的條款及條件] 旁的核取方塊，然後選取 [購買] 以完成快速入門範本部署。 
1. 若要監視您的部署，請從頂端瀏覽橫幅中的 [通知] 鈴鐺圖示選取部署，或瀏覽至您在 Azure 入口網站中的 [資源群組]，並選取 [設定] 欄位中的 [部署]，然後選擇 'Microsoft.Template' 部署。 

  >[!NOTE]
  >如果您的部署中途失敗，您將必須使用 PowerShell 以手動方式[移除新建立的接聽程式](#remove-availability-group-listener)，再重新部署 **101-sql-vm-aglistener-setup** 快速入門範本。 

## <a name="remove-availability-group-listener"></a>移除可用性群組接聽程式
如果您稍後需要移除範本所設定的可用性群組接聽程式，您必須透過 SQL VM 資源提供者來執行。 由於接聽程式是透過 SQL VM 資源提供者註冊的，因為僅透過 SQL Server Management Studio 加以刪除，是不夠的。 實際上應使用 PowerShell 透過 SQL VM 資源提供者加以刪除。 這麼做可從 SQL VM 資源提供者中移除 AG 接聽程式中繼資料，而真正將接聽程式從可用性群組中刪除。 

下列程式碼片段會同時從 SQL 資源提供者和您的可用性群組中刪除 SQL 可用性群組接聽程式： 

```PowerShell
# Remove the AG listener
# example: Remove-AzureRmResource -ResourceId '/subscriptions/a1a11a11-1a1a-aa11-aa11-1aa1a11aa11a/resourceGroups/SQLAG-RG/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/Cluster/availabilitygrouplisteners/aglistener' -Force
Remove-AzureRmResource -ResourceId '/subscriptions/<SubscriptionID>/resourceGroups/<resource-group-name>/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/<cluster-name>/availabilitygrouplisteners/<listener-name>' -Force
```
 
## <a name="common-errors"></a>常見錯誤
本節將討論一些已知問題和可能的解決方法。 

### <a name="availability-group-listener-for-availability-group-ag-name-already-exists"></a>可用性群組 '\<AG-Name>' 已有可用性群組接聽程式
經選取在 AG 接聽程式的 Azure 快速入門範本中使用的可用性群組已包含接聽程式；可能是實際包含在可用性群組內，或是作為 SQL VM 資源提供者內的中繼資料。  請先使用 [PowerShell](#remove-availability-group-listener) 移除接聽程式，再重新部署 **101-sql-vm-aglistener-setup** 快速入門範本。 

### <a name="connection-only-works-from-primary-replica"></a>只能從主要複本進行連線
之所以出現此行為，可能是因為失敗的 **101-sql-vm-aglistener-setup** 範本部署導致 ILB 設定處於不一致的狀態。 請確認後端集區有列出可用性設定組，且健康情況探查和負載平衡都有其規則。 若遺漏了任何項目，表示 ILB 設定處於不一致的狀態。 

若要消除此行為，請使用 [PowerShell](#remove-availability-group-listener) 移除接聽程式、透過 Azure 入口網站刪除內部負載平衡器，然後重新執行[步驟 3](#step-3---manually-create-the-internal-load-balanced-ilb)。 

### <a name="badrequest---only-sql-virtual-machine-list-can-be-updated"></a>BadRequest - 只能更新 SQL 虛擬機器清單
在部署 **101-sql-vm-aglistener-setup** 範本時，如果已透過 SQL Server Management Studio (SSMS) 刪除接聽程式，但並未將其從 SQL VM 資源提供者刪除，就可能發生此錯誤。 透過 SSMS 刪除接聽程式後，並不會從 SQL VM 資源提供者中移除接聽程式的中繼資料；您必須使用 [PowerShell](#remove-availability-group-listener) 從資源提供者中刪除接聽程式。 

### <a name="domain-account-does-not-exist"></a>網域帳戶不存在
這個錯誤可能是兩個原因的其中一個所造成。 指定的網域帳戶確實不存在，或者缺少[使用者主體名稱 (UPN)](/windows/desktop/ad/naming-properties#userprincipalname) 資料。 **101-sql-vm-ag-setup** 範本會預期 UPN 格式 (也就是 user@domain.com) 的網域帳戶，但部分網域帳戶可能沒有。 會發生這種情況通常是由於伺服器已升級為網域控制站，或已透過 PowerShell 建立使用者，而本機使用者經過移轉而成為第一個網域系統管理員帳戶。 

 請確認帳戶確實存在。 若是如此，您可能發生第二種情況。 若要解決此問題，請執行下列動作：

 1. 在網域控制站上，從 [伺服器管理員] 的 [工具] 選項中開啟 [Active Directory 使用者和電腦] 視窗。 
 2. 選取左窗格中的 [使用者]，瀏覽至帳戶。
 3. 以滑鼠右鍵按一下所需的帳戶，然後選取 [屬性]。
 4. 選取 [帳號] 索引標籤，然後確認 [使用者登入名稱] 是否空白。 如果是，這會是造成錯誤的原因。 

     ![空白的使用者帳戶表示缺少 UPN](media/virtual-machines-windows-sql-availability-group-quickstart-template/account-missing-upn.png)

 5. 填寫 [使用者登入名稱] 以符合使用者的名稱，並且從下拉式清單中選取正確的網域。 
 6. 選取 [套用] 儲存變更，並選取 [確定] 關閉對話方塊。 

 進行這些變更後，請嘗試再次部署 Azure 快速入門範本。 



## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章： 

* [SQL Server VM 概觀](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server VM 常見問題集](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server VM 定價指引](virtual-machines-windows-sql-server-pricing-guidance.md)
* [SQL Server VM 版本資訊](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [切換 SQL Server VM 的授權模式](virtual-machines-windows-sql-ahb.md)



