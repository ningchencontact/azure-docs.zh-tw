---
title: Azure Cosmos DB 中的唯一索引鍵
description: 了解如何在 Azure Cosmos DB 資料庫中使用唯一索引鍵
author: aliuy
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: andrl
ms.openlocfilehash: 006d0ef28d82a7648a56b3bf871c5a3afd6a55a6
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2018
ms.locfileid: "51624415"
---
# <a name="unique-keys-in-azure-cosmos-db"></a>Azure Cosmos DB 中的唯一索引鍵

唯一索引鍵可為您提供在 Cosmos 容器中新增一層資料完整性的能力。 您會在建立 Cosmos 容器時建立唯一索引鍵原則。 利用唯一索引鍵，您就能確保邏輯分割區內一或多個值的唯一性 (您可以保證每個[分割區索引鍵](partition-data.md)的唯一性)。 一旦使用唯一索引鍵原則建立容器之後，該原則就能防止在邏輯分割區內建立任何新的 (或已更新的) 重複項目，如唯一索引鍵限制式所指定。 與唯一索引鍵結合的分割區索引鍵可保證項目在容器範圍內的唯一性。

例如，假設 Cosmos 容器的唯一索引鍵限制式為電子郵件地址且分割區索引鍵為 `CompanyID`。 藉由將使用者的電子郵件位址設定為唯一索引鍵，您就能確保每個項目在指定的 `CompanyID` 內都具有唯一的電子郵件地址。 您無法使用重複的電子郵件地址和相同的分割區索引鍵值來建立兩個項目。  

如果您希望使用者能夠使用相同的電子郵件地址建立多個項目，但名字、姓氏和電子郵件地址不同，則您可以在唯一索引鍵原則中新增額外的路徑。 您也可以利用名字、姓氏和電子郵件的組合來建立唯一索引鍵 (複合式唯一索引鍵)，而不是根據電子郵件地址來建立唯一索引鍵。 在此情況下，在指定的 `CompanyID` 內允許這三個值的每個唯一組合。 例如，容器可以包含具有下列值的項目，其中每個項目都會遵守唯一索引鍵限制式。

|CompanyID|名字|姓氏|電子郵件地址|
|---|---|---|---|
|Contoso|Gaby|Duperre|gaby@contoso.com |
|Contoso|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Ivan|Duperre|gaby@fabrikam.com|
|Fabrkam|   |Duperre|gaby@fabraikam.com|
|Fabrkam|   |   |gaby@fabraikam.com|

如果您嘗試使用上表所列的組合來插入另一個項目，您將會收到錯誤，指出不符合唯一索引鍵限制式。 您將會以傳回訊息的形式收到「具有指定識別碼或名稱的資源已經存在」或「具有指定識別碼、名稱或唯一索引的資源已經存在」。  

## <a name="defining-a-unique-key"></a>定義唯一索引鍵

只有在建立 Cosmos 容器時，您才能定義唯一索引鍵。 唯一索引鍵的範圍限定為某個邏輯分割區。 在上述範例中，如果您根據郵遞區號來分割容器，最終將在每個邏輯分割區中具有重複的項目。 建立唯一索引鍵時，請考慮下列屬性：

* 您無法將現有容器更新為使用不同的唯一索引鍵。 換句話說，一旦使用唯一索引鍵原則建立容器之後，便無法變更此原則。

* 如果想要為現有容器設定唯一索引鍵，您必須使用唯一索引鍵限制式來建立新容器，然後使用適當的資料移轉工具來將資料從現有容器移至新容器。 對於 SQL 容器，使用[資料移轉工具](import-data.md)來移動資料。 對於 MongoDB 容器，使用 [mongoimport.exe 或 mongorestore.exe](mongodb-migrate.md) 來移動資料。

* 唯一索引鍵原則最多可包含 16 個路徑值 (例如：/firstName、/lastName、/address/zipCode)。 每個唯一索引鍵原則最多可以包含 10 個唯一索引鍵限制式或組合，而每個唯一索引限制式的結合路徑都不應超過 60 個位元組。 在前一個範例中，名字、姓氏和電子郵件地址合起來只是一個限制式，而它會使用 16 個可能路徑中的三個。

* 當容器具有唯一索引鍵原則時，建立、更新和刪除項目的要求單位 (RU) 費用會稍微更高些。

* 不支援疏鬆的唯一索引鍵。 如果遺漏了部分唯一路徑值，則會將它們視為 Null 值，並將其納入唯一性限制式。 因此，只有具 Null 值的單一項目能夠滿足此限制式。

* 唯一索引鍵名稱會區分大小寫。 例如，假設容器的唯一索引鍵限制式已設為 /address/zipcode。 如果您的資料具有名為 ZipCode 的欄位，Cosmos DB 就會插入 "null" 作為唯一索引鍵，因為 "zipcode" 與 "ZipCode" 不同。 由於這個區分大小寫的緣故，因此，無法插入包含 ZipCode 的所有其他記錄，因為重複的 "null" 將違反唯一索引鍵限制式。

## <a name="next-steps"></a>後續步驟

* 深入了解[邏輯分割區](partition-data.md)
