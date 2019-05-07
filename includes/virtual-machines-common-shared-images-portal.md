---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/29/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 9b9b6d08fd14a850838590ce003e889e8e652c7c
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65148054"
---
## <a name="sign-in-to-azure"></a>登入 Azure 

在 https://portal.azure.com 登入 Azure 入口網站。

> [!NOTE]
> 如果您註冊，以便在預覽期間使用共用映像資源庫，您可能需要重新註冊`Microsoft.Compute`提供者。 開啟[Cloud Shell](https://shell.azure.com/bash)和型別： `az provider register -n Microsoft.Compute`

## <a name="create-an-image-gallery"></a>建立映像資源庫

映像資源庫是用於啟用映像共用的主要資源。 資源庫名稱允許的字元為大寫或小寫字母、數字、點和句點。 組件庫名稱不能包含連字號。  資源庫名稱在您的訂用帳戶內必須是唯一的。 

下列範例會在 *myGalleryRG* 資源群組中建立名為 *myGallery* 的資源庫。

1. 選取 Azure 入口網站左上角的 [建立資源]。
1. 使用型別**共用映像庫**中的搜尋方塊，然後選取**共用映像庫**結果中。
1. 在 **共用映像庫**頁面上，按一下**建立**。
1. 選取正確的訂用帳戶。
1. 在 **資源群組**，選取**新建**和型別*myGalleryRG*名稱。
1. 在 **名稱**，型別*myGallery*資源庫的名稱。
1. 保留預設值，如**地區**。
1. 您可以輸入圖庫 中的簡短描述，例如*我的映像庫的測試。* 然後按一下**檢閱 + 建立**。
1. 通過驗證之後，請選取**建立**。
1. 部署完成時，選取**移至資源**。
   
## <a name="create-an-image-definition"></a>建立映像定義 

映像定義會建立映像的邏輯群組。 它們用來管理在其中建立映像版本的相關資訊。 映像定義名稱可包含大寫或小寫字母、 數字、 點、 連字號和句號。 如需您可以指定映像定義之值的詳細資訊，請參閱[映像定義](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#image-definitions)。

建立您的資源庫內的資源庫映像定義。 在此範例中，稱為資源庫映像*myImageDefinition*。

1. 在新的映像庫頁面上，選取**加入新的映像定義**從頁面頂端。 
1. 針對**映像定義名稱**，型別*myImageDefinition*。
1. 針對**作業系統**，選取您的來源映像為基礎的正確選項。
1. 針對**發行者**，型別*myPublisher*。 
1. 針對**優惠**，型別*myOffer*。
1. 針對**SKU**，型別*mySKU*。
1. 請確定 **[是]** 選取我們**啟用**，然後選取**檢閱 + 建立**。
1. 映像定義通過驗證之後，請選取**建立**。
1. 部署完成時，選取**移至資源**。



## <a name="create-an-image-version"></a>建立映像版本

從受控映像建立映像版本。 在此範例中，映像版本為 *1.0.0*，且它會被複寫到「美國中西部」和「美國中南部」資料中心。 當選擇針對複寫的目標區域，請記住，您也必須包含*來源*做為複寫的目標區域。

映像版本允許的字元是數字及句點。 數字必須在 32 位元整數的範圍內。 格式：*MajorVersion*。*MinorVersion*。*修補程式*。

1. 在您的映像定義的頁面，選取**新增版本**從頁面頂端。
1. 在 **地區**，選取您的受控映像儲存所在的區域。 需要建立相同區域中的受管理的映像從建立映像版本。
1. 針對**名稱**，型別*1.0.0*。 映像版本名稱應該遵循*主要*。*次要*。*修補程式*格式化使用的整數。 
1. 在 **來源映像**，從下拉式清單中選取來源受控映像。
1. 在 **從最新的排除**，保留預設值*No*。
1. 針對**生命週期結尾**，從幾個在未來幾個月的行事曆選取日期。
1. 在 **複寫**，保留**預設複本計數**為 1。 您要複寫到來源地區，因此保留為預設值的第一個複本，然後挑選要第二個複本區域*美國東部*。
1. 當您完成時，選取**檢閱 + 建立**。 Azure 會驗證設定。
1. 映像版本通過驗證之後，選取**建立**。
1. 部署完成時，選取**移至資源**。

可能需要將映像複寫到所有目標區域的一段時間。
