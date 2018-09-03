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
ms.date: 08/27/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 7509ed46ba07cd8250f82f8eb258d18e3f4a1ee6
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "43107100"
---
# <a name="tutorial-create-azure-resource-manager-templates-with-dependent-resources"></a>教學課程：使用相依資源建立 Azure Resource Manager 範本

了解如何建立 Azure Resource Manager 範本以部署多項資源。  在建立範本之後，您可以從 Azure 入口網站使用 Cloud Shell 來部署範本。

某些資源必須在另一項資源已存在時才能部署。 例如，在虛擬機器的儲存體帳戶和網路介面存在之前，您無法建立虛擬機器。 您可以讓一項資源相依於其他資源，以定義此關聯性。 資源管理員會評估資源之間的相依性，並依其相依順序進行部署。 如果資源並未彼此相依，Resource Manager 就會平行部署資源。 如需詳細資訊，請參閱[定義 Azure Resource Manager 範本中部署資源的順序](./resource-group-define-dependencies.md)。

> [!div class="checklist"]
> * 開啟快速入門範本
> * 瀏覽範本
> * 部署範本
> * 清除資源

本教學課程中的指示會建立虛擬機器、虛擬網路和其他相依資源。 

## <a name="prerequisites"></a>必要條件

若要完成本文，您需要：

* [Visual Studio Code](https://code.visualstudio.com/)。
* Resource Manager Tools 擴充功能。  請參閱[安裝擴充功能](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)

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

- 此範本中定義了多少個 Azure 資源？
- 其中一項資源是 Azure 儲存體帳戶。  定義是否與最後一個教學課程中使用的定義相仿？
- 您是否可找到此範本中定義的資源所適用的範本參考？
- 您是否可找到資源的相依性？

1. 在 Visual Studio Code 中摺疊元素，直到您只看到**資源**內的第一層元素和第二層元素：

    ![Visual Studio Code 的 Azure Resource Manager 範本](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code.png)

    範本中定義了五項資源。
2. 展開第一項資源。 這是儲存體帳戶。 其定義應與最後一個教學課程開頭所用的定義相同。

    ![Visual Studio Code 的 Azure Resource Manager 範本儲存體帳戶定義](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-storage-account-definition.png)

3. 展開第二項資源。 其資源類型為 **Microsoft.Network/publicIPAddresses**。 若要尋找範本參考，請瀏覽至[範本參考](https://docs.microsoft.com/azure/templates/)，然後在 [依標題篩選] 欄位中輸入**公用 IP 位址**或**多個公用 IP 位址**。 將資源定義與範本參考相比較。

    ![Visual Studio Code 的 Azure Resource Manager 範本公用 IP 位址定義](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-public-ip-address-definition.png)
4. 重複上一個步驟，以尋找此範本中定義的其他資源所適用的範本參考。  將資源定義與參考相比較。
5. 展開第四項資源：

    ![Visual Studio Code 的 Azure Resource Manager 範本 dependson](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependson.png)

    dependsOn 元素可讓您定義一項資源，作為一或多項資源的相依項目。 在此範例中，此資源是 networkInterface。  它依存於其他兩項資源：

    * publicIPAddress
    * virtualNetwork

6. 展開第五項資源。 此資源是虛擬機器。 它依存於其他兩項資源：

    * storageAccount
    * networkInterface

下圖說明此範本的資源和相依性資訊：

![Visual Studio Code 的 Azure Resource Manager 範本相依性圖表](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependency-diagram.png)

藉由指定相依性，Resource Manager 將可有效部署解決方案。 它會以平行方式部署儲存體帳戶、公用 IP 位址和虛擬網路，因為它們沒有相依性。 在公用 IP 位址和虛擬網路部署之後，會建立網路介面。 當所有其他資源皆部署後，Resource Manager 會部署虛擬機器。

## <a name="deploy-the-template"></a>部署範本

有許多方法可用來部署範本。  在本教學課程中，您會從 Azure 入口網站使用 Cloud Shell。

1. 登入 [Azure 入口網站](https://portal.azure.com)
2. 選取位於右上角的 [Cloud Shell]，如下圖所示：

    ![Azure 入口網站的 Cloud Shell](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell.png)
3. 從 Cloud Shell 的左上角選取 [PowerShell]。  在本教學課程中您會使用 PowerShell。
4. 選取 [重新啟動]
5. 從 Cloud Shell 中選取 [上傳檔案]：

    ![Azure 入口網站的 Cloud Shell 上傳檔案](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-upload-file.png)
6. 選取您先前在本教學課程中儲存的檔案。 預設名稱為 **azuredeploy.json**。  如果有檔案具有相同的檔案名稱，將會直接覆寫舊檔案而不另行通知。
7. 從 Cloud Shell 執行下列命令，以確認已成功上傳檔案。 

    ```shell
    ls
    ```

    ![Azure 入口網站的 Cloud Shell 列出檔案](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-list-file.png)

    顯示在螢幕擷取畫面上的檔案名稱是 azuredeploy.json。

8. 從 Cloud Shell 執行下列命令，以驗證 JSON 檔案的內容：

    ```shell
    cat azuredeploy.json
    ```
9. 從 Cloud Shell 執行下列 PowerShell 命令︰

    ```powershell
    $resourceGroupName = "<Enter the resource group name>"
    $location = "<Enter the Azure location>"
    $vmAdmin = "<Enter the admin username>"
    $vmPassword = "<Enter the password>"
    $dnsLabelPrefix = "<Enter the prefix>"

    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    $vmPW = ConvertTo-SecureString -String $vmPassword -AsPlainText -Force
    New-AzureRmResourceGroupDeployment -Name mydeployment0710 -ResourceGroupName $resourceGroupName `
        -TemplateFile azuredeploy.json -adminUsername $vmAdmin -adminPassword $vmPW `
        -dnsLabelPrefix $dnsLabelPrefix
    ```
    以下是範例部署的螢幕擷取畫面：

    ![Azure 入口網站的 Cloud Shell 部署範本](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-deploy-template.png)

    在螢幕擷取畫面上，會使用下列值：

    * **$resourceGroupName**：myresourcegroup0710。 
    * **$location**：eastus2
    * **&lt;DeployName>**：mydeployment0710
    * **&lt;TemplateFile>**：azuredeploy.json
    * **範本參數**：

        * **adminUsername**：JohnDole
        * **adminPassword**：Pass@word123
        * **dnsLabelPrefix**：myvm0710

10. 執行下列 PowerShell 命令，以列出新建立的虛擬機器：

    ```powershell
    Get-AzureRmVM -Name SimpleWinVM -ResourceGroupName <ResourceGroupName>
    ```

    在範本內，虛擬機器名稱會硬式編碼為 **SimpleWinVM**。

## <a name="clean-up-resources"></a>清除資源

不再需要 Azure 資源時，可藉由刪除資源群組來清除您所部署的資源。

1. 在 Azure 入口網站中，選取左側功能表中的 [資源群組]。
2. 在 [依名稱篩選] 欄位中輸入資源群組名稱。
3. 選取資源群組名稱。  您在資源群組中應該會看到共計六個資源。
4. 從頂端功能表中選取 [刪除資源群組]。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您會開發和部署用來建立虛擬機器、虛擬網路和相依資源的範本。 若要深入了解範本，請參閱[了解 Azure Resource Manager 範本的結構和語法](./resource-group-authoring-templates.md)。