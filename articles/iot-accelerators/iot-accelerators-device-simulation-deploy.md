---
title: 部署裝置模擬解決方案 - Azure | Microsoft Docs
description: 本教學課程示範如何從 azureiotsuite.com 佈建裝置模擬解決方案。
author: troyhopwood
manager: ''
ms.author: troyhop
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 12/18/2017
ms.topic: conceptual
ms.openlocfilehash: e96c0f16ca4b69d103674b7f895c52d197280ca5
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/01/2018
ms.locfileid: "34627001"
---
# <a name="deploy-the-azure-iot-device-simulation-solution"></a>部署 Azure IoT 裝置模擬解決方案

本教學課程示範如何佈建裝置模擬解決方案。 您要從 azureiotsuite.com 部署解決方案。

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 設定裝置模擬解決方案
> * 部署裝置模擬解決方案
> * 登入裝置模擬解決方案

## <a name="prerequisites"></a>先決條件

若要完成此教學課程，您需要一個有效的 Azure 訂用帳戶。

如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資料，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/)。

## <a name="deploy-the-solution"></a>部署解決方案

您必須先選擇一些設定選項，才能將解決方案部署到 Azure 訂用帳戶：

1. 使用 Azure 帳戶認證登入 [azureiotsuite.com](https://www.azureiotsolutions.com)，然後按一下 **+** 以建立新的解決方案：

    ![建立新解決方案](./media/iot-accelerators-device-simulation-deploy/createnewsolution.png)

1. 按一下 [裝置模擬] 圖格上的 [選取]：

    ![選擇裝置模擬](./media/iot-accelerators-device-simulation-deploy/select.png)

1. 在 [建立裝置模擬解決方案] 頁面上，輸入裝置模擬解決方案的**解決方案名稱**。

1. 選取您要用來佈建解決方案的 [訂用帳戶] 和 [區域]。

1. 指定是否要隨裝置模擬解決方案部署新的 IoT 中樞。 如果您核取此方塊，系統便會在訂用帳戶中部署新的 IoT 中樞。 無論您做何選擇，都一律可將模擬指向任何 IoT 中樞。

1. 按一下 [建立解決方案]  開始佈建程序。 此程序通常需要數分鐘的執行時間：

    ![裝置模擬解決方案詳細資料](./media/iot-accelerators-device-simulation-deploy/createsolution.png)

## <a name="sign-in-to-the-solution"></a>登入解決方案

佈建程序完成時，您可以登入裝置模擬解決方案。

1. 在 [已佈建的解決方案] 頁面上，按一下新的裝置模擬解決方案底下的 [啟動]：

    ![選擇新的解決方案](./media/iot-accelerators-device-simulation-deploy/newsolution.png)

1. 您可以在出現的面板中檢視有關裝置模擬解決方案的資訊。 選擇 [解決方案儀表板] 以連線到裝置模擬解決方案。

    > [!NOTE]
    > 當您完成時，可以從此面板刪除裝置模擬解決方案。

    ![解決方案面板](./media/iot-accelerators-device-simulation-deploy/properties.png)

1. 裝置模擬解決方案儀表板會顯示在您的瀏覽器中。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 設定方案
> * 部署解決方案
> * 登入解決方案

既然您已部署裝置模擬解決方案，下一步便是[探索裝置模擬解決方案的功能](iot-accelerators-device-simulation-explore.md)。

<!-- Next tutorials in the sequence -->
