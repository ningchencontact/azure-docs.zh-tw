---
title: 部署遠端監視解決方案架構 - Azure | Microsoft Docs
description: 本教學課程示範如何從 azureiotsuite.com 佈建遠端監視解決方案加速器。
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 05/01/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 8738fbfc29f6776a091cdc2b872eab88abd195cd
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2018
---
# <a name="deploy-the-remote-monitoring-solution-accelerator"></a>部署遠端監視解決方案加速器

本教學課程示範如何佈建遠端監視解決方案加速器。 您要從 azureiotsuite.com 部署解決方案。您也可以使用 CLI 部署解決方案，若要了解此選項，請參閱[從命令列部署解決方案加速器](iot-accelerators-remote-monitoring-deploy-cli.md)。

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 設定解決方案加速器
> * 部署解決方案加速器
> * 登入解決方案加速器

## <a name="prerequisites"></a>先決條件

若要完成此教學課程，您需要一個有效的 Azure 訂用帳戶。

如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資料，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/)。

## <a name="deploy-the-solution-accelerator"></a>部署解決方案加速器

您必須先選擇一些設定選項，才能將解決方案加速器部署到 Azure 訂用帳戶：

1. 使用您的 Azure 帳戶認證登入 [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators)。

1. 按一下 [遠端監視] 圖格上的 [立即試用]。

    ![選擇遠端監視](./media/iot-accelerators-remote-monitoring-deploy/remotemonitoring.png)

1. 在 [建立遠端監視解決方案] 頁面上，輸入遠端監視解決方案加速器的**解決方案名稱**。

1. 選取**基本**或**標準**部署。 如果您要部署解決方案來了解其運作方式或是執行示範，請選擇 [基本] 選項來將成本降至最低。

1. 選擇 **Java** 或 **.NET** 作為語言。 所有的微服務都可供作為 Java 或 .NET 的實作。

1. 如需有關設定選擇的詳細資訊，請檢閱 [解決方案詳細資料] 面板。

1. 選取您要用來佈建解決方案的 [訂用帳戶] 和 [區域]。

1. 按一下 [建立解決方案]  開始佈建程序。 此程序通常需要數分鐘的執行時間：

    ![遠端監視解決方案的詳細資料](./media/iot-accelerators-remote-monitoring-deploy/createform.png)

如需疑難排解資訊，請參閱 GitHub 存放庫中的[部署失敗時該怎麼辦](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide#what-to-do-when-a-deployment-fails)。

## <a name="sign-in-to-the-solution-accelerator"></a>登入解決方案加速器

完成佈建程序之後，您就能登入遠端監視解決方案加速器。

1. 在 [佈建解決方案] 頁面上，選擇新的遠端監視解決方案：

    ![選擇新的解決方案](./media/iot-accelerators-remote-monitoring-deploy/choosenew.png)

1. 您可以檢視有關所出現之面板中的遠端監視解決方案的資訊。 選擇 [解決方案儀表板] 以連線到遠端監視解決方案。

    > [!NOTE]
    > 當您完成時，可以從此面板刪除遠端監視解決方案。

    ![解決方案面板](./media/iot-accelerators-remote-monitoring-deploy/solutionpanel.png)

1. 遠端監視解決方案儀表板會顯示在您的瀏覽器中。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 設定解決方案加速器
> * 部署解決方案加速器
> * 登入解決方案加速器

既然您已部署遠端監視解決方案，下一步便是[探索解決方案儀表板的功能](iot-accelerators-remote-monitoring-explore.md)。

<!-- Next tutorials in the sequence -->