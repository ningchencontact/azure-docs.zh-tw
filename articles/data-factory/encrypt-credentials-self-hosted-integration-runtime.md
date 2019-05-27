---
title: 在 Azure Data Factory 中加密認證 | Microsoft Docs
description: 了解如何在含自我裝載整合執行階段的電腦上加密和儲存內部部署資料存放區的認證。
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: abnarain
ms.openlocfilehash: 8e705a4430f6ccee847dc7d41ef80456a6dc4ea5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "66155141"
---
# <a name="encrypt-credentials-for-on-premises-data-stores-in-azure-data-factory"></a>在 Azure Data Factory 中加密內部部署資料存放區的認證
您可以在含自我裝載整合執行階段的電腦上加密和儲存內部部署資料存放區的認證 (含敏感性資訊的連結服務)。 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

您會將具有認證的 JSON 定義檔傳遞至 <br/>[**新 AzDataFactoryV2LinkedServiceEncryptedCredential** ](/powershell/module/az.datafactory/New-AzDataFactoryV2LinkedServiceEncryptedCredential) cmdlet 來產生輸出 JSON 定義檔與加密認證。 然後，使用更新的 JSON 定義來建立連結服務。

## <a name="author-sql-server-linked-service"></a>編寫 SQL Server 連結服務
在任何資料夾中，使用下列內容建立名為 **SqlServerLinkedService.json** 的 JSON 檔案：  

先將 `<servername>`、`<databasename>`、`<username>` 和 `<password>` 取代為 SQL Server 的值，再儲存檔案。 並且，將 `<integration runtime name>` 取代為您整合執行階段的名稱。 

```json
{
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<servername>;Database=<databasename>;User ID=<username>;Password=<password>;Timeout=60"
            }
        },
        "connectVia": {
            "type": "integrationRuntimeReference",
            "referenceName": "<integration runtime name>"
        },
        "name": "SqlServerLinkedService"
    }
}
```

## <a name="encrypt-credentials"></a>加密認證
若要加密內部部署自我裝載的整合執行階段之 JSON 承載中的敏感性資料，請執行**新增 AzDataFactoryV2LinkedServiceEncryptedCredential**，並傳入 JSON 承載。 這個 Cmdlet 確保使用 DPAPI 來加密認證，並將其本機儲存在自我裝載整合執行階段節點上。 包含加密認證參考輸出承載可以重新導向至另一個 JSON 檔案 （在此情況下 '是 encryptedLinkedService.json'）。

```powershell
New-AzDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "SqlServerLinkedService" -DefinitionFile ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
```

## <a name="use-the-json-with-encrypted-credentials"></a>搭配使用 JSON 與加密認證
現在，使用包含加密認證之前一個命令的輸出 JSON 檔案來設定 **SqlServerLinkedService**。

```powershell
Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -DefinitionFile ".\encryptedSqlServerLinkedService.json" 
```

## <a name="next-steps"></a>後續步驟
如需資料移動安全性考量的資訊，請參閱[資料移動安全性考量](data-movement-security-considerations.md)。

