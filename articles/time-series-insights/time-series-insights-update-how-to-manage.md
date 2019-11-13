---
title: 布建和管理預覽環境-Azure 時間序列 |Microsoft Docs
description: 瞭解如何布建和管理 Azure 時間序列深入解析預覽環境。
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/23/2019
ms.custom: seodec18
ms.openlocfilehash: f66925c20f6767286eb98a7f5fab180845da4c33
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014787"
---
# <a name="provision-and-manage-azure-time-series-insights-preview"></a>佈建及管理 Azure 時間序列深入解析預覽版

此文說明如何使用 [Azure 入口網站](https://portal.azure.com/)建立及管理 Azure 時間序列深入解析預覽版環境。

## <a name="overview"></a>Overview

Azure 時間序列深入解析預覽版環境是隨用隨付 (PAYG) 環境。

當您布建 Azure 時間序列深入解析預覽環境時，您會建立下列 Azure 資源：

* Azure 時間序列深入解析預覽版環境  
* Azure 儲存體一般用途 v1 帳戶
* 選擇性的暖存放區，可提供更快速且無限制的查詢
  
了解[如何規劃您的環境](./time-series-insights-update-plan.md)。

將每個 Azure 時間序列深入解析預覽環境與事件來源產生關聯。 如需詳細資訊，請閱讀[新增事件中樞來源](./time-series-insights-how-to-add-an-event-source-eventhub.md)與[新增 IoT 中樞來源](./time-series-insights-how-to-add-an-event-source-iothub.md)。 您會在此步驟提供時間戳記識別碼屬性與唯一的取用者群組。 這樣做可以確定環境有適當事件的存取權。

> [!NOTE]
> 當您建立時間序列預覽環境時，在布建工作流程中，上述步驟是選擇性的。 不過，您必須將事件來源連結到環境，讓資料可以在該環境中開始流動。

佈建完成後，您可以修改您的存取原則與其他環境屬性，以符合您的商業需求。

## <a name="create-the-environment"></a>建立環境

若要建立 Azure 時間序列深入解析預覽環境：

1. 在 [ **SKU** ] 功能表上，選取 [ **PAYG** ] 按鈕。 提供環境名稱，然後選擇要使用的訂用帳戶群組和資源群組。 然後選取支援的位置來裝載環境。

   [![建立 Azure 時間序列深入解析實例。](media/v2-update-manage/manage-three.png)](media/v2-update-manage/manage-three.png#lightbox)

1. 輸入時間序列識別碼。

    >[!NOTE]
    > * 時間序列識別碼區分大小寫且不可變。 (設定之後就無法變更。)
    > * 時間序列識別碼最多可以有三個金鑰。
    > * 如需選取時間序列識別碼的詳細資訊，請閱讀[選擇時間序列識別碼](./time-series-insights-update-how-to-id.md)。

1. 透過選取儲存體帳戶名稱並指定複寫選項，以建立 Azure 儲存體帳戶。 這樣做會自動建立 Azure 儲存體一般用途 v1 帳戶。 帳戶會建立在與您先前選取的 Azure 時間序列深入解析預覽環境相同的區域中。

    [![為您的實例建立 Azure 儲存體帳戶](media/v2-update-manage/manage-five.png)](media/v2-update-manage/manage-five.png#lightbox)

1. **（選擇性）** 如果您想要更快速且無限的查詢環境中最新的資料，請為您的環境啟用暖存放區。 建立時間序列深入解析預覽環境之後，您也可以透過左側流覽窗格中的 [**存放裝置**設定] 選項來建立或刪除暖存放區。

1. **（選擇性）** 您現在可以新增事件來源。 或者，您可以等到實例布建完成。

   * 時間序列深入解析支援[Azure IoT 中樞](./time-series-insights-how-to-add-an-event-source-iothub.md)和[Azure 事件中樞](./time-series-insights-how-to-add-an-event-source-eventhub.md)作為事件來源選項。 雖然您在建立環境時只能新增單一事件來源，但您稍後可以加入另一個事件來源。 當您新增事件來源時，可以選取現有的取用者群組，或建立新的取用者群組。 最好是建立唯一的取用者群組，以確保您的 Azure 時間序列深入解析預覽環境可以看到所有事件。

   * 選擇適當的時間戳記屬性。 根據預設，Azure 時間序列深入解析會針對每個事件來源使用訊息排入佇列的時間。

     > [!TIP]
     > 以訊息排入佇列的時間可能不是最佳設定，無法在批次事件案例或歷程記錄資料上傳案例中使用。 在這種情況下，請務必確認您的決定要使用或不使用 Timestamp 屬性。

     [![事件來源 索引標籤](media/v2-update-manage/manage-two.png)](media/v2-update-manage/manage-two.png#lightbox)

1. 確認您的環境已使用您想要的設定進行布建。

    [![審查 + 建立 索引標籤](media/v2-update-manage/manage-three.png)](media/v2-update-manage/manage-three.png#lightbox)

## <a name="manage-the-environment"></a>管理環境

您可以使用 Azure 入口網站管理 Azure 時間序列深入解析預覽版環境。 當您透過 Azure 入口網站進行管理時，您會看到隨用隨付 Azure 時間序列深入解析預覽環境與正式運作的 S1 或 S2 環境之間的一些主要差異：

* Azure 入口網站的 [概觀] 刀鋒視窗在 Azure 時間序列深入解析中不會變更，但下列方式除外：
  * 已移除容量，因為它不適用於隨用隨付環境。
  * 已加入時間序列識別碼屬性。 它會決定分割您的資料的方式。
  * 已移除參考資料集。
  * 顯示的 URL 會將您導向 [Azure 時間序列深入解析預覽版總管](./time-series-insights-update-explorer.md)。
  * 已列出您的 Azure 儲存體帳戶名稱。

* Azure 入口網站的 [**設定**] 分頁會在 Azure 時間序列深入解析 Preview 中移除，因為 PAYG 環境無法設定。 不過，您可以使用**存放裝置**設定來設定新引進的暖存放區。

* Azure 入口網站的**參考資料** 分頁會在 Azure 時間序列深入解析 Preview 中移除，因為參考資料不是隨用隨付環境的一部分。

[Azure 入口網站中的 ![時間序列深入解析預覽環境](media/v2-update-manage/manage-four.png)](media/v2-update-manage/manage-four.png#lightbox)

## <a name="next-steps"></a>後續步驟

- 閱讀[規劃您的環境](./time-series-insights-update-plan.md)，以深入瞭解時間序列深入解析正式運作的環境和預覽環境。

- 瞭解如何[新增事件中樞來源](./time-series-insights-how-to-add-an-event-source-eventhub.md)。

- 設定[IoT 中樞來源](./time-series-insights-how-to-add-an-event-source-iothub.md)。
