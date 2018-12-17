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
ms.date: 11/30/2018
ms.author: magoedte
ms.openlocfilehash: 5a9211abdbc4c9ea7907dfac00d449317dd13089
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2018
ms.locfileid: "53190742"
---
# <a name="troubleshooting-azure-monitor-for-containers"></a>對適用於容器的 Azure 監視器進行疑難排解

當您設定使用適用於容器的 Azure 監視器來監視 Azure Kubernetes Service (AKS) 叢集時，您可能會遇到阻止資料收集或報告狀態的問題。 本文將詳細說明一些常見問題與疑難排解步驟。

## <a name="azure-monitor-for-containers-is-enabled-but-not-reporting-any-information"></a>適用於容器的 Azure 監視器已啟用但未報告任何資訊
如果適用於容器的 Azure 監視器已成功啟用並設定，但您無法檢視狀態資訊，或者未從 Log Analytics 記錄查詢中傳回任何結果，您可以遵循下列步驟來診斷此問題： 

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
| 錯誤訊息 `Error retrieving data` | 雖然 Azure Kubenetes Service 叢集是為監視健康情況和效能而設定，但這個叢集和 Azure Log Analytics 工作區之間有所連結。 Log Analytics 工作區是用來儲存叢集的所有監視資料。 Log Analytics 工作區遭刪除或消失時可能會發生此錯誤。 請查看[管理存取權](../../azure-monitor/platform/manage-access.md?toc=/azure/azure-monitor/toc.json#workspace-information)，檢查工作區是否可使用。 如果工作區消失，您必須使用適用於容器的 Azure 監視器，將叢集重新上架。 若要重新上架，您必須[停用](container-insights-optout.md)對叢集的監視，並再次[啟用](container-insights-onboard.md?toc=%2fazure%2fmonitoring%2ftoc.json#enable-monitoring-for-a-new-cluster)適用於容器的 Azure 監視器。 |  
| 透過 az aks cli 新增適用於容器的 Azure 監視器後，會出現 `Error retrieving data` | 使用 `az aks cli` 上架時，適用於容器的 Azure 監視器罕見地無法正常上架。 檢查這個解決方案是否已上架。 若要檢查，請前往 Log Analytics 工作區，選取左側窗格的 [Solutions (解決方案)]，查看解決方案是否可使用。 若要解決此問題，您必須按照[如何部署適用於容器的 Azure 監視器](container-insights-onboard.md?toc=%2fazure%2fmonitoring%2ftoc.json)的指示，重新部署這個解決方案 |  

為協助診斷問題，[在此](https://github.com/Microsoft/OMS-docker/tree/ci_feature_prod/Troubleshoot#troubleshooting-script)提供疑難排解指令碼。  

## <a name="next-steps"></a>後續步驟
藉由啟用監視來擷取 AKS 叢集節點和 Pod 的健康情況計量，這些健康情況計量都可在 Azure 入口網站中取得。 若要了解如何使用適用於容器的 Azure 監視器，請參閱[檢視 Azure Kubernetes Service 健康情況](container-insights-analyze.md)。