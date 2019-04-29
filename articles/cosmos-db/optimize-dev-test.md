---
title: 在 Azure Cosmos DB 中最佳化開發與測試
description: 本文說明 Azure Cosmos DB 如何免費提供多個對服務進行開發與測試的選項。
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/20/2018
ms.author: rimman
ms.openlocfilehash: 3bb39fe99e612fb19f12fd049ebb8f600e48373e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60930212"
---
# <a name="optimize-development-and-testing-cost-in-azure-cosmos-db"></a>在 Azure Cosmos DB 中最佳化開發與測試成本

本文描述使用 Azure Cosmos DB 免費進行開發與測試的各種不同選項。

## <a name="azure-cosmos-db-emulator-locally-downloadable-version"></a>Azure Cosmos DB 模擬器 (可下載的本機版)

[Azure Cosmos DB 模擬器](local-emulator.md)是能模擬 Azure Cosmos DB 雲端服務的可下載本機版。 即使沒有網路連線，您也可以撰寫及測試使用 Azure Cosmos DB API 的程式碼，且不會產生任何費用。 Azure Cosmos DB 模擬器能提供可供開發之用，且能夠精確模擬雲端服務的本機環境。 您可以在本機開發及測試應用程式，而不需建立 Azure 訂用帳戶。 當您準備好將應用程式部署到雲端時，只需要更新連接字串來連線到雲端中的 Azure Cosmos DB 端點，而不需要做其他修改。 您也可以使用 Azure DevOps 中的 [Azure Cosmos DB 模擬器建置工作來設定 CI/CD 管線](tutorial-setup-ci-cd.md)以執行測試。 若要開始使用，請參閱 [Azure Cosmos DB 模擬器](local-emulator.md)一文。

## <a name="try-azure-cosmos-db-for-free"></a>免費試用 Azure Cosmos DB

[免費試用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) 為可讓您在雲端中建立資料庫與集合，並對 Azure Cosmos DB 進行實驗的免費體驗。 您不需要註冊 Azure 或支付任何費用。 「試用 Azure Cosmos DB」帳戶可於有限的時間內使用，目前為 30 天。 您可以隨時更新它們。 「試用 Azure Cosmos DB」帳戶可讓您輕鬆評估 Azure Cosmos DB，並使用快速入門或教學課程來建置及測試應用程式。 您可以在不產生任何費用的情況下建立示範或執行單元測試。 藉由使用「免費試用 Azure Cosmos DB」帳戶，您可以免費評估 Azure Cosmos DB 的進階功能，包括周全全域散發、SLA，以及一致性模型。 您可以建立具有最多 25 個 Azure Cosmos 容器和 10,000 RU/s 輸送量的資料庫。 您可以在不註冊 Azure 帳戶或使用信用卡的情況下執行範例應用程式。 透過「免費試用 Azure Cosmos DB」，只要幾分鐘的時間，您便可以建立多重區域的 Azure Cosmos 帳戶並在其上執行應用程式。 若要開始使用，請參閱[免費試用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) 頁面。

## <a name="azure-free-account"></a>Azure 免費帳戶

Azure Cosmos DB 已包含在 [Azure 免費帳戶](https://azure.microsoft.com/free)中，此帳戶會在特定期間內提供 Azure 點數和免費資源。 此免費帳戶會特別針對 Azure Cosmos DB，在為期一年的時間內提供 5-GB 的儲存體，以及 400 RU 的已佈建輸送量。 此體驗能讓任何開發人員在不產生任何成本的情況下輕鬆測試 Azure Cosmos DB 的功能，或將它與其他 Azure 服務進行整合。 透過 Azure 免費帳戶，您可以取得美金 $200 元的點數，可供您在前 30 天內使用。 即使您開始使用服務也不會向您收費，直到您選擇升級為止。 若要開始使用，請造訪 [Azure 免費帳戶](https://azure.microsoft.com/free)頁面。

## <a name="next-steps"></a>後續步驟

您可以透過下列文章來開始使用模擬器或免費的 Azure Cosmos DB 帳戶：

* 深入了解[最佳化開發與測試](optimize-dev-test.md)
* 深入了解 [Azure Cosmos DB 帳單](understand-your-bill.md)
* 深入了解[最佳化輸送量成本](optimize-cost-throughput.md)
* 深入了解[最佳化儲存體成本](optimize-cost-storage.md)
* 深入了解[最佳化讀取和寫入的成本](optimize-cost-reads-writes.md)
* 深入了解[最佳化查詢成本](optimize-cost-queries.md)
* 深入了解[最佳化多重區域 Azure Cosmos 帳戶的成本](optimize-cost-regions.md)

