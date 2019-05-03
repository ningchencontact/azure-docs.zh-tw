---
title: Azure 區塊鏈限制
description: 服務和 Azure 區塊鏈 Service 中的功能限制的概觀
services: azure-blockchain
keywords: 區塊鏈
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 169ec7a8ef407af3f754046aa8e3b06793a7e962
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028166"
---
# <a name="limits-in-azure-blockchain-service"></a>Azure 區塊鏈服務中的限制

Azure 區塊鏈服務有服務和功能的限制，例如，成員可以有的節點、 協會限制和儲存體數量的數目。

## <a name="pricing-tier"></a>定價層

在交易和驗證程式節點上的最大限制取決於是否您佈建 Azure 區塊鏈服務在基本或標準定價層。

| 定價層 | 最大交易的節點 | 最大的驗證程式節點 |
|:---|:---:|:---:|
| 基本 | 10 | 1 |
| 標準 | 10 | 2 |

不支援成員建立之後，請變更基本和標準之間的定價層。

## <a name="storage-capacity"></a>儲存體容量

可用於每個節點交易記錄資料庫資料和記錄檔的儲存體的最大數量是 1 tb。

不支援減少交易記錄資料庫和記錄儲存體大小。

## <a name="consortium-limits"></a>協會限制

* **協會和成員的名稱必須是唯一**的 Azure 區塊鏈 Service 中的其他協會和成員名稱。

* **無法變更成員和協會名稱**

* **協會中的所有成員必須都位於相同的定價層**

* **協會所參與的所有成員必須都位於相同的區域**

    在協會中建立的第一個成員指定區域。 受邀的成員協會必須位於相同區域的第一個成員。 限制所有成員，以相同的區域，有助於確保不會造成負面影響網路共識。

* **協會必須有至少一個系統管理員**

    如果只有一位管理員正在協會，無法移除自己協會或刪除其成員，直到另一個系統管理員新增或升級協會。

* **無法再加入協會所移除的成員**

    相反地，它們必須是受邀加入協會，並建立新的成員。 保留過去的交易，不會刪除其現有的成員資源。

* **協會中的所有成員必須都使用相同的交易記錄資料庫版本**

    如需有關修補、 更新和 Azure 區塊鏈服務中可用的交易記錄資料庫版本的詳細資訊，請參閱[修補、 更新和版本](ledger-versions.md)。

## <a name="next-steps"></a>後續步驟

* [修補、 更新和版本](ledger-versions.md)
