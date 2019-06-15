---
title: 移轉至 Azure HDInsight 的叢集設定中以角色為基礎的細微存取
description: 深入了解在 HDInsight 叢集組態中以角色為基礎的細微存取移轉所需的變更。
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 1ec4786291d6e2e5be6785e52cf3ab5bb5bbc690
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66754545"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>移轉至叢集組態中以角色為基礎的細微存取

我們引進了一些重要的變更，以支援更多更細緻以角色為基礎的存取以取得敏感資訊。 當這些組件變更時，有些**動作可能需要**如果您使用其中一個[受影響的實體/案例](#am-i-affected-by-these-changes)。

## <a name="what-is-changing"></a>變更內容為何？

以前，密碼無法透過 HDInsight API 來取得具有擁有者、 參與者或讀取器的叢集使用者[RBAC 角色](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)，因為它們是可供任何使用`*/read`所需的權限。
接下來，存取這些機密資料將會需要`Microsoft.HDInsight/clusters/configurations/*`權限，這表示它們無法再存取讀取者角色的使用者。 可用來取得更高的存取於使用者的角色權限的值應該允許被定義祕密。 這些包含值，例如叢集閘道 HTTP 認證、 儲存體帳戶金鑰和資料庫認證。

我們也引進新[HDInisght 叢集操作員](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator)能夠擷取祕密，但不授與 「 參與者 」 或 「 擁有者的系統管理權限的角色。 總結：

| 角色                                  | 先前                                                                                       | 從現在開始       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| 讀取者                                | -讀取權限，包括密碼                                                                   | -讀取存取權**排除**祕密 |           |   |   |
| HDInsight 叢集操作員<br>（新的角色） | N/A                                                                                              | -讀取/寫入存取，包括密碼         |   |   |
| 參與者                           | -讀取/寫入存取，包括密碼<br>-建立和管理所有 Azure 資源的類型。     | 無變更 |
| 擁有者                                 | -讀取/寫入存取，包括祕密<br>-完整存取權的所有資源<br>-委派給其他人的存取 | 無變更 |

如需如何將 HDInsight 叢集操作員角色指派新增至使用者授與讀取/寫入存取權叢集祕密，請參閱下一節，[將 HDInsight 叢集操作員角色指派新增至使用者](#add-the-hdinsight-cluster-operator-role-assignment-to-a-user)。

## <a name="am-i-affected-by-these-changes"></a>是否會受這些變更？

下列實體和案例會受到影響：

- [API](#api)：使用使用者`/configurations`或`/configurations/{configurationName}`端點。
- [適用於 Visual Studio Code 的 azure HDInsight 工具](#azure-hdinsight-tools-for-visual-studio-code)1.1.1 版或以下版本。
- [適用於 IntelliJ 的 azure 工具組](#azure-toolkit-for-intellij)版本 3.20.0 或以下版本。
- [Azure Data Lake 和 Stream Analytics Tools for Visual Studio](#azure-data-lake-and-stream-analytics-tools-for-visual-studio)低於 2.3.9000.1 的版本。
- [適用於 Eclipse 的 azure 工具組](#azure-toolkit-for-eclipse)版本 3.15.0 或以下版本。
- [適用於.NET 的 SDK](#sdk-for-net)
    - [版本 1.x 或 2.x](#versions-1x-and-2x):使用使用者`GetClusterConfigurations`， `GetConnectivitySettings`， `ConfigureHttpSettings`，`EnableHttp`或`DisableHttp`ConfigurationsOperationsExtensions 類別的方法。
    - [版本 3.x 版與](#versions-3x-and-up):使用使用者`Get`， `Update`， `EnableHttp`，或`DisableHttp`方法，從`ConfigurationsOperationsExtensions`類別。
- [適用於 Python SDK](#sdk-for-python):使用使用者`get`或是`update`方法從`ConfigurationsOperations`類別。
- [SDK for Java](#sdk-for-java):使用使用者`update`或是`get`方法從`ConfigurationsInner`類別。
- [SDK for Go](#sdk-for-go):使用使用者`Get`或是`Update`方法從`ConfigurationsClient`結構。
- [Az.HDInsight PowerShell](#azhdinsight-powershell)下方 2.0.0 版。
請參閱下節 (或使用上方連結) 以了解案例的移轉步驟。

### <a name="api"></a>API

將變更或已被取代的下列 Api:

- [**取得 /configurations/ {configurationName}** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) （移除機密資訊）
    - 先前用來取得個別的組態類型 （包括密碼）。
    - 此 API 呼叫現在會傳回個別的組態類型，對省略的祕密。 若要取得所有的設定，包括祕密，使用新的 POST /configurations 呼叫。 若要取得剛閘道設定，請使用新的 POST /getGatewaySettings 呼叫。
- [**取得 /configurations** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) （已過時）
    - 先前用來取得所有設定 （包括密碼）
    - 將不再支援這個 API 呼叫。 若要取得所有的設定，接下來，使用新的 POST /configurations 呼叫。 取得組態省略的敏感性參數使用，請使用 GET /configurations/ {configurationName} 呼叫。
- [**POST /configurations/{configurationName}** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings) (deprecated)
    - 先前用來更新閘道的認證。
    - 將已被取代，不再支援這個 API 呼叫。 請改用新的 POST /updateGatewaySettings。

Api 已新增下列取代：</span>

- [**POST /configurations**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#list-configurations)
    - 使用此 API 來取得所有的組態，包括密碼。
- [**POST /getGatewaySettings**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-gateway-settings)
    - 您可以使用此 API 來取得閘道設定。
- [**POST /updateGatewaySettings**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings)
    - 您可以使用此 API 來更新閘道設定 （使用者名稱和/或密碼）。

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>適用於 Visual Studio Code 的 azure HDInsight 工具

如果您使用 1.1.1 版或以下，更新[最新版的 Azure HDInsight Tools for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false)以避免中斷。

### <a name="azure-toolkit-for-intellij"></a>Azure Toolkit for IntelliJ

如果您使用版本 3.20.0 或下面，更新[最新版的 Azure Toolkit for IntelliJ 外掛程式](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij)以避免中斷。

### <a name="azure-data-lake-and-stream-analytics-tools-for-visual-studio"></a>Azure Data Lake 和 Stream Analytics Tools for Visual Studio

更新或更新版本的版本 2.3.9000.1 [Azure Data Lake 和 Stream Analytics Tools for Visual Studio](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools&ssr=false#overview)以避免中斷。  使用更新的說明，請參閱我們的文件[更新 Data Lake Tools for Visual Studio](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-visual-studio-tools-get-started#update-data-lake-tools-for-visual-studio)。

### <a name="azure-toolkit-for-eclipse"></a>適用於 Eclipse 的 Azure 工具組

如果您使用版本 3.15.0 或下面，更新[最新版的 Azure Toolkit for Eclipse](https://marketplace.eclipse.org/content/azure-toolkit-eclipse)以避免中斷。

### <a name="sdk-for-net"></a>適用於 .NET 的 SDK

#### <a name="versions-1x-and-2x"></a>版本 1.x 和 2.x

若要更新[2.1.0 版](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/2.1.0)HDInsight sdk for.NET。 如果您使用的方法，這些變更的影響，可能需要最少程式碼修改：

- `ClusterOperationsExtensions.GetClusterConfigurations` 將會**不會再傳回敏感性參數**，例如儲存體金鑰 （核心站台） 或 HTTP 認證 （閘道）。
    - 若要擷取所有的設定，包括敏感性參數，使用`ClusterOperationsExtensions.ListConfigurations`從現在開始。  請注意，'讀者 」 角色的使用者將無法使用這個方法。 這可讓您更精確地控制哪些使用者可以存取叢集的機密資訊。
    - 若要擷取只 HTTP 閘道的認證，請使用`ClusterOperationsExtensions.GetGatewaySettings`。

- `ClusterOperationsExtensions.GetConnectivitySettings` 現在已被取代，並已被取代`ClusterOperationsExtensions.GetGatewaySettings`。

- `ClusterOperationsExtensions.ConfigureHttpSettings` 現在已被取代，並已被取代`ClusterOperationsExtensions.UpdateGatewaySettings`。

- `ConfigurationsOperationsExtensions.EnableHttp` 和`DisableHttp`現在已被取代。 HTTP 是現在一律啟用，因此不再需要這些方法。

#### <a name="versions-3x-and-up"></a>版本 3.x 版與更新版本

若要更新[5.0.0 版](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/5.0.0)或更新版本的 HDInsight SDK for.NET。 如果您使用的方法，這些變更的影響，可能需要最少程式碼修改：

- [`ConfigurationOperationsExtensions.Get`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet) 將會**不會再傳回敏感性參數**，例如儲存體金鑰 （核心站台） 或 HTTP 認證 （閘道）。
    - 若要擷取所有的設定，包括敏感性參數，使用[ `ConfigurationOperationsExtensions.List` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.list?view=azure-dotnet)從現在開始。  請注意，'讀者 」 角色的使用者將無法使用這個方法。 這可讓您更精確地控制哪些使用者可以存取叢集的機密資訊。 
    - 若要擷取只 HTTP 閘道的認證，請使用[ `ClusterOperationsExtensions.GetGatewaySettings` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.getgatewaysettings?view=azure-dotnet)。 
- [`ConfigurationsOperationsExtensions.Update`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet) 現在已被取代，並已被取代[ `ClusterOperationsExtensions.UpdateGatewaySettings` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.updategatewaysettings?view=azure-dotnet)。 
- [`ConfigurationsOperationsExtensions.EnableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet) 並[ `DisableHttp` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet)現在已被取代。 HTTP 是現在一律啟用，因此不再需要這些方法。

### <a name="sdk-for-python"></a>適用於 Python 的 SDK

若要更新[1.0.0 版](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/)或更新版本的 HDInsight SDK for Python。 如果您使用的方法，這些變更的影響，可能需要最少程式碼修改：

- [`ConfigurationsOperations.get`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurations_operations.configurationsoperations?view=azure-python#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-) 將會**不會再傳回敏感性參數**，例如儲存體金鑰 （核心站台） 或 HTTP 認證 （閘道）。
    - 若要擷取所有的設定，包括敏感性參數，使用[ `ConfigurationsOperations.list` ](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurations_operations.configurationsoperations?view=azure-python#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-)從現在開始。  請注意，'讀者 」 角色的使用者將無法使用這個方法。 這可讓您更精確地控制哪些使用者可以存取叢集的機密資訊。 
    - 若要擷取只 HTTP 閘道的認證，請使用[ `ConfigurationsOperations.get_gateway_settings` ](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-)。
- [`ConfigurationsOperations.update`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#update-resource-group-name--cluster-name--tags-none--custom-headers-none--raw-false----operation-config-) 現在已被取代，並已被取代[ `ClusterOperations.update_gateway_settings` ](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-)。

### <a name="sdk-for-java"></a>SDK For Java

若要更新[1.0.0 版](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/)或更新版本的 HDInsight SDK for Java。 如果您使用的方法，這些變更的影響，可能需要最少程式碼修改：

- [`ConfigurationsInner.get`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.get) 將會**不會再傳回敏感性參數**，例如儲存體金鑰 （核心站台） 或 HTTP 認證 （閘道）。
    - 若要擷取所有的設定，包括敏感性參數，使用[ `ConfigurationsInner.list` ](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.configurationsinner.list?view=azure-java-stable)從現在開始。  請注意，'讀者 」 角色的使用者將無法使用這個方法。 這可讓您更精確地控制哪些使用者可以存取叢集的機密資訊。 
    - 若要擷取只 HTTP 閘道的認證，請使用[ `ClustersInner.getGatewaySettings` ](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.clustersinner.getgatewaysettings?view=azure-java-stable)。
- [`ConfigurationsInner.update`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.update) 現在已被取代，並已被取代[ `ClustersInner.updateGatewaySettings` ](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.clustersinner.updategatewaysettings?view=azure-java-stable)。

### <a name="sdk-for-go"></a>SDK For Go

若要更新[版本 27.1.0](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight)或更新版本的 HDInsight SDK for Go。 如果您使用的方法，這些變更的影響，可能需要最少程式碼修改：

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Get) 將會**不會再傳回敏感性參數**，例如儲存體金鑰 （核心站台） 或 HTTP 認證 （閘道）。
    - 若要擷取所有的設定，包括敏感性參數，使用[ `ConfigurationsClient.list` ](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.List)從現在開始。  請注意，'讀者 」 角色的使用者將無法使用這個方法。 這可讓您更精確地控制哪些使用者可以存取叢集的機密資訊。 
    - 若要擷取只 HTTP 閘道的認證，請使用[ `ClustersClient.get_gateway_settings` ](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.GetGatewaySettings)。
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Update) 現在已被取代，並已被取代[ `ClustersClient.update_gateway_settings` ](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings)。

### <a name="azhdinsight-powershell"></a>Az.HDInsight PowerShell
若要更新[Az PowerShell 版本 2.0.0](https://www.powershellgallery.com/packages/Az)或更新版本，以避免中斷。  如果您使用的方法，這些變更的影響，可能需要最少程式碼修改。
- `Grant-AzHDInsightHttpServicesAccess` 現在已被取代，並已由新取代`Set-AzHDInsightGatewayCredential`cmdlet。
- `Get-AzHDInsightJobOutput` 已更新為支援細微角色型存取儲存體金鑰。
    - 具有 HDInsight 叢集操作員、參與者或擁有者角色的使用者將不受影響。
    - 讀取者角色的使用者必須指定`DefaultStorageAccountKey`參數明確。
- `Revoke-AzHDInsightHttpServicesAccess` 現在已被取代。 HTTP 現在永遠啟用，因此不再需要這個指令程式。
 請參閱[az。HDInsight 移轉指南](https://github.com/Azure/azure-powershell/blob/master/documentation/migration-guides/Az.2.0.0-migration-guide.md#azhdinsight)如需詳細資訊。

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>將 HDInsight 叢集操作員角色指派新增至使用者

Monica[參與者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor)或[擁有者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner)角色可以指派[HDInisght 叢集操作員](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator)角色給使用者，您會想要有機密的讀取/寫入存取HDInsight 叢集的組態值 （例如叢集閘道認證和儲存體帳戶金鑰）。

### <a name="using-the-azure-cli"></a>使用 Azure CLI

要加入此角色指派的最簡單方式是使用`az role assignemnt create`Azure CLI 命令。

> [!NOTE]
> 與 「 參與者 」 或 「 擁有者角色，必須此命令執行的使用者，因為只有他們可以授與這些權限。 `--assignee`電子郵件地址，您要將 HDInsight 叢集操作員角色指派的使用者。

#### <a name="grant-role-at-the-resource-cluster-level"></a>授與資源 （叢集） 層級的角色

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee <user@domain.com> --scope /subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.HDInsight/clusters/<ClusterName>
```

#### <a name="grant-role-at-the-resource-group-level"></a>授與資源群組層級的角色

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com -g <ResourceGroupName>
```

#### <a name="grant-role-at-the-subscription-level"></a>授與訂用帳戶層級角色

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com
```

### <a name="using-the-azure-portal"></a>使用 Azure 入口網站

您也可以使用 Azure 入口網站將 HDInsight 叢集操作員角色指派新增至使用者。 文件，請參閱 <<c0> [ 管理使用 RBAC 和 Azure 入口網站的 Azure 資源的存取權-新增角色指派](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment)。
