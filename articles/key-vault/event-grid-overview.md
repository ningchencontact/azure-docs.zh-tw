---
title: 使用 Azure 事件方格監視 Key Vault
description: 使用 Azure 事件方格來訂閱 Key Vault 事件
services: media-services
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: article
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: 1fa554e03188c4d8d6227a6d2c0a560c3080b0fe
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73033494"
---
# <a name="monitoring-key-vault-with-azure-event-grid-preview"></a>使用 Azure 事件方格監視 Key Vault （預覽）

與 Event Grid Key Vault 的整合目前為預覽狀態。 它可讓使用者在金鑰保存庫中儲存的秘密狀態變更時收到通知。 狀態變更會定義為即將到期的秘密（在到期日30天內）、已過期的密碼，或有新版本可用的密碼。 支援三種秘密類型（金鑰、憑證和密碼）的通知。

應用程式可以使用新式無伺服器架構來回應這些事件，而不需要複雜的程式碼或昂貴且無效率的輪詢服務。 事件會透過[Azure 事件方格](https://azure.microsoft.com/services/event-grid/)推送至事件處理常式，例如[Azure Functions](https://azure.microsoft.com/services/functions/)、 [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)，或甚至是您自己的 Webhook，而您只需支付使用的部分。 如需價格的資訊，請參閱[事件格線價格](https://azure.microsoft.com/pricing/details/event-grid/)。

## <a name="key-vault-events-and-schemas"></a>Key Vault 事件和架構

Event Grid 使用[事件訂閱](../event-grid/concepts.md#event-subscriptions)將事件訊息路由至訂閱者。 Key Vault 事件包含回應資料變更所需的所有資訊。 您可以識別 Key Vault 事件，因為「事件識別碼」屬性會以 "KeyVault" 開頭。

如需詳細資訊，請參閱[Key Vault 事件架構](../event-grid/event-schema-key-vault.md)。

> [!NOTE]
> 只有在設定訂用帳戶之後所建立的秘密版本（全部三種類型），才會觸發事件。
>
> 針對現有的密碼，您必須產生新的版本。

## <a name="practices-for-consuming-events"></a>消費事件做法

處理 Key Vault 事件的應用程式應該遵循幾個建議做法：

* 可以將多個訂用帳戶設定為將事件路由到相同的事件處理常式。 請務必不要假設事件來自特定來源，但要檢查訊息的主題，以確保其來自您預期的金鑰保存庫。
* 同樣地，檢查 eventType 也是必須進行的步驟之一，而且不要假設您收到的所有事件都是您預期的類型。
* 請忽略您不了解的欄位。  此做法將有助於保持未來可能新增功能的彈性。
* 使用 "subject" 前置詞和後置詞相符，將事件限制為特定的事件。

## <a name="next-steps"></a>後續步驟

- [Azure Key Vault 總覽](key-vault-overview.md)
- [Azure 事件方格總覽](../event-grid/overview.md)
- [如何：將 Key Vault 事件路由至自動化 Runbook （預覽）](event-grid-tutorial.md)。
- [適用于 Azure Key Vault 的 Azure 事件方格事件架構（預覽）](../event-grid/event-schema-key-vault.md)
- [Azure 自動化概觀](../automation/index.yml)
