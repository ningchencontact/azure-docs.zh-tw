---
author: cynthn
ms.author: cynthn
ms.date: 04/30/2019
ms.topic: include
ms.service: virtual-machines-linux
manager: jeconnoc
ms.openlocfilehash: e1b3b5fe603072069cb3a19c7597fcc1872fefd7
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2019
ms.locfileid: "65416265"
---
標準化的虛擬機器 (VM) 映像可讓組織移轉至雲端，並確保在部署中的一致性。 映像通常包含預先定義的安全性和組態設定和必要的軟體。 設定您自己的映像處理管線需要的時間、 基礎結構和安裝程式，但使用 Azure VM 映像產生器，只是提供描述您的映像的簡單設定、 提交至服務，和建置，並散發映像。
 
Azure VM 映像產生器 （Azure 映像產生器） 可讓您開始使用 Windows 或 Linux 為基礎的 Azure Marketplace 映像、 現有的自訂映像或 Red Hat Enterprise Linux (RHEL) ISO，並開始新增您自己的自訂。 因為映像產生器的基礎[HashiCorp Packer](https://packer.io/)，您也可以匯入現有的 Packer shell 佈建程式指令碼。 您也可以指定您想要您的映像或裝載的 Azure 共用映像庫 (virtual-machines-common-shared-image-galleries.md)，做為受管理的映像 VHD 的位置。

> [!IMPORTANT]
> Azure 映像產生器目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="preview-features"></a>Preview 功能

預覽版本中，支援這些功能：

- 建立 golden 基準映像，包括您的最小的安全性和公司的組態，並允許進一步自訂其需求的部門。
- 修補現有的映像，映像產生器可讓您持續修補現有的自訂映像。
- Azure 共用映像庫中，與整合可讓您在發佈時，版本，並擴展全球，映像，並提供您的映像管理系統。
- 與現有的映像整合建置管線，只從您的管線，呼叫映像產生器，或使用簡單預覽映像產生器 Azure DevOps 工作。
- 將現有的映像自訂管線移轉至 Azure。 使用您現有的指令碼、 命令及處理程序來自訂映像。
- 使用 Red Hat 攜帶您自己的訂用帳戶支援。 建立合格、 未使用 Red Hat 訂用帳戶使用的 Red Hat Enterprise 映像。
- 建立 VHD 格式的映像。
 

## <a name="regions"></a>區域
Azure 映像產生器服務會在這些區域提供預覽。 這些區域外，就可以發佈映像。
- 美國東部
- 美國東部 2
- 美國中西部
- 美國西部
- 美國西部 2

## <a name="os-support"></a>OS 支援
AIB 會支援 Azure Marketplace 基本 OS 映像：
- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 7.6
- CentOS 7.6
- Windows 2016
- Windows 2019


## <a name="how-it-works"></a>運作方式


![Azure 映像產生器的概念圖](./media/virtual-machines-image-builder-overview/image-builder.png)

Azure 映像產生器是完全受控的 Azure 服務均可存取 Azure 資源提供者。 Azure 映像產生器程序有三個主要部分： 來源、 自訂和散發，這些顯示在範本中。 下圖顯示的元件，與它們的某些屬性。 
 


**映像產生器程序** 

![Azure 映像產生器程序的概念圖](./media/virtual-machines-image-builder-overview/image-builder-process.png)

1. 建立映像做為範本的.json 檔案。 此.json 檔案包含影像來源、 自訂和發佈的相關資訊。 有多個範例中的， [Azure 映像產生器的 GitHub 存放庫](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts)。
1. 送出至服務，這會在您指定的資源群組中建立的映像範本的成品。 在背景中，來源映像或 ISO，並視需要的指令碼，會下載映像產生器。 這些儲存在個別的資源群組，會自動建立訂用帳戶，格式為:IT_<DestinationResourceGroup>_<TemplateName>。 
1. 一旦建立映像範本時，您可以建立映像。 在背景中映像產生器會使用範本和原始程式檔來建立 VM、 網路和儲存體中 IT_<DestinationResourceGroup>_<TemplateName>資源群組。
1. 在建立映像，映像產生器會將映像會根據的範本，然後刪除 IT_ 中的其他資源<DestinationResourceGroup>_<TemplateName>程序中所建立的資源群組。


## <a name="permissions"></a>權限

若要允許發佈至其中一個受管理的映像或共用映像庫映像的 Azure VM 映像產生器，您必須提供 「 Azure 虛擬機器映像產生器 」 服務的 「 參與者 」 權限 (應用程式識別碼： cf32a0cc-373c-47c9-9156-0db11f6a6dfc) 上的資源群組。 

如果您使用的現有自訂的受控映像或映像版本，Azure 映像產生器將會需要 'Reader' 存取這些資源群組的最小值。

您可以指派存取使用 Azure CLI:

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/<distributeResoureGroupName>
```

如果找不到的服務帳戶，這可能表示，您要在其中新增角色指派的訂用帳戶具有尚未註冊資源提供者。


## <a name="costs"></a>費用
您將會產生一些計算、 網路和儲存體成本時建立、 建置及儲存使用 Azure 映像產生器的映像。 這些成本是類似於手動建立自訂映像所產生的成本。 如需資源，您將您 Azure 費率收費。 

在映像建立程序中，檔案會下載並儲存在`IT_<DestinationResourceGroup>_<TemplateName>`資源群組，將會產生小型的儲存體成本。 您不想保留這些項目，f 會刪除映像範本映像建置之後。
 
映像產生器會建立使用 D1v2 VM 大小和儲存體和網路的 VM 所需的 vm。 這些資源將持續期間的建置流程，並將會刪除映像產生器完成後建立映像。 
 
Azure 映像產生器會將映像到您選定的區域，可能會產生網路出口流量費用。
 
## <a name="next-steps"></a>後續步驟 
 
若要試用 Azure 映像產生器，請參閱文章中的建置[Linux](../articles/virtual-machines/linux/image-builder.md)或是[Windows](../articles/virtual-machines/windows/image-builder.md)映像。
 
 
