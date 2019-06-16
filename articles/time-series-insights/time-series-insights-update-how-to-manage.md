---
title: 佈建及管理 Azure 時間序列深入解析預覽版 | Microsoft Docs
description: 了解如何佈建及管理 Azure 時間序列深入解析預覽版。
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: 6251df2317ceff9dded92f2d829bfab0503fdf1b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66237595"
---
# <a name="provision-and-manage-azure-time-series-insights-preview"></a>佈建及管理 Azure 時間序列深入解析預覽版

此文說明如何使用 [Azure 入口網站](https://portal.azure.com/)建立及管理 Azure 時間序列深入解析預覽版環境。

## <a name="overview"></a>概觀

Azure 時間序列深入解析預覽版環境是隨用隨付 (PAYG) 環境。

當您佈建 Azure 時間序列深入解析預覽版環境時，您會建立兩個 Azure 資源：

* Azure 時間序列深入解析預覽版環境  
* Azure 儲存體一般用途 v1 帳戶
  
了解[如何規劃您的環境](./time-series-insights-update-plan.md)。

>[!IMPORTANT]
> 預覽版本中，確定您使用 Azure 儲存體的一般用途 v1 (GPv1) 帳戶。

(選擇性) 您可以將每個 Azure 時間序列深入解析預覽版環境與一個事件來源關聯。 如需詳細資訊，請閱讀[新增事件中樞來源](./time-series-insights-how-to-add-an-event-source-eventhub.md)與[新增 IoT 中樞來源](./time-series-insights-how-to-add-an-event-source-iothub.md)。 您會在此步驟提供時間戳記識別碼屬性與唯一的取用者群組。 這樣做可以確定環境有適當事件的存取權。

佈建完成後，您可以修改您的存取原則與其他環境屬性，以符合您的商業需求。

## <a name="create-the-environment"></a>建立環境

下列步驟說明如何建立 Azure 時間序列深入解析預覽版環境：

1. 選取 [SKU]  功能表下的 [預付型]  。 提供環境名稱，並選擇要使用的訂用帳戶群組與資源群組。 接著，選取要在其中裝載環境的支援位置。

   [![建立 Azure Time Series Insights 執行個體。](media/v2-update-manage/manage_three.PNG)](media/v2-update-manage/manage_three.PNG#lightbox)

1. 輸入時間序列識別碼。

    >[!NOTE]
    > * 時間序列識別碼區分大小寫且不可變。 (設定之後就無法變更。)
    > * 時間序列識別碼最多可以有三個金鑰。
    > * 如需選取時間序列識別碼的詳細資訊，請閱讀[選擇時間序列識別碼](./time-series-insights-update-how-to-id.md)。

1. 透過選取儲存體帳戶名稱並指定複寫選項，以建立 Azure 儲存體帳戶。 這樣做會自動建立 Azure 儲存體一般用途 v1 帳戶。 它將會在與您先前選取之 Azure 時間序列深入解析預覽版環境相同的區域中建立。

    [![建立您的執行個體的 Azure 儲存體帳戶](media/v2-update-manage/manage_five.PNG)](media/v2-update-manage/manage_five.PNG#lightbox)

1. (選擇性) 您可以新增事件來源。

   * 時間序列深入解析支援 [Azure IoT 中樞](./time-series-insights-how-to-add-an-event-source-iothub.md)與 [Azure 事件中樞](./time-series-insights-how-to-add-an-event-source-eventhub.md)作為選項。 雖然您在建立環境時只能新增單一事件來源，但您可以稍後新增另一個事件來源。 最好建立唯一取用者群組以確定您的 Azure 時間序列深入解析預覽版執行個體可以看到所有事件。 新增事件來源時，您可以選取現有的取用者群組或建立新的取用者群組。

   * 您也應該選擇適當的時間戳記屬性。 根據預設，Azure 時間序列深入解析為每個事件來源使用訊息加入佇列時間。

     > [!TIP]
     > 在批次事件或歷史資料上傳案例中，訊息加入佇列時間可能不是最佳配置設定。 務必確定您是否要在此類案例中使用時間戳記屬性的決定。

     [![事件來源 索引標籤](media/v2-update-manage/manage_two.PNG)](media/v2-update-manage/manage_two.PNG#lightbox)

1. 確認您的環境已使用想要的設定佈建。

    [![檢閱 + 建立的索引標籤](media/v2-update-manage/manage_three.PNG)](media/v2-update-manage/manage_three.PNG#lightbox)

## <a name="manage-the-environment"></a>管理環境

您可以使用 Azure 入口網站管理 Azure 時間序列深入解析預覽版環境。 這裡提供使用 Azure 入口網站，管理預付型 Azure 時間序列深入解析預覽版環境與 S1 或 S2 環境的主要差異：

* Azure 入口網站的 [概觀]  刀鋒視窗在 Azure 時間序列深入解析中不會變更，但下列方式除外：
  * 已移除容量，因為此概念不適用於預付型環境。
  * 已新增時間序列識別碼屬性。 它會決定分割您的資料的方式。
  * 已移除參考資料集。
  * 顯示的 URL 會將您導向 [Azure 時間序列深入解析預覽版總管](./time-series-insights-update-explorer.md)。
  * 已列出您的 Azure 儲存體帳戶名稱。

* 在 Azure 時間序列深入解析預覽版中，已移除 Azure 入口網站的 [設定]  刀鋒視窗，因為無法設定預付型環境。

* 在 Azure 時間序列深入解析預覽版中，已移除 Azure 入口網站的 [參考]  資料刀鋒視窗，因為參考資料不是預付型環境的元件。

[![在 Azure 入口網站中的時間序列深入解析預覽環境](media/v2-update-manage/manage_four.PNG)](media/v2-update-manage/manage_four.PNG#lightbox)

## <a name="next-steps"></a>後續步驟

- 閱讀[規劃您的環境](./time-series-insights-update-plan.md)。

- 了解如何[新增事件中樞來源](./time-series-insights-how-to-add-an-event-source-eventhub.md)。

- 設定[「 IoT 中樞 」 來源](./time-series-insights-how-to-add-an-event-source-iothub.md)。