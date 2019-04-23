---
title: 在 Azure IoT Central 中自訂操作員的檢視 | Microsoft Docs
description: 身為建置者，您可以在 Azure IoT Central 應用程式中自訂操作員的檢視。
author: sandeeppujar
ms.author: sandeepu
ms.date: 03/26/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 7fc42b24090b5fc68176fea2c7b993643ea24200
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678219"
---
# <a name="tutorial-customize-the-azure-iot-central-operators-view"></a>教學課程：自訂 Azure IoT Central 操作員的檢視

本教學課程將為建置者說明如何自訂應用程式的操作員檢視。 當您以建置者的身分對應用程式進行變更時，您可以在 Microsoft Azure IoT Central 應用程式中預覽操作員的檢視。

在本教學課程中，您會自訂應用程式，以對操作員顯示與連線的空調裝置有關的資訊。 您的自訂將使操作員能夠管理已連線至應用程式的空調裝置。

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 設定裝置儀表板
> * 設定裝置設定版面配置
> * 設定裝置屬性版面配置
> * 以操作員的身分預覽裝置
> * 設定預設應用程式儀表板
> * 以操作員身分預覽預設應用程式儀表板

## <a name="prerequisites"></a>必要條件

在開始之前，您應先完成兩個先前的教學課程：

* [在 Azure IoT Central 應用程式中定義新的裝置類型](tutorial-define-device-type.md)。
* [為您的裝置設定規則和動作](tutorial-configure-rules.md)。

## <a name="configure-your-device-dashboard"></a>設定裝置儀表板

身為建置者，您可以定義要在裝置儀表板上顯示哪些資訊。 在[在應用程式中定義新裝置類型](tutorial-define-device-type.md)教學課程中，您已將折線圖和其他資訊新增至**連線的空調**儀表板。

1. 若要編輯**連線的空調**裝置範本，請選擇左側導覽功能表上的 [裝置範本]：

    ![裝置範本頁面](media/tutorial-customize-operator/devicetemplates.png)

2. 若要自訂您的裝置儀表板，請選取您從**在應用程式中定義新裝置類型**教學課程中建立的[連線的空調 (1.0.0)](tutorial-define-device-type.md) 裝置範本裝置。

3. 若要編輯儀表板，請選取 [儀表板] 索引標籤。

4. 若要將關鍵效能指標 (KPI) 圖格新增至儀表板，請選擇 [KPI]：

    若要定義 KPI，請使用下表中的資訊：

    | 設定     | 值 |
    | ----------- | ----- |
    | Name        | 最高溫度 |
    | 時間範圍  | 過去 1 星期 |
    | 測量類型 | 遙測 |
    | 測量 | 溫度 |
    | 彙總 | 最大值 |
    | 可見性  | 已啟用 |

    ![新增 KPI](media/tutorial-customize-operator/addkpi.png)

5. 選取 [ **儲存**]。 您現在可以在儀表板上檢視 KPI 圖格：

    ![KPI 圖格](media/tutorial-customize-operator/temperaturekpi.png)

6. 若要移動儀表板上的圖格或調整其大小，請將滑鼠指標移至圖格上方。 您可以將圖格拖曳至新位置，或調整其大小。

## <a name="configure-your-settings-layout"></a>設定「設定」版面配置

身為建置者，您也可以設定裝置設定的操作員檢視。 操作員可以使用裝置設定索引標籤來設定裝置。 例如，操作員可使用設定索引標籤來設定連線空調的目標溫度。

1. 若要為連線的空調編輯設定版面配置，請選擇 [設定] 索引標籤。

2. 您可以移動設定圖格以及調整其大小：

    ![編輯設定版面配置](media/tutorial-customize-operator/settingslayout.png)

## <a name="configure-your-properties-layout"></a>設定屬性版面配置

除了儀表板和設定以外，您也可以設定裝置屬性的操作員檢視。 操作員可以使用裝置屬性索引標籤來管理裝置中繼資料。 例如，操作員可使用屬性索引標籤來檢視裝置序號，或更新製造商的連絡詳細資料。

1. 若要為連線的空調編輯屬性版面配置，請選擇 [屬性] 索引標籤。

2. 您可以移動屬性欄位以及調整其大小：

    ![編輯屬性版面配置](media/tutorial-customize-operator/propertieslayout.png)

## <a name="preview-the-device"></a>預覽裝置

您會使用 [裝置範本] 頁面為操作員自訂儀表板、設定與屬性索引標籤。 您會使用 [Device Explorer] 頁面來檢視並使用裝置範本。

1. 若要以操作員身分檢視及使用已連線的空調範本，請瀏覽至 [Device Explorer] 頁面，並從範本中選擇 IoT Central 產生的模擬裝置：

    ![檢視並使用裝置範本](media/tutorial-customize-operator/usetemplate.png)

2. 若要更新此裝置的位置，請選擇 [屬性]，然後編輯 [位置] 圖格中的值。 然後選取 [儲存]：

    ![編輯屬性值](media/tutorial-customize-operator/editproperty.png)

3. 若要將設定傳送至連線的空調，請選擇 [設定]，並變更圖格中的設定值，然後選擇 [更新]：

    ![將設定傳送至裝置](media/tutorial-customize-operator/sendsetting.png)

    當裝置確認新的設定值時，該設定在圖格上會顯示為**已同步**。

4. 身為操作員，您可以檢視建置者所設定的裝置儀表板：

    ![裝置儀表板的操作員檢視](media/tutorial-customize-operator/operatordashboard.png)

## <a name="configure-the-default-dashboard"></a>設定預設儀表板

當建置者或操作員登入 Azure IoT Central 應用程式時，他們會看到應用程式儀表板。 身為建置者，您可以設定預設儀表板的內容，使其包含對操作員最有用和相關的內容。

> [!NOTE]
> 使用者也可以建立自己的個人儀表板，並選擇其中之一作為其預設值。

1. 若要自訂預設應用程式儀表板，請瀏覽到 [首頁]，然後選取頁面右上角的 [編輯]。 此時會出現一個面板，其中包含您可以新增至儀表板的物件程式庫。

    ![儀表板頁面](media/tutorial-customize-operator/builderhome.png)

2. 若要自訂儀表板，請從 [程式庫] 新增圖格。 選擇 [連結]，然後新增組織網站的詳細資料。 然後，選擇 [儲存]：

    ![將連結新增至儀表板](media/tutorial-customize-operator/addlink.png)

    > [!NOTE]
    > 您也可以將連結新增至 Azure IoT Central 應用程式內的頁面。 例如，您可以將連結新增至裝置儀表板或設定頁面。

3. (選擇性) 選擇 [影像]，並上傳要在儀表板上顯示的影像。 影像可以顯示在您加以選取時所將瀏覽到的 URL：

    ![將影像新增至儀表板](media/tutorial-customize-operator/addimage.png)

    若想深入了解，請參閱[如何準備及上傳影像至您的 Azure IoT Central 應用程式](howto-prepare-images.md)。

## <a name="preview-the-dashboard"></a>預覽儀表板

若要以操作員的身分預覽應用程式儀表板，請選取頁面右上方的 [完成]。

![切換設計模式](media/tutorial-customize-operator/operatorviewhome.png)

您可以選取連結和影像圖格，以瀏覽至您以建置者的身分設定的 URL。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何自訂應用程式的操作員檢視。

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * 設定裝置儀表板
> * 設定裝置設定版面配置
> * 設定裝置屬性版面配置
> * 以操作員的身分預覽裝置
> * 設定預設首頁
> * 以操作員的身分預覽預設首頁

現在，您已了解如何自訂應用程式的操作員檢視，建議的後續步驟是：

* [監視您的裝置 (以操作員身分)](tutorial-monitor-devices.md)
* [將裝置新增至應用程式 (以操作員和裝置開發人員的身分)](tutorial-add-device.md)