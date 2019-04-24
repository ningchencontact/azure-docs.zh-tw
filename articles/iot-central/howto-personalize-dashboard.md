---
title: 建立 Azure IoT Central 的個人儀表板 |Microsoft Docs
description: 身為使用者，了解如何建立及管理您的個人儀表板。
author: dominicbetts
ms.author: dobett
ms.date: 02/13/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: fb74669dcaa802ad06a9c4dff3ffdf25726f518c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60518611"
---
# <a name="create-and-manage-personal-dashboards"></a>建立和管理個人儀表板

**儀表板**是您第一次瀏覽至您的應用程式時，載入的頁面。 A**產生器**應用程式中定義的所有使用者的預設應用程式儀表板。 您可以使用您的個人化的應用程式儀表板，來取代此預設儀表板。 您可以有數個儀表板，顯示不同的資料，並在它們之間切換。

## <a name="create-dashboard"></a>建立儀表板

下列螢幕擷取畫面顯示從建立應用程式中的儀表板**範例 Contoso**範本。 您可以使用專屬的儀表板，以取代預設的應用程式儀表板。 若要這樣做，請選取 **+ 新增**右上方的頁面。

![儀表板的 「 範例 Contoso"範本為基礎的應用程式](media/howto-personalize-dashboard/defaultdashboard.png)

選取 **+ 新增**，編輯器隨即開啟儀表板。 在編輯器中，您可以為您的儀表板命名，並從程式庫中選擇項目。 程式庫包含磚和儀表板基本類型，您可以使用來自訂儀表板。

![儀表板程式庫](media/howto-personalize-dashboard/dashboardeditor.png)

例如，您可以在其中加入**裝置設定與屬性**磚，以顯示其中一個您所管理之裝置的設定和屬性值。 若要這麼做，請先選取 [裝置範本]，然後選取 [裝置執行個體]。 然後為 [] 圖格，標題並選取**設定**或是**屬性**來顯示。 下列螢幕擷取畫面示**展開速度**選取要新增到 [] 圖格的設定。 選取 **完成**儀表板中儲存的變更。

![含設定和屬性詳細資料的 [設定裝置詳細資料] 表單](media/howto-personalize-dashboard/dashboardsetting.png)

現在當您檢視您的個人儀表板，您會看到新的圖格**展開速度**裝置設定：

![含針對圖格顯示之設定和屬性的 [儀表板] 索引標籤](media/howto-personalize-dashboard/personaldashboard.png)

您可以瀏覽其他 圖格類型程式庫中，以了解如何進一步自訂您的個人儀表板。

## <a name="manage-dashboards"></a>管理儀表板

您可以有數個個人儀表板和它們之間切換，或選擇預設的應用程式儀表板：

![切換儀表板](media/howto-personalize-dashboard/switchdashboards.png)

您可以編輯您的個人儀表板，並刪除您不再需要：

![刪除儀表板](media/howto-personalize-dashboard/managedashboards.png)

## <a name="next-steps"></a>後續步驟

既然您已了解如何建立和管理個人儀表板，您可以：

> [!div class="nextstepaction"]
> [了解如何管理您的應用程式喜好設定](howto-manage-preferences.md)
