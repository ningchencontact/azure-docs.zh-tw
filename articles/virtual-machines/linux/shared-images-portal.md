---
title: 適用於 Linux 使用入口網站中建立共用的 Azure 虛擬機器映像 |Microsoft Docs
description: 了解如何使用 Azure 入口網站來建立及共用虛擬機器映像。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/27/2019
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: e5518d019800d2d194369225a060b9b728e21520
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67465465"
---
# <a name="create-a-shared-image-gallery-using-the-azure-portal"></a>建立使用 Azure 入口網站共用映像庫

[共用映像資源庫](shared-image-galleries.md)可簡化跨組織共用自訂映像。 自訂映像類似 Marketplace 映像，但您要自行建立它們。 自訂映像可用於啟動部署工作，例如，預先載入應用程式、應用程式設定和其他 OS 設定。 

共用映像資源庫可讓您與 AAD 租用戶中區域內或跨區域組織中的其他人共用自訂 VM 映像。 選擇您要共用的映像、您要開放使用的區域，以及您要共用的對象。 您可以建立多個資源庫，讓您可以根據邏輯群組共用映像。 

資源庫是一種頂層資源，可提供完整的角色型存取控制 (RBAC)。 可建立映像版本，並且您可以選擇將每個映像版本複寫到不同的 Azure 區域集合。 資源庫僅適用於受控映像。

共用映像庫具有多個資源類型。 我們將在這篇文章中使用或建置這些資源類型：

| 資源 | 描述|
|----------|------------|
| **受控映像** | 這是基本映像，既可單獨使用，也可用來在映像庫中建立個**映像版本**。 受控映像是從一般化 VM 建立的。 受控映像是一種特殊的 VHD 類型，可用來產生多個 VM，現在可以用來建立共用映像版本。 |
| **映像庫** | 和 Azure Marketplace 一樣，**映像庫**是用於管理和共用映像的存放庫，但您可以控制哪些使用者能夠存取。 |
| **映像定義** | 映像會在資源庫內定義，並帶有映像資訊以及在內部使用時所需滿足的需求。 這包括映像是 Windows 還是 Linux、版本資訊以及最小和最大的記憶體需求。 這是映像類型的定義。 |
| **映像版本** | **映像版本**是在使用資源庫時用來建立 VM 的項目。 您可以視需要為環境準備多個映像版本。 和受控映像一樣，當您使用**映像版本**來建立 VM 時，系統會使用映像版本來建立 VM 的新磁碟。 映像版本可以使用多次。 |


## <a name="before-you-begin"></a>開始之前

若要完成本文中的範例，您必須具有現有的受控映像。 您可以遵循[教學課程：使用 Azure PowerShell 建立 Azure VM 的自訂映像](tutorial-custom-images.md)來建立一個 (如有需要)。 如果受控映像會包含資料磁碟，資料磁碟大小不得超過 1 TB。

逐步完成本文之後，請視需要取代資源群組和 VM 名稱。

 
[!INCLUDE [virtual-machines-common-shared-images-portal](../../../includes/virtual-machines-common-shared-images-portal.md)]

## <a name="create-vms-from-an-image"></a>從映像建立 VM

映像版本完成之後，您可以建立一或多個新的 VM。 

> [!IMPORTANT]
> 若要從另一個 azure 租用戶中的映像部署 VM，您無法使用入口網站。 若要從租用戶之間共用映像建立 VM，您必須使用[Azure CLI](shared-images.md#create-a-vm)或是[Powershell](../windows/shared-images.md#create-vms-from-an-image)。


此範例在*美國東部*資料中心的 *myResourceGroup* 中建立名為 *myVMfromImage* 的 VM。

1. 在您的映像版本頁面上，選取**建立 VM**從頁面頂端的功能表。
1. 針對**資源群組**，選取**新建**和型別*myResourceGroup*的名稱。
1. 在 **虛擬機器名稱**，型別*myVM*。
1. 針對**地區**，選取*美國東部*。
1. 針對**可用性選項**，保留預設值是*沒有所需的基礎結構備援性*。
1. 值**映像**應該自動填入，如果您從映像版本的頁面開始。
1. 針對**大小**，從可用大小清單中選擇的 VM 大小，然後按一下 選取。
1. 底下**系統管理員帳戶**，選取**密碼**或是**SSH 公用金鑰**，然後輸入您的資訊。
1. 如果您想要允許遠端存取 VM，底下**公用輸入連接埠**，選擇**允許選取的連接埠**，然後選取**SSH (22)** 從下拉式清單。 如果您不想允許遠端存取 VM，請勿**無**選取**公用輸入連接埠**。
1. 當您完成時，請選取**檢閱 + 建立**在頁面底部的按鈕。
1. VM 通過驗證之後，請選取**建立**底部的頁面以開始部署。


## <a name="clean-up-resources"></a>清除資源

若不再需要，您可以刪除資源群組、虛擬機器和所有相關資源。 若要這樣做，請選取虛擬機器的資源群組，選取 [刪除]  ，然後確認要刪除的資源群組名稱。

如果您想要刪除個別的資源，您需要以相反順序加以刪除。 例如，若要刪除的映像定義，您需要刪除所有從該映像建立的映像版本。

## <a name="next-steps"></a>後續步驟

您也可以使用範本建立共用映像庫資源。 有數個 Azure 快速入門範本可以使用： 

- [建立共用映像資源庫](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [在共用映像資源庫中建立映像定義](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [在共用映像資源庫中建立映像版本](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [從映像版本建立 VM](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

如需共用映像資源庫的詳細資訊，請參閱[概觀](shared-image-galleries.md)。 若遇到任何問題，請參閱[針對共用映像資源庫問題進行疑難排解](troubleshooting-shared-images.md)。

