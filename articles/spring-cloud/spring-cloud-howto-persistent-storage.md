---
title: 如何在 Azure Spring Cloud 中使用持續性儲存體 | Microsoft Docs
description: 如何在 Azure Spring Cloud 中使用持續性儲存體
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: jeconnoc
ms.openlocfilehash: 8c57698471d1363438c10e5806f9ed6f1da5333f
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038447"
---
# <a name="how-to-use-persistent-storage-in-azure-spring-cloud"></a>如何在 Azure Spring Cloud 中使用持續性儲存體

Azure Spring Cloud 能為您的應用程式提供兩種類型的儲存體：持續性和暫時性。  Azure Spring Cloud 預設會針對每個應用程式執行個體啟用暫時性儲存體。 暫時性儲存體具有 5GB 的限制，且其預設掛接路徑為 `/tmp`。

> [!WARNING]
> 重新啟動應用程式執行個體將會永久刪除其相關聯的暫時性儲存體。

持續性儲存體是 Azure 所管理的檔案共用容器，並會基於每個應用程式範圍進行配置。 儲存在持續性儲存體中的資料會共用至應用程式的所有執行個體。 Azure Spring Cloud 服務執行個體可以具有最多 10 個啟用持續性磁碟的應用程式，且每個應用程式都會有 50GB 的持續性儲存體。 持續性儲存體的預設掛接路徑為 `/persistent`。

## <a name="enable-persistent-storage-using-the-azure-portal"></a>使用 Azure 入口網站來啟用持續性儲存體

1. 在您的 Azure Spring Cloud 服務頁面中，選取 [應用程式儀表板]  ，然後選取需要持續性儲存體的應用程式。
1. 在 [概觀]  索引標籤中，找到 [持續性儲存體]  屬性，然後選取 [已停用]  。
1. 按一下 [啟用]  以啟用持續性儲存體，然後選取 [確定]  按鈕來套用變更。

啟用持續性儲存體之後，其大小和路徑會顯示在 [概觀]  頁面的 [持續性儲存體]  屬性中。

> [!WARNING]
> [停用]  持續性儲存體將會解除配置該應用程式的儲存體。  該儲存體帳戶中的所有資料都將遺失。 

## <a name="enable-persistent-storage-using-the-azure-cli"></a>使用 Azure CLI 來啟用持續性儲存體

建立已啟用持續性磁碟的應用程式：
 
```azurecli
az spring-cloud app create -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
```

在現有應用程式中啟用持續性儲存體：

```azurecli
az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
```

在現有應用程式中刪除持續性儲存體：

> [!WARNING]
> 停用持續性儲存體將會解除配置該應用程式的儲存體，並永久遺失儲存在該儲存體內的所有資料。 

```azurecli
az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage false
```

## <a name="next-steps"></a>後續步驟

了解[應用程式和服務配額](spring-cloud-quotas.md)，或是了解如何[手動調整您的應用程式](spring-cloud-tutorial-scale-manual.md)。