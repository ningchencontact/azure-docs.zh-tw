---
title: 自動成長使用 Azure 入口網站適用於 MySQL 的 Azure 資料庫中的儲存體
description: 這篇文章說明如何啟用自動成長的 Azure 資料庫的儲存體，適用於 MySQL 使用 Azure 入口網站
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: 5343475f38dd5389d6b0e266ff7167925cd38d71
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66676784"
---
# <a name="auto-grow-storage-in-azure-database-for-mysql-using-the-azure-portal"></a>自動成長使用 Azure 入口網站適用於 MySQL 的 Azure 資料庫中的儲存體
本文說明如何設定 Azure Database for MySQL 伺服器的儲存體成長而不會影響工作負載。

伺服器達到配置的儲存體限制時，會將伺服器標示為唯讀。 不過，如果您啟用儲存體自動成長，伺服器儲存體增加來容納不斷成長的資料。 針對具有小於 100 GB 佈建儲存體的伺服器，佈建的儲存體大小會增加 5 GB 的可用儲存體未達 1 GB 或 10%的已佈建的儲存體的較大者為。 針對具有超過 100GB 的佈建的儲存體的伺服器，佈建的儲存體大小會增加 5%，低於佈建儲存體大小的 5%的可用儲存空間時。 所指定的最大儲存體限制[此處](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers#storage)套用。

## <a name="prerequisites"></a>必要條件
若要完成本操作說明指南，您需要：
- [Azure Database for MySQL 伺服器](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="enable-storage-auto-grow"></a>啟用儲存體自動成長 

請遵循下列步驟來設定的 MySQL 伺服器儲存體自動成長：

1. 在  [Azure 入口網站](https://portal.azure.com/)，選取您的 Azure Database for MySQL 伺服器。

2. 在 [MySQL 伺服器] 頁面上底下**設定**標題之下，按一下**定價層**以開啟 [定價層] 頁面。

3. 在 [自動成長] 區段中，選取**是**若要啟用儲存體自動成長。

    ![Azure Database for MySQL-Settings_Pricing_tier-自動成長](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. 按一下 [確定]  儲存變更。

5. 通知會確認該自動成長已成功啟用。

    ![Azure Database for MySQL-自動成長成功](./media/howto-auto-grow-storage-portal/5-auto-grow-success.png)

## <a name="next-steps"></a>後續步驟

深入了解[如何建立度量警示](howto-alert-on-metric.md)。
