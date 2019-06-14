---
title: 使用 Azure IoT 中樞診斷連線中斷並進行疑難排解
description: 了解如何透過 Azure IoT 中樞的裝置連線能力，診斷常見錯誤並進行疑難排解
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/19/2018
ms.author: jlian
ms.openlocfilehash: a107689796c58b17c445e7a9cf7c6f0402ef6005
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "61440115"
---
# <a name="detect-and-troubleshoot-disconnects-with-azure-iot-hub"></a>使用 Azure IoT 中樞偵測連線中斷並進行疑難排解

IoT 裝置的連線問題可能因為有許多可能的失敗點而難以排解。 裝置端應用程式邏輯、實體網路、通訊協定、硬體和 Azure IoT 中樞全都可能造成問題。 本文提供有關如何偵測從雲端 (相對於裝置端) 連線裝置的問題建議並進行疑難排解。

## <a name="get-alerts-and-error-logs"></a>取得警示和錯誤記錄

使用 Azure 監視器，在裝置連線卸除時取得警示並寫入記錄。

### <a name="turn-on-diagnostic-logs"></a>開啟診斷記錄

若要記錄裝置連線事件和錯誤，請開啟 IoT 中樞的診斷功能。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 瀏覽至您的 IoT 中樞。

3. 選取 [診斷設定]  。

4. 選取 [開啟診斷]  。

5. 讓 [連線]  記錄可供收集。

6. 若要讓分析變得更容易，您應該開啟 [傳送至 Log Analytics]  ([請參閱定價](https://azure.microsoft.com/pricing/details/log-analytics/))。 請參閱[解決連線錯誤](#resolve-connectivity-errors)下方的範例。

   ![建議的設定](./media/iot-hub-troubleshoot-connectivity/diagnostic-settings-recommendation.png)

若要深入了解，請參閱[監視 Azure IoT 中樞的健康情況並快速診斷問題](iot-hub-monitor-resource-health.md)。

### <a name="set-up-alerts-for-the-connected-devices-count-metric"></a>為_已連線的裝置_計數計量設定警示

若要取得警示，當裝置中斷連線時，設定的警示**連接的裝置 （預覽）** 計量。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 瀏覽至您的 IoT 中樞。

3. 選取 [警示]  。

4. 選取 **新的警示規則**。

5. 選取 **新增條件**，然後選取 「 已連線的裝置 （預覽） 」。

6. 完成設定您想要的臨界值，並由下列提示的警示選項。

若要深入了解，請參閱[什麼是 Microsoft Azure 中的傳統警示？](../azure-monitor/platform/alerts-overview.md)。

## <a name="resolve-connectivity-errors"></a>解決連線錯誤

開啟已連線裝置的診斷記錄和警示後，您會在發生錯誤時收到警示。 這一節說明如何在收到警示時解決常見問題。 以下步骤假设已经在 Azure Monitor 日志中设置了诊断日志。

1. 在 Azure 入口網站中，前往 **Log Analytics** 的工作區。

2. 選取 [記錄搜尋]  。

3. 若要隔離 IoT 中樞的連線錯誤記錄，請輸入下列查詢，然後選取 [執行]  ：

    ```
    search *
    | where ( Type == "AzureDiagnostics" and ResourceType == "IOTHUBS")
    | where ( Category == "Connections" and Level == "Error")
    ```

1. 如果有結果，請尋找 `OperationName`、`ResultType` (錯誤碼) 和 `ResultDescription` (錯誤訊息)，以取得關於錯誤的詳細資訊。

   ![錯誤記錄的範例](./media/iot-hub-troubleshoot-connectivity/diag-logs.png)

2. 使用此表格來了解和解決常見錯誤。

    | Error | 根本原因 | 解決方案 |
    |-------|------------|------------|
    | 404104 DeviceConnectionClosedRemotely | 裝置已關閉連線，但 IoT 中樞不知道原因為何。 常見原因包括 MQTT/AMQP 逾時和網際網路連線中斷。 | 藉由[測試連線](tutorial-connectivity.md)，確定裝置可以連線到 IoT 中樞。 如果連線正常，但裝置連線會間歇性中斷，請務必針對您所選的通訊協定 (MQTT/AMPQ)，實作適當的保持運作裝置邏輯。 |
    | 401003 IoTHubUnauthorized | IoT 中樞無法驗證連線。 | 請確定 SAS 或您使用的其他安全性權杖並未過期。 不需特殊設定，[Azure IoT SDK](iot-hub-devguide-sdks.md) 就會自動產生權杖。 |
    | 409002 LinkCreationConflict | 裝置有多個連線。 裝置出現新的連線要求時，IoT 中樞會關閉有此錯誤的前一個連線。 | 最常見的情況是，裝置偵測到中斷連線並嘗試重新建立連線，但 IoT 中樞仍將其視為連線的裝置。 IoT 中樞會關閉先前的連線，並記錄此錯誤。 此錯誤通常會顯現為不同暫時性問題的副作用，所以請尋找記錄中的其他錯誤，進一步進行疑難排解。 否則，請務必在連線中斷後才發出新的連線要求。 |
    | 500001 ServerError | IoT 中樞發生伺服器端問題。 最有可能是暫時性問題。 在 IoT 中樞小組努力維護 [SLA](https://azure.microsoft.com/support/legal/sla/iot-hub/) 的同時，小部分的 IoT 中樞節點可能會偶爾遇到暫時性錯誤。 當您的裝置嘗試連線到有問題的節點時，您會收到這個錯誤。 | 若要解決暫時性失敗，請從裝置發出重試。 若要[自動管理重試](iot-hub-reliability-features-in-sdks.md#connection-and-retry)，請確定您使用最新版的 [Azure IoT SDK](iot-hub-devguide-sdks.md)。<br><br>如需暫時性錯誤處理和重試的最佳做法，請參閱[暫時性錯誤處理](/azure/architecture/best-practices/transient-faults)。  <br><br>如果在重試後仍有問題，請檢查[資源健康情況](iot-hub-monitor-resource-health.md#use-azure-resource-health)和 [Azure 狀態](https://azure.microsoft.com/status/history/)，了解 IoT 中樞是否有已知的問題。 如果沒有任何已知問題且問題持續發生，請[連絡支援人員](https://azure.microsoft.com/support/options/)以便進一步調查。 |
    | 500008 GenericTimeout | IoT 中樞無法在逾時前完成連線要求。如同 500001 ServerError，此錯誤可能是暫時性錯誤。 | 請遵循 500001 ServerError 的疑難排解步驟，以找出根本原因並解決此錯誤。|

## <a name="other-steps-to-try"></a>其他要嘗試的步驟

如果上述步驟無法解決問題，您可以嘗試：

* 如果您可以存取有問題的裝置 (實體或遠端 (如 SSH))，請遵循[裝置端疑難排解指南](https://github.com/Azure/azure-iot-sdk-node/wiki/Troubleshooting-Guide-Devices)，繼續進行疑難排解。

* 在 Azure 入口網站 > 您的 IoT 中樞 > IoT 裝置，確認您的裝置 [已啟用]  。

* 向 [Azure IoT 中樞論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=azureiothub)、[Stack Overflow](https://stackoverflow.com/questions/tagged/azure-iot-hub) 或[Azure 支援](https://azure.microsoft.com/support/options/)取得協助。

為了協助每個人改善文件，如果此指南對您沒有幫助，請在下方的意見反應區段中留下意見。

## <a name="next-steps"></a>後續步驟

* 若要深入了解如何解決暫時性問題，請參閱[暫時性錯誤處理](/azure/architecture/best-practices/transient-faults)。

* 若要深入了解 Azure IoT SDK 以及如何管理重試，請參閱[如何使用 Azure IoT 中樞裝置 SDK 來管理連線能力和可靠傳訊](iot-hub-reliability-features-in-sdks.md#connection-and-retry)。
