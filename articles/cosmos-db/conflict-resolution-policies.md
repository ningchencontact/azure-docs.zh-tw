---
title: Azure Cosmos DB 中的衝突解決
description: 本文描述 Azure Cosmos DB 中的衝突類別和衝突解決原則。
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: mjbrown
ms.openlocfilehash: 2f219ed6c3155e8b7d3d978116e1748f6c115fea
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2018
ms.locfileid: "50243883"
---
# <a name="conflict-types-and-resolution-policies"></a>衝突類型和解決原則

如果您的 Cosmos 帳戶設定了多個寫入區域，就適用衝突和衝突解決原則。

對於設定了多個區域的 Cosmos DB 帳戶，當多個寫入作業同時更新多個區域中的相同項目時，就可能發生更新衝突。 更新衝突分為下列三個類別：

1. **插入衝突**：當應用程式從兩個或多個區域，同時插入唯一索引相同的兩個或多個項目 (例如，ID 屬性) 時，可能發生此衝突。 在此情況下，寫入作業一開始在各自的當地區域可能會成功，但根據您所選的衝突解決原則，系統最後只會認可一個具有該原始識別碼的項目。
2. **取代衝突**：當應用程式從兩個或多個區域同時更新單一項目時，可能發生此衝突。
3. **刪除衝突**：當應用程式同時從一個區域刪除項目，且從任何其他區域更新它時，可能發生此衝突。

## <a name="conflict-resolution-policies"></a>衝突解決原則

Cosmos DB 針對解決更新衝突提供彈性原則導向的機制。 您可以在 Cosmos 容器上選取下列兩個衝突解決原則：

- **Last-Write-Wins (LWW)** (最後寫入者優先)，預設會使用系統定義的時間戳記屬性 (根據時間同步化時鐘通訊協定)。 或者，在使用 SQL API 時，Cosmos DB 允許您指定用於衝突解決的任何其他自訂數值屬性 (也稱為「衝突解決路徑」)。  

如果兩個或多個項目在插入或取代作業發生衝突，則包含「衝突解決路徑」之最高值的項目成為「優先」項目。 如果多個項目的衝突解決路徑值都相同，則由系統決定「優先」版本。 所有區域保證都會彙整出單一優先項目，且認可項目的版本最後都會相同。 如果涉及刪除衝突，則不論衝突解決路徑的值為何，刪除的版本一律優先於插入或取代衝突。

> [!NOTE]
> LWW 是預設的衝突解決原則，且可供 SQL、Table、MongoDB、Cassandra 和 Gremlin API 取得。

若要深入了解，請參閱[使用 LWW 衝突解決原則的範例](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)。

- **自訂**：此原則適用於應用程式定義的衝突調整語意。 除了在您的 Cosmos 容器上設定此原則之外，您也需要註冊合併預存程序，當系統在伺服器端上的資料庫交易底下偵測到更新衝突時，會自動叫用此預存程序。 系統只針對合併程序的執行提供一次保證 (為認可通訊協定的一部分)。  

不過，如果您使用自訂解決選項設定您的容器，但在容器上註冊合併程序失敗，或合併程序在執行階段擲回例外狀況，則衝突會寫入衝突摘要。 然後您的應用程式將需要手動解決衝突摘要中的衝突。 若要深入了解，請參閱[使用自訂解決原則及如何使用衝突摘要的範例](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)。

> [!NOTE]
> 自訂衝突解決原則只適用於 SQL API 帳戶。

## <a name="next-steps"></a>後續步驟

接下來，您可以閱讀下列文章，以了解如何設定衝突解決原則：

* [如何使用 LWW 衝突解決原則](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)
* [如何使用自訂衝突解決原則](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)
* [如何使用衝突摘要](how-to-manage-conflicts.md#read-from-conflict-feed)
