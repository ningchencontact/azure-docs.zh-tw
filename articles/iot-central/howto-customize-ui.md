---
title: 自訂 Azure IoT Central UI |Microsoft Docs
description: 如何自訂您的 Azure IoT central 應用程式的佈景主題和說明連結
author: dominicbetts
ms.author: dobett
ms.date: 04/25/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 4d385f1e8c883453b4153ca4c9119d3be0a608bb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66495568"
---
# <a name="customize-the-azure-iot-central-ui"></a>自訂 Azure IoT 中心 UI 

*這篇文章適用於系統管理員。*

IoT 中心可讓您套用自訂佈景主題，並修改為指向您自己的自訂說明資源的說明連結來自訂您的應用程式的 UI。 下列螢幕擷取畫面顯示使用標準的佈景主題的頁面：

![標準的 IoT 中心主題](./media/howto-customize-ui/standard-ui.png)

下列螢幕擷取畫面顯示反白顯示的自訂 UI 項目搭配使用自訂的螢幕擷取畫面的頁面：

![自訂的 IoT 中心主題](./media/howto-customize-ui/themed-ui.png)

## <a name="create-theme"></a>建立佈景主題

若要建立自訂佈景主題，瀏覽至**來自訂您的應用程式**頁面**管理**區段：

![IoT Central 佈景主題](./media/howto-customize-ui/themes.png)

在此頁面上，您可以自訂您的應用程式的下列層面：

### <a name="application-logo"></a>應用程式標誌

PNG 影像，不能大於 1 MB，具有透明背景。 IoT Central 應用程式標題列左方會顯示此標誌。

如果您的標誌映像包含應用程式的名稱，您可以隱藏的應用程式名稱的文字。 如需詳細資訊，請參閱 <<c0> [ 管理應用程式設定](./howto-administer.md#manage-application-settings)。

### <a name="browser-icon-favicon"></a>瀏覽器圖示 (favicon)

PNG 影像，不能大於 32 x 32 像素，具有透明背景。 網頁瀏覽器可以使用此映像，在網址列、 歷程記錄、 書籤，以及瀏覽器索引標籤。

### <a name="browser-colors"></a>瀏覽器色彩

您可以變更頁面標題的色彩和用於區分腔調音按鈕及其他的反白顯示的色彩。 使用六個字元的十六進位色彩值，格式`##ff6347`。 如需詳細資訊**十六進位值**色彩標記法，請參閱[HTML 色彩](https://www.w3schools.com/html/html_colors.asp)。

> [!NOTE]
> 您可以隨時還原回預設的選項上**來自訂您的應用程式**頁面。

### <a name="changes-for-operators"></a>運算子的變更

如果系統管理員建立自訂佈景主題，則運算子和其他應用程式的使用者不再可以選擇在佈景主題**設定**。

## <a name="replace-help-links"></a>取代 [說明] 連結

自訂說明資訊提供給您的運算子和其他使用者，您可以修改應用程式上的連結**協助**功能表。

若要修改的說明連結，瀏覽至**自訂 [說明]** 頁面**管理**區段：

![自訂 IoT Central 說明連結](./media/howto-customize-ui/help-links.png)

您也可以將新的項目新增至 [說明] 功能表，並移除預設項目：

![自訂的 IoT Central 說明](./media/howto-customize-ui/custom-help.png)

> [!NOTE]
> 您可以隨時還原回預設說明連結上**自訂 [說明]** 頁面。

## <a name="next-steps"></a>後續步驟

既然您已了解如何自訂您的 IoT Central 應用程式中的 UI，以下是一些建議的後續步驟：

- [管理您的應用程式](./howto-administer.md)
- [設定應用程式儀表板](./howto-configure-homepage.md)