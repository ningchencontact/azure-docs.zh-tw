---
title: 連線處理站解決方案功能 - Azure | Microsoft Docs
description: 概述連線處理站的預先設定解決方案功能。
services: iot-suite
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: ''
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/20/2018
ms.author: dobett
ms.openlocfilehash: fea9ccc53bd019039cf1e989d72db7a218e4517c
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2018
---
# <a name="what-is-azure-iot-suite-connected-factory"></a>什麼是 Azure IoT 套件的連線處理站？

連線處理站是 Microsoft 的 Azure 產業 IoT 參考架構實作，封裝為開放原始碼解決方案。 您可以用它來當作商業產品的起點。 您可以將連線處理站解決方案的預先建立版本，部署到 [Azure IoT 套件](https://www.azureiotsuite.com/#solutions/types/CF)中的 Azure 訂用帳戶。

![連線處理站解決方案的概觀](media/iot-suite-connected-factory-features/dashboard.png)

連線處理站包括下列功能：

## <a name="industrial-device-interoperability"></a>產業裝置互通性

- 使用 OPC UA 介面連線到產業資產。
- 使用模擬的生產線 (在 Docker 容器中執行 OPC UA 伺服器) 並從中查看即時遙測。
- 從雲端儀表板瀏覽 OPC UA 伺服器的 OPC UA 資訊模型。

## <a name="remote-management"></a>遠端管理

- 從雲端儀表板設定您的 OPC UA 資產 (呼叫方法、讀取和寫入資料)。
- 從雲端儀表板發行和取消發行 OPC UA 資產中的遙測資料。

## <a name="cloud-dashboard"></a>雲端儀表板

- 直接在雲端儀表板中檢視遙測預覽。
- 使用時間序列深入解析總管儀表板，來檢視遙測資料的趨勢，並建立相互關聯。
- 從雲端儀表板查看計算的設備綜合效率 (OEE) 和關鍵效能指標 (KPI)。
- 從樹狀拓撲和互動式地圖上檢視產業資產階層。
- 從雲端儀表板檢視、接收及關閉警示。

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

- [Azure 時間序列深入解析](../time-series-insights/time-series-insights-overview.md)是針對儲存、視覺化及查詢大量時間序列資料而建置。 連線處理站會利用這項服務。
- 整合此服務的連線處理站可讓您對裝置資料執行深入且即時的分析。

## <a name="rules-and-alerts"></a>規則和警示

[設定警示的閾值規則](iot-suite-connected-factory-configure.md)。

## <a name="end-to-end-security"></a>端對端安全性

- 使用角色型存取控制 (RBAC) 設定使用者的安全性權限。
- 端對端加密是使用 OPC UA 驗證 (使用 X.509 憑證) 及安全性權杖來加以實作。

## <a name="customizability"></a>自訂能力

- [自訂](iot-suite-v1-guidance-on-customizing-preconfigured-solutions.md)解決方案以滿足特定的業務需求。
- 完整的解決方案原始程式碼可在 GitHub 上取得。 請參閱[連線處理站的預先設定解決方案](https://github.com/Azure/azure-iot-connected-factory)存放庫。

## <a name="next-steps"></a>後續步驟

閱讀下列文章，深入了解連線工廠預先設定的解決方案︰

* [連線處理站的預先設定解決方案逐步解說](iot-suite-connected-factory-sample-walkthrough.md)
* [為連線處理站部署閘道]( iot-suite-connected-factory-gateway-deployment.md)
