---
title: 使用相依資源建立 Azure Resource Manager 範本 | Microsoft Docs
description: 了解如何使用多項資源建立 Azure Resource Manager 範本，以及如何使用 Azure 入口網站加以部署
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 11/13/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: c5bf56482534a55d24d8ca043e36c39cec99b1f0
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2019
ms.locfileid: "56267524"
---
# <a name="tutorial-create-azure-resource-manager-templates-with-dependent-resources"></a>教學課程：使用相依資源建立 Azure Resource Manager 範本中使用的 Cloud Shell 部署方法

了解如何建立 Azure Resource Manager 範本以部署多項資源。  在建立範本之後，您可以從 Azure 入口網站使用 Cloud Shell 來部署範本。

在本教學課程中，您會建立儲存體帳戶、虛擬機器、虛擬網路和其他相依資源。 某些資源必須在另一項資源已存在時才能部署。 例如，在虛擬機器的儲存體帳戶和網路介面存在之前，您無法建立虛擬機器。 您可以讓一項資源相依於其他資源，以定義此關聯性。 資源管理員會評估資源之間的相依性，並依其相依順序進行部署。 如果資源並未彼此相依，Resource Manager 就會平行部署資源。 如需詳細資訊，請參閱[定義 Azure Resource Manager 範本中部署資源的順序](./resource-group-define-dependencies.md)。

本教學課程涵蓋下列工作：

> [!div class="checklist"]
> * 開啟快速入門範本
> * 瀏覽範本
> * 部署範本

如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

若要完成本文，您需要：

* [Visual Studio Code](https://code.visualstudio.com/) 搭配 Resource Manager Tools 擴充功能。  請參閱[安裝延伸模組](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)。
* 為了提高安全性，請使用為虛擬機器系統管理員帳戶產生的密碼。 以下是用於產生密碼的範例：

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Azure Key Vault 的設計訴求是保護加密金鑰和其他祕密。 如需詳細資訊，請參閱[教學課程：在 Resource Manager 範本部署中整合 Azure Key Vault](./resource-manager-tutorial-use-key-vault.md)。 我們也建議您每三個月更新一次密碼。

## <a name="open-a-quickstart-template"></a>開啟快速入門範本

Azure 快速入門範本是 Resource Manager 範本的存放庫。 您可以尋找範例範本並加以自訂，而不要從頭建立範本。 本教學課程中使用的範本名為[部署簡單的 Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/)。

1. 在 Visual Studio Code 中，選取 [檔案]>[開啟檔案]。
2. 在 [檔案名稱] 中，貼上下列 URL：

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. 選取 [開啟] 以開啟檔案。
4. 選取 [檔案]>[另存新檔]，以名稱 **azuredeploy.json** 將檔案的複本儲存至您的本機電腦。

## <a name="explore-the-template"></a>瀏覽範本

當您探索這一節中的範本時，請試著回答下列問題：

* 此範本中定義了多少個 Azure 資源？
* 其中一項資源是 Azure 儲存體帳戶。  定義是否與最後一個教學課程中使用的定義相仿？
* 您是否可找到此範本中定義的資源所適用的範本參考？
* 您是否可找到資源的相依性？

1. 在 Visual Studio Code 中摺疊元素，直到您只看到**資源**內的第一層元素和第二層元素：

    ![Visual Studio Code 的 Azure Resource Manager 範本](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code.png)

    範本中定義了五項資源：

    * `Microsoft.Storage/storageAccounts` 。 請參閱[範本參考](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts)。
    * `Microsoft.Network/publicIPAddresses` 。 請參閱[範本參考](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses)。
    * `Microsoft.Network/virtualNetworks` 。 請參閱[範本參考](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks)。
    * `Microsoft.Network/networkInterfaces` 。 請參閱[範本參考](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces)。
    * `Microsoft.Compute/virtualMachines` 。 請參閱[範本參考](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines)。

    自訂範本之前，最好能初步了解範本。

2. 展開第一項資源。 這是儲存體帳戶。 將資源定義與[範本參考](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts)相比較。

    ![Visual Studio Code 的 Azure Resource Manager 範本儲存體帳戶定義](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-storage-account-definition.png)

3. 展開第二項資源。 資源類型為 `Microsoft.Network/publicIPAddresses`。 將資源定義與[範本參考](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses)相比較。

    ![Visual Studio Code 的 Azure Resource Manager 範本公用 IP 位址定義](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-public-ip-address-definition.png)
4. 展開第四項資源。 資源類型為 `Microsoft.Network/networkInterfaces`：  

    ![Visual Studio Code 的 Azure Resource Manager 範本 dependson](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependson.png)

    dependsOn 元素可讓您定義一項資源，作為一或多項資源的相依項目。 此資源依存於其他兩項資源：

    * `Microsoft.Network/publicIPAddresses`
    * `Microsoft.Network/virtualNetworks`

5. 展開第五項資源。 此資源是虛擬機器。 它依存於其他兩項資源：

    * `Microsoft.Storage/storageAccounts`
    * `Microsoft.Network/networkInterfaces`

下圖說明此範本的資源和相依性資訊：

![Visual Studio Code 的 Azure Resource Manager 範本相依性圖表](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependency-diagram.png)

藉由指定相依性，Resource Manager 將可有效部署解決方案。 它會以平行方式部署儲存體帳戶、公用 IP 位址和虛擬網路，因為它們沒有相依性。 在公用 IP 位址和虛擬網路部署之後，會建立網路介面。 當所有其他資源皆部署後，Resource Manager 會部署虛擬機器。

## <a name="deploy-the-template"></a>部署範本

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

有許多方法可用來部署範本。  在本教學課程中，您會從 Azure 入口網站使用 Cloud Shell。

1. 登入 [Cloud Shell](https://shell.azure.com)。 
2. 從 Cloud Shell 的左上角選取 [PowerShell]，然後選取 [確認]。  在本教學課程中您會使用 PowerShell。
3. 從 Cloud Shell 中選取 [上傳檔案]：

    ![Azure 入口網站的 Cloud Shell 上傳檔案](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-upload-file.png)
4. 選取您先前在本教學課程中儲存的範本。 預設名稱為 **azuredeploy.json**。  如果有檔案具有相同的檔案名稱，將會直接覆寫舊檔案而不另行通知。
5. 從 Cloud Shell 執行下列命令，以確認已成功上傳檔案。 

    ```bash
    ls
    ```

    ![Azure 入口網站的 Cloud Shell 列出檔案](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-list-file.png)

    顯示在螢幕擷取畫面上的檔案名稱是 azuredeploy.json。

6. 從 Cloud Shell 執行下列命令，以驗證 JSON 檔案的內容：

    ```bash
    cat azuredeploy.json
    ```
7. 從 Cloud Shell 執行下列 PowerShell 命令。 為了提高安全性，請使用為虛擬機器系統管理員帳戶產生的密碼。 請參閱[必要條件](#prerequisites)。

    ```azurepowershell
    $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $adminUsername = Read-Host -Prompt "Enter the virtual machine admin username"
    $adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
    $dnsLabelPrefix = Read-Host -Prompt "Enter the DNS label prefix"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -adminUsername $adminUsername `
        -adminPassword $adminPassword `
        -dnsLabelPrefix $dnsLabelPrefix `
        -TemplateFile azuredeploy.json
    ```

    > [!NOTE]
    > 在 Cloud shell 中使用 Azure PowerShell 時發生檔案 IO 問題。  錯誤訊息是「無法擷取此 Cmdlet 的動態參數。找不到路徑 'Azure:/azuredeploy.json'，因為該路徑不存在。  暫時的解決方法是不要在 `New-AzResourceGroupDeploy` 命令中包含 **-TemplateFile** 參數。 此命令會提示您輸入檔案名稱。

8. 執行下列 PowerShell 命令，以列出新建立的虛擬機器：

    ```azurepowershell
    $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
    Get-AzVM -Name SimpleWinVM -ResourceGroupName $resourceGroupName
    ```

    在範本內，虛擬機器名稱會硬式編碼為 **SimpleWinVM**。

9. 透過 RDP 連線至虛擬機器，以確認虛擬機器已成功建立。

## <a name="clean-up-resources"></a>清除資源

不再需要 Azure 資源時，可藉由刪除資源群組來清除您所部署的資源。

1. 在 Azure 入口網站中，選取左側功能表中的 [資源群組]。
2. 在 [依名稱篩選] 欄位中輸入資源群組名稱。
3. 選取資源群組名稱。  您在資源群組中應該會看到共計六個資源。
4. 從頂端功能表中選取 [刪除資源群組]。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已開發和部署用來建立虛擬機器、虛擬網路和相依資源的範本。 若要深入了解如何根據條件部署 Azure 資源，請參閱：

> [!div class="nextstepaction"]
> [使用條件](./resource-manager-tutorial-use-conditions.md)
