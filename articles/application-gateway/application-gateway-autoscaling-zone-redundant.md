---
title: Azure 中的自動調整規模和區域備援應用程式閘道 (公開預覽)
description: 本文介紹 Azure 應用程式 v2 SKU，其包含自動調整和區域備援功能。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 3/6/2019
ms.author: victorh
ms.openlocfilehash: 95b14a0028134e522206f3595bc3b9ebf9aaf396
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2019
ms.locfileid: "59548699"
---
# <a name="autoscaling-and-zone-redundant-application-gateway-public-preview"></a>自動調整規模和區域備援應用程式閘道 (公開預覽)

在提供效能增強功能並新增重要新功能 (例如自動調整規模、區域備援及靜態 VIP 支援) 支援的新 v2 SKU 下，現以「公開預覽版」方式提供了「應用程式閘道」和「Web 應用程式防火牆」(WAF)。 除了已知限制一節中所列的幾個例外項目之外，新 v2 SKU 會繼續支援已正式推出之 SKU 下的現有功能。 新的 v2 SKU 包含下列增強功能：

- **自動調整**：自動調整 SKU 下的「應用程式閘道」或 WAF 部署可以根據變動的流量負載模式來相應增加或相應減少。 自動調整規模也可讓您在佈建時，無須選擇部署大小或執行個體計數。 此 SKU 提供，則為 true 的彈性。 在新 SKU 中，「應用程式閘道」可在固定容量 (停用自動調整規模功能) 模式下運作，也可在啟用自動調整規模功能模式下運作。 固定容量模式適用於工作負載一致且可預測的案例。 自動調整規模模式則有利於在應用程式流量中可看到許多變異的應用程式。

- **區域備援**：「應用程式閘道」或 WAF 部署可以跨多個「可用性區域」，讓您無須使用「流量管理員」在每個區域中佈建及運轉個別的「應用程式閘道」執行個體。 您可以選擇已部署「應用程式閘道」執行個體的單一區域或多個區域，藉此確保區域在失敗後能夠復原。 應用程式的後端集區可以均等分散在可用性區域間。
- **效能增強功能**：與已正式推出的 SKU 相比，自動調整規模 SKU 最多可提升 5 倍的 SSL 卸載效能。
- **更快的佈建和更新速度**：與已正式推出的 SKU 相比，自動調整規模 SKU 提供更快的佈建和更新速度。
- **靜態 VIP**：應用程式閘道 VIP 現在獨家支援靜態 VIP 類型。 這可確保應用程式閘道相關聯的 VIP 不會變更即使重新啟動。

> [!IMPORTANT]
> 自動調整規模和區域備援應用程式閘道 SKU 目前為公開預覽版。 此預覽版是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽專用的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

![](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

> [!NOTE]
> 自動調整和區域備援應用程式閘道 SKU 現在支援[預設健全狀況探查](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#default-health-probe)自動監視它的後端集區中的所有資源的健全狀況，並反白顯示會被視為這些後端成員狀況不良。 預設健全狀況探查將會自動設定，您還沒有設定任何自訂探查設定這些後端。 若要進一步了解，請參閱[健康情況探查應用程式閘道中](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview)。

## <a name="feature-comparison-between-v1-sku-and-v2-sku"></a>SKU v1 與 v2 SKU 之間的功能比較

下表比較每個 SKU 提供的功能。

|                                                   | v1 SKU   | v2 SKU   |
| ------------------------------------------------- | -------- | -------- |
| 自動調整                                       |          | &#x2713; |
| 區域備援                                   |          | &#x2713; |
| &nbsp;靜態的 VIP&nbsp;&nbsp;                      |          | &#x2713; |
| URL 型路由                                 | &#x2713; | &#x2713; |
| 多網站裝載                             | &#x2713; | &#x2713; |
| 流量重新導向                               | &#x2713; | &#x2713; |
| Web 應用程式防火牆 (WAF)                    | &#x2713; | &#x2713; |
| 安全通訊端層 (SSL) 終止            | &#x2713; | &#x2713; |
| 端對端 SSL 加密                         | &#x2713; | &#x2713; |
| 工作階段親和性                                  | &#x2713; | &#x2713; |
| 自訂錯誤頁面                                | &#x2713; | &#x2713; |
| 請重寫 HTTP (S) 標頭                           |          | &#x2713; |
| WebSocket 支援                                 | &#x2713; | &#x2713; |
| HTTP/2 支援                                    | &#x2713; | &#x2713; |
| 清空連線                               | &#x2713; | &#x2713; |
| Azure Kubernetes Service (AKS) 輸入控制器 |          | &#x2713; |

## <a name="supported-regions"></a>支援區域

自動調整 SKU 可在以下區域取得：美國中北部、美國中南部、美國西部、美國西部 2、美國東部、美國東部 2、美國中部、北歐、西歐、東南亞、法國中部、英國西部、日本東部、日本西部。

## <a name="pricing"></a>價格

在預覽期間，完全免費。 您需支付應用程式閘道，Key Vault 中，虛擬機器，例如以外的資源，並以此類推。

## <a name="known-issues-and-limitations"></a>已知問題與限制

|問題|詳細資料|
|--|--|
|驗證憑證|不支援。<br>如需詳細資訊，請參閱[應用程式閘道端對端 SSL 的概觀](ssl-overview.md#end-to-end-ssl-with-the-v2-sku)。|
|在相同子網路上混合使用 Standard_v2 和標準應用程式閘道|不支援|
|應用程式閘道子網路上的使用者定義路由 (UDR)|不支援|
|輸入連接埠範圍的 NSG| - Standard_v2 SKU 適用 65200 至 65535<br>- 標準 SKU 適用 65503 至 65534<br>如需詳細資訊，請參閱[常見問題集](application-gateway-faq.md#are-network-security-groups-supported-on-the-application-gateway-subnet)。|
|Azure 診斷中的效能記錄|不支援。<br>應該使用 Azure 計量。|
|計費|目前不收費。|
|FIPS 模式|目前不支援。|
|純 ILB 模式|目前不支援。 支援將公用與 ILB 模式搭配使用。|
|Netwatcher 整合|公開預覽版不支援此功能。|

## <a name="next-steps"></a>後續步驟
- [使用 Azure PowerShell 建立具有保留虛擬 IP 位址的自動調整規模、區域備援應用程式閘道](tutorial-autoscale-ps.md)
- 深入了解[應用程式閘道](overview.md)。
- 深入了解 [Azure 防火牆](../firewall/overview.md)。
