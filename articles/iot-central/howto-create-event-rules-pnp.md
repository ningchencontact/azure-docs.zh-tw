---
title: 在 Azure IoT Central 應用程式中建立和管理事件規則 | Microsoft Docs
description: Azure IoT Central 事件規則可讓您近乎即時地監視裝置，以及在觸發規則時自動叫用動作，例如傳送電子郵件。
author: dominicbetts
ms.author: dobett
ms.date: 07/23/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 1a7fc2b38e8354fb29255bb7f9d6b2c03ed53725
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2019
ms.locfileid: "69879951"
---
# <a name="create-an-event-rule-and-set-up-notifications-in-your-azure-iot-central-application-preview-features"></a>在您的 Azure IoT Central 應用程式中建立事件規則並設定通知 (預覽功能)

*本文適用於操作員、建置員及系統管理員。*

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

您可以使用 Azure IoT Central 來遠端監視連線的裝置。 Azure IoT Central 規則可讓您近乎即時地監視裝置, 並自動叫用動作, 例如傳送電子郵件。 只要按幾下，就可以定義條件來監視裝置資料以及設定對應的動作。 本文將說明如何建立規則來監視裝置所傳送的事件。

裝置可以使用事件量測來傳送重要或參考的裝置事件。 當裝置報告所選裝置的事件時，就會觸發事件規則。

## <a name="create-an-event-rule"></a>建立事件規則

若要建立事件規則，裝置範本必須至少定義一個事件量測。 此範例使用會報告風扇馬達錯誤事件的冷飲販賣機裝置。 此規則會監視裝置所報告的事件，並在每次報告事件時傳送電子郵件。

1. 流覽至 [**規則**] 頁面。

1. 如果您尚未建立任何規則，您會看到下列畫面：

   ![還沒有規則](media/howto-create-event-rules-pnp/rules-landing-page1.png)

1. 選取 [ **+ 新增規則**], 以查看您可以建立的規則類型。

1. 選擇 [**事件**] 以建立事件監視規則。

   ![規則類型](media/howto-create-event-rules-pnp/rule-types1.png)

1. 輸入可協助您識別規則的名稱, 然後按 Enter 鍵。

1. 在 [**範圍**] 區段中, 選取要將此規則設為範圍的裝置定義。 您也可以在此畫面中, 使用 [ **+ 篩選**] 來篩選套用規則的裝置。 此規則會自動套用到裝置範本下的所有裝置。 若要停用規則, 請選取標頭中的 [**停**用] 按鈕。

### <a name="configure-the-rule-conditions"></a>設定規則條件

條件會定義規則所監視的準則。

1. 選取您要**將匯總設定**為 [開啟] 或 [關閉]。

   - 如果沒有彙總，規則會在每個事件資料點符合條件時觸發。 例如, 如果您將規則的條件設定為在發生**風扇馬達錯誤**事件時觸發, 則此規則幾乎會在裝置報告該事件時立即觸發。
   - 如果**Count**當做彙總函式使用, 則您必須提供**值**, 以及需要評估條件的**時間範圍**。 在此情況下, 會匯總事件的計數, 而且只有在匯總的事件計數與值相符時, 才會觸發此規則。

1. 從 [**測量**] 下拉式清單中選擇您想要監視的事件。 此範例選取了**風扇馬達錯誤**事件。

1. (選擇性) 您也可以將 [**計數**] 設定為 [**匯總**], 並提供規則將會觸發的值。

     例如, 如果您想要在5分鐘內有三個以上的裝置事件時發出警示, 請選取事件, 並將彙總函式設定為**Count**、operator as**大於**, 並將**值**設為3。 將 [**時間範圍]** 設定為**5 分鐘**。 如果裝置在 5 分鐘內傳送三個以上的事件時，規則就會觸發。 規則評估頻率與**時間範圍**相同, 這表示在此範例中, 規則會每隔5分鐘評估一次。

 ![條件](media/howto-create-event-rules-pnp/aggregate-condition-filled-out1.png)

> [!NOTE]
> 您可以在**條件**下新增多個事件量測。 若指定多個條件，就必須符合所有條件才會觸發規則。 每個條件都是以隱含方式由 ' AND ' 子句聯結。 使用彙總時，必須彙總每個量測。

### <a name="configure-actions"></a>設定動作

本節示範如何設定要在引發規則時採取的動作。 當規則中指定的所有條件都評估為 True 時，即會叫用動作。

1. 按一下 [**動作**] 區段中的 [ **+] 動作**。 在此，您會看到可用動作的清單。  

   ![新增動作](media/howto-create-event-rules-pnp/add-action1.png)

1. 選擇 [**電子郵件**] 動作、輸入動作的顯示名稱、[寄件者] 欄位中的有效電子郵件地址, 以及提供當規則引發時出現在電子郵件內文中的附注。

   > [!NOTE]
   > 只有已新增至應用程式且至少已登入一次的使用者才會收到電子郵件。 深入了解 Azure IoT Central 中的[使用者管理](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)。

   ![設定動作](media/howto-create-event-rules-pnp/configure-action1.png)

1. 若要儲存動作, 請選擇 [**完成**]。

1. 若要儲存規則，請選擇 [儲存]。 幾分鐘內，規則就會生效，並開始監視傳送至應用程式的事件。 符合規則中指定的條件時，規則就會觸發所設定的電子郵件動作。

## <a name="parameterize-the-rule"></a>將規則參數化

規則可以從 [裝置屬性] 衍生特定值來作為參數。 在不同裝置的事件臨界值不同的情況下, 使用參數會很有説明。 當您建立規則時, 請選擇指定閾值的裝置屬性 (例如**最大理想臨界**值), 而不是提供絕對值, 例如3個事件。 當規則執行時, 它會將裝置事件與裝置屬性中所設定的值進行比對。

使用參數是一種有效的方法, 可減少要管理的規則數目。

您也可以使用 [裝置屬性] 將動作設定為參數。 如果將電子郵件地址儲存為裝置屬性，您就可以在定義 [收件者] 地址時加以使用。

## <a name="delete-a-rule"></a>刪除規則

如果不再需要某個規則，請開啟規則並選擇 [刪除] 來加以刪除。 規則在刪除後就會從裝置範本和所有相關聯的裝置中移除。

## <a name="enable-or-disable-a-rule"></a>啟用或停用規則

選擇您想要啟用或停用的規則。 切換規則中的 [**啟用**] 或 [**停**用] 按鈕, 以啟用或停用規則範圍內所有裝置的規則。

## <a name="enable-or-disable-a-rule-for-a-device"></a>啟用或停用裝置的規則

選擇您想要啟用或停用的規則。 在 [**範圍**] 區段中新增篩選, 以在裝置範本中包含或排除特定裝置。

## <a name="next-steps"></a>後續步驟

既然您已瞭解如何在 Azure IoT Central 應用程式中建立規則, 建議的下一個步驟是瞭解[如何管理您的裝置](howto-manage-devices-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
