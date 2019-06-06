---
title: 在 Azure DevTest Labs 中設定共用的映像庫 |Microsoft Docs
description: 了解如何在 Azure DevTest Labs 中設定共用的映像庫
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2019
ms.author: spelluru
ms.openlocfilehash: fba969b70ae052c928f33888d3c93eb7683ae9f7
ms.sourcegitcommit: ec7b0bf593645c0d1ef401a3350f162e02c7e9b8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/01/2019
ms.locfileid: "66455762"
---
# <a name="configure-a-shared-image-gallery-in-azure-devtest-labs"></a>在 Azure DevTest Labs 中設定共用映像庫
DevTest Labs 現已支援[共用映像庫](/virtual-machines/windows/shared-image-galleries.md)功能。 其能讓實驗室使用者在建立實驗室資源期間，可以存取共用位置中的映像。 也可協助您圍繞著自訂的受控 VM 映像，來建置結構和組織。 共用映像庫功能支援：

- 映像的受控全域複製
- 映像的版本設定及分組，讓管理更加容易
- 在支援可用性區域的區域中，使用區域備援儲存體 (ZRS) 帳戶，讓映像具備高可用性。 面對區域性故障時，ZRS 能提供更佳的復原力。
- 使用以角色區分的存取控制 (RBAC)，在訂用帳戶和甚至是租用戶之間，進行共用。

如需詳細資訊，請參閱 <<c0> [ 共用映像庫文件](../virtual-machines/windows/shared-image-galleries.md)。 
 
如果您有大量需要維護的受控映像，而且想要提供給全公司使用時，便可使用共用映像庫作為存放庫，以便輕鬆地更新及共用映像。 您身為實驗室擁有者，可以對您的實驗室附加現有的映像庫。 一旦附加了此映像庫之後，實驗室使用者即可利用最新的映像來建立電腦。 此功能最主要的好處是，DevTest Labs 現已可在實驗室之間、訂用帳戶之間以及區域之間，使用共用映像功能。 

## <a name="considerations"></a>考量
- 您只可以附加至實驗室的一個共用的映像庫一次。 如果您想要附加另一個資源庫，您必須中斷現有的連結，並將附加另一個。 
- DevTest Labs 目前不支援上傳的映像，以透過實驗室資源庫。 
- 在建立虛擬機器使用共用的映像資源庫映像時，DevTest Labs 一律會使用此映像的最新發行的版本。
- 研發/測試實驗室會自動將盡力嘗試確保共用映像資源庫將映像複寫到實驗室所在的區域，雖然它不一定可行。 若要避免發生問題，從這些映像建立 Vm 的使用者，請確定映像已複寫到實驗室的區域。 」

## <a name="use-azure-portal"></a>使用 Azure 入口網站
1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 選取 **所有服務**左側的導覽功能表上。
1. 選取  **DevTest Labs**從清單中。
1. 從實驗室清單中，選取您**實驗室**。
1. 選取 **組態和原則**中**設定**左側功能表上的一節。
1. 選取 **共用映像資源庫**下方**虛擬機器基底**左側功能表上。

    ![共用映像組件庫功能表](./media/configure-shared-image-gallery/shared-image-galleries-menu.png)
1. 附加至您的實驗室的現有共用的映像庫上即可**附加**按鈕和下拉式清單中選取您的資源庫。

    ![連結](./media/configure-shared-image-gallery/attach-options.png)
1. 移至連結的資源庫並設定您要的資源庫**啟用或停用**共用映像來建立 VM。

    ![啟用或停用](./media/configure-shared-image-gallery/enable-disable.png)
1. 實驗室使用者接著可以建立虛擬機器上的 使用已啟用的映像 **+ 新增**並找出中的映像**選擇您的基底**頁面。

    ![實驗室使用者](./media/configure-shared-image-gallery/lab-users.png)
## <a name="use-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本

### <a name="attach-a-shared-image-gallery-to-your-lab"></a>將共用的映像庫連結至您的實驗室
如果您使用 Azure Resource Manager 範本將共用映像資源庫新增至您的實驗室，您需要將它加入您的 Resource Manager 範本的資源區段下，在下列範例所示：

```json
"resources": [
{
    "apiVersion": "2018-10-15-preview",
    "type": "Microsoft.DevTestLab/labs",
    "name": "[parameters('newLabName')]",
    "location": "[resourceGroup (). location]",
    "resources": [
    {
        "apiVersion": "2018-10-15-preview",
        "name": "[variables('labVirtualNetworkName')]",
        "type": "virtualNetworks",
        "dependsOn": [
            "[resourceId('Microsoft.DevTestLab/labs', parameters('newLabName'))]"
        ]
    },
    {
        "apiVersion":"2018-10-15-preview",
        "name":"myGallery",
        "type":"sharedGalleries",
        "properties": {
            "galleryId":"[parameters('existingSharedGalleryId')]",
            "allowAllImages": "Enabled"
        },
        "dependsOn":[
            "[resourceId('Microsoft.DevTestLab/labs', parameters('newLabName'))]"
        ]
    }
    ]
} 

```

如需完整的 Resource Manager 範本範例，請參閱我們公用 GitHub 存放庫中的這些 Resource Manager 範本範例：[設定共用的映像庫時建立實驗室](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-lab-shared-gallery-configured)。

### <a name="create-a-vm-using-an-image-from-the-shared-image-gallery"></a>使用共用的映像庫的映像建立 VM
如果您使用 Azure Resource Manager 範本來建立虛擬機器使用共用的映像資源庫映像，請使用下列範例：

```json

"resources": [
{
    "apiVersion": "2018-10-15-preview",
    "type": "Microsoft.DevTestLab/labs/virtualMachines",
    "name": "[variables('resourceName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "sharedImageId": "[parameters('existingSharedImageId')]",
        "size": "[parameters('newVMSize')]",
        "isAuthenticationWithSshKey": false,
        "userName": "[parameters('userName')]",
        "sshKey": "",
        "password": "[parameters('password')]",
        "labVirtualNetworkId": "[variables('labVirtualNetworkId')]",
        "labSubnetName": "[variables('labSubnetName')]"
    }
}
],

```

若要深入了解，請參閱我們的公用 GitHub 上的這些 Resource Manager 範本範例。
[建立虛擬機器使用共用的映像資源庫映像](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-vm-username-pwd-sharedimage)。

## <a name="use-api"></a>使用 API

- 使用 API 版本 2018年-10-15-preview。
- 若要附加您的資源庫，請傳送要求，如下列程式碼片段所示：
    
    ``` 
    PUT [Lab Resource Id]/SharedGalleries/[newGalleryName]
    Body: 
    {
        “properties”:{
            “galleryId”: “[Shared Image Gallery resource Id]”,
            “allowAllImages”:”Enabled”
        }
    }
    ```
- 若要檢視您共用的映像庫中的所有映像，您可以列出所有共用的映像，以及由其資源識別碼

    ```
    GET [Lab Resource Id]/SharedGalleries/mySharedGallery/SharedImages
    ````
- 若要建立使用共用映像的 VM，您可以進行虛擬機器和虛擬機器內容中的 PUT 作業，傳遞您先前的呼叫中取得的共用映像的識別碼。 屬性。SharedImageId


## <a name="next-steps"></a>後續步驟
在成品，請參閱下列文章：

- [指定必要的成品，您的實驗室](devtest-lab-mandatory-artifacts.md)
- [建立自訂構件](devtest-lab-artifact-author.md)
- [將構件儲存機制加入實驗室](devtest-lab-artifact-author.md)
- [診斷構件失敗](devtest-lab-troubleshoot-artifact-failure.md)
