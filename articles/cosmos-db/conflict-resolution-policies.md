---
title: Azure Cosmos DB 中多個寫入區域的衝突解決類型和解決原則
description: 本文描述 Azure Cosmos DB 中的衝突類別和衝突解決原則。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 98e9f5fff1b74d417ee07ed0056c8046b49baa17
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236538"
---
# <a name="conflict-types-and-resolution-policies"></a>衝突類型和解決原則

如果您的 Azure Cosmos DB 帳戶設定了多個寫入區域，就適用衝突和衝突解決原則。

Azure Cosmos 帳戶設有多個寫入區域，當寫入器同時更新多個區域中相同的項目時，可能會發生更新衝突。 更新衝突可以是下列三種類型：

* **插入衝突**：應用程式同時在兩個或多個區域中插入兩個或多個具有相同的唯一索引的項目時，可能會發生這些衝突。 比方說，這項衝突可能會發生與 ID 屬性。

* **取代衝突**：當應用程式會更新相同的項目，同時在兩個或多個區域時，可能會發生這些衝突。

* **刪除衝突**：當應用程式同時會刪除一個區域中的項目，並更新另一個區域中，可能會發生這些衝突。

## <a name="conflict-resolution-policies"></a>衝突解決原則

Azure Cosmos DB 會提供彈性原則導向的機制來解決寫入衝突。 您可以選取從 Azure Cosmos 容器上的兩個衝突解決原則：

- **最後寫入為準 (LWW)** ：此解決原則預設會使用系統定義的時間戳記屬性。 它是以時間同步化時鐘通訊協定為基礎。 如果您使用 SQL API，您可以指定要用於衝突解決的任何其他自訂數值屬性 （例如您自己的觀念，時間戳記）。 自訂的數值屬性也稱為*衝突解析路徑*。 

  如果兩個或更多項目在插入或取代作業時發生衝突，則包含衝突解決路徑最高值的項目會成為「優先」項目。 如果多個項目的衝突解決路徑值都相同，則由系統決定「優先」項目。 所有區域保證都會彙整出單一優先項目，且認可項目的版本最後都會相同。 如果涉及刪除衝突，則刪除的版本一律優先於插入或取代衝突。 不論衝突解決之道的值為何，就會發生這個結果。

  > [!NOTE]
  > 「最後寫入為準」是預設的衝突解決原則。 它會提供下列 Api:SQL、 MongoDB、 Cassandra、 Gremlin 和資料表。

  若要深入了解，請參閱[使用 LWW 衝突解決原則的範例](how-to-manage-conflicts.md)。

- **自訂**：此解決原則適用於應用程式定義的衝突調整語意。 當您在您的 Azure Cosmos 容器上設定此原則時，您也必須註冊*合併預存程序*。 在資料庫交易在伺服器上偵測到衝突時，會自動叫用此程序。 系統只針對合併程序的執行提供一次保證 (為認可通訊協定的一部分)。  

  如果您使用自訂的解析 選項，來設定您的容器，您無法註冊容器上的合併程序或合併程序在執行階段會發生例外狀況，會寫入衝突*衝突摘要*。 然後您的應用程式需要手動解決衝突摘要中的衝突。 若要深入了解，請參閱[如何使用自訂解決原則及如何使用衝突摘要的範例](how-to-manage-conflicts.md)。

  > [!NOTE]
  > 自訂衝突解決原則只適用於 SQL API 帳戶。

## <a name="next-steps"></a>後續步驟

了解如何設定衝突解決原則：

* [如何在應用程式中設定多重主機](how-to-multi-master.md)
* [如何管理衝突解決原則](how-to-manage-conflicts.md)
* [如何讀取衝突摘要](how-to-manage-conflicts.md#read-from-conflict-feed)
