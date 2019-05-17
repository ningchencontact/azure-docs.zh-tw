---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 05/06/2019
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 4063e79a9415ac35b09cc77d0110c04e191b49c7
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/11/2019
ms.locfileid: "65546715"
---
共用映像庫服務可協助您圍繞自訂受控虛擬機器映像來建置結構和組織。 共用映像庫提供：

- 管理全域複寫的映像。
- 版本控制和映像，以便更容易管理的群組。
- 讓您的映像支援可用性區域的區域中使用區域備援儲存體 (ZRS) 帳戶成為高可用性。 ZRS 提供較佳的彈性，防範區域性失敗。
- 跨訂用帳戶，和租用戶，使用 RBAC 甚至之間共用。

使用共用映像庫，即可在組織內對於不同的使用者、服務主體或 AD 群組共用您的映像。 共用映像可以複寫至多個區域，以更快速調整您的部署。

根據映像的建立方式，受控映像可能是完整 VM (包括任何連結的資料磁碟) 或僅含 OS 磁碟的複本。 當您從映像建立 VM 時，映像中 VHD 的複本可用來建立新 VM 的磁碟。 受控映像會保留在儲存體中，而且可以一再用來建立新的 VM。

如果您有大量的受管理的映像，您需要維護，而且想要提供給全公司時，您可以使用共用映像庫做為儲存機制，可讓您輕鬆地共用您的映像。 

共用映像庫具有多個資源類型：

| 資源 | 說明|
|----------|------------|
| **受控映像** | 可單獨使用或用來建立的基本映像**映像版本**映像庫中。 受控映像是從一般化 VM 建立的。 受控映像是一種特殊的 VHD 類型，可用來產生多個 VM，現在可以用來建立共用映像版本。 |
| **映像庫** | 和 Azure Marketplace 一樣，**映像庫**是用於管理和共用映像的存放庫，但您可以控制哪些使用者能夠存取。 |
| **映像定義** | 映像資源庫內所定義，並執行的映像和使用組織的需求的相關資訊。 您可以包含類似影像是否為 Windows 或 Linux，最小和最大的記憶體需求的資訊和版本資訊。 這是映像類型的定義。 |
| **映像版本** | **映像版本**是在使用資源庫時用來建立 VM 的項目。 您可以視需要為環境準備多個映像版本。 和受控映像一樣，當您使用**映像版本**來建立 VM 時，系統會使用映像版本來建立 VM 的新磁碟。 映像版本可以使用多次。 |

<br>


![圖形：顯示如何在資源庫中備有多個映像版本](./media/shared-image-galleries/shared-image-gallery.png)

## <a name="image-definitions"></a>映像定義

映像定義是版本的映像的邏輯群組。 映像定義會保存為什麼建立映像的相關資訊、 哪些是，OS 和使用映像的相關資訊。 影像定義就像是建立特定的映像的詳細資訊的所有的計劃。 您不須部署 VM 映像定義，但從定義建立的映像版本。


有三個參數的每個映像定義用於組合-**發行者**，**提供**並**SKU**。 這些用來尋找特定的映像定義。 不同的映像版本之間可以擁有一或兩個相同的值，但不能三個值都相同。  例如，以下是三個映像定義和其值：

|映像定義|發行者|供應項目|SKU|
|---|---|---|---|
|myImage1|Contoso|財務|後端|
|myImage2|Contoso|財務|前端|
|myImage3|測試|財務|前端|

這三個映像定義都擁有唯一的值組。 格式會類似於如何目前指定發行者、 優惠和 SKU [Azure Marketplace 映像](../articles/virtual-machines/windows/cli-ps-findimage.md)Azure PowerShell，以取得最新版的 Marketplace 映像中。 每個映像定義必須要有一組唯一的這些值。

其他參數，讓您可以更輕鬆地追蹤您的資源可設定您的映像定義如下：

* 作業系統狀態-您可以 OS 狀態設定為一般化或特製化，但只有一般化目前支援。 必須從已使用的 Windows Sysprep 一般化的 Vm 建立映像或`waagent -deprovision`適用於 Linux。
* 作業系統-可以是 Windows 或 Linux。
* 描述-提供映像定義的存在原因的詳細的資訊的使用說明。 例如，您可能有您已預先安裝應用程式的前端伺服器的映像定義。
* 使用者授權合約-可用來指向特定的映像定義的使用者授權合約。
* 隱私權聲明 」 以及 「 版本資訊-Azure 儲存體中儲存的版本資訊與隱私權聲明，並提供的 URI 來存取這些映像定義的一部分。
* 生命週期結束日期-附加至您的映像定義的生命週期結束日期，為了能夠使用自動化來刪除舊的映像定義。
* 標記-當您建立您的映像定義時，可以新增標記。 如需標記的詳細資訊，請參閱[使用標記來組織您的資源](../articles/azure-resource-manager/resource-group-using-tags.md)
* 最小值和最大 vCPU 和記憶體建議-如果您的映像有個 vCPU 和記憶體建議，您可以附加該資訊加入您的映像定義。
* 不允許磁碟類型-您可以提供您 VM 的儲存體需求的相關資訊。 例如，如果映像不適合標準 HDD 磁碟，您新增至不允許清單。


## <a name="regional-support"></a>區域支援

下表詳列來源區域。 所有公用區域可以是目標區域中，但您要複寫至澳洲中部和澳大利亞中部 2 有您的訂用帳戶列入白名單。 若欲要求加入允許清單，請移至： https://www.microsoft.com/en-au/central-regions-eligibility/


| 來源區域 |
|---------------------|-----------------|------------------|-----------------|
| 澳大利亞中部   | 美國中部 EUAP | 南韓中部    | 英國南部 2      |
| 澳大利亞中部 2 | 東亞       | 南韓南部      | 英國西部         |
| 澳洲東部      | 美國東部         | 美國中北部 | 美國中西部 |
| 澳大利亞東南部 | 美國東部 2       | 北歐     | 西歐     |
| 巴西南部        | 美國東部 2 EUAP  | 美國中南部 | 印度西部      |
| 加拿大中部      | 法國中部  | 印度南部      | 美國西部         |
| 加拿大東部         | 法國南部    | 東南亞   | 美國西部         |
| 印度中部       | 日本東部      | 英國北部         | 美國西部 2       |
| 美國中部          | 日本西部      | 英國南部         |                 |



## <a name="limits"></a>限制 

有限制，每個訂用帳戶，使用共用映像資源庫部署資源：
- 100 共用的映像組件庫，每個訂用帳戶，每個區域
- 1,000 的映像定義，每個訂用帳戶，每個區域
- 每個訂用帳戶，每個區域 10,000 映像版本

如需詳細資訊，請參閱 <<c0> [ 檢查資源使用量與限制](https://docs.microsoft.com/azure/networking/check-usage-against-limits)如需有關如何檢查您目前的使用方式的範例。
 

## <a name="scaling"></a>縮放
共用映像庫可讓您指定要讓 Azure 保留的映像複本數目。 這對於有多個 VM 的部署案例很有幫助，因為 VM 部署可以分散到不同複本，減少執行個體建立程序由於單一複本多載而遭到節流的機會。

![圖形：顯示如何調整映像](./media/shared-image-galleries/scaling.png)


## <a name="replication"></a>複寫
共用映像庫也可讓您自動地將映像複寫到其他 Azure 區域。 每個共用映像版本皆可複寫到不同區域，實際情形取決於何者對組織有利。 舉例來說，一律將最新映像複寫到多個區域，而讓所有較舊的版本只留在 1 個區域中。 這有助於節省共用映像版本的儲存體成本。 

作為共用映像版本複寫目的地的區域可在共用映像版本建立之後更新。 複寫到不同區域所需的時間取決於所複製的資料量，以及作為版本複寫目的地的區域數目。 在某些情況下，這可能需要幾小時的時間。 複寫進行期間，您可以檢視每個區域的複寫狀態。 映像複寫完成之後在區域中，您可以再部署 VM 或擴展集的區域中使用該映像版本。

![圖形：顯示如何複寫映像](./media/shared-image-galleries/replication.png)


## <a name="access"></a>Access

由於共用映像庫、共用映像和共用映像版本都是資源，所以您可以使用內建的原生 Azure RBAC 控制來共用。 您可以使用 RBAC 來分享給其他使用者、 服務主體和群組的這些資源。 您甚至可以共用內建立的租用戶以外的人員存取。 一旦使用者擁有存取共用的映像版本，他們可以部署 VM 或虛擬機器擴展集。  以下共用矩陣可協助您了解使用者有權存取的項目：

| 與使用者共用     | 共用映像庫 | 共用映像 | 共用映像版本 |
|----------------------|----------------------|--------------|----------------------|
| 共用映像庫 | 有                  | 是          | 有                  |
| 共用映像         | 無                   | yes          | 有                  |
| 共用映像版本 | 無                   | 否           | 有                  |

我們建議您使用共用組件庫層級以獲得最佳體驗。 如需有關 RBAC 的詳細資訊，請參閱 <<c0> [ 管理 Azure 資源使用 RBAC 存取權](../articles/role-based-access-control/role-assignments-portal.md)。

映像也可以共用，大規模的情況下，跨租用戶使用多租用戶應用程式註冊。 如需有關如何在租用戶之間共用映像的詳細資訊，請參閱[跨 Azure 租用戶共用組件庫的 VM 映像](../articles/virtual-machines/linux/share-images-across-tenants.md)。

## <a name="billing"></a>計費
使用共用映像庫服務不會有額外費用。 您只會支付下列資源的費用：
- 儲存共用映像版本的儲存體成本。 成本取決於映像版本的複本數目和的版本複寫到的區域數目。 比方說，如果您有 2 個影像，同時會複寫至 3 個區域，然後您會變更其大小所根據的 6 個受控磁碟。 如需詳細資訊，請參閱[受控磁碟定價](https://azure.microsoft.com/pricing/details/managed-disks/)。
- 複寫的第一個映像版本從來源區域來複寫區域的網路輸出費用。 後續的複本會在區域中，處理，因此會不有任何額外費用。 

## <a name="updating-resources"></a>正在更新資源

建立之後，您可以進行一些變更映像資源庫資源。 這些僅限於：
 
共用映像庫：
- 說明

映像定義：
- 建議的 vCPU
- 建議的記憶體
- 說明
- 生命週期結束日期

映像版本：
- 區域複本計數
- 目標區域
- 從最新版排除
- 生命週期結束日期


## <a name="sdk-support"></a>SDK 支援

下列 SDK 支援建立共用映像資源庫：

- [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/virtualmachines/management?view=azure-dotnet)
- [Java](https://docs.microsoft.com/java/azure/?view=azure-java-stable)
- [Node.js](https://docs.microsoft.com/javascript/api/azure-arm-compute/?view=azure-node-latest)
- [Python](https://docs.microsoft.com/python/api/overview/azure/virtualmachines?view=azure-python)
- [Go](https://docs.microsoft.com/go/azure/)

## <a name="templates"></a>範本

您可以使用範本建立共用映像庫資源。 有數個 Azure 快速入門範本可以使用： 

- [建立共用映像資源庫](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [在共用映像資源庫中建立映像定義](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [在共用映像資源庫中建立映像版本](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [從映像版本建立 VM](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

## <a name="frequently-asked-questions"></a>常見問題集 

**問：** 如何列出訂用帳戶之間的所有共用映像庫資源？ 
 
 A. 若要針對您可在 Azure 入口網站上存取的訂用帳戶，列出其彼此之間的所有共用映像庫資源，請遵循下列步驟：

1. 開啟 [Azure 入口網站](https://portal.azure.com)。
1. 移至 [所有資源]。
1. 選取所有要列出其內含全部資源的訂用帳戶。
1. 尋找類型為 [私用資源庫] 的資源。
 
   若要查看映像定義和映像版本，您還應該選取 [顯示隱藏的類型]。
 
   若要列出您有權存取之所有訂用帳戶的共用映像庫資源，請在 Azure CLI 中使用下列命令：

   ```bash
   az account list -otsv --query "[].id" | xargs -n 1 az sig list --subscription
   ```


**問：** 是否可以將現有映像移至共用映像庫？
 
 A. 是。 根據您可能擁有的映像類型，案例共有 3 種。

 案例 1：如果您擁有受控映像，則可以透過該映像建立映像定義和映像版本。

 案例 2：如果您擁有非受控的一般化映像，則可以透過該映像建立受控映像，繼而建立映像定義和映像版本。 

 案例 3：如果您在本機檔案系統擁有 VHD，則需要上傳 VHD、建立受控映像，然後就可以從中建立映像定義和映像版本。
- 如果是 Windows VM 的 VHD，請參閱[上傳一般化 VHD](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed)。
- 如果是 Linux VM 的 VHD，請參閱[上傳 VHD](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd#option-1-upload-a-vhd)


**問：** 是否可以從特製化磁碟建立映像版本？

 A. 否，目前不支援將特製化磁碟作為映像。 如果您有特製化磁碟，則需要將特製化磁碟連結至新的 VM 以[從 VHD 建立 VM](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-specialized-portal#create-a-vm-from-a-disk)。 擁有執行中的 VM 後，您必須依照指示從 [Windows VM](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-custom-images) 或 [Linux VM](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images) 建立受控映像。 擁有一般化受控映像後，就可以開始建立共用映像描述和映像版本的程序。

 
**問：** 共用映像庫資源是否可以在建立後移至不同訂用帳戶？

 A. 否，無法將共用映像庫資源移至不同訂用帳戶。 不過如有需要，可將資源庫中的映像版本複寫到其他區域。

**問：** 是否可以跨雲端 (Azure China 21Vianet、Azure 德國和 Azure Government 雲端) 複寫映像版本？ 

 A. 否，無法跨雲端複寫映像版本。

**問：** 是否可以跨訂用帳戶複寫映像版本？ 

 A. 否，但可以在訂用帳戶中跨區域複寫映像版本，然後透過 RBAC 在其他訂用帳戶中使用。

**問：** 是否可以跨 Azure AD 租用戶共用映像版本？ 

 A. 是，您可以使用 RBAC 來跨租用戶共用的個人。 但共用大規模的情況下，請參閱 「 共用資源庫映像跨 Azure 租用戶 」 使用[PowerShell](../articles/virtual-machines/windows/share-images-across-tenants.md)或是[CLI](../articles/virtual-machines/linux/share-images-across-tenants.md)。


**問：** 跨目標區域複寫映像版本需要多久時間？

 A. 映像版本的複寫時間完全取決於映像大小以及作為其複寫目的地的區域數目。 不過，建議您最好讓映像小一點，並讓來源和目標區域近一點以獲得最佳結果。 您可以使用 -ReplicationStatus 旗標檢查複寫的狀態。


**問：** 來源區域和目標區域有何差異？

 A. 來源區域是要建立映像版本的區域，目標區域則是要儲存映像版本複本的區域。 每個映像版本只能有一個來源區域。 此外，在建立映像版本時，請務必將來源區域位置傳遞作為其中一個目標區域。  


**問：** 如何在建立映像版本時指定來源區域？

 A. 在建立映像版本時，您可以使用 **--location** 標籤 (在 CLI 中) 以及 **-Location** 標籤 (在 PowerShell 中) 來指定來源區域。 請確定用來作為基底映像以供建立映像版本的受控映像所在位置，與您想要在其中建立映像版本的位置相同。 此外，在建立映像版本時，請務必將來源區域位置傳遞作為其中一個目標區域。  


**問：** 如何指定要在每個區域中建立的映像版本複本數目？

 A. 有兩種方式可供指定要在每個區域中建立的映像版本複本數目：
 
1. 區域複本計數，用以指定要在每個區域建立的複本數目。 
2. 一般複本計數，這是未指定區域複本計數時，每個區域計數的預設值。 

若要指定區域的複本計數，傳遞位置以及您想要在該區域中建立的複本數目：“South Central US=2”。 

如果未對每個位置指定區域複本計數，則複本的預設數目會是所指定的一般複本計數。 

若要在 CLI 中指定一般複本計數，請在 `az sig image-version create` 命令中使用 **--replica-count** 引數。


**問：** 是否可以在想要用來建立映像定義和映像版本的地方以外的位置建立共用映像庫？

 A. 是，可以的。 但是，最佳做法，我們鼓勵您將資源群組、 共用的映像庫、 映像定義和映像版本放在相同的位置。


**問：** 使用共用映像庫需要哪些費用？

 A. 除了用於儲存映像版本的儲存體費用，以及用於將映像版本從來源區域複寫到目標區域的網路輸出費用外，使用共用映像庫服務不會再有任何費用。

**問：** 我應該使用哪些 API 版本來建立共用映像庫、映像定義、映像版本，以及從映像版本建立 VM/VMSS？

 A. 對於使用映像版本來部署 VM 和虛擬機器擴展集，建議您使用 API 版本 2018-04-01 或更高版本。 若要處理共用映像庫、映像定義及映像版本，建議您使用 API 版本 2018-06-01。 區域備援儲存體 (ZRS) 需要 2019年-03-01 版或更新版本。
