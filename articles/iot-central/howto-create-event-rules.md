---
title: 在 Azure IoT Central 應用程式中建立和管理事件規則 | Microsoft Docs
description: Azure IoT Central 事件規則可讓您近乎即時地監視裝置，以及在觸發規則時自動叫用動作，例如傳送電子郵件。
services: iot-central
author: ankitgupta
ms.author: ankitgup
ms.date: 04/29/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: ede7748b1471136cf792c2b30b7c90e12b0b274a
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/13/2018
ms.locfileid: "39006843"
---
# <a name="create-an-event-rule-and-set-up-an-action-in-your-azure-iot-central-application"></a>在 Azure IoT Central 應用程式中建立事件規則並設定動作

您可以使用 Microsoft Azure IoT Central 來遠端監視連網裝置。 Azure IoT Central 規則可讓您近乎即時地監視裝置，並讓您自動叫用動作 (例如，傳送電子郵件，或在符合規則條件時觸發 Microsoft Flow 中的工作流程)。 只要按幾下，就可以定義條件來監視裝置資料以及設定要叫用的動作。 本文會詳細說明事件監視規則。

Azure IoT Central 使用[事件量測](howto-set-up-template.md)來擷取裝置的資料。 每個量測類型都有可定義量測的關鍵屬性。 您可以建立規則來監視每一種裝置量測，並在觸發規則時產生警示。 當裝置報告所選裝置的事件時，就會觸發事件規則。

## <a name="create-an-event-rule"></a>建立事件規則

本節說明如何建立事件規則。 此範例使用會報告風扇馬達錯誤事件的冷飲販賣機裝置。 此規則會監視裝置所報告的事件，並在每次報告事件時傳送電子郵件。

1. 針對要新增規則的裝置，瀏覽至其裝置詳細資料頁面。

1. 如果您尚未建立任何規則，您會看到下列畫面：

    ![還沒有規則](media/howto-create-event-rules/image1.png)

1. 在 [規則] 索引標籤上，選擇 [+ 新增規則] 來查看可建立的規則類型。

    ![規則類型](media/howto-create-event-rules/image2.png)

1. 按一下 [事件] 來開啟表單以建立規則。

    ![事件規則](media/howto-create-event-rules/image3.png)

1. 選擇名稱來協助您識別此裝置範本中的規則。

1. 若要立即為透過此範本所建立的所有裝置啟用規則，請切換 [啟用規則]。

### <a name="configure-the-rule-condition"></a>設定規則條件

本節說明如何新增條件，以監視風扇馬達錯誤事件量測。

1. 選擇 [條件] 旁的 [+]。

1. 從下拉式清單中選擇想要監視的事件量測。 此範例選取了**風扇馬達錯誤**事件。

1. (選擇性) 如果您想要監視裝置所報告事件的特定值，您也可以提供該值。 例如，如果裝置報告具有不同錯誤碼的相同事件，則在規則條件中提供錯誤碼的值，將可確保只有在裝置以事件裝載的形式傳送該特定值時，才會觸發此規則。 讓此選項保持空白，即表示無論事件值為何，只要裝置傳送事件時就觸發此規則。

    ![新增事件條件](media/howto-create-event-rules/image4.png)

    > [!NOTE]
    > 在定義事件規則的條件時，必須至少選取一個事件量測。

1. 按一下 [儲存] 以儲存規則。 幾分鐘內，規則就會生效，並開始監視傳送至應用程式的事件。

### <a name="add-an-action"></a>新增動作

本節會說明如何在規則中新增動作。 這會說明如何新增電子郵件動作，但您也可以在規則中[新增 Microsoft Flow 動作](howto-add-microsoft-flow.md)，以在觸發規則時啟動 Microsoft Flow 中的工作流程。

> [!NOTE]
> 此時，一個規則只能有 1 個關聯動作。

1. 選擇 [動作] 旁的 [+]。 在此，您會看到可用動作的清單。

    ![新增動作](media/howto-create-event-rules/image5.png)

1. 選擇 [傳送電子郵件] 動作，在 [收件者] 欄位中輸入有效的電子郵件地址，然後提供當此規則觸發時要在電子郵件本文中出現的附註。

    > [!NOTE]
    > 只有已新增至應用程式且至少已登入一次的使用者才會收到電子郵件。 深入了解 Azure IoT Central 中的[使用者管理](howto-administer.md)。

   ![設定動作](media/howto-create-event-rules/image6.png)

1. 按一下 [檔案] 。 幾分鐘內，規則就會生效，並開始監視傳送至應用程式的事件。 當規則中指定的條件相符時，規則就會觸發所設定的電子郵件動作。

## <a name="parameterize-the-rule"></a>將規則參數化

您也可以使用 [裝置屬性] 將動作設定為參數。 如果將電子郵件地址儲存為裝置屬性，您就可以在定義 [收件者] 地址時加以使用。

## <a name="delete-a-rule"></a>刪除規則

如果不再需要某個規則，請開啟規則並選擇 [刪除] 來加以刪除。 規則在刪除後就會從裝置範本和所有相關聯的裝置中移除。

## <a name="enable-or-disable-a-rule-for-a-device-template"></a>啟用或停用裝置範本的規則

瀏覽至裝置，然後選擇您要啟用或停用的規則。 切換規則中的 [對此範本的所有裝置啟用規則] 按鈕，可對所有與裝置範本相關聯的裝置啟用或停用規則。

## <a name="enable-or-disable-a-rule-for-a-device"></a>啟用或停用裝置的規則

瀏覽至裝置，然後選擇您要啟用或停用的規則。 切換 [啟用此裝置的規則] 按鈕，可對該裝置啟用或停用規則。

## <a name="next-steps"></a>後續步驟

您現在已了解如何在 Azure IoT Central 應用程式中建立規則，以下是建議的後續步驟：

> [!div class="nextstepaction"]
> [如何在規則中新增 Microsoft Flow 動作 ](howto-add-microsoft-flow.md)
> [如何管理裝置](howto-manage-devices.md)。
