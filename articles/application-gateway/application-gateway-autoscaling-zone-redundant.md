---
title: Azure 中的自動調整規模和區域備援應用程式閘道 (公開預覽)
description: 本文介紹 Azure 應用程式 v2 SKU，其包含自動調整和區域備援功能。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 2/5/2019
ms.author: victorh
ms.openlocfilehash: 880a6fc9d5636ff0ac67d24d0ef66c8f48ae22fb
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2019
ms.locfileid: "55743645"
---
# <a name="autoscaling-and-zone-redundant-application-gateway-public-preview"></a>自動調整規模和區域備援應用程式閘道 (公開預覽)

在提供效能增強功能並新增重要新功能 (例如自動調整規模、區域備援及靜態 VIP 支援) 支援的新 v2 SKU 下，現以「公開預覽版」方式提供了「應用程式閘道」和「Web 應用程式防火牆」(WAF)。 除了已知限制一節中所列的幾個例外項目之外，新 v2 SKU 會繼續支援已正式推出之 SKU 下的現有功能。 新的 v2 SKU 包含下列增強功能：

- **自動調整**：自動調整 SKU 下的「應用程式閘道」或 WAF 部署可以根據變動的流量負載模式來相應增加或相應減少。 自動調整規模也可讓您在佈建時，無須選擇部署大小或執行個體計數。 因此，SKU 可提供真正的靈活彈性。 在新 SKU 中，「應用程式閘道」可在固定容量 (停用自動調整規模功能) 模式下運作，也可在啟用自動調整規模功能模式下運作。 固定容量模式適用於工作負載一致且可預測的案例。 自動調整規模模式則有利於在應用程式流量中可看到許多變異的應用程式。

   > [!NOTE]
   > 目前針對 WAF SKU 並未提供自動調整規模功能。 請為 WAF 設定固定容量模式，而不是自動調整規模模式。
- **區域備援**：「應用程式閘道」或 WAF 部署可以跨多個「可用性區域」，讓您無須使用「流量管理員」在每個區域中佈建及運轉個別的「應用程式閘道」執行個體。 您可以選擇已部署「應用程式閘道」執行個體的單一區域或多個區域，藉此確保區域在失敗後能夠復原。 應用程式的後端集區可以均等分散在可用性區域間。
- **效能增強功能**：與已正式推出的 SKU 相比，自動調整規模 SKU 最多可提升 5 倍的 SSL 卸載效能。
- **更快的佈建和更新速度**：與已正式推出的 SKU 相比，自動調整規模 SKU 提供更快的佈建和更新速度。
- **靜態 VIP**：應用程式閘道 VIP 現在獨家支援靜態 VIP 類型。 這可確保與應用程式閘道相關的 VIP 即使在重新啟動後也不會變更。

> [!IMPORTANT]
> 自動調整規模和區域備援應用程式閘道 SKU 目前為公開預覽版。 此預覽版是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽專用的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

![](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="supported-regions"></a>支援區域

自動調整 SKU 可在以下區域取得：美國中北部、美國中南部、美國西部、美國西部 2、美國東部、美國東部 2、美國中部、北歐、西歐、東南亞、法國中部、英國西部、日本東部、日本西部。

## <a name="pricing"></a>價格

預覽期間無須付費。 將向您收取的是應用程式閘道以外資源 (例如 Key Vault、虛擬機器等) 的費用。

## <a name="known-issues-and-limitations"></a>已知問題與限制

|問題|詳細資料|
|--|--|
|驗證憑證|不支援。<br>如需詳細資訊，請參閱[應用程式閘道端對端 SSL 的概觀](ssl-overview.md#end-to-end-ssl-with-the-v2-sku)。|
|在相同子網路上混合使用 Standard_v2 和標準應用程式閘道|不支援。<br>此外，如果已啟用自動調整，一個子網路只可以有一個應用程式閘道。|
|應用程式閘道子網路上的使用者定義路由 (UDR)|不支援|
|輸入連接埠範圍的 NSG| - Standard_v2 SKU 適用 65200 至 65535<br>- 標準 SKU 適用 65503 至 65534<br>如需詳細資訊，請參閱[常見問題集](application-gateway-faq.md#are-network-security-groups-supported-on-the-application-gateway-subnet)。|
|Azure 診斷中的效能記錄|不支援。<br>應該使用 Azure 計量。|
|計費|目前不收費。|
|FIPS 模式、WebSocket|目前不支援。|
|純 ILB 模式|目前不支援。 支援將公用與 ILB 模式搭配使用。|
|Web 應用程式防火牆自動調整規模|WAF 不支援自動調整規模模式。 支援固定容量模式。|
|Netwatcher 整合|公開預覽版不支援此功能。|

## <a name="next-steps"></a>後續步驟
- [使用 Azure PowerShell 建立具有保留虛擬 IP 位址的自動調整規模、區域備援應用程式閘道](tutorial-autoscale-ps.md)
- 深入了解[應用程式閘道](overview.md)。
- 深入了解 [Azure 防火牆](../firewall/overview.md)。