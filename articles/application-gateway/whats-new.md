---
title: Azure 應用程式閘道的新功能
description: 了解 Azure 應用程式閘道的新功能，例如最新的版本資訊、已知問題、錯誤 (bug) 修正、已被取代的功能，以及即將進行的變更。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.date: 4/30/2019
ms.author: victorh
ms.openlocfilehash: 6755825aa123b3d7d1eed23291790ff3dc477ac5
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382474"
---
# <a name="whats-new-in-azure-application-gateway"></a>Azure 應用程式閘道的新功能為何？

Azure 應用程式閘道會持續更新。 為了讓您隨時掌握最新的開發訊息，此文章提供下列相關資訊：

- 最新版本
- 已知問題
- 錯誤修正
- 已被取代的功能

## <a name="new-features"></a>新功能

|功能  |說明  |新增日期  |
|---------|---------|---------|
|更多計量 |我們已新增下列新的計量，以協助您監視應用程式閘道 v2 SKU：[計時相關的計量](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#timing-metrics) \(部分機器翻譯\)、後端回應狀態、接收的位元組、已傳送的位元組、用戶端 TLS 通訊協定和目前的計算單位。 請參閱[應用程式閘道 V2 SKU支援的計量](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#metrics-supported-by-application-gateway-v2-sku) \(部分機器翻譯\)。 |2019 年 8 月 |
|WAF 自訂規則 |應用程式閘道 WAF_v2 現在支援建立自訂規則。 請參閱[應用程式閘道自訂規則](custom-waf-rules-overview.md)。 |2019 年 6 月 |
|自動調整規模、區域備援、靜態 VIP 支援 GA |已重寫 v2 SKU (支援自動調整規模、區域備援、增強效能、靜態 VIP、金鑰保存庫，及標頭) 的一般可用性。 請參閱[應用程式閘道自動調整規模文件](application-gateway-autoscaling-zone-redundant.md)。 |2019 年 4 月 |
|金鑰保存庫整合 |對於在已啟用 HTTPS 的接聽程式中附加的伺服器認證，應用程式閘道現在支援與金鑰保存庫 (公開預覽) 整合。 請參閱 [具備金鑰保存庫認證的 SSL 終止](key-vault-certs.md)。 |2019 年 4 月 |
|標頭 CRUD/重寫     |您現在可以重新撰寫 HTTP 標頭。 請參閱[教學課程：建立應用程式閘道及重寫 HTTP 標題](tutorial-http-header-rewrite-powershell.md)，以取得詳細資訊。|2018 年 12 月|
|WAF 組態和排除清單     |我們已新增更多選項，以協助您設定 WAF 和減少誤判。 如需詳細資訊，請參閱 [Web 應用程式防火牆要求大小限制與排除清單](application-gateway-waf-configuration.md)。|2018 年 12 月|
|自動調整規模、區域備援、靜態 VIP 支援      |v2 SKU 有許多改進，例如自動調整、增進的效能等等。 如需詳細資訊，請參閱[什麼是 Azure 應用程式閘道](overview.md)。|2018 年 9 月|
|清空連線     |清空連線可讓您依正常程序移除後端集區中的成員。 如需詳細資訊，請參閱[清空連線](overview.md#connection-draining)。|2018 年 9 月|
|自訂錯誤頁面     |透過自訂錯誤頁面中，您可以用其餘網站的格式建立錯誤頁面。 若要這麼做，請參閱[建立應用程式閘道的自訂錯誤頁面](custom-error.md)。|2018 年 9 月|
|計量的增強功能     |透過強化的計量，您可以更清楚地檢視應用程式閘道的狀態。 若要啟用應用程式閘道的計量，請參閱[應用程式閘道的後端健康情況、診斷記錄和計量](application-gateway-diagnostics.md)。|2018 年 6 月|

## <a name="next-steps"></a>後續步驟

如需 Azure 應用程式閘道的詳細資訊，請參閱[什麼是 Azure 應用程式閘道？](overview.md)
