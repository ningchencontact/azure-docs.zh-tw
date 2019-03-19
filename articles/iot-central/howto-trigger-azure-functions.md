---
title: 在 Azure IoT Central 中使用 Webhook 觸發 Azure Functions
description: 建立每次在 Azure IoT Central 中觸發規則時就會執行的函式應用程式。
author: viv-liu
ms.author: viviali
ms.date: 02/20/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 512956d2de0f9a838cc6378345a334e489d1d120
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/04/2019
ms.locfileid: "57306862"
---
# <a name="trigger-azure-functions-using-webhooks-in-azure-iot-central"></a>在 Azure IoT Central 中使用 Webhook 觸發 Azure Functions

*本主題適用於建置員和系統管理員。*

使用 Azure Functions，在自 IoT Central 規則輸出的 Webhook 上執行無伺服器程式碼。 您不需要佈建 VM 或發佈 Web 應用程式，就能使用 Azure Functions，但您可以改為以無伺服器的方式執行此程式碼。 您可以使用 Azure Functions 先轉換 Webhook 承載，再將之傳送到其最終目的地，例如 SQL 資料庫或事件方格。

## <a name="prerequisites"></a>必要條件

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="how-to-connect-azure-functions"></a>如何連接 Azure Functions

1. [在 Azure 入口網站中建立新的函式應用程式](https://ms.portal.azure.com/#create/Microsoft.FunctionApp)。

    ![在 Azure 入口網站中建立新的函式應用程式](media/howto-trigger-azure-functions/createfunction.png)

2. 展開函式應用程式，然後選取 **+ 按鈕**旁邊函式。 如果這個函式是您函式應用程式中的第一個函式，請選取 [自訂函式]。 這會顯示一組完整的函式範本。

    ![在函式應用程式中選擇自訂函式](media/howto-trigger-azure-functions/customfunction.png)

3. 在 [搜尋] 欄位中，輸入 **"generic"**，然後選擇您需要的泛型 Webhook 觸發程序範本語言。 本主題是使用 C# 函式。 

    ![選取泛型 Webhook 觸發程序](media/howto-trigger-azure-functions/genericwebhooktrigger.png)

4. 在新的函數中，選取 **<> / Get 函式 URL**，然後複製並儲存值。 您可以使用此值來設定 Webhook。

    ![取得函式的 URL](media/howto-trigger-azure-functions/getfunctionurl.png)

4. 在 IoT Central 中，瀏覽至您想要連接到您函式應用程式的規則。

5. 新增 Webhook 動作。 在 [顯示名稱] 中輸入名稱，然後將您先前複製的函式 URL 貼到 [回呼 URL]。

    ![在回呼 URL 欄位中輸入函式 URL](media/howto-trigger-azure-functions/configurewebhook.PNG)

6. 儲存規則。 立即觸發規則時，webhook 會叫用函式應用程式執行。 在您的函式應用程式，您可以選取**監視器**以查看函式的引動過程的歷程記錄。 您可以使用 App Insights 或傳統檢視查看歷程記錄。

    ![監視函式的引動過程歷程記錄](media/howto-trigger-azure-functions/monitorfunction.PNG)

如需詳細資訊，請參閱與[建立由泛型 Webhook 觸發的函式](https://docs.microsoft.com/azure/azure-functions/functions-create-generic-webhook-triggered-function)相關的 Azure Functions 文章。

## <a name="next-steps"></a>後續步驟
您現在已了解如何設定和使用 Webhook，建議的後續步驟為瀏覽[在 Microsoft Flow 中建置工作流程](howto-add-microsoft-flow.md)。
