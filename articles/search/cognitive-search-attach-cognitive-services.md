---
title: 對技能集附加認知服務 - Azure 搜尋服務
description: 將認知服務多合一訂用帳戶附加至認知技能集的指示
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 7f604d1b94e51db11e6d6992b7f070d64ae869dd
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/12/2018
ms.locfileid: "53317231"
---
# <a name="associate-cognitive-services-resource-with-a-skillset"></a>將認知服務資源與技能集建立關聯 

> [!NOTE]
> 從 2018 年 12 月 21 日開始，您可以在認知服務資源與 Azure 搜尋服務的技能集之間建立關聯。 這可讓我們開始收取執行技能集的費用。 自這個日期起，我們也會開始收取文件萃取階段的影像擷取費用。 從文件中擷取文字的功能則繼續免費提供。
>
> 內建技能的執行會依現行的[認知服務隨用隨附價格](https://azure.microsoft.com/pricing/details/cognitive-services/)收費。 影像擷取定價會依預覽定價收費，如 [Azure 搜尋服務定價頁面](https://go.microsoft.com/fwlink/?linkid=2042400)所述。


認知搜尋會擷取並擴充資料，使其在 Azure 搜尋服務中可供搜尋。 我們將擷取和擴充步驟稱為「認知技能」。 在內容編製索引期間呼叫的一組技能，定義於「技能集」中。 技能集可以使用[預先定義的技能](cognitive-search-predefined-skills.md)或自訂技能 (如需詳細資訊，請參閱[範例：建立自訂技能](cognitive-search-create-custom-skill-example.md))。

在本文中，您可了解如何將[認知服務](https://azure.microsoft.com/services/cognitive-services/)資源與認知技能集建立關聯。

您選取的認知服務資源可強化內建的認知技能。 此資源也將用於計費用途。 您使用內建認知技術執行的任何擴充，都會按照您選取的認知服務資源進行計費。 其計費的費率相同，如同您使用認知服務資源執行相同的工作。 請參閱[認知服務價格](https://azure.microsoft.com/pricing/details/cognitive-services/)。

## <a name="limits-when-no-cognitive-services-resource-is-selected"></a>未選取任何認知服務資源時的限制
從 2019 年 2 月 1 日開始，如果您未將認知服務訂用帳戶與您的技能集建立關聯，將只能免費擴充少量文件 (每天 20 份文件)。 

## <a name="associating-a-cognitive-services-resource-with-a-new-skillset"></a>將認知服務資源與新技能集建立關聯

1. 在 [匯入資料]體驗期間，與資料來源連線之後，您會瀏覽到 [新增認知搜尋]選擇性步驟。 

1. 請展開 [附加認知服務] 區段。 這會顯示在和搜尋服務相同的區域中，您擁有的任何認知服務資源。 
![展開的附加認知服務](./media/cognitive-search-attach-cognitive-services/attach1.png "展開的附加認知服務")

1. 選取現有的認知服務資源，或 [建立新的認知服務資源]。 如果您選取 [免費 (有限的擴充)] 資源，您只能免費擴充少量文件 (每天 20 份文件)。 如果您按一下 [建立新的認知服務資源]，隨即開啟的新索引標籤可讓您建立認知服務資源。 

1. 如果您建立新的資源，請按一下 [重新整理]，來重新整理認知服務資源的清單，然後選取資源。 
![選取的認知服務資源](./media/cognitive-search-attach-cognitive-services/attach2.png "選取的認知服務資源")

1. 您已如此做之後，可以展開 [新增擴充] 區段，以選取您想要對資料執行的特定認知技能，並繼續進行其餘的流程。

## <a name="associating-a-cognitive-services-resource-with-an-existing-skillset"></a>將認知服務資源與現有技能集建立關聯

1. 在 [服務概觀] 頁面中，選取 [技能集] 索引標籤。![技能索引標籤](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "技能索引標籤")

1. 按一下您要修改的技能集。 這樣會開啟刀鋒視窗，讓您編輯技能集。

1. 選取現有的認知服務資源，或 [建立新的認知服務資源]。 如果您選取 [免費 (有限的擴充)] 資源，您只能免費擴充少量文件 (每天 20 份文件)。 如果您按一下 [建立新的認知服務資源]，隨即開啟的新索引標籤可讓您建立認知服務資源。 <n/> 
<img src="./media/cognitive-search-attach-cognitive-services/attach-existing2.png" width="350">

1. 如果您建立新的資源，請按一下 [重新整理]，來重新整理認知服務資源的清單，然後選取資源。
1. 按一下 [確定] 確認變更

## <a name="associating-a-cognitive-services-resource-programmatically"></a>以程式設計方式與認知服務資源建立關聯

當以程式設計方式定義技能集時，請新增 `cognitiveServices` 區段。 該區段應包含認知服務資源的索引鍵，以供與技能集建立關聯，以及包含 @odata.type，而其應設定為 "#Microsoft.Azure.Search.CognitiveServicesByKey"。 此模式如下列範例所示。

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
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey"
        "description": "mycogsvcs",
        "key": "your key goes here"
    }
}
```
## <a name="example-estimating-the-cost-of-document-cracking-and-enrichment"></a>範例：評估文件萃取與擴充的成本
您可能想要評估擴充指定類型的文件會花費多少成本。 以下練習只是範例，但可能會對您很有幫助。

假設我們有 1000 個 PDF，而我們預估這批的每一份文件平均都有 6 頁。 若是每一份的每一頁都有一個影像，以供此練習使用。 同時也假設每個頁面平均約有 3,000 個字元。 

現在，假設我們想要在擴充管線時執行下列步驟：
1. 在文件萃取期間，擷取文件中的內容與影像。
1. 在擴充期間，OCR 所擷取的每一頁，結合所有頁面的文字，然後在所有影像的結合文字中擷取每一個組織。

讓我們來逐步評估若要內嵌這些文件需要花費多少成本。

對於 1000 份文件：

1. 在文件萃取方面，我們會擷取總共 6000 個影像。 假設每擷取 1000 個影像要 $1 美元，所以此項的成本為 $6.00 美元。

2. 我們會從這 6000 個影像當中逐個擷取文字。 使用英文時，OCR 認知技能會使用最佳演算法 (DescribeText)。 假設每分析 1,000 個影像的成本為 $2.50 美元，因此我們要為此步驟支付 $15.00 美金。

3. 對於實體擷取，每一頁共有 3 筆文字記錄 (每筆記錄為 1,000 個字元)。 3 筆文字記錄/頁面 * 6,000 頁 = 18,000 筆文字記錄。 假設 $2.00 美元 / 1000 筆文字記錄，則此步驟的成本為 $36.00 美元。

總結來看，使用所述的技能集來內嵌上述性質的 1000 份 PDF 文件，一共要支付 $57.00 美元。  在此練習中，我們假設每筆交易都以最高的價格計算，其可能因為晉級而使價格較低。 請參閱[認知服務價格](https://azure.microsoft.com/pricing/details/cognitive-services)。



## <a name="next-steps"></a>後續步驟
+ [Azure 搜尋服務價格頁面](https://azure.microsoft.com/pricing/details/search/)
+ [如何定義技能集](cognitive-search-defining-skillset.md) (英文)
+ [建立技能集 (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [如何對應擴充的欄位](cognitive-search-output-field-mapping.md)
