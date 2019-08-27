---
title: 在 Azure IoT Central 中設定規則和動作 | Microsoft Docs
description: 本教學課程將為建置者說明如何在 Azure IoT Central 應用程式中設定以遙測為基礎的規則和動作。
author: dominicbetts
ms.author: dobett
ms.date: 08/06/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: c2aa6edfe7ce9b670ea1015e2da72f3ecc48c9ec
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878829"
---
# <a name="tutorial-configure-rules-and-actions-for-your-device-in-azure-iot-central-preview-features"></a>教學課程：在 Azure IoT Central 中為您的裝置設定規則和動作 (預覽功能)

*本文適用於操作員、建置員及系統管理員。*

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

在本教學課程中，您會建立在環境感應器裝置溫度超過 90&deg; F 時傳送電子郵件的規則。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立以遙測為基礎的規則
> * 新增動作

## <a name="prerequisites"></a>必要條件

在開始之前，您應先完成[在應用程式中定義新的裝置類型](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)教學課程，以建立要搭配使用的**環境感應器**裝置範本。

## <a name="create-a-telemetry-based-rule"></a>建立以遙測為基礎的規則

1. 若要將以遙測為基礎的新規則新增至應用程式，請在左側導覽功能表中選取 [規則]  。

1. 若要建立新的規則，請選取 [+ 新增]  。 然後，選擇 [遙測]  。

1. 輸入**環境溫度**作為規則名稱。

1. 在 [範圍]  區段中，選取 [環境感應器]  作為裝置範本。 套用此規則的裝置範圍。 您可以使用 [+ 篩選]  新增更多篩選準則。

1. 在 [條件]  區段中，您可以定義規則所觸發的內容。 使用下列資訊來定義以溫度遙測為基礎的條件：

    | 欄位                                        | 值                             |
    | -------------------------------------------- | ------------------------------    |
    | 測量                                  | 溫度                       |
    | 運算子                                     | 大於                   |
    | 值                                        | 90                                |

    若要新增更多條件，請選取 [+ 條件]  。

    ![建立規則條件](./media/tutorial-configure-rules-pnp/condition.png)

1. 若要新增要在規則觸發時所執行的動作，請選取 [+ 動作]  ，然後選擇 [電子郵件]  。

1. 請使用下表中的資訊來定義您的動作：

    | 設定   | 值                                             |
    | --------- | ------------------------------------------------- |
    | 至        | 您的電子郵件地址                                |
    | 注意     | 環境溫度已超出臨界值。 |

    > [!NOTE]
    > 若要接收電子郵件通知，電子郵件地址必須是[應用程式中的使用者識別碼](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)，且該使用者必須已至少登入應用程式一次。

    ![建立規則動作](./media/tutorial-configure-rules-pnp/action.png)

1. 選取 [ **儲存**]。 您的規則會列在 [規則]  頁面上。

## <a name="test-the-rule"></a>測試規則

規則在您儲存之後，不久即會生效。 符合規則中定義的條件時，應用程式即會傳送訊息至您在動作中指定的電子郵件地址。

> [!NOTE]
> 測試完成後，請關閉規則以停止在收件匣中接收警示。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

* 建立以遙測為基礎的規則
* 新增動作

現在，您已定義以臨界值為基礎的規則，建議執行的下一個步驟是：

> [!div class="nextstepaction"]
> [建立事件規則並設定通知](howto-create-event-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)。
