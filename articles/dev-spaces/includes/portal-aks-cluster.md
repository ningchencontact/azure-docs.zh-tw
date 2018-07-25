---
title: 包含檔案
description: 包含檔案
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: 05736495d0d4a0c3a5072d29ad27801b6d4a7241
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2018
ms.locfileid: "37967675"
---
## <a name="create-a-kubernetes-cluster-enabled-for-azure-dev-spaces"></a>建立已針對 Azure Dev Spaces 啟用的 Kubernetes 叢集

1. 在 http://portal.azure.com 登入 Azure 入口網站。
1. 選擇 [建立資源] > 搜尋 **Kubernetes** > 選取 [Kubernetes Service] > [建立]。

   完成建立 AKS 叢集表單中每個標題底下的下列步驟。

    - **專案詳細資料**：選取 Azure 訂用帳戶，以及新的或現有的 Azure 資源群組。
    - **叢集詳細資料**：輸入 AKS 叢集的名稱、地區 (目前，您必須選擇 EastUS、美國中部、WestEurope、WestUS2、CanadaCentral 或 CanadaEast)、版本及 DNS 名稱前置詞。
    - **級別**：選取 AKS 代理程式節點的 VM 大小和節點數目。 如果您開始使用 Azure Dev Spaces，一個節點就足以瀏覽所有功能。 節點計數可以輕易在叢集部署後隨時調整。 請注意，VM 大小無法在 AKS 叢集建立後變更。 不過，部署 AKS 叢集後，您就可以輕鬆地建立具有較大 VM 的新 AKS 叢集，而如果您需要相應增加，請使用 Dev Spaces 重新部署到該較大叢集。

   務必選擇 Kubernetes 1.10.3 版或更新版本。

   ![Kubernetes 組態設定](../media/common/Kubernetes-Create-Cluster-2.PNG)

   完成時，請選取 [下一步: 網路功能]。

1. 確定已啟用 [Http 應用程式路由]。

   ![啟用 Http 應用程式路由](../media/common/Kubernetes-Create-Cluster-3.PNG)

    > [!IMPORTANT]
    > 您務必要在建立 AKS 叢集時啟用 [Http 應用程式路由]。 您之後無法變更此設定。

1. 選擇您想要的角色型存取控制 (RBAC) 設定。 Azure Dev Spaces 會在啟用或停用 RBAC 的情況下支援叢集。

    ![RBAC 設定](../media/common/k8s-RBAC.PNG)

1. 選取 [檢閱 + 建立]，然後在完成時選取 [建立]。
