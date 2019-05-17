---
title: 即時檢視適用於容器的 Azure 監視器記錄 | Microsoft Docs
description: 本文章說明容器記錄檔 (stdout/stderr) 和事件的即時檢視，而不需要 for containers 中使用 Azure 監視器使用 kubectl。
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
ms.date: 05/10/2019
ms.author: magoedte
ms.openlocfilehash: 376a7f3f83cc7fcf7490675d9c0aef1513862e8a
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2019
ms.locfileid: "65521724"
---
# <a name="how-to-view-logs-and-events-in-real-time-preview"></a>如何檢視記錄檔和事件，即時 （預覽）
適用於容器的 azure 監視器包含的功能，目前為預覽狀態，而不需執行 kubectl 命令提供您的 Azure Kubernetes Service (AKS) 容器記錄檔 (stdout/stderr) 和事件的即時檢視。 當您選取其中一個選項時，新的窗格會出現下方的效能資料的資料表上**節點**，**控制站**，並**容器**檢視。 它會顯示即時記錄和容器引擎，進一步協助疑難排解問題所產生的事件。 

>[!NOTE]
>**參與者**叢集資源的存取權是需要這項功能才能運作。
>

即時記錄支援三種不同的方法來控制記錄的存取：

1. 已啟用不具 Kubernetes RBAC 授權的 AKS 
2. 已使用 Kubernetes RBAC 授權啟用的 AKS
3. 啟用與 Azure Active Directory (AD) SAML 型單一登入的 AKS 

## <a name="kubernetes-cluster-without-rbac-enabled"></a>已啟用不具 RBAC 的 Kubernetes 叢集
 
如果您的 Kubernetes 叢集不是使用 Kubernetes RBAC 授權所設定或已與 Azure AD 單一登入整合，則您不需遵循這些步驟。 由於 Kubernetes 授權會使用 kube-api，因此，唯讀權限是必要的。

## <a name="kubernetes-rbac-authorization"></a>Kubernetes RBAC 授權
如果您已啟用 Kubernetes RBAC 授權，您將需套用叢集角色繫結。 下列範例步驟將示範如何從這個 yaml 設定範本設定叢集角色繫結。 

1. 複製並貼上這個 yaml 檔案，然後將它儲存為 LogReaderRBAC.yaml。  

    ```
    apiVersion: rbac.authorization.k8s.io/v1 
    kind: ClusterRole 
    metadata: 
       name: containerHealth-log-reader 
    rules: 
       - apiGroups: [""] 
         resources: ["pods/log", "events"] 
         verbs: ["get", "list"]  
    --- 
    apiVersion: rbac.authorization.k8s.io/v1 
    kind: ClusterRoleBinding 
    metadata: 
       name: containerHealth-read-logs-global 
    roleRef: 
        kind: ClusterRole 
        name: containerHealth-log-reader 
        apiGroup: rbac.authorization.k8s.io 
    subjects: 
       - kind: User 
         name: clusterUser 
         apiGroup: rbac.authorization.k8s.io
    ```

2. 如果您要將它設定在第一次，您執行下列命令來建立叢集規則繫結： `kubectl create -f LogReaderRBAC.yaml`。 如果您先前已啟用支援即時記錄檔預覽之前，我們介紹了即時的事件記錄檔，以更新您的設定，執行下列命令： `kubectl apply -f LiveLogRBAC.yml`。 

## <a name="configure-aks-with-azure-active-directory"></a>使用 Azure Active Directory 設定 AKS
AKS 可設定為使用 Azure Active Directory (AD) 進行使用者驗證。 如果您第一次設定此項，請參閱[將 Azure Active Directory 與 Azure Kubernetes Service 整合](../../aks/azure-ad-integration.md)。 在執行建立[用戶端應用程式](../../aks/azure-ad-integration.md#create-client-application)並指定**重新導向 URI** 的步驟期間，您需要將另一個 URI 新增至清單 `https://ininprodeusuxbase.microsoft.com/*`。  

>[!NOTE]
>使用 Azure Active Directory 設定驗證以進行單一登入，只能在新 AKS 叢集的初始部署期間完成。 您無法針對已經部署的 AKS 叢集設定單一登入。 您必須設定的驗證 **（傳統） 的應用程式註冊**選項，以便在 URI 中，支援使用萬用字元，並同時的 Azure AD 中將它新增至清單中，將其註冊為**原生**應用程式。
> 

## <a name="view-live-logs-and-events"></a>檢視即時記錄檔和事件

從容器引擎產生時，您可以檢視即時記錄檔事件**節點**，**控制站**，並**容器**檢視。 從 [屬性] 窗格中您選取**檢視即時資料 （預覽）** 選項和一個窗格顯示如下供您檢視記錄檔和事件的連續資料流中的效能資料資料表。 

![節點屬性 窗格檢視即時記錄檔選項](./media/container-insights-live-logs/node-properties-live-logs-01.png)  

記錄檔和事件訊息會限制根據檢視中選取資源類型。

| 檢視 | 資源類型 | 記錄檔或事件 | 顯示資料 |
|------|---------------|--------------|----------------|
| 節點 | 節點 | Event | 選取節點時不會篩選事件，並顯示整個叢集的 Kubernetes 事件。 窗格標題將會顯示叢集的名稱。 |
| 節點 | Pod | Event | 選取的 pod 時為其命名空間篩選事件。 窗格標題將會顯示 pod 的命名空間。 | 
| 控制器 | Pod | Event | 選取的 pod 時為其命名空間篩選事件。 窗格標題將會顯示 pod 的命名空間。 |
| 控制器 | 控制器 | Event | 選取控制站時為其命名空間篩選事件。 窗格標題將會顯示控制器的命名空間。 |
| 節點/控制站/容器 | 容器 | 記錄 | 窗格標題將會顯示用來分組的 pod 容器名稱。 |

如果使用 AAD 搭配 SSO 設定 AKS 叢集，系統會在您第一次使用時，於該瀏覽器工作階段期間提示您進行驗證。 選取您的帳戶，然後向 Azure 完成驗證。  

成功驗證之後，即時記錄窗格將會出現在中間窗格的下方區段。 如果擷取狀態指示器會在窗格最右邊顯示綠色核取記號，這表示它可以擷取資料。
    
  ![即時記錄窗格中已擷取的資料](./media/container-insights-live-logs/live-logs-pane-01.png)  

在 [搜尋] 列中，您可以篩選以反白顯示文字中的記錄檔或事件，並在最右邊的搜尋列中的關鍵字，它會顯示符合篩選出的多少筆結果。   

  ![即時記錄窗格中的篩選範例](./media/container-insights-live-logs/live-logs-pane-filter-example-01.png)

若要暫停 autoscroll 及控制行為的窗格，可讓您以手動方式捲動以查看新的資料讀取，按一下**捲**選項。 若要重新啟用自動捲動，只要按一下**捲**選項一次。 您也可以暫停擷取記錄檔或事件資料上即可**暫停**選項，然後當您準備好繼續時，只要按一下**播放**。  

![即時記錄窗格中的暫停即時檢視](./media/container-insights-live-logs/live-logs-pane-pause-01.png)

您可以移至 Azure 監視器的記錄檔以查看歷程記錄的容器記錄，方法是選取**檢視容器記錄檔**從下拉式清單**analytics 檢視**。

## <a name="next-steps"></a>後續步驟
- 若要繼續了解如何使用 Azure 監視器並監視您 AKS 叢集的其他層面，請參閱[檢視 Azure Kubernetes 服務健康情況](container-insights-analyze.md)。
- 檢視[記錄查詢範例](container-insights-log-search.md#search-logs-to-analyze-data)以查看預先定義的查詢和範例，以評估或自訂警示、 視覺化，或分析您的叢集。
