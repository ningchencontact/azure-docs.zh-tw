---
title: 遷移至叢集設定的細微角色型存取-Azure HDInsight
description: 瞭解遷移至 HDInsight 叢集設定的細微角色型存取所需的變更。
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: ebb1723a9a2b2d069a1766d4f78151f2b684c5b9
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2019
ms.locfileid: "68464671"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>移轉至叢集組態中以角色為基礎的細微存取

我們即將介紹一些重要的變更, 以支援更精細的角色型存取來取得機密資訊。 做為這些變更的一部分, 如果您使用其中一個[受影響的實體/案例](#am-i-affected-by-these-changes),**可能會需要**執行某些動作。

## <a name="what-is-changing"></a>變更內容為何？

先前, 具有「擁有者」、「參與者」或「讀者」 [RBAC 角色](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)的叢集使用者, 可以透過 HDInsight API 取得秘密, 因為他們可供`*/read`具備該許可權的任何人使用。
接下來, 存取這些密碼將需要`Microsoft.HDInsight/clusters/configurations/*`許可權, 這表示他們無法再由具有「讀取者」角色的使用者存取。 密碼會定義為值, 可用來取得比使用者角色應允許更高的存取權。 其中包括叢集閘道 HTTP 認證、儲存體帳戶金鑰和資料庫認證之類的值。

我們也引進了新的[HDInsight 叢集操作員](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator)角色, 將能夠在不被授與參與者或擁有者的系統管理許可權的情況下, 取得密碼。 總結：

| Role                                  | 先前                                                                                       | 往後       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| 讀者                                | -讀取權限, 包括秘密                                                                   | -讀取權限, 不**包括**秘密 |           |   |   |
| HDInsight 叢集操作員<br>(新角色) | N/A                                                                                              | -讀取/寫入存取權, 包括秘密         |   |   |
| 參與者                           | -讀取/寫入存取權, 包括秘密<br>-建立和管理所有類型的 Azure 資源。     | 沒有變更 |
| 擁有者                                 | -讀取/寫入存取權, 包括秘密<br>-所有資源的完整存取權<br>-將存取權委派給其他人 | 沒有變更 |

如需如何將 HDInsight 叢集操作員角色指派新增至使用者以授與對叢集密碼之讀取/寫入存取權的詳細資訊, 請參閱下一節[將 hdinsight 叢集操作員角色指派新增至使用者](#add-the-hdinsight-cluster-operator-role-assignment-to-a-user)。

## <a name="am-i-affected-by-these-changes"></a>我是否受到這些變更的影響？

下列實體和案例會受到影響:

- [API](#api)：使用或`/configurations` `/configurations/{configurationName}`端點的使用者。
- [適用于 Visual Studio Code](#azure-hdinsight-tools-for-visual-studio-code) 1.1.1 版或以下版本的 Azure HDInsight 工具。
- [Azure Toolkit for IntelliJ](#azure-toolkit-for-intellij)版本3.20.0 或以下。
- [Azure Data Lake 和串流分析適用于 Visual Studio 版本2.3.9000.1 的工具](#azure-data-lake-and-stream-analytics-tools-for-visual-studio)。
- [Azure Toolkit for Eclipse](#azure-toolkit-for-eclipse)版本3.15.0 或以下。
- [適用于 .NET 的 SDK](#sdk-for-net)
    - 1\.x[或2.x 版](#versions-1x-and-2x):使用 ConfigurationsOperationsExtensions 類別`GetClusterConfigurations`的`GetConnectivitySettings` `ConfigureHttpSettings`、 、或`DisableHttp`方法的使用者。 `EnableHttp`
    - 2\.x[版和更新版本](#versions-3x-and-up):`Get` `Update`使用類別的`EnableHttp`、、或`DisableHttp`方法的使用者。 `ConfigurationsOperationsExtensions`
- [適用于 Python 的 SDK](#sdk-for-python):使用`ConfigurationsOperations`類別的`get`或`update`方法的使用者。
- [適用于 JAVA 的 SDK](#sdk-for-java):使用`ConfigurationsInner`類別的`update`或`get`方法的使用者。
- [適用于 Go 的 SDK](#sdk-for-go):使用來自`Get` 結構`ConfigurationsClient`之`Update`或方法的使用者。
- [Az. HDInsight PowerShell](#azhdinsight-powershell)版本2.0.0。
請參閱下節 (或使用上方連結) 以了解案例的移轉步驟。

### <a name="api"></a>API

下列 Api 將會變更或淘汰:

- [**取得/configurations/{configurationName}** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration)(已移除敏感性資訊)
    - 先前用來取得個別的設定類型 (包括密碼)。
    - 此 API 呼叫現在會傳回個別設定類型, 並省略密碼。 若要取得所有設定 (包括秘密), 請使用新的 POST/configurations 呼叫。 若只要取得閘道設定, 請使用新的 POST/getGatewaySettings 呼叫。
- [**取得/configurations**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration)不再
    - 先前用來取得所有設定 (包括秘密)
    - 此 API 呼叫將不再受到支援。 若要繼續進行所有設定, 請使用新的 POST/configurations 呼叫。 若要取得已省略敏感性參數的設定, 請使用 GET/configurations/{configurationName} 呼叫。
- [**張貼/configurations/{configurationName}** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings)不再
    - 先前用來更新閘道認證的。
    - 此 API 呼叫將會被取代, 而且不再支援。 請改用新的文章/updateGatewaySettings。

已新增下列取代 Api:</span>

- [**張貼/configurations**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#list-configurations)
    - 使用此 API 來取得所有設定, 包括秘密。
- [**POST /getGatewaySettings**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-gateway-settings)
    - 使用此 API 來取得閘道設定。
- [**POST /updateGatewaySettings**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings)
    - 使用此 API 來更新閘道設定 (使用者名稱和/或密碼)。

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>適用于 Visual Studio Code 的 Azure HDInsight 工具

如果您使用的版本為1.1.1 或以下, 請更新為[Visual Studio Code 的最新版本 Azure HDInsight 工具](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false), 以避免中斷。

### <a name="azure-toolkit-for-intellij"></a>Azure Toolkit for IntelliJ

如果您使用的是 [版本 3.20.0] 或以下, 請更新至[最新版本的 Azure Toolkit for IntelliJ 外掛程式](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij)以避免中斷。

### <a name="azure-data-lake-and-stream-analytics-tools-for-visual-studio"></a>適用于 Visual Studio 的 Azure Data Lake 和串流分析工具

Azure Data Lake 的版本2.3.9000.1 或更新版本[, 以及 Visual Studio 的串流分析工具](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools&ssr=false#overview), 以避免中斷。  如需更新的協助, 請參閱我們[的檔: 更新 Visual Studio 的 Data Lake 工具](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-visual-studio-tools-get-started#update-data-lake-tools-for-visual-studio)。

### <a name="azure-toolkit-for-eclipse"></a>適用於 Eclipse 的 Azure 工具組

如果您使用的是 [版本 3.15.0] 或以下, 請更新至[最新版本的 Azure Toolkit for Eclipse](https://marketplace.eclipse.org/content/azure-toolkit-eclipse)以避免中斷。

### <a name="sdk-for-net"></a>適用於 .NET 的 SDK

#### <a name="versions-1x-and-2x"></a>1\.x 和2.x 版

更新為適用于 .NET 的 HDInsight SDK[版本 2.1.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/2.1.0) 。 如果您使用受這些變更影響的方法, 可能需要最少的程式碼修改:

- `ClusterOperationsExtensions.GetClusterConfigurations`將不會再傳回如儲存體金鑰 (核心網站) 或 HTTP 認證 (閘道) 之類的**敏感參數**。
    - 若要取出所有設定 (包括機密參數) `ClusterOperationsExtensions.ListConfigurations` , 請使用 [繼續]。  請注意, 具有「讀取者」角色的使用者將無法使用這個方法。 這可讓您更精確地控制哪些使用者可以存取叢集的機密資訊。
    - 若只要取得 HTTP 閘道認證, `ClusterOperationsExtensions.GetGatewaySettings`請使用。

- `ClusterOperationsExtensions.GetConnectivitySettings`現在已被取代, 並已由`ClusterOperationsExtensions.GetGatewaySettings`取代。

- `ClusterOperationsExtensions.ConfigureHttpSettings`現在已被取代, 並已由`ClusterOperationsExtensions.UpdateGatewaySettings`取代。

- `ConfigurationsOperationsExtensions.EnableHttp`和`DisableHttp`現在已被取代。 HTTP 現在一律會啟用, 因此不再需要這些方法。

#### <a name="versions-3x-and-up"></a>3\.x 版和更新版本

更新為適用于 .NET 的 HDInsight SDK 第[5.0.0 版](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/5.0.0)或更新版本。 如果您使用受這些變更影響的方法, 可能需要最少的程式碼修改:

- [`ConfigurationOperationsExtensions.Get`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet)將不會再傳回如儲存體金鑰 (核心網站) 或 HTTP 認證 (閘道) 之類的**敏感參數**。
    - 若要取出所有設定 (包括機密參數) [`ConfigurationOperationsExtensions.List`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.list?view=azure-dotnet) , 請使用 [繼續]。  請注意, 具有「讀取者」角色的使用者將無法使用這個方法。 這可讓您更精確地控制哪些使用者可以存取叢集的機密資訊。 
    - 若只要取得 HTTP 閘道認證, [`ClusterOperationsExtensions.GetGatewaySettings`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.getgatewaysettings?view=azure-dotnet)請使用。 
- [`ConfigurationsOperationsExtensions.Update`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet)現在已被取代, 並已由[`ClusterOperationsExtensions.UpdateGatewaySettings`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.updategatewaysettings?view=azure-dotnet)取代。 
- [`ConfigurationsOperationsExtensions.EnableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet)和[`DisableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet)現在已被取代。 HTTP 現在一律會啟用, 因此不再需要這些方法。

### <a name="sdk-for-python"></a>適用於 Python 的 SDK

更新為適用于 Python 的 HDInsight SDK [1.0.0 版](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/)或更新版本。 如果您使用受這些變更影響的方法, 可能需要最少的程式碼修改:

- [`ConfigurationsOperations.get`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-)將不會再傳回如儲存體金鑰 (核心網站) 或 HTTP 認證 (閘道) 之類的**敏感參數**。
    - 若要取出所有設定 (包括機密參數) [`ConfigurationsOperations.list`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) , 請使用 [繼續]。  請注意, 具有「讀取者」角色的使用者將無法使用這個方法。 這可讓您更精確地控制哪些使用者可以存取叢集的機密資訊。 
    - 若只要取得 HTTP 閘道認證, [`ClusterOperations.get_gateway_settings`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-)請使用。
- [`ConfigurationsOperations.update`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#update-resource-group-name--cluster-name--configuration-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-)現在已被取代, 並已由[`ClusterOperations.update_gateway_settings`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-)取代。

### <a name="sdk-for-java"></a>適用于 JAVA 的 SDK

更新為適用于 JAVA 的 HDInsight SDK [1.0.0 版](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/)或更新版本。 如果您使用受這些變更影響的方法, 可能需要最少的程式碼修改:

- [`ConfigurationsInner.get`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.get)將不會再傳回如儲存體金鑰 (核心網站) 或 HTTP 認證 (閘道) 之類的**敏感參數**。
    - 若要取出所有設定 (包括機密參數) [`ConfigurationsInner.list`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.configurationsinner.list?view=azure-java-stable) , 請使用 [繼續]。  請注意, 具有「讀取者」角色的使用者將無法使用這個方法。 這可讓您更精確地控制哪些使用者可以存取叢集的機密資訊。 
    - 若只要取得 HTTP 閘道認證, [`ClustersInner.getGatewaySettings`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.clustersinner.getgatewaysettings?view=azure-java-stable)請使用。
- [`ConfigurationsInner.update`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.update)現在已被取代, 並已由[`ClustersInner.updateGatewaySettings`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.clustersinner.updategatewaysettings?view=azure-java-stable)取代。

### <a name="sdk-for-go"></a>適用于 Go 的 SDK

更新為適用于 Go 的 HDInsight SDK[版本 27.1.0](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight)或更新版本。 如果您使用受這些變更影響的方法, 可能需要最少的程式碼修改:

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Get)將不會再傳回如儲存體金鑰 (核心網站) 或 HTTP 認證 (閘道) 之類的**敏感參數**。
    - 若要取出所有設定 (包括機密參數) [`ConfigurationsClient.list`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.List) , 請使用 [繼續]。  請注意, 具有「讀取者」角色的使用者將無法使用這個方法。 這可讓您更精確地控制哪些使用者可以存取叢集的機密資訊。 
    - 若只要取得 HTTP 閘道認證, [`ClustersClient.get_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.GetGatewaySettings)請使用。
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Update)現在已被取代, 並已由[`ClustersClient.update_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings)取代。

### <a name="azhdinsight-powershell"></a>Az HDInsight PowerShell
更新至[Az PowerShell version 2.0.0](https://www.powershellgallery.com/packages/Az)或更新版本以避免中斷。  如果您使用受這些變更影響的方法, 可能需要最少的程式碼修改。
- `Grant-AzHDInsightHttpServicesAccess`現在已被取代, 並已由新`Set-AzHDInsightGatewayCredential`的 Cmdlet 取代。
- `Get-AzHDInsightJobOutput`已更新為支援儲存體金鑰的細微角色型存取。
    - 具有 HDInsight 叢集操作員、參與者或擁有者角色的使用者將不受影響。
    - 只有讀取者角色的使用者必須明確指定`DefaultStorageAccountKey`參數。
- `Revoke-AzHDInsightHttpServicesAccess`現在已被取代。 HTTP 現在一律會啟用, 因此不再需要此 Cmdlet。
 請參閱[az。HDInsight 遷移指南](https://github.com/Azure/azure-powershell/blob/master/documentation/migration-guides/Az.2.0.0-migration-guide.md#azhdinsight)以取得詳細資料。

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>將 HDInsight 叢集操作員角色指派新增至使用者

具有「[參與者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor)」或「[擁有](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner)者」角色的使用者可以將[hdinsight 叢集操作員](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator)角色指派給您想要擁有敏感性 HDInsight 叢集設定值之讀取/寫入存取權的使用者 (例如叢集閘道認證)和儲存體帳戶金鑰)。

### <a name="using-the-azure-cli"></a>使用 Azure CLI

新增此角色指派最簡單的方式, 就是使用`az role assignment create` Azure CLI 中的命令。

> [!NOTE]
> 此命令必須由具有「參與者」或「擁有者」角色的使用者執行, 因為只有他們可以授與這些許可權。 `--assignee`是您要指派 HDInsight 叢集操作員角色之使用者的電子郵件地址。

#### <a name="grant-role-at-the-resource-cluster-level"></a>授與資源 (叢集) 層級的角色

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee <user@domain.com> --scope /subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.HDInsight/clusters/<ClusterName>
```

#### <a name="grant-role-at-the-resource-group-level"></a>授與資源群組層級的角色

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com -g <ResourceGroupName>
```

#### <a name="grant-role-at-the-subscription-level"></a>授與訂用帳戶層級的角色

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com
```

### <a name="using-the-azure-portal"></a>使用 Azure 入口網站

您也可以使用 Azure 入口網站, 將 HDInsight 叢集操作員角色指派新增至使用者。 請參閱[使用 RBAC 來管理對 Azure 資源的存取和 Azure 入口網站新增角色指派](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment)中的檔。
