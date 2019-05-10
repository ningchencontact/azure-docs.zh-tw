---
title: 診斷和 Azure Functions 中使用 Azure Cosmos DB 觸發程序時，針對問題進行疑難排解
description: 常見的問題、 因應措施，並使用 Azure Functions 使用 Azure Cosmos DB 觸發程序時的診斷步驟
author: ealsur
ms.service: cosmos-db
ms.date: 04/16/2019
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: e8f0b9c8bf1bfb846f13306f58bcb1721ed6b422
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2019
ms.locfileid: "65510535"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-trigger-in-azure-functions"></a>診斷和 Azure Functions 中使用 Azure Cosmos DB 觸發程序時，針對問題進行疑難排解

本文章涵蓋常見的問題、 因應措施，以及診斷步驟，當您使用[Azure Cosmos DB 觸發程序](change-feed-functions.md)搭配 Azure Functions。

## <a name="dependencies"></a>相依性

Azure Cosmos DB 觸發程序和繫結取決於延伸模組套件的基底的 Azure Functions 執行階段。 永遠保持更新，這些封裝，因為它們可能包含的修正和可能解決所有潛在的問題，您可能會遇到的新功能：

* Azure Functions v2，請參閱[Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB)。
* 適用於 Azure Functions V1，請參閱[Microsoft.Azure.WebJobs.Extensions.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB)。

本文章一律會參考 Azure Functions V2 每當執行階段所述，除非明確指定。

## <a name="consume-the-azure-cosmos-db-sdk-independently"></a>單獨使用 Azure Cosmos DB SDK

延伸模組套件的主要功能是提供 Azure Cosmos DB 觸發程序和繫結支援。 它也包含[Azure Cosmos DB.NET SDK](sql-api-sdk-dotnet-core.md)，這有幫助，如果您想要以程式設計的方式互動使用 Azure Cosmos DB 未使用的觸發程序和繫結。

如果想要使用 Azure Cosmos DB SDK，請確定您未新增至您的專案其他的 NuGet 套件參考。 相反地，**可讓透過 Azure Functions 的延伸模組套件所解析的 SDK 參考**。 使用 Azure Cosmos DB SDK 分開的觸發程序和繫結

此外，如果您要以手動方式建立您自己的執行個體[Azure Cosmos DB SDK 用戶端](./sql-api-sdk-dotnet-core.md)，您應該遵循的模式只有一個執行個體的用戶端[使用單一模式方法](../azure-functions/manage-connections.md#documentclient-code-example-c). 此程序可避免潛在的通訊端問題，在您的作業。

## <a name="common-scenarios-and-workarounds"></a>常見的案例和因應措施

### <a name="azure-function-fails-with-error-message-collection-doesnt-exist"></a>Azure 函式會失敗，錯誤訊息集合並不存在

Azure 函式會失敗，錯誤訊息 「 任一來源 '集合-name' （資料庫中的集合' database_name '） 或租用 'collection2-name' （資料庫中的集合' database2-name'） 不存在。 啟動接聽程式之前，必須存在於這兩個集合。 若要自動建立租用集合，設定為 'true' 的 'CreateLeaseCollectionIfNotExists' 」

這表示一個或兩個所需的觸發程序運作的 Azure Cosmos 容器不存在或未連線至 Azure 函式。 **錯誤本身會告訴您哪一個 Azure Cosmos 資料庫和容器是觸發程序尋找**根據您的設定。

1. 請確認`ConnectionStringSetting`屬性和它**參考存在於 Azure Functions 應用程式的設定**。 這個屬性的值應該是連接字串本身，但組態設定的名稱。
2. 確認`databaseName`和`collectionName`存在於您的 Azure Cosmos 帳戶。 如果您使用的自動的值取代 (使用`%settingName%`模式)，請確定設定的名稱位於您的 Azure 函式應用程式。
3. 如果您未指定`LeaseCollectionName/leaseCollectionName`，預設值是 「 租用 」。 請確認這類的容器已存在。 您可以選擇設定`CreateLeaseCollectionIfNotExists`到您觸發程序中的屬性`true`來自動建立它。
4. 請確認您[Azure Cosmos 帳戶的防火牆組態](how-to-configure-firewall.md)以查看若要查看不是它不會封鎖 Azure 函式。

### <a name="azure-function-fails-to-start-with-shared-throughput-collection-should-have-a-partition-key"></a>Azure 函式失敗時開始使用 「 共用輸送量集合應該有資料分割索引鍵 」

舊版的 Azure Cosmos DB 延伸模組不支援使用租用容器內建立[共用的輸送量資料庫](./set-throughput.md#set-throughput-on-a-database)。 若要解決此問題，請更新[Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB)取得最新版本的延伸模組。

### <a name="azure-function-fails-to-start-with-the-lease-collection-if-partitioned-must-have-partition-key-equal-to-id"></a>Azure 函式失敗時開始使用 「 租用集合，如果資料分割，必須有識別碼相等的資料分割索引鍵。 」

此錯誤表示您目前的租用容器已分割，但資料分割索引鍵路徑不是`/id`。 若要解決此問題，您需要以重新建立租用容器`/id`做為資料分割索引鍵。

### <a name="you-see-a-value-cannot-be-null-parameter-name-o-in-your-azure-functions-logs-when-you-try-to-run-the-trigger"></a>您會看到 「 值不可為 null。 參數名稱： o 」 在您的 Azure Functions 記錄當您嘗試執行觸發程序

如果您使用 Azure 入口網站，而且您嘗試選取，就會出現此問題**執行**檢查 Azure 函式使用觸發程序時在螢幕上的按鈕。 觸發程序不需要為您選取 [執行] 啟動，就會自動開始部署 Azure 函式時。 如果您想要檢查 Azure 入口網站上的 Azure 函式的記錄資料流、 只要到您受監視的容器，然後插入一些新項目，您就會自動看到觸發程序執行。

### <a name="my-changes-take-too-long-be-received"></a>會收到我的變更時間過長

此案例中可以有多個原因，且所有人都必須檢查：

1. 您的 Azure 函式在與您的 Azure Cosmos 帳戶位於相同區域中部署嗎？ 如需最佳的網路延遲，Azure 函式和您的 Azure Cosmos 帳戶應該會共置於相同的 Azure 區域中。
2. 您的 Azure Cosmos 容器連續或偶發中是否發生所做的變更？
如果是後者，可能是所儲存的變更與 Azure Function 揀選它們之間有些延遲。 這是因為就內部而言，當觸發程序會檢查您的 Azure Cosmos 容器中的變更，並發現任何暫止讀取，它會進入睡眠狀態可設定的一段時間 （根據預設，5 秒） 之前檢查新的變更 （若要避免高的 RU 耗用量）。 您可以設定透過此睡眠時間`FeedPollDelay/feedPollDelay`中設定[組態](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration)的觸發程序 （的值必須是以毫秒為單位）。
3. Azure Cosmos 容器可能[速率限制](./request-units.md)。
4. 您可以使用`PreferredLocations`觸發程序來指定逗號分隔的清單，以定義自訂的慣用的連接順序的 Azure 區域中的屬性。

### <a name="some-changes-are-missing-in-my-trigger"></a>某些變更會遺失我的觸發程序中

如果您發現，一些在您的 Azure Cosmos 容器中發生的變更不會被挑選 Azure 函式，則需要進行初始調查步驟。

當您的 Azure 函式收到所做的變更時，它通常會處理它們，並可以選擇性地，將結果傳送至另一個目的地。 當您在調查遺失的變更時，請務必**量值的變更不會收到在擷取點**（Azure 函式啟動時），不是在目的地上。

如果目的地上遺漏一些變更，這可能表示這是某些 Azure 函式執行期間發生的事件之後所做的變更已收到的錯誤。

在此案例中，最好的做法是加入`try/catch blocks`程式碼中，並可能正在處理的變更，來偵測特定項目子集的任何失敗，並據以處理它們在迴圈內 （將它們傳送至另一個儲存體的進一步分析或重試）。 

> [!NOTE]
> Azure Cosmos DB 觸發程序，根據預設，不會重試變更批次在您的程式碼執行期間發生未處理例外狀況。 這表示，所做的變更並未到達目的地的原因是因為您無法加以處理。

如果您發現您的觸發程序未完全收到一些變更，最常見的案例是，則**另一個 Azure 函式執行**。 它可能是部署在 Azure 中的另一個 Azure 函式或開發人員的電腦上本機執行 Azure 函式具有**完全相同的組態**（相同的監視和租用容器），以及此 Azure 函式竊取您預期您的 Azure 函式，來處理變更的子集。

此外，可驗證的案例中，如果您知道多少 Azure 函式應用程式執行個體已執行。 如果您檢查您的租用容器，並計算租用項目內的相異值數目`Owner`中這些屬性應該等於函式應用程式的執行個體數目。 如果有多個比已知的 Azure 函式應用程式執行個體的擁有者，這表示這些額外的擁有者是一個 「 竊取 」 所做的變更。

要套用的一個簡單的方法，若要解決此情況下，是`LeaseCollectionPrefix/leaseCollectionPrefix`到您的函式，以新/不同的值或，或者，使用新的租用容器進行測試。

## <a name="next-steps"></a>後續步驟

* [若要啟用監視您的 Azure 函式](../azure-functions/functions-monitoring.md)
* [Azure Cosmos DB.NET SDK 進行疑難排解](./troubleshoot-dot-net-sdk.md)