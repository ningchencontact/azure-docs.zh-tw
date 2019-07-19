---
title: 設定 Azure IoT Central 應用程式儀表板 |Microsoft Docs
description: 作為建立者, 瞭解如何設定預設的 Azure IoT Central 應用程式儀表板。
author: dominicbetts
ms.author: dobett
ms.date: 07/10/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 94ad51ac11687dfe060176132e2030d61b8d4ffc
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850205"
---
# <a name="configure-the-application-dashboard"></a>設定應用程式儀表板

當具有應用程式存取權的使用者流覽至應用程式的 URL 時,**儀表板**就是載入的頁面。 如果您選取了**範例 Contoso**或**範例 Devkits**應用程式範本來建立您的應用程式, 您的應用程式就會有預先定義的儀表板。 如果您選擇**自訂應用**程式範本, 您的儀表板會是空白的。

> [!NOTE]
> 使用者也可以[建立自己的個人儀表板](howto-personalize-dashboard.md)來使用, 而不是預設的應用程式儀表板。

## <a name="add-tiles"></a>新增圖格

下列螢幕擷取畫面顯示從**範例 Contoso**範本建立之應用程式中的儀表板。 若要自訂應用程式的預設儀表板, 請選取頁面右上方的 [**編輯**]。

![以「範例 Contoso」範本為基礎的應用程式儀表板](media/howto-configure-homepage/image1a.png)

選取 [**編輯**], 開啟 [儀表板程式庫] 面板。 程式庫包含您可以用來自訂儀表板的圖格和儀表板基本專案。

![儀表板程式庫](media/howto-configure-homepage/image2a.png)

例如, 您可以新增 [**裝置設定] 和 [屬性**] 磚, 以顯示裝置的目前設定和屬性值的選取專案。 若要這麼做，請先選取 [裝置範本]  ，然後選取 [裝置執行個體]  。 然後，提供圖格標題並選取要顯示的 [設定]  或 [屬性]  。 下列螢幕擷取畫面顯示已選取要新增至磚的設定和屬性。 選取 [**完成**] 以將變更儲存到儀表板。

![含設定和屬性詳細資料的 [設定裝置詳細資料] 表單](media/howto-configure-homepage/image3a.png)

現在, 當操作員查看預設應用程式儀表板時, 他們會看到新的磚, 其中包含裝置的 [**設定溫度**] 設定:

![含針對圖格顯示之設定和屬性的 [儀表板] 索引標籤](media/howto-configure-homepage/image4a.png)

您可以流覽程式庫中的其他磚類型, 以探索如何進一步自訂預設應用程式儀表板。

若要深入瞭解如何在 Azure IoT Central 中使用磚, 請參閱[使用儀表板磚](howto-use-tiles.md)。

## <a name="next-steps"></a>後續步驟

既然您已瞭解如何設定 Azure IoT Central 預設應用程式儀表板, 您可以:

> [!div class="nextstepaction"]
> [了解如何準備及上傳映像](howto-prepare-images.md)
