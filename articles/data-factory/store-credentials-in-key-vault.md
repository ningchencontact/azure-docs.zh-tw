---
title: "在 Azure Key Vault 中儲存認證 | Microsoft Docs"
description: "了解如何為 Azure Key Vault 中使用的資料存放區儲存認證，Azure Data Factory 可以在執行階段自動擷取。"
services: data-factory
author: linda33wj
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2017
ms.author: jingwang
ms.openlocfilehash: 51318a6bb4f88759984531b005c65712b6817ec0
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="store-credential-in-azure-key-vault"></a>在 Azure Key Vault 中儲存認證

您可以在 [Azure Key Vault](../key-vault/key-vault-whatis.md) 中儲存供資料存放區和計算使用的認證。 執行活動 (該活動使用資料存放區/計算) 時，Azure Data Factory 會擷取認證。

目前，自訂活動以外的所有活動類型都支援這項功能。 特別針對連接器設定，查看[每個連接器主題](copy-activity-overview.md#supported-data-stores-and-formats)中的＜連結服務屬性＞一節以取得詳細資訊。

> [!NOTE]
> 本文適用於第 2 版的 Data Fatory (目前為預覽版)。 如果您使用第 1 版的 Data Factory 服務 (正式推出版本 (GA))，請參閱 [Data Factory 第 1 版文件](v1/data-factory-introduction.md)。

## <a name="prerequisites"></a>先決條件

此功能依賴資料處理站服務識別。 請參閱[資料處理站服務識別](data-factory-service-identity.md)了解其運作方式，並確定您的資料處理站有相關聯的服務識別。

## <a name="steps"></a>步驟

若要參考儲存在 Azure Key Vault 中的認證，您需要：

1. **[擷取資料處理站服務識別](data-factory-service-identity.md#retrieve-service-identity)**，做法是複製與資料處理站一起產生的 "SERVICE IDENTITY APPLICATION ID"。
2. **將服務識別存取權授與您的 Azure Key Vault。** 在金鑰保存庫 -> 存取原則 -> 新增 -> 搜尋此服務識別應用程式識別碼，以授與 [祕密] 權限下拉式清單中的 [取得] 權限。 這樣可以讓這個指定的處理站存取金鑰保存庫中的密碼。
3. **建立指向您的 Azure Key Vault 的已連結服務。** 請參閱 [Azure Key Vault 已連結服務](#azure-key-vault-linked-service)。
4. **建立資料存放區已連結服務，其中參考儲存在金鑰保存庫的對應密碼。** 請參閱[參考儲存在金鑰保存庫中的祕密](#reference-secret-stored-in-key-vault)。

## <a name="azure-key-vault-linked-service"></a>Azure Key Vault 已連結服務

以下是針對 Azure Key Vault 已連結服務支援的屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | type 屬性必須設為：**AzureKeyVault**。 | yes |
| baseUrl | 指定 Azure Key Vault URL。 | yes |

**範例：**

```json
{
    "name": "AzureKeyVaultLinkedService",
    "properties": {
        "type": "AzureKeyVault",
        "typeProperties": {
            "baseUrl": "https://<azureKeyVaultName>.vault.azure.net"
        }
    }
}
```

## <a name="reference-secret-stored-in-key-vault"></a>參考儲存在金鑰保存庫中的祕密

當您在參考金鑰保存庫密碼的已連結服務中設定欄位時，支援下列屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | 欄位的 type 屬性必須設定為：**AzureKeyVaultSecret**。 | yes |
| secretName | Azure Key Vault 中密碼的名稱。 | yes |
| secretVersion | Azure Key Vault 中密碼的版本。<br/>如果未指定，它會一律使用最新版本的密碼。<br/>如果指定，則它會遵循指定的版本。| 否 |
| store | 代表您用來儲存認證的 Azure Key Vault 已連結服務。 | yes |

**範例：(請參閱「密碼」一節)**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "typeProperties": {
            "deploymentType": "<>",
            "organizationName": "<>",
            "authenticationType": "<>",
            "username": "<>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
}
```

## <a name="next-steps"></a>後續步驟
如需 Azure Data Factory 中的複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。