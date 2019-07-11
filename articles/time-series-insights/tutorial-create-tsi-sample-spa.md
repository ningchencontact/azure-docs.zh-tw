---
title: 教學課程：建立 Azure 時間序列深入解析單一頁面 Web 應用程式 | Microsoft Docs
description: 了解如何建立會從 Azure 時間序列深入解析環境查詢及轉譯資料的單頁 Web 應用程式。
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 06/29/2019
ms.author: dpalled
manager: cshankar
ms.custom: seodec18
ms.openlocfilehash: e415c681ae5a35de6e8ff76e09cfef8cc8cc98f8
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/03/2019
ms.locfileid: "67544073"
---
# <a name="tutorial-create-an-azure-time-series-insights-single-page-web-app"></a>教學課程：建立 Azure 時間序列深入解析單一頁面 Web 應用程式

本教學課程將逐步引導您建立自己的單頁 Web 應用程式 (SPA) 來存取 Azure 時間序列深入解析資料。

在本教學課程中，您將了解：

> [!div class="checklist"]
> * 應用程式設計
> * 如何向 Azure Active Directory (Azure AD) 註冊您的應用程式
> * 如何建置、發佈和測試您的 Web 應用程式

> [!NOTE]
> * [GitHub](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial) 上有提供此教學課程的原始程式碼。
> * 會裝載時間序列深入解析[用戶端應用程式範例](https://insights.timeseries.azure.com/clientsample)以顯示本教學課程所使用的已完成應用程式。

## <a name="prerequisites"></a>必要條件

* 如果您沒有帳戶，請註冊[免費 Azure 訂用帳戶](https://azure.microsoft.com/free/)。

* 免費的 Visual Studio。 下載 [2017 或 2019 社群版本](https://www.visualstudio.com/downloads/)來開始使用。

* 適用於 Visual Studio 的 IIS Express、Web Deploy 和 Azure 雲端服務核心工具元件。 請藉由修改 Visual Studio 安裝來新增元件。

## <a name="application-design"></a>應用程式設計

時間序列深入解析 SPA 範例是本教學課程所用設計和程式碼的基礎。 此程式碼會使用時間序列深入解析 JavaScript 用戶端程式庫。 時間序列深入解析用戶端程式庫會提供兩種主要 API 類別的概念：

- **用來呼叫時間序列深入解析查詢 API 的包裝函式方法**：可讓您使用 JSON 型運算式來查詢時間序列深入解析資料的 REST API。 這些方法會組織在程式庫的 TsiClient.server 命名空間下。

- **用來建立和填入數種圖表控制項類型的方法**：可用來在網頁上以視覺方式呈現時間序列深入解析資料的方法。 這些方法會組織在程式庫的 TsiClient.ux 命名空間下。

本教學課程也會使用來自範例應用程式時間序列深入解析環境的資料。 如需時間序列深入解析範例應用程式的結構及其如何使用時間序列深入解析用戶端程式庫的詳細資訊，請參閱[探索 Azure 時間序列深入解析 JavaScript 用戶端程式庫](tutorial-explore-js-client-lib.md)教學課程。

## <a name="register-the-application-with-azure-ad"></a>向 Azure AD 註冊應用程式

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

## <a name="build-and-publish-the-web-application"></a>建置和發佈 Web 應用程式

1. 建立用來儲存應用程式專案檔的資料夾。 然後，移至下列每個 URL。 以滑鼠右鍵按一下頁面右上角的 [原始]  連結，然後選取 [另存新檔]  將檔案儲存到專案目錄。

   - [index.html](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html)  ：頁面的 HTML 和 JavaScript
   - [sampleStyles.css]( https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/sampleStyles.css)  ：CSS 樣式表

   > [!NOTE]
   > 根據瀏覽器，您可能必須先將副檔名變更為 .html 或 .css 再儲存檔案。

1. 確認必要元件已安裝在 Visual Studio 中。 必須安裝適用於 Visual Studio 的 IIS Express、Web Deploy 和 Azure 雲端服務核心工具元件。

    [![Visual Studio - 修改已安裝的元件](media/tutorial-create-tsi-sample-spa/vs-installation.png)](media/tutorial-create-tsi-sample-spa/vs-installation.png#lightbox)

    > [!NOTE]
    > Visual Studio 體驗可能會隨版本和組態設定而與所示範例略有不同。

1. 開啟 Visual Studio 並登入。 若要建立 Web 應用程式的專案，請在 [檔案]  功能表上，選取 [開放]   > [網站]  。

    [![Visual Studio - 建立新的解決方案](media/tutorial-create-tsi-sample-spa/vs-solution-create.png)](media/tutorial-create-tsi-sample-spa/vs-solution-create.png#lightbox)

1. 在 [開啟網站]  窗格中，選取您用來儲存 HTML 和 CSS 檔案的工作目錄，然後選取 [開啟]  。

   [![Visual Studio - [檔案] 功能表，含有 [開啟] 和 [網站] 選項](media/tutorial-create-tsi-sample-spa/vs-file-open-web-site.png)](media/tutorial-create-tsi-sample-spa/vs-file-open-web-site.png#lightbox)

1. 在 Visual Studio 的 [檢視]  功能表中，選取 [方案總管]  。 新的解決方案隨即開啟。 其包含網站專案 (地球圖示)，其中包含 HTML 和 CSS 檔案。

   [![Visual Studio - 方案總管中的新解決方案](media/tutorial-create-tsi-sample-spa/vs-solution-explorer.png)](media/tutorial-create-tsi-sample-spa/vs-solution-explorer.png#lightbox)

1. 在發佈應用程式之前，您必須先更改 index.html  中的組態設定。

   1. 將註解 `"PROD RESOURCE LINKS"` 底下的三行取消註解，讓相依性從 DEVELOPMENT 切換到 PRODUCTION。 將註解 `"DEV RESOURCE LINKS"` 底下的三行註解化。

      [!code-html[head-sample](~/samples-javascript/pages/tutorial/index.html?range=2-20&highlight=10-13,15-18)]

      相依性應該會變為註解，如下列範例所示：

      ```HTML
      <!-- PROD RESOURCE LINKS -->
      <link rel="stylesheet" type="text/css" href="./sampleStyles.css">
      <script src="https://unpkg.com/tsiclient@1.1.4/tsiclient.js"></script>
      <link rel="stylesheet" type="text/css" href="https://unpkg.com/tsiclient@1.1.4/tsiclient.css">

      <!-- DEV RESOURCE LINKS -->
      <!-- <link rel="stylesheet" type="text/css" href="./sampleStyles.css">
      <script src="../../dist/tsiclient.js"></script>
      <link rel="stylesheet" type="text/css" href="../../dist/tsiclient.css"> -->
      ```

   1. 若要將應用程式設定為使用 Azure AD 應用程式註冊識別碼，請將 `clientID` 值變更為使用您在[註冊應用程式以使用 Azure AD](#register-the-application-with-azure-ad) 時的**步驟 3** 中所複製的 [應用程式識別碼]  。 如果您已在 Azure AD 中建立**登出 URL**，請將該值設定為 `postLogoutRedirectUri` 值。

      [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-153&highlight=4-5)]

      例如︰

      ```javascript
      clientId: '8884d4ca-b9e7-403a-bd8a-366d0ce0d460',
      postLogoutRedirectUri: 'https://tsispaapp.azurewebsites.net',
      ```

   1. 修改完成時，請儲存 index.html  。

1. 在 Azure 訂用帳戶中將 Web 應用程式發佈為 Azure App Service。  

   > [!NOTE]
   > 下列步驟所示螢幕擷取畫面中的數個選項，會自動填入 Azure 訂用帳戶中的資料。 每個窗格可能需要幾秒鐘的時間才能完全載入。  

   1. 在 [方案總管] 中，以滑鼠右鍵按一下網站專案節點，然後選取 [發佈 Web 應用程式]  。  

      [![Visual Studio - 選取方案總管的 [發佈 Web 應用程式] 選項](media/tutorial-create-tsi-sample-spa/vs-solution-explorer-publish-web-app.png)](media/tutorial-create-tsi-sample-spa/vs-solution-explorer-publish-web-app.png#lightbox)

   1. 選取 [啟動]  以開始發佈應用程式。

      [![Visual Studio - [發行設定檔] 窗格](media/tutorial-create-tsi-sample-spa/vs-publish-profile-target.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-target.png#lightbox)

   1. 選取要用來發佈應用程式的訂用帳戶。 選取 [TsiSpaApp]  專案。 然後選取 [確定]  。

      [![Visual Studio - [發行設定檔 App Service] 窗格](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png#lightbox)

   1. 選取 [發佈]  以部署 Web 應用程式。

      [![Visual Studio - [發佈] 選項和發佈記錄輸出](media/tutorial-create-tsi-sample-spa/vs-publish-profile-output.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-output.png#lightbox)

   1. Visual Studio 的 [輸出]  窗格中便會出現成功發佈記錄。 部署完成時，Visual Studio 便會在瀏覽器索引標籤中開啟 Web 應用程式，並提示您進行登入。 成功登入後，時間序列深入解析控制項便會填入資料。

## <a name="troubleshoot"></a>疑難排解  

錯誤碼/情況 | 說明
---------------------| -----------
*AADSTS50011：未註冊應用程式的回覆位址。* | Azure AD 註冊遺漏「回覆 URL」  屬性。 請移至 [設定]   > [回覆 URL]  來檢視 Azure AD 應用程式註冊。 確認**重新導向 URI** 是否存在，這是您在[註冊應用程式以使用 Azure AD](#register-the-application-with-azure-ad) 時可於**步驟 2** 選擇要指定的值。
*AADSTS50011：在要求中指定的回覆 URL 不符合為應用程式設定的回覆 URL：'\<Application ID GUID>'* 。 | 在[建置和發佈 Web 應用程式](#build-and-publish-the-web-application)的**步驟 6** 中指定的 `postLogoutRedirectUri`，必須符合在 Azure AD 應用程式註冊中的 [設定]   > [回覆 URL]  下指定的值。 請務必也要按照[建置和發佈 Web 應用程式](#build-and-publish-the-web-application)中的**步驟 5**，將 [目的地 URL]  的值變更為使用 https  。
Web 應用程式會載入，但使用無樣式的文字登入頁面和白色背景。 | 請確認[建置和發佈 Web 應用程式](#build-and-publish-the-web-application)中的**步驟 4** 所討論的路徑正確無誤。 如果 Web 應用程式找不到 .css 檔案，頁面將無法正確套用樣式。

## <a name="clean-up-resources"></a>清除資源

本教學課程建立了數個執行中的 Azure 服務。 如果您不打算完成本教學課程系列，建議您刪除所有資源，以避免產生不必要的成本。

在 Azure 入口網站的左側功能表：

1. 選取 [資源群組]  ，然後選取您為時間序列深入解析環境建立的資源群組。 在頁面頂端選取 [刪除資源群組]  、輸入資源群組的名稱，然後選取 [刪除]  。
1. 選取 [資源群組]  ，然後選取裝置模擬解決方案加速器所建立的資源群組。 在頁面頂端選取 [刪除資源群組]  、輸入資源群組的名稱，然後選取 [刪除]  。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解：

> [!div class="checklist"]
> * 應用程式設計
> * 如何向 Azure AD 註冊應用程式
> * 如何建置、發佈和測試您的 Web 應用程式

本教學課程會與 Azure AD 整合，並使用所登入使用者的身分識別來取得存取權杖。 若要了解如何藉由使用服務或精靈應用程式的身分識別來存取時間序列深入解析 API，請參閱下列文章：

> [!div class="nextstepaction"]
> [Azure 時間序列深入解析 API 的驗證和授權](time-series-insights-authentication-and-authorization.md)
