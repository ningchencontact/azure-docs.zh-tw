---
title: 在 Azure IoT Central 應用程式中設定裝置範本 | Microsoft Docs
description: 了解如何使用量測、設定、屬性、規則和儀表板來設定裝置範本。
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 04/16/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 52c6c8fe4375354d650f92b73bffc288c9a2ccfe
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2018
ms.locfileid: "34201504"
---
# <a name="set-up-a-device-template"></a>設定裝置範本

裝置範本是一份藍圖，可為連線至 Microsoft Azure IoT Central 應用程式的某種裝置，定義其所具有的特性和行為。

例如，建置者可為具有下列項目的 IoT 連網風扇建立裝置範本：

- 溫度遙測量測

- 風扇馬達錯誤事件量測

- 風扇運轉狀態量測

- 風扇速度設定

- 位置屬性

- 可傳送警示的規則

- 可讓您對裝置有全面了解的儀表板

透過此裝置範本，操作員可以使用 **fan-1** 和 **fan-2** 等名稱，建立真實的風扇裝置並將其連網。 這些風扇全都會有量測、設定、屬性、規則和儀表板，可供應用程式使用者監視和管理。

> [!NOTE]
只有建置者和系統管理員可以建立、編輯和刪除裝置範本。 任何使用者都可以在 [Device Explorer] 頁面，透過現有裝置範本來建立裝置。

## <a name="create-a-new-device-template"></a>建立新的裝置範本

1. 瀏覽至 [應用程式建置者] 頁面。

1. 若要建立空白範本，請選擇 [建立裝置範本]，然後選擇 [自訂]。

1. 輸入新裝置範本的名稱，然後選擇 [建立]。

    ![裝置詳細資料頁面](./media/howto-set-up-template/devicedetailspage.png)

1. 現在您位於新模擬裝置的 [裝置詳細資料] 頁面。 當您建立新的裝置範本時，系統會自動為您建立模擬裝置。 模擬裝置會報告資料，並可透過和真實裝置一樣的方式來加以控制。

現在，請看看 [裝置詳細資料] 頁面上的每個索引標籤。

## <a name="measurements"></a>量測

量測是來自裝置的資料。 您可以在裝置範本中新增多個量測，以符合裝置的功能。 目前支援的量測類型有遙測和事件。

- **遙測**量測是數值資料點，裝置會收集一段時間的資料點，而且資料點會以連續資料流的形式呈現。 例如，溫度。
- **事件**量測是時間點資料，會代表裝置上某個重要的事物。 事件會有相關聯的嚴重性來表示事件的重要性。 例如，風扇馬達錯誤
- **狀態**量測代表裝置或其元件在一段時間內的狀態。 例如，風扇模式可定義為具有運轉中和已停止這兩種可能的狀態。

### <a name="create-a-telemetry-measurement"></a>建立遙測量測
若要新增遙測量測，請按一下 [+ 新增量測] 按鈕來開啟表單，從中選取量測類型選項。 選取 [遙測]，然後在 [建立遙測] 表單上輸入詳細資料。

> [!NOTE]
> 真實裝置連網時，請注意裝置所報告量測的名稱。 該名稱必須與量測的 [欄位名稱] 完全相符。

例如，您可以新增溫度遙測量測：

![量測表單](./media/howto-set-up-template/measurementsform.png)

選擇 [儲存] 後，[溫度] 量測就會出現在量測清單中，而且操作員會看到裝置所收集溫度資料的視覺效果。

![量測圖表](./media/howto-set-up-template/measurementsgraph.png)

### <a name="create-an-event-measurement"></a>建立事件量測
若要新增事件量測，請按一下 [+ 新增量測] 按鈕來開啟表單，從中選取量測類型選項。 選取 [事件]，然後在 [建立事件] 表單上輸入詳細資料。

在這張表單上提供事件的 [顯示名稱]、[欄位名稱] 和 [嚴重性]。 可供選擇的嚴重性層級有三個 - **錯誤**、**警告**和**資訊**。  

例如，您可以新增「風扇馬達錯誤」事件。

![事件量測表單](./media/howto-set-up-template/eventmeasurementsform.png)

選擇 [儲存] 後，[風扇馬達錯誤] 量測就會出現在量測清單中，而且操作員會看到裝置所傳送事件資料的視覺效果。

![事件量測圖表](./media/howto-set-up-template/eventmeasurementschart.png)

若要檢視關於事件的其他詳細資料，請按一下圖表上的 [事件] 圖示：

![事件量測詳細資料](./media/howto-set-up-template/eventmeasurementsdetail.png)


### <a name="create-a-state-measurement"></a>建立狀態量測
若要新增狀態量測，請按一下 [+ 新增量測] 按鈕來開啟表單，從中選取量測類型選項。 選取 [狀態]，然後在 [建立狀態] 表單上輸入詳細資料。

在這張表單上提供狀態的 [顯示名稱]、[欄位名稱] 和可能的**值**。 每個**值**也可以具有顯示名稱，以便用於在圖表和資料表上顯示值。

例如，您可以新增「風扇模式」狀態，讓其具有兩個可由裝置傳送的可能值，分別是**運轉中**和**已停止**。

![狀態量測表單](./media/howto-set-up-template/statemeasurementsform.png)

選擇 [儲存] 後，[風扇模式] 狀態量測就會出現在量測清單中，而且操作員會看到裝置所傳送狀態資料的視覺效果。

![狀態量測圖表](./media/howto-set-up-template/statemeasurementschart.png)

如果裝置在一小段時間內傳送太多資料點，狀態量測在顯示時就會使用不同的視覺效果，如下所示。 如果您按一下圖表，則該時段內的所有資料點會依時間先後順序顯示。 您也可以縮小時間範圍，以查看圖表上所繪製的量測。

![狀態量測詳細資料](./media/howto-set-up-template/statemeasurementsdetail.png)


## <a name="settings"></a>設定

設定可控制裝置。 設定可讓應用程式操作員對裝置提供輸入。 您可以在裝置範本中新增多個設定，這些設定會在 [設定] 索引標籤上顯示為圖格供操作員使用。 可新增的設定有六種：數字、文字、日期、切換、挑選清單和區段標籤。

> [!NOTE]
> 真實裝置連網時，請注意裝置所報告設定的名稱。 該名稱必須與設定的 [欄位名稱] 完全相符。

設定的狀態可為三者其中之一。 裝置會報告這些狀態。

- **已同步處理**：裝置已變更而能反映設定值。

- **擱置**：裝置目前正在變更為設定值。

- **錯誤**：裝置傳回錯誤。

例如，您可以新增風扇速度設定：

![設定表單](./media/howto-set-up-template/settingsform.png)

選擇 [儲存] 後，**風扇速度**設定就會顯示為圖格，並已準備好可供用來變更裝置的風扇速度。

> [!NOTE]
> 在建立新的圖格之後，就可以試用新的設定。 首先，請到螢幕右上方關閉設計模式：

![設定圖格](./media/howto-set-up-template/settingstile.png)

## <a name="properties"></a>properties

屬性為與裝置相關聯的裝置中繼資料，例如裝置位置和序號。 您可以在裝置範本中新增多個屬性，這些屬性會在 [屬性] 索引標籤上顯示為圖格。操作員可以在建立新裝置時指定屬性值，並可隨時編輯這些值。 可新增的屬性有六種：數字、文字、日期、切換、裝置屬性和標籤。

屬性分為兩種：

- **裝置屬性**是裝置所報告的屬性。
- **應用程式屬性**是僅儲存在應用程式中的屬性。 裝置不認得應用程式屬性。

> [!NOTE]
> 若為裝置屬性，當真實裝置連網時，請注意裝置所報告屬性的名稱。 該名稱必須與屬性的 [欄位名稱] 完全相符。 若為應用程式屬性，欄位名稱可為所需的任意值，只要該名稱是裝置範本中的唯一名稱即可。

例如，您可以將裝置位置新增為新的屬性：

![屬性表單](./media/howto-set-up-template/propertiesform.png)

選擇 [儲存] 後，裝置位置會顯示為圖格：

![屬性圖格](./media/howto-set-up-template/propertiestile.png)

> [!NOTE]
> 在建立新的圖格之後，就可以變更屬性值。 首先，請到螢幕右上方關閉設計模式。

## <a name="rules"></a>規則

規則可讓操作員近乎即時地監視裝置。 規則會在觸發規則時自動叫用**動作**，例如傳送電子郵件。 目前可用的規則只有一種：

- **遙測規則：** 當所選裝置的遙測超出指定閾值時，便會觸發遙測規則。 深入了解[遙測規則](howto-create-telemetry-rules.md)。

## <a name="dashboard"></a>儀表板

儀表板可供操作員查看裝置的相關資訊。 身為建置者，您可以在此頁面新增圖格，以協助操作員了解裝置的運作情形。 您可以在裝置範本中新增多個儀表板圖格。 可新增的儀表板圖格有六種：影像、折線圖、橫條圖、KPI、設定和屬性，以及標籤。

例如，您可以新增 [設定和屬性] 圖格，以顯示目前選取的設定和屬性值：

![儀表板裝置詳細資料表單](./media/howto-set-up-template/dashboardsettingsandpropertiesform.png)

現在，當操作員檢視儀表板時，就會看到這個可顯示裝置屬性和設定的圖格：

![儀表板圖格](./media/howto-set-up-template/dashboardtile.png)

## <a name="next-steps"></a>後續步驟

您現在已了解如何在 Azure IoT Central 應用程式中設定裝置範本，以下是建議的後續步驟：

> [!div class="nextstepaction"]
> [建立新的裝置範本版本](howto-version-devicetemplate.md)
