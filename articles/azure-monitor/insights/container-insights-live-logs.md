---
title: 即時檢視適用於容器的 Azure 監視器記錄 | Microsoft Docs
description: 本文說明如何在不搭配使用 kubectl 與適用於容器的 Azure 監視器的情況下，即時檢視容器記錄 (stdout/stderr)。
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
ms.date: 12/06/2018
ms.author: magoedte
ms.openlocfilehash: da11bb0669bf6bde2c65b2a7a0badaa1ae35abda
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2018
ms.locfileid: "53189113"
---
# <a name="how-to-view-container-logs-real-time-with-azure-monitor-for-containers-preview"></a>如何使用適用於容器的 Azure 監視器 (預覽) 即時檢視容器記錄
此功能目前處於預覽狀態，可提供您 Azure Kubernetes Service (AKS) 容器記錄 (stdout/stderr) 的即時檢視，而不需執行 kubectl 命令。 當您選取此選項時，新窗格會顯示於 [容器] 檢視上的容器效能資料表格下方，而其會顯示容器引擎所產生的即時引擎，以進一步協助對問題進行即時疑難排解。  

即時記錄支援三種不同的方法來控制記錄的存取：

1. 已啟用不具 Kubernetes RBAC 授權的 AKS 
2. 已使用 Kubernetes RBAC 授權啟用的 AKS
3. 已使用 Azure Active Directory (AD) SAML 型單一登入啟用的 AKS 

## <a name="kubernetes-cluster-without-rbac-enabled"></a>已啟用不具 RBAC 的 Kubernetes 叢集
 
如果您的 Kubernetes 叢集不是使用 Kubernetes RBAC 授權所設定或已與 Azure AD 單一登入整合，則您不需遵循這些步驟。 由於 Kubernetes 授權會使用 kube-api，因此，唯讀權限是必要的。

## <a name="kubernetes-rbac-authorization"></a>Kubernetes RBAC 授權
如果您已啟用 Kubernetes RBAC 授權，您將需套用叢集角色繫結。 下列範例步驟將示範如何從這個 yaml 設定範本設定叢集角色繫結。   

1. 複製並貼上這個 yaml 檔案，然後將它儲存為 LogReaderRBAC.yaml。  

   ```
   kind: ClusterRole 
   apiVersion: rbac.authorization.k8s.io/v1 
   metadata:   
      name: containerHealth-log-reader 
   rules: 
      - apiGroups: [""]   
        resources: ["pods/log"]   
        verbs: ["get"] 
   --- 
   kind: ClusterRoleBinding 
   apiVersion: rbac.authorization.k8s.io/v1 
   metadata:   
      name: containerHealth-read-logs-global 
   subjects:   
      - kind: User     
        name: clusterUser
        apiGroup: rbac.authorization.k8s.io 
    roleRef:   
       kind: ClusterRole
       name: containerHealth-log-reader
       apiGroup: rbac.authorization.k8s.io 
   ```

2. 執行下列命令來建立叢集規則繫結：`kubectl create -f LogReaderRBAC.yaml`。 

## <a name="configure-aks-with-azure-active-directory"></a>使用 Azure Active Directory 設定 AKS
AKS 可設定為使用 Azure Active Directory (AD) 進行使用者驗證。 如果您第一次設定此項，請參閱[將 Azure Active Directory 與 Azure Kubernetes Service 整合](../../aks/aad-integration.md)。 在執行建立[用戶端應用程式](../../aks/aad-integration.md#create-client-application)並指定**重新導向 URI** 的步驟期間，您需要將另一個 URI 新增至清單 ** https://ininprodeusuxbase.microsoft.com/***。  

>[!NOTE]
>使用 Azure Active Directory 設定驗證以進行單一登入，只能在新 AKS 叢集的初始部署期間完成。 您無法針對已經部署的 AKS 叢集設定單一登入。  
> 

## <a name="view-live-logs"></a>檢視即時記錄
當您檢視**容器**時，可以**檢視容器記錄**或**檢視容器即時記錄**。  當您選取 [檢視容器即時記錄] 時，新窗格會出現於容器效能資料表格下方，而其會顯示容器引擎所產生的即時引擎，可進一步協助對問題進行即時疑難排解。  
1. 登入 [Azure 入口網站](https://portal.azure.com)。 
2. 從 **Microsoft Azure** 功能表，選取 [監視器]，然後選取 [容器]。  
3. 從 [受監視的容器] 檢視下方的清單中選取容器。  
4. 選取 [容器] 檢視，隨即會在所選取容器的屬性面板上列出 [檢視容器即時記錄]。  
5. 如果使用 AAD 搭配 SSO 設定 AKS 叢集，系統會在您第一次使用時，於該瀏覽器工作階段期間提示您進行驗證。 選取您的帳戶，然後向 Azure 完成驗證。  

成功驗證之後，即時記錄窗格將會出現在中間窗格的下方區段。 如果擷取狀態指示器會在窗格最右邊顯示綠色核取記號，這表示它可以擷取資料。
    
  ![即時記錄窗格中已擷取的資料](./media/container-insights-live-logs/live-logs-pane-01.png)  

在搜尋列中，您可以依關鍵字進行篩選，在記錄中醒目提示該文字。   

  ![即時記錄窗格中的篩選範例](./media/container-insights-live-logs/live-logs-pane-filter-01.png)

若要暫停自動捲動並控制窗格的行為，而且可讓您以手動方式捲動來查看新的記錄資料讀取，請按一下 [捲動] 選項。  若要重新啟用自動捲動，只要再按一下 [捲動] 選項即可。  您也可以按一下 [暫停] 選項來暫停擷取記錄資料，然後當您準備好繼續時，只要按一下 [播放] 即可。  

![即時記錄窗格中的暫停即時檢視](./media/container-insights-live-logs/live-logs-pane-pause-01.png)

## <a name="next-steps"></a>後續步驟
若要繼續了解如何使用 Azure 監視器並監視您 AKS 叢集的其他層面，請參閱[檢視 Azure Kubernetes 服務健康情況](container-insights-analyze.md)。