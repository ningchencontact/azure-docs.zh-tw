---
title: Azure Digital Twins 公開預覽服務限制 | Microsoft Docs
description: 了解 Azure Digital Twins 公開預覽服務限制
author: dwalthermsft
manager: deshner
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: dwalthermsft
ms.openlocfilehash: 86ae75118dd1311ea2ae92fb718fe4c58b8e5673
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/02/2018
ms.locfileid: "50961750"
---
# <a name="public-preview-service-limits"></a>公開預覽服務限制

在公開預覽期間，Azure Digital Twins 具有下列暫時的訂用帳戶、執行個體和比率限制。

這些限制條件可讓您更輕鬆地了解新服務和其許多功能。

> [!NOTE]
> 公開上市 (GA) 後，這些限制將會增加或移除。

## <a name="per-subscription-limits"></a>每個訂用帳戶的限制

在公開預覽期間，每個 Azure 訂用帳戶一次只能建立或執行一個 Azure Digital Twins 執行個體。

> [!TIP]
> 如果您刪除執行個體，則可建立一個新的執行個體。

## <a name="per-instance-limits"></a>每個執行個體的限制

接著，每個 Azure Digital Twins 執行個體可以有：

- 一個 **IoTHub** 資源。
- 一個事件類型為 **DeviceMessage** 的 **EventHub** 端點。
- 最多三個事件類型為 **SensorChange**、**SpaceChange**、**TopologyOperation** 或 **UdfCustom** 的 **EventHub**、**ServiceBus** 或 **EventGrid** 端點。

## <a name="management-api-limits"></a>管理 API 限制

管理 API 的要求比率限制如下：

- 每秒可對管理 API 發出 100 個要求。
- 單一管理 API 查詢最多會傳回 1,000 個物件。 

> [!IMPORTANT]
> 如果超過 1,000 個物件的限制，您就會收到錯誤，而且必須簡化您的查詢。

## <a name="udf-rate-limits"></a>UDF 比率限制

對於可對您 Azure Digital Twins 執行個體發出的所有使用者定義函式呼叫總數，其限制如下：

- 每秒 400 個用戶端程式庫呼叫
- 每秒 100 次 **SendNotification** 呼叫

> [!NOTE]
> 下列動作可能會產生要暫時套用的額外比率限制：
> - 對拓撲物件中繼資料所做的編輯
> - 對 UDF 定義所做的更新
> - 第一次傳送遙測資料的裝置

## <a name="device-telemetry-limits"></a>裝置遙測限制

下列限制是裝置可傳送至 Azure Digital Twins 執行個體的所有訊息總數上限：

- 每秒 100 個訊息

## <a name="next-steps"></a>後續步驟

若要試用 Azure Digital Twins 的範例，請移至[尋找可用會議室的快速入門](./quickstart-view-occupancy-dotnet.md)。