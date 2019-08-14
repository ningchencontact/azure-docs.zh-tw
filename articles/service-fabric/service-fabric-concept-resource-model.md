---
title: Azure Service Fabric 應用程式資源模型 |Microsoft Docs
description: 本文概述如何使用 Azure Resource Manager 管理 Azure Service Fabric 應用程式
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: atsenthi
ms.openlocfilehash: 7795612d8aa4974bc640571d49ad1520e2a0f94c
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2019
ms.locfileid: "68963847"
---
# <a name="what-is-the-service-fabric-application-resource-model"></a>什麼是 Service Fabric 應用程式資源模型？
建議您透過 Azure Resource Manager, 將 Service Fabric 應用程式部署到您的 Service Fabric 叢集。 這個方法可讓您以 JSON 描述應用程式和服務, 並將它們部署在與叢集相同的 Resource Manager 範本中。 相對於透過 PowerShell 或 Azure CLI 來部署和管理應用程式, 不需要等待叢集準備就緒。 應用程式註冊、布建和部署的流程, 都可以在單一步驟中進行。 這是在您的叢集中管理應用程式生命週期的最佳作法。 如需詳細資訊, 請參閱[最佳做法](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code#azure-service-fabric-resources)。

當可行時，將應用程式視為 Resource Manager 資源來管理有助於改善：
* 審核記錄:Resource Manager 會針對每個作業進行審核, 並保留詳細的*活動記錄*, 協助您追蹤對這些應用程式和叢集所做的任何變更。
* 以角色為基礎的存取控制:管理叢集的存取權, 以及部署在叢集上的應用程式, 都可以透過相同的 Resource Manager 範本來完成。
* Azure Resource Manager (透過 Azure 入口網站) 會成為管理叢集和重要應用程式部署的一站。

## <a name="service-fabric-application-life-cycle-with-azure-resource-manager"></a>使用 Azure Resource Manager Service Fabric 應用程式生命週期 
在本檔中, 您將瞭解如何:

> [!div class="checklist"]
> * 使用 Azure Resource Manager 部署應用程式資源 
> * 使用 Azure Resource Manager 升級應用程式資源
> * 刪除應用程式資源

## <a name="deploy-application-resources-using-azure-resource-manager"></a>使用 Azure Resource Manager 部署應用程式資源  
若要使用 Azure Resource Manager 的應用程式資源模型來部署應用程式及其服務, 您需要封裝應用程式程式碼、上傳套件, 然後在 Azure Resource Manager 範本中以應用程式的形式參考套件的位置resource. 如需詳細資訊, 請參閱[封裝應用程式](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps#create-an-sfpkg)。
          
然後, 建立 Azure Resource Manager 範本、使用應用程式詳細資料更新參數檔案, 然後將它部署在 Service Fabric 叢集上。 請參閱這裡的範例

### <a name="create-a-storage-account"></a>建立儲存體帳戶 
從 Resource Manager 範本部署應用程式時, 需要有儲存體帳戶才能暫存應用程式映射。 您可以重複使用現有的儲存體帳戶, 或建立新的儲存體帳戶來暫存您的應用程式。 如果您想要使用現有的儲存體帳戶, 您可以略過此步驟。 

![建立儲存體帳戶][CreateStorageAccount]

### <a name="configure-storage-account"></a>設定儲存體帳戶 
建立儲存體帳戶之後, 您必須建立可暫存應用程式的 blob 容器。 在 Azure 入口網站中, 流覽至您想要儲存應用程式的儲存體帳戶。 選取 [ **blob** ] 分頁, 然後按一下 [**新增容器**] 按鈕。 新增具有 Blob 公用存取層級的新容器。
   
![建立 Blob][CreateBlob]

### <a name="stage-application-in-a-storage-account"></a>在儲存體帳戶中暫存應用程式
在部署應用程式之前, 必須先暫存在 blob 儲存體中。 在本教學課程中, 我們將手動建立應用程式封裝, 但此步驟可以自動化。  如需詳細資訊, 請參閱[封裝應用程式](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps#create-an-sfpkg)。 在下列步驟中, 會使用[投票範例應用程式](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart)。

1. 在 Visual Studio 以滑鼠右鍵按一下投票專案, 然後選取 [封裝]。   
![封裝應用程式][PackageApplication]  
2. 開啟剛建立的 **.\service-fabric-dotnet-quickstart\Voting\pkg\Debug**目錄, 並將內容壓縮成一個名為**表決**的檔案, 讓 ApplicationManifest 位於 zip 檔案的根目錄。  
![Zip 應用程式][ZipApplication]  
3. 將檔案的副檔名從 .zip 重新命名為 **. sfpkg**。
4. 在 Azure 入口網站的儲存體帳戶的 **應用程式** 容器中, 按一下 **上傳** 和 上傳**sfpkg**。  
![上傳應用程式套件][UploadAppPkg]

現在會暫存應用程式。 我們現在已準備好建立 Azure Resource Manager 範本來部署應用程式。      
   
### <a name="create-the-azure-resource-manager-template"></a>建立 Azure Resource Manager 範本
範例應用程式包含可用來部署應用程式的[Azure Resource Manager 範本](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM)。 範本檔案會命名為**UserApp**和**UserApp**。 

> [!NOTE] 
> **UserApp**必須使用您的叢集名稱來更新。
>
>

| 參數              | 說明                                 | 範例                                                      | 註解                                                     |
| ---------------------- | ------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| clusterName            | 您正在部署的叢集名稱 | sf-cluster123                                                |                                                              |
| 應用程式            | 應用程式的名稱                 | 投票                                                       |
| applicationTypeName    | 應用程式的類型名稱           | VotingType                                                   | 必須符合 ApplicationManifest 中的內容                 |
| applicationTypeVersion | 應用程式類型的版本         | 1.0.0                                                        | 必須符合 ApplicationManifest 中的內容                 |
| serviceName            | 服務的服務名稱         | 投票 ~ VotingWeb                                             | 的格式必須為 ApplicationName ~ ServiceType            |
| serviceTypeName        | 服務的類型名稱                | VotingWeb                                                    | 必須符合 ServiceManifest 中的內容                 |
| appPackageUrl          | 應用程式的 blob 儲存體 URL     | https://servicefabricapps.blob.core.windows.net/apps/Voting.sfpkg | Blob 儲存體中應用程式封裝的 URL (設定此項的程式如下所述) |
       
```json
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

### <a name="deploy-the-application"></a>部署應用程式 
若要部署應用程式, 請執行 New-azresourcegroupdeployment 以部署至包含您叢集的資源群組。
```powershell
New-AzResourceGroupDeployment -ResourceGroupName "sf-cluster-rg" -TemplateParameterFile ".\UserApp.Parameters.json" -TemplateFile ".\UserApp.json" -Verbose
```

## <a name="upgrade-service-fabric-application-using-azure-resource-manager"></a>使用 Azure Resource Manager 升級 Service Fabric 應用程式
已部署到 Service Fabric 叢集的應用程式將會升級, 原因如下:

1. 新的服務會新增至應用程式。 服務定義必須加入至 service-manifest 和 application-manifest。 然後, 若要反映應用程式的新版本, 您必須將應用程式類型版本從1.0.0 更新為 1.0.1 [UserApp](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/blob/master/ARM/UserApp.Parameters.json)。

    ```
    "applicationTypeVersion": {
        "value": "1.0.1"
    },
    "serviceName2": {
        "value": "Voting~VotingData"
    },
    "serviceTypeName2": {
        "value": "VotingDataType"
    }
    ```
2. 現有服務的新版本會新增至應用程式。 這牽涉到應用程式程式碼的變更, 以及應用程式類型版本和名稱的更新。

    ```
     "applicationTypeVersion": {
        "value": "1.0.1"
    },
    ```

## <a name="delete-application-resources"></a>刪除應用程式資源
使用 Azure Resource Manager 中的應用程式資源模型所部署的應用程式, 可以使用下列步驟從叢集刪除

1) 使用[get-azresource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource?view=azps-2.5.0)命令取得應用程式的資源識別碼  

    #### <a name="get-resource-id-for-application"></a>取得應用程式的資源識別碼
    ```
    Get-AzResource  -Name <String> | f1
    ```
2) 使用[移除-get-azresource](https://docs.microsoft.com/powershell/module/az.resources/remove-azresource?view=azps-2.5.0)刪除應用程式資源  

    #### <a name="delete-application-resource-using-its-resource-id"></a>使用資源識別碼來刪除應用程式資源
    ```
    Remove-AzResource  -ResourceId <String> [-Force] [-ApiVersion <String>]
    ```

## <a name="next-steps"></a>後續步驟
取得應用程式資源模型的相關資訊:

* [在 Service Fabric 中模型化應用程式](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-model)
* [Service Fabric 應用程式和服務資訊清單](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-and-service-manifests)

<!--Image references-->
[CreateStorageAccount]: ./media/service-fabric-application-model/create-storage-account.png
[CreateBlob]: ./media/service-fabric-application-model/create-blob.png
[PackageApplication]: ./media/service-fabric-application-model/package-application.png
[ZipApplication]: ./media/service-fabric-application-model/zip-application.png
[UploadAppPkg]: ./media/service-fabric-application-model/upload-app-pkg.png