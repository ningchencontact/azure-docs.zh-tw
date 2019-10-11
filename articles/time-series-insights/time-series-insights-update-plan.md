---
title: 規劃您的 Azure 時間序列深入解析預覽環境| Microsoft Docs
description: 規劃您的 Azure 時間序列深入解析預覽版環境。
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/24/2019
ms.custom: seodec18
ms.openlocfilehash: 780e639ad6e1ee721820749da2df9ea6efa07bd6
ms.sourcegitcommit: f272ba8ecdbc126d22a596863d49e55bc7b22d37
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2019
ms.locfileid: "72273734"
---
# <a name="plan-your-azure-time-series-insights-preview-environment"></a>規劃您的 Azure 時間序列深入解析預覽版環境

本文說明使用 Azure 時間序列深入解析 Preview 來規劃和快速入門的最佳作法。

> [!NOTE]
> 如需規劃一般可用性時間序列深入解析實例的最佳做法，請參閱[規劃您的 Azure 時間序列深入解析一般可用性環境](time-series-insights-environment-planning.md)。

## <a name="best-practices-for-planning-and-preparation"></a>規劃和準備的最佳做法

若要開始使用時間序列深入解析，建議您先了解：

* 當您布建[時間序列深入解析預覽環境](#the-preview-environment)時所得到的結果。
* 您[的時間序列識別碼和時間戳記屬性](#configure-time-series-ids-and-timestamp-properties)為何。
* 新的[時間序列模型是](#understand-the-time-series-model)什麼，以及如何建立您自己的。
* 如何以[JSON 有效率地傳送事件](#shape-your-events)。
* 時間序列深入解析的商務嚴重損壞[修復選項](#business-disaster-recovery)。

Azure 時間序列深入解析採用隨用隨付商務模型。 如需費用和容量的詳細資訊，請參閱[時間序列深入解析定價](https://azure.microsoft.com/pricing/details/time-series-insights/)。

## <a name="the-preview-environment"></a>預覽環境

當您佈建時間序列深入解析預覽版環境時，您會建立兩個 Azure 資源：

* Azure 時間序列深入解析預覽版環境
* Azure 儲存體一般用途 V1 帳戶

若要開始使用，您需要三個額外項目：

* [時間序列模型](./time-series-insights-update-tsm.md)
* [連線到時間序列深入解析的事件來源](./time-series-insights-how-to-add-an-event-source-iothub.md)
* [流入事件來源的事件](./time-series-insights-send-events.md)，這些事件必須已經對應至該模型，且為有效的 JSON 格式

## <a name="configure-time-series-ids-and-timestamp-properties"></a>設定時間序列識別碼和時間戳記屬性

若要建立新的時間序列深入解析環境，請選取一個時間序列識別碼。 此動作會當作您資料的邏輯分割。 如上述事項，請務必備妥您的時間序列識別碼。

> [!IMPORTANT]
> 時間序列識別碼「不可變」且「事後無法變更」。 在最終選取前及第一次使用前，請確認每個識別碼。

您最多可以選取三個金鑰，以唯一區分您的資源。 如需詳細資訊，請參閱[選擇時間序列識別碼的最佳做法](./time-series-insights-update-how-to-id.md)和[儲存體與輸入](./time-series-insights-update-storage-ingress.md)。

時間戳記屬性也很重要。 您可以在新增事件來源時指定此屬性。 每個事件來源都有選擇性的時間戳記屬性，可用來依時間追蹤事件來源。 時間戳記值會區分大小寫，而且格式必須設定為每個事件來源的個別規格。

> [!TIP]
> 請確認您事件來源的格式設定和剖析需求。

保留空白時，系統會使用事件來源內的事件加入佇列時間，作為事件時間戳記。 如果您傳送歷史資料或批次事件，自訂「時間戳記屬性」比預設的「事件加入佇列時間」更有用。 如需詳細資訊，請參閱如何[在 Azure IoT 中樞中新增事件來源](./time-series-insights-how-to-add-an-event-source-iothub.md)。

## <a name="understand-the-time-series-model"></a>了解時間序列模型

您現在可以設定您時間序列深入解析環境的時間序列模型。 使用新模型很容易就能尋找及分析 IoT 資料。 它能用來鑑藏、維護及擴充時間序列資料，並協助準備取用者可用的資料集。 此模型使用時間序列識別碼，它會對應至實例，以將唯一資源與變數（稱為類型和階層）產生關聯。 了解新的[時間序列模型](./time-series-insights-update-tsm.md)。

模型是動態的，所以隨時可以建置。 若要快速開始使用，請先建置並上傳模型，再將資料推送至時間序列深入解析中。 若要建置您的模型，請參閱[使用時間序列模型](./time-series-insights-update-how-to-tsm.md)。

對許多客戶而言，時間序列模型對應至現有的資產模型或 ERP 系統。 如果您沒有現有的模型，我們[提供](https://github.com/Microsoft/tsiclient)的預先建置使用者體驗可快速啟動並執行。 若要了解模型能如何協助您，請檢視[範例示範環境](https://insights.timeseries.azure.com/preview/demo)。

## <a name="shape-your-events"></a>塑形您的事件

您可以確認您傳送事件到時間序列深入解析的方式。 在理想情況下，您的事件已妥善且有效率地反正規化。

良好的經驗法則：

* 將中繼資料儲存在時間序列模型中。
* 時間序列模式、實例欄位和事件只包含必要的資訊，例如時間序列識別碼或時間戳記。

如需詳細資訊，請參閱[塑形事件](./time-series-insights-send-events.md#supported-json-shapes)。

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>後續步驟

- 請參閱[Azure Advisor](../advisor/advisor-overview.md) ，以規劃您的商務復原設定選項。

- 若要深入瞭解[儲存體和](./time-series-insights-update-storage-ingress.md)輸入，請參閱時間序列深入解析預覽。

- 深入瞭解時間序列深入解析預覽中的[資料模型](./time-series-insights-update-tsm.md)。