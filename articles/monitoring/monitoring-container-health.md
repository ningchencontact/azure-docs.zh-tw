---
title: 監視 Azure Kubernetes Service (AKS) 健康情況 (預覽) | Microsoft Docs
description: 本文說明如何輕鬆檢閱 AKS 容器效能，快速了解已裝載 Kubernetes 環境的使用率。
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/15/2018
ms.author: magoedte
ms.openlocfilehash: 8027149f3e5ace163bf380bc5362fcb101397986
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2018
ms.locfileid: "42145538"
---
# <a name="monitor-azure-kubernetes-service-aks-container-health-preview"></a>監視 Azure Kubernetes Service (AKS) 容器健康情況 (預覽)

本文說明如何設定及使用 Azure 監視器容器健康情況，以監視部署至 Kubernetes 環境並在 Azure Kubernetes Service (AKS) 上裝載之工作負載的效能。 監視 Kubernetes 叢集和容器很重要，尤其在您使用多個應用程式大規模執行生產叢集時。

容器健康情況可讓您透過計量 API 從 Kubernetes 中提供的控制器、節點與容器收集記憶體與處理器計量，以監視效能。 啟用容器健康情況之後，系統會使用適用於 Linux 的 Log Analytics 代理程式的容器化版本自動收集這些計量，並儲存在 [Log Analytics](../log-analytics/log-analytics-overview.md) 工作區中。 所包含的預先定義檢視能顯示所在容器的工作負載，以及影響 Kubernetes 叢集效能健康情況的項目，使您可以：  

* 識別節點上正在執行的容器，以及其平均的處理器與記憶體使用率。 此知識可協助您識別資源瓶頸。
* 識別容器在控制器或 Pod 中的所在位置。 此知識可協助您檢視控制器或 Pod 的整體效能。 
* 檢閱在和支援 Pod 的標準程序無關之主機上執行的工作負載的資源使用率。
* 了解叢集在平均負載和最高負載之下的行為。 此知識可協助您識別所需的容量，並判斷叢集可承受的負載上限。 

如果您想要監視和管理 Docker 與 Windows 容器主機以檢視設定、稽核以及資源使用率，請參閱[容器監視解決方案](../log-analytics/log-analytics-containers.md)。

## <a name="prerequisites"></a>必要條件 
開始之前，請確定您有下列項目：

- 新的或現有的 AKS 叢集。
- 適用於 Linux 的 Log Analytics 代理程式容器化版本 microsoft/oms:ciprod04202018 或更新版本。 版本號碼以具有下列格式的日期呈現：*mmddyyyy*。 此代理程式會在容器健康情況上線期間自動安裝。 
- Log Analytics 工作區。 您可以在啟用新 AKS 叢集的監視時建立它，或是讓上線體驗在 AKS 叢集訂用帳戶的預設資源群組中建立預設工作區。 若選擇自行建立它 ，您可以透過 [Azure Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md)、透過 [PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json)，或是在 [Azure 入口網站](../log-analytics/log-analytics-quick-create-workspace.md)中建立它。
- 用於啟用容器監視的 Log Analytics 參與者角色。 如需有關如何控制 Log Analytics 工作區存取的詳細資訊，請參閱[管理工作區](../log-analytics/log-analytics-manage-access.md)。

[!INCLUDE [log-analytics-agent-note](../../includes/log-analytics-agent-note.md)]

## <a name="components"></a>元件 

您監視效能的能力需依賴容器化適用於 Linux 的 Log Analytics 代理程式，它會收集叢集中所有節點的效能與事件資料。 在您啟用容器監視之後，會自動部署並向指定的記錄分析工作區註冊此代理程式。 

>[!NOTE] 
>如果您已部署 AKS 叢集，則可以使用 Azure CLI 或提供的 Azure Resource Manager 範本來啟用監視，如此文章稍後所示。 您無法使用 `kubectl` 來生集、刪除、重新部署或部署此代理程式。 
>

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站
登入 [Azure 入口網站](https://portal.azure.com)。 

## <a name="enable-container-health-monitoring-for-a-new-cluster"></a>啟用新叢集的容器健康情況監視
在部署期間，您可以在 Azure 入口網站中或使用 Azure CLI 來啟用對新 AKS 叢集的監視。 若要從入口網站啟用，請依照快速入門文章[部署 Azure Kubernetes Service (AKS) 叢集](../aks/kubernetes-walkthrough-portal.md)中的步驟執行。 在 [監視] 頁面上，針對 [啟用監視] 選項，請選取 [是]，然後選取現有的 Log Analytics 工作區，或是建立新的 Log Analytics 工作區。 

若要使用 Azure CLI 針對建立的新 AKS 叢集啟用監視，請依照快速入門文章中[建立 AKS 叢集](../aks/kubernetes-walkthrough.md#create-aks-cluster)一節下的步驟執行。  

>[!NOTE]
>如果您選擇使用 Azure CLI，必須先在本機安裝並使用 CLI。 您必須執行 Azure CLI 2.0.43 版或更新版本。 若要知道您使用的版本，請執行 `az --version`。 如果您需要安裝或升級 Azure CLI，請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。 
>

啟用監視並順利完成所有設定工作之後，您可以透過兩種方式來監視叢集效能：

* 選取左窗格中的 [健康情況]，直接在 AKS 叢集中進行。
* 在所選取叢集的 AKS 叢集頁面中，選取 [監視器容器健全狀況] 圖格。 在 Azure 監視器中，選取左窗格中的 [健康情況]。 

  ![在 AKS 中選取容器健康情況的選項](./media/monitoring-container-health/container-performance-and-health-select-01.png)

在啟用監視之後，可能需要約 15 分鐘的時間才能檢視叢集的作業資料。 

## <a name="enable-container-health-monitoring-for-existing-managed-clusters"></a>啟用現有受控叢集的容器健康情況監視
您可以使用 Azure CLI、從入口網站或是透過使用 PowerShell Cmdlet `New-AzureRmResourceGroupDeployment` 所提供的 Azure Resource Manager 範本，來對已部署的 AKS 叢集啟用監視。 

### <a name="enable-monitoring-using-azure-cli"></a>使用 Azure CLI 來啟用監視
下列步驟會使用 Azure CLI 來啟用對 AKS 叢集的監視。 在此範例中，您不需要預先建立或指定現有的工作區。 此命令透過在 AKS 叢集預設資源群組中建立預設工作區 (若該區域中沒有預設工作區) 來為您簡化程序。  所建立的預設工作區格式類似 *DefaultWorkspace-<GUID>-<Region>* 格式。  

```azurecli
az aks enable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG  
```

輸出看起來會像下面這樣：

```azurecli
provisioningState       : Succeeded
```

### <a name="enable-monitoring-in-the-azure-portal"></a>在 Azure 入口網站中啟用監視
若要在 Azure 入口網站中啟用 AKS 容器的監視，請執行下列步驟：

1. 在 Azure 入口網站中，選取 [所有服務]。 
2. 在資源清單中，開始輸入**容器**。  
    清單會根據您輸入的文字進行篩選。 
3. 選取 [Kubernetes 服務]。  

    ![[Kubernetes 服務] 連結](./media/monitoring-container-health/azure-portal-01.png)

4. 在容器清單中，選取容器。
5. 在容器概觀頁面上，選取 [監視器容器健全狀況]。  
6. 在 [上線至容器健康狀態與記錄] 頁面上，若您在和叢集相同的訂用帳戶中有現有的 Log Analytics 工作區，請在下拉式清單中選取它。  
    清單會預先選取訂用帳戶中已部署 AKS 容器的預設工作區和位置。 

    ![啟用 AKS 容器健康情況監視](./media/monitoring-container-health/container-health-enable-brownfield-02.png)

>[!NOTE]
>如果您想要建立新的 Log Analytics 工作區以儲存來自叢集的監視資料，請遵循[建立 Log Analytics 工作區](../log-analytics/log-analytics-quick-create-workspace.md)中的指示。 請務必在和部署 AKS 容器相同的訂用帳戶中建立工作區。 
 
在啟用監視之後，可能需要約 15 分鐘的時間才能檢視叢集的作業資料。 

### <a name="enable-monitoring-by-using-an-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本啟用監視
此方法包含兩個 JSON 範本。 一個範本會指定啟用監視的設定，另一個範本則包含可設定以指定下列各項的參數值：

* AKS 容器資源識別碼。 
* 叢集部署所在的資源群組。
* 要在其中建立工作區的 Log Analytics 工作區與區域。 

Log Analytics 工作區必須手動建立。 若要建立工作區，您可以透過 [Azure Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md)、透過 [PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json)，或是在 [Azure 入口網站](../log-analytics/log-analytics-quick-create-workspace.md)中設定它。

若您不熟悉使用範本來部署資源的概念，請參閱：
* [使用 Resource Manager 範本與 Azure PowerShell 來部署資源](../azure-resource-manager/resource-group-template-deploy.md)
* [使用 Resource Manager 範本與 Azure CLI 部署資源](../azure-resource-manager/resource-group-template-deploy-cli.md)

如果您選擇使用 Azure CLI，必須先在本機安裝並使用 CLI。 您必須執行 Azure CLI 2.0.27 版或更新版本。 若要知道您使用的版本，請執行 `az --version`。 如果您需要安裝或升級 Azure CLI，請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。 

#### <a name="create-and-execute-a-template"></a>建立並執行範本

1. 複製以下 JSON 語法並貼到您的檔案中：

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "aksResourceId": {
        "type": "string",
        "metadata": {
           "description": "AKS Cluster Resource ID"
           }
    },
    "aksResourceLocation": {
    "type": "string",
     "metadata": {
        "description": "Location of the AKS resource e.g. \"East US\""
       }
    },
    "workspaceResourceId": {
      "type": "string",
      "metadata": {
         "description": "Azure Monitor Log Analytics Resource ID"
       }
    },
    "workspaceRegion": {
    "type": "string",
    "metadata": {
       "description": "Azure Monitor Log Analytics workspace region"
      }
     }
    },
    "resources": [
      {
    "name": "[split(parameters('aksResourceId'),'/')[8]]",
    "type": "Microsoft.ContainerService/managedClusters",
    "location": "[parameters('aksResourceLocation')]",
    "apiVersion": "2018-03-31",
    "properties": {
      "mode": "Incremental",
      "id": "[parameters('aksResourceId')]",
      "addonProfiles": {
        "omsagent": {
          "enabled": true,
          "config": {
            "logAnalyticsWorkspaceResourceID": "[parameters('workspaceResourceId')]"
          }
         }
       }
      }
     },
    {
        "type": "Microsoft.Resources/deployments",
        "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
        "apiVersion": "2017-05-10",
        "subscriptionId": "[split(parameters('workspaceResourceId'),'/')[2]]",
        "resourceGroup": "[split(parameters('workspaceResourceId'),'/')[4]]",
        "properties": {
            "mode": "Incremental",
            "template": {
                "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                "contentVersion": "1.0.0.0",
                "parameters": {},
                "variables": {},
                "resources": [
                    {
                        "apiVersion": "2015-11-01-preview",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "location": "[parameters('workspaceRegion')]",
                        "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                        "properties": {
                            "workspaceResourceId": "[parameters('workspaceResourceId')]"
                        },
                        "plan": {
                            "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                            "product": "[Concat('OMSGallery/', 'ContainerInsights')]",
                            "promotionCode": "",
                            "publisher": "Microsoft"
                        }
                    }
                ]
            },
            "parameters": {}
        }
       }
     ]
    }
    ```

2. 將此檔案儲存為本機資料夾的 **existingClusterOnboarding.json**。
3. 將下列 JSON 語法貼到您的檔案中：

    ```json
    {
       "$schema": "https://schema.management.azure.com/  schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
         "aksResourceId": {
           "value": "/subscriptions/<SubscroptiopnId>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
       },
       "aksResourceLocation": {
         "value": "East US"
       },
       "workspaceResourceId": {
         "value": "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroup>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>"
       },
       "workspaceRegion": {
         "value": "eastus"
       }
     }
    }
    ```

4. 使用 AKS 叢集之 [AKS 概觀] 頁面上的值，來編輯 **aksResourceId** 和 **aksResourceLocation** 的值。 **workspaceResourceId** 值是您 Log Analytics 工作區的完整資源識別碼，其中包含工作區名稱。 同時針對 **workspaceRegion** 指定工作區的所在位置。 
5. 將此檔案儲存為本機資料夾的 **existingClusterParam.json**。
6. 您已準備好部署此範本。 

    * 在包含範本的資料夾中使用下列 PowerShell 命令：

        ```powershell
        New-AzureRmResourceGroupDeployment -Name OnboardCluster -ResourceGroupName ClusterResourceGroupName -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
        ```
        可能需要幾分鐘的時間才能完成設定變更。 完成之後，將會顯示如下訊息並包含結果：

        ```powershell
        provisioningState       : Succeeded
        ```

    * 在 Linux 上使用 Azure CLI 來執行下列命令：
    
        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --resource-group <ResourceGroupName> --template-file ./existingClusterOnboarding.json --parameters @./existingClusterParam.json
        ```

        可能需要幾分鐘的時間才能完成設定變更。 完成之後，將會顯示如下訊息並包含結果：

        ```azurecli
        provisioningState       : Succeeded
        ```
在啟用監視之後，可能需要約 15 分鐘的時間才能檢視叢集的作業資料。 

## <a name="verify-agent-and-solution-deployment"></a>驗證代理程式和解決方案部署
使用代理程式 *06072018* 版或更新版本時，您可以確認代理程式和方案皆已成功部署。 使用舊版的代理程式時，您只能確認代理程式部署。

### <a name="agent-version-06072018-or-later"></a>代理程式 06072018 版或更新版本
執行下列命令以確認代理程式已成功部署。 

```
kubectl get ds omsagent --namespace=kube-system
```

輸出應該像下面這樣，這表示它已正確部署：

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

若要確認解決方案的部署，請執行下列命令：

```
kubectl get deployment omsagent-rs -n=kube-system
```

輸出應該像下面這樣，這表示它已正確部署：

```
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>早於 06072018 的代理程式版本

若要確認已正確部署 06072018 版以前發行的 Log Analytics 代理程式，請執行下列命令：  

```
kubectl get ds omsagent --namespace=kube-system
```

輸出應該像下面這樣，這表示它已正確部署：  

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-configuration-with-cli"></a>使用 CLI 來檢視設定
使用 `aks show` 命令來取得詳細資料，例如解決方案是否已啟用、Log Analytics 工作區 resourceID 為何，以及有關叢集的詳細資料。  

```azurecli
az aks show -g <resoourceGroupofAKSCluster> -n <nameofAksCluster>
```

在幾分鐘之後，此命令就會完成，並以 JSON 格式傳回叢集的相關資訊。  此命令的結果應該會顯示監視附加元件設定檔，而且應該會類似下列命令輸出：

```
"addonProfiles": {
    "omsagent": {
      "config": {
        "logAnalyticsWorkspaceResourceID": "/subscriptions/<WorkspaceSubscription>/resourceGroups/<DefaultWorkspaceRG>/providers/Microsoft.OperationalInsights/workspaces/<defaultWorkspaceName>"
      },
      "enabled": true
    }
  }
```

## <a name="view-performance-utilization"></a>檢視效能使用率
當您開啟容器健康情況時，頁面會立即顯示整個叢集的效能使用率。 關於 AKS 叢集的檢視資訊，會組織成四個檢視方塊：

- 叢集
- 節點 
- Controllers  
- 容器

在 [叢集] 索引標籤上，四個效能折線圖會顯示您叢集的關鍵效能計量。 

![[叢集] 索引標籤上的範例效能圖表](./media/monitoring-container-health/container-health-cluster-perfview.png)

效能圖表會顯示四個效能計量：

- **節點 CPU 使用率&nbsp;%**：整個叢集中 CPU 使用率的彙總檢視方塊。 您可以從圖表上方的百分位數選取器中，個別或合併選取 [Avg]、[Min]、[Max]、[50th]、[90th] 和 [95th]，來篩選時間範圍的結果。 
- **節點記憶體使用率&nbsp;%**：整個叢集中記憶體使用率的彙總檢視方塊。 您可以從圖表上方的百分位數選取器中，個別或合併選取 [Avg]、[Min]、[Max]、[50th]、[90th] 和 [95th]，來篩選時間範圍的結果。 
- **節點計數**：Kubernetes 中的節點計數和狀態。 所表示的叢集節點狀態為 [所有]、[就緒] 和 [尚未就緒]，且可以在圖表上方的選取器中進行個別篩選或結合。 
- **活動 Pod 計數**：Kubernetes 中的 Pod 計數和狀態。 所表示的叢集節點狀態為 [所有]、[擱置]、[執行中] 和 [未知]，且可以在圖表上方的選取器中進行個別篩選或結合。 

當您切換至 [節點]、[控制站] 和 [容器] 索引標籤時，頁面右邊會自動顯示屬性窗格。  它會顯示所選項目的屬性，包括您為了組織 Kubernetes 物件而定義的標籤。  按一下窗格中的 **>>** 連結，以檢視\隱藏此窗格。  

![範例 Kubernetes 觀點屬性窗格](./media/monitoring-container-health/perspectives-preview-pane-01.png)

當您展開階層中的物件時，屬性窗格會根據所選的物件更新。 在此窗格中，您也可以按一下窗格頂端的 [檢視 Kubernetes 事件記錄] 連結，以檢視已預先定義記錄搜尋的 Kubernetes 事件。 如需有關檢視 Kubernetes 記錄資料的詳細資訊，請參閱[搜尋記錄來分析資料](#search-logs-to-analyze-data)。

切換至 [節點] 索引標籤，而資料列階層會依循 Kubernetes 物件模型，從您叢集中的節點開始。 展開節點後，您便可以檢視一或多個在節點上執行的 Pod。 若有超過一個容器被分組至 Pod，它們會以最後一個資料列的形式顯示於階層中。 當主機有處理器或記憶體的壓力時，您也可檢視主機上有多少非 Pod 相關的工作負載正在執行。

![效能檢視中的範例 Kubernetes 節點階層](./media/monitoring-container-health/container-health-nodes-view.png)

您可以從頁面頂端選取控制器或容器，然後檢閱那些物件的狀態與資源使用率。 使用頂端的下拉式方塊來依據命名空間、服務及節點進行篩選。 如果您想要改為檢閱記憶體使用率，請在 [計量] 下拉式清單中選取 [記憶體 RSS] 或 [記憶體工作集]。 只有 Kubernetes 1.8 版和更新版本才支援**記憶體 RSS**。 否則，您會以 *NaN&nbsp;%* 的形式檢視 **Min&nbsp;%** 的值，這是代表未定義或無法顯示之值的數值資料類型值。 

![容器節點效能檢視](./media/monitoring-container-health/container-health-node-metric-dropdown.png)

根據預設，效能資料是以過去六小時的資料為基礎，但您可以使用右上角的 [時間範圍] 下拉式清單來變更時間長度。 此時，頁面不會不自動重新整理，因此您必須手動重新整理。 您也可以在百分位數選取器中選取 [Avg]、[Min]、[Max]、[50th]、[90th] 和 [95th]，來篩選時間範圍內的結果。 

![資料篩選的百分位數選取範圍](./media/monitoring-container-health/container-health-metric-percentile-filter.png)

在下列範例中，請注意節點 *aks-nodepool-3977305*，[容器] 的值為 5，這是彙總的已部署容器總數。

![每個節點的彙總容器數範例](./media/monitoring-container-health/container-health-nodes-containerstotal.png)

這可幫助您快速識別叢集中節點之間的容器是否出現不平衡的情況。 

當您檢視節點時所顯示的資訊已詳述於下表：

| 欄 | 說明 | 
|--------|-------------|
| 名稱 | 主機的名稱。 |
| 狀態 | 節點狀態的 Kubernetes 檢視。 |
| Avg&nbsp;%、Min&nbsp;%、Max&nbsp;%、50th&nbsp;%、90th&nbsp;% | 根據選取期間內百分位數的平均節點百分比。 |
| Avg、Min、Max、50th、90th | 根據所選取時間的期間內百分位數的平均節點實際值。 從為節點設定的 CPU/記憶體限制測量所得的平均值；對於 Pod 與容器而言，則是主機所報告的平均值。 |
| 容器 | 容器的數目。 |
| Uptime | 呈現自節點啟動或重新啟動以來經過的時間。 |
| Controllers | 僅適用於容器與 Pod。 顯示它位在哪個控制器之中。 並非所有 Pod 都位在控制器之中，因此有些可能會顯示 **N/A**。 | 
| Trend Avg&nbsp;%、Min&nbsp;%、Max&nbsp;%、50th&nbsp;%、90th&nbsp;% | 代表控制器百分位數計量百分比的橫條圖趨勢。 |


在選取器中，選取 [控制器]。

![選取控制器檢視](./media/monitoring-container-health/container-health-controllers-tab.png)

您可在這裡檢視控制器的效能健康情況。

![<Name> 控制器效能檢視](./media/monitoring-container-health/container-health-controllers-view.png)

資料列階層會以控制器開始並展開控制器。 您會檢視一或多個容器。 展開 Pod，最後一個資料夾會顯示分組至 Pod 的容器。 

在您檢視控制器時所顯示的資訊已詳述於下表：

| 欄 | 說明 | 
|--------|-------------|
| 名稱 | 控制器的名稱。|
| 狀態 | 容器的彙總狀態，就是當其完成執行後的狀態，例如 [確定]、[終止]、[失敗]、[停止] 或 [暫停]。 如果容器在執行中，但狀態卻未正確呈現或未由代理程式擷取，而且超過 30 分鐘都沒有回應時，則狀態為 [未知]。 下表中提供狀態圖示的其他詳細資料。|
| Avg&nbsp;%、Min&nbsp;%、Max&nbsp;%、50th&nbsp;%、90th&nbsp;% | 針對選取計量和百分位數之每個實體的平均百分比彙總平均值。 |
| Avg、Min、Max、50th、90th  | 容器針對選取百分位數的平均 CPU millicore 或記憶體效能彙總。 平均值是從為 Pod 設定的 CPU/記憶體限制測量所得。 |
| 容器 | 該控制器或 Pod 的容器總數。 |
| Restarts | 容器重新啟動計數的匯總。 |
| Uptime | 代表自容器啟動以來經過的時間。 |
| 節點 | 僅適用於容器與 Pod。 顯示它位在哪個控制器之中。 | 
| Trend Avg&nbsp;%、Min&nbsp;%、Max&nbsp;%、50th&nbsp;%、90th&nbsp;%| 代表控制器百分位數計量百分比的橫條圖趨勢。 |

[狀態] 欄位中的圖示會指出容器的線上狀態：
 
| 圖示 | 狀態 | 
|--------|-------------|
| ![準備好執行的狀態圖示](./media/monitoring-container-health/container-health-ready-icon.png) | 執行中 (就緒)|
| ![等候中或已暫停狀態圖示](./media/monitoring-container-health/container-health-waiting-icon.png) | 等候中或已暫停|
| ![上次回報的執行狀態圖示](./media/monitoring-container-health/container-health-grey-icon.png) | 上次回報的執行，但尚未回應超過 30 分鐘|
| ![成功狀態圖示](./media/monitoring-container-health/container-health-green-icon.png) | 已成功停止或無法停止|

狀態圖示會根據 Pod 所提供的功能顯示計數。 它會顯示較差的兩個狀態，且當您停留在狀態上時，會顯示容器中所有 Pod 的彙總狀態。 如果沒有就緒狀態，則狀態值會顯示 **(0)**。 

在選取器中，選取 [容器]。

![選取容器檢視](./media/monitoring-container-health/container-health-containers-tab.png)

您可在這裡檢視容器的效能健康情況。

![<Name> 控制器效能檢視](./media/monitoring-container-health/container-health-containers-view.png)

在您檢視容器時所顯示的資訊已詳述於下表：

| 欄 | 說明 | 
|--------|-------------|
| 名稱 | 控制器的名稱。|
| 狀態 | 容器的狀態，若有的話。 下一個表格會提供狀態圖示的其他詳細資料。|
| Avg&nbsp;%、Min&nbsp;%、Max&nbsp;%、50th&nbsp;%、90th&nbsp;% | 針對選取計量和百分位數之每個實體的平均百分比彙總。 |
| Avg、Min、Max、50th、90th  | 彙總容器所選百分位數的平均 CPU millicore 或記憶體效能。 平均值是從為 Pod 設定的 CPU/記憶體限制測量所得。 |
| Pod | Pod 所在的容器。| 
| 節點 |  容器所在的節點。 | 
| Restarts | 代表自容器啟動以來經過的時間。 |
| Uptime | 代表自容器啟動或重新啟動以來經過的時間。 |
| Trend Avg&nbsp;%、Min&nbsp;%、Max&nbsp;%、50th&nbsp;%、90th&nbsp;% | 代表容器平均計量百分比的橫條圖趨勢。 |

[狀態] 欄位中的圖示會指出 Pod 的線上狀態，如下表所述：
 
| 圖示 | 狀態 | 
|--------|-------------|
| ![準備好執行的狀態圖示](./media/monitoring-container-health/container-health-ready-icon.png) | 執行中 (就緒)|
| ![等候中或已暫停狀態圖示](./media/monitoring-container-health/container-health-waiting-icon.png) | 等候中或已暫停|
| ![上次回報的執行狀態圖示](./media/monitoring-container-health/container-health-grey-icon.png) | 上次回報的執行，但未回應的時間超過 30 分鐘|
| ![終止的狀態圖示](./media/monitoring-container-health/container-health-terminated-icon.png) | 已成功停止或無法停止|
| ![失敗狀態圖示](./media/monitoring-container-health/container-health-failed-icon.png) | 失敗狀態 |

## <a name="container-data-collection-details"></a>容器資料收集詳細資料
容器健康情況會從容器主機和容器收集各種效能計量和記錄資料。 每隔三分鐘會收集一次資料。

### <a name="container-records"></a>容器資料列

容器健康情況所收集的記錄，以及記錄搜尋結果中所顯示的資料類型已顯示於下表中：

| 資料類型 | 記錄檔搜尋中的資料類型 | 欄位 |
| --- | --- | --- |
| 主機和容器的效能 | `Perf` | Computer、ObjectName、CounterName &#40;%Processor Time、Disk Reads MB、Disk Writes MB、Memory Usage MB、Network Receive Bytes、Network Send Bytes、Processor Usage sec、Network&#41;、CounterValue、TimeGenerated、CounterPath、SourceSystem |
| 容器清查 | `ContainerInventory` | TimeGenerated、Computer、container name、ContainerHostname、Image、ImageTag、ContainerState、ExitCode、EnvironmentVar、Command、CreatedTime、StartedTime、FinishedTime、SourceSystem、ContainerID、ImageID |
| 容器映像清查 | `ContainerImageInventory` | TimeGenerated、Computer、Image、ImageTag、ImageSize、VirtualSize、Running、Paused、Stopped、Failed、SourceSystem、ImageID、TotalContainer |
| 容器記錄檔 | `ContainerLog` | TimeGenerated、Computer、image ID、container name、LogEntrySource、LogEntry、SourceSystem、ContainerID |
| 容器服務記錄檔 | `ContainerServiceLog`  | TimeGenerated、Computer、TimeOfCommand、Image、Command、SourceSystem、ContainerID |
| 容器節點清查 | `ContainerNodeInventory_CL`| TimeGenerated、Computer、ClassName_s、DockerVersion_s、OperatingSystem_s、Volume_s、Network_s、NodeRole_s、OrchestratorType_s、InstanceID_g、SourceSystem|
| 容器流程 | `ContainerProcess_CL` | TimeGenerated、Computer、Pod_s、Namespace_s、ClassName_s、InstanceID_s、Uid_s、PID_s、PPID_s、C_s、STIME_s、Tty_s、TIME_s、Cmd_s、Id_s、Name_s、SourceSystem |
| 清查 Kubernetes 叢集中的 Pod | `KubePodInventory` | TimeGenerated、Computer、ClusterId、ContainerCreationTimeStamp、PodUid、PodCreationTimeStamp、ContainerRestartCount、PodRestartCount、PodStartTime、ContainerStartTime、ServiceName、ControllerKind、ControllerName、ContainerStatus、ContainerID、ContainerName、Name、PodLabel、Namespace、PodStatus、ClusterName、PodIp、SourceSystem |
| 清查 Kubernetes 叢集中的節點部分 | `KubeNodeInventory` | TimeGenerated、Computer、ClusterName、ClusterId、LastTransitionTimeReady、Labels、Status、KubeletVersion、KubeProxyVersion、CreationTimeStamp、SourceSystem | 
| Kubernetes 事件 | `KubeEvents_CL` | TimeGenerated、Computer、ClusterId_s、FirstSeen_t、LastSeen_t、Count_d、ObjectKind_s、Namespace_s、Name_s、Reason_s、Type_s、TimeGenerated_s、SourceComponent_s、ClusterName_s、Message、SourceSystem | 
| Kubernetes 叢集中的服務 | `KubeServices_CL` | TimeGenerated、ServiceName_s、Namespace_s、SelectorLabels_s、ClusterId_s、ClusterName_s、ClusterIP_s、ServiceType_s、SourceSystem | 
| Kubernetes 叢集節點部分的效能計量 | Perf &#124; where ObjectName == “K8SNode” | Computer、ObjectName、CounterName &#40;cpuUsageNanoCores、memoryWorkingSetBytes、memoryRssBytes、networkRxBytes、networkTxBytes、restartTimeEpoch、networkRxBytesPerSec、networkTxBytesPerSec、cpuAllocatableNanoCores、memoryAllocatableBytes、cpuCapacityNanoCores、memoryCapacityBytes&#41;、CounterValue、TimeGenerated、CounterPath、SourceSystem | 
| Kubernetes 叢集容器部分的效能計量 | Perf &#124; where ObjectName == “K8SContainer” | CounterName &#40;cpuUsageNanoCores、memoryWorkingSetBytes、memoryRssBytes、restartTimeEpoch、cpuRequestNanoCores、memoryRequestBytes、cpuLimitNanoCores、memoryLimitBytes&#41;、CounterValue、TimeGenerated、CounterPath、SourceSystem | 

## <a name="search-logs-to-analyze-data"></a>搜尋記錄來分析資料
Log Analytics 可協助您找出趨勢、診斷瓶頸、預測或讓將資料相互關聯，協助您判斷目前叢集設定是否以最佳方式運作。 已為您提供可立即開始使用，或自訂以您想要的方式傳回資訊的預先定義記錄搜尋。 

您可以在預覽窗格中選取 [檢視 Kubernetes 事件記錄] 或 [檢視容器記錄] 選項，以在工作區中對資料執行互動式分析。 [記錄搜尋] 頁面會出現在您所在 Azure 入口網站頁面的右邊。

![在 Log Analytics 中分析資料](./media/monitoring-container-health/container-health-log-search-example.png)   

轉送至 Log Analytics 的容器記錄輸出為 STDOUT 與 STDERR。 因為容器健康情況會監視 Azure 受控 Kubernetes (AKS)，所以基於大量已產生資料的原因，今天將不會收集 Kube-system。 

### <a name="example-log-search-queries"></a>範例記錄檔搜尋查詢
從一或兩個範例開始建置查詢，然後加以修改以滿足您的需求，通常很實用。 若要取得建置更進階查詢的協助，您可以試驗下列範例查詢：

| 查詢 | 說明 | 
|-------|-------------|
| ContainerInventory<br> &#124; project Computer, Name, Image, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime<br> &#124; render table | 列出所有容器的生命週期資訊| 
| KubeEvents_CL<br> &#124; where not(isempty(Namespace_s))<br> &#124; sort by TimeGenerated desc<br> &#124; render table | Kubernetes 事件|
| ContainerImageInventory<br> &#124; summarize AggregatedValue = count() by Image, ImageTag, Running | 映像清查 | 
| **在進階分析中，選取折線圖**：<br> Perf<br> &#124; where ObjectName == "Container" and CounterName == "% Processor Time"<br> &#124; summarize AvgCPUPercent = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | 容器 CPU | 
| **在進階分析中，選取折線圖**：<br> Perf &#124; where ObjectName == "Container" and CounterName == "Memory Usage MB"<br> &#124; summarize AvgUsedMemory = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | 容器記憶體 |

## <a name="how-to-stop-monitoring-with-container-health"></a>如何停止使用容器健康情況來監視
如果您在啟用 AKS 容器的監視之後，決定不要繼續監視它時，可以透過使用所提供的 Azure Resource Manager 範本搭配 PowerShell Cmdlet **New-AzureRmResourceGroupDeployment** 或是 Azure CLI 來*選擇退出*。 其中一個 JSON 範本會指定要*選擇退出*的設定。另一個則包含可以設定以指定 AKS 叢集資源識別碼，以及部署叢集之資源群組的參數值。 

若您不熟悉使用範本部署資源的概念，請參閱：
* [使用 Resource Manager 範本與 Azure PowerShell 來部署資源](../azure-resource-manager/resource-group-template-deploy.md)
* [使用 Resource Manager 範本與 Azure CLI 部署資源](../azure-resource-manager/resource-group-template-deploy-cli.md)

如果您選擇使用 Azure CLI，必須先在本機安裝並使用 CLI。 您必須執行 Azure CLI 2.0.27 版或更新版本。 若要知道您使用的版本，請執行 `az --version`。 如果您需要安裝或升級 Azure CLI，請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。 

### <a name="create-and-execute-template"></a>建立和執行範本

1. 複製以下 JSON 語法並貼到您的檔案中：

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
           "type": "string",
           "metadata": {
             "description": "AKS Cluster Resource ID"
           }
       },
      "aksResourceLocation": {
        "type": "string",
        "metadata": {
           "description": "Location of the AKS resource e.g. \"East US\""
         }
       }
    },
    "resources": [
      {
        "name": "[split(parameters('aksResourceId'),'/')[8]]",
        "type": "Microsoft.ContainerService/managedClusters",
        "location": "[parameters('aksResourceLocation')]",
        "apiVersion": "2018-03-31",
        "properties": {
          "mode": "Incremental",
          "id": "[parameters('aksResourceId')]",
          "addonProfiles": {
            "omsagent": {
              "enabled": false,
              "config": null
            }
           }
         }
       }
      ]
    }
    ```

2. 將此檔案儲存為本機資料夾的 **OptOutTemplate.json**。
3. 將下列 JSON 語法貼到您的檔案中：

    ```json
    {
     "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "aksResourceId": {
         "value": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
      },
      "aksResourceLocation": {
        "value": "<aksClusterRegion>"
        }
      }
    }
    ```

4. 使用可所選取叢集的 [屬性] 頁面上找到之 AKS 叢集的值，來編輯 **aksResourceId** 和 **aksResourceLocation** 的值。

    ![容器屬性頁面](./media/monitoring-container-health/container-properties-page.png)

    當您在 [屬性] 頁面上時，亦請複製 [工作區資源識別碼]。 如果您稍後決定要刪除 Log Analytics 工作區，必須要有這個值。 這部分的程序並不會刪除 Log Analytics 工作區。 

5. 將此檔案儲存為本機資料夾的 **OptOutParam.json**。
6. 您已準備好部署此範本。 

    * 在包含範本的資料夾中使用下列 PowerShell 命令：

        ```powershell
        Connect-AzureRmAccount
        Select-AzureRmSubscription -SubscriptionName <yourSubscriptionName>
        New-AzureRmResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
        ```

        可能需要幾分鐘的時間才能完成設定變更。 完成後，系統會傳回類似下列包含結果的訊息：

        ```powershell
        ProvisioningState       : Succeeded
        ```

    * 在 Linux 上，使用 Azure CLI 執行下列命令：

        ```azurecli
        az login   
        az account set --subscription "Subscription Name" 
        az group deployment create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
        ```

        可能需要幾分鐘的時間才能完成設定變更。 完成後，系統會傳回類似下列包含結果的訊息：

        ```azurecli
        ProvisioningState       : Succeeded
        ```

如果建立工作區只為了支援監視叢集，當您不再需要時，可以手動予以刪除。 如果您不熟悉如何刪除工作區，請參閱[使用 Azure 入口網站來刪除 Azure Log Analytics 工作區](../log-analytics/log-analytics-manage-del-workspace.md)。 請注意，您會需要我們稍早在步驟 4 中複製的**工作區資源識別碼**。 

## <a name="troubleshooting"></a>疑難排解
本節提供的資訊有助於排解容器健康情況的疑難問題。

如果成功啟用並設定容器健康情況，但當您執行記錄搜尋時，在 Log Analytics 中無法檢視狀態資訊或結果，則可以執行下列步驟來協助診斷問題： 

1. 請執行下列命令來檢查代理程式的狀態： 

    `kubectl get ds omsagent --namespace=kube-system`

    輸出應該像下面這樣，這表示它已正確部署：

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```  
2. 使用代理程式 *06072018* 版或更新版本執行下列命令，來檢查方案部署狀態：

    `kubectl get deployment omsagent-rs -n=kube-system`

    輸出應該像下面這樣，這表示它已正確部署：

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

3. 執行下列命令以檢查 Pod 的狀態，來確認其是否正在執行：`kubectl get pods --namespace=kube-system`

    輸出應像下面這樣，omsagent 的狀態為「執行中」：

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system 
    NAME                                READY     STATUS    RESTARTS   AGE 
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d 
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d 
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d 
    omsagent-484hw                      1/1       Running   0          1d 
    omsagent-fkq7g                      1/1       Running   0          1d 
    ```

4. 檢查代理程式記錄。 容器化的代理程式完成部署時，它會執行 OMI 命令並顯示代理程式與提供者版本，以執行快速檢查。 

5. 執行下列命令以確認代理程式是否順利上線：`kubectl logs omsagent-484hw --namespace=kube-system`

    狀態應該如以下所示：

    ```
    User@aksuser:~$ kubectl logs omsagent-484hw --namespace=kube-system
    :
    :
    instance of Container_HostInventory
    {
        [Key] InstanceID=3a4407a5-d840-4c59-b2f0-8d42e07298c2
        Computer=aks-nodepool1-39773055-0
        DockerVersion=1.13.1
        OperatingSystem=Ubuntu 16.04.3 LTS
        Volume=local
        Network=bridge host macvlan null overlay
        NodeRole=Not Orchestrated
        OrchestratorType=Kubernetes
    }
    Primary Workspace: b438b4f6-912a-46d5-9cb1-b44069212abc    Status: Onboarded(OMSAgent Running)
    omi 1.4.2.2
    omsagent 1.6.0.23
    docker-cimprov 1.0.0.31
    ```

## <a name="next-steps"></a>後續步驟

若要檢視詳細的容器健康情況與應用程式效能資訊，請參閱[搜尋記錄](../log-analytics/log-analytics-log-search.md)。 
