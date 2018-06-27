---
title: Monitor Azure Kubernetes Service (AKS) 健康情況 (預覽) | Microsoft Docs
description: 本文說明如何輕鬆檢閱 AKS 容器效能，快速了解已裝載 Kubernetes 環境的使用率。
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2018
ms.author: magoedte
ms.openlocfilehash: 7c4294947cba72b1638e77c2dd8de1f5ee37b62a
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2018
ms.locfileid: "36285981"
---
# <a name="monitor-azure-kubernetes-service-aks-container-health-preview"></a>監視 Azure Kubernetes Service (AKS) 容器健康情況 (預覽)

本文說明如何設定和使用 Azure 監視器容器健康情況，以監視部署至 Azure Kubernetes Service (AKS) 上所裝載 Kubernetes 環境的工作負載效能。  監視 Kubernetes 叢集和容器很重要，尤其在您使用多個應用程式大規模執行生產叢集時。

容器健康情況可透過計量 API 從 Kubernetes 中可用的控制器、節點及容器 收集記憶體與處理器計量，讓您能夠監視效能。  啟用容器健康情況之後，會使用適用於 Linux 的 OMS 代理程式的容器化版本，自動收集這些計量，並儲存在 [Log Analytics](../log-analytics/log-analytics-overview.md) 工作區中。  所包含預先定義的檢視可顯示 各處容器的工作負載，以及影響 Kubernetes 叢集效能健康情況的項目，讓您了解：  

* 節點上有哪些容器在執行，以及其平均的處理器與記憶體使用率，來找出資源瓶頸
* 識別容器所在的控制器和/或 Pod，以查看控制器或 Pod 的整體效能 
* 在和支援 Pod 的標準流程無關的主機上，檢視其上執行中工作負載的資源使用率
* 了解叢集在負載低於平均和最繁重時的行為，協助識別容量需求並判斷其可承受的最大負載 

如果您想要監視和管理 Docker 與 Windows 容器主機以檢視設定、稽核以及資源使用率，請參閱[容器監視解決方案](../log-analytics/log-analytics-containers.md)。

## <a name="requirements"></a>需求 
開始之前，請檢閱下列詳細資料，以了解支援的必要條件。

- 支援下列 AKS 叢集版本：1.7.7 至 1.9.6。
- 適用於 Linux 的 OMS 代理程式容器化版本 microsoft/oms:ciprod04202018 與更新版本。 此代理程式會在容器健康情況上線期間自動安裝。  
- Log Analytics 工作區。  它可在您啟用新 AKS 叢集的監視功能時建立，或您可以透過 [Azure Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md)、[PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json) 或從 [Azure 入口網站](../log-analytics/log-analytics-quick-create-workspace.md)來建立。
- 用於啟用容器監視的 Log Analytics 參與者角色成員。  如需有關如何控制 Log Analytics 工作區存取的詳細資訊，請參閱[管理工作區](../log-analytics/log-analytics-manage-access.md)。

## <a name="components"></a>元件 

此功能依賴容器化適用於 Linux 的 OMS 代理程式來收集叢集中所有節點的效能與事件資料。  在您啟用容器監視之後，會自動部署並向指定的記錄分析工作區註冊此代理程式。 

>[!NOTE] 
>如果您已部署 AKS 叢集，則可以使用提供的 Azure Resource Manager 範本啟用監視，如本文稍後所示。 您無法使用 `kubectl` 來生集、刪除、重新部署或部署此代理程式。  
>

## <a name="sign-in-to-azure-portal"></a>登入 Azure 入口網站
在 [https://portal.azure.com](https://portal.azure.com) 登入 Azure 入口網站。 

## <a name="enable-container-health-monitoring-for-a-new-cluster"></a>啟用新叢集的容器健康情況監視
只有當您從 Azure 入口網站部署 AKS 叢集，才能啟用監視。  請依照快速入門文章[部署 Azure Kubernetes Service (AKS) 叢集](../aks/kubernetes-walkthrough-portal.md)中的步驟執行。  當您在 [監視] 頁面上時，在 [啟用監視] 選項選取 [是] 即可啟用，然後可以選取現有或建立新的 Log Analytics 工作區。  

啟用監視之後，可以順利完成所有設定工作，您可以透過兩種方式來監視叢集效能：

1. 選取左窗格中的 [健康情況]，直接從 AKS 叢集進行。<br><br> 
2. 在所選取叢集的 AKS 叢集頁面中，按一下 [監視容器健康情況] 圖格。  在 Azure 監視器中，選取左窗格的 [健康情況]。  

![在 AKS 中選取容器健康情況的選項](./media/monitoring-container-health/container-performance-and-health-select-01.png)

啟用監視之後，大約需要 15 分鐘的時間，您才能看到該叢集的作業資料。  

## <a name="enable-container-health-monitoring-for-existing-managed-clusters"></a>啟用現有受控叢集的容器健康情況監視
您可以從 Azure 入口網站或透過 PowerShell Cmdlet **New-AzureRmResourceGroupDeployment** 或 Azure CLI 使用提供的 Azure Resource Manager 範本，來完對已部署的 AKS 容器啟用監視。  


### <a name="enable-from-azure-portal"></a>從 Azure 入口網站啟用
執行下列步驟從 Azure 入口網站啟用 AKS 容器的監視。

1. 在 Azure 入口網站中，按一下 [所有服務]。 在資源清單中輸入**容器**。 當您開始輸入時，清單會根據您輸入的文字進行篩選。 選取 [Kubernetes 服務]。<br><br> ![Azure 入口網站](./media/monitoring-container-health/azure-portal-01.png)<br><br>  
2. 在容器清單中選取容器。
3. 在容器的概觀頁面上，選取 [監視容器健康情況]，然後 [上線以進行容器健康情況與記錄] 頁面隨即出現。
4. 在 [上線以進行容器健康情況與記錄] 頁面上，如果相同訂用帳戶中有現有 Log Analytics 工作區可作為叢集，請從下拉式清單中加以選取。  清單會預先選取訂用帳戶中可部署 AKS 容器的預設工作區和位置。 您可以選取 [新建]，並在相同訂用帳戶中指定新的工作區。<br><br> ![啟用 AKS 容器健康情況監視](./media/monitoring-container-health/container-health-enable-brownfield.png) 

    如果您選取 [新建]，[建立新工作區] 窗格會隨即出現。 [區域] 預設值是建立容器資源所在的區域，您可以接受預設值或選取不同的區域，然後指定工作區的名稱。  按一下 [建立] 以接受您的選取。<br><br> ![定義容器監視的工作區](./media/monitoring-container-health/create-new-workspace-01.png)  

    >[!NOTE]
    >在現階段，您無法在「美國中西部」區域建立新的工作區，您只能選取該區域中預先存在的工作區。  即使您可以從清單中選取該區域並讓部署啟動，但不久之後就會失敗。  
    >
 
啟用監視之後，大約需要 15 分鐘的時間，您才能看到該叢集的作業資料。 

### <a name="enable-using-azure-resource-manager-template"></a>啟用使用 Azure Resource Manager 範本
此範本包含兩個 JSON 範本，一個範本可指定啟用監視的設定，而另一個 JSON 範本包含參數值，您可設定來指定下列各項：

* AKS 容器資源識別碼 
* 叢集部署所在的資源群組 
* 在當中建立工作區的 Log Analytics 工作區與區域 

Log Analytics 工作區必須手動建立。  您可以從 [Azure 入口網站](../log-analytics/log-analytics-quick-create-workspace.md)透過 [Azure Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md)、[PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json) 來建立工作區。

如果您不熟悉透過 PowerShell 使用範本來部署資源的概念，請參閱[使用 Resource Manager 範本與 Azure PowerShell 來部署資源](../azure-resource-manager/resource-group-template-deploy.md)，或不熟悉 Azure CLI 的概念，請參閱[使用 Resource Manager 範本與 Azure CLI 部署資源](../azure-resource-manager/resource-group-template-deploy-cli.md)。

如果您選擇使用 Azure CLI，必須先在本機安裝和使用 CLI。  您必須執行 Azure CLI 2.0.27 版或更新版本。 請執行 `az --version` 來識別版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。 

#### <a name="create-and-execute-template"></a>建立和執行範本

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
3. 複製以下 JSON 語法並貼到您的檔案中：

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

4. 以您在 AKS 叢集的 [AKS 概觀] 頁面找到的值來編輯 **aksResourceId**、**aksResourceLocation** 的值。  **workspaceResourceId** 值是您 Log Analytics 工作區的完整資源識別碼，其中包含工作區名稱。  同時針對 **workspaceRegion** 指定工作區所在位置。    
5. 將此檔案儲存為本機資料夾的 **existingClusterParam.json**。
6. 您已準備好部署此範本。 

    * 從包含範本的資料夾使用下列 PowerShell 命令：

        ```powershell
        New-AzureRmResourceGroupDeployment -Name OnboardCluster -ResourceGroupName ClusterResourceGroupName -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
        ```
        可能需要幾分鐘的時間才能完成設定變更。 完成後，您會看到類似下列包含結果的訊息：

        ```powershell
        provisioningState       : Succeeded
        ```

    * 在 Linux 上，使用 Azure CLI 執行下列命令：
    
        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --resource-group <ResourceGroupName> --template-file ./existingClusterOnboarding.json --parameters @./existingClusterParam.json
        ```

        可能需要幾分鐘的時間才能完成設定變更。 完成後，您會看到類似下列包含結果的訊息：

        ```azurecli
        provisioningState       : Succeeded
        ```
啟用監視之後，大約需要 15 分鐘的時間，您才能看到該叢集的作業資料。  

## <a name="verify-agent-deployed-successfully"></a>請確認已成功部署代理程式
若要確認已正確部署 OMS 代理程式，請執行下列命令：` kubectl get ds omsagent --namespace=kube-system`。

輸出應像下面這樣，才表示確實正確部署：

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-performance-utilization"></a>檢視效能使用率
當您開啟容器健康情況時，頁面會立即顯示叢集節點的效能使用率。  關於 AKS 叢集的檢視資訊會組織成三個檢視方塊：

- 節點 
- Controllers  
- 容器

Kubernetes 物件後面的資料列階層會從叢集中的節點開始。  展開節點後，您會看到節點上有一或多個 Pod 在執行中，如果有多個容器分組至 Pod，則會顯示在階層中的最後一個資料列。<br><br> ![效能檢視中的範例 Kubernetes 節點階層](./media/monitoring-container-health/container-performance-and-health-view-03.png)

您可以從頁面頂端選取控制器或容器，然後檢閱那些物件的狀態與資源使用率。  使用畫面頂端的下拉式方塊，依據命名空間、服務及節點進行篩選。 如果您想要改為檢閱記憶體使用率，請從 [計量] 下拉式清單中選取 [記憶體 RSS] 或 [記憶體工作集]。  Kubernetes 1.8 和更新版本才支援**記憶體 RSS**。 否則，您會看見 **AVG %** 的值顯示為 *NaN%*，其為數值資料類型值，代表未定義或無法顯示的值。 

![容器效能節點效能檢視](./media/monitoring-container-health/container-performance-and-health-view-04.png)

根據預設，效能資料是以過去六小時的資料為基礎，但您可以使用頁面右上角的 [時間範圍] 下拉式清單來變更視窗。 此時，頁面不會不自動重新整理，因此您必須手動重新整理。 

在下列範例中，您會注意到*aks agentpool 3402399-0* 節點，[容器] 的值為 10，其為累計的部署容器總數。<br><br> ![每個節點範例的積存容器數](./media/monitoring-container-health/container-performance-and-health-view-07.png)<br><br> 這可幫助您快速識別叢集中節點之間的容器是否出現不平衡的情況。  

下表描述當您檢視「節點」時所顯示的資訊。

| 欄 | 說明 | 
|--------|-------------|
| Name | 主機的名稱 |
| 狀態 | 節點狀態的 Kubernetes 檢視 |
| AVG % | 根據選取的時間期間內所選取計量的平均節點百分比。 |
| AVERAGE | 根據選取的時間期間內所選取計量的平均節點實際值。  從為節點設定的 CPU/記憶體限制測量所得的平均值；對於 Pod 與容器而言，則是主機所報告的平均值。 |
| 容器 | 容器的數目。 |
| Uptime | 呈現自節點啟動或重新啟動以來經過的時間。 |
| Pod | 僅適用於容器。 顯示它位在哪個 Pod 之中。 |
| Controllers | 僅適用於容器與 Pod。 顯示它位在哪個控制器之中。 並非所有 Pod 都位在控制器之中，因此有些可能會顯示 N/A。 | 
| Trend AVG% | 根據容器與節點的平均計量百分比顯示的橫條圖趨勢。 |


從選取器選擇 [控制器]。<br><br> ![選取控制器檢視](./media/monitoring-container-health/container-performance-and-health-view-08.png)

您可在這裡查看控制器的效能健康情況。<br><br> ![<Name> 控制器效能檢視](./media/monitoring-container-health/container-performance-and-health-view-05.png)

資料列階層會以控制器開始，展開控制器後，您會看到一或多個 Pod 或一或多個容器。  展開 Pod 與最後一個資料夾，可顯示分組至 Pod 的容器。  

下表描述當您檢視「控制器」時所顯示的資訊。

| 欄 | 說明 | 
|--------|-------------|
| Name | 控制器的名稱|
| 狀態 | 容器的狀態，就是當其完成執行後的狀態，例如終止、失敗、停止或暫停。 如果容器在執行中，但狀態卻未正確呈現或未由代理程式收取，而且超過 30 分鐘都沒有回應時，狀態為不明。 |
| AVG % | 針對所選取計量的每個實體，積存其平均百分比的平均值。 |
| AVERAGE | 積存容器的平均 CPU millicore 或記憶體效能。  從為 Pod 設定的 CPU/記憶體限制測量所得的平均值。 |
| 容器 | 該控制器或 Pod 的容器總數。 |
| Restarts | 積存容器中的重新啟動計數。 |
| Uptime | 代表自容器啟動以來經過的時間。 |
| Pod | 僅適用於容器。 顯示它位在哪個 Pod 之中。 |
| 節點 | 僅適用於容器與 Pod。 顯示它位在哪個控制器之中。 | 
| Trend AVG% | 呈現容器平均計量百分比的橫條圖趨勢。 |

從選取器選擇 [容器]。<br><br> ![選取容器檢視](./media/monitoring-container-health/container-performance-and-health-view-09.png)

我們可在這裡查看容器的效能健康情況。<br><br> ![<Name> 控制器效能檢視](./media/monitoring-container-health/container-performance-and-health-view-06.png)

下表描述當您檢視「容器」時所顯示的資訊。

| 欄 | 說明 | 
|--------|-------------|
| Name | 控制器的名稱|
| 狀態 | 積存容器的狀態，若有的話。 |
| AVG % | 針對所選取計量的每個實體，積存其平均百分比的平均值。 |
| AVERAGE | 積存容器的平均 CPU millicore 或記憶體效能。 從為 Pod 設定的 CPU/記憶體限制測量所得的平均值。 |
| 容器 | 該控制器的容器總數。|
| Restarts | 代表自容器啟動以來經過的時間。 |
| Uptime | 代表自容器啟動或重新啟動以來經過的時間。 |
| Pod | Pod 所在位置的資訊。 |
| 節點 |  容器所在的節點。  | 
| Trend AVG% | 呈現容器平均計量百分比的橫條圖趨勢。 |

## <a name="container-data-collection-details"></a>容器資料收集詳細資料
容器健康情況會從容器主機和容器收集各種效能計量和記錄資料。 每隔三分鐘會收集一次資料。

### <a name="container-records"></a>容器資料列

下表顯示的範例是容器健康情況所收集的資料列，以及記錄搜尋結果中所顯示的資料類型。

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
Log Analytics 可協助您找出趨勢、診斷瓶頸、預測或讓將資料相互關聯，協助您判斷目前叢集設定是否以最佳方式運作。  提供可立即開始使用的預先定義記錄搜尋，或自訂以您想要的方式傳回資訊。 

您可以在展開容器時選取位在最右邊的 [檢視記錄] 選項，在工作區中對資料執行互動式分析。  在入口網站上，[記錄搜尋] 頁面會顯示在您所在頁面的正上方。<br><br> ![在 Log Analytics 中分析資料](./media/monitoring-container-health/container-performance-and-health-view-logs-01.png)   

轉送至 Log Analytics 的容器記錄輸出為 STDOUT 與 STDERR。 因為容器健康情況會監視 Azure 受控 Kubernetes (AKS)，而今天不會收集 Kube-system，因為已產生大量資料。     

### <a name="example-log-search-queries"></a>範例記錄檔搜尋查詢
從一或兩個範例開始建置查詢，然後加以修改以符合您的需求，通常很實用。 您可以試驗下列範例查詢，協助您建置更進階的查詢。

| 查詢 | 說明 | 
|-------|-------------|
| ContainerInventory<br> &#124; project Computer, Name, Image, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime<br> &#124; render table | 列出所有容器的生命週期資訊| 
| KubeEvents_CL<br> &#124; where not(isempty(Namespace_s))<br> &#124; sort by TimeGenerated desc<br> &#124; render table | Kubernetes 事件|
| ContainerImageInventory<br> &#124; summarize AggregatedValue = count() by Image, ImageTag, Running | 映像清查 | 
| **在進階分析中，選取折線圖**：<br> Perf<br> &#124; where ObjectName == "Container" and CounterName == "% Processor Time"<br> &#124; summarize AvgCPUPercent = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | 容器 CPU | 
| **在進階分析中，選取折線圖**：<br> Perf &#124; where ObjectName == "Container" and CounterName == "Memory Usage MB"<br> &#124; summarize AvgUsedMemory = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | 容器記憶體 |

## <a name="how-to-stop-monitoring-with-container-health"></a>如何停止使用容器健康情況來監視
在啟用監視 AKS 容器之後，當您決定不要繼續監視時，可以透過 PowerShell Cmdlet **New-AzureRmResourceGroupDeployment** 或 Azure CLI 使用提供的 Azure Resource Manager 範本來「退出」。  一個 JSON 範本指定可「退出」的設定，而另一個 JSON 範本包含參數值，您可設定來指定 AKS 叢集資源識別碼與叢集部署所在的資源群組。  如果您不熟悉透過 PowerShell 使用範本來部署資源的概念，請參閱[使用 Resource Manager 範本與 Azure PowerShell 來部署資源](../azure-resource-manager/resource-group-template-deploy.md)，或不熟悉 Azure CLI 的概念，請參閱[使用 Resource Manager 範本與 Azure CLI 部署資源](../azure-resource-manager/resource-group-template-deploy-cli.md)。

如果您選擇使用 Azure CLI，必須先在本機安裝和使用 CLI。  您必須執行 Azure CLI 2.0.27 版或更新版本。 請執行 `az --version` 來識別版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。 

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
              "config": {
                "logAnalyticsWorkspaceResourceID": null
              }
            }
           }
         }
       }
      ]
    }
    ```

2. 將此檔案儲存為本機資料夾的 **OptOutTemplate.json**。
3. 複製以下 JSON 語法並貼到您的檔案中：

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

4. 以您在所選取叢集的 [屬性] 頁面所找到 AKS 叢集的值來編輯 **aksResourceId** 與 **aksResourceLocation** 的值。<br><br> ![容器屬性頁面](./media/monitoring-container-health/container-properties-page.png)<br>

    當您在 [屬性] 頁面上時，亦請複製 [工作區資源識別碼]。  如果您稍後決定要刪除 Log Analytics 工作區 (不會在此過程中執行)，必須要有這個值。  

5. 將此檔案儲存為本機資料夾的 **OptOutParam.json**。
6. 您已準備好部署此範本。 

    * 從包含範本的資料夾使用下列 PowerShell 命令：

        ```powershell
        Connect-AzureRmAccount
        Select-AzureRmSubscription -SubscriptionName <yourSubscriptionName>
        New-AzureRmResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
        ```

        可能需要幾分鐘的時間才能完成設定變更。 完成後會傳回類似下列包含結果的訊息：

        ```powershell
        ProvisioningState       : Succeeded
        ```

    * 在 Linux 上，使用 Azure CLI 執行下列命令：

        ```azurecli
        az login   
        az account set --subscription "Subscription Name" 
        az group deployment create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
        ```

        可能需要幾分鐘的時間才能完成設定變更。 完成後會傳回類似下列包含結果的訊息：

        ```azurecli
        ProvisioningState       : Succeeded
        ```

如果建立工作區只為了支援監視叢集，當您不再需要時，可以手動予以刪除。 如果您不熟悉如何刪除工作區，請參閱[使用 Azure 入口網站來刪除 Azure Log Analytics 工作區](../log-analytics/log-analytics-manage-del-workspace.md)。  請注意，您會需要我們稍早在步驟 4 中複製的**工作區資源識別碼**。  

## <a name="troubleshooting"></a>疑難排解
本節提供的資訊有助於排解容器健康情況的疑難問題。

如果成功啟用容器健康情況，但當您執行記錄搜尋時，在 Log Analytics 中看不到任何狀態資訊或結果，則可以執行下列步驟來協助診斷問題。   

1. 執行下列命令來檢查代理程式的狀態：`kubectl get ds omsagent --namespace=kube-system`

    輸出應像下面這樣，才表示代理程式在叢集中的所有節點上執行中。  例如，此叢集有兩個節點，而您應預期該值等於節點數目。  

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```
2. 執行下列命令以檢查 Pod 的狀態，確認其是否在執行中：`kubectl get pods --namespace=kube-system`

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

3. 檢查代理程式記錄。 容器化的代理程式完成部署時，它會執行 OMI 命令並顯示代理程式版本與 Docker 提供者，以執行快速檢查。 請執行下列命令，以查看代理程式是否順利上線：`kubectl logs omsagent-484hw --namespace=kube-system`

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

[搜尋記錄](../log-analytics/log-analytics-log-search.md)以檢視詳細的容器健康情況與應用程式效能資訊。  
