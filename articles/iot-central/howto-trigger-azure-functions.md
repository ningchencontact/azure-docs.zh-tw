---
title: 在 Azure IoT Central 中使用 Webhook 觸發 Azure Functions
description: 建立每次在 Azure IoT Central 中觸發規則時就會執行的函式應用程式。
author: viv-liu
ms.author: viviali
ms.date: 07/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 92d6f005018040e20c2df72dbc608a47bc8d9f08
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849013"
---
# <a name="trigger-azure-functions-using-webhooks-in-azure-iot-central"></a>在 Azure IoT Central 中使用 Webhook 觸發 Azure Functions

*本主題適用於建置員和系統管理員。*

使用 Azure Functions，在自 IoT Central 規則輸出的 Webhook 上執行無伺服器程式碼。 您不需要布建 VM 或發佈 web 應用程式以使用 Azure Functions, 但您可以改為執行此程式碼無伺服器。 您可以使用 Azure Functions 先轉換 Webhook 承載，再將之傳送到其最終目的地，例如 SQL 資料庫或事件方格。

## <a name="prerequisites"></a>必要條件

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="how-to-connect-azure-functions"></a>如何連接 Azure Functions

1. [在 Azure 入口網站中建立新的函數應用程式](https://ms.portal.azure.com/#create/Microsoft.FunctionApp)。

    ![在 Azure 入口網站中建立新的函數應用程式](media/howto-trigger-azure-functions/createfunction.png)

2. 展開函式應用程式, 然後選取 [函數] 旁的 [ **+] 按鈕**。 如果此函式是函數應用程式中的第一個函式, 請**在入口網站中**選取作為開發環境, 然後選取 [**繼續**]。

    ![在函式應用程式中選擇自訂函式](media/howto-trigger-azure-functions/customfunction.png)

3. 選擇 [ **Webhook + API**範本], 然後選取 [**建立**]。 本主題使用以 .NET 為基礎的 Azure function。

    ![選取泛型 Webhook 觸發程序](media/howto-trigger-azure-functions/genericwebhooktrigger.png)

4. 在您的新函式中, 選取 [ **</> 取得函數 URL**], 然後複製並儲存值。 您可以使用此值來設定 Webhook。

    ![取得函式的 URL](media/howto-trigger-azure-functions/getfunctionurl.png)

4. 在 IoT Central 中，瀏覽至您想要連接到您函式應用程式的規則。

5. 新增 Webhook 動作。 在 [顯示名稱]  中輸入名稱，然後將您先前複製的函式 URL 貼到 [回呼 URL]  。

    ![在回呼 URL 欄位中輸入函式 URL](media/howto-trigger-azure-functions/configurewebhook.PNG)

6. 儲存規則。 現在, 當觸發規則時, webhook 會叫用函式應用程式來執行。 在您的函數應用程式中, 您可以選取 [**監視**] 以查看函式的調用歷程記錄。 您可以使用 App Insights 或傳統檢視查看歷程記錄。

    ![監視函式的引動過程歷程記錄](media/howto-trigger-azure-functions/monitorfunction.PNG)

如需詳細資訊，請參閱與[建立由泛型 Webhook 觸發的函式](https://docs.microsoft.com/azure/azure-functions/functions-create-generic-webhook-triggered-function)相關的 Azure Functions 文章。

## <a name="next-steps"></a>後續步驟
您現在已了解如何設定和使用 Webhook，建議的後續步驟為瀏覽[在 Microsoft Flow 中建置工作流程](howto-add-microsoft-flow.md)。
