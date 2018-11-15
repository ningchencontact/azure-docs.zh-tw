---
title: 設定 Azure IoT Central 應用程式的首頁 | Microsoft Docs
description: 了解如何以建置者身分設定 Azure IoT Central 應用程式的首頁。
author: tbhagwat3
ms.author: tanmayb
ms.date: 07/10/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 8e79d05147cae5bf9c46fc1f7c0e7e98574383d5
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2018
ms.locfileid: "51007615"
---
## <a name="configuring-homepage"></a>設定首頁

首頁是當可存取應用程式的使用者瀏覽至應用程式的 URL 時所載入的頁面。 如果您在建立應用程式時選取「範例 Contoso」或「範例 Devkits」應用程式範本，您的應用程式將會有預先定義的首頁。 如果您選取了「自訂應用程式」應用程式範本，您的首頁將會空白。

例如，以下是以「範例 Contoso」範本為基礎的應用程式首頁。 若要自訂應用程式的首頁，請先選取右上方的 [編輯]。 

![以「範例 Contoso」範本為基礎的應用程式首頁](media\howto-configure-homepage\image1.png)

選取 [編輯] 會在左側面板中開啟儀表板程式庫。 可以新增許多類型的圖格和儀表板基元來自訂您的首頁。

![儀表板程式庫](media\howto-configure-homepage\image2.png)

例如，您可以新增 [設定和屬性] 圖格，以顯示目前選取的設定和屬性值。 若要這麼做，請先選取 [裝置範本]，然後選取 [裝置執行個體]。 然後，提供圖格標題並選取要顯示的 [設定] 或 [屬性]。 在此情況下，我們已選取 [設定溫度]。 按一下 [完成] 會導致此圖格出現在首頁上。

![含設定和屬性詳細資料的 [設定裝置詳細資料] 表單](media\howto-configure-homepage\image3.png)

現在，當操作員檢視首頁時，就會看到這個可顯示裝置屬性或設定的圖格：

![含針對圖格顯示之設定和屬性的 [儀表板] 索引標籤](media\howto-configure-homepage\image4.png)

試用程式庫中的各種其他圖格類型，探索如何進一步自訂您的應用程式首頁。

## <a name="next-steps"></a>後續步驟

既然您已了解如何設定 Azure IoT Central 首頁，您可以：

> [!div class="nextstepaction"]
> [了解如何準備及上傳映像](howto-prepare-images.md)
