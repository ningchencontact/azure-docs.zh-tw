---
title: "部署 Azure 裝置模擬方案 |Microsoft 文件"
description: "本教學課程會示範如何佈建裝置模擬從方案 azureiotsuite.com。"
services: iot device simulation
suite: iot-suite
author: troyhopwood
manager: timlt
ms.author: troyhop
ms.service: iot-suite
ms.date: 12/18/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: da9fb95ed5d3387c98c3274a53769d3f5f945371
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/20/2017
---
# <a name="deploy-the-azure-iot-device-simulation-solution"></a>部署 Azure IoT 裝置模擬方案

本教學課程會示範如何佈建裝置模擬方案。 您要從 azureiotsuite.com 部署解決方案。

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 設定裝置模擬解決方案
> * 裝置模擬方案部署
> * 登入裝置模擬方案

## <a name="prerequisites"></a>必要條件

若要完成此教學課程，您需要一個有效的 Azure 訂用帳戶。

如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資料，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/)。

## <a name="deploy-the-solution"></a>部署解決方案

您將方案部署到您的 Azure 訂閱之前，您必須選擇一些組態選項：

1. 登入[azureiotsuite.com](https://www.azureiotsuite.com)使用您的 Azure 帳戶的認證，再按一下 **+** 來建立新的方案：

    ![建立新解決方案](media/iot-suite-device-simulation-deploy/createnewsolution.png)

1. 按一下**選取**上**裝置模擬**磚：

    ![選擇裝置模擬](media/iot-suite-device-simulation-deploy/select.png)

1. 在**建立裝置模擬解決方案**頁面上，輸入**方案名稱**裝置模擬方案。

1. 選取您要用來佈建解決方案的 [訂用帳戶] 和 [區域]。

1. 指定是否要與裝置模擬解決方案部署新的 IoT 中樞。 如果您核取此方塊時，新的 IoT 中樞被部署到您的訂用帳戶。 無論選擇，您可以一律指向您的模擬任何 IoT 中樞。

1. 按一下 [建立解決方案]  開始佈建程序。 此程序通常需要數分鐘的執行時間：

    ![裝置模擬方案詳細資料](media/iot-suite-device-simulation-deploy/createsolution.png)

## <a name="sign-in-to-the-solution"></a>登入方案

佈建程序完成時，您可以登入您的裝置模擬方案。

1. 在**佈建解決方案**頁面上，按一下**啟動**下新的裝置模擬方案：

    ![選擇新的解決方案](media/iot-suite-device-simulation-deploy/newsolution.png)

1. 您可以檢視裝置模擬方案中的面板會顯示的資訊。 選擇**方案儀表板**連接裝置模擬解決方案。

    > [!NOTE]
    > 當您完成時，您可以從這個面板刪除裝置模擬方案。

    ![解決方案面板](media/iot-suite-device-simulation-deploy/properties.png)

1. 裝置模擬方案儀表板會顯示您的瀏覽器中。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 設定方案
> * 部署解決方案
> * 登入方案

既然您已部署的裝置模擬解決方案，下一個步驟是[探索裝置模擬解決方案的功能](./iot-suite-device-simulation-explore.md)。

<!-- Next tutorials in the sequence -->
