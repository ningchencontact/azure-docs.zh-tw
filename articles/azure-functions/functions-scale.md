---
title: Azure Functions 的規模調整和主控 |Microsoft Docs
description: 了解如何選擇 Azure Functions 取用方案或進階方案。
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: azure 函式、 函式，取用方案、 進階方案、 事件處理，webhook，動態計算，無伺服器架構
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 03/27/2019
ms.author: glenga
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 459758142c94ddfd244df1427dd5632186efc0de
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2019
ms.locfileid: "59266917"
---
# <a name="azure-functions-scale-and-hosting"></a>Azure Functions 的規模調整和主控

在兩個不同的計劃中，執行 azure Functions:取用方案和進階方案 （公開預覽狀態）。 執行您的程式碼時，取用方案會自動新增的計算能力。 您的應用程式是相應放大，以處理負載，需要時，且程式碼停止執行時相應減少。 您不必支付閒置虛擬機器的費用，或預先保留容量。  「 進階 」 方案會也會自動縮放及執行您的程式碼時新增額外的計算能力。  Premium 方案隨附其他功能，例如進階計算執行個體，可將無限期保留執行個體暖和 VNet 連線能力。  如果您有現有的 App Service 方案，您也可以執行您的函式應用程式中。

> [!NOTE]  
> 兩者[進階方案](https://aka.ms/functions-premiumplan)並[適用於 Linux 的耗用量計劃](https://azure.microsoft.com/updates/azure-functions-consumption-plan-for-linux-preview/)目前處於預覽狀態。

如果您還不熟悉 Azure Functions，請參閱 [Azure Functions 概觀](functions-overview.md)。

建立函式應用程式時，您可以針對應用程式中的函式選擇主控方案。 不管用哪一種方案，都是以 Azure Functions 主機的執行個體來執行函式。 方案的類型控制：

* 主控件執行個體如何向外延展。
* 每個主控件可用的資源。
* 執行個體功能，例如 VNet 連線能力。

> [!NOTE]
> 您可以藉由變更計劃屬性，函式應用程式資源耗用量和進階方案之間切換。

## <a name="consumption-plan"></a>取用方案

當您使用取用方案時，Azure Functions 主控件執行個體以動態方式加入和移除的內送事件數量而定。 此無伺服器方案會自動調整，您只需支付函式執行時使用的計算資源。 在取用方案中，函式執行會在一段可設定的時間之後逾時。

帳單是根據執行數目、執行時間以及使用的記憶體。 帳單會跨函式應用程式內的所有函式進行彙總。 如需詳細資訊，請參閱 [Azure Functions 價格頁面]。

取用方案是預設主控方案，具有下列優點︰

* 只有當您的函式執行時才需付費。
* 自動調整規模，即使在高負載期間也會。

## <a name="premium-plan-public-preview"></a>進階方案 （公開預覽）

當您使用 「 進階 」 方案時，Azure Functions 主控件執行個體快速加入和移除就像取用方案的內送事件數量而定。  不過，「 進階 」 方案也提供：

* 一律準備執行個體，以避免任何冷啟動。
* VNet 連線能力。
* 無限制的執行持續時間。
* 進階的執行個體大小 （一個核心、 雙核心和四個核心執行個體）。
* 可預測的定價選項。
* 用於多個函式應用程式的計劃的高密度應用程式配置。

如何設定這些選項的詳細資訊可在[Azure Functions 的進階方案](functions-premium-plan.md)。

而不是每個執行和耗用記憶體的計費，計費根據 core GB 秒數以及所需和保留執行個體使用的數目。  要暖完全次，因此有固定的每月成本，每個作用中 （不論執行數目） 的方案需要至少一個執行個體。

Azure Functions 的進階方案，在下列情況下，請考慮：
* 您的函式應用程式會連續執行或接近連續執行。 在此情況下，App Service 方案可以更符合成本效益。
* 您需要的 CPU 或記憶體選項比取用方案所提供的更多。
* 您的程式碼必須執行時間超過[允許的最大執行時間](#timeout)取用方案上。
* 您需要 App Service 方案才有提供的功能，例如 App Service 環境、VNET/VPN 連線和較大 VM 大小的支援。

> [!NOTE]
> 高階方案預覽目前支援執行.NET、 節點或 Java 中透過 Windows 基礎結構的函式。

當執行進階方案的 JavaScript 函式，您應該選擇 Vcpu 數目較少的執行個體。 如需詳細資訊，請參閱 <<c0> [ 選擇單一核心 Premium 方案](functions-reference-node.md#considerations-for-javascript-functions)。  

## <a name="app-service-plan"></a>App Service 方案

函式應用程式也可以為其他 App Service 應用程式 （基本、 標準、 進階和隔離 Sku） 相同的專用 Vm 上執行。 App Service 方案支援 Linux。

在下列情況中請考慮使用 App Service 方案︰

* 您有現有的、使用量過低的 VM 已在執行其他 App Service 執行個體。
* 您想要在 Linux 上執行函數應用程式，或想要在其上提供自訂映像以執行函數。

您需支付相同函式應用程式，App Service 方案中其他的 App Service 資源，例如 web 應用程式一樣。 如需 App Service 方案運作方式的詳細資訊，請參閱 [Azure App Service 方案深入概觀](../app-service/overview-hosting-plans.md)。 

使用 App Service 方案時，您可以透過手動新增更多 VM 執行個體來相應放大，或者您可以啟用自動規模調整。 如需詳細資訊，請參閱[手動或自動調整執行個體計數規模](../azure-monitor/platform/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json)。 您也可以透過選擇不同的 App Service 方案來相應增加。 如需詳細資訊，請參閱[在 Azure 中為應用程式進行相應增加](../app-service/web-sites-scale.md)。 

在 App Service 方案上執行 JavaScript 函式時，您應該選擇 vCPU 數目較少的方案。 有关详细信息，请参阅[选择单核应用服务计划](functions-reference-node.md#choose-single-vcpu-app-service-plans)。 
<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 

### <a name="always-on"></a> Always On

如果您執行 App Service 方案，應該啟用 [永遠開啟] 設定，以讓您的函數應用程式能夠正確執行。 在 App Service 方案中，函式的執行階段會在無作用幾分鐘後進入閒置狀態，因此只有 HTTP 觸發程序會「喚醒」您的函式。 只有 App Service 方案具備「永遠開啟」選項。 在取用方案中，平台會自動啟動函數應用程式。

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]

## <a name="what-is-my-hosting-plan"></a>我的主控方案是什麼

若要判斷您函式應用程式所用的主控方案，請在 [Azure 入口網站](https://portal.azure.com)中，參閱函式應用程式 [概觀] 中的 [App Service 方案 / 定價層]。 如果是 App Service 方案，則會同時指出定價層。 

![在入口網站中檢視調整方案](./media/functions-scale/function-app-overview-portal.png)

您也可以使用 Azure CLI 來判斷方案，如下所示：

```azurecli-interactive
appServicePlanId=$(az functionapp show --name <my_function_app_name> --resource-group <my_resource_group> --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv
```  

如果此命令的輸出是 `dynamic`，表示函式應用程式在取用方案中。 此命令的輸出時`ElasticPremium`，函式應用程式處於 「 進階 」 方案。  所有其他值皆代表 App Service 方案的層級。

即使已啟用 [永遠開啟] 選項，個別函式的執行逾時還是由 [host.json](functions-host-json.md#functiontimeout) 專案檔的 `functionTimeout` 設定來控制。

## <a name="storage-account-requirements"></a>儲存體帳戶的需求

任何計劃，函式應用程式都需要支援 Azure Blob、 佇列、 檔案和資料表儲存體的一般 Azure 儲存體帳戶。 這是因為函式的作業，例如管理觸發程序和記錄函數執行依賴 Azure 儲存體，但有些儲存體帳戶不支援佇列和資料表。 這些帳戶 (包括僅限 Blob 的儲存體帳戶 (包含進階儲存體) 和搭配區域備援儲存體複寫的一般用途儲存體帳戶) 會在您建立函式應用程式時，從現有**儲存體帳戶**選項中篩選出來。

<!-- JH: Does using a Premium Storage account improve perf? -->

若要深入了解儲存體帳戶類型，請參閱 [Azure 儲存體服務簡介](../storage/common/storage-introduction.md#azure-storage-services)。

## <a name="how-the-consumption-and-premium-plans-work"></a>取用和進階方案的運作方式

在耗用量和進階方案，縮放控制器會自動調整 CPU 和記憶體資源加上其他函式主控件執行個體，根據其函式被觸發的事件數目。 在取用方案中的 Functions 主機的每個執行個體僅限於 1.5 GB 記憶體和 1 的 CPU。  主控件執行個體是整個函式應用程式，這表示函式應用程式共用內的資源執行個體和向內的所有函式，在相同的時間。 共用相同的取用方案的函數應用程式是獨立調整。  在 「 進階 」 方案，您的方案大小會判斷可用的記憶體和 CPU 的所有應用程式在該執行個體上的該計劃中。  

函式程式碼檔案會儲存在 Azure 檔案共用上的函式的主要儲存體帳戶。 當您刪除函式應用程式的主要儲存體帳戶時，函式程式碼檔案會被刪除，且無法復原。

> [!NOTE]
> 在取用方案中使用 Blob 觸發程序時，處理新 Blob 時最多會有 10 分鐘的延遲。 當函式應用程式已進入閒置狀態時，就會發生此延遲。 在函數應用程式開始執行之後，會立即處理 blob。 若要避免此冷啟動延遲，請使用 「 進階 」 方案，或使用[Event Grid 觸發程序](functions-bindings-event-grid.md)。 如需詳細資訊，請參閱 [blob 觸發程序繫結參考文件](functions-bindings-storage-blob.md#trigger)。

### <a name="runtime-scaling"></a>執行階段調整

Azure Functions 使用名為「縮放控制器」的元件來監視事件的速率，並判斷是否相應放大或相應縮小。 縮放控制器會在每種觸發程序類型使用啟發學習法。 例如，當使用 Azure 佇列儲存體觸發程序時，會根據佇列長度和最舊佇列訊息的壽命調整規模。

調整的單位是函數應用程式。 當函式應用程式相應放大時，會配置額外資源來執行 Azure Functions 主機的多個執行個體。 反之，當計算需求降低時，縮放控制器會移除 Functions 主機的執行個體。 執行個體的數目最終會在函數應用程式中沒有任何函式執行時相應減少至零個。

![縮放控制器能監視事件及建立執行個體](./media/functions-scale/central-listener.png)

### <a name="understanding-scaling-behaviors"></a>了解規模調整行為

縮放比例會因為許多因素而有所不同，也會因為選取的觸發程序和語言不同，而進行不同的規模調整。 不過，現在系統中存在數個面向的縮放行為：

* 單一函式應用程式只能相應增加至最多 200 個執行個體。 單一執行個體可能會一次處理一個以上的訊息或要求，因此沒有設定平行執行的數目上限。
* HTTP 觸發程序，新的執行個體將只會配置最多一次每 1 秒。
* 非 HTTP 觸發程序，新的執行個體將只會配置最多一次每隔 30 秒。

不同的觸發程序可能也會有不同的縮放限制，包含下方文件中所述的限制：

* [事件中樞](functions-bindings-event-hubs.md#trigger---scaling)

### <a name="best-practices-and-patterns-for-scalable-apps"></a>可調整應用程式的最佳做法與模式

函數應用程式中有多個面向會影響其調整規模的效果，包括主機設定、執行階段耗用量和資源效率。  如需詳細資訊，請參閱[效能考量文章中的延展性一節](functions-best-practices.md#scalability-best-practices)。 您也應了解在縮放函式應用程式後，連線會如何運作。 如需詳細資訊，請參閱[如何管理 Azure Functions 中的連線](manage-connections.md)。

### <a name="billing-model"></a>計費模式

[Azure Functions 價格頁面]會詳細說明取用方案的計費方式。 使用量是在函式應用程式層級彙總，且只會計算函式程式碼執行的時間。 計費單位如下︰

* **以十億位元組-秒 (GB-s) 為單位的資源取用量**。 會計算為在函數應用程式中執行之所有函式的記憶體大小和執行時間組合。 
* **執行**。 每次函式為回應事件觸發程序而執行，就算一次。

有用的查詢，以及如何了解耗用量計費的詳細資訊，請參閱[計費的常見問題集 >](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ)。

[Azure Functions 定價頁面]: https://azure.microsoft.com/pricing/details/functions
