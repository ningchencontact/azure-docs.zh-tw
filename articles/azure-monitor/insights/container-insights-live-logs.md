---
title: 即時檢視適用於容器的 Azure 監視器記錄 | Microsoft Docs
description: 本文說明容器記錄 (stdout/stderr) 和事件的即時觀點, 而不需使用 kubectl 與容器的 Azure 監視器。
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
ms.date: 07/12/2019
ms.author: magoedte
ms.openlocfilehash: 2eab6fa75e4adbbde7bcf20f18301a1e516235c2
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035349"
---
# <a name="how-to-view-logs-and-events-in-real-time-preview"></a>如何即時查看記錄和事件 (預覽)
適用于容器的 Azure 監視器包含功能 (目前處於預覽狀態), 可提供您 Azure Kubernetes Service (AKS) 容器記錄 (stdout/stderr) 和事件的即時視圖, 而不需要執行 kubectl 命令。 當您選取其中一個選項時, 新的窗格會出現在 [**節點**]、[**控制器**] 和 [**容器**] 視圖的 [效能] 資料表下方。 它會顯示容器引擎所產生的即時記錄和事件, 以進一步協助針對問題進行即時疑難排解。

>[!NOTE]
>需要具備叢集資源的**參與者**存取權, 這項功能才能正常執行。
>

即時記錄支援三種不同的方法來控制記錄的存取:

1. 已啟用不具 Kubernetes RBAC 授權的 AKS
2. 已使用 Kubernetes RBAC 授權啟用的 AKS
3. AKS 已啟用 Azure Active Directory (AD) SAML 型單一登入

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

2. 如果您是第一次設定它, 請執行下列命令來套用叢集規則系結: `kubectl create -f LogReaderRBAC.yaml`。 如果您先前已在推出即時事件記錄檔之前啟用支援即時記錄預覽, 請執行下列命令來更新您的設定`kubectl apply -f LogReaderRBAC.yaml`:。

## <a name="configure-aks-with-azure-active-directory"></a>使用 Azure Active Directory 設定 AKS

AKS 可設定為使用 Azure Active Directory (AD) 進行使用者驗證。 如果您是第一次設定它, 請參閱[整合 Azure Active Directory 與 Azure Kubernetes Service](../../aks/azure-ad-integration.md)。 在建立[用戶端應用程式](../../aks/azure-ad-integration.md#create-the-client-application)的步驟中, 指定下列各項:

-  **重新導向 URI**：需要建立兩個**Web**應用程式類型。 第一個 [基底 url] `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`值應該是, 而第二個`https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`[基底 url] 值應該是。
- 註冊應用程式之後, 從 [**總覽**] 頁面的左側窗格中選取 [**驗證**]。 在 [**驗證**] 頁面的 [**高級設定**] 底下, 隱含地授與**存取權杖**和**識別碼權杖**, 然後儲存您的變更。

>[!NOTE]
>只有在初始部署新的 AKS 叢集時, 才可以完成使用單一登入的 Azure Active Directory 來設定驗證。 您無法針對已經部署的 AKS 叢集設定單一登入。
  
>[!IMPORTANT]
>如果您已使用更新的 URI 重新設定使用者驗證 Azure AD, 請清除瀏覽器的快取, 以確保已下載並套用更新的驗證權杖。   

## <a name="view-live-logs-and-events"></a>查看即時記錄和事件

您可以從 [**節點**]、[**控制器**] 和 [**容器**] 視圖, 查看容器引擎產生的即時記錄事件。 在 [屬性] 窗格中, 您可以選取 [**查看即時資料 (預覽)** ] 選項, 窗格會顯示在 [效能資料] 資料表下方, 您可以在其中查看連續資料流程中的記錄和事件。

![節點屬性窗格 view live logs 選項](./media/container-insights-live-logs/node-properties-live-logs-01.png)  

記錄和事件訊息會根據在視圖中選取的資源類型而受到限制。

| 檢視表 | 資源類型 | 記錄或事件 | 呈現的資料 |
|------|---------------|--------------|----------------|
| 節點 | 節點 | Event - 事件 | 當選取節點時, 事件不會經過篩選, 而且會顯示整個叢集的 Kubernetes 事件。 窗格標題會顯示叢集的名稱。 |
| 節點 | Pod | Event - 事件 | 選取 pod 時, 事件會篩選到其命名空間。 窗格標題會顯示 pod 的命名空間。 | 
| 控制器 | Pod | Event - 事件 | 選取 pod 時, 事件會篩選到其命名空間。 窗格標題會顯示 pod 的命名空間。 |
| 控制器 | 控制器 | Event - 事件 | 選取控制器時, 事件會篩選到其命名空間。 窗格標題會顯示控制器的命名空間。 |
| 節點/控制器/容器 | 容器 | 記錄檔 | 窗格標題會顯示容器所群組的 pod 名稱。 |

如果使用 AAD 搭配 SSO 設定 AKS 叢集，系統會在您第一次使用時，於該瀏覽器工作階段期間提示您進行驗證。 選取您的帳戶，然後向 Azure 完成驗證。  

成功驗證之後，即時記錄窗格將會出現在中間窗格的下方區段。 如果擷取狀態指示器會在窗格最右邊顯示綠色核取記號，這表示它可以擷取資料。
    
  ![即時記錄窗格中已擷取的資料](./media/container-insights-live-logs/live-logs-pane-01.png)  

在搜尋列中, 您可以依關鍵字進行篩選, 以反白顯示記錄檔或事件中的文字, 並在最右邊的搜尋列中, 顯示有多少結果符合篩選準則。

  ![即時記錄窗格中的篩選範例](./media/container-insights-live-logs/live-logs-pane-filter-example-01.png)

在查看事件時, 您可以使用搜尋列右邊的 [**篩選**] 膠囊按鈕, 進一步限制結果。 視您選取的資源而定, 膠囊按鈕會列出要從中選擇的 pod、命名空間或叢集。  

若要暫停 autoscroll 並控制窗格的行為, 並可讓您手動流覽新的資料讀取, 請按一下 [**滾動**] 選項。 若要重新啟用 autoscroll, 只要再次按一下 [**滾動**條] 選項即可。 您也可以按一下 [**暫停**] 選項來暫停抓取記錄檔或事件資料, 當您準備好繼續時, 只要按一下 [**播放**] 即可。  

![即時記錄窗格中的暫停即時檢視](./media/container-insights-live-logs/live-logs-pane-pause-01.png)

您可以從 [**分析] 中**的下拉式清單視圖選取 [**查看容器記錄**], 以移至 Azure 監視器記錄以查看歷程記錄容器記錄。

## <a name="next-steps"></a>後續步驟

- 若要繼續了解如何使用 Azure 監視器並監視您 AKS 叢集的其他層面，請參閱[檢視 Azure Kubernetes 服務健康情況](container-insights-analyze.md)。

- 查看[記錄查詢範例](container-insights-log-search.md#search-logs-to-analyze-data), 以查看預先定義的查詢和範例, 以評估或自訂警示、視覺化或分析您的叢集。
