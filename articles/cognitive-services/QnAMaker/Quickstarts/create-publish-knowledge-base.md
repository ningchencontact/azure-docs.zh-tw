---
title: 建立、訓練及發佈知識庫 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 您可以從自己的內容 (例如常見問題集或產品手冊) 建立 QnA Maker 知識庫 (KB)。 此範例中的 QnA Maker 知識庫是從簡單的常見問題集網頁建立而來，以回答 BitLocker 金鑰復原問題。
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: 03ce1047dd175ae4a676fa1461632a8e23122a8d
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249737"
---
# <a name="create-train-and-publish-your-qna-maker-knowledge-base"></a>建立、訓練及發佈您的 QnA Maker 知識庫

您可以從自己的內容 (例如常見問題集或產品手冊) 建立 QnA Maker 知識庫 (KB)。 本文包含從簡單的常見問題集網頁建立 QnA Maker 知識庫的範例，以回答 BitLocker 金鑰復原問題。

## <a name="prerequisite"></a>必要條件

> [!div class="checklist"]
> * 如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="create-a-qna-maker-knowledge-base"></a>建立 QnA Maker 知識庫

1. 利用您的 Azure 認證登入 [QnAMaker.ai](https://QnAMaker.ai) 入口網站。

1. 在 QnA Maker 入口網站上，選取 [建立知識庫]  。

   ![QnA Maker 入口網站的螢幕擷取畫面](../media/qna-maker-create-kb.png)

1. 在 [建立]  頁面的步驟 1 中，選取 [建立 QnA 服務]  。 系統會將您導向 [Azure 入口網站](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)，以在您的訂用帳戶中設定 QnA Maker 服務。 如果 Azure 入口網站逾時，請在網站上選取 [再試一次]  。 連線之後，您的 Azure 儀表板隨即出現。

1. 您在 Azure 中成功建立新的 QnA Maker 服務之後，請回到 qnamaker.ai/create。 從步驟 2 的下拉式清單中選取您的 QnA Maker 服務。 如果您已建立新的 QnA Maker 服務，請務必重新整理頁面。

   ![選取 QnA Maker 服務知識庫的螢幕擷取畫面](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

1. 在步驟 3 中，將您的知識庫命名為**我的範例 QnA KB**。

1. 若要將內容新增至您的知識庫，請選取三種類型的資料來源。 在步驟 4 的 [填入您的 KB]  之下，在 [URL]  方塊中新增 [BitLocker 修復常見問題集](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview-and-requirements-faq) URL。

   ![新增資料來源的螢幕擷取畫面](../media/qnamaker-quickstart-kb/add-datasources.png)

1. 在步驟 5 中，選取 [建立您的 KB]  。

1. 當 QnA Maker 建立知識庫時，會出現一個快顯視窗。 擷取程序需要幾分鐘的時間來讀取 HTML 頁面並找出問題和回答。

1. 在 QnA Maker 成功建立知識庫之後，[知識庫]  頁面隨即開啟。 您可以在此頁面上編輯知識庫的內容。

## <a name="edit-the-knowledge-base"></a>編輯知識庫

1. 在 QnA Maker 入口網站的 [編輯]  區段中，選取 [新增 QnA 配對]  以在知識庫中新增資料列。 在 [問題]  之下，輸入 **Hi**。 在 [回答]  之下，輸入**Hello.Ask me bitlocker questions.**

    ![QnA Maker 入口網站的螢幕擷取畫面](../media/qnamaker-quickstart-kb/add-qna-pair.png)

1. 在右上方，選取 [儲存並定型]  來儲存您的編輯內容並將 QnA Maker 模型定型。 除非儲存編輯內容，否則不會保留。

## <a name="test-the-knowledge-base"></a>測試知識庫

1. 在 QnA Maker 入口網站的右上方，選取 [測試]  來測試您所做的變更是否已生效。 在方塊中輸入 `hi there`，然後選取 Enter。 您應會看到您所建立的回答成為回應。

1. 選取 [檢查]  ，更詳細地檢查回應。 測試視窗用來測試您對知識庫所做的變更，然後加以發佈。

    ![測試面板的螢幕擷取畫面](../media/qnamaker-quickstart-kb/inspect.png)

1. 再次選取 [測試]  ，以關閉 [測試]  快顯視窗。

## <a name="publish-the-knowledge-base"></a>發佈知識庫

發佈知識庫時，知識庫的問題與答案內容會從測試索引移到 Azure 搜尋服務中的生產索引。

![移動您的知識庫內容的螢幕擷取畫面](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

1. 在 QnA Maker 入口網站中，從 [編輯]  旁邊的功能表選取 [發佈]  。 若要接著確認，請選取頁面上的 [發佈]  。

1. QnA Maker 服務現在已成功發佈。 您可以在您的應用程式或 Bot 程式碼中使用此端點。

    ![發佈成功的螢幕擷取畫面](../media/qnamaker-quickstart-kb/publish-sucess.png)

## <a name="create-a-bot"></a>建立 Bot

發佈之後，您可以從 [發佈]  頁面建立 Bot： 

* 您可以快速建立數個 Bot，讓全部 Bot 指向相同知識庫中個別 Bot 適用的不同區域或定價方案。 
* 如果您想讓知識庫只有一個 Bot，可使用**在 Azure 入口網站中檢視 Bot** 連結，來檢視您目前的 Bot 清單。 

當您變更知識庫並重新發佈時，不需要對 Bot 採取進一步的動作。 其已設定為與知識庫搭配使用，而且會與知識庫未來的所有變更搭配運作。 每次發佈知識庫後，與之連線的所有 Bot 就會自動更新。

1. 在 QnA Maker 入口網站的 [發佈]  頁面上，選取 [建立 Bot]  。 只有在發佈知識庫後，此按鈕才會出現。

    ![建立 Bot 的螢幕擷取畫面](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

1. Azure 入口網站會在新的瀏覽器索引標籤中開啟，其中會包含 Azure Bot 服務的建立頁面。 設定 Azure Bot 服務。 如需有關這些組態設定的詳細資訊，請參閱[使用 Azure Bot 服務 v4 建立 QnA Bot](../tutorials/create-qna-bot.md)。
    
    * 建立 Bot 時，請勿在 Azure 入口網站中變更下列設定。 這些是為您現有知識庫預先填入的內容： 
        * QnA 驗證金鑰
        * App Service 方案和位置
        * Azure 儲存體
    * Bot 與 QnA Maker 可共用 Web 應用程式服務方案，但不能共用 Web 應用程式。 這表示**應用程式名稱**必須與您建立 QnA Maker 服務時所用的應用程式名稱不同。 


## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建立知識庫](../How-To/create-knowledge-base.md)
