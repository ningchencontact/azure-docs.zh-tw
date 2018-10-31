---
title: 了解 Digital Twins 物件模型和空間智慧圖形 | Microsoft Docs
description: 使用 Azure Digital Twins 建立人員、地點與裝置間的關聯性模型
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: alinast
ms.openlocfilehash: 1c2068af510cb3733ce99a6ae7b40487a8c1a015
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323732"
---
# <a name="understanding-digital-twins-object-models-and-spatial-intelligence-graph"></a>了解 Digital Twins 物件模型和空間智慧圖形

Azure Digital Twins 是一項 Azure IoT 服務，可針對實體環境和相關聯的裝置、感應器和使用者提供完整的虛擬表示法。 它可將網域特有的概念組織到實用的模型中，而這些模型又會自行納入空間智慧圖形內，使開發效能得以提升。 這類概念可忠實建立人員、空間和裝置之間的關聯性和互動方式的模型。

_Digital Twins 物件模型_可說明網域特有的概念、類別和屬性。 想要依據本身需求自訂解決方案的使用者可預先定義模型。 這些預先定義的 Digital Twins 物件模型會共同組成_本體_。 智慧建築的本題會說明區域、地點、樓層、辦公室、區域、會議室和聚焦會議室。 能源網本體會說明各種發電站、變電站、能源資源和客戶。 這些 Digital Twins 物件模型和本體可用來自訂適用於各種案例和需求的 Azure Digital Twins。

_Digital Twins 物件模型_和_本體_準備就緒後，即可填入_空間圖形_。 空間圖形是 IoT 解決方案的相關空間、裝置和人員之間各種關聯性的虛擬表示法。 下圖顯示使用智慧建築本體的空間圖形範例。

![Digital Twins 空間圖形建築][1]

<a id="model" />

空間圖形可將空間、裝置、感應器和使用者結合在一起。 前述各項會模擬實際的情況而連結在一起：場地 43 有四個樓層，每個樓層各有多個不同的區域。 使用者會與其工作站相關聯，並且取得圖形某些部分的存取權。  例如，系統管理員將有權對空間圖形進行變更，而訪客可能只有檢視特定建築資料的權限。

## <a name="digital-twins-object-models"></a>Digital Twins 物件模型

Digital Twins 物件模型支援下列主要物件類別：

- **空間**是虛擬或實體位置，例如 `Tenant`、`Customer`、`Region`、`Venue`。
- **裝置**是設備的是虛擬或實體部件，例如 `AwesomeCompany Device`、`Raspberry Pi 3`。
- **感應器**是可偵測事件的物件，例如 `AwesomeCompany Temperature Sensor`、`AwesomeCompany Presence Sensor`。
- **使用者**會識別現用者及其特性。

其他物件類別包括：

- **資源**會連結至空間，通常代表空間圖形中的物件所要使用的 Azure 資源，例如 `IoTHub`。
- **Blob** 會連結至物件 (例如空間、裝置、感應器和使用者)，並且作為 MIME 類型的檔案和中繼資料，例如 `maps`、`pictures`、`manuals`。
- **擴充類型**是可為實體增補某些特性的可擴充列舉，例如 `SpaceType`、`SpaceSubtype`。
- **本體**代表一組擴充類型，例如 `Default`、`Building`、`BACnet`、`EnergyGrid`。
- **屬性索引鍵和值**是空間、裝置、感應器和使用者的自訂特性。 它們可在內建特性以外附加使用，例如，以 `DeltaProcessingRefreshTime` 作為索引鍵，以 `10` 作為值。
- **角色**是為空間圖形中的使用者和裝置指派的權限集，例如 `Space Administrator`、`User Administrator`、`Device Administrator`。
- **角色指派**是空間圖形中的角色和物件之間的關聯，例如，為使用者或服務主體授與對空間圖形中的空間進行管理的權限。
- **安全性金鑰存放區**可為指定空間物件下的階層內包含的所有裝置提供安全性金鑰，使裝置能夠安全地與 Digital Twins 服務進行通訊。
- **使用者定義的函式** (或簡稱 **UDF**) 可用來在空間圖形內自訂感應器遙測處理。 比方說，UDF 可以設定感應器值、根據感應器讀數執行自訂邏輯並設定對空間的輸出、將中繼資料連結至空間，以及在符合預先定義的條件時傳送通知。 目前可以用 JavaScript 撰寫 UDF。
- **比對器**物件可決定要對指定的遙測訊息執行哪些 UDF。
- **端點**是可路由遙測訊息和 Digital Twins 事件的位置，例如 `Event Hub`、`Service Bus`、`Event Grid`。

<a id="graph" />

## <a name="spatial-intelligence-graph"></a>空間智慧圖形

**空間圖形**是定義於 **Digital Twins 物件模型**中的空間、裝置和人員的階層式圖形。 空間圖形支援_繼承_、_篩選_、_周遊_、_延展性_和_擴充性_。 使用者可透過 REST API 的集合來管理空間圖形並與其互動 (如下所示)。

在訂用帳戶中部署 Digital Twins 服務的使用者會成為根節點的全域管理員，而自動授與整個結構的完整存取權。 接著，這個使用者可使用 `Space` API 在圖形中佈建空間。 裝置可使用 `Device` API 來佈建，感應器可使用 `Sensor` API 來佈建，依此類推。我們也提供[開放原始碼工具](https://github.com/Azure-Samples/digital-twins-samples-csharp)以供大量佈建圖形之用。

圖形_繼承_會套用至源自父節點及其下所有節點的權限和屬性。 例如，如果為使用者指派了指定節點的角色，使用者就會有該角色對指定節點和其下每個節點的權限。 此外，為指定節點定義的每個屬性索引鍵和擴充類型，也將會由該節點下所有的節點繼承。

圖形_篩選_可讓使用者依識別碼、名稱、類型、子類型、父空間、相關聯的空間、感應器資料類型、屬性索引鍵和值、周遊、minLevel、maxLevel 和其他 OData 篩選參數來縮小要求結果的範圍。

圖形_周遊_可讓使用者充分瀏覽空間圖形，兼具深度和廣度。 在深度方面，可使用瀏覽參數 `traverse`、`minLevel`、`maxLevel` 由上往下或由下往上周遊圖形。 在廣度方面，則可瀏覽圖形以取得直接連結至父空間或其子系之一的同層級節點。 在查詢物件時，您可以使用 GET API 的 `includes` 參數取得與該物件有關聯性的所有相關物件。

Azure Digital Twins 可確保圖形的_延展性_，因此能夠處理您實際的工作負載。 Digital Twins 可用來代表不動產、基礎結構、裝置、感應器、遙測等項目的大型組合。

圖形的_擴充性_可讓使用者以新的類型和本體自訂基礎的 Digital Twins 物件模型。 您的 Digital Twins 資料也可用可擴充的屬性和值來補強。

### <a name="spatial-intelligence-graph-management-apis"></a>空間智慧圖形管理 API

在您從 [Azure 入口網站](https://portal.azure.com)部署 Azure Digital Twins 後，管理 API 的 [Swagger](https://swagger.io/tools/swagger-ui/) URL 就會自動產生，並以下列格式顯示在 Azure 入口網站的 [概觀] 區段中：

```plaintext
https://yourInstanceName.yourLocation.azuresmartspaces.net/management/swagger
```

| 自訂屬性名稱 | 更換為 |
| --- | --- |
| `yourInstanceName` | Azure Digital Twins 執行個體的名稱 |
| `yourLocation` | 裝載您執行個體的伺服器區域 |

 依照下圖中的用法可以看到完整的 URL 格式：

![Digital Twins 入口網站管理 API][2]

如需使用空間智慧圖形的詳細資訊，請瀏覽「Azure Digital Twins 管理 API 搶先預覽」。

> [!TIP]
> Swagger 搶先預覽中有 API 功能集的示範。
> 這些內容位在 [docs.westcentralus.azuresmartspaces.net/management/swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger)。

深入了解[如何使用 Swagger](how-to-use-swagger.md)。

所有 API 呼叫都必須使用 [OAuth](https://docs.microsoft.com/azure/active-directory/develop/v1-protocols-oauth-code) 進行驗證。 API 會遵循 [Microsoft REST API 指導方針慣例](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md)。 大部分傳回集合的 API 都支援 [OData](http://www.odata.org/getting-started/basic-tutorial/#queryData) 系統查詢選項。

## <a name="next-steps"></a>後續步驟

若要深入了解裝置連線能力，以及如何將遙測訊息傳送至 Azure Digital Twins 服務，請參閱 [Azure Digital Twins 裝置連線能力和遙測輸入](concepts-device-ingress.md)。

若要深入了解管理 API 的限制和節流，請參閱 [Azure Digital Twins API 管理和限制](concepts-service-limits.md)。

<!-- Images -->
[1]: media/concepts/digital-twins-spatial-graph-building.png
[2]: media/concepts/digital-twins-spatial-graph-management-api-url.png
