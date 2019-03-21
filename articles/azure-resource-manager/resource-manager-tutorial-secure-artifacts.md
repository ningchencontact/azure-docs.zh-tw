---
title: 保護 Azure Resource Manager 範本部署中的成品 | Microsoft Docs
description: 了解如何保護 Azure Resource Manager 範本中使用的成品。
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 02/25/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: f7f235ce709fd81c4bb4c367774b4a96cd920e13
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/19/2019
ms.locfileid: "58120341"
---
# <a name="tutorial-secure-artifacts-in-azure-resource-manager-template-deployments"></a>教學課程：保護 Azure Resource Manager 範本部署中的成品

了解如何使用 Azure 儲存體帳戶搭配共用存取簽章 (SAS)，保護 Azure Resource Manager 範本使用的成品。 除了完成部署所需的主要範本檔案以外，部署成品可以是任何檔案。 例如，在[教學課程：使用 Azure Resource Manager 範本匯入 SQL BACPAC 檔案](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md)中，主要範本會建立 Azure SQL Database，它也會呼叫 BACPAC 檔案以建立資料表及插入資料。 BACPAC 檔案是成品。 該成品會儲存在具有公用存取權的 Azure 儲存體帳戶中。 在本教學課程中，您會使用 SAS 在您自己的 Azure 儲存體帳戶中授與 BACPAC 檔案的有限存取權。 如需關於 SAS 的詳細資訊，請參閱[使用共用存取簽章 (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md)。

若要了解如何保護連結的範本，請參閱[教學課程：建立連結的 Azure Resource Manager 範本](./resource-manager-tutorial-create-linked-templates.md)。

本教學課程涵蓋下列工作：

> [!div class="checklist"]
> * 準備 BACPAC 檔案
> * 開啟現有範本
> * 編輯範本
> * 部署範本
> * 驗證部署

如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

若要完成本文，您需要：

* [Visual Studio Code](https://code.visualstudio.com/) 搭配 Resource Manager Tools 擴充功能。 請參閱[安裝延伸模組](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)。
* 檢閱[本教學課程：使用 Azure Resource Manager 範本匯入 SQL BACPAC 檔案](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md)。 本教學課程中使用的範本就是該教學課程中開發的範本。 本文會提供完整範本的下載連結。
* 為了提高安全性，請使用為 SQL Server 系統管理員帳戶產生的密碼。 以下是用於產生密碼的範例：

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Azure Key Vault 的設計訴求是保護加密金鑰和其他祕密。 如需詳細資訊，請參閱[教學課程：在 Resource Manager 範本部署中整合 Azure Key Vault](./resource-manager-tutorial-use-key-vault.md)。 我們也建議您每三個月更新一次密碼。

## <a name="prepare-a-bacpac-file"></a>準備 BACPAC 檔案

在這一節中，您會準備 BACPAC 檔案，以便在部署 Resource Manager 範本時安全地存取該檔案。 本節有五個程序︰

* 下載 BACPAC 檔案。
* 建立 Azure 儲存體帳戶。
* 建立儲存體帳戶 Blob 容器。
* 將 BACPAC 檔案上傳至容器。
* 擷取 BACPAC 檔案的 SAS 權杖。

若要使用 PowerShell 指令碼自動執行這些步驟，請參閱[上傳連結的範本](./resource-manager-tutorial-create-linked-templates.md#upload-the-linked-template)中的指令碼。

### <a name="download-the-bacpac-file"></a>下載 BACPAC 檔案

下載 [BACPAC 檔案](https://armtutorials.blob.core.windows.net/sqlextensionbacpac/SQLDatabaseExtension.bacpac)，並使用相同名稱 **SQLDatabaseExtension.bacpac**，將此檔案儲存到本機電腦。

### <a name="create-a-storage-account"></a>建立儲存體帳戶

1. 在 Azure 入口網站中，選取以下影像以開啟 Resource Manager 範本。

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-storage-account-create%2fazuredeploy.json" target="_blank"><img src="./media/resource-manager-tutorial-secure-artifacts/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. 輸入下列屬性：

    * 訂用帳戶：選取 Azure 訂用帳戶。
    * **資源群組**：選取 [新建] 並且為它命名。 資源群組是 Azure 資源的容器，主要用於管理。 在本教學課程中，您可以對儲存體帳戶和 Azure SQL Database 使用相同的資源群組。 請記下此資源群組名稱，您稍後在教學課程中建立 Azure SQL Database 時需要用到它。
    * **位置**：選取區域。 例如，**美國中部**。 
    * **儲存體帳戶類型**：使用預設值，亦即 **Standard_LRS**。
    * **位置**：使用預設值，亦即 **[resourceGroup().location]**。 這表示您使用儲存體帳戶的資源群組位置。
    * **我同意上方所述的條款及條件**：(已選取)
3. 選取 [購買]。
4. 選取入口網站右上角的通知圖示 (鈴鐺圖示)，以查看部署狀態。

    ![Resource Manager 教學課程的入口網站通知窗格](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-portal-notifications-pane.png)
5. 成功部署儲存體帳戶之後，請選取 [通知] 窗格中的 [移至資源群組]，以開啟資源群組。

### <a name="create-a-blob-container"></a>建立 Blob 容器

需要有 Blob 容器，您才可以上傳任何檔案。 

1. 選取要開啟的儲存體帳戶。 您應該只會看到資源群組中所列的一個儲存體帳戶。 您的儲存體帳戶名稱與下列螢幕擷取畫面中顯示的名稱不同。

    ![Resource Manager 教學課程的儲存體帳戶](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-storage-account.png)

2. 選取 [Blob] 圖格。

    ![Resource Manager 教學課程的 Blob](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-blobs.png)
3. 選取頂端的 [+ 容器] 以建立新的容器。
4. 輸入下列值：

    * **名稱**：輸入 **sqlbacpac**。 
    * **公用存取層級**：使用預設值 [私人 (沒有匿名存取)]。
5. 選取 [確定] 。
6. 選取 **sqlbacpac** 以開啟新建立的容器。

### <a name="upload-the-bacpac-file-to-the-container"></a>將 BACPAC 檔案上傳至容器

1. 選取 [上傳] 。
2. 輸入下列值：

    * **檔案**：遵循指示來選取您稍早下載的 BACPAC 檔案。 預設名稱是 **SQLDatabaseExtension.bacpac**。
    * **驗證類型**：選取 [SAS]。  [SAS] 是預設值。
3. 選取 [上傳] 。  成功上傳檔案後，檔案名稱應會列在容器中。

### <a name="a-namegenerate-a-sas-token-generate-a-sas-token"></a><a name="generate-a-sas-token" />產生 SAS 權杖

1. 以滑鼠右鍵按一下容器中的 **SQLDatabaseExtension.bacpac**，然後選取 [產生 SAS]。
2. 輸入下列值：

    * **權限**：使用預設值 [讀取]。
    * **開始與到期日期/時間**：預設值讓您可使用 SAS 權杖 8 小時。 如果您需要更多時間才能完成本教學課程，請更新 [到期日]。
    * **允許的 IP 位址**：將此欄位保留空白。
    * **允許的通訊協定**：使用預設值：**HTTPS**。
    * **簽署金鑰**：使用預設值：**金鑰 1**。
3. 選取 [產生 Blob SAS 權杖和 URL]。
4. 複製 [Blob SAS URL]。 URL 的中間是檔案名稱 **SQLDatabaseExtension.bacpac**。  此檔案名稱將 URL 分成三個部分：

   - **成品位置**： https://xxxxxxxxxxxxxx.blob.core.windows.net/sqlbacpac/。 確定位置是以 "/" 結尾。
   - **BACPAC 檔案名稱**：SQLDatabaseExtension.bacpac。
   - **成品位置 SAS 權杖**：確定權杖的前面加上 "?"。

     您在[部署範本](#deploy-the-template)中需要這三個值。

## <a name="open-an-existing-template"></a>開啟現有範本

在這個課程中，您會修改在[教學課程：使用 Azure Resource Manager 範本匯入 SQL BACPAC 檔案](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md)中建立的範本，以呼叫具有 SAS 權杖的 BACPAC 檔案。  在 SQL 擴充功能教學課程中開發的範本會在 [https://armtutorials.blob.core.windows.net/sqlextensionbacpac/azuredeploy.json](https://armtutorials.blob.core.windows.net/sqlextensionbacpac/azuredeploy.json) 共用。

1. 在 Visual Studio Code 中，選取 [檔案]>[開啟檔案]。
2. 在 [檔案名稱] 中，貼上下列 URL：

    ```url
    https://armtutorials.blob.core.windows.net/sqlextensionbacpac/azuredeploy.json
    ```
3. 選取 [開啟] 以開啟檔案。

    範本中定義了五個資源：

   * `Microsoft.Sql/servers` 。 請參閱[範本參考](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers)。
   * `Microsoft.SQL/servers/securityAlertPolicies` 。 請參閱[範本參考](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/securityalertpolicies)。
   * `Microsoft.SQL/servers/filewallRules` 。 請參閱[範本參考](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers/firewallrules)。
   * `Microsoft.SQL/servers/databases` 。  請參閱[範本參考](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases)。
   * `Microsoft.SQL/server/databases/extensions` 。  請參閱[範本參考](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions)。

     自訂範本之前，最好能初步了解範本。
4. 選取 [檔案]>[另存新檔]，以名稱 **azuredeploy.json** 將檔案的複本儲存至您的本機電腦。

## <a name="edit-the-template"></a>編輯範本

新增下列額外參數：

```json
"_artifactsLocation": {
    "type": "string",
    "metadata": {
        "description": "The base URI where artifacts required by this template are located."
    }
},
"_artifactsLocationSasToken": {
    "type": "securestring",
    "metadata": {
        "description": "The sasToken required to access _artifactsLocation."
    },
    "defaultValue": ""
},
"bacpacFileName": {
    "type": "string",
    "defaultValue": "SQLDatabaseExtension.bacpac",
    "metadata": {
        "description": "The bacpac for configure the database and tables."
    }
}
```

![Resource Manager 教學課程的安全成品參數](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-secure-artifacts-parameters.png)

更新下列兩個元素的值：

```json
"storageKey": "[parameters('_artifactsLocationSasToken')]",
"storageUri": "[uri(parameters('_artifactsLocation'), parameters('bacpacFileName'))]",
```

## <a name="deploy-the-template"></a>部署範本

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

請參閱[部署範本](./resource-manager-tutorial-create-multiple-instances.md#deploy-the-template)一節，以了解部署程序。 改用下列 PowerShell 部署指令碼：

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the virtual machine admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$artifactsLocation = Read-Host -Prompt "Enter the artifacts location"
$artifactsLocationSasToken = Read-Host -Prompt "Enter the artifacts location SAS token" -AsSecureString
$bacpacFileName = Read-Host -Prompt "Enter the BACPAC file name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -_artifactsLocation $artifactsLocation `
    -_artifactsLocationSasToken $artifactsLocationSasToken `
    -bacpacFileName $bacpacFileName `
    -TemplateFile "$HOME/azuredeploy.json"
```

使用所產生的密碼。 請參閱[必要條件](#prerequisites)。
如需 _artifactsLocation、_artifactsLocationSasToken 和 bacpacFileName 的值，請參閱[產生 SAS 權杖](#generate-a-sas-token)。

## <a name="verify-the-deployment"></a>驗證部署

在入口網站中，請從新部署的資源群組中選取 SQL 資料庫。 選取 [查詢編輯器 (預覽)]，然後輸入系統管理員認證。 您應該會看到兩個已匯入資料庫中的資料表：

![Azure Resource Manager 部署 SQL 擴充功能 BACPAC](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>清除資源

不再需要 Azure 資源時，可藉由刪除資源群組來清除您所部署的資源。

1. 在 Azure 入口網站中，選取左側功能表中的 [資源群組]。
2. 在 [依名稱篩選] 欄位中輸入資源群組名稱。
3. 選取資源群組名稱。  您在資源群組中應該會看到共計六個資源。
4. 從頂端功能表中選取 [刪除資源群組]。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已使用 SAS 權杖，部署 Azure SQL Server、SQL Database 並匯入 BACPAC 檔案。 若要了解如何跨多個區域部署 Azure 資源，以及如何使用安全的部署實務，請參閱

> [!div class="nextstepaction"]
> [使用 Azure 部署管理員](./resource-manager-tutorial-deploy-vm-extensions.md)
