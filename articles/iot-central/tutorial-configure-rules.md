---
title: 在 Azure IoT Central 中設定規則和動作 | Microsoft Docs
description: 本教學課程將為建置者說明如何在 Azure IoT Central 應用程式中設定以遙測為基礎的規則和動作。
author: ankitgupta
ms.author: ankitgup
ms.date: 04/16/2018
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: af2aa8d7b01d973da400808fd3e97d0739693cd2
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2018
ms.locfileid: "35236326"
---
# <a name="tutorial-configure-rules-and-actions-for-your-device-in-azure-iot-central"></a>教學課程：在 Azure IoT 中心為您的裝置設定規則和動作

本教學課程將為身為建置者的您說明如何在 Microsoft Azure IoT Central 應用程式中設定以遙測為基礎的規則和動作。

在本教學課程中，您會建立在連線的空調裝置溫度超過 90&deg; F 時傳送電子郵件的規則。

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 建立以遙測為基礎的規則
> * 新增動作

## <a name="prerequisites"></a>先決條件

在開始之前，您應先完成[在應用程式中定義新的裝置類型](tutorial-define-device-type.md)教學課程，以建立要搭配使用的**連線的空調**裝置範本。

## <a name="create-a-telemetry-based-rule"></a>建立以遙測為基礎的規則

1. 若要將以遙測為基礎的新規則新增至應用程式，請在左側導覽功能表中選擇 [Device Explorer]：

    ![Device Explorer 工具](media/tutorial-configure-rules/explorerpage.png)

    您會看到**連線的空調 (1.0.0)** 裝置範本，以及您在上一個教學課程中建立的**連線的空調-1** 裝置。

2. 若要開始自訂連線的空調裝置，請選擇您在上一個教學課程中建立的裝置：

    ![連線的空調頁面](media/tutorial-configure-rules/builderdevicelist.png)

3. 若要開始在 [規則] 檢視中新增規則，請選擇 [規則]：

    ![規則檢視](media/tutorial-configure-rules/builderrulesview.png)

4. 若要開始建立以閾值為基礎的遙測規則，請選擇 [新增規則]，然後選擇 [遙測]。

5. 若要定義規則，請使用下表中的資訊：

    | 設定     | 值                          |
    | ----------- | ------------------------------ |
    | Name        | 空調溫度    |
    | 啟用規則 | 另一                             |
    | 條件   | 溫度大於 90 度 |

    ![溫度規則條件](media/tutorial-configure-rules/buildertemperaturerule.png)

## <a name="add-an-action"></a>新增動作

在定義規則時，您也須定義在符合規則條件時所要執行的動作。 在本教學課程中，您所新增的動作將會傳送規則所觸發的電子郵件通知。

1. 若要新增 [動作]，請在 [設定遙測規則] 面板中向下捲動，並選擇 [動作] 旁的 **+**，然後選擇 [電子郵件]：

    ![溫度規則動作](media/tutorial-configure-rules/builderaddaction.png)

2. 若要定義動作，請使用下表中的資訊：

    | 設定   | 值                          |
    | --------- | ------------------------------ |
    | 至        | 您的電子郵件地址             |
    | 注意     | 空調溫度已超過閾值。 |

    > [!NOTE]
    > 若要接收電子郵件通知，電子郵件地址必須是[應用程式中的使用者識別碼](howto-administer.md)，且該使用者必須已至少登入應用程式一次。

    ![應用程式建置者溫度動作](media/tutorial-configure-rules/buildertemperatureaction.png)

3. 選擇 [儲存]。 您的規則會列在 [規則] 頁面上：

    ![應用程式建置者規則](media/tutorial-configure-rules/builderrules.png)

## <a name="test-the-rule"></a>測試規則

規則在您儲存之後，不久即會生效。 符合規則中定義的條件時，應用程式即會傳送訊息至您在動作中指定的電子郵件地址。

![電子郵件動作](media/tutorial-configure-rules/email.png)

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * 建立以遙測為基礎的規則
> * 新增動作

現在，您已定義以閾值為基礎的規則，建議您接下來應[自訂操作員的檢視](tutorial-customize-operator.md)。

若要深入了解 Azure IoT Central 中不同類型的規則，以及如何將規則定義參數化，請參閱：
* [建立遙測規則並設定通知](howto-create-telemetry-rules.md)。
* [建立事件規則並設定通知](howto-create-event-rules.md)。

<!-- Next tutorials in the sequence -->
