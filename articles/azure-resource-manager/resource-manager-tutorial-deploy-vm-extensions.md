---
title: 使用 Azure Resource Manager 範本部署虛擬機器擴充功能 | Microsoft Docs
description: 了解如何使用 Azure Resource Manager 範本部署虛擬機器擴充功能
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 11/13/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 2f5cffa0b31d46d76564565001aaed678eb1a649
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2018
ms.locfileid: "51613567"
---
# <a name="tutorial-deploy-virtual-machine-extensions-with-azure-resource-manager-templates"></a>教學課程：使用 Azure Resource Manager 範本部署虛擬機器擴充功能

了解如何使用 [Azure 虛擬機器擴充功能](../virtual-machines/extensions/features-windows.md)在 Azure VM 上執行部署後設定及自動化工作。 有許多不同的虛擬機器擴充功能可與 Azure 虛擬機器搭配使用。 在本教學課程中，您將從 Resource Manager 範本在 Windows VM 上執行 PowerShell 指令碼的自訂指令碼擴充功能。  指令碼會在 VM 上安裝 Web 伺服器。

本教學課程涵蓋下列工作：

> [!div class="checklist"]
> * 準備 PowerShell 指令碼
> * 開啟快速入門範本
> * 編輯範本
> * 部署範本
> * 驗證部署

如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

若要完成本文，您需要：

* [Visual Studio Code](https://code.visualstudio.com/) 搭配 Resource Manager Tools 擴充功能。  請參閱[安裝延伸模組](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)。
* 為了提高安全性，請使用為虛擬機器系統管理員帳戶產生的密碼。 以下是用於產生密碼的範例：

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Azure Key Vault 的設計訴求是保護加密金鑰和其他祕密。 如需詳細資訊，請參閱[教學課程：在 Resource Manager 範本部署中整合 Azure Key Vault](./resource-manager-tutorial-use-key-vault.md)。 我們也建議您每三個月更新一次密碼。

## <a name="prepare-a-powershell-script"></a>準備 PowerShell 指令碼

具有下列內容的 PowerShell 指令碼可從[具有公用存取權的 Azure 儲存體帳戶](https://armtutorials.blob.core.windows.net/usescriptextensions/installWebServer.ps1)共用：

```azurepowershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

如果您選擇將檔案發佈到自己的位置，您稍後必須在本教學課程中更新範本中的 [fileUri] 元素。

## <a name="open-a-quickstart-template"></a>開啟快速入門範本

Azure 快速入門範本是 Resource Manager 範本的存放庫。 您可以尋找範例範本並加以自訂，而不要從頭建立範本。 本教學課程中使用的範本名為[部署簡單的 Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/)。

1. 在 Visual Studio Code 中，選取 [檔案]>[開啟檔案]。
2. 在 [檔案名稱] 中，貼上下列 URL：

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. 選取 [開啟] 以開啟檔案。
4. 範本中定義了五項資源：

    * `Microsoft.Storage/storageAccounts` 。 請參閱[範本參考](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts)。
    * `Microsoft.Network/publicIPAddresses` 。 請參閱[範本參考](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses)。
    * `Microsoft.Network/virtualNetworks` 。 請參閱[範本參考](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks)。
    * `Microsoft.Network/networkInterfaces` 。 請參閱[範本參考](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces)。
    * `Microsoft.Compute/virtualMachines` 。 請參閱[範本參考](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines)。

    自訂範本之前，最好能初步了解範本。
5. 選取 [檔案]>[另存新檔]，以名稱 **azuredeploy.json** 將檔案的複本儲存至您的本機電腦。

## <a name="edit-the-template"></a>編輯範本

使用下列內容，將虛擬機器擴充功能資源新增到現有的範本：

```json
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "[concat(variables('vmName'),'/', 'InstallWebServer')]",
    "location": "[parameters('location')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/',variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.7",
        "autoUpgradeMinorVersion":true,
        "settings": {
            "fileUris": [
                "https://armtutorials.blob.core.windows.net/usescriptextensions/installWebServer.ps1"
            ],
            "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File installWebServer.ps1"
        }
    }
}
```

如果您需要此資源定義的詳細資訊，請參閱[擴充功能參考](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines/extensions)。 以下是部分重要元素：

* **name**：由於擴充功能資源是虛擬機器物件的子資源，因此名稱必須有虛擬機器名稱前置詞。 請參閱[子資源](./resource-manager-templates-resources.md#child-resources)。
* **dependsOn**：在虛擬機器建立後，才可建立擴充功能資源。
* **fileUris**：這些是指令碼檔案儲存所在的位置。 如果您選擇不使用提供的值，則必須更新值。
* **commandToExecute**：這是用來叫用指令碼的命令。  

## <a name="deploy-the-template"></a>部署範本

請參閱[部署範本](./resource-manager-tutorial-create-multiple-instances.md#deploy-the-template)一節，以了解部署程序。 建議您對虛擬機器系統管理員帳戶使用產生的密碼。 請參閱[必要條件](#prerequisites)。

## <a name="verify-the-deployment"></a>驗證部署

在入口網站中，選取 VM，然後在 VM 的概觀中，使用 IP 位址右邊的 [按一下以複製]，將它複製並貼到瀏覽器索引標籤中。預設的 IIS 歡迎使用頁面會開啟，而且應如下所示：

![Azure Resource Manager 會部署 VM 擴充功能客戶指令碼 IIS Web 伺服器](./media/resource-manager-tutorial-deploy-vm-extensions/resource-manager-template-deploy-extensions-customer-script-web-server.png)

## <a name="clean-up-resources"></a>清除資源

不再需要 Azure 資源時，可藉由刪除資源群組來清除您所部署的資源。

1. 在 Azure 入口網站中，選取左側功能表中的 [資源群組]。
2. 在 [依名稱篩選] 欄位中輸入資源群組名稱。
3. 選取資源群組名稱。  您在資源群組中應該會看到共計六個資源。
4. 從頂端功能表中選取 [刪除資源群組]。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已部署虛擬機器和虛擬機器擴充功能。 擴充功能在虛擬機器上安裝了 IIS Web 伺服器。 若要了解如何使用 SQL Database 擴充功能匯入 BACPAC 檔案，請參閱：

> [!div class="nextstepaction"]
> [](./resource-manager-tutorial-deploy-vm-extensions.md)
