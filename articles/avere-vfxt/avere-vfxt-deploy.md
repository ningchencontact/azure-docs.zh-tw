---
title: 部署適用於 Azure 的 Avere vFXT
description: 在 Azure 中部署 Avere vFXT 叢集的步驟
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: rohogue
ms.openlocfilehash: 6ddf950bf2d138a94675ee394109a0d227ea206b
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255447"
---
# <a name="deploy-the-vfxt-cluster"></a>部署 vFXT 叢集

此程序會引導您使用 Azure Marketplace 中提供的部署精靈。 精靈會自動使用 Azure Resource Manager 範本來部署叢集。 在表單中輸入參數並按一下 [建立] 後，Azure 會自動完成下列步驟：

* 建立叢集控制器，這是基本 VM，其中包含部署和管理叢集所需的軟體。
* 設定資源群組和虛擬網路基礎結構，包括建立新的元素。
* 建立叢集節點 Vm，並將其設定為 Avere 叢集。
* 如有要求，會建立新的 Azure Blob 容器，並將其設定為叢集核心檔案管理工具。

依照本檔中的指示進行之後，您將會有一個虛擬網路、一個子網、一個控制器和一個 vFXT 叢集，如下圖所示。 此圖顯示選擇性的 Azure Blob 核心檔案管理工具，其中包含新的 Blob 儲存體容器（在新的儲存體帳戶中，未顯示），以及子網內 Microsoft 儲存體的服務端點。 

![此圖顯示三個具有 Avere 叢集元件的同心圓矩形。 外部矩形會標示為「資源群組」，並包含標示為「Blob 儲存體（選擇性）」的六邊形。 下一個矩形已標記為「虛擬網路：10.0.0.0/16 ' 且不包含任何唯一的元件。 最內層的矩形標示為「子網： 10.0.0.0/24」，其中包含標示為「叢集控制器」的 VM、三個標示為「vFXT 節點（vFXT Cluster）」的 Vm 堆疊，以及標示為「服務端點」的六邊形。 有一個箭號可連接服務端點（位於子網內）和 blob 儲存體（在資源群組中的子網和 vnet 外部）。 箭頭會通過子網和虛擬網路界限。](media/avere-vfxt-deployment.png)  

使用建立範本之前，請確定您已滿足下列先決條件：  

1. [新的訂用帳戶](avere-vfxt-prereqs.md#create-a-new-subscription)
1. [訂用帳戶擁有者權限](avere-vfxt-prereqs.md#configure-subscription-owner-permissions)
1. [vFXT 叢集配額](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)
1. [儲存體服務端點（如有需要）](avere-vfxt-prereqs.md#create-a-storage-service-endpoint-in-your-virtual-network-if-needed) -使用現有的虛擬網路部署和建立 blob 儲存體所需

如需有關叢集部署步驟和規劃的詳細資訊，請參閱[規劃您的 Avere vFXT 系統](avere-vfxt-deploy-plan.md)和[部署概觀](avere-vfxt-deploy-overview.md)。

## <a name="create-the-avere-vfxt-for-azure"></a>建立 Avere vFXT for Azure

藉由搜尋 Avere 並選取 [Avere vFXT for Azure ARM 範本]，在 Azure 入口網站中存取建立範本。 

![使用階層連結 [新增 > Marketplace > 全部內容] 瀏覽顯示的 Azure 入口網站的視窗。 在 [所有專案] 頁面中，搜尋欄位具有「avere」一詞，而第二個結果「Avere vFXT for Azure ARM 範本」則以紅色標示，以反白顯示。](media/avere-vfxt-template-choose.png)

閱讀 Avere vFXT for Azure ARM 範本頁面上的詳細資料之後，請按一下 [**建立**] 開始。 

![使用部署範本的 Azure Marketplace 第一頁顯示畫面](media/avere-vfxt-deploy-first.png)

範本會分割成四個步驟 - 兩個資訊收集頁面，再加上驗證和確認步驟。 

* 第一頁著重於叢集控制器 VM 的設定。 
* 第二頁會收集建立叢集所需的參數，以及相關聯的資源 (例如子網路和儲存體)。 
* 第三頁會總結設定並驗證組態。 
* 第四頁會說明軟體的條款及條件，並讓您開始叢集建立程序。 

## <a name="page-one-parameters---cluster-controller-information"></a>第一頁的參數 - 叢集控制器資訊

部署範本的第一頁會收集叢集控制器的相關資訊。 

![部署範本的第一頁](media/avere-vfxt-deploy-1.png)

填寫下列資訊︰

* **叢集控制器名稱** - 設定叢集控制器 VM 的名稱。

* **控制器的使用者名稱** - 填入叢集控制器 VM 的根使用者名稱。 

* **驗證類型** - 選擇使用 [密碼] 或 [SSH 公開金鑰] 驗證來連線到控制器。 建議使用 SSH 公開金鑰方法；如果需要協助，請參閱[如何建立和使用 SSH 金鑰](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)。

* **密碼**或 **SSH 公開金鑰** - 根據您選取的驗證類型，您必須在下方欄位中提供 RSA 公開金鑰或密碼。 此認證會與稍早提供的使用者名稱搭配使用。

* **訂用帳戶** - 選取 Avere vFXT 的訂用帳戶。 

* **資源群組**- 選取 Avere vFXT 叢集的現有空資源群組，或按一下 [新建] 並輸入新的資源群組名稱。 

* **位置** - 選取您叢集和資源的 Azure 位置。

完成後，按一下 [確定]。 

> [!NOTE]
> 如果您想要讓叢集控制器有公開的 IP 位址，請為叢集建立新的虛擬網路，而不是選取現有的網路。 這項設定位在第二頁。

## <a name="page-two-parameters---vfxt-cluster-information"></a>第二頁的參數 - vFXT 叢集資訊

部署範本的第二頁可讓您設定叢集大小、節點類型、快取大小和儲存體參數等其他設定。 

![部署範本的第二頁](media/avere-vfxt-deploy-2.png)

* **Avere vFXT 叢集節點計數** - 選擇要在叢集中使用的節點數目。 最小值是三個節點，而最大值是十二個。 

* **叢集管理密碼** - 建立用於管理叢集的密碼。 搭配使用此密碼和使用者名稱 ```admin```，即可登入叢集控制台來監視叢集並進行設定。

* **Avere vFXT 叢集名稱** - 為叢集提供唯一名稱。 

* **大小**-此區段會顯示將用於叢集節點的 VM 類型。 雖然只有一個建議選項，[**變更大小**] 連結會開啟一個資料表，其中包含此實例類型的詳細資料，以及價格計算機的連結。  

* **每個節點的快取大小** - 叢集快取會散佈在叢集節點上，因此，Avere vFXT 叢集上的快取大小總計會是節點數目乘上每個節點的快取大小。 

  建議的設定是針對 Standard_E32s_v3 節點，每個節點使用 4 TB。

* **虛擬網路**-定義要存放叢集的新 vnet，或選取符合[規劃您的 Avere vFXT 系統](avere-vfxt-deploy-plan.md#resource-group-and-network-infrastructure)中所述之必要條件的現有 vnet。 

  > [!NOTE]
  > 如果您建立新的 Vnet，叢集控制器將會有公用 IP 位址，以便您存取新的私人網路。 如果您選擇現有的 Vnet，則不會對叢集控制器設定公用 IP 位址。 
  > 
  > 叢集控制器上可見的公用 IP 位址可提供較簡便的 vFXT 存取方式，但會產生些微安全性風險。 
  >  * 叢集控制器上的公用 IP 位址可讓您使用它作為跳躍點主機，以從私人子網路外連線到 Avere vFXT 叢集。
  >  * 如果您沒有在控制器上設定公用 IP 位址，就必須使用另一個跳躍點主機、VPN 連線或 ExpressRoute 來存取叢集。 例如，在已設定 VPN 連線的虛擬網路內建立控制器。
  >  * 如果您建立具有公用 IP 位址的控制器，您應該使用網路安全性群組來保護該控制器 VM。 根據預設，Avere vFXT for Azure 部署會建立網路安全性群組，並限制只能透過連接埠 22，對使用公用 IP 位址的控制器進行輸入存取。 若要進一步保護系統，您可以鎖定 IP 來源位址範圍的存取，也就是只允許從要用來存取叢集的機器進行連線。

  部署範本也會使用 Azure Blob 儲存體的儲存體服務端點來設定新的 vnet，並將網路存取控制鎖定為只有來自叢集子網的 Ip。 

* **子網路** - 選擇現有虛擬網路中的子網路，或建立新的子網路。 

* **建立和使用 blob 儲存體**-選擇 [ **true** ] 以建立新的 Azure blob 容器，並將其設定為新 Avere vFXT 叢集的後端儲存體。 此選項也會在與叢集相同的資源群組中建立新的儲存體帳戶，並在叢集子網內建立 Microsoft 儲存體服務端點。 
  
  如果您提供現有的虛擬網路，則在建立叢集之前，它必須具有儲存體服務端點。 （如需詳細資訊，請參閱[規劃您的 Avere vFXT 系統](avere-vfxt-deploy-plan.md)）。

  如果您不想建立新的容器，請將此欄位設為 [false]。 在此情況下，建立叢集之後，您必須連接並設定儲存體。 參閱[設定儲存體](avere-vfxt-add-storage.md)的指示。 

* **（新）儲存體帳戶**-如果建立新的 Azure Blob 容器，請輸入新儲存體帳戶的名稱。 

## <a name="validation-and-purchase"></a>驗證及購買

第三頁會摘要說明設定並驗證參數。 驗證成功之後，請按一下 [確定] 按鈕繼續作業。 

![部署範本的第三頁 - 驗證](media/avere-vfxt-deploy-3.png)

在第四頁上，輸入任何必要的連絡人資訊，然後按一下 [**建立**] 按鈕以接受條款，並建立 Avere vFXT for Azure 叢集。 

![部署範本的第四頁 - 條款和條件、建立按鈕](media/avere-vfxt-deploy-4.png)

叢集部署大約需要 15 到 20 分鐘。

## <a name="gather-template-output"></a>收集範本輸出

Avere vFXT 範本在完成叢集的建立後，會輸出有關新叢集的一些重要資訊。 

> [!TIP]
> 請務必從範本中複製管理 IP 位址。 您需要此位址來管理叢集。

若要尋找這項資訊，請遵循此程序：

1. 移至您叢集控制器的資源群組。

1. 在左側按一下 [部署]，然後選取 [microsoft-avere.vfxt-template]。

   ![資源群組入口網站頁面，其中左側已選取 [部署]，而 [microsoft-avere.vfxt-template] 則顯示在 [部署名稱] 底下的表格中](media/avere-vfxt-outputs-deployments.png)

1. 在左側按一下 [輸出]。 複製每個欄位中的值。 

   ![輸出頁面的標籤右側欄位中顯示 SSHSTRING、RESOURCE_GROUP、LOCATION、NETWORK_RESOURCE_GROUP、NETWORK、SUBNET、SUBNET_ID、VSERVER_IPs 和 MGMT_IP 值](media/avere-vfxt-outputs-values.png)

## <a name="next-step"></a>後續步驟

現在叢集已處於執行狀態且您已知道其管理 IP 位址，您可以[連線到叢集設定工具](avere-vfxt-cluster-gui.md)，以視需要啟用支援、新增儲存體及自訂其他叢集設定。
