---
title: 適用于搜尋索引的 Azure SQL 受控執行個體連接-Azure 搜尋服務
description: 啟用公用端點, 允許從 Azure 搜尋服務上的索引子連接到 SQL 受控實例。
author: vl8163264128
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: victliu
ms.openlocfilehash: 07bc1a55c0222fda87b28acbaa8bfe552fb8e6ed
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/30/2019
ms.locfileid: "70186642"
---
# <a name="configure-a-connection-from-an-azure-search-indexer-to-sql-managed-instance"></a>設定從 Azure 搜尋服務索引子到 SQL 受控執行個體的連接
如[使用索引子將 Azure SQL Database 連接到 Azure 搜尋服務](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq)中所述, 透過公用端點 Azure 搜尋服務支援針對**SQL 受控實例**建立索引子。

## <a name="create-azure-sql-managed-instance-with-public-endpoint"></a>建立具有公用端點的 Azure SQL 受控執行個體
建立 SQL 受控執行個體並選取 [**啟用公用端點**] 選項。

   ![啟用公用端點](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/enable-public-endpoint.png "啟用公用端點")

## <a name="enable-azure-sql-managed-instance-public-endpoint"></a>啟用 Azure SQL 受控執行個體公用端點
您也可以在 [**安全性** > ] [**虛擬網路** > ] [**公用端點** > **啟用**] 底下的現有 SQL 受控執行個體上啟用公用端點。

   ![啟用公用端點](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-vnet.png "啟用公用端點")

## <a name="verify-nsg-rules"></a>驗證 NSG 規則
檢查網路安全性群組是否具有正確的**輸入安全性規則**, 以允許來自 Azure 服務的連線。

   ![NSG 輸入安全性規則](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/nsg-rule.png "NSG 輸入安全性規則")

## <a name="get-public-endpoint-connection-string"></a>取得公用端點連接字串
請確定您使用的是**公用端點**的連接字串 (埠 3342, 而不是埠 1433)。

   ![公用端點連接字串](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-connection-string.png "公用端點連接字串")

## <a name="next-steps"></a>後續步驟
使用設定時, 您現在可以使用入口網站或 REST API, 將 SQL 受控執行個體指定為 Azure 搜尋服務索引子的資料來源。 如需詳細資訊，請參閱 [使用索引子將 Azure SQL Database 連接至 Azure 搜尋服務](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) 。
