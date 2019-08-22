---
title: 在 Azure IoT Central 應用程式中建立和管理遙測規則 | Microsoft Docs
description: Azure IoT Central 遙測規則可讓您近乎即時地監視裝置，以及在觸發規則時自動叫用動作，例如傳送電子郵件。
author: ankitgupta
ms.author: ankitgup
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: d6deecf558105701be591c1f08923eca2c1e3bbd
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2019
ms.locfileid: "69879938"
---
# <a name="create-a-telemetry-rule-and-set-up-notifications-in-your-azure-iot-central-application-preview-features"></a>在您的 Azure IoT Central 應用程式中建立遙測規則並設定通知 (預覽功能)

*本文適用於操作員、建置員及系統管理員。*

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

您可以使用 Azure IoT Central 來遠端監視連線的裝置。 Azure IoT Central 規則可讓您近乎即時地監視裝置，以及自動叫用動作，例如傳送電子郵件或觸發 Microsoft Flow。 只要按幾下，就可以定義條件來監視裝置資料以及設定對應的動作。 本文將說明如何建立規則來監視裝置所傳送的遙測。

裝置可以使用遙測量測，從裝置傳送數值資料。 當所選裝置的遙測超出指定閾值時，便會觸發遙測規則。

## <a name="create-a-telemetry-rule"></a>建立遙測規則

若要建立遙測規則, 裝置定義必須至少定義一個遙測測量。 此範例使用會傳送溫度和濕度遙測的冷藏自動販賣機裝置。 此規則會監視裝置所報告的遙測，並在裝置溫度超過 80 度時傳送電子郵件。

1. 流覽至 [**規則**] 頁面。

1. 如果您尚未建立任何規則, 您會看到下列畫面:

    ![還沒有規則](media/howto-create-telemetry-rules-pnp/rules-landing-page1.png)

1. 選取 [ **+ 新增規則**], 以查看您可以建立的規則類型。

1. 選取 [**遙測**] 以建立規則來監視裝置遙測。

    ![規則類型](media/howto-create-telemetry-rules-pnp/rule-types1.png)


1. 輸入可協助您識別規則的名稱, 然後按 Enter 鍵。

1. 在 [範圍] 區段中, 選取您想要將此規則設為範圍的裝置定義。 您也可以在此畫面中, 使用 [ **+ 篩選**] 來篩選套用規則的裝置。 此規則會自動套用到裝置範本下的所有裝置。 若要停用規則, 請選取標頭中的 [**停**用] 按鈕。

### <a name="configure-the-rule-conditions"></a>設定規則條件

條件會定義規則所監視的準則。

1. 選取您要**將匯總設定**為 [開啟] 或 [關閉]。

      - 彙總為選擇性的。 如果沒有彙總，規則就會在每個遙測資料點符合條件時觸發。 例如, 如果規則設定為在溫度高於80時觸發, 則此規則幾乎會在裝置回報溫度 > 80 時立即觸發。
      - 如果選擇了彙總函式 (例如 average、min、max、count), 則使用者必須提供需要評估條件的**時間範圍**。 例如，如果您將期間設定為「5 分鐘」，而且您的規則會尋找超過 80 的平均溫度，則此規則會在平均溫度超過 80 至少 5 分鐘時觸發。 規則評估頻率與**時間範圍**相同, 這表示在此範例中, 規則會每隔5分鐘評估一次。

1. 從 [量測] 下拉式清單中選取想要監視的遙測。

1. 接下來, 選擇**運算子**並提供**值**。

     ![條件](media/howto-create-telemetry-rules-pnp/aggregate-condition-filled-out1.png)


>[!NOTE]
>藉由選取 [ **+ 條件**], 可以加入一個以上的遙測測量。 若指定多個條件，就必須符合所有條件才會觸發規則。 每個條件都是以隱含方式由 ' AND ' 子句聯結。 使用彙總時，必須彙總每個量測。

### <a name="configure-actions"></a>設定動作

本節示範如何設定要在引發規則時採取的動作。 當規則中指定的所有條件都評估為 True 時，即會叫用動作。

1. 按一下 [**動作**] 區段中的 [ **+] 動作**。 在此，您會看到可用動作的清單。  

    ![新增動作](media/howto-create-telemetry-rules-pnp/add-action1.png)


1. 選擇 [**電子郵件**] 動作、輸入動作的顯示名稱、[寄件者] 欄位中的有效電子郵件地址, 以及提供當規則引發時出現在電子郵件內文中的附注。

    > [!NOTE]
    > 只有已新增至應用程式且至少已登入一次的使用者才會收到電子郵件。 深入了解 Azure IoT Central 中的[使用者管理](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)。

   ![設定動作](media/howto-create-telemetry-rules-pnp/configure-action1.png)


1. 若要儲存動作, 請選擇 [**完成**]。

1. 若要儲存規則，請選擇 [儲存]。 幾分鐘內，規則就會生效，並開始監視傳送至應用程式的遙測。 符合規則中指定的條件時，規則就會觸發所設定的電子郵件動作。

## <a name="parameterize-the-rule"></a>將規則參數化

規則可以從 [裝置屬性] 衍生特定值來作為參數。 在不同裝置會有不同遙測閾值的情況下，使用參數會很有幫助。 建立規則時, 請選擇指定閾值的裝置屬性 (例如**最大理想臨界**值), 而不是提供絕對值, 例如80度。 當規則執行時，它會比對裝置遙測與裝置屬性中所設定的值。

使用參數是一種有效的方法, 可減少要管理的規則數目。

您也可以使用 [裝置屬性] 將動作設定為參數。 如果將電子郵件地址儲存為屬性，您就可以在定義 [收件者] 地址時加以使用。

## <a name="delete-a-rule"></a>刪除規則

如果不再需要某個規則，請開啟規則並選擇 [刪除] 來加以刪除。 規則在刪除後就會從裝置範本和所有相關聯的裝置中移除。

## <a name="enable-or-disable-a-rule"></a>啟用或停用規則

選擇您想要啟用或停用的規則。 切換規則中的 [**啟用**] 或 [**停**用] 按鈕, 以啟用或停用規則中範圍內所有裝置的規則。

## <a name="enable-or-disable-a-rule-for-a-device"></a>啟用或停用裝置的規則

選擇您想要啟用或停用的規則。 在 [**範圍**] 區段中新增篩選, 以在裝置範本中包含或排除特定裝置。

## <a name="next-steps"></a>後續步驟

既然您已瞭解如何在 Azure IoT Central 應用程式中建立規則, 建議的下一個步驟是瞭解[如何管理您的裝置](howto-manage-devices-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
