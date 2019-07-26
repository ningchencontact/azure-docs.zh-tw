---
title: 建立 Azure Kubernetes Service 叢集資源
titleSuffix: Azure Cognitive Services
description: 瞭解如何建立 Azure Kubernetes Service (AKS) 資源。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/3/2019
ms.author: dapine
ms.openlocfilehash: 5e442f38a8e81170eeeac2f31f00a7d0eeb08d2b
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377408"
---
## <a name="create-an-azure-kubernetes-service-cluster-resource"></a>建立 Azure Kubernetes Service 叢集資源

1. 移至 [ [Azure Kubernetes Service](https://ms.portal.azure.com/#create/microsoft.aks)], 然後選取 [**建立**]。

1. 在 [**基本**] 索引標籤上, 輸入下列資訊:

    |設定|值|
    |--|--|
    |訂閱|選取適當的訂用帳戶。|
    |資源群組|選取可用的資源群組。|
    |Kubernetes 叢集名稱|輸入名稱 (小寫)。|
    |區域|選取附近的位置。|
    |Kubernetes 版本|1.12.8 (預設值)。|
    |DNS 名稱首碼|會自動建立, 但您可以覆寫。|
    |節點大小|標準 DS2 v2:<br>`2 vCPUs`、 `7 GB`|
    |節點計數|將滑杆保持為預設值。|

1. 在 [**調整規模**] 索引標籤上, 將 [**虛擬節點**和**虛擬機器擴展集 (預覽)** ] 設定為預設值。
1. 在 [**驗證**] 索引標籤上, 將 [**服務主體**] 和 [**啟用 RBAC** ] 設定為預設值。
1. 在 [**網路**功能] 索引標籤上, 輸入下列選項:

    |設定|值|
    |--|--|
    |HTTP 應用程式路由|否|
    |網路設定|基本|

1. 在 [**監視**] 索引標籤上, 確定 [**啟用容器監視**] 已設定為 **[是]** , 並將 [ **Log Analytics 工作區**] 保留為預設值。
1. 在 [**標記**] 索引標籤上, 將 [名稱/值組] 保留空白。
1. 選取 [**審查並建立**]。
1. 通過驗證之後, 請選取 [**建立**]。

> [!NOTE]
> 如果驗證失敗, 可能是因為「服務主體」錯誤。 返回 [**驗證**] 索引標籤, 然後返回 [**檢查 + 建立**], 其中應執行驗證, 然後傳遞。
