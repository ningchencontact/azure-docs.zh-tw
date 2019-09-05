---
title: 從 Azure VPN 閘道設定診斷記錄事件的警示
description: 在 VPN 閘道診斷記錄事件上設定警示的步驟
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: alzam
ms.openlocfilehash: d914c020553bace7ea5ab8898ac4093fea30e6c9
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70306997"
---
# <a name="set-up-alerts-on-diagnostic-log-events-from-vpn-gateway"></a>從 VPN 閘道設定診斷記錄事件的警示

本文可協助您使用 Azure Log Analytics，根據 Azure VPN 閘道的診斷記錄事件來設定警示。 

Azure 中提供下列記錄：

|***名稱*** | ***描述*** |
|---        | ---               |
|GatewayDiagnosticLog | 包含閘道設定事件、主要變更和維護事件的診斷記錄 |
|TunnelDiagnosticLog | 包含通道狀態變更事件。 通道連接/中斷線上活動具有狀態變更的摘要原因（如果適用） |
|RouteDiagnosticLog | 記錄在閘道上發生的靜態路由和 BGP 事件的變更 |
|IKEDiagnosticLog | 記錄閘道上的 IKE 控制訊息和事件 |
|P2SDiagnosticLog | 記錄閘道上的點對站控制訊息和事件 |

## <a name="setup"></a>設定警示

下列範例步驟會針對包含站對站 VPN 通道的中斷線上活動建立警示：


1. 在 Azure 入口網站中，搜尋 [**所有服務**] 底下的 [ **log analytics** ]，然後選取 [ **log analytics 工作區**]。

   ![前往 Log Analytics 工作區的選取專案](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert0.png "建立")

2. 在 [ **Log Analytics** ] 頁面上選取 [**建立**]。

   ![具有 [建立] 按鈕的 Log Analytics 頁面](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert1.png  "選取")

3. 選取 **[新建]** 並填入詳細資料。

   ![建立 Log Analytics 工作區的詳細資料](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert2.png  "選取")

4. 在 [**監視** > **診斷設定**] 分頁上尋找您的 VPN 閘道。

   ![在診斷設定中尋找 VPN 閘道的選項](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert3.png  "選取")

5. 若要開啟診斷，請按兩下閘道，然後選取 [**開啟診斷**]。

   ![開啟診斷的選取專案](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert4.png  "選取")

6. 填入詳細資料，並確定已選取 [**傳送至 Log Analytics** ] 和 [ **TunnelDiagnosticLog** ]。 選擇您在步驟3中建立的 Log Analytics 工作區。

   ![選取的核取方塊](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert5.png  "選取")

7. 請移至虛擬網路閘道資源的總覽，然後從 [**監視**] 索引標籤中選取 [**警示**]。然後建立新的警示規則，或編輯現有的警示規則。

   ![建立新警示規則的選取專案](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "選取")

   ![點對站](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "選取")
8. 選取 Log Analytics 工作區和資源。

   ![工作區和資源的選取專案](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert7.png  "選取")

9. 選取 [**自訂記錄搜尋**] 做為 [**新增條件**] 底下的 [信號邏輯]。

   ![自訂記錄搜尋的選取專案](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert8.png  "選取")

10. 在 [搜尋查詢] 文字方塊中，輸入下列查詢。 視需要取代 < > 和 TimeGenerated 中的值。

    ```
    AzureDiagnostics
    | where Category == "TunnelDiagnosticLog"
    | where _ResourceId == tolower("<RESOURCEID OF GATEWAY>")
    | where TimeGenerated > ago(5m) 
    | where remoteIP_s == "<REMOTE IP OF TUNNEL>"
    | where status_s == "Disconnected"
    | project TimeGenerated, OperationName, instance_s, Resource, ResourceGroup, _ResourceId 
    | sort by TimeGenerated asc
    ```

    將 [臨界值] 設定為0，然後選取 [**完成**]。

    ![輸入查詢並選取臨界值](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert9.png  "選取")

11. 在 [**建立規則**] 頁面上，選取 [**動作群組**] 區段底下的 [**建立新**的]。 填入詳細資料，然後選取 **[確定]** 。

    ![新動作群組的詳細資料](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert10.png  "選取")

12. 在 [**建立規則**] 頁面上，填入**自訂動作**的詳細資料，並確定 [**動作組名**] 區段中出現正確的名稱。 選取 [**建立警示規則**] 以建立規則。

    ![建立規則的選取專案](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert11.png  "選取")

## <a name="next-steps"></a>後續步驟

若要設定通道計量的警示，請參閱[設定 VPN 閘道計量的警示](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md)。
