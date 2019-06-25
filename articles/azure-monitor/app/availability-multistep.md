---
title: 監視您的 web 應用程式，搭配多重步驟 web 測試和 Azure Application Insights |Microsoft Docs
description: 設定多重步驟 web 測試，來監視您的 web 應用程式使用 Azure Application Insights
services: application-insights
author: mrbullwinkle
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: d8bfe92af4e8afc4edae76efb2e1cb7b287c7aa9
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305008"
---
# <a name="multi-step-web-tests"></a>多重步驟 Web 測試

您可以監視記錄的一連串的 Url 並透過多步驟 web 測試的網站之間的互動。 本文將逐步引導您完成使用 Visual Studio Enterprise 中建立多重步驟 web 測試的程序。

> [!NOTE]
> 多重步驟 web 測試有與其相關聯的額外成本。 若要了解更多的諮詢[官方定價指南](https://azure.microsoft.com/pricing/details/application-insights/)。

## <a name="pre-requisites"></a>先決條件

* Visual Studio 2017 Enterprise 或更新版本。
* Visual Studio web 效能和負載測試工具。

若要找出測試的工具必要條件。 啟動**Visual Studio 安裝程式** > **個別元件** > **偵錯和測試** >  **執行 web 效能和負載測試工具**。

![使用 Web 效能和負載測試工具的項目旁邊的核取方塊選取個別元件的 Visual Studio 安裝程式 UI 的螢幕擷取畫面](./media/availability-multistep/web-performance-load-testing.png)

## <a name="record-a-multi-step-web-test"></a>記錄多步驟 web 測試

若要建立多重步驟測試，您可以使用 Visual Studio Enterprise 來記錄案例，然後將記錄結果上傳至 Application Insights。 Application Insights 設定的間隔會重新執行案例，並確認回應。

> [!IMPORTANT]
> * 您無法在測試中使用已編碼的函式或迴圈。 測試必須完全包含於 .webtest 指令碼中。 不過，您可以使用標準外掛程式。
> * 多重步驟 Web 測試僅支援英文字元。 如果您在 Visual Studio 中使用其他語言，請更新 Web 測試的定義檔，以轉譯/排除非英文字元。

使用 Visual Studio Enterprise 來記錄 Web 工作階段。

1. 建立 Web 效能和負載測試專案。 **檔案** > **新** > **專案** > **Visual C#**   > **測試**

    ![Visual Studio 新專案 UI](./media/availability-multistep/vs-web-performance-and-load-test.png)

2. 開啟`.webtest`檔案，並開始錄製。

    ![Visual Studio 測試錄製 UI](./media/availability-multistep/open-web-test.png)

3. 按一下您想要測試，以模擬一部分錄製的步驟。

    ![瀏覽器記錄 UI](./media/availability-multistep/record.png)

4. 編輯本測試以進行下列事項：

    * 加入驗證以檢查收到的文字和回應碼。
    * 移除任何 uneccesary 互動。 您也可以移除圖片的相依要求，或加入與您考慮您的測試成功不相關的追蹤網站。
    
    請記住，您只能編輯的測試指令碼-您可以加入自訂程式碼，或呼叫其他 web 測試。 請勿在此測試中插入迴圈。 您可以使用標準 Web 測試的外掛程式。

5. 若要驗證，並確保其運作正常的 Visual Studio 中執行測試。

    Web 測試執行器會開啟網頁瀏覽器，並重複您已記錄的動作。 請確定一切如預期般運作。

## <a name="upload-the-web-test"></a>上傳 web 測試

1. 在 Application Insights 入口網站，在 [可用性] 窗格中選取**建立測試** > **測試類型** > **多重步驟 web 測試**。

2. 設定測試位置、 頻率及警示參數。

### <a name="frequency--location"></a>頻率] 和 [位置

|設定| 說明
|----|----|----|
|**測試頻率**| 設定從每個測試位置執行測試的頻率。 預設頻率為 5 分鐘且有五個測試位置，則您的網站平均每一分鐘會執行測試。|
|**測試位置**| 會從我們的伺服器，將 web 要求傳送至您的 URL 位置。 **我們建議的測試位置的最小數目為 5**以便，您可以分辨您網站的問題與網路問題。 您最多可以選取 16 個位置。

### <a name="success-criteria"></a>成功準則

|設定| 說明
|----|----|----|
| **測試逾時** |降低此值可收到有關回應變慢的警示。 如果未在這段時間內收到您網站的回應，則測試會視為失敗。 如果已選取 [剖析相依要求]  ，則必須在這段時間內收到所有映像、樣式檔、指令碼和其他相依資源。|
| **HTTP 回應** | 視為成功的傳回狀態碼。 200 是表示已傳回標準 Web 網頁的代碼。|
| **內容比對** | 字串，例如 「 歡迎 ！ 我們會測試每個回應中的區分大小寫完全相符。 必須是單純字串，不含萬用字元。 別忘了，如果頁面內容變更，則可能需要更新。 **內容比對支援英文字元** |

### <a name="alerts"></a>警示

|設定| 說明
|----|----|----|
|**接近即時 （預覽）** | 我們建議使用近乎即時警示。 設定這類警示是完成之後建立可用性測試。  |
|**傳統** | 我們不再建議針對新的可用性測試使用傳統的警示。|
|**警示位置臨界值**|建議至少為位置數的 3/5。 警示位置臨界值與測試的位置數目之間的最佳關係**警示位置閾值** = **的測試位置-2，最少五個測試位置的數字。**|

## <a name="advanced-configuration"></a>進階組態

### <a name="plugging-time-and-random-numbers-into-your-test"></a>您的測試中插入時間和隨機數字

假設您要測試的工具會從外部來源取得與時間相關的資料 (例如股票)。 當您記錄 Web 測試時，您必須使用特定的時間，但您將它們設為測試的參數：StartTime 和 EndTime。

![我很棒的內建應用程式螢幕擷取畫面](./media/availability-multistep/app-insights-72webtest-parameters.png)

當您執行測試時，希望 EndTime 永遠為目前時間，而 StartTime 為 15 分鐘前。

Web 測試的日期時間外掛程式提供方式，來處理參數化次數。

1. 針對您想要的每個變數參數值，各加入一個 Web 測試外掛程式。 在 Web 測試工具列中，選擇 [加入 Web 測試外掛程式]  。
    
    ![加入 Web 測試外掛程式](./media/availability-multistep/app-insights-72webtest-plugin-name.png)
    
    在此範例中，我們會使用兩個日期時間外掛程式執行個體。 一個執行個體設定為 "15 minutes ago"，另一個則設定為 "now"。

2. 開啟每個外掛程式的屬性。 為屬性命名，然後將它設為使用目前時間。 對其中一個，設定 [加入分鐘] = -15。

    ![內容參數](./media/availability-multistep/app-insights-72webtest-plugin-parameters.png)

3. 在 Web 測試參數中，使用 {{plug-in name}} 來參考外掛程式名稱。

    ![StartTime](./media/availability-multistep/app-insights-72webtest-plugins.png)

現在將您的測試上傳至入口網站。 在每次執行測試時，它會使用動態值。

### <a name="dealing-with-sign-in"></a>處理登入

如果使用者登入您的應用程式，您有許多模擬登入的選項，以便在登入後方測試頁面。 您使用的方法取決於應用程式所提供的安全性類型。

在所有情況下，您應該只為了測試用途在您的應用程式中建立帳戶。 可能的話，限制此測試帳戶的權限，讓 Web 測試不可能影響實際使用者。

**簡單的使用者名稱和密碼**以一般方式記錄 web 測試。 先刪除 Cookie。

**SAML 驗證**使用適用於 web 測試的 SAML 外掛程式。 存取由外掛程式...

**用戶端祕密**如果您的應用程式登入路由牽涉到用戶端祕密，請使用該路由。 Azure Active Directory (AAD) 是可提供用戶端密碼登入的服務範例。 在 AAD 中，用戶端密碼是應用程式金鑰。

以下是使用應用程式金鑰之 Azure Web 應用程式的 Web 測試範例︰

![範例螢幕擷取畫面](./media/availability-multistep/client-secret.png)

從使用用戶端密鑰 (AppKey) 的 AAD 取得權杖。
從回應中擷取持有人權杖。
使用授權標頭中的持有人權杖呼叫 API。
請確定您的 web 測試是實際的用戶端-也就是它自己的應用程式在 AAD 中，並使用其 clientId + 應用程式金鑰。 您測試中的服務在 AAD 中也有自己的應用程式︰此應用程式的 appID URI 會反映於 [資源] 欄位中的 Web 測試。

### <a name="open-authentication"></a>開放驗證
使用您的 Microsoft 或 Google 帳戶登入即是開放驗證的範例。 許多使用 OAuth 的應用程式都提供替代用戶端密碼，第一個技巧就是調查該可能性。

如果您的測試必須使用 OAuth 登入，則常用的方式是：

使用 Fiddler 等工具來檢查網頁瀏覽器、驗證網站及您的應用程式之間的流量。
使用不同的電腦或瀏覽器，或以較長時間間隔 (讓權杖過期) 執行兩次以上的登入。
藉由比較不同的工作階段，識別從驗證網站傳回的權杖，登入之後此權杖會傳遞至您的應用程式伺服器。
使用 Visual Studio 記錄 Web 測試。
將權杖參數化，當驗證器傳回權杖時設定參數，然後在查詢網站時使用參數。 (Visual Studio 會嘗試將測試參數化，但不會正確地將權杖參數化。)

## <a name="troubleshooting"></a>疑難排解

專用[疑難排解文章](troubleshoot-availability.md)。

## <a name="next-steps"></a>後續步驟

* [可用性警示](availability-alerts.md)
* [Url ping web 測試](monitor-web-app-availability.md)
