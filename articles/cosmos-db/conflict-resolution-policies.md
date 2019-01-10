---
title: Azure Cosmos DB 中的衝突解決類型和解決原則
description: 本文描述 Azure Cosmos DB 中的衝突類別和衝突解決原則。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 4af3f4c60f186c34d0f5522461ba70c68b59d1ab
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2019
ms.locfileid: "54033888"
---
# <a name="conflict-types-and-resolution-policies"></a>衝突類型和解決原則

如果您的 Azure Cosmos DB 帳戶設定了多個寫入區域，就適用衝突和衝突解決原則。

對於已設定多個寫入區域的 Azure Cosmos DB 帳戶，當寫入器同時更新多個區域中的相同項目時，就可能發生更新衝突。 更新衝突分為下列三個類別：

* **插入衝突**：當應用程式從兩個或多個區域，同時插入唯一索引相同的兩個或多個項目時，可能發生此衝突。 例如，這項衝突可能會發生於 ID 屬性。 所有的寫入在其各自的當地區域最初可能都會成功。 但根據您所選擇的衝突解決原則，最後只會認可一個具有原始識別碼的項目。

* **取代衝突**：當應用程式從兩個或多個區域同時更新單一項目時，可能發生這類衝突。

* **刪除衝突**：當應用程式同時從一個區域刪除項目，且從任何其他區域更新它時，可能發生這類衝突。

## <a name="conflict-resolution-policies"></a>衝突解決原則

Azure Cosmos DB 提供彈性的原則導向機制來解決更新衝突。 您可以在 Azure Cosmos DB 容器上選取下列兩個衝突解決原則：

- **最後寫入為準 (LWW)**： 此解決原則預設會使用系統定義的時間戳記屬性。 它是以時間同步化時鐘通訊協定為基礎。 如果您使用 Azure Cosmos DB SQL API，即可指定任何其他自訂數值屬性以用來解決衝突。 自訂數值屬性也稱為衝突解決路徑。 

  如果兩個或更多項目在插入或取代作業時發生衝突，則包含衝突解決路徑最高值的項目會成為「優先」項目。 如果多個項目的衝突解決路徑值都相同，則由系統決定「優先」項目。 所有區域保證都會彙整出單一優先項目，且認可項目的版本最後都會相同。 如果涉及刪除衝突，則刪除的版本一律優先於插入或取代衝突。 無論衝突解決路徑的值為何，都會發生此結果。

  > [!NOTE]
  > 「最後寫入為準」是預設的衝突解決原則。 它適用於 SQL、Azure Cosmos DB 資料表、MongoDB、Cassandra 和 Gremlin API 帳戶。

  若要深入了解，請參閱[使用 LWW 衝突解決原則的範例](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)。

- **自訂**：此解決原則適用於應用程式定義的衝突調整語意。 當您在 Azure Cosmos DB 容器上設定此原則時，您也需要註冊合併預存程序。 在伺服器的資料庫交易之下偵測到衝突時，就會自動叫用此程序。 系統只針對合併程序的執行提供一次保證 (為認可通訊協定的一部分)。  

  如果您使用自訂解決選項設定您的容器，請記得兩個要點。 如果您無法在容器上註冊合併程序，或合併程序在執行階段擲回例外狀況，則衝突會寫入至衝突摘要。 然後您的應用程式需要手動解決衝突摘要中的衝突。 若要深入了解，請參閱[如何使用自訂解決原則及如何使用衝突摘要的範例](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)。

  > [!NOTE]
  > 自訂衝突解決原則只適用於 SQL API 帳戶。

## <a name="next-steps"></a>後續步驟

了解如何設定衝突解決原則。 請參閱下列文章：

* [使用 LWW 衝突解決原則](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)
* [使用自訂衝突解決原則](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)
* [使用衝突摘要](how-to-manage-conflicts.md#read-from-conflict-feed)
