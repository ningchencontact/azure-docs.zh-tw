---
title: 適用于 Azure Key Vault 的 Azure 事件方格事件架構
description: 說明使用 Azure 事件方格為 Azure Key Vault 事件提供的屬性和架構
services: event-grid
author: msmbaldwin
ms.service: event-grid
ms.topic: reference
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: 17404388b2b6c3fee1c6ab666f7233a66817f642
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082874"
---
# <a name="azure-event-grid-event-schema-for-azure-key-vault-preview"></a>適用于 Azure Key Vault 的 Azure 事件方格事件架構（預覽）

本文提供[Azure Key Vault](../key-vault/index.yml)中事件的屬性和架構，目前為預覽狀態。 如需事件結構描述的簡介，請參閱 [Azure 事件格線事件結構描述](event-schema.md)。

## <a name="available-event-types"></a>可用的事件類型

Azure Key Vault 帳戶會產生下列事件種類：

| 事件完整名稱 | 事件顯示名稱 | 描述 |
| ---------- | ----------- |---|
| KeyVault. CertificateNewVersionCreated | 已建立憑證新版本 | 在建立新憑證或新憑證版本時觸發。 |
| KeyVault. CertificateNearExpiry | 憑證即將到期 | 當憑證的目前版本即將到期時觸發。 （預設值為到期日前30天）。 |
| KeyVault. CertificateExpired | 憑證到期 | 當憑證過期時觸發。 |
| KeyVault. KeyNewVersionCreated | 已建立金鑰新版本 | 在建立新的金鑰或新的金鑰版本時觸發。 |
| KeyVault. KeyNearExpiry | 金鑰即將到期 | 當目前的金鑰版本即將到期時觸發。 （預設值為到期日前30天）。 |
| KeyVault. KeyExpired | 金鑰已過期 | 在金鑰過期時觸發。 |
| KeyVault. SecretNewVersionCreated | 已建立秘密新版本 | 在建立新的秘密或新的秘密版本時觸發。 |
| KeyVault. SecretNearExpiry | 密碼即將到期 | 當目前的秘密版本即將到期時觸發。 （預設值為到期日前30天）。 |
| KeyVault. SecretExpired | 密碼已過期 | 當秘密過期時觸發。 |

## <a name="event-examples"></a>事件範例

下列範例顯示**KeyVault. SecretNewVersionCreated**的架構：

```JSON
[
   {
      "id":"00eccf70-95a7-4e7c-8299-2eb17ee9ad64",
      "topic":"/subscriptions/{subscription-id}/resourceGroups/sample-rg/providers/Microsoft.KeyVault/vaults/sample-kv",
      "subject":"newsecret",
      "eventType":"Microsoft.KeyVault.SecretNewVersionCreated",
      "eventTime":"2019-07-25T01:08:33.1036736Z",
      "data":{
         "Id":"https://sample-kv.vault.azure.net/secrets/newsecret/ee059b2bb5bc48398a53b168c6cdcb10",
         "vaultName":"sample-kv",
         "objectType":"Secret",
         "objectName ":"newsecret",
         "version":" ee059b2bb5bc48398a53b168c6cdcb10",
         "nbf":"1559081980",
         "exp":"1559082102"
      },
      "dataVersion":"1",
      "metadataVersion":"1"
   }
]
```

## <a name="event-properties"></a>事件屬性

事件具有下列的最高層級資料：

| 屬性 | 在系統提示您進行確認時，輸入 | 描述 |
| ---------- | ----------- |---|
| id | 字串 | 觸發此事件之物件的識別碼 |
| vaultName | 字串 | 觸發此事件的物件金鑰保存庫名稱 |
| objectType | 字串 | 觸發此事件之物件的類型 |
| 下方 | 字串 | 觸發此事件的物件名稱 |
| 版本 | 字串 | 觸發此事件的物件版本 |
| nbf | number | 觸發此事件的物件自 1970-01-01T00：00：00Z 之後的非之前日期（以秒為單位） |
| exp | number | 觸發此事件的物件自 1970-01-01T00：00：00Z 之後的到期日（以秒為單位） |


## <a name="next-steps"></a>後續步驟

* 若要初步了解 Azure Event Grid，請參閱[什麼是 Event Grid？](overview.md)。
* 如需有關如何建立 Azure 事件方格訂用帳戶的詳細資訊，請參閱[Event grid 訂](subscription-creation-schema.md)用帳戶架構。
* 若要深入瞭解 Key Vault 與 Event Grid 整合，請參閱[使用 Azure 事件方格監視 Key Vault （預覽）](../key-vault/event-grid-overview.md)。
* 如需 Key Vault 與 Event Grid 整合的教學課程，請參閱[使用 Azure 事件方格接收和回應金鑰保存庫通知（預覽）](../key-vault/event-grid-tutorial.md)。
* 若要取得 Key Vault 和 Azure 自動化的其他指引，請參閱：
    - [什麼是 Azure 金鑰保存庫？](../key-vault/key-vault-overview.md)
    - [使用 Azure 事件方格監視 Key Vault (預覽)](../key-vault/event-grid-overview.md)
    - [使用 Azure 事件方格來接收和回應金鑰保存庫通知（預覽）](../key-vault/event-grid-tutorial.md)
    - [Azure 自動化概觀](../automation/index.yml)
