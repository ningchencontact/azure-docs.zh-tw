---
title: 如何設定 Azure VPN 閘道診斷記錄事件的警示
description: 若要設定 VPN 閘道診斷記錄事件的警示的步驟
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptional
ms.date: 04/22/2019
ms.author: alzam
ms.openlocfilehash: 39a6d2e6201dd0635149159cb3727fd3c40f710a
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2019
ms.locfileid: "63769732"
---
# <a name="setting-up-alerts-on-vpn-gateway-diagnostic-log-events"></a>設定 VPN 閘道診斷記錄事件的警示

這篇文章可協助您設定 VPN 閘道診斷記錄事件為基礎的警示。


## <a name="setup"></a>安裝程式以使用入口網站的診斷記錄事件為基礎的 Azure 監視器警示

下列範例步驟會建立站對站 VPN 通道中斷連線事件的警示



1. 搜尋所有服務] 下的 [Log Analytics]，並挑選 「 Log Analytics 工作區 」![點對站](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert0.png "建立")

2. 在 [Log Analytics] 頁面中，按一下 [建立]。
![點對站](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert1.png  "選取")

3. 檢查 [新建] 工作區，並填寫詳細資料。
![點對站](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert2.png  "選取")

4. 尋找您的 VPN 閘道，在 [監視] > [診斷設定] 刀鋒視窗![點對站](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert3.png  "選取")

5. 若要開啟診斷，在閘道上按兩下，，然後按一下 [開啟診斷]![點對站](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert4.png  "選取")

6. 填入詳細資料，並請確定已選取 「 傳送到 Log Analytics 」 和 「 TunnelDiagnosticLog"。 選取在步驟 3 中建立 Log Analytics 工作區。
![點對站](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert5.png  "選取")

7. 瀏覽至虛擬網路閘道資源概觀並從 [監視] 索引標籤中，選取 [警示]，然後建立新的警示規則或編輯現有的警示規則。
![點對站](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "選取")

8. 選取 Log Analytics 工作區和資源。
![點對站](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert7.png  "選取")

9. 選取 [自訂記錄搜尋] 的訊號邏輯以在 [新增條件]![點對站](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert8.png  "選取")

10. 在 [搜尋查詢] 文字方塊中，並將值替換為適當的繁體中文輸入下列查詢。

    AzureDiagnostics |其中類別目錄 = ="TunnelDiagnosticLog"且 ResourceId = = toupper ("<RESOURCEID OF GATEWAY>")] 與 [TimeGenerated > ago(5m) 和 remoteIP_s = ="<REMOTE IP OF TUNNEL>」 和 status_s = ="已中斷連線 」

    設定的臨界值為 0，按一下 [完成]

    ![點對站](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert9.png  "選取")

11. 在 [建立規則] 頁面上，按一下 [新建] 區段下的動作群組。 填入詳細資料，然後按一下 [確定]

![點對站](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert10.png  "選取")

12. 在 [建立規則] 頁面上，填入 [自訂動作] 的詳細資料，並請確定正確的動作群組名稱出現 「 動作群組名稱 」 一節。 按一下 [建立警示規則] 來建立規則。
![點對站](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert11.png  "選取")

## <a name="next-steps"></a>後續步驟

若要設定通道的計量警示，請參閱[如何設定計量警示的 VPN 閘道](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md)。
