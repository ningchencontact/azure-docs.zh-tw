---
title: 連結認知服務資源與技能 - Azure 搜尋服務
description: 將認知服務-全方位訂用帳戶連結至 Azure 搜尋服務中的認知擴充管線的指示。
manager: cgronlun
author: LuisCabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 69b03bd24abcdf502bf80cfce4221f4958058932
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/11/2019
ms.locfileid: "65541716"
---
# <a name="attach-a-cognitive-services-resource-with-a-skillset-in-azure-search"></a>在 Azure 搜尋服務中連結認知服務資源與技能 

AI 演算法磁碟機[認知的索引編製管線](cognitive-search-concept-intro.md)用於處理 Azure 搜尋服務中的非結構化的資料。 這些演算法根據[Azure 認知服務資源](https://azure.microsoft.com/services/cognitive-services/)，包括[Computer Vision](https://azure.microsoft.com/services/cognitive-services/computer-vision/)影像分析和光學字元辨識 (OCR) 及[文字分析](https://azure.microsoft.com/services/cognitive-services/text-analytics/)實體辨識、 關鍵片語擷取和其他類。

您可以免費擴充數目有限的文件，或是針對較大型且較頻繁的工作負載附加計費的「認知服務」資源。 本文中，您將學習如何將認知服務資源關聯認知技能來充實資料期間[Azure 搜尋服務編製索引](search-what-is-an-index.md)。

即使您的管線包含認知服務 Api 不相關的技能，您仍然應該附加認知服務資源。 如此一來，就會覆寫您少數類每日限制的免費資源。 您可以免費使用未繫結至認知服務 Api 的技能。 這些技巧包括[自訂技術](cognitive-search-create-custom-skill-example.md)，[文字合併](cognitive-search-skill-textmerger.md)，[文字分隔器](cognitive-search-skill-textsplit.md)，以及[shaper](cognitive-search-skill-shaper.md)。

> [!NOTE]
> 當您展開範圍，藉由增加處理、 新增更多的文件，或新增更多的 AI 演算法的頻率，您必須將可計費的認知服務資源的連結。 您將支付呼叫認知服務中的 Api 和 Azure 搜尋服務中的文件破解階段一部分的映像擷取。 您不會收取從文件的文字擷取。
>
> 執行內建的技巧享有[認知服務付移價格](https://azure.microsoft.com/pricing/details/cognitive-services/)。 如需映像擷取定價資訊，請參閱 < [Azure 搜尋服務定價頁面](https://go.microsoft.com/fwlink/?linkid=2042400)。

## <a name="use-free-resources"></a>使用免費資源

您可以使用有限的免費處理選項，完成認知搜尋教學課程和快速入門練習。

免費 （有限的類） 資源會限制每日、 每個訂閱 20 文件。

1. 開啟 [匯入資料精靈]:

   ![開啟匯入資料精靈](media/search-get-started-portal/import-data-cmd2.png "開啟匯入資料精靈")

1. 選擇資料來源，並繼續**認知搜尋 （選擇性） 新增**。 如需此精靈的逐步解說，請參閱[匯入、 索引和查詢，藉由使用入口網站工具](search-get-started-portal.md)。

1. 依序展開**附加的認知服務**，然後選取**Free （有限類）**:

   ![展開連結的認知服務區段](./media/cognitive-search-attach-cognitive-services/attach1.png "展開連結的認知服務區段")

1. 繼續進行下一個步驟中，**新增類**。 如需技能的入口網站中可用的說明，請參閱[步驟 2:新增認知技術](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline)在認知服務快速入門。

## <a name="use-billable-resources"></a>使用計費資源

建立每日超過 20 個類的工作負載，您要附加的可計費的認知服務資源。

您只需要支付呼叫認知服務 Api 的技能。 您不會收取[自訂技術](cognitive-search-create-custom-skill-example.md)，或是想要的技能[文字合併](cognitive-search-skill-textmerger.md)，[文字分隔器](cognitive-search-skill-textsplit.md)，並[shaper](cognitive-search-skill-shaper.md)，這不是以 API 為基礎。

1. 開啟 [匯入資料精靈]，選擇資料來源，而持續**認知搜尋 （選擇性） 新增**。

1. 依序展開**附加的認知服務**，然後選取**建立新的認知服務資源**。 新的索引標籤隨即開啟，讓您可以建立資源：

   ![建立認知服務資源](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "建立認知服務資源")

1. 在 **位置**清單中，選取您的 Azure 搜尋服務所在的區域。 您要使用此區域，基於效能考量。 使用此區域也，則不必輸出頻寬費用跨區域。

1. 在 **定價層**清單中，選取**S0**取得認知服務功能，包括回預先定義的技術，Azure 搜尋服務所使用的願景和語言功能-全方位集合。

   S0 層中，您可以找到費率適用於特定工作負載[認知服務定價頁面](https://azure.microsoft.com/pricing/details/cognitive-services/)。
  
   + 在 **選取提供**清單中，請確定**認知服務**已選取。
   + 底下**語言**功能，如費率**文字分析標準**套用至 AI 編製索引。
   + 底下**願景**功能，如費率**電腦願景 S1**套用。

1. 選取 **建立**佈建這個新的認知服務資源。

1. 返回上一個索引標籤，其中包含匯入資料精靈。 選取 **重新整理**以顯示 認知服務資源，然後選取 資源：

   ![選取 認知服務資源](./media/cognitive-search-attach-cognitive-services/attach2.png "選取認知服務資源")

1. 依序展開**新增類**一節，以選取您想要在您的資料上執行的特定認知技能。 完成精靈的其餘部分。 如需技能的入口網站中可用的說明，請參閱[步驟 2:新增認知技術](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline)在認知服務快速入門。

## <a name="attach-an-existing-skillset-to-a-cognitive-services-resource"></a>將現有的技能附加至認知服務資源

如果您有現有的技能，就可以將其附加至新的或不同的「認知服務」資源。

1. 在 **服務概觀**頁面上，選取**技能**:

   ![技能索引標籤](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "技能索引標籤")

1. 選取名稱的技能組合，然後選取現有的資源，或建立新的。 按一下 [確定] 確認變更。

   ![技能資源清單](./media/cognitive-search-attach-cognitive-services/attach-existing2.png "技能資源清單")

   請記住**免費 （有限類）** 選項限制為 20 的文件每日，並可供您**建立新的認知服務資源**佈建一個新的計費資源。 如果您建立新資源，請選取 [重新整理] 以重新整理「認知服務」資源的清單，然後選取資源。

## <a name="attach-cognitive-services-programmatically"></a>透過程式設計方式附加認知服務

透過程式設計方式定義技能時，請將 `cognitiveServices` 區段新增至該技能。 在該區段中，包含您想要關聯技能與認知服務資源的索引鍵。 請記住，資源必須位於與您的 Azure 搜尋服務資源相同的區域。 以及包含 `@odata.type`，並將它設定為 `#Microsoft.Azure.Search.CognitiveServicesByKey`。

下列範例示範了此模式。 請注意`cognitiveServices`定義的最後一節。

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

若要估計與認知搜尋編製索引相關聯的成本，開始了解什麼的平均文件看起來像讓您能夠執行一些數字。 例如，您可能會近似：

+ 1,000 的 Pdf。
+ 六個頁面每個。
+ 每一頁 （6,000 映像） 的一個映像。
+ 每一頁的 3,000 個字元。

假設每個 PDF、 影像和文字光學字元辨識 (OCR) 的映像、 擷取、 文件破解和實體辨識的組織所組成的管線。

這篇文章中所示的價格是假設的。 它們用來說明此估計程序。 您的成本可能較低。 針對交易的實際價格，請參閱 <<c0> [ 認知服務定價](https://azure.microsoft.com/pricing/details/cognitive-services)。

1. 針對文字和影像內容的文件破解，目前文字擷取是免費功能。 針對 6000 的映像，假設 $1 表示擷取每 1,000 個映像。 這是此步驟中的 $6.00 的成本。

2. 針對 6,000 個英文影像的 OCR，OCR 認知技能會使用最佳演算法 (DescribeText)。 假設每分析 1,000 個影像的費用為美金 $2.50 元，則在此步驟中您需支付美金 $15.00 元。

3. 針對實體擷取，您必須使用每一頁的三個文字記錄的總計。 每筆記錄是 1,000 個字元。 三個文字每頁記錄數乘以 6,000 頁面等於 18000 的文字記錄。 假設每 1,000 筆文字記錄需要美金 $2.00 元，則此步驟需要美金 $36.00 元。

總結，您會支付大約 $57.00 內嵌 1,000 的 PDF 文件所述的技能組合使用這種。

## <a name="next-steps"></a>後續步驟
+ [Azure 搜尋服務定價頁面](https://azure.microsoft.com/pricing/details/search/)
+ [如何定義技能集](cognitive-search-defining-skillset.md) (英文)
+ [建立技能集 (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [如何對應擴充的欄位](cognitive-search-output-field-mapping.md)
