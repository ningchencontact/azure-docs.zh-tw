---
title: 連結認知服務資源與技能 - Azure 搜尋服務
description: 將「認知服務」多功能訂用帳戶連結至「Azure 搜尋服務」中認知擴充管線的指示。
manager: cgronlun
author: LuisCabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: bfa9bbb9816148182b79a8231f2ddb3e46433804
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/19/2019
ms.locfileid: "54413238"
---
# <a name="attach-a-cognitive-services-resource-with-a-skillset-in-azure-search"></a>在 Azure 搜尋服務中連結認知服務資源與技能 

推動[認知搜尋管線](cognitive-search-concept-intro.md)的 AI 演算法，而這些管線用來處理 Azure 搜尋服務編製索引作業中的非結構化資料。 這些演算法是以[認知服務資源](https://azure.microsoft.com/services/cognitive-services/)為基礎，包括可供影像分析和光學字元辨識 (OCR) 的[電腦視覺](https://azure.microsoft.com/services/cognitive-services/computer-vision/)，以及可供實體辨識、關鍵片語擷取和其他擴充的[文字分析](https://azure.microsoft.com/services/cognitive-services/text-analytics/)。

您可以免費擴充數目有限的文件，或是針對較大型且較頻繁的工作負載連結可計費的認知服務資源。 在本文中，了解如何將「認知服務」資源與您的認知技能建立關聯，以在 [Azure 搜尋服務編製索引](search-what-is-an-index.md)期間擴充資料。

如果您的管線包含與認知服務 API 不相關的技能，您仍然應該連結認知服務資源。 這麼做可覆寫限制您每天少量擴充的**免費**資源。 未繫結至認知服務 API 的技能完全免費。 這些技能包括：[自訂技能](cognitive-search-create-custom-skill-example.md)、[文字合併](cognitive-search-skill-textmerger.md)、[文字分隔器](cognitive-search-skill-textsplit.md)及[塑形器](cognitive-search-skill-shaper.md)。

> [!NOTE]
> 從 2018 年 12 月 21 日開始，您可以將認知服務資源與 Azure 搜尋服務技能建立關聯。 這可讓我們收取技能執行的費用。 自這個日期起，我們也會開始收取文件萃取階段中影像擷取的費用。 從文件中擷取文字的功能則繼續免費提供。
>
> [內建認知技能](cognitive-search-predefined-skills.md)執行會以[認知服務預付型方案價格](https://azure.microsoft.com/pricing/details/cognitive-services)收費，費率與直接執行工作時一樣。 影像擷取是 Azure 搜尋服務費用，目前是以預覽版定價提供。 如需詳細資訊，請參閱 [Azure 搜尋服務定價頁面](https://go.microsoft.com/fwlink/?linkid=2042400)或[計費方式](search-sku-tier.md#how-billing-works)。


## <a name="use-free-resources"></a>使用免費資源

您可以使用有限的免費處理選項，完成認知搜尋教學課程和快速入門練習。 

> [!Important]
> 從 2019 年 2 月 1 日開始，[免費 (有限的擴充)] 會限制為每天 20 份文件。 

1. 開啟 [匯入資料] 精靈。

   ![匯入資料命令](media/search-get-started-portal/import-data-cmd2.png "匯入資料命令")

1. 選擇資料來源，然後繼續進行 [新增認知搜尋 (選擇性)]。 如需此精靈的逐步解說，請參閱[使用入口網站工具進行匯入、編製索引和查詢](search-get-started-portal.md)。

1. 展開 [附加認知服務]，然後選取 [免費 (有限的擴充)]。

   ![展開的 [附加認知服務] 區段](./media/cognitive-search-attach-cognitive-services/attach1.png "展開的 [附加認知服務]")

繼續進行下一個步驟 [新增擴充]。 如需入口網站中可用技能的描述，請參閱[步驟 2：新增認知技能](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline) (位於認知搜尋快速入門中)。

## <a name="use-billable-resources"></a>使用計費資源

對於每天為數超過 20 項擴充的工作負載，您需要連結可計費的認知服務資源。 

您只需支付呼叫認知服務 API 的技能費用。 非 API 型技能 (例如[自訂技能](cognitive-search-create-custom-skill-example.md)、[文字合併](cognitive-search-skill-textmerger.md)、[文字分隔器](cognitive-search-skill-textsplit.md)及[塑形器](cognitive-search-skill-shaper.md)技能) 不計費。

1. 在 [匯入資料] 精靈的 [附加認知服務] 中，選取現有的資源或按一下 [建立新的認知服務資源]。

1. 針對 [建立新的認知服務資源]，會開啟新的索引標籤，以便讓您能夠建立資源。 請為資源提供一個唯一名稱。

1. 選擇與「Azure 搜尋服務」相同的位置。 目前，支援認知技能索引編製功能的區域如下：

  * 美國中西部
  * 美國中南部
  * 美國東部
  * 美國東部 2
  * 美國西部 2
  * 加拿大中部
  * 西歐
  * 英國南部
  * 北歐
  * 巴西南部
  * 東南亞
  * 印度中部
  * 澳洲東部

1. 選擇多功能定價層 [S0]。 此層提供支援認知搜尋中預先定義之技能的「視覺」和「語言」功能。

    ![建立新的認知服務資源](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "建立新的認知服務資源")

1. 按一下 [建立] 以佈建新的「認知服務」資源。 

1. 返回包含 [匯入資料] 精靈的上一個索引標籤。 按一下 [重新整理] 以顯示「認知服務」資源，然後選取該資源。

   ![選取的認知服務資源](./media/cognitive-search-attach-cognitive-services/attach2.png "選取的認知服務資源")

1. 展開 [新增擴充] 區段，以選取您想要對資料執行的特定認知技能，並繼續進行其餘的流程。 如需入口網站中可用技能的描述，請參閱[步驟 2：新增認知技能](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline) (位於認知搜尋快速入門中)。

## <a name="attach-an-existing-skillset-to-a-cognitive-services-resource"></a>將現有的技能附加至認知服務資源

如果您有現有的技能，就可以將其附加至新的或不同的「認知服務」資源。

1. 在 [服務概觀] 頁面中，按一下 [技能]。

   ![技能索引標籤](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "技能索引標籤")

1. 按一下技能的名稱，然後選取現有的資源或建立一個新資源。 按一下 [確定] 以確認變更。 

   ![技能資源清單](./media/cognitive-search-attach-cognitive-services/attach-existing2.png "技能資源清單")

請記住，[免費 (有限的擴充)] 是限制為每天 20 個文件，而 [建立新的認知服務資源] 則用來佈建新的計費資源。 如果您建立新資源，請選取 [重新整理] 以重新整理「認知服務」資源的清單，然後選取資源。

## <a name="attach-cognitive-services-programmatically"></a>透過程式設計方式附加認知服務

透過程式設計方式定義技能時，請將 `cognitiveServices` 區段新增至該技能。 在該區段中，包含您想要與技能建立關聯之認知服務資源的索引鍵。 請記住，資源必須與「Azure 搜尋服務」位在相同的區域中。 以及包含 `@odata.type`，並將它設定為 `#Microsoft.Azure.Search.CognitiveServicesByKey`。 

下列範例示範了此模式。 請注意，位於定義底部的 cognitiveServices 區段

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2017-11-11-Preview
api-key: [admin key]
Content-Type: application/json
```
```json
{
    "name": "skillset name",
    "skills": 
    [
      {
        "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
        "categories": [ "Organization" ],
        "defaultLanguageCode": "en",
        "inputs": [
          {
            "name": "text", "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "organizations", "targetName": "organizations"
          }
        ]
      }
    ],
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "mycogsvcs",
        "key": "<your key goes here>"
    }
}
```

## <a name="example-estimate-costs"></a>範例：預估成本

若要預估與認知搜尋索引編製相關的成本，請從平均文件樣貌的概念開始著手，以便您能夠執行一些數字。 例如，針對估算目的，您可以模擬：

+ 1000 個 PDF
+ 各有 6 頁
+ 每頁各一個影像 (6000 個影像)
+ 每頁 3000 個字元

假設有一個管線，由運用影像和文字擷取、影像的光學字元辨識 (OCR) 及組織的具名實體辨識對每個 PDF 進行的文件破解所組成。 

在這個練習中，我們將採用每一交易的最高價格。 實際成本可能會因分級定價而較低。 請參閱[認知服務價格](https://azure.microsoft.com/pricing/details/cognitive-services)。

1. 針對文字和影像內容的文件破解，目前文字擷取是免費功能。 針對 6000 個影像，假設每 1,000 個擷取的影像為 $1 美元，則此步驟將花費 $6.00 美元。

2. 針對 6,000 個英文影像的 OCR，OCR 認知技能會使用最佳演算法 (DescribeText)。 假設每分析 1,000 個影像的費用為美金 $2.50 元，則在此步驟中您需支付美金 $15.00 元。

3. 對於實體擷取，每頁共有 3 筆文字記錄  每筆記錄是 1,000 個字元。 每頁 3 筆文字記錄 * 6,000 頁 = 18,000 筆文字記錄。 假設每 1,000 筆文字記錄需要美金 $2.00 元，則此步驟需要美金 $36.00 元。

全部加總後，就使用所述技能來內嵌 1,000 個此性質的 PDF 文件而言，您將需支付大約 $57.00 美元。 

## <a name="next-steps"></a>後續步驟
+ [Azure 搜尋服務定價頁面](https://azure.microsoft.com/pricing/details/search/)
+ [如何定義技能集](cognitive-search-defining-skillset.md) (英文)
+ [建立技能集 (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [如何對應擴充的欄位](cognitive-search-output-field-mapping.md)
