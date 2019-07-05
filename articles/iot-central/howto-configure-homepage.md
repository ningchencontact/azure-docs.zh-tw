---
title: 設定 Azure IoT Central 應用程式儀表板 |Microsoft Docs
description: 產生器中，以了解如何設定預設 Azure IoT Central 應用程式儀表板。
author: dominicbetts
ms.author: dobett
ms.date: 02/13/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: e6947a4f15797028274d49069d9e2787b143860d
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503220"
---
# <a name="configure-the-application-dashboard"></a>設定應用程式儀表板

**儀表板**頁面載入時，可存取應用程式的使用者瀏覽至應用程式的 URL。 如果您選取其中一個**範例 Contoso**或是**範例 Devkits**應用程式範本來建立您的應用程式，您的應用程式已預先定義的儀表板。 如果您選擇**自訂應用程式**應用程式範本，您的儀表板是空白。

> [!NOTE]
> 使用者也可以[建立自己專屬的儀表板](howto-personalize-dashboard.md)使用而不是預設應用程式儀表板。

## <a name="add-tiles"></a>新增圖格

下列螢幕擷取畫面顯示從建立應用程式中的儀表板**範例 Contoso**範本。 若要自訂您的應用程式的預設儀表板，請選取**編輯**上方的頁面。

![儀表板的 「 範例 Contoso"範本為基礎的應用程式](media/howto-configure-homepage/image1a.png)

選取**編輯**，開啟 [儀表板程式庫] 面板。 程式庫包含磚和儀表板基本類型，您可以使用來自訂儀表板。

![儀表板程式庫](media/howto-configure-homepage/image2a.png)

例如，您可以在其中加入**裝置設定與屬性**磚，以顯示選取的裝置目前的設定和屬性值。 若要這麼做，請先選取 [裝置範本]  ，然後選取 [裝置執行個體]  。 然後，提供圖格標題並選取要顯示的 [設定]  或 [屬性]  。 下列螢幕擷取畫面顯示設定，以及選取要新增到 [] 圖格的屬性。 選取 **完成**儀表板中儲存的變更。

![含設定和屬性詳細資料的 [設定裝置詳細資料] 表單](media/howto-configure-homepage/image3a.png)

現在當運算子檢視預設應用程式儀表板，他們會看到包含新的磚**設定的溫度**裝置設定：

![含針對圖格顯示之設定和屬性的 [儀表板] 索引標籤](media/howto-configure-homepage/image4a.png)

您可以瀏覽其他 圖格類型程式庫中，以了解如何進一步自訂預設的應用程式儀表板。

若要深入了解如何使用 Azure IoT Central 中的磚，請參閱[使用儀表板磚](howto-use-tiles.md)。

## <a name="next-steps"></a>後續步驟

既然您已了解如何設定 Azure IoT Central 預設應用程式儀表板，您可以：

> [!div class="nextstepaction"]
> [了解如何準備及上傳映像](howto-prepare-images.md)
