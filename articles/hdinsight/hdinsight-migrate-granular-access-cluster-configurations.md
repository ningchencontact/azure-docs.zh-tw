---
title: 移轉至 Azure HDInsight 的叢集設定中以角色為基礎的細微存取
description: 深入了解要移轉至叢集組態中以角色為基礎的細微存取所需的變更。
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/19/2019
ms.openlocfilehash: 0422d848ccdf9ba82e68813de64eec863ee4ad29
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2019
ms.locfileid: "60006228"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>移轉至叢集組態中以角色為基礎的細微存取

我們引進了一些重要的變更，以支援更多更細緻以角色為基礎的存取以取得敏感資訊。 當這些組件變更時，有些**動作可能需要**如果您使用其中一個[受影響的實體/案例](#am-i-affected-by-these-changes)。

## <a name="what-is-changing"></a>變更內容為何？

以前，密碼無法透過 HDInsight API 來取得具有擁有者、 參與者或讀取器的叢集使用者[RBAC 角色](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)。
從現在開始，這些機密資料將不再可供讀取者角色的使用者存取。 我們也是會引進新的 「 HDInisght 叢集操作員 」 角色是要能夠擷取祕密，但不授與管理權限的參與者或擁有者。 總結：

| 角色                                  | 先前                                                                                       | Now       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| 讀取者                                | -讀取權限，包括密碼                                                                   | -讀取存取權**排除**祕密 |           |   |   |
| HDInsight 叢集操作員<br>（新的角色） | N/A                                                                                              | -讀取/寫入存取，包括密碼         |   |   |
| 參與者                           | -讀取/寫入存取，包括密碼<br>-建立和管理所有 Azure 資源的類型。     | 無變更 |
| 擁有者                                 | -讀取/寫入存取，包括祕密<br>-完整存取權的所有資源<br>-委派給其他人的存取 | 無變更 |

如需如何將 HDInsight 叢集操作員角色指派新增至使用者授與讀取/寫入存取權叢集祕密，請參閱下一節，[將 HDInsight 叢集操作員角色指派新增至使用者](#add-the-hdinsight-cluster-operator-role-assignment-to-a-user)。

## <a name="am-i-affected-by-these-changes"></a>是否會受這些變更？

下列實體和案例會受到影響：

- [API](#api)：使用使用者`/configurations`或`/configurations/{configurationName}`端點。
- [適用於 Visual Studio Code 的 azure HDInsight 工具](#azure-hdinsight-tools-for-visual-studio-code)版本 ___ 和以下版本。
- [適用於 IntelliJ 的 azure 工具組](#azure-toolkit-for-intellij)版本 __ 和以下版本。
- [適用於 Eclipse 的 azure 工具組](#azure-toolkit-for-eclipse)版本 __ 和以下版本。
- [適用於.NET 的 SDK](#sdk-for-net)
    - [版本 1.x 或 2.x](#versions-1x-and-2x):使用使用者`GetClusterConfigurations`， `GetConnectivitySettings`， `ConfigureHttpSettings`，`EnableHttp`或`DisableHttp`ConfigurationsOperationsExtensions 類別的方法。
    - [版本 3.x 版與](#versions-3x-and-up):使用使用者`EnableHttp`， `DisableHttp`， `Update`，或`Get`方法，從`ConfigurationsOperationsExtensions`類別。
- [適用於 Python SDK](#sdk-for-python):使用使用者`get`或`update`ConfigurationsOperations 類別的方法。
- [SDK for Java](#sdk-for-java):使用使用者`update`或`get`ConfigurationsInner 類別的方法。
- [SDK for Go](#sdk-for-go):使用使用者`Get`或`Update`ConfigurationsClient 結構中的方法。

若要查看移轉下列各節 （或使用上述連結） 為您的案例的步驟，請參閱。

### <a name="api"></a>API

將變更或已被取代的下列 Api:

- [**取得 /configurations/ {configurationName}** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) （移除機密資訊） https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/configurations/{configurationName}?api-version={api-version}
    - 先前用來取得個別的組態類型 （包括密碼）。
    - 此 API 呼叫現在會傳回個別的組態類型，對省略的祕密。 若要取得所有的設定，包括祕密，使用 新[POST /configurations]()呼叫。 若要取得剛閘道設定，請使用 新[POST /getGatewaySettings]()呼叫。
- [**取得 /configurations** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configurations) （已過時） https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/configurations?api-version={api-version}
    - 先前用來取得所有設定 （包括密碼）
    - 將不再支援這個 API 呼叫。 若要取得所有的設定，接下來，使用 新[POST /configurations]()呼叫。 取得與省略的敏感性參數的組態，請使用[GET /configurations/ {configurationName}]()呼叫。
- [**POST /configurations/{configurationName}**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#change-connectivity-settings) (deprecated) https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/configurations/{configurationName}?api-version={api-version}
    - 先前用來更新閘道的認證。
    - 將已被取代，不再支援這個 API 呼叫。 使用新[POST /updateGatewaySettings]()改。

Api 已新增下列取代：</span>

- **POST /configurations** https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/configurations?api-version={api-version}
    - 使用此 API 來取得所有的組態，包括密碼。
- **POST /getGatewaySettings** https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/getGatewaySettings?api-version={api-version}
    - 您可以使用此 API 來取得閘道設定。
- **POST /updateGatewaySettings** https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/updateGatewaySettings?api-version={api-version}
    - 您可以使用此 API 來更新閘道設定 （使用者名稱和/或密碼）。

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>適用於 Visual Studio Code 的 azure HDInsight 工具

如果您使用 1.1.1 版或更舊版本，請更新至[最新版的 Azure HDInsight Tools for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false)以避免中斷。

### <a name="azure-toolkit-for-intellij"></a>Azure Toolkit for IntelliJ

如果您使用新版 3.21.0 或更舊版本，請更新至[最新版的 Azure Toolkit for IntelliJ 外掛程式](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij)以避免中斷。

### <a name="azure-toolkit-for-eclipse"></a>適用於 Eclipse 的 Azure 工具組

如果您使用 2019年-03-29 版本或更舊版本，請更新至最新版的 Azure Toolkit for Eclipse，以避免中斷。

### <a name="sdk-for-net"></a>適用於 .NET 的 SDK

#### <a name="versions-1x-and-2x"></a>版本 1.x 和 2.x

請更新至[2.1.0 版](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/2.1.0)HDInsight sdk for.NET。 如果您使用的方法，這些變更的影響，可能需要最少程式碼修改：

- `ClusterOperationsExtensions.GetClusterConfigurations` 將會**不會再傳回敏感性參數**，例如儲存體金鑰 （核心站台） 或 HTTP 認證 （閘道）。
    - 若要擷取所有的設定，包括敏感性參數，使用`ClusterOperationsExtensions.ListConfigurations`從現在開始。  請注意，'讀者 」 角色的使用者將無法使用這個方法。 這可讓您更精確地控制哪些使用者可以存取叢集的機密資訊。
    - 若要擷取只 HTTP 閘道的認證，請使用`ClusterOperationsExtensions.GetGatewaySettings`。

- `ClusterOperationsExtensions.GetConnectivitySettings` 現在已被取代，並已被取代`ClusterOperationsExtensions.GetGatewaySettings`。

- `ClusterOperationsExtensions.ConfigureHttpSettings` 現在已被取代，並已被取代`ClusterOperationsExtensions.UpdateGatewaySettings`。

- `ConfigurationsOperationsExtensions.EnableHttp` 和`DisableHttp`現在已被取代。 HTTP 是現在一律啟用，因此不再需要這些方法。

#### <a name="versions-3x-and-up"></a>版本 3.x 版與更新版本

請更新至[5.0.0 版](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/5.0.0)HDInsight sdk for.NET。 如果您使用的方法，這些變更的影響，可能需要最少程式碼修改：

- [`ConfigurationOperationsExtensions.Get`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet) 將會**不會再傳回敏感性參數**，例如儲存體金鑰 （核心站台） 或 HTTP 認證 （閘道）。
    - 若要擷取所有的設定，包括敏感性參數，使用`ConfigurationOperationsExtensions.List`從現在開始。  請注意，'讀者 」 角色的使用者將無法使用這個方法。 這可讓您更精確地控制哪些使用者可以存取叢集的機密資訊。 
    - 若要擷取只 HTTP 閘道的認證，請使用`ClusterOperationsExtensions.GetGatewaySettings`。 
- [`ConfigurationsOperationsExtensions.Update`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet) 現在已被取代，並已被取代`ClusterOperationsExtensions.UpdateGatewaySettings`。 
- [`ConfigurationsOperationsExtensions.EnableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet) 並[ `DisableHttp` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet)現在已被取代。 HTTP 是現在一律啟用，因此不再需要這些方法。

### <a name="sdk-for-python"></a>適用於 Python 的 SDK

請更新至[1.0.0 版](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/)HDInsight sdk for Python。 如果您使用的方法，這些變更的影響，可能需要最少程式碼修改：

- [`ConfigurationsOperations.get`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurations_operations.configurationsoperations?view=azure-python#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-) 將會**不會再傳回敏感性參數**，例如儲存體金鑰 （核心站台） 或 HTTP 認證 （閘道）。
    - 若要擷取所有的設定，包括敏感性參數，使用[ `ConfigurationsOperations.list` ](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurations_operations.configurationsoperations?view=azure-python#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-)從現在開始。  請注意，'讀者 」 角色的使用者將無法使用這個方法。 這可讓您更精確地控制哪些使用者可以存取叢集的機密資訊。 
    - 若要擷取只 HTTP 閘道的認證，請使用[ `ConfigurationsOperations.get_gateway_settings` ](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-)。
- [`ConfigurationsOperations.update`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#update-resource-group-name--cluster-name--tags-none--custom-headers-none--raw-false----operation-config-) 現在已被取代，並已被取代[ `ClusterOperationsExtensions.update_gateway_settings` ](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-)。

### <a name="sdk-for-java"></a>SDK For Java

請更新至[1.0.0 版](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/)HDInsight sdk for Java。 如果您使用的方法，這些變更的影響，可能需要最少程式碼修改：

- [`ConfigurationsInner.get`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.get) 將會**不會再傳回敏感性參數**，例如儲存體金鑰 （核心站台） 或 HTTP 認證 （閘道）。
    - 若要擷取所有的設定，包括敏感性參數，使用`ConfigurationsInner.list`從現在開始。  請注意，'讀者 」 角色的使用者將無法使用這個方法。 這可讓您更精確地控制哪些使用者可以存取叢集的機密資訊。 
    - 若要擷取只 HTTP 閘道的認證，請使用`ConfigurationsOperations.get_gateway_settings`。
- [`ConfigurationsInner.update`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.update) 現在已被取代，並已被取代`ClusterOperationsExtensions.update_gateway_settings`。

### <a name="sdk-for-go"></a>SDK For Go

請更新至[版本 27.1.0](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight) HDInsight sdk for Go。 如果您使用的方法，這些變更的影響，可能需要最少程式碼修改：

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Get) 將會**不會再傳回敏感性參數**，例如儲存體金鑰 （核心站台） 或 HTTP 認證 （閘道）。
    - 若要擷取所有的設定，包括敏感性參數，使用[ `ConfigurationsClient.list` ](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.List)從現在開始。  請注意，'讀者 」 角色的使用者將無法使用這個方法。 這可讓您更精確地控制哪些使用者可以存取叢集的機密資訊。 
    - 若要擷取只 HTTP 閘道的認證，請使用[ `ClustersClient.get_gateway_settings` ](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.GetGatewaySettings)。
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Update) 現在已被取代，並已被取代[ `ClustersClient.update_gateway_settings` ](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings)。

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>將 HDInsight 叢集操作員角色指派新增至使用者

擁有使用者[參與者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor)或是[擁有者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner)角色可授與 HDInsight 叢集操作員 」 角色給使用者，您想要讀取/寫入存取 HDInsight 叢集祕密像叢集閘道認證和儲存體帳戶金鑰。

### <a name="using-the-azure-cli"></a>使用 Azure CLI

要加入此角色指派的最簡單方式是在 Azure CLI 中使用下列命令：

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com
```

> [!NOTE]
> 與 「 參與者 」 或 「 擁有者角色，必須此命令執行的使用者，因為只有他們可以授與這些權限。 `--assignee`電子郵件地址，您要將 HDInsight 叢集操作員角色指派的使用者。

上述命令會授與此訂用帳戶層級的角色。 若要改為只授與此資源群組層級的角色，您可以修改命令如下：

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com -g <ResourceGroupName>
```

### <a name="using-the-azure-portal"></a>使用 Azure 入口網站

您也可以使用 Azure 入口網站將 HDInsight 叢集操作員角色指派新增至使用者。 文件，請參閱 <<c0> [ 管理使用 RBAC 和 Azure 入口網站的 Azure 資源的存取權-新增角色指派](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment)。
