---
title: 搭配 Azure Logic Apps 進行 IoT 遠端監視和通知 | Microsoft Docs
description: 使用 Azure Logic Apps 進行 IoT 中樞的 IoT 溫度監視，並對於偵測到的任何異常自動將電子郵件通知寄送到您的信箱。
author: robinsh
keywords: iot 監視、iot 通知、iot 溫度監視
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/19/2019
ms.author: robinsh
ms.openlocfilehash: 26637468f44e12f7ad66f907e0f6be3d907e578f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "64719318"
---
# <a name="iot-remote-monitoring-and-notifications-with-azure-logic-apps-connecting-your-iot-hub-and-mailbox"></a>搭配連接 IoT 中樞和信箱的 Azure Logic Apps 進行 IoT 遠端監視和通知

![端對端圖表](media/iot-hub-monitoring-notifications-with-azure-logic-apps/iot-hub-e2e-logic-apps.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/)可協助您協調工作流程，跨內部部署和雲端服務，一個或多個企業中，並跨各種通訊協定。 邏輯應用程式會開始觸發程序，然後再接著可以使用條件和迭代器等的內建控制項進行排序的一或多個動作。 這種彈性可讓 Logic Apps 是理想的 IoT 解決方案，IoT 監視案例。 比方說，到達的遙測資料的裝置會在 IoT 中樞端點可以起始邏輯應用程式工作流程，倉儲的 Azure 儲存體 blob 中的資料，請傳送電子郵件警示，警告的資料異常、 排程技術人員，如果裝置會回報失敗依此類推。

## <a name="what-you-learn"></a>您學到什麼

您學會如何建立連接 IoT 中樞和信箱的邏輯應用程式用於溫度監視和通知。

在您的裝置上執行的用戶端程式碼會設定應用程式屬性，`temperatureAlert`上每個遙測訊息，則傳送至 IoT 中樞。 當用戶端程式碼偵測溫度超過 30°c 時，它會設定這個屬性`true`; 否則它會將屬性設定為`false`。

在本主題中，您將設定您的 IoT 中樞上的路由來傳送訊息所在`temperatureAlert = true`至服務匯流排端點，以及您設定訊息抵達服務匯流排端點上觸發，並傳送電子郵件通知給您的邏輯應用程式。

## <a name="what-you-do"></a>您要做什麼

* 建立服務匯流排命名空間，並將它的服務匯流排佇列。
* 將自訂端點和路由規則新增至 IoT 中樞訊息包含服務匯流排佇列的溫度警示的路由。
* 建立、 設定及測試邏輯應用程式取用來自服務匯流排佇列的訊息，並將通知電子郵件傳送至所需的收件者。

## <a name="what-you-need"></a>您需要什麼

* 完成[Raspberry Pi 線上模擬器](iot-hub-raspberry-pi-web-simulator-get-started.md)教學課程中，或其中一個裝置教學課程中，例如[搭配 node.js 的 Raspberry Pi](iot-hub-raspberry-pi-kit-node-get-started.md)。 這些會涵蓋下列需求：

  * 有效的 Azure 訂用帳戶。
  * 位於您訂用帳戶中的 Azure IoT 中樞。
  * 在您將遙測訊息傳送至 Azure IoT 中樞的裝置上執行用戶端應用程式。

## <a name="create-service-bus-namespace-and-queue"></a>建立服務匯流排命名空間和佇列

建立服務匯流排命名空間與佇列。 稍後在本主題中中,，您可以建立路由規則中 IoT 中樞，以便直接包含服務匯流排佇列，其中會收取的邏輯應用程式和以傳送通知電子郵件觸發溫度警示的訊息。

### <a name="create-a-service-bus-namespace"></a>建立服務匯流排命名空間

1. 在  [Azure 入口網站](https://portal.azure.com/)，選取 **+ 建立資源** > **整合** > **服務匯流排**。

1. 在 [**建立命名空間**] 窗格中，提供下列資訊：

   **名稱**：服務匯流排命名空間名稱。 命名空間必須是唯一的 Azure。

   **定價層**：選取 **基本**從下拉式清單。 「基本」層足供本教學課程之用。

   **资源组**：使用 IoT 中樞所用的相同資源群組。

   **位置**：使用 IoT 中樞所用的相同位置。

   ![在 Azure 入口網站中建立服務匯流排命名空間](media/iot-hub-monitoring-notifications-with-azure-logic-apps/1-create-service-bus-namespace-azure-portal.png)

1. 選取 [建立]  。 等待部署完成再繼續進行下一個步驟。

### <a name="add-a-service-bus-queue-to-the-namespace"></a>命名空間中新增服務匯流排佇列

1. 開啟 服務匯流排命名空間。 移至服務匯流排命名空間的最簡單方式是選取**資源群組**資源窗格中，選取您的資源群組，然後從資源清單中選取服務匯流排命名空間。

1. 在 **服務匯流排命名空間**窗格中，選取 **+ 佇列**。

1. 輸入佇列的名稱，然後選取**建立**。 已成功建立佇列，當**Create queue**窗格便會關閉。

   ![在 Azure 入口網站中新增服務匯流排佇列](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-queue.png)

1. 回到**服務匯流排命名空間**窗格下方**實體**，選取**佇列**。 從清單中，開啟 服務匯流排佇列，然後選取**共用存取原則** >  **+ 新增**。

1. 輸入原則名稱，檢查**管理**，然後選取**建立**。

   ![在 Azure 入口網站中新增服務匯流排佇列原則](media/iot-hub-monitoring-notifications-with-azure-logic-apps/2-add-service-bus-queue-azure-portal.png)

## <a name="add-a-custom-endpoint-and-routing-rule-to-your-iot-hub"></a>將自訂端點和路由規則新增至您的 IoT 中樞

將服務匯流排佇列的自訂端點新增至您的 IoT 中樞，並建立訊息路由規則，以直接包含該端點，溫度警示的訊息，它們會挑選對應的邏輯應用程式。 路由規則會使用路由查詢時， `temperatureAlert = "true"`，以根據值的訊息轉送`temperatureAlert`裝置上執行的用戶端程式碼所設定的應用程式屬性。 若要進一步了解，請參閱[訊息根據訊息屬性的路由查詢](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#message-routing-query-based-on-message-properties)。

### <a name="add-a-custom-endpoint"></a>新增自訂端點

1. 開啟 IoT 中樞。 取得 IoT 中樞的最簡單方式是選取**資源群組**資源窗格中，選取您的資源群組，然後從資源清單中選取您的 IoT 中樞。

1. 底下**Messaging**，選取**訊息路由**。 在 **訊息路由**窗格中，選取**自訂端點**索引標籤，然後選取 **+ 新增**。 從下拉式清單中，選取**服務匯流排佇列**。

   ![將端點新增至 Azure 入口網站的 IoT 中樞](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-iot-hub-custom-endpoint.png)

1. 在 [**新增服務匯流排端點**] 窗格中，輸入下列資訊：

   **端點名稱**:端點的名稱。

   **服務匯流排命名空間**：選取您所建立的命名空間。

   **服務匯流排佇列**:選取您建立的佇列。

   ![將端點新增至 Azure 入口網站的 IoT 中樞](media/iot-hub-monitoring-notifications-with-azure-logic-apps/3-add-iot-hub-endpoint-azure-portal.png)

1. 選取 [建立]  。 已成功建立端點之後，請繼續下一個步驟。

### <a name="add-a-routing-rule"></a>新增路由規則

1. 回到**訊息路由**窗格中，選取**路由**索引標籤，然後選取 **+ 新增**。

1. 在 [**新增路由**] 窗格中，輸入下列資訊：

   **名稱**：路由規則的名稱。

   **端點**：選取您建立的端點。

   **資料來源**：選取 **裝置的遙測訊息**。

   **路由查詢**：輸入 `temperatureAlert = "true"` 。

   ![在 Azure 入口網站中新增路由規格](media/iot-hub-monitoring-notifications-with-azure-logic-apps/4-add-routing-rule-azure-portal.png)

1. 選取 [ **儲存**]。 您可以關閉**訊息路由**窗格。

## <a name="create-and-configure-a-logic-app"></a>建立和設定邏輯應用程式

在上一節中，您設定您的 IoT 中樞將訊息路由包含至您的服務匯流排佇列的溫度警示。 現在，您設定邏輯應用程式來監視服務匯流排佇列，並傳送電子郵件通知，每當訊息新增至佇列。

### <a name="create-a-logic-app"></a>建立邏輯應用程式

1. 選取 **建立資源** > **整合** > **邏輯應用程式**。

1. 輸入以下資訊：

   **名稱**：邏輯應用程式的名稱。

   **资源组**：使用 IoT 中樞所用的相同資源群組。

   **位置**：使用 IoT 中樞所用的相同位置。

   ![在 Azure 入口網站中建立邏輯應用程式](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-a-logic-app.png)

1. 選取 [建立]  。

### <a name="configure-the-logic-app-trigger"></a>設定邏輯應用程式觸發程序

1. 開啟邏輯應用程式。 若要取得邏輯應用程式的最簡單方式是選取**資源群組**資源窗格中，選取您的資源群組，然後從資源清單中選取您的邏輯應用程式。 當您選取的邏輯應用程式時，Logic Apps 設計工具隨即開啟。

1. 在 Logic Apps 設計工具中，向下捲動至**範本**，然後選取**空白邏輯應用程式**。

   ![在 Azure 入口網站中，從空白的邏輯應用程式開始。](media/iot-hub-monitoring-notifications-with-azure-logic-apps/5-start-with-blank-logic-app-azure-portal.png)

1. 選取 **所有**索引標籤，然後選取**Service Bus**。

   ![選取服務匯流排，開始在 Azure 入口網站中建立邏輯應用程式](media/iot-hub-monitoring-notifications-with-azure-logic-apps/6-select-service-bus-when-creating-blank-logic-app-azure-portal.png)

1. 底下**觸發程序**，選取 **（自動完成） 佇列中的一個或多個訊息送達時**。

   ![在 Azure 入口網站中選取您的邏輯應用程式的觸發程序](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-service-bus-trigger.png)

1. 建立服務匯流排連接。
   1. 輸入連線名稱，然後從清單中選取您的服務匯流排命名空間。 下一個畫面隨即開啟。

      ![在 Azure 入口網站中建立邏輯應用程式的服務匯流排連接](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-connection-1.png)

   1. 選取服務匯流排原則 (RootManageSharedAccessKey)。 然後選取**建立**。

      ![在 Azure 入口網站中建立邏輯應用程式的服務匯流排連接](media/iot-hub-monitoring-notifications-with-azure-logic-apps/7-create-service-bus-connection-in-logic-app-azure-portal.png)

   1. 最後一個畫面中，如**佇列名稱**，選取您從下拉式清單建立的佇列。 請輸入`175`for**最大訊息計數**。

      ![對於邏輯應用程式中的服務匯流排連接指定最大訊息計數](media/iot-hub-monitoring-notifications-with-azure-logic-apps/8-specify-maximum-message-count-for-service-bus-connection-logic-app-azure-portal.png)

   1. 選取 [**儲存**] 功能表頂端的 Logic Apps 設計工具，以儲存變更。

### <a name="configure-the-logic-app-action"></a>設定邏輯應用程式動作

1. 建立 SMTP 服務連接。

   1. 選取 [新增步驟]  。 在 [**選擇動作**，選取**所有**] 索引標籤。

   1. 型別`smtp`在 [搜尋] 方塊中，選取**SMTP**在搜尋結果中，服務，然後選取**傳送電子郵件**。

      ![在 Azure 入口網站的邏輯應用程式中建立 SMTP 連接](media/iot-hub-monitoring-notifications-with-azure-logic-apps/9-create-smtp-connection-logic-app-azure-portal.png)

   1. 輸入您的信箱的 SMTP 資訊，然後選取**建立**。

      ![在 Azure 入口網站的邏輯應用程式中輸入 SMTP 連接資訊](media/iot-hub-monitoring-notifications-with-azure-logic-apps/10-enter-smtp-connection-info-logic-app-azure-portal.png)

      取得 [Hotmail/Outlook.com](https://support.office.com/article/Add-your-Outlook-com-account-to-another-mail-app-73f3b178-0009-41ae-aab1-87b80fa94970)、[Gmail](https://support.google.com/a/answer/176600?hl=en) 和 [Yahoo Mail](https://help.yahoo.com/kb/SLN4075.html) 的 SMTP 資訊。

      > [!NOTE]
      > 您可能需要停用 SSL，以建立連線。 如果此情況下，您想要在建立連線之後，重新啟用 SSL，請參閱本節結尾處的選擇性步驟。

   1. 從**加入新的參數**下拉式清單**傳送電子郵件**步驟中，選取**從**，**至**，**主旨**並**主體**。 按一下或點選畫面關閉選取方塊上的任何位置。

      ![選擇 SMTP 連線電子郵件欄位](media/iot-hub-monitoring-notifications-with-azure-logic-apps/smtp-connection-choose-fields.png)

   1. 輸入**寄件者**和**收件者**的電子郵件地址，並對於**主旨**和**內文**輸入 `High temperature detected`。 如果**之應用程式及使用此流程中的連接器新增動態內容** 對話方塊隨即開啟，請選取**隱藏**來關閉它。 您不在本教學課程使用動態內容。

      ![填入的 SMTP 連線電子郵件欄位](media/iot-hub-monitoring-notifications-with-azure-logic-apps/fill-in-smtp-connection-fields.png)

   1. 選取 **儲存**儲存 SMTP 連線。

1. （選擇性）如果您已停用 SSL 來與您的電子郵件提供者建立連接，並且想要重新啟用它，請遵循下列步驟：

   1. 在上**邏輯應用程式**窗格下方**開發工具**，選取**API 連線**。

   1. 從 API 連線清單中，選取 SMTP 連接。

   1. 在上**smtp API 連線**窗格下方**一般**，選取**編輯 API 連線**。

   1. 在 **編輯 API 連線**窗格中，選取**啟用 SSL？** ，重新輸入您的電子郵件帳戶的密碼，然後選取**儲存**。

      ![編輯邏輯應用程式在 Azure 入口網站中的 SMTP API 連線](media/iot-hub-monitoring-notifications-with-azure-logic-apps/re-enable-smtp-connection-ssl.png)

邏輯應用程式現在就來處理從服務匯流排佇列的溫度警示，並將通知傳送至您的電子郵件帳戶。

## <a name="test-the-logic-app"></a>測試邏輯應用程式

1. 啟動您的裝置上的用戶端應用程式。

1. 如果您使用實體裝置，仔細顯示熱度圖感應器附近的熱度圖來源直到溫度超過 30°c 度如果您使用線上模擬器，用戶端程式碼會隨機輸出超過 30 C.的遙測訊息

1. 您應該開始接收邏輯應用程式所傳送的電子郵件通知。

   > [!NOTE]
   > 您的電子郵件服務提供者可能需要驗證寄件者身分，確定是您傳送電子郵件。

## <a name="next-steps"></a>後續步驟

您已成功建立連接 IoT 中樞的邏輯應用程式，以及溫度監視和通知的信箱。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
