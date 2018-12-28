---
title: Azure 時間序列預覽版管理 - 如何佈建及管理 Azure 時間序列預覽版。 | Microsoft Docs
description: 了解如何佈建及管理 Azure 時間序列深入解析預覽版。
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/10/2018
ms.custom: seodec18
ms.openlocfilehash: 1834a59ff675f109267a406e3c2b03411e3bb50b
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/12/2018
ms.locfileid: "53273494"
---
# <a name="how-to-provision-and-manage-azure-time-series-insights-preview"></a>如何佈建及管理 Azure 時間序列深入解析預覽版

此文件說明如何使用 [Azure 入口網站](https://portal.azure.com/)建立及管理 Azure 時間序列深入解析預覽版環境。

## <a name="overview"></a>概觀

下面提供有關如何佈建 Azure 時間序列深入解析預覽版環境的簡短概觀：

* Azure 時間序列深入解析預覽版環境是**預付型**環境。
  * 您必須在建立程序中提供時間序列識別碼。 時間序列識別碼最多可以有三個金鑰。 閱讀[如何選擇時間序列識別碼](./time-series-insights-update-how-to-id.md)以了解如何選取時間序列識別碼。
  * 當您佈建 Azure 時間序列深入解析預覽版環境時，您會建立兩個 Azure 資源：

    * Azure 時間序列深入解析預覽版環境  
    * Azure 儲存體一般用途 V1 帳戶
  
    了解[如何規劃您的環境](./time-series-insights-update-plan.md)。

    >[!IMPORTANT]
    > 針對使用 V2 帳戶的客戶，請勿在您要使用的儲存體帳戶上啟用非經常性/封存屬性。

* 每個 Azure 時間序列深入解析預覽版環境都可以 (選擇性) 與一個事件來源關聯。 如需詳細資訊，請閱讀[如何新增事件中樞來源](./time-series-insights-how-to-add-an-event-source-eventhub.md)與[如何新增 IoT 中樞來源](./time-series-insights-how-to-add-an-event-source-iothub.md)。
  * 您將在此步驟提供時間戳記識別碼屬性與唯一的取用者群組。 這樣做可以確定環境有適當事件的存取權。

* 一旦佈建完成，您就可以修改您的存取原則與其他環境屬性，以符合您的商業需求。

## <a name="new-environment-creation"></a>新的環境建立作業

下列步驟說明如何建立 Azure 時間序列深入解析預覽版環境：

1. 選取 [SKU] 功能表下的 [預付型]。 提供環境名稱並指定要使用的訂用帳戶群組與資源群組。 接著，選取要在其中裝載環境的支援位置。

1. 輸入時間序列識別碼

    >[!NOTE]
    > * 時間序列識別碼區分大小寫且不可變 (設定之後無法變更)。
    > * 時間序列識別碼最多可以有 3 個金鑰。
    > * 閱讀[如何選擇時間序列識別碼](./time-series-insights-update-how-to-id.md)以了解如何選取時間序列識別碼。

1. 透過選取 Azure 儲存體帳戶名稱並指定複寫選項以建立 Azure 儲存體帳戶。 這樣做會自動建立 Azure 儲存體一般用途 V1 帳戶。 它將會在與您先前選取之 Azure 時間序列深入解析預覽版環境相同的區域中建立。

    ![建立 Azure 時間序列深入解析執行個體。][1]

1. (選擇性) 您可以新增事件來源。

   * 時間序列深入解析支援 [Azure IoT 中樞](./time-series-insights-how-to-add-an-event-source-iothub.md)與[事件中樞](./time-series-insights-how-to-add-an-event-source-eventhub.md)做為選項。 雖然您只能在建立環境時新增單一事件來源，稍後您可以新增額外的事件來源。 最好建立唯一取用者群組以確定您的 Azure 時間序列深入解析預覽本執行個體可以看到所有事件。 新增事件來源時，您可以選取現有的取用者群組或建立新的取用者群組。

   * 您也應該指定適當的時間戳記屬性。 根據預設，Azure 時間序列深入解析為每個事件來源使用訊息加入佇列時間。

     > [!TIP]
     > 在批次事件或歷史資料上傳案例中，訊息加入佇列時間可能不是最佳配置設定。 務必確定您是否要在此類案例中使用時間戳記屬性的決定。

    ![新增事件來源到您的執行個體。][2]

1. 檢閱並建立

    ![新增事件來源到您的執行個體。][3]

確認您的環境已使用想要的設定佈建。

## <a name="management"></a>管理

您可以使用 Azure 入口網站管理您的 Azure 時間序列深入解析預覽版環境。 這裡提供使用 Azure 入口網站管理**預付型** Azure 時間序列深入解析預覽版環境與 S1 或 S2 環境的主要差異：

* Azure 入口網站的 [概觀] 刀鋒視窗在 Azure 時間序列深入解析中沒有變更，但下列方式除外：
  * 已移除容量，因為此概念不適用於**預付型**環境。
  * 已新增時間序列識別碼屬性。 它會決定分割您的資料的方式。
  * 已移除參考資料集。
  * 顯示的 URL 會將您導向 [Azure 時間序列深入解析預覽版總管](./time-series-insights-update-explorer.md)。
  * 已列出您的 Azure 儲存體帳戶名稱。

* 在 Azure 時間序列深入解析預覽版中，已移除 Azure 入口網站的 [設定] 刀鋒視窗，因為**預付型**環境無法設定。

* 在 Azure 時間序列深入解析預覽版中，已移除 Azure 入口網站的 [參考] 資料刀鋒視窗，因為參考資料不是**預付型**環境的元件。

![驗證您的執行個體。][4]

## <a name="next-steps"></a>後續步驟

閱讀[如何規劃您的環境](./time-series-insights-update-plan.md)。

閱讀[如何新增事件中樞來源](./time-series-insights-how-to-add-an-event-source-eventhub.md)。

閱讀[如何新增 IoT 中樞來源](./time-series-insights-how-to-add-an-event-source-iothub.md)。

<!-- Images -->
[1]: media/v2-update-manage/manage_one.png
[2]: media/v2-update-manage/manage_two.png
[3]: media/v2-update-manage/manage_three.png
[4]: media/v2-update-manage/manage_four.png
