---
title: 在 Azure Data Factory 中將連結服務參數化 | Microsoft Docs
description: 了解如何在 Azure Data Factory 中將連結服務參數化，並在執行階段傳遞動態值。
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: douglasl
ms.openlocfilehash: 287dcdedede5cab575aa0b9a73ec3e122556dc93
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900718"
---
# <a name="parameterize-linked-services-in-azure-data-factory"></a>在 Azure Data Factory 中將連結服務參數化

您現在可以將連結服務參數化，並在執行階段傳遞動態值。 舉例來說，若要連線至同一部 Azure SQL Database 伺服器上的不同資料庫，您現在可以將連結服務定義中的資料庫名稱參數化。 這讓您無需為 Azure SQL 資料庫伺服器上的每個資料庫各建立一個連結服務。 您也可以將連結服務中的其他屬性參數化，例如 *User name*。

您可以使用 Azure 入口網站中的 Data Factory UI 或程式設計介面將連結服務參數化。

> [!TIP]
> 建議您不要將密碼或秘密參數化。 請改為將所有連接字串儲存在 Azure Key Vault 中，並將 *Secret Name* 參數化。

## <a name="supported-data-stores"></a>支援的資料存放區

目前，Azure 入口網站的 Data Factory UI 中支援下列資料存放區的連結服務參數化。 至於其他資料存放區，您可以選取 [管線] 索引標籤上的**程式碼**圖示，並使用 JSON 編輯器，將連結服務參數化。
- 連接字串
- Azure SQL 資料倉儲
- SQL Server
- Oracle
- Cosmos DB
- Amazon Redshift
- MySQL
- 適用於 MySQL 的 Azure 資料庫

## <a name="data-factory-ui"></a>Data Factory UI

![將動態內容新增至連結服務定義](media/parameterize-linked-services/parameterize-linked-services-image1.png)

![建立新的參數](media/parameterize-linked-services/parameterize-linked-services-image2.png)

## <a name="json"></a>JSON

```json
{
    "name": "AzureSqlDatabase",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "value": "Server=tcp:myserver.database.windows.net,1433;Database=@{linkedService().DBName};User ID=user;Password=fake;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
                "type": "SecureString"
            }
        },
        "connectVia": null,
        "parameters": {
            "DBName": {
                "type": "String"
            }
        }
    }
}
```
