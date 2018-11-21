---
title: 部署適用於 Azure 的 Avere vFXT
description: 在 Azure 中部署 Avere vFXT 叢集的步驟
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: c8bad3642f1e98cac3857d536f539554235e1a51
ms.sourcegitcommit: 0fc99ab4fbc6922064fc27d64161be6072896b21
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2018
ms.locfileid: "51578631"
---
# <a name="deploy-the-vfxt-cluster"></a>部署 vFXT 叢集

在 Azure 中建立 vFXT 叢集的最簡單方式就是使用叢集控制器。 叢集控制器是包含建立和管理 vFXT 叢集所需指令碼、範本及軟體基礎結構的 VM。

部署新的 vFXT 叢集包含下列步驟：

1. [建立叢集控制器](#create-the-cluster-controller-vm)。
1. 如果使用 Azure Blob 儲存體，請在您的虛擬網路中[建立儲存體端點](#create-a-storage-endpoint-if-using-azure-blob)。
1. [連線到叢集控制器](#access-the-controller)。 這些步驟的其餘部分會從叢集控制器 VM 執行。 
1. 為叢集節點[建立存取角色](#create-the-cluster-node-access-role)。 系統會提供原型。
1. 為您想要建立的 vFXT 叢集類型[自訂叢集建立指令碼](#edit-the-deployment-script)。
1. [執行叢集建立指令碼](#run-the-script)。

如需有關叢集部署步驟和規劃的詳細資訊，請參閱[規劃您的 Avere vFXT 系統](avere-vfxt-deploy-plan.md)和[部署概觀](avere-vfxt-deploy-overview.md)。 

依照本文件中的指示進行操作之後，您將會擁有一個虛擬網路、一個子網路、一個控制器，以及一個 vFXT 叢集，如下圖所示：

![顯示 VNet 和一個子網路的圖表，其中 VNet 包含選擇性的 Blob 儲存體，子網路則包含三個標示為 vFXT 節點/vFXT 叢集的已群組 VM 及一個標示為叢集控制器的 VM](media/avere-vfxt-deployment.png)

開始之前，請確定您已滿足下列先決條件：  

1. [新的訂用帳戶](avere-vfxt-prereqs.md#create-a-new-subscription)
1. [訂用帳戶擁有者權限](avere-vfxt-prereqs.md#configure-subscription-owner-permissions)
1. [vFXT 叢集配額](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)

您也可以視需要在建立叢集控制器[之前](avere-vfxt-pre-role.md)先建立叢集節點角色，但在之後再建立此角色會比較簡單。

## <a name="create-the-cluster-controller-vm"></a>建立叢集控制器 VM

第一步是建立將建立並設定 vFXT 叢集節點的虛擬機器。 

叢集控制器是一個已預先安裝 Avere vFXT 叢集建立軟體和指令碼的 Linux VM。 它不需要大量的處理能力或儲存體空間，因此您可以選擇較低成本的選項。 此 VM 會用於 vFXT 叢集的整個存留期。

建立叢集控制器 VM 的方法有兩個。 [下方](#create-controller---arm-template)提供一個可簡化程序的 [Azure Resource Manager 範本](#create-controller---arm-template)，但您也可以從 [Azure Marketplace 映像](#create-controller---azure-marketplace-image)建立控制器。 

您可以在建立控制器的過程中建立新資源群組。

> [!TIP]
>
> 請決定是否要在叢集控制器上使用公用 IP 位址。 公用 IP 位址提供較簡便的 vFXT 存取方式，但會產生些微安全性風險。
>
>  * 叢集控制器上的公用 IP 位址可讓您使用它作為跳躍點主機，以從私人子網路外連線到 Avere vFXT 叢集。
>  * 如果您沒有在控制器上設定公用 IP 位址，就必須使用另一個跳躍點主機、VPN 連線或 ExpressRoute 來存取叢集。 例如，在已設定 VPN 連線的虛擬網路內建立控制器。
>  * 如果您建立具有公用 IP 位址的控制器，您應該使用網路安全性群組來保護該控制器 VM。 請只允許透過連接埠 22 進行存取來提供網際網路存取權。

### <a name="create-controller---resource-manager-template"></a>建立控制器 - Resource Manager 範本

若要從入口網站建立叢集控制器節點，請按一下下方的 [Deploy to Azure] \(部署至 Azure\) 按鈕。 這個部署範本會建立將建立並管理 Avere vFXT 叢集的 VM。

[![用來建立控制器的按鈕](media/deploytoazure.png)](https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAvere%2Fmaster%2Fsrc%2Fvfxt%2Fazuredeploy.json)

請提供下列資訊。

在 [基本] 區段中：  

* 叢集的**訂用帳戶**
* 叢集的**資源群組** 
* **位置** 

在 [設定] 區段中：

* 是否要建立新的虛擬網路

  * 如果您建立新的 VNet，系統將會指派公用 IP 位址給叢集控制器，以便您存取它。 此外，也會為此 VNet 建立一個將輸入流量僅限於連接埠 22 的網路安全性群組。
  * 如果您想要使用 ExpressRoute 或 VPN 來連線到叢集控制器，請將此值設定為 `false`，然後在剩餘的欄位中指定現有的 VNet。 叢集控制器將會使用該 VNet 來進行網路通訊。 

* 虛擬網路資源群組、名稱及子網路名稱 - 輸入現有資源的名稱 (如果要使用現有的 VNet)，或輸入新名稱 (如果要建立新的 VNet)
* **控制器名稱** - 設定控制器 VM 的名稱
* 控制器管理員使用者名稱 - 預設值為 `azureuser`
* SSH 金鑰 - 貼上要與管理員使用者名稱建立關聯的公開金鑰。 如果需要協助，請參閱[如何建立和使用 SSH 金鑰](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)。

在 [條款及條件] 底下： 

* 閱讀服務條款，然後勾選核取方塊以接受條款。 

  > [!NOTE] 
  > 如果您不是訂用帳戶擁有者，請依照[事先接受軟體條款](avere-vfxt-prereqs.md#accept-software-terms-in-advance)中的先決條件步驟，讓擁有者為您接受條款。 


完成時，請按一下 [購買]。 在 5 或 6 分鐘之後，您的控制器節點就會啟動並執行。

瀏覽輸出頁面以收集建立叢集所需的控制器資訊。 讀取[建立叢集所需的資訊](#information-needed-to-create-the-cluster)以深入了解相關資訊。

### <a name="create-controller---azure-marketplace-image"></a>建立控制器 - Azure Marketplace 映像

請在 Azure Marketplace 中搜尋 ``Avere`` 名稱來尋找控制器範本。 選取 [適用於 Azure 的 Avere vFXT 控制器] 範本。

如果您尚未接受條款並為 Marketplace 映像啟用以程式設計方式存取的功能，請接受條款並按一下 [建立] 按鈕底下的 [想要以程式設計方式部署嗎?]。

![以程式設計方式存取的連結 (位於 [建立] 按鈕下方) 螢幕擷取畫面](media/avere-vfxt-deploy-programmatically.png)

按一下 [啟用] 按鈕並儲存設定。

![顯示點選滑鼠來啟用以程式設計方式存取之功能的螢幕擷取畫面](media/avere-vfxt-enable-program.png)

返回 [適用於 Azure 的 Avere vFXT 控制器] 範本，然後按一下 [建立]。 

在第一個面板中，填入或確認下列基本選項：

* **訂用帳戶**
* **資源群組** (如果您想要建立新的群組，請輸入新名稱)。
* **虛擬機器名稱** - 控制器的名稱
* **區域**
* **可用性選項** - 不需要備援
* **映像** - Avere vFXT 控制器節點映像
* **大小** - 保留預設值，或選擇另一個低成本的類型
* **管理員帳戶** - 設定登入叢集控制器的方式： 
  * 選取 [使用者名稱/密碼] 或 [SSH 公開金鑰] (建議使用)。
  
    > [!TIP] 
    > SSH 金鑰較為安全。 如果需要協助，請參閱[如何建立和使用 SSH 金鑰](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)。 
  * 指定使用者名稱 
  * 貼上 SSH 金鑰，或輸入並確認密碼
* **輸入連接埠規則** - 如果使用公用 IP 位址，請開啟連接埠 22 (SSH)

按 [下一步] 以設定磁碟選項：

* **OS 磁碟類型** - 預設值 [HDD] 即已足夠
* **使用非受控磁碟** - 不需要
* **資料磁碟** - 請勿使用

按 [下一步] 以選取網路功能選項：

* **虛擬網路** - 選取控制器的 VNet，或輸入名稱以建立新的 VNet。 如果您不想要在控制器上使用公用 IP 位址，請考慮將它置於已經設定 ExpressRoute 或另一個存取方法的 VNet 內。
* **子網路** - 選取 VNet 內的子網路 (選擇性)。 如果您建立新的 VNet，可以同時建立新的子網路。
* **公用 IP** - 如果您想要使用公用 IP 位址，可以在這裡指定。 
* **網路安全性群組** - 請保留預設值 ([基本]) 
* **公用輸入連接埠** - 如果使用公用 IP 位址，請使用此控制項來允許來自 SSH 流量的存取。 
* 此 VM 無法使用 [加速網路]。

按 [下一步] 以設定管理選項：

* **開機診斷** - 請變更為 [關閉]
* **OS 客體診斷** - 請保持停用
* **診斷儲存體帳戶** - 請視需要選取或指定一個新帳戶來保存診斷資訊。
* **受控服務識別** - 請將此選項變更為 [開啟]，這會為叢集控制器建立一個 Azure AD 服務主體。
* **自動關機** - 請保持關閉 

此時，如果您不想要使用執行個體標記，可以按一下 [檢閱 + 建立]。 否則，請按 [下一步] 兩次，以略過 [客體設定] 頁面並前往標記頁面。 在該處完成時，按一下 [檢閱 + 建立]。 

在驗證完您的選取項目之後，按一下 [建立] 按鈕。  

需要花 5 或 6 分鐘的時間進行建立。

## <a name="create-a-storage-endpoint-if-using-azure-blob"></a>建立儲存體端點 (如果使用 Azure Blob)

如果您使用 Azure Blob 儲存體作為後端資料儲存體，您應該在虛擬網路中建立儲存體服務端點。 此[服務端點](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)會讓 Azure Blob 流量保留在本機，而不會透過網際網路進行路由傳送。

1. 從入口網站中，按一下左側的 [虛擬網路]。
1. 選取您控制器的 VNet。 
1. 按一下左側的 [服務端點]。
1. 按一下頂端的 [新增]。
1. 將服務保留為 ``Microsoft.Storage``，然後選擇控制器的子網路。
1. 在底部按一下 [新增]。

  ![含有建立服務端點之註解的 Azure 入口網站螢幕擷取畫面](media/avere-vfxt-service-endpoint.png)

## <a name="information-needed-to-create-the-cluster"></a>建立叢集所需的資訊

建立叢集控制器之後，請確定您具有後續步驟所需的資訊。 

連線到控制器所需的資訊： 

* 控制器使用者名稱和 SSH 金鑰 (或密碼)
* 控制器 IP 位址或其他可連線到控制器 VM 的方法

叢集所需的資訊： 

* 資源群組名稱
* Azure 位置 
* 虛擬網路名稱
* 子網路名稱
* 叢集節點角色名稱 - 如[下方](#create-the-cluster-node-access-role)所述，當您建立角色時會設定此名稱。
* 儲存體帳戶名稱 (如果要建立 Blob 容器)

如果您已藉由使用 Resource Manager 範本來建立控制器節點，您可以從[範本輸出](#find-template-output)取得資訊。 

如果您使用了 Azure Marketplace 映像來建立控制器，便已直接提供大部分的項目。 

瀏覽至控制器 VM 資訊頁面，以尋找遺漏的項目。 例如，按一下 [所有資源] 並搜尋控制器名稱，然後按一下控制器名稱，即可查看其詳細資料。

### <a name="find-template-output"></a>尋找範本輸出

若要從 Resource Manager 範本輸出尋找此資訊，請依照此程序：

1. 移至您叢集控制器的資源群組。

1. 在左側按一下 [部署]，然後選取 [Microsoft.Template]。

   ![資源群組入口網站頁面，其中左側已選取 [部署]，而 [Microsoft.Template] 則顯示在 [部署名稱] 底下的表格中](media/avere-vfxt-deployment-template.png)

1. 在左側按一下 [輸出]。 複製每個欄位中的值。 

   ![輸出頁面，其中 SSHSTRING、RESOURCE_GROUP、LOCATION、NETWORK、SUBNET 及 SUBNET_ID 顯示在標籤右邊的欄位中](media/avere-vfxt-template-outputs.png)

## <a name="access-the-controller"></a>存取控制器

若要執行剩餘的部署步驟，您必須連線到叢集控制器。

1. 連線到叢集控制器的方法取決於您的設定。

   * 如果控制器具有公用 IP 位址，請以您所設定的管理員使用者名稱身分透過 SSH 連線到控制器的 IP (例如``ssh azureuser@40.117.136.91``)。
   * 如果控制器沒有公用 IP，請使用 VPN 或 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/) 來連線到您的 VNet。

1. 登入您的控制器之後，請執行 `az login`來進行驗證。 複製殼層中所提供的驗證碼，然後使用網頁瀏覽器來載入 [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin)，並向 Microsoft 系統進行驗證。 返回殼層來進行確認。

   ![顯示瀏覽器連結和驗證碼之 'AZ login' 命令的命令列輸出](media/avere-vfxt-azlogin.png)

1. 使用您的訂用帳戶 ID，透過執行此命令來新增訂用帳戶：```az account set --subscription YOUR_SUBSCRIPTION_ID```

## <a name="create-the-cluster-node-access-role"></a>建立叢集節點存取角色

> [!NOTE] 
> * 如果您不是訂用帳戶擁有者，且尚未建立角色，請讓訂用帳戶擁有者依照下列步驟，或使用[在沒有控制器的情況下建立 Avere vFXT 叢集執行階段存取角色](avere-vfxt-pre-role.md)中的程序來進行操作。
> 
> * Microsoft 內部使用者應使用名為「Avere 叢集執行階段操作員」的現有角色，而非嘗試建立一個角色。 

[角色型存取控制](https://docs.microsoft.com/azure/role-based-access-control/) (RBAC) 會為 vFXT 叢集節點提供授權來執行必要的工作。  

在執行標準 vFXT 叢集作業時，個別 vFXT 節點必須執行諸如讀取 Azure 資源屬性、管理儲存體及控制其他節點之網路介面設定等作業。 

1. 在控制器上，於編輯器中開啟 ``/avere-cluster.json`` 檔案。

   ![先顯示列出命令再顯示 "vi /avere-cluster.json" 的主控台](media/avere-vfxt-open-role.png)

1. 編輯檔案以包括您的訂用帳戶 ID 並刪除其上的一行。 將檔案儲存為 ``avere-cluster.json``。

   ![顯示訂用帳戶 ID 與所選要刪除之「移除此行」的主控台文字編輯器](media/avere-vfxt-edit-role.png)

1. 使用此命令以建立角色：  

   ```bash
   az role definition create --role-definition /avere-cluster.json
   ```

您將會把角色的名稱傳遞給下一個步驟中的叢集建立指令碼。 

## <a name="create-nodes-and-configure-the-cluster"></a>建立節點並設定叢集

若要建立 Avere vFXT 叢集，請編輯控制器上所包含的其中一個範例指令碼，然後在該處執行該指令碼。 範例指令碼位於叢集控制器上的根目錄 (`/`)。

* 如果您想要建立 Blob 容器以作為 Avere vFXT 的後端儲存體系統，請使用 ``create-cloudbacked-cluster`` 指令碼。

* 如果您將在稍後新增儲存體，請使用 ``create-minimal-cluster`` 指令碼。

> [!TIP]
> 新增節點及終結 vFXT 叢集的原型指令碼也包含在叢集控制器 VM 的 `/` 目錄中。

### <a name="edit-the-deployment-script"></a>編輯部署指令碼

請在編輯器中開啟範例指令碼。 您可以使用不同的名稱來儲存自訂指令碼，以避免覆寫原始範例。

請為這些指令碼變數輸入值。

* 資源群組名稱

  * 如果您使用不同資源群組中的網路或儲存體元件，請將變數取消註解，並一併提供這些名稱。 

```python
# Resource groups
# At a minimum specify the resource group.  If the network resources live in a
# different group, specify the network resource group.  Likewise for the storage
# account resource group.
RESOURCE_GROUP=
#NETWORK_RESOURCE_GROUP=
#STORAGE_RESOURCE_GROUP=
```

* 位置名稱
* 虛擬網路名稱
* 子網路名稱
* Azure AD 執行階段角色名稱 - 如果您已依照[建立叢集節點存取角色](#create-the-cluster-node-access-role)中的範例進行操作，請使用 ``avere-cluster``。 
* 儲存體帳戶名稱 (如果要建立新的 Blob 容器)
* 叢集名稱 - 您不能在同一個資源群組中有兩個名稱相同的 vFXT 叢集。 
* 管理密碼 - 選擇一個安全密碼來監視及管理叢集。 此密碼會指派給使用者 ``admin``。 
* 節點執行個體類型 - 如需相關資訊，請參閱 [vFXT 節點大小](avere-vfxt-deploy-plan.md#vfxt-node-sizes)
* 節點快取大小 - 如需相關資訊，請參閱 [vFXT 節點大小](avere-vfxt-deploy-plan.md#vfxt-node-sizes)

儲存檔案並結束。

### <a name="run-the-script"></a>執行指令碼

請輸入您已建立的檔案名稱來執行指令碼。 (範例：`./create-cloudbacked-cluster-west1`)  

> [!TIP]
> 請考慮在[終端機多工器](http://linuxcommand.org/lc3_adv_termmux.php) (例如 `screen` 或 `tmux`) 內執行此命令，以防萬一發生連線中斷的情況。  

輸出也會記錄到 `~/vfxt.log`中。

當指令碼完成時，請複製管理 IP 位址，您將需要這項資訊來進行叢集管理。

![在接近結尾處顯示管理 IP 位址的指令碼命令列輸出](media/avere-vfxt-mgmt-ip.png)

## <a name="next-step"></a>後續步驟

既然叢集已處於執行狀態且您知道其管理 IP 位址，現在即可[連線到叢集設定工具](avere-vfxt-cluster-gui.md)，以視需要啟用支援及新增儲存體。
