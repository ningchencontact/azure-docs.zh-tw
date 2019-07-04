---
title: 容器支援
titleSuffix: Azure Cognitive Services
description: 了解如何建立 azure kubernetes (AKS) 資源。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 1d68c08f6dfca74c38973af1686d614f3f10cc28
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67455132"
---
## <a name="create-an-azure-kubernetes-service-aks-cluster-resource"></a>建立 Azure Kubernetes Service (AKS) 叢集資源

1. 移至[建立](https://ms.portal.azure.com/#create/microsoft.aks)為 Kubernetes 服務。

1. 在 **基本概念**索引標籤上，輸入下列詳細資料：

    |設定|值|
    |--|--|
    |訂用帳戶|選取適當的訂用帳戶|
    |資源群組|選取可用的資源群組|
    |Kubernetes 叢集名稱|所需的名稱 （小寫）|
    |區域|選取的鄰近位置|
    |Kubernetes 版本|1.12.8 （預設值）|
    |DNS 名稱前置詞|自動建立，但可以選擇性地覆寫|
    |節點大小|標準 DS2 v2:<br>`2 vCPUs`、 `7 GB`|
    |節點計數|將滑桿保留為預設值|

1. 在上**擴展**索引標籤上，保留*虛擬節點*並*虛擬機器擴展集*預設值。
1. 在 **驗證**索引標籤上，保留*服務主體*並*啟用 RBAC*預設值。
1. 在 **網路**索引標籤上，輸入下列選擇：

    |設定|值|
    |--|--|
    |HTTP 應用程式路由|否|
    |網路設定|基本|

1. 在上**監視**索引標籤上，請確認*啟用容器監視*設為**是**，並將*Log Analytics 工作區*為其預設值
1. 在 **標記**索引標籤上，現在空白的名稱/值組。
1. 按一下 **檢閱並建立**。
1. 通過驗證之後，請按一下**建立**。

> [!NOTE]
> 如果驗證失敗，它可能是因為*服務主體*時發生錯誤。 瀏覽回到*驗證*索引標籤，然後回到*檢閱 + 建立*驗證應該在其中執行，然後再傳遞。
