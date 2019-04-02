---
title: 傳送至 Azure 資訊安全中心的安全性訊息，IoT preview |Microsoft Docs
description: 了解如何將傳送您 iot 使用 Azure 資訊安全中心的安全性訊息。
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: c611bb5c-b503-487f-bef4-25d8a243803d
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: be17c5bb4d09e0868af0c6fd9b31f7653b614735
ms.sourcegitcommit: 09bb15a76ceaad58517c8fa3b53e1d8fec5f3db7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2019
ms.locfileid: "58762764"
---
# <a name="send-security-messages-sdk"></a>傳送安全訊息 SDK

> [!IMPORTANT]
> IoT 的 azure 資訊安全中心目前處於公開預覽狀態。
> 此預覽版的服務等級協定，不提供，且不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本作法指南說明的 IoT 服務功能的 Azure 資訊安全中心 (ASC)，當您選擇要收集並傳送您的裝置安全性訊息，而不需使用 ASC IoT 代理程式，並說明如何執行這項操作。  

在本指南中，您將了解如何： 
> [!div class="checklist"]
> * 使用的傳送安全訊息 APIC#
> * 使用適用於 C 的傳送安全性訊息 API

## <a name="asc-for-iot-capabilities"></a>針對 IoT 功能 ASC

處理和分析任何種類的安全性訊息資料，只要傳送的資料符合 iot ASC [IoT 結構描述的 ASC](https://aka.ms/iot-security-schemas)和訊息已設定為安全性訊息。

## <a name="security-message"></a>安全性訊息

Iot 的 ASC 定義安全性訊息，使用下列準則：
- 如果訊息已傳送 Azure IoT C 與 /C# SDK
- 如果訊息符合[安全性訊息結構描述](https://aka.ms/iot-security-schemas)
- 如果訊息已設定為在傳送前的安全性訊息

每個安全性訊息包含寄件者的中繼資料，例如`AgentId`， `AgentVersion`，`MessageSchemaVersion`和安全性事件的清單。
結構描述定義的有效和必要屬性包括的事件類型的安全性訊息。

[!NOTE]
> 傳送訊息，不符合結構描述會被忽略。 請務必確認結構描述初始化傳送資料，因為已忽略的訊息不目前儲存之前。 
> 未設定為使用 Azure IoT C 安全性訊息傳送的訊息 /C# SDK 將不會路由至 IoT 管線 ASC

## <a name="valid-message-example"></a>有效的訊息範例

下列範例會顯示有效的安全性訊息物件。 此範例包含的訊息中繼資料，另一個`ProcessCreate`安全性事件。

一次設定為安全性訊息並傳送此訊息會被處理 asc iot。

```json
"AgentVersion": "0.0.1",
"AgentId": "e89dc5f5-feac-4c3e-87e2-93c16f010c25",
"MessageSchemaVersion": "1.0",
"Events": [
    {
        "EventType": "Security",
        "Category": "Triggered",
        "Name": "ProcessCreate",
        "IsEmpty": false,
        "PayloadSchemaVersion": "1.0",
        "Id": "21a2db0b-44fe-42e9-9cff-bbb2d8fdf874",
        "TimestampLocal": "2019-01-27 15:48:52Z",
        "TimestampUTC": "2019-01-27 13:48:52Z",
        "Payload":
            [
                {
                    "Executable": "/usr/bin/echo",
                    "ProcessId": 11750,
                    "ParentProcessId": 1593,
                    "UserName": "nginx",
                    "CommandLine": "./backup .htaccess"
                }
            ]
    }
]
```

## <a name="send-security-messages"></a>傳送安全訊息 

傳送安全訊息而不使用 ASC IoT 代理程式，使用[Azure IoTC#裝置 SDK](https://github.com/Azure/azure-iot-sdk-csharp/tree/preview)或是[Azure IoT C 裝置 SDK](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview)。

若要從 ASC 處理 iot 裝置傳送裝置資料，使用其中一個下列的 Api 來標記正確路由至 ASC IoT 處理管線的訊息。 將處理訊息傳送這種方式，並將其顯示在 ASC 的安全性深入解析為 IoT 在這兩個 IoT 中樞或 Azure 資訊安全中心內。 

，即使傳送正確的標頭，以標記的所有資料也必須都遵守[IoT 訊息結構描述的 ASC](https://aka.ms/iot-security-schemas)。 

### <a name="send-security-message-api"></a>傳送安全訊息 API

**傳送安全訊息**API 是目前可供 C 和C#。  

#### <a name="c-api"></a>C# API

```cs

private static async Task SendSecurityMessageAsync(string messageContent)
{
    ModuleClient client = ModuleClient.CreateFromConnectionString("<connection_string>");
    Message  securityMessage = new Message(Encoding.UTF8.GetBytes(messageContent));
    securityMessage.SetAsSecurityMessage();
    await client.SendEventAsync(securityMessage);
}
```

#### <a name="c-api"></a>C API

```c
bool SendMessageAsync(IoTHubAdapter* iotHubAdapter, const void* data, size_t dataSize) {
 
    bool success = true;
    IOTHUB_MESSAGE_HANDLE messageHandle = NULL;
 
    messageHandle = IoTHubMessage_CreateFromByteArray(data, dataSize);
 
    if (messageHandle == NULL) {
        success = false;
        goto cleanup;
    }
 
    if (IoTHubMessage_SetAsSecurityMessage(messageHandle) != IOTHUB_MESSAGE_OK) {
        success = false;
        goto cleanup;
    }
 
    if (IoTHubModuleClient_SendEventAsync(iotHubAdapter->moduleHandle, messageHandle, SendConfirmCallback, iotHubAdapter) != IOTHUB_CLIENT_OK) {
        success = false;
        goto cleanup;
    }
 
cleanup:
    if (messageHandle != NULL) {
        IoTHubMessage_Destroy(messageHandle);
    }
 
    return success;
}
 
static void SendConfirmCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback) {
    if (userContextCallback == NULL) {
        //error handling
        return;
    }
 
    if (result != IOTHUB_CLIENT_CONFIRMATION_OK){
        //error handling
    }
}
```

## <a name="next-steps"></a>後續步驟
- 閱讀 IoT 服務 ASC[概觀](overview.md)
- 深入了解 ASC iot[架構](architecture.md)
- 啟用[服務](quickstart-onboard-iot-hub.md)
- 讀取[常見問題集](resources-frequently-asked-questions.md)
- 了解如何存取[未經處理的安全性資料](how-to-security-data-access.md)
- 了解[建議](concept-recommendations.md)
- 了解[警示](concept-security-alerts.md)
