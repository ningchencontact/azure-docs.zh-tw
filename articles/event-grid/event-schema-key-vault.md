---
title: Azure 事件方格 Azure Key Vault 事件架構
description: 說明使用 Azure 事件方格為 Azure Key Vault 事件提供的屬性和架構
services: event-grid
author: msmbaldwin
ms.service: event-grid
ms.topic: reference
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: c92352dd28b870c5f58dec0b82a8000f14a8e62d
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73033520"
---
# <a name="azure-event-grid-event-schema-for-azure-key-vault-preview"></a>適用于 Azure Key Vault 的 Azure 事件方格事件架構（預覽）

本文提供[Azure Key Vault](../key-vault/index.yml)事件的屬性和架構，目前處於預覽狀態。 如需事件結構描述的簡介，請參閱 [Azure Event Grid 事件結構描述](event-schema.md)。

## <a name="available-event-types"></a>可用的事件類型

Azure Key Vault 帳戶會發出下列事件種類：

| 事件完整名稱 | 事件顯示名稱 | 說明 |
| ---------- | ----------- |---|
| KeyVault. CertificateNewVersionCreated | 已建立憑證新版本 | 在建立新憑證或新憑證版本時觸發 |
| KeyVault. CertificateNearExpiry | 憑證即將到期 | 當憑證的目前版本即將到期時觸發（預設為到期日前30天） |
| KeyVault. CertificateExpired | 憑證到期 | 憑證過期時觸發 |
| KeyVault. KeyNewVersionCreated | 已建立金鑰新版本 | 在建立新的金鑰或新的金鑰版本時觸發 |
| KeyVault. KeyNearExpiry | 金鑰即將到期 | 當目前的金鑰版本即將到期時觸發（預設為到期日前30天） |
| KeyVault. KeyExpired | 金鑰已過期 | 在金鑰過期時觸發 |
| KeyVault. SecretNewVersionCreated | 已建立秘密新版本 | 在建立新的秘密或新的秘密版本時觸發 |
| KeyVault. SecretNearExpiry | 密碼即將到期 | 當目前的秘密版本即將到期時觸發（預設為到期日前30天） |
| KeyVault. SecretExpired | 密碼已過期 | 在秘密過期時觸發 |

## <a name="event-examples"></a>事件範例

下列範例顯示**KeyVault. SecretNewVersionCreated**的架構。

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

| 屬性 | Type | 描述 |
| ---------- | ----------- |---|
| id | string | 觸發此事件之物件的識別碼。 |
| vaultName | string | 觸發此事件的物件金鑰保存庫名稱。 |
| objectType | string | 觸發此事件之物件的類型 |
| 下方 | string | 觸發此事件的物件名稱 |
| version | string | 觸發此事件的物件版本 |
| nbf | number | 自 1970-01-01T00：00：00Z 屬於觸發此事件的物件之後的日期（以秒為單位） |
| exp | number | 觸發此事件的物件自 1970-01-01T00：00：00Z 之後的到期日（以秒為單位） |


## <a name="next-steps"></a>後續步驟

* 如需 Azure Event Grid 的簡介，請參閱[什麼是 Event Grid？](overview.md)
* 若要了解 Event Grid 訂用帳戶的建立，請參閱 [Event Grid 訂用帳戶結構描述](subscription-creation-schema.md)。
* 若要深入瞭解 Key Vault/事件格線整合，請參閱[使用 Azure 事件方格監視 Key Vault （預覽）](../key-vault/event-grid-overview.md)
* 若要查看 Key Vault/事件格線整合的教學課程，請參閱[如何：將 Key Vault 事件路由至自動化 Runbook （預覽）](../key-vault/event-grid-tutorial.md)。

- [Azure Key Vault 總覽](../key-vault/key-vault-overview.md)
- [Azure 事件方格總覽](overview.md)
- [使用 Azure 事件方格監視 Key Vault （預覽）](../key-vault/event-grid-overview.md)
- [如何：將 Key Vault 事件路由至自動化 Runbook （預覽）](../key-vault/event-grid-tutorial.md)。
- [Azure 自動化概觀](../automation/index.yml)
