---
title: 從 Azure VPN 閘道設定診斷記錄事件的警示
description: 若要設定 VPN 閘道診斷記錄事件的警示的步驟
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptional
ms.date: 06/12/2019
ms.author: alzam
ms.openlocfilehash: 6c85f47a2e3691306d59c5c44856fd08c07f2d36
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67202883"
---
# <a name="set-up-alerts-on-diagnostic-log-events-from-vpn-gateway"></a>從 VPN 閘道設定診斷記錄事件的警示

這篇文章可協助您設定以從 Azure VPN 閘道，使用 Azure Log Analytics 的診斷記錄事件為基礎的警示。 

下列記錄檔可在 Azure 中：

|***名稱*** | ***說明*** |
|---        | ---               |
|GatewayDiagnosticLog | 包含閘道設定事件、 主要的變更和維護事件的診斷記錄檔 |
|TunnelDiagnosticLog | 包含通道狀態變更事件。 如果適用的話，通道連接/中斷連接事件會有摘要的理由，狀態變更 |
|RouteDiagnosticLog | 靜態路由與發生在閘道的 BGP 事件記錄檔變更 |
|IKEDiagnosticLog | 控制項的 IKE 訊息和閘道上的事件記錄 |
|P2SDiagnosticLog | 點對站台控制訊息與在閘道上的事件記錄 |

## <a name="setup"></a>設定警示

下列範例步驟會建立牽涉到站台對站 VPN 通道中斷連線事件的警示：


1. 在 Azure 入口網站中，搜尋**Log Analytics**下方**所有服務**，然後選取**Log Analytics 工作區**。

   ![選取項目移至 Log Analytics 工作區](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert0.png "建立")

2. 選取  **Create**上**Log Analytics**頁面。

   ![Log Analytics 頁面使用 [建立] 按鈕](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert1.png  "選取")

3. 選取 **新建**並填寫詳細資料。

   ![建立 Log Analytics 工作區的詳細資料](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert2.png  "選取")

4. 尋找您的 VPN 閘道**監視器** > **診斷設定**刀鋒視窗。

   ![在 診斷設定中尋找 VPN 閘道的選取項目](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert3.png  "選取")

5. 若要開啟診斷，連按兩下 閘道，然後按**開啟診斷**。

   ![選取項目開啟診斷](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert4.png  "選取")

6. 填入詳細資料，並確定**傳送至 Log Analytics**並**TunnelDiagnosticLog**已選取。 選擇您在步驟 3 中建立 Log Analytics 工作區。

   ![選取核取方塊](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert5.png  "選取")

7. 請移至虛擬網路閘道資源的概觀，並選取**警示**從**監視** 索引標籤。然後建立新的警示規則，或編輯現有的警示規則。

   ![選取項目建立新的警示規則](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "選取")

   ![點對站](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "選取")
8. 選取 Log Analytics 工作區和資源。

   ![工作區和資源的選取項目](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert7.png  "選取")

9. 選取 **自訂記錄搜尋**下方的訊號邏輯為**新增條件**。

   ![選取項目自訂記錄檔搜尋](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert8.png  "選取")

10. 在 [搜尋查詢]  文字方塊中，輸入下列查詢。 取代為適當的繁體中文的值。

     `AzureDiagnostics |
     where Category  == "TunnelDiagnosticLog" and ResourceId == toupper("<RESOURCEID OF GATEWAY>") and TimeGenerated > ago(5m) and
     remoteIP_s == "<REMOTE IP OF TUNNEL>" and status_s == "Disconnected"`

    設定的臨界值為 0，然後選取**完成**。

    ![輸入查詢，然後選取 臨界值](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert9.png  "選取")

11. 在上**建立規則**頁面上，選取**新建**之下**動作群組**一節。 填入詳細資料，然後選取**確定**。

    ![新的動作群組的詳細資料](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert10.png  "選取")

12. 在 **建立規則**頁面上，填入的詳細資料**自訂動作**，並確定正確的名稱會出現在**動作群組名稱**一節。 選取 **建立警示規則**來建立規則。

    ![選取項目建立規則](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert11.png  "選取")

## <a name="next-steps"></a>後續步驟

若要設定通道的計量警示，請參閱[設定的 VPN 閘道計量警示](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md)。
