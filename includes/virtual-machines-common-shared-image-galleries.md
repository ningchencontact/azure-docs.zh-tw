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
ms.openlocfilehash: e43b8c951d8c7d5d60904fe49d8639efaba5c89f
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2019
ms.locfileid: "68775851"
---
共用映射資源庫是一種服務, 可協助您在受控映射周圍建立結構和組織。 共用映射資源庫提供:

- 受管理的映射全域複寫。
- 為映射進行版本控制和群組, 以方便管理。
- 在支援可用性區域的區域中, 具有區域冗余儲存體 (ZRS) 帳戶的高可用性映射。 面對區域性故障時，ZRS 能提供更佳的復原力。
- 使用 RBAC 在訂用帳戶之間共用, 甚至是在 Active Directory (AD) 租使用者之間共用。
- 使用每個區域中的映射複本來調整您的部署。

使用共用映像庫，即可在組織內對於不同的使用者、服務主體或 AD 群組共用您的映像。 共用映像可以複寫至多個區域，以更快速調整您的部署。

根據映像的建立方式，受控映像可能是完整 VM (包括任何連結的資料磁碟) 或僅含 OS 磁碟的複本。 當您從映像建立 VM 時，映像中 VHD 的複本可用來建立新 VM 的磁碟。 受控映像會保留在儲存體中，而且可以一再用來建立新的 VM。

如果您有大量需要維護的受控映射, 而且想要讓它們可供整個公司使用, 您可以使用共用映射庫做為存放庫, 讓您輕鬆共用您的映射。 

共用映像庫具有多個資源類型：

| Resource | 描述|
|----------|------------|
| **受控映像** | 可以單獨使用或用來在映射庫中建立**映射版本**的基本映射。 受控映像是從一般化 VM 建立的。 受控映像是一種特殊的 VHD 類型，可用來產生多個 VM，現在可以用來建立共用映像版本。 |
| **映像庫** | 和 Azure Marketplace 一樣，**映像庫**是用於管理和共用映像的存放庫，但您可以控制哪些使用者能夠存取。 |
| **映像定義** | 映射會定義于資源庫中, 並包含影像的相關資訊, 以及在您的組織內使用它的需求。 您可以包含如映射是 Windows 或 Linux、最小和最大記憶體需求, 以及版本資訊等資訊。 這是映像類型的定義。 |
| **映像版本** | **映像版本**是在使用資源庫時用來建立 VM 的項目。 您可以視需要為環境準備多個映像版本。 和受控映像一樣，當您使用**映像版本**來建立 VM 時，系統會使用映像版本來建立 VM 的新磁碟。 映像版本可以使用多次。 |

<br>


![圖形：顯示如何在資源庫中備有多個映像版本](./media/shared-image-galleries/shared-image-gallery.png)

## <a name="image-definitions"></a>映像定義

映射定義是映射版本的邏輯群組。 映射定義包含建立映射的原因、作業系統的目標, 以及使用映射的相關資訊。 映射定義就像是關於建立特定映射的所有詳細資料的計畫。 您不會從映射定義部署 VM, 而是從定義所建立的映射版本。


每個映射定義都有三個參數, 用於組合-**發行者**、**供應**專案和**SKU**。 這些是用來尋找特定的映射定義。 不同的映像版本之間可以擁有一或兩個相同的值，但不能三個值都相同。  例如，以下是三個映像定義和其值：

|映像定義|發行者|供應項目|SKU|
|---|---|---|---|
|myImage1|Contoso|財務|後端|
|myImage2|Contoso|財務|前端|
|myImage3|測試|財務|前端|

這三個映像定義都擁有唯一的值組。 格式類似于您目前可以在 Azure PowerShell 中指定[Azure Marketplace 映射](../articles/virtual-machines/windows/cli-ps-findimage.md)的發行者、供應專案和 SKU, 以取得最新版本的 Marketplace 映射。 每個映射定義都必須有一組唯一的這些值。

以下是可在映射定義上設定的其他參數, 讓您可以更輕鬆地追蹤您的資源:

* 作業系統狀態-您可以將 OS 狀態設定為 [一般化] 或 [特製化], 但目前僅支援一般化。 映射必須從已使用適用于 Windows 或`waagent -deprovision` Linux 的 Sysprep 所一般化的 vm 建立。
* 作業系統-可以是 Windows 或 Linux。
* 描述-使用描述, 以提供有關映射定義存在原因的詳細資訊。 例如, 您的前端伺服器可能會有預先安裝應用程式的映射定義。
* Eula-可用來指向映射定義特有的使用者授權合約。
* 隱私權聲明和版本資訊-將版本資訊和隱私權聲明儲存在 Azure 儲存體中, 並提供 URI 供您作為映射定義的一部分來存取這些聲明。
* 生命循環結束日期-將生命循環結束日期附加至您的映射定義, 以便能夠使用自動化來刪除舊的映射定義。
* 標記-您可以在建立映射定義時新增標記。 如需標記的詳細資訊, 請參閱[使用標記來組織您的資源](../articles/azure-resource-manager/resource-group-using-tags.md)
* 最小和最大 vCPU 和記憶體建議-如果您的映射有 vCPU 和記憶體建議, 您可以將該資訊附加至映射定義。
* 不允許的磁片類型-您可以提供 VM 儲存體需求的相關資訊。 例如, 如果映射不適合標準 HDD 磁片, 您可以將它們新增至不允許清單。


## <a name="regional-support"></a>區域支援

下表列出來源區域。 所有公用區域都可以是目的地區域, 但若要複寫至澳大利亞中部並澳大利亞中部2您必須將訂用帳戶列入允許清單。 若欲要求加入允許清單，請移至： https://azure.microsoft.com/en-au/global-infrastructure/australia/contact/


| 來源區域 |
|---------------------|-----------------|------------------|-----------------|
| 澳大利亞中部   | 美國中部 EUAP | 南韓中部    | 美國中西部 |
| 澳大利亞中部 2 | 東亞       | 南韓南部      | 西歐     |
| 澳大利亞東部      | East US         | 美國中北部 | 印度西部      |
| 澳大利亞東南部 | 美國東部 2       | 北歐     | 美國西部         |
| 巴西南部        | 美國東部 2 EUAP  | 美國中南部 | 美國西部 2       |
| 加拿大中部      | 法國中部  | 印度南部      |                 |
| 加拿大東部         | 法國南部    | 東南亞   |                 |
| 印度中部       | 日本東部      | 英國南部         |                 |
| 美國中部          | 日本西部      | 英國西部          |                 |



## <a name="limits"></a>限制 

使用共用映射庫部署資源時, 每個訂用帳戶都有限制:
- 100共用映射資源庫, 每個訂用帳戶, 每個區域
- 1000每個區域每個訂用帳戶的映射定義
- 10000映射版本, 每個訂用帳戶, 每個區域

如需詳細資訊, 請參閱根據[限制檢查資源使用狀況](https://docs.microsoft.com/azure/networking/check-usage-against-limits), 以取得如何檢查目前使用量的範例。
 

## <a name="scaling"></a>縮放
共用映像庫可讓您指定要讓 Azure 保留的映像複本數目。 這對於有多個 VM 的部署案例很有幫助，因為 VM 部署可以分散到不同複本，減少執行個體建立程序由於單一複本多載而遭到節流的機會。


使用共用映射資源庫時, 您現在可以在虛擬機器擴展集中部署最多1000個 VM 實例 (從600開始, 使用受控映射)。 映射複本可提供更佳的部署效能、可靠性和一致性。  您可以根據區域的規模需求, 在每個目的地區域中設定不同的複本計數。 由於每個複本都是您映射的深層複本, 因此可協助您使用每個額外的複本, 以線性方式調整您的部署。 雖然我們不了解兩個影像或區域都相同, 但以下是如何在區域中使用複本的一般指導方針:

- 針對您同時建立的每20個 Vm, 建議您保留一個複本。 例如, 如果您使用區域中的相同映射同時建立 120 Vm, 我們建議您至少保留映射的6個複本。 
- 針對最多600個實例的每個擴展集部署, 建議您至少保留一個複本。 例如, 如果您同時建立5個擴展集, 每個都有600個 VM 實例在單一區域中使用相同的映射, 我們建議您至少保留映射的5個複本。 

我們一律建議您 overprovision 複本數目, 因為影像大小、內容和 OS 類型等因素。


![圖形：顯示如何調整映像](./media/shared-image-galleries/scaling.png)



## <a name="make-your-images-highly-available"></a>讓您的映射具有高可用性

[Azure 區域冗余儲存體 (ZRS)](https://azure.microsoft.com/blog/azure-zone-redundant-storage-in-public-preview/)可針對區域中的可用性區域失敗提供恢復功能。 透過共用映射資源庫的正式運作, 您可以選擇將映射儲存在可用性區域的區域中的 ZRS 帳戶。 

您也可以選擇每個目的地區域的帳戶類型。 預設儲存體帳戶類型為 Standard_LRS, 但您可以選擇 [Standard_ZRS] 作為具有可用性區域的區域。 請在[這裡](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs)查看 ZRS 的區域可用性。

![顯示 ZRS 的圖形](./media/shared-image-galleries/zrs.png)


## <a name="replication"></a>複寫
共用映像庫也可讓您自動地將映像複寫到其他 Azure 區域。 每個共用映像版本皆可複寫到不同區域，實際情形取決於何者對組織有利。 舉例來說，一律將最新映像複寫到多個區域，而讓所有較舊的版本只留在 1 個區域中。 這有助於節省共用映像版本的儲存體成本。 

作為共用映像版本複寫目的地的區域可在共用映像版本建立之後更新。 複寫到不同區域所需的時間取決於所複製的資料量，以及作為版本複寫目的地的區域數目。 在某些情況下，這可能需要幾小時的時間。 複寫進行期間，您可以檢視每個區域的複寫狀態。 一旦在區域中完成映射複寫, 您就可以在該區域中使用該映射版本來部署 VM 或擴展集。

![圖形：顯示如何複寫映像](./media/shared-image-galleries/replication.png)


## <a name="access"></a>存取權

由於共用映射庫、映射定義和映射版本都是資源, 因此可以使用內建的原生 Azure RBAC 控制項來共用。 使用 RBAC, 您可以將這些資源與其他使用者、服務主體和群組共用。 您甚至可以在其建立所在的租使用者外, 共用其個人的存取權。 一旦使用者擁有共用映射版本的存取權, 他們就可以部署 VM 或虛擬機器擴展集。  以下共用矩陣可協助您了解使用者有權存取的項目：

| 與使用者共用     | 共用映像庫 | 映像定義 | 影像版本 |
|----------------------|----------------------|--------------|----------------------|
| 共用映像庫 | 是                  | 是          | 是                  |
| 映像定義     | 否                   | yes          | 是                  |

建議您在資源庫層級共用, 以獲得最佳體驗。 我們不建議您共用個別的映射版本。 如需 RBAC 的詳細資訊, 請參閱[使用 rbac 來管理 Azure 資源的存取權](../articles/role-based-access-control/role-assignments-portal.md)。

您也可以使用多租使用者應用程式註冊, 大規模地共用映射。 如需跨租使用者共用映射的詳細資訊, 請參閱[在 Azure 租使用者之間共用資源庫 VM 映射](../articles/virtual-machines/linux/share-images-across-tenants.md)。

## <a name="billing"></a>帳務
使用共用映像庫服務不會有額外費用。 您只會支付下列資源的費用：
- 儲存共用映像版本的儲存體成本。 成本取決於映射版本的複本數目, 以及版本所複寫到的區域數目。 例如, 如果您有2個影像, 而且兩者都複寫到3個區域, 則會根據其大小來變更6個受控磁片。 如需詳細資訊，請參閱[受控磁碟定價](https://azure.microsoft.com/pricing/details/managed-disks/)。
- 從來源區域將第一個映射版本複寫至複寫區域的網路輸出費用。 後續的複本會在區域內處理, 因此不會產生額外費用。 

## <a name="updating-resources"></a>正在更新資源

建立之後, 您就可以對映射庫資源進行一些變更。 這些限制為:
 
共用映像庫：
- 描述

映像定義：
- 建議的 vCPU
- 建議的記憶體
- 描述
- 生命週期結束日期

映像版本：
- 區域複本計數
- 目標區域
- 從最新排除
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

## <a name="frequently-asked-questions"></a>常見問答集 

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
 
 A. 是的。 根據您可能擁有的映像類型，案例共有 3 種。

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

 A. 是, 您可以使用 RBAC 來與租使用者之間的個人共用。 但是, 若要大規模共用, 請參閱使用[PowerShell](../articles/virtual-machines/windows/share-images-across-tenants.md)或[CLI](../articles/virtual-machines/linux/share-images-across-tenants.md)在 Azure 租使用者上共用資源庫映射。


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

若要指定區域複本計數, 請傳遞位置, 以及您想要在該區域中建立的複本數目:“South Central US=2”。 

如果未對每個位置指定區域複本計數，則複本的預設數目會是所指定的一般複本計數。 

若要在 CLI 中指定一般複本計數，請在 `az sig image-version create` 命令中使用 **--replica-count** 引數。


**問：** 是否可以在想要用來建立映像定義和映像版本的地方以外的位置建立共用映像庫？

 A. 是，可以的。 但最佳做法是, 建議您將資源群組、共用映射庫、映射定義和映射版本保留在相同的位置。


**問：** 使用共用映像庫需要哪些費用？

 A. 除了用於儲存映像版本的儲存體費用，以及用於將映像版本從來源區域複寫到目標區域的網路輸出費用外，使用共用映像庫服務不會再有任何費用。

**問：** 我應該使用哪些 API 版本來建立共用映像庫、映像定義、映像版本，以及從映像版本建立 VM/VMSS？

 A. 對於使用映像版本來部署 VM 和虛擬機器擴展集，建議您使用 API 版本 2018-04-01 或更高版本。 若要處理共用映像庫、映像定義及映像版本，建議您使用 API 版本 2018-06-01。 區域冗余儲存體 (ZRS) 需要2019-03-01 版或更新版本。
