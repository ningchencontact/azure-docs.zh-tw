---
title: 使用自訂規則和通知來擴充 Azure IoT Central |Microsoft Docs
description: 身為方案開發人員，IoT Central 應用程式設定為裝置停止傳送遙測時，請傳送電子郵件通知。 此解決方案會使用 Azure Stream Analytics 和 Azure Functions。
author: dominicbetts
ms.author: dobett
ms.date: 05/14/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 5248b9546ffe931b72123778d0d23574e5238405
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/06/2019
ms.locfileid: "66742420"
---
# <a name="extend-azure-iot-central-with-custom-rules-that-send-notifications"></a>使用自訂規則傳送通知，來擴充 Azure IoT Central

本使用方法指南所顯示的是，解決方案開發人員，如何擴充您的 IoT Central 應用程式使用自訂規則和通知。 此範例會示範用戶端裝置停止傳送遙測時，傳送通知給操作員。 解決方案會使用[Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/)來偵測裝置停止傳送遙測時的查詢。 Stream Analytics 作業使用[Azure Functions](https://docs.microsoft.com/azure/azure-functions/)傳送通知電子郵件使用[SendGrid](https://sendgrid.com/docs/for-developers/partners/microsoft-azure/)。

本使用方法指南會示範如何超越 IoT Central 它已經怎麼使用內建規則和動作。

在本使用說明指南中，您將了解如何：

* Stream IoT Central 應用程式使用的遙測*連續資料匯出*。
* 建立裝置停止傳送資料時偵測到的 Stream Analytics 查詢。
* 傳送電子郵件通知使用 Azure Functions 和 SendGrid 服務。

## <a name="prerequisites"></a>必要條件

若要完成此操作指南中的步驟，您必須具備有效的 Azure 訂用帳戶。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

### <a name="iot-central-application"></a>IoT Central 應用程式

建立 IoT Central 應用程式來自[Azure IoT Central-我的應用程式](https://aka.ms/iotcentral)頁面使用下列設定：

| 設定 | Value |
| ------- | ----- |
| 付款計劃 | Pay-As-You-Go |
| 應用程式範本 | Contoso 範例 |
| 應用程式名稱 | 接受預設值，或選擇您自己的名稱 |
| URL | 接受預設值，或選擇您自己唯一的 URL 前置詞 |
| 目錄 | Azure Active Directory 租用戶 |
| Azure 訂用帳戶 | 您的 Azure 訂用帳戶 |
| 區域 | 美國東部 |

範例和螢幕擷取畫面，在此發行項使用**美國東部**區域。 選擇靠近您的位置，並確定您在相同的區域中建立的所有資源。

### <a name="resource-group"></a>資源群組

使用[Azure 入口網站來建立資源群組](https://portal.azure.com/#create/Microsoft.ResourceGroup)稱為**DetectStoppedDevices**以包含您所建立的其他資源。 在您的 IoT Central 應用程式的相同位置中建立您的 Azure 資源。

### <a name="event-hubs-namespace"></a>事件中樞命名空間

使用[Azure 入口網站來建立事件中樞命名空間](https://portal.azure.com/#create/Microsoft.EventHub)使用下列設定：

| 設定 | 值 |
| ------- | ----- |
| Name    | 選擇您的命名空間名稱 |
| 定價層 | 基本 |
| 訂用帳戶 | 您的訂用帳戶 |
| 資源群組 | DetectStoppedDevices |
| 位置 | 美國東部 |
| 輸送量單位 | 1 |

### <a name="stream-analytics-job"></a>Stream Analytics 作業

使用[Azure 入口網站來建立 Stream Analytics 作業](https://portal.azure.com/#create/Microsoft.StreamAnalyticsJob)使用下列設定：

| 設定 | 值 |
| ------- | ----- |
| Name    | 選擇您的作業名稱 |
| 訂用帳戶 | 您的訂用帳戶 |
| 資源群組 | DetectStoppedDevices |
| 位置 | 美國東部 |
| 裝載環境 | 雲端 |
| 串流單位 | 3 |

### <a name="function-app"></a>函式應用程式

使用[Azure 入口網站建立函數應用程式](https://portal.azure.com/#create/Microsoft.FunctionApp)使用下列設定：

| 設定 | 值 |
| ------- | ----- |
| 應用程式名稱    | 選擇您的函式應用程式名稱 |
| 訂用帳戶 | 您的訂用帳戶 |
| 資源群組 | DetectStoppedDevices |
| 作業系統 | Windows |
| 主控方案 | 取用方案 |
| 位置 | 美國東部 |
| 執行階段堆疊 | .NET |
| 儲存體 | 新建 |

### <a name="sendgrid-account"></a>SendGrid 帳戶

使用[Azure 入口網站來建立 SendGrid 帳戶](https://portal.azure.com/#create/Sendgrid.sendgrid)使用下列設定：

| 設定 | 值 |
| ------- | ----- |
| Name    | 選擇您的 SendGrid 帳戶名稱 |
| 密碼 | 建立密碼 |
| 訂用帳戶 | 您的訂用帳戶 |
| 資源群組 | DetectStoppedDevices |
| 定價層 | F1 免費 |
| 連絡人資訊 | 填寫必要資訊 |

當您已建立所有必要的資源，您**DetectStoppedDevices**資源群組看起來像下列螢幕擷取畫面：

![偵測已停止的裝置的資源群組](media/howto-create-custom-rules/resource-group.png)

## <a name="create-an-event-hub"></a>建立事件中樞

您可以設定為持續將遙測匯出至事件中樞的 IoT Central 應用程式。 在本節中，您可以建立事件中樞，以從您的 IoT Central 應用程式接收遙測資料。 事件中樞提供遙測資料至您的 Stream Analytics 作業進行處理。

1. 在 Azure 入口網站中，瀏覽至您的事件中樞命名空間，然後選取 **+ 事件中樞**。
1. 命名您的事件中樞**centralexport**，然後選取**建立**。

您的事件中樞命名空間看起來像下列螢幕擷取畫面：

![事件中樞命名空間](media/howto-create-custom-rules/event-hubs-namespace.png)

## <a name="get-sendgrid-api-key"></a>取得 SendGrid API 金鑰

函式應用程式必須傳送電子郵件的 SendGrid API 金鑰。 若要建立 SendGrid API 金鑰：

1. 在 Azure 入口網站中，瀏覽至您的 SendGrid 帳戶。 然後選擇**管理**來存取您的 SendGrid 帳戶。
1. 在您的 SendGrid 帳戶，選擇**設定**，然後**API 金鑰**。 選擇**建立 API 金鑰**:

    ![建立 SendGrid API 金鑰](media/howto-create-custom-rules/sendgrid-api-keys.png)

1. 在上**建立 API 金鑰**頁面上，建立名為索引鍵**AzureFunctionAccess**具有**完整存取權**權限。
1. 請記下的 API 金鑰，您會需要它，當您設定您的函式應用程式。

## <a name="define-the-function"></a>定義函式

此解決方案會使用 Azure Functions 應用程式傳送電子郵件通知，當 Stream Analytics 作業偵測到已停止的裝置。 若要建立函式應用程式：

1. 在 Azure 入口網站中，瀏覽至**App Service**執行個體中**DetectStoppedDevices**資源群組。
1. 選取  **+** 來建立新的函式。
1. 在 **選擇的開發環境**頁面上，選擇 **-入口網站**，然後選取**繼續**。
1. 在  **CREATE FUNCTION**頁面上，選擇**Webhook + API** ，然後選取**建立**。

在入口網站建立預設函式呼叫**HttpTrigger1**:

![預設 HTTP 觸發程序函式](media/howto-create-custom-rules/default-function.png)

### <a name="configure-function-bindings"></a>設定函式繫結

若要傳送使用 SendGrid 電子郵件，您需要設定您的函式繫結，如下所示：

1. 選取 **整合**，選擇 輸出**HTTP ($return)** ，然後選取**刪除**。
1. 選擇 **+ 新輸出**，然後選擇**SendGrid**，然後選擇**選取**。 選擇**安裝**安裝 SendGrid 延伸模組。
1. 安裝完成時，選取**使用函式傳回值**。 新增有效**位址**接收電子郵件通知。  新增有效**地址**來做為電子郵件寄件者。
1. 選取 **新**旁**SendGrid API 金鑰應用程式設定**。 請輸入**SendGridAPIKey**做為金鑰，以及您先前記下做為值的 SendGrid API 金鑰。 然後選取 [建立]  。
1. 選擇**儲存**以儲存您的函式的 SendGrid 繫結。

整合設定看起來像下列螢幕擷取畫面：

![函式應用程式整合](media/howto-create-custom-rules/function-integrate.png)

### <a name="add-the-function-code"></a>新增函式程式碼

若要實作您的函式，新增C#程式碼，以剖析傳入的 HTTP 要求並傳送電子郵件，如下所示：

1. 選擇**HttpTrigger1**函式應用程式中函式，並取代C#為下列程式碼的程式碼：

    ```csharp
    #r "Newtonsoft.Json"
    #r "..\bin\SendGrid.dll"

    using System;
    using SendGrid.Helpers.Mail;
    using Microsoft.Azure.WebJobs.Host;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;

    public static SendGridMessage Run(HttpRequest req, ILogger log)
    {
        string requestBody = new StreamReader(req.Body).ReadToEnd();
        log.LogInformation(requestBody);
        var notifications = JsonConvert.DeserializeObject<IList<Notification>>(requestBody);

        SendGridMessage message = new SendGridMessage();
        message.Subject = "Contoso device notification";

        var content = "The following device(s) have stopped sending telemetry:<br/><br/><table><tr><th>Device ID</th><th>Time</th></tr>";
        foreach(var notification in notifications) {
            log.LogInformation($"No message received - Device: {notification.deviceid}, Time: {notification.time}");
            content += $"<tr><td>{notification.deviceid}</td><td>{notification.time}</td></tr>";
        }
        content += "</table>";
        message.AddContent("text/html", content);

        return message;
    }

    public class Notification
    {
        public string deviceid { get; set; }
        public string time { get; set; }
    }
    ```

    儲存新的程式碼之前，可能會看見一則錯誤訊息。

1. 選取 **儲存**儲存函式。

### <a name="test-the-function-works"></a>測試函式可運作

若要在入口網站中測試函式，請先選擇**記錄檔**底部的程式碼編輯器。 然後選擇**測試**右邊的程式碼編輯器。 使用下列做為 JSON**要求本文**:

```json
[{"deviceid":"test-device-1","time":"2019-05-02T14:23:39.527Z"},{"deviceid":"test-device-2","time":"2019-05-02T14:23:50.717Z"},{"deviceid":"test-device-3","time":"2019-05-02T14:24:28.919Z"}]
```

函式記錄檔訊息會出現在**記錄檔**面板：

![函式記錄輸出](media/howto-create-custom-rules/function-app-logs.png)

在幾分鐘的時間之後,**至**電子郵件地址會收到一封電子郵件，使用下列內容：

```txt
The following device(s) have stopped sending telemetry:

Device ID   Time
test-device-1   2019-05-02T14:23:39.527Z
test-device-2   2019-05-02T14:23:50.717Z
test-device-3   2019-05-02T14:24:28.919Z
```

## <a name="add-stream-analytics-query"></a>新增 Stream Analytics 查詢

此解決方案會使用 Stream Analytics 查詢來偵測裝置停止超過 120 秒傳送遙測時。 此查詢會使用事件中樞的遙測資料，做為輸入。 作業會將查詢結果傳送至函式應用程式。 在本節中，您可以設定 Stream Analytics 作業：

1. 在 Azure 入口網站中，瀏覽至您的 Stream analytics 作業，底下**作業拓樸**選取**輸入**，選擇 **+ 新增串流輸入**，然後選擇 **事件中樞**。
1. 使用下表中的資訊，來設定使用您先前建立的事件中樞的輸入，然後選擇**儲存**:

    | 設定 | Value |
    | ------- | ----- |
    | 輸入別名 | centraltelemetry |
    | 訂用帳戶 | 您的訂用帳戶 |
    | 事件中樞命名空間 | 您的事件中樞命名空間 |
    | 事件中樞名稱 | 使用現有- **centralexport** |

1. 底下**作業拓樸**，選取**輸出**，選擇 **+ 新增**，然後選擇**Azure 函式**。
1. 使用下表中的資訊，來設定輸出，然後選擇**儲存**:

    | 設定 | Value |
    | ------- | ----- |
    | 輸出別名 | emailnotification |
    | 訂用帳戶 | 您的訂用帳戶 |
    | 函式應用程式 | 函式應用程式 |
    | 函式  | HttpTrigger1 |

1. 底下**作業的拓樸**，選取**查詢**和現有的查詢取代為下列 SQL:

    ```sql
    with
    LeftSide as
    (
        SELECT
        -- Get the device ID from the message metadata and create a column
        GetMetadataPropertyValue([centraltelemetry], '[EventHub].[IoTConnectionDeviceId]') as deviceid1, 
        EventEnqueuedUtcTime AS time1
        FROM
        -- Use the event enqueued time for time-based operations
        [centraltelemetry] TIMESTAMP BY EventEnqueuedUtcTime
    ),
    RightSide as
    (
        SELECT
        -- Get the device ID from the message metadata and create a column
        GetMetadataPropertyValue([centraltelemetry], '[EventHub].[IoTConnectionDeviceId]') as deviceid2, 
        EventEnqueuedUtcTime AS time2
        FROM
        -- Use the event enqueued time for time-based operations
        [centraltelemetry] TIMESTAMP BY EventEnqueuedUtcTime
    )

    SELECT
        LeftSide.deviceid1 as deviceid,
        LeftSide.time1 as time
    INTO
        [emailnotification]
    FROM
        LeftSide
        LEFT OUTER JOIN
        RightSide 
        ON
        LeftSide.deviceid1=RightSide.deviceid2 AND DATEDIFF(second,LeftSide,RightSide) BETWEEN 1 AND 120
        where
        -- Find records where a device didn't send a message 120 seconds
        RightSide.deviceid2 is NULL
    ```

1. 選取 [ **儲存**]。
1. 若要啟動 Stream Analytics 作業，請選擇**概觀**，然後**開始**，然後**現在**，然後**啟動**:

    ![串流分析](media/howto-create-custom-rules/stream-analytics.png)

## <a name="configure-export-in-iot-central"></a>在 IoT 中心中設定匯出

瀏覽至[IoT Central 應用程式](https://aka.ms/iotcentral)您範本所建立的 Contoso。 在本節中，您可以設定應用程式串流至事件中樞的遙測從模擬的裝置。 若要匯出的設定：

1. 瀏覽至**連續資料匯出**頁面上，選取 **+ 新增**，然後**Azure 事件中樞**。
1. 使用下列設定來設定匯出，然後選取**儲存**:

    | 設定 | Value |
    | ------- | ----- |
    | 顯示名稱 | 匯出至事件中樞 |
    | Enabled | 另一 |
    | 事件中樞命名空間 | 事件中樞命名空間名稱 |
    | 事件中樞 | centralexport |
    | 度量 | 另一 |
    | 裝置 | 關閉 |
    | 裝置範本 | 關閉 |

![連續資料匯出組態](media/howto-create-custom-rules/cde-configuration.png)

等到匯出狀態變成**執行**再繼續。

## <a name="test"></a>測試

若要測試方案，您可以停用 IoT 中心從連續資料匯出至已停止的模擬裝置：

1. 在您的 IoT Central 應用程式，瀏覽至**連續資料匯出**頁面，然後選取**匯出至事件中樞**匯出組態。
1. 設定**Enabled**要**Off** ，然後選擇 **儲存**。
1. 在至少兩分鐘後**至**電子郵件地址會收到看起來像下列的範例內容的一或多個電子郵件：

    ```txt
    The following device(s) have stopped sending telemetry:

    Device ID   Time
    7b169aee-c843-4d41-9f25-7a02671ee659    2019-05-09T14:28:59.954Z
    ```

## <a name="tidy-up"></a>整理一下

不妨整理一下之後此操作說明,，並避免不必要的成本，刪除**DetectStoppedDevices**在 Azure 入口網站中的資源群組。

您可以刪除 IoT Central 應用程式，從**管理**應用程式內的頁面。

## <a name="next-steps"></a>後續步驟

在此操作指南中，您已了解如何：

* Stream IoT Central 應用程式使用的遙測*連續資料匯出*。
* 建立裝置停止傳送資料時偵測到的 Stream Analytics 查詢。
* 傳送電子郵件通知使用 Azure Functions 和 SendGrid 服務。

既然您已經知道如何建立自訂規則和通知時，建議的下一個步驟是了解如何[擴充 Azure IoT Central 自訂的分析與](howto-create-custom-analytics.md)。
