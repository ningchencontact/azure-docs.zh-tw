---
title: Azure 監視器警示的一般警示結構描述
description: 了解常見的警示結構描述，您應該使用它，以及如何加以啟用的原因
author: anantr
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: anantr
ms.component: alerts
ms.openlocfilehash: c18227a491478d0d8010761440a54fd088344b39
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2019
ms.locfileid: "60149371"
---
# <a name="common-alert-schema"></a>一般警示結構描述

本文說明常見的警示結構描述的是，使用它，以及如何啟用它的優點。

## <a name="what-is-the-common-alert-schema"></a>什麼是常見的警示結構描述？

常見的警示結構描述會立即標準化的使用體驗，在 Azure 中的警示通知。 在過去，三種警示類型在 Azure 中的立即 （計量、 記錄和活動記錄檔） 有它們自己的電子郵件範本、 webhook 結構描述等。常見的警示結構描述中，您現在可以接收警示通知，以一致的結構描述。

任何警示的執行個體描述**受影響的資源**並**警示原因的**，和這些執行個體以通用的結構描述，下列各節中所述：
* **Essentials**:一組**標準化欄位**，在所有的警示類型，描述通用**哪項資源**警示是以及其他一般警示中繼資料 （例如嚴重性或描述）。 
* **警示內容**:一組欄位會描述**造成警示的**，使用不同的欄位**根據警示類型**。 比方說，計量的警示會有計量的值，在警示內容中，度量名稱等的欄位，而活動記錄警示會有事件產生警示的相關資訊。 

我們從客戶收到的典型整合案例牽涉到要擔心的小組負責 team 之後，其啟動工作某些樞紐 （例如，資源群組） 為基礎的警示執行個體的路由。 常見的警示結構描述中，您可以有標準化路由邏輯在警示類型之間利用必要的欄位中，保留原狀以進一步調查的關注小組內容欄位。

這表示，您可能有較少的整合，使得管理和維護它們的程序_大部分_更簡單的工作。 此外，未來的警示承載類 （例如，自訂，診斷擴充，等等） 只會呈現出常見的結構描述中。

## <a name="what-enhancements-does-the-common-alert-schema-bring"></a>常見的警示結構描述會將哪些增強功能？

常見的警示結構描述將主要資訊清單形式顯現在您的警示通知。 您會看到的增強功能如下：

|  動作 | 增強功能|
|:---|:---|
| sms | 所有警示類型一致 SMS 範本。 |
| 電子郵件 | 一致且詳細的電子郵件範本，可讓您輕鬆地診斷問題。 內嵌在入口網站和受影響的資源上的警示執行個體的深層連結，請確定您可以快速跳至補救程序。 |
| Webhook/邏輯應用程式/Azure 函式/自動化 Runbook | 一致 JSON 結構，對於所有的警示類型，可讓您輕鬆地建置在不同的警示類型之間的整合。 |

新的結構描述也可讓更豐富的警示的使用體驗在 Azure 入口網站和 Azure 行動應用程式中未來。 

[深入了解 Webhook/邏輯應用程式/Azure 函式/自動化 Runbook 的結構描述定義。](https://aka.ms/commonAlertSchemaDefinitions)

> [!NOTE]
> 下列動作不支援常見的警示結構描述：ITSM 連接器。

## <a name="how-do-i-enable-the-common-alert-schema"></a>如何啟用常見的警示結構描述？

您可以選擇加入或退出一般警示架構透過動作群組，在這兩個入口網站和 REST API。 若要切換至新的結構描述切換存在在動作層級。 例如，您必須個別選擇的電子郵件動作和 webhook 動作。

> [!NOTE]
> 1. 下列警示類型預設支援通用的結構描述 (沒有選擇在所需):
>     * 智慧型偵測警示
> 1. 下列警示類型目前不支援通用的結構描述：
>     * 所產生的警示[適用於 Vm 的 Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview)
>     * 所產生的警示[Azure 成本管理](https://docs.microsoft.com/azure/billing/billing-cost-management-budget-scenario)

### <a name="through-the-azure-portal"></a>透過 Azure 入口網站

![常見的警示結構描述選擇](media/alerts-common-schema/portal-opt-in.png)

1. 在 動作群組中，開啟任何現有或新的動作。 
1. 切換來啟用常見的警示結構描述所示，選取 [是]。

### <a name="through-the-action-groups-rest-api"></a>透過動作群組 REST API

您也可以使用[動作群組 API](https://docs.microsoft.com/rest/api/monitor/actiongroups)選擇常見的警示結構描述。 同時透過[建立或更新](https://docs.microsoft.com/rest/api/monitor/actiongroups/createorupdate)REST API 呼叫時，您可以設定旗標 」 useCommonAlertSchema 」 為 'true' （若要選擇加入） 或 （若要退出） 的 ' false'，任何下列動作-也就是電子郵件/webhook/邏輯應用程式/Azure 函式/自動化 runbook。

例如，下列要求主體對[建立或更新](https://docs.microsoft.com/rest/api/monitor/actiongroups/createorupdate)REST API 將會執行下列動作：

* 啟用電子郵件動作 「 John Doe 的電子郵件 」 的一般警示結構描述
* 停用電子郵件動作 「 Jane Smith 的電子郵件 」 的一般警示結構描述
* 啟用一般警示 webhook 動作 」 範例 webhook"架構

```json
{
  "properties": {
    "groupShortName": "sample",
    "enabled": true,
    "emailReceivers": [
      {
        "name": "John Doe's email",
        "emailAddress": "johndoe@email.com",
        "useCommonAlertSchema": true
      },
      {
        "name": "Jane Smith's email",
        "emailAddress": "janesmith@email.com",
        "useCommonAlertSchema": false
      }
    ],
    "smsReceivers": [
      {
        "name": "John Doe's mobile",
        "countryCode": "1",
        "phoneNumber": "1234567890"
      },
      {
        "name": "Jane Smith's mobile",
        "countryCode": "1",
        "phoneNumber": "0987654321"
      }
    ],
    "webhookReceivers": [
      {
        "name": "Sample webhook",
        "serviceUri": "http://www.example.com/webhook",
        "useCommonAlertSchema": true
      }
    ]
  },
  "location": "Global",
  "tags": {}
}
```





## <a name="next-steps"></a>後續步驟

- [一般 Webhook/邏輯應用程式/Azure 函式/自動化 Runbook 的警示的結構描述定義。](https://aka.ms/commonAlertSchemaDefinitions)



