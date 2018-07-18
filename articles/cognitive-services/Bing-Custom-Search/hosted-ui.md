---
title: Bing 自訂搜尋：網站搜尋 | Microsoft Docs
description: 說明如何設定託管的 UI
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 593ea4d23f8ddcec8efc4be632afa2aab1a5210f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2018
ms.locfileid: "35368839"
---
# <a name="configure-your-hosted-ui-experience"></a>設定託管的 UI 體驗
設定您的自訂搜尋執行個體之後，您可以呼叫自訂搜尋 API 以取得搜尋結果，並在您的應用程式中加以顯示。 或者，如果您的應用程式是 Web 應用程式，您可以使用自訂搜尋所提供的託管 UI。   

## <a name="configure-custom-hosted-ui"></a>設定自訂託管 UI
使用下列指示，設定要包含在 Web 應用程式中的託管 UI。
1.  登入自訂搜尋[入口網站](https://customsearch.ai)。
2.  按一下 [自訂搜尋] 執行個體。 若要建立執行個體，請參閱[建立您的第一個 Bing 自訂搜尋執行個體](quick-start.md)。
3.  按一下 [託管的 UI] 索引標籤。
4.  選取配置。
    - 搜尋列和結果 (預設值) &mdash; 會顯示搜尋方塊和搜尋結果
    - 僅限結果 &mdash; 不顯示搜尋方塊，而僅顯示結果
    - Pop-over 視窗 &mdash; 不顯示搜尋方塊，而僅在滑動覆疊中顯示結果
    
   > [!IMPORTANT]
   > 選取 [僅限結果] 配置時，請務必包含 customConfig 查詢參數；請參閱[查詢參數](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters)。

5.  在 [其他組態] 下方，為您的應用程式提供適當的值。 這些設定是選用的。 若要檢視套用或移除這些設定的效果，請查看右側的預覽窗格。  可用的組態選項包括：
    - Web 搜尋組態：
        - 啟用 Web 結果 &mdash; 決定是否傳回 Web 搜尋結果
        - 啟用自動建議 &mdash; 決定是否啟用自訂自動建議
        - 每頁的 Web 結果 &mdash; 每次顯示的 Web 搜尋結果數目
        - 影像標題 &mdash; 決定是否顯示搜尋結果的影像
        - 醒目提示字組 &mdash; 決定是否以粗體顯示搜尋字詞的結果
    - 影像搜尋組態：
        - 啟用影像結果 &mdash; 決定是否傳回影像搜尋結果
    - 其他組態：
        - 頁面標題 &mdash; 顯示在頁面標題區域中的文字
        - 工具列佈景主題 &mdash; 決定頁面標題區域的背景色彩
        - 搜尋方塊文字預留位置 &mdash; 在輸出之前顯示於搜尋方塊中的文字
        - 標題連結 URL &mdash; 標題連結的目標
        - 標誌 URL &mdash; 顯示於標題旁的影像 
        - Favicon URL &mdash; 顯示於瀏覽器標題列中的圖示

   > [!IMPORTANT]
   > 至少必須啟用一個影像搜尋或 Web 搜尋。

6.  輸入搜尋訂用帳戶金鑰，或從下拉式清單中選擇金鑰。 下拉式清單中會填入您帳戶的 Azure 訂用帳戶中的金鑰。 請參閱[認知服務 API 帳戶](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。
7.  如果您已啟用自動建議，請輸入自動建議訂用帳戶金鑰，或從下拉式清單中選擇金鑰。 下拉式清單中會填入您帳戶的 Azure 訂用帳戶中的金鑰。 自訂自動建議需要特定的訂用帳戶層，請參閱[定價頁面](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)。

> [!NOTE]
> 當您對自訂託管 UI 組態進行變更時，右側的窗格會顯示所做變更的參考。 顯示的搜尋結果並不是執行個體的實際結果

[!INCLUDE[publish or revert](./includes/publish-revert.md)]

## <a name="consume-custom-ui"></a>使用自訂 UI
若要使用託管的 UI，請： 

- 在您的網頁中包含指令碼
    ``` html
    <html>
        <body>
            <script type="text/javascript"
                id="bcs_js_snippet"            
                src="https://ui.customsearch.ai/api/ux/render?customConfig=<YOUR-CUSTOM-CONFIG-ID>&market=en-US&safeSearch=Moderate">            
            </script>
        </body>    
    </html>
    ```

- 使用提供的 URL `https://ui.customsearch.ai/hosted?customConfig=YOUR-CUSTOM-CONFIG-ID`

  > [!IMPORTANT]
  > 頁面無法顯示您的隱私權聲明或其他通知和條款。 對您的適用性可能有所不同。  

如需相關資訊 (包括您的自訂組態識別碼)，請移至 [生產] 索引標籤下的 [端點]。

## <a name="next-steps"></a>後續步驟
- [使用裝飾標記醒目提示文字](./hit-highlighting.md)
- [頁面網頁](./page-webpages.md)