---
title: 如何啟動、停止和刪除您的 Azure 春季雲端應用程式 |Microsoft Docs
description: 如何啟動、停止和刪除您的 Azure 春季雲端應用程式
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: jeconnoc
ms.openlocfilehash: 9f537ab425428728137e04713e434d8dc09e065a
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607732"
---
# <a name="how-to-start-stop-and-delete-your-azure-spring-cloud-application"></a>如何啟動、停止和刪除您的 Azure 春季雲端應用程式

本指南說明如何使用 Azure 入口網站或 CLI，在 Azure 春季雲端中變更應用程式的狀態。

## <a name="using-the-azure-portal"></a>使用 Azure 入口網站

部署應用程式之後，您可以使用 Azure 入口網站來**啟動**、**停止**和**刪除**它。

1. 在 Azure 入口網站中，移至您的 Azure 春季雲端服務實例。
1. 選取 [**應用程式儀表板**] 索引標籤。
1. 選取您想要變更其狀態的應用程式。
2. 在該應用程式的 [**總覽**] 頁面中，尋找 [**啟動/停止**]、[**重新開機**] 和 [**刪除**] 應用程式的按鈕。

## <a name="using-the-azure-cli"></a>使用 Azure CLI

> [!NOTE]
> 您可以使用選擇性參數，並使用 Azure CLI 設定預設值。 閱讀我們的[參考檔](spring-cloud-cli-reference.md)以深入瞭解。  

安裝適用于 Azure CLI 的春季雲端延伸模組：

```azurecli
az extension add --name spring-cloud
```

* 若要啟動您的應用程式：
    ```azurecli
    az spring-cloud app start -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* 若要停止您的應用程式：
    ```azurecli
    az spring-cloud app stop -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* 若要重新開機應用程式：
    ```azurecli
    az spring-cloud app restart -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* 若要刪除您的應用程式：
    ```azurecli
    az spring-cloud app delete -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```
