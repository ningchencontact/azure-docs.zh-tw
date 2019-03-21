---
title: 使用模擬 （mock） 的資料-Azure Logic Apps 的測試邏輯應用程式
description: 設定靜態的結果，而不會影響生產環境測試邏輯應用程式使用模擬 （mock） 的資料
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.topic: article
ms.date: 03/18/2019
ms.openlocfilehash: 0fbe56ceeeba71bcbb5ef358cd66de15e36508fc
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "58165091"
---
# <a name="test-logic-apps-with-mock-data-by-setting-up-static-results"></a>設定靜態的結果，以測試邏輯應用程式使用模擬 （mock） 的資料

測試邏輯應用程式，您可能無法準備好實際呼叫，或因為各種原因而存取的應用程式、 服務和系統。 通常在這些情況下，您可能必須執行不同的條件路徑，強制錯誤、 提供特定的訊息回應內文，或嘗試略過一些步驟。 藉由設定靜態的結果，邏輯應用程式中的動作，您可以模擬該動作的輸出資料。 啟用靜態動作的結果不會執行動作，但改為傳回的模擬 （mock） 的資料。

比方說，如果您設定靜態的結果，適用於 Outlook 365 傳送郵件的動作，Logic Apps 引擎只會傳回模擬 （mock） 的資料，您指定為靜態的結果，而不是呼叫 Outlook 和傳送電子郵件。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先<a href="https://azure.microsoft.com/free/" target="_blank">註冊免費的 Azure 帳戶</a>。

* [如何建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知識

* 邏輯應用程式，您要設定靜態的結果

<a name="set-up-static-results"></a>

## <a name="set-up-static-results"></a>設定靜態的結果

1. 如果您還沒有這麼做，在[Azure 入口網站](https://portal.azure.com)，在 Logic Apps 設計工具中開啟邏輯應用程式。

1. 您要設定靜態的結果時的動作，請遵循下列步驟： 

   1. 在動作的右上角，選擇省略符號 (*...*) 按鈕，然後選取**靜態結果**，例如：

      ![選取 「 靜態的結果 」 > 「 啟用靜態結果 」](./media/test-logic-apps-mock-data-static-results/select-static-result.png)

   1. 選擇**啟用靜態結果**。 針對必要 （*） 屬性，指定您想要傳回動作的回應的模擬 （mock） 的輸出值。

      例如，以下是 HTTP 動作的必要的屬性：

      | 屬性 | 描述 |
      |----------|-------------|
      | **狀態** | 要傳回動作的狀態 |
      | **狀態碼** | 要傳回的特定狀態碼 |
      | **標頭** | 可傳回的標頭內容 |
      |||

      ![選取 [啟用靜態的結果]](./media/test-logic-apps-mock-data-static-results/enable-static-result.png)

      若要以 JavaScript Object Notation (JSON) 格式輸入模擬 （mock） 的資料，請選擇**切換至 JSON 模式**(![選擇 「 切換到 JSON 的模式 」](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png))。

   1. 選擇性屬性，開啟**選取選擇性欄位**清單，然後選取您想要模擬的屬性。

      ![選取 選擇性的屬性](./media/test-logic-apps-mock-data-static-results/optional-properties.png)

1. 當您準備好儲存時時，請選擇**完成**。

   在動作的右上角的標題列中現在會顯示測試的小燒杯圖示 (![靜態結果圖示](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png))，這表示您已啟用靜態的結果。

   ![圖示顯示啟用靜態的結果](./media/test-logic-apps-mock-data-static-results/static-results-enabled.png)

   若要尋找前一個執行中使用模擬 （mock） 的資料，請參閱[尋找執行中使用靜態的結果](#find-runs-mock-data)本主題稍後的。

<a name="reuse-sample-outputs"></a>

## <a name="reuse-previous-outputs"></a>重複使用先前的輸出

如果您的邏輯應用程式有先前執行具有輸出，您可以重複使用為模擬 （mock） 的輸出，您可以複製並貼執行的輸出。

1. 如果您還沒有這麼做，在[Azure 入口網站](https://portal.azure.com)，在 Logic Apps 設計工具中開啟邏輯應用程式。

1. 邏輯應用程式的主功能表上，選取**概觀**。

1. 在 **執行歷程記錄**區段中，選取想要執行您的邏輯應用程式。

1. 在邏輯應用程式的工作流程中，尋找並展開您想要輸出的動作。

1. 選擇**顯示原始輸出**連結。

1. 複製完整的 JavaScript Object Notation (JSON) 物件或您想要使用，例如特定的子區段中，輸出 區段中或甚至只是標頭 區段。

1. 請依照下列步驟開啟**靜態結果**為您的動作中的方塊[設定靜態的結果](#set-up-static-results)。

1. 在後**靜態結果**方塊隨即開啟，選擇其中一個步驟：

   * 若要貼上完整的 JSON 物件，請選擇**切換至 JSON 模式**(![選擇 「 切換到 JSON 的模式 」](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)):

     ![選擇 「 切換到 JSON 的模式 」 針對完整的物件](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-complete.png)

   * 若要貼上剛才 JSON 區段，該區段的標籤旁選擇**切換至 JSON 模式**該區段，例如：

     ![選擇 「 切換到 JSON 的模式 」 的輸出，](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-outputs.png)

1. 在 JSON 編輯器中，貼上您先前複製的 JSON。

   ![JSON 模式](./media/test-logic-apps-mock-data-static-results/json-editing-mode.png)

1. 完成之後，選擇 [完成]。 或者，若要返回設計工具，選擇**切換編輯器模式**(![選擇 [切換編輯器模式]](./media/test-logic-apps-mock-data-static-results/switch-editor-mode-button.png))。

<a name="find-runs-mock-data"></a>

## <a name="find-runs-that-use-static-results"></a>尋找執行中使用靜態的結果

邏輯應用程式的執行歷程記錄會識別其中的動作會使用靜態的結果執行。 若要尋找這些執行，請遵循下列步驟：

1. 邏輯應用程式的主功能表上，選取**概觀**。 

1. 在右窗格中，在**執行歷程記錄**，尋找**靜態結果**資料行。 

   包含動作結果的任何執行都**靜態結果**資料行設定為**已啟用**，例如：

   ![執行歷程記錄-靜態的結果資料行](./media/test-logic-apps-mock-data-static-results/run-history.png)

1. 若要檢視使用靜態的結果的動作，選取您想要的位置執行**靜態結果**資料行會設定為**已啟用**。

   使用靜態的結果的動作會顯示測試的小燒杯 (![靜態結果圖示](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)) 圖示，例如：

   ![執行歷程記錄-使用靜態的結果的動作](./media/test-logic-apps-mock-data-static-results/static-results-enabled-run-details.png)

## <a name="disable-static-results"></a>停用靜態的結果

關閉 靜態的結果不會擲出的值從最後一個安裝程式。 因此，當您開啟靜態的結果在下一次，您可以繼續使用您先前的值。

1. 您要停用靜態輸出之尋找動作。 在動作的右上角，選擇 測試的小燒杯圖示 (![靜態結果圖示](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png))。

   ![停用靜態的結果](./media/test-logic-apps-mock-data-static-results/disable-static-results.png)

1. 選擇**停用靜態 Result** > **完成**。

   ![停用靜態的結果](./media/test-logic-apps-mock-data-static-results/disable-static-results-button.png)

## <a name="get-support"></a>取得支援

* 如有問題，請瀏覽 [Azure Logic Apps 論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。

* 若要提交或票選功能構想，請造訪 [Logic Apps 使用者意見反應網站](https://aka.ms/logicapps-wish)。

## <a name="next-steps"></a>後續步驟

* 深入了解[Azure Logic Apps](../logic-apps/logic-apps-overview.md)