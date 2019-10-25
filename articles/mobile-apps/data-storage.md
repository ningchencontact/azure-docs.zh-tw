---
title: 使用 Visual Studio App Center 和 Azure 服務在雲端中儲存、管理及保存應用程式資料
description: 深入瞭解可讓您在雲端儲存、管理和保存行動應用程式資料的服務（例如 App Center）。
author: elamalani
ms.assetid: 12344321-0123-4678-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: b7043c7e9bd944bfb3633397edfa3c35609bd8ec
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795571"
---
# <a name="store-sync-and-query-mobile-application-data-from-the-cloud"></a>從雲端儲存、同步和查詢行動應用程式資料
不論您所建立的應用程式類型為何，您都可能會產生和處理資料。 使用者期望很高，而他們希望應用程式在所有情況下都能快速且順暢地工作。 大部分的應用程式會在多個裝置上工作，因此當您存取應用程式時，您可能會從桌面或行動裝置存取它。 多個使用者可能會同時使用應用程式並共用資料，並預期會立即即時存取資料。

除此之外，您的應用程式使用者不一定會有網際網路連線能力。 應用程式的設計與預期會使用或不使用網際網路連接。 隨著所有這些日益複雜的複雜性，開發人員可以選擇適當的解決方案來儲存資料並將其同步至雲端，為其應用程式提供絕佳的客戶體驗。

Microsoft 提供各種不同的服務，讓您不需要加速伺服器、挑選您的資料庫，或擔心調整或安全性，以盡可能提供豐富的體驗。 這些服務提供絕佳的開發人員體驗，可讓您使用 SQL 或 NoSQL Api 將應用程式資料儲存在雲端中、同步處理所有裝置上的資料，以及讓應用程式可以使用或不使用網路連線，以協助建立可調整且健全的應用程式.

使用下列服務在雲端中管理和儲存行動應用程式資料。

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center 資料](/appcenter/data/)是一項資料管理服務，可讓應用程式在線上和離線案例中，跨不同的裝置和平臺管理、保存及同步處理雲端中的應用程式資料。 這項服務以**Cosmos DB**服務為基礎，可隨著您的使用者群和應用程式數目增加而成長，以確保您所有資料的低延遲、高可用性和高擴充性。

**主要功能**
- 輕鬆地布建**新的 Cosmos DB 資料庫**，或從 App Center 入口網站**連接到現有的 Cosmos DB 資料庫**。
- **NoSQL 資料庫支援**，可輕鬆地儲存、同步及查詢應用程式資料。
- 這項服務是以**Cosmos DB**為基礎，繼承 Azure Cosmos DB 所提供的大部分主要功能，而且可以**全域擴充**以符合您的商務需求。
- **線上和離線同步**處理功能，可在不同裝置之間同步資料。
- 可讓您輕鬆管理私用應用程式資料的行動**用戶端 sdk** 。
- **平臺支援**-IOS、Android、Xamarin、React Native。

**參考**
- [使用 App Center 註冊](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [開始使用 App Center 資料](/appcenter/data/getting-started)

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)是 Microsoft 的全域散發多模型資料庫服務，可協助您建立全球規模的應用程式。 透過按鈕點選，Cosmos DB 可讓您有彈性且獨立地跨任意數目的 Azure 區域調整輸送量和儲存體。 您可以彈性調整輸送量和儲存體，並利用您最愛的 API 介面（包括 SQL、MongoDB、Cassandra、資料表或 Gremlin），以快速、單一位數毫秒的資料存取。 Cosmos DB 針對輸送量、延遲、可用性和一致性，唯一提供完整的服務等級協定（Sla）。

**主要功能**
- 支援**各種不同的 api** ，包括 SQL （核心） api、Cassandra API、MongoDB Api、Gremlin api 和資料表 API。
- 通行的**全域散發**會在您的使用者所在的地方複寫您的資料，讓您的使用者可以與最靠近他們的資料複本進行互動。
- **沒有架構或索引管理**，因為資料庫引擎與架構完全無關。
- 在全球所有 Azure 區域（包括公用雲端中的54個以上區域）中都有提供 Cosmos DB，這是**普遍的區域存在**。
- 明確地定義，做為 Cosmos DB 多宿主複寫通訊協定的**多個一致性選擇，** 會仔細地設計成提供五個定義完善的一致性選擇-強式、限定過期、會話、一致前置詞和最終。
- **99.999%** 的讀取和寫入可用性。
- **以程式設計方式（或透過入口網站）叫用 Cosmos 帳戶的區域性容錯移轉**，以確保您的應用程式能夠承受區域性災難。
- **保證在全球第99個百分位數的低延遲**。

**參考**
- [Azure 入口網站](https://portal.azure.com) 
- [Documentation](/azure/cosmos-db/introduction)   

## <a name="azure-sql-database"></a>Azure SQL Database
 [Azure SQL Database](https://azure.microsoft.com/services/sql-database/)是一般用途的關係資料庫受控服務，可讓您針對 Azure 雲端中的應用程式和解決方案，建立高度可用且高效能的資料儲存層。

**主要功能**
- **彈性資料庫模型和工具**-彈性資料庫讓開發人員能夠集區資源，以在一組資料庫中用來進行調整，然後藉由將腳本當做作業提交來進行管理管理，而 SQL Database 執行跨資料庫撰寫腳本。
- **高效**能-高輸送量應用程式可以利用最新版本，以提供額外25% 的高階資料庫能力。
- **備份、複寫和高可用性**-內建複寫和資料庫層級的 MICROSOFT 支援 SLA，可提供應用程式持續性，並防範嚴重事件。 主動式異地複寫可讓您設定容錯移轉和自助式還原，讓您完全掌控「糟糕復原」（從最多35天的可用資料備份還原資料）。
- **近乎零的維護**-自動軟體是服務的一部分，而內建的系統複本則有助於提供固有的資料保護、資料庫執行時間和系統穩定性。 當舊的電腦失敗時，系統複本會自動移往即時佈建的新電腦。
- **安全性**SQL Database 提供一系列的安全性功能，以符合組織或產業規定的合規性政策
    - 「審核」可讓開發人員執行符合規範的工作，並取得活動的相關知識。
    - 開發人員和 IT 可以在資料庫層級上執行原則，以協助限制具有資料列層級安全性、動態資料遮罩和透明資料加密的敏感性資料存取，以進行 Azure SQL Database。
    - 主要雲端審計員會在 Azure 合規性認證和核准的範圍中驗證 SQL Database，例如 HIPAA BAA、ISO/IEC 27001:2005、FedRAMP 和 EU Model 子句）。

**參考**
- [Azure 入口網站](https://portal.azure.com) 
- [Documentation](/azure/sql-database/)
   