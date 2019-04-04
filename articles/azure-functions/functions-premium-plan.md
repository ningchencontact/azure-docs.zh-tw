---
title: Azure Functions 的進階方案 （預覽） |Microsoft Docs
description: 詳細資料和 Azure Functions 的進階組態選項 (VNet，沒有冷啟動 」、 「 無限制的執行持續時間) 計劃。
services: functions
author: jeffhollan
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: jehollan
ms.openlocfilehash: ca65b6a1691a870054682b36109f2bdc10d4ad98
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2019
ms.locfileid: "58918700"
---
# <a name="azure-functions-premium-plan-preview"></a>Azure Functions 的進階方案 （預覽）

Azure Functions Premium 方案是函式應用程式的裝載選項。 「 進階 」 方案提供的功能，像是 VNet 連線能力、 沒有冷啟動，以及進階硬體。  可以將多個函式應用程式部署到相同的進階方案，並計劃可讓您設定計算執行個體大小、 基本方案大小，以及最大的計畫大小。  「 進階 」 方案和其他的計劃，以及裝載型別比較，請參閱[函式級別和裝載選項](functions-scale.md)。

> [!NOTE]
> 高階方案預覽目前支援執行.NET、 節點或 Java 中透過 Windows 基礎結構的函式。

## <a name="create-a-premium-plan"></a>建立進階方案

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]

您也可以從 Azure CLI 來建立進階方案

```azurecli-interactive
az functionapp plan create -g <resource-group> -n <plan-name> -l <region> --number-of-workers 1 --sku EP1
```

## <a name="features"></a>特性

部署到進階方案的函式應用程式提供下列功能。

### <a name="pre-warmed-instances"></a>已預先準備的執行個體

未發生任何事件和執行立即在取用方案中，您的應用程式可能會相應減少為零的執行個體。 當有新的事件時的新執行個體必須在其上執行的應用程式進行特製化。  特製化的新執行個體可能需要一些時間，取決於應用程式。  第一次呼叫此額外的延遲通常稱為應用程式冷啟動。

在 「 進階 」 方案，您可以讓您預先準備指定數目的執行個體上的應用程式。  已預先準備的執行個體也可讓您預先調整之前高負載的應用程式。 應用程式相應放大，它首先會調整成預先 warmed 的執行個體。 其他執行個體會繼續緩衝和暖立即在下一個調整規模作業的準備。 擁有已預先準備的執行個體的緩衝區，您可以有效避免冷啟動延遲。  已預先準備的執行個體是 「 進階 」 方案的功能和您需要保留至少一個執行的執行個體，可在所有時間計劃是使用中。

您也可以選取 Azure 入口網站中設定預先準備的執行個體數目**相應放大**中**平台功能** 索引標籤。

![彈性的調整規模設定](./media/functions-premium-plan/scale-out.png)

您也可以使用 Azure CLI 來設定應用程式已預先準備的執行個體

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.preWarmedInstanceCount=<desired_prewarmed_count> --resource-type Microsoft.Web/sites
```

### <a name="private-network-connectivity"></a>私人網路連線能力

部署到進階方案的 azure Functions 會利用[新的 VNet 整合的 web 應用程式](../app-service/web-sites-integrate-with-vnet.md#new-vnet-integration)。  設定時，您的應用程式可以與您的 VNet 內的資源通訊，或透過服務端點保護。  IP 限制也有應用程式，可限制連入流量。

指派給函式應用程式的子網路，在進階方案，您會需要有足夠的 IP 位址的子網路的每個可能的執行個體。 雖然執行個體數目上限可能有所差異，在預覽期間，我們需要至少 100 個可用的位址與 IP 區塊。

如需詳細資訊，請參閱[將您的函式應用程式與 VNet 整合](functions-create-vnet.md)。

### <a name="rapid-elastic-scale"></a>快速彈性延展

取用方案為使用相同的快速調整邏輯應用程式，會自動新增額外的計算執行個體。  若要深入了解如何調整的運作方式，請參閱[函式級別和裝載](./functions-scale.md#how-the-consumption-and-premium-plans-work)。

### <a name="unbounded-run-duration"></a>執行持續時間的未繫結

取用方案中的 azure 函式僅限於單一執行 10 分鐘的時間。  在 「 進階 」 方案，執行持續期間預設值為 30 分鐘，以防止失控的執行。 不過，您可以[修改 host.json 組態](./functions-host-json.md#functiontimeout)若要將此進階方案的應用程式的未繫結。

## <a name="plan-and-sku-settings"></a>計劃和 SKU 的設定

當您建立方案時，您會設定兩個設定： 執行個體 （或計劃大小） 的最小數目和最大的高載的限制。  進階方案的最小執行個體為 1，而最大的高載，在預覽期間為 20。  最小執行個體都已保留，一律執行。

> [!IMPORTANT]
> 向您收取每個執行個體配置的最小執行個體計數無論或不執行函式。

如果您的應用程式需要超過您的方案大小的執行個體，它可以繼續相應放大，直到執行個體數目達到最大的高載的限制。  您會收到帳單，針對超過您的方案大小的執行個體而它們正在執行且可租給您。  我們將盡力調整您的應用程式至其定義的最大限制，而您的應用程式期間都保證最低的方案執行個體。

您可以設定的計畫大小和最大值，在 Azure 入口網站中選取**相應放大**計劃] 或 [函數應用程式部署至該方案中的選項 (底下**平台功能**)。

您也可以增加從 Azure CLI 的最大的高載限制：

```azurecli-interactive
az resource update -g <resource_group> -n <premium_plan_name> --set properties.maximumElasticWorkerCount=<desired_max_burst> --resource-type Microsoft.Web/serverfarms 
```

### <a name="available-instance-skus"></a>可用的執行個體 Sku

在建立我們的調整您的計劃時，您可以選擇三個執行個體大小。  您將支付的核心和每秒取用的記憶體總數。  您的應用程式可以自動相應放大至多個執行個體所需。  

|SKU|核心|記憶體|儲存體|
|--|--|--|--|
|EP1|1|3.5GB|250 GB|
|EP2|2|7 GB|250 GB|
|EP3|4|14 GB|250 GB|

## <a name="regions"></a>區域

以下是針對公開預覽版目前支援的區域。

|區域|
|--|
|澳洲東部|
|澳洲東南部|
|加拿大中部|
|印度中部|
|美國中部|
|東亞|
|美國東部 2|
|法國中部|
|日本西部|
|南韓中部|
|北歐|
|美國中南部|
|印度南部|
|東南亞|
|英國西部|
|西歐|
|印度西部|
|美國西部|

## <a name="known-issues"></a>已知問題

您可以追蹤的已知問題的狀態[公開預覽版的 GitHub](https://github.com/Azure/Azure-Functions/wiki/Premium-plan-known-issues)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [了解 Azure Functions 的級別和裝載選項](functions-scale.md)
