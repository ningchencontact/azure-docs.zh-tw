---
title: 連結認知服務資源與技能 - Azure 搜尋服務
description: 在 Azure 搜尋服務中將認知服務的多個訂用帳戶附加至認知擴充管線的指示。
manager: nitinme
author: LuisCabrer
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: luisca
ms.openlocfilehash: 113286f829b628d4740fbba34e7279741a934aef
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "71265938"
---
# <a name="attach-a-cognitive-services-resource-with-a-skillset-in-azure-search"></a>在 Azure 搜尋服務中連結認知服務資源與技能 

AI 演算法會在 Azure 搜尋服務中驅動用於檔擴充的[認知索引管線](cognitive-search-concept-intro.md)。 這些演算法是以 Azure 認知服務資源為基礎，包括影像分析和光學字元辨識（OCR）的[電腦視覺](https://azure.microsoft.com/services/cognitive-services/computer-vision/)，以及實體辨識、關鍵字組解壓縮和其他擴充的[文字分析](https://azure.microsoft.com/services/cognitive-services/text-analytics/). 根據 Azure 搜尋服務用於檔擴充的用途，演算法會包裝在*技能*內，放在*技能集*中，並在索引編制期間由*索引子*所參考。

您可以免費充實有限數目的檔。 或者，您可以針對較大型且較頻繁的工作負載，將可計費的認知服務資源附加至*技能集*。 在本文中，您將瞭解如何附加可計費的認知服務資源，以在 Azure 搜尋服務[編制索引](search-what-is-an-index.md)期間擴充檔。

> [!NOTE]
> 可計費事件包括認知服務 API 的呼叫，以及在 Azure 搜尋服務的檔破解階段中進行影像解壓縮的過程。 從檔或不會呼叫認知服務的技能，不會收取文字解壓縮的費用。
>
> 計費技能的執行是認知服務的[隨用隨付價格](https://azure.microsoft.com/pricing/details/cognitive-services/)。 如需影像解壓縮的價格，請參閱[Azure 搜尋服務定價頁面](https://go.microsoft.com/fwlink/?linkid=2042400)。

## <a name="same-region-requirement"></a>相同區域需求

我們需要 Azure 搜尋服務和 Azure 認知服務存在於相同的區域內。 否則，您將會在執行時間收到此訊息： `"Provided key is not a valid CognitiveServices type key for the region of your search service."` 

沒有任何方法可以跨區域移動服務。 如果您收到此錯誤，您應該在與 Azure 搜尋服務相同的區域中建立新的認知服務資源。

> [!NOTE]
> 某些內建技能是以非區域認知服務為基礎（例如，[文字翻譯技能](cognitive-search-skill-text-translation.md)）。 請注意，如果您在技能集中加入這些技能的任何一項，您的資料不一定會保留在與 Azure 搜尋服務或認知服務資源相同的區域中。 如需詳細資訊，請參閱[服務狀態頁面](https://aka.ms/allinoneregioninfo)。

## <a name="use-free-resources"></a>使用免費資源

您可以使用有限的免費處理選項，完成認知搜尋教學課程和快速入門練習。

每個訂用帳戶每天可免費（有限的擴充）資源限制為20份檔。

1. 開啟 [匯入資料] 嚮導：

   ![開啟 [匯入資料] 嚮導](media/search-get-started-portal/import-data-cmd.png "開啟 [匯入資料] 嚮導")

1. 選擇資料來源並繼續**新增認知搜尋（選擇性）** 。 如需此 wizard 的逐步解說，請參閱[使用入口網站工具匯入、編制索引和查詢](search-get-started-portal.md)。

1. 展開 [**附加認知服務**]，然後選取 **[免費（有限的擴充）** ]：

   ![展開的附加認知服務區段](./media/cognitive-search-attach-cognitive-services/attach1.png "展開的附加認知服務區段")

1. 繼續進行下一個步驟，**新增擴充**。 如需入口網站中可用技能的描述，請參閱認知搜尋快速入門中的[步驟2：新增認知技能](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline)。

## <a name="use-billable-resources"></a>使用計費資源

針對每天建立超過20個擴充的工作負載，請務必附加可計費的認知服務資源。 我們建議您一律附加可計費的認知服務資源，即使您不想要呼叫認知服務 API 也一樣。 附加資源會覆寫每日限制。

您只需支付呼叫認知服務 API 的技能。 您不需支付[自訂技能](cognitive-search-create-custom-skill-example.md)或[文字合併](cognitive-search-skill-textmerger.md)、[文字分隔器](cognitive-search-skill-textsplit.md)和[整形](cognitive-search-skill-shaper.md)程式等技能，這不是以 API 為基礎。

1. 開啟 [匯入資料] 嚮導，選擇 [資料來源]，然後繼續**新增認知搜尋（選擇性）** 。

1. 展開 [**附加認知服務**]，然後選取 [**建立新的認知服務資源**]。 隨即會開啟新的索引標籤，讓您可以建立資源：

   ![建立認知服務資源](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "建立認知服務資源")

1. 在 [**位置**] 清單中，選取您的 Azure 搜尋服務服務所在的區域。 基於效能考慮，請務必使用此區域。 使用此區域也會對區域之間的輸出頻寬費用產生不一致的情況。

1. 在 [**定價層**] 清單中，選取 [ **S0** ] 以取得認知服務功能的多功能集合，包括可回到 Azure 搜尋服務所使用之預先定義技能的願景和語言功能。

   針對 S0 層，您可以在[認知服務定價頁面](https://azure.microsoft.com/pricing/details/cognitive-services/)上找到特定工作負載的費率。
  
   + 在 [**選取供應**專案] 清單中，確認已選取 [**認知服務**]。
   + 在 [**語言**功能] 底下，**文字分析 Standard**的費率適用于 AI 索引。
   + 在 [**視覺**功能] 底下，適用**電腦視覺 S1**的費率。

1. 選取 [建立] 以布**建**新的認知服務資源。

1. 返回上一個索引標籤，其中包含 [匯入資料] wizard。 選取 [重新整理] 以顯示認知服務資源，**然後選取資源**：

   ![選取認知服務資源](./media/cognitive-search-attach-cognitive-services/attach2.png "選取認知服務資源")

1. 展開 [**新增擴充**] 區段，以選取您想要在資料上執行的特定認知技能。 完成嚮導的其餘部分。 如需入口網站中可用技能的描述，請參閱認知搜尋快速入門中的[步驟2：新增認知技能](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline)。

## <a name="attach-an-existing-skillset-to-a-cognitive-services-resource"></a>將現有的技能附加至認知服務資源

如果您有現有的技能，就可以將其附加至新的或不同的「認知服務」資源。

1. 在 [**服務總覽**] 頁面上，選取 [**技能集**]：

   ![技能集索引標籤](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "技能集索引標籤")

1. 選取技能集的名稱，然後選取現有的資源或建立一個新的資源。 按一下 [確定] 確認變更。

   ![技能集資源清單](./media/cognitive-search-attach-cognitive-services/attach-existing2.png "技能集資源清單")

   請記住，[**免費（有限的擴充）** ] 選項會每天限制您20份檔，而且您可以使用 [**建立新的認知服務資源**] 來布建新的可計費資源。 如果您建立新資源，請選取 [重新整理] 以重新整理「認知服務」資源的清單，然後選取資源。

## <a name="attach-cognitive-services-programmatically"></a>透過程式設計方式附加認知服務

透過程式設計方式定義技能時，請將 `cognitiveServices` 區段新增至該技能。 在該區段中，包含您要與技能集建立關聯之認知服務資源的索引鍵。 請記住，資源必須位於與您 Azure 搜尋服務資源相同的區域中。 以及包含 `@odata.type`，並將它設定為 `#Microsoft.Azure.Search.CognitiveServicesByKey`。

下列範例示範了此模式。 請注意定義結尾的 `cognitiveServices` 區段。

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2019-05-06
api-key: [admin key]
Content-Type: application/json
```
```json
{
    "name": "skillset name",
    "skills": 
    [
      {
        "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
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

若要估計與認知搜尋索引相關的成本，請從一般檔的外觀開始著手，讓您可以執行一些數位。 例如，您可能會大致估計：

+ 1000 Pdf。
+ 每個頁面都有六頁。
+ 每頁一個影像（6000個影像）。
+ 每頁3000個字元。

假設管線包含每個 PDF 的檔破解、影像和文字解壓縮、影像的光學字元辨識（OCR），以及組織的實體辨識。

本文中所顯示的價格是假設的。 它們是用來說明估計程式。 您的成本可能較低。 如需交易的實際價格，請參閱[認知服務定價](https://azure.microsoft.com/pricing/details/cognitive-services)。

1. 針對文字和影像內容的文件破解，目前文字擷取是免費功能。 針對6000映射，會針對每個已解壓縮的1000影像假設 $1。 這是此步驟的 $6.00 成本。

2. 針對 6,000 個英文影像的 OCR，OCR 認知技能會使用最佳演算法 (DescribeText)。 假設每分析 1,000 個影像的費用為美金 $2.50 元，則在此步驟中您需支付美金 $15.00 元。

3. 針對實體解壓縮，每頁總共會有三個文字記錄。 每筆記錄是 1,000 個字元。 每個頁面乘以6000頁的三個文字記錄等於18000文字記錄。 假設每 1,000 筆文字記錄需要美金 $2.00 元，則此步驟需要美金 $36.00 元。

總之，您需要支付 $57.00，才能使用所述的技能集來內嵌這種類型的 1000 PDF 檔。

## <a name="next-steps"></a>後續步驟
+ [Azure 搜尋服務定價頁面](https://azure.microsoft.com/pricing/details/search/)
+ [如何定義技能集](cognitive-search-defining-skillset.md)
+ [建立技能集 (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [如何對應擴充的欄位](cognitive-search-output-field-mapping.md)
