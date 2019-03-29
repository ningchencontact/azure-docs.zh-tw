---
title: 如何對適用於容器的 Azure 監視器進行疑難排解 | Microsoft Docs
description: 本文說明如何對適用於容器的 Azure 監視器問題進行疑難排解並解決問題。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2018
ms.author: magoedte
ms.openlocfilehash: db4b468c03d93b073067083f4fae1ec86c70dde8
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2019
ms.locfileid: "58577031"
---
# <a name="troubleshooting-azure-monitor-for-containers"></a>對適用於容器的 Azure 監視器進行疑難排解

當您設定使用適用於容器的 Azure 監視器來監視 Azure Kubernetes Service (AKS) 叢集時，您可能會遇到阻止資料收集或報告狀態的問題。 本文將詳細說明一些常見問題與疑難排解步驟。

## <a name="authorization-error-during-onboarding-or-update-operation"></a>上架或更新作業期間的授權錯誤
在啟用適用於容器的 Azure 監視器，或更新的叢集，以支援收集計量，您可能會收到錯誤類似於下列-*用戶端 < 使用者的身分識別 >' 與物件識別碼 '< 使用者的 objectId >' 沒有執行動作 'Microsoft.Authorization/roleAssignments/write' 範圍*

在上架或更新的過程中，授與**監視計量發行者**角色指派會嘗試在叢集資源。 使用者起始的處理序啟用容器或更新的 Azure 監視器，以支援的度量集合必須有存取權**Microsoft.Authorization/roleAssignments/write** AKS 叢集上的權限資源範圍。 只有成員**擁有者**並**使用者存取系統管理員**內建角色會授與此權限存取權。 如果您的安全性原則需要指派更細微的層級權限，我們建議您檢視[自訂角色](../../role-based-access-control/custom-roles.md)並將它指派給需要它的使用者。 

您可以也手動授與此角色在 Azure 入口網站執行下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 
2. 在 Azure 入口網站中，按一下左上角的 [所有服務]。 在資源清單中，輸入**Kubernetes**。 當您開始輸入時，清單會根據您輸入的文字進行篩選。 選取  **Azure Kubernetes**。
3. 在 Kubernetes 叢集清單中，選取 從清單。
2. 從左側功能表中，按一下**存取控制 (IAM)**。
3. 選取 **+ 新增**新增角色指派，然後選取**監視計量發行者**角色，並在**選取**方塊中輸入**AKS**至篩選條件的結果，只在叢集上服務的訂用帳戶中定義的主體。 選取從該叢集的特定清單。
4. 選取 [儲存] 以完成角色指派。 

## <a name="azure-monitor-for-containers-is-enabled-but-not-reporting-any-information"></a>適用於容器的 Azure 監視器已啟用但未報告任何資訊
如果已成功啟用並設定，適用於容器的 Azure 監視器，但您無法檢視狀態資訊，或從記錄檔查詢會傳回任何結果，您會診斷此問題，依照下列步驟： 

1. 執行下列命令來檢查代理程式的狀態： 

    `kubectl get ds omsagent --namespace=kube-system`

    輸出應該像下面這樣，這表示它已正確部署：

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```  
2. 使用下列命令，利用代理程式版本 *06072018* 或更新版本來檢查部署狀態：

    `kubectl get deployment omsagent-rs -n=kube-system`

    輸出應該會像下列範例，這表示它已正確部署：

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

3. 執行下列命令來檢查 Pod 的狀態，以確認其正在執行：`kubectl get pods --namespace=kube-system`

    輸出應該會像下列範例，且 omsagent 的狀態為「執行中」：

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

5. 執行下列命令，以確認代理程式已順利上線：`kubectl logs omsagent-484hw --namespace=kube-system`

    狀態應該會像下列範例：

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

## <a name="error-messages"></a>錯誤訊息

下表簡要說明使用適用於容器的 Azure 監視器時，可能遇到的已知錯誤。

| 錯誤訊息  |  動作 |  
| ---- | --- |  
| 錯誤訊息 `No data for selected filters`  | 為新建立的叢集打造監視資料流程可能需要點時間。 至少需 10 到 15 分鐘才會出現叢集的資料。 |   
| 錯誤訊息 `Error retrieving data` | 雖然 Azure Kubenetes Service 叢集是為監視健康情況和效能而設定，但這個叢集和 Azure Log Analytics 工作區之間有所連結。 Log Analytics 工作區是用來儲存叢集的所有監視資料。 Log Analytics 工作區遭刪除或消失時可能會發生此錯誤。 請查看[管理存取權](../../azure-monitor/platform/manage-access.md?toc=/azure/azure-monitor/toc.json#view-workspace-details)，檢查工作區是否可使用。 如果工作區消失，您必須使用適用於容器的 Azure 監視器，將叢集重新上架。 若要重新上架，您必須[停用](container-insights-optout.md)對叢集的監視，並再次[啟用](container-insights-onboard.md?toc=%2fazure%2fmonitoring%2ftoc.json#enable-monitoring-for-a-new-cluster)適用於容器的 Azure 監視器。 |  
| 透過 az aks cli 新增適用於容器的 Azure 監視器後，會出現 `Error retrieving data` | 使用 `az aks cli` 上架時，適用於容器的 Azure 監視器罕見地無法正常上架。 檢查這個解決方案是否已上架。 若要檢查，請前往 Log Analytics 工作區，選取左側窗格的 [Solutions (解決方案)]，查看解決方案是否可使用。 若要解決此問題，您必須按照[如何部署適用於容器的 Azure 監視器](container-insights-onboard.md?toc=%2fazure%2fmonitoring%2ftoc.json)的指示，重新部署這個解決方案 |  

為協助診斷問題，[在此](https://github.com/Microsoft/OMS-docker/tree/ci_feature_prod/Troubleshoot#troubleshooting-script)提供疑難排解指令碼。  

## <a name="next-steps"></a>後續步驟
藉由啟用監視來擷取 AKS 叢集節點和 Pod 的健康情況計量，這些健康情況計量都可在 Azure 入口網站中取得。 若要了解如何使用適用於容器的 Azure 監視器，請參閱[檢視 Azure Kubernetes Service 健康情況](container-insights-analyze.md)。