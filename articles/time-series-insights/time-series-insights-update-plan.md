---
title: Plan your Preview environment - Azure Time Series Insights | Microsoft Docs
description: Learn how to plan your Azure Time Series Insights Preview environment.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 11/22/2019
ms.custom: seodec18
ms.openlocfilehash: 9fb2dcf2c05b709340f8e9ae549bab5756e6abf2
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420320"
---
# <a name="plan-your-azure-time-series-insights-preview-environment"></a>規劃您的 Azure 時間序列深入解析預覽版環境

This article describes best practices to plan and get started quickly by using Azure Time Series Insights Preview.

> [!NOTE]
> For best practices to plan a general availability Time Series Insights instance, see [Plan your Azure Time Series Insights general availability environment](time-series-insights-environment-planning.md).

## <a name="best-practices-for-planning-and-preparation"></a>規劃和準備的最佳做法

Best practices surrounding planning for and preparing your environment are described further in the following articles:

* What you get when you [provision a Time Series Insights Preview environment](#the-preview-environment).
* What your [Time Series IDs and Timestamp properties are](#configure-time-series-ids-and-timestamp-properties).
* What the new [Time Series Model is](#understand-the-time-series-model), and how to build your own.
* How to [send events efficiently in JSON](#shape-your-events).
* Time Series Insights [business disaster recovery options](#business-disaster-recovery).

Azure Time Series Insights employs a pay-as-you-go business model. 如需費用和容量的詳細資訊，請參閱[時間序列深入解析定價](https://azure.microsoft.com/pricing/details/time-series-insights/)。

## <a name="the-preview-environment"></a>The preview environment

當您佈建時間序列深入解析預覽版環境時，您會建立兩個 Azure 資源：

* Azure 時間序列深入解析預覽版環境
* Azure 儲存體一般用途 V1 帳戶

As part of the provisioning process, you specify whether you want to enable a warm store. Warm store provides you with a tiered query experience. When enabled, you must specify a retention period between 7 and 30 days. Queries executed within the warm store retention period generally provide faster response times. When a query spans over the warm store retention period, it's served from cold store.

Queries on warm store are free, while queries on cold store incur costs. It's important to understand your query patterns and plan your warm store configuration accordingly. We recommend that interactive analytics on the most recent data reside in your warm store and pattern analysis and long-term trends reside in cold.

> [!NOTE]
> We currently support a maximum of 1,000 properties with warm store.

若要開始使用，您需要三個額外項目：

* [時間序列模型](./time-series-insights-update-tsm.md)
* [連線到時間序列深入解析的事件來源](./time-series-insights-how-to-add-an-event-source-iothub.md)
* [流入事件來源的事件](./time-series-insights-send-events.md)，這些事件必須已經對應至該模型，且為有效的 JSON 格式

## <a name="review-preview-limits"></a>Review preview limits

[!INCLUDE [Review Time Series Insights Preview limits](../../includes/time-series-insights-preview-limits.md)]

## <a name="configure-time-series-ids-and-timestamp-properties"></a>Configure Time Series IDs and Timestamp properties

若要建立新的時間序列深入解析環境，請選取一個時間序列識別碼。 此動作會當作您資料的邏輯分割。 如上述事項，請務必備妥您的時間序列識別碼。

> [!IMPORTANT]
> Time Series IDs *can't be changed later*. 在最終選取前及第一次使用前，請確認每個識別碼。

You can select up to three keys to uniquely differentiate your resources. 如需詳細資訊，請參閱[選擇時間序列識別碼的最佳做法](./time-series-insights-update-how-to-id.md)和[儲存體與輸入](./time-series-insights-update-storage-ingress.md)。

The **Timestamp** property is also important. 您可以在新增事件來源時指定此屬性。 每個事件來源都有選擇性的時間戳記屬性，可用來依時間追蹤事件來源。 時間戳記值會區分大小寫，而且格式必須設定為每個事件來源的個別規格。

> [!TIP]
> 請確認您事件來源的格式設定和剖析需求。

保留空白時，系統會使用事件來源內的事件加入佇列時間，作為事件時間戳記。 如果您傳送歷史資料或批次事件，自訂「時間戳記屬性」比預設的「事件加入佇列時間」更有用。 For more information, read about how to [add event sources in Azure IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md).

## <a name="understand-the-time-series-model"></a>了解時間序列模型

您現在可以設定您時間序列深入解析環境的時間序列模型。 使用新模型很容易就能尋找及分析 IoT 資料。 它能用來鑑藏、維護及擴充時間序列資料，並協助準備取用者可用的資料集。 The model uses Time Series IDs, which map to an instance that associates the unique resource with variables, known as types, and hierarchies. 了解新的[時間序列模型](./time-series-insights-update-tsm.md)。

模型是動態的，所以隨時可以建置。 若要快速開始使用，請先建置並上傳模型，再將資料推送至時間序列深入解析中。 若要建置您的模型，請參閱[使用時間序列模型](./time-series-insights-update-how-to-tsm.md)。

對許多客戶而言，時間序列模型對應至現有的資產模型或 ERP 系統。 如果您沒有現有的模型，我們[提供](https://github.com/Microsoft/tsiclient)的預先建置使用者體驗可快速啟動並執行。 若要了解模型能如何協助您，請檢視[範例示範環境](https://insights.timeseries.azure.com/preview/demo)。

## <a name="shape-your-events"></a>塑形您的事件

您可以確認您傳送事件到時間序列深入解析的方式。 在理想情況下，您的事件已妥善且有效率地反正規化。

良好的經驗法則：

* Store metadata in your Time Series Model.
* Ensure that Time Series Mode, instance fields, and events include only necessary information, such as a Time Series ID or Timestamp property.

如需詳細資訊，請參閱[塑形事件](./time-series-insights-send-events.md#supported-json-shapes)。

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>後續步驟

- Review [Azure Advisor](../advisor/advisor-overview.md) to plan out your business recovery configuration options.
- Read more about [storage and ingress](./time-series-insights-update-storage-ingress.md) in the Time Series Insights Preview.
- Learn about [data modeling](./time-series-insights-update-tsm.md) in the Time Series Insights Preview.
