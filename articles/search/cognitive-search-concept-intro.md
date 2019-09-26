---
title: 認知搜尋和 AI 擴充的簡介 - Azure 搜尋服務
description: 可使用認知技能和 AI 演算法在 Azure 搜尋服務索引中建立可搜尋內容的內容擷取、自然語言處理 (NLP) 和影像處理。
manager: nitinme
author: HeidiSteen
services: search
ms.service: search
ms.topic: overview
ms.date: 08/15/2019
ms.author: heidist
ms.openlocfilehash: aa8ab3a3bc2f243e8b0ee404228926ef46bc46db
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2019
ms.locfileid: "71266382"
---
# <a name="what-is-cognitive-search-in-azure-search"></a>什麼是 Azure 搜尋服務的「認知搜尋」？

認知搜尋是 Azure 搜尋服務中的 AI 功能，可用來從影像、Blob 及其他非結構化的資料來源擷取文字，以擴充其內容並使它更容易在 Azure 搜尋服務索引中被找到。 系統會透過附加到索引管線的「認知技術」  來進行擷取和擴充。 AI 擴充是以下列方式進行支援： 

+ **自然語言處理**技術，包括[實體辨識](cognitive-search-skill-entity-recognition.md)、[語言偵測](cognitive-search-skill-language-detection.md)、[關鍵片語擷取](cognitive-search-skill-keyphrases.md)、文字操作和[情感偵測](cognitive-search-skill-sentiment.md)。 透過這些技術，非結構化的文字將能取得新的型態，並對應為索引中可搜尋且可篩選的欄位。

+ **影像處理**技術，包括[光學字元辨識 (OCR)](cognitive-search-skill-ocr.md) 和[視覺特徵](cognitive-search-skill-image-analysis.md)的識別，例如臉部偵測、影像轉譯、影像辨識 (名人和地標)，或是色彩或影像方向之類的屬性。 您可以為影像內容建立可使用 Azure 搜尋服務的各種查詢功能來搜尋的文字表示法。

![認知搜尋管線圖](./media/cognitive-search-intro/cogsearch-architecture.png "認知搜尋管線概觀")

Azure 搜尋服務中的認知技術是以認知服務 API 中的機器學習模型為基礎：[電腦視覺](https://docs.microsoft.com/azure/cognitive-services/computer-vision/)和[文字分析](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview)。 

在資料擷取階段中會套用自然語言和影像處理，且其結果會在 Azure 搜尋服務的可搜尋索引中成為文件撰寫的一部分。 資料會作為 Azure 資料集的來源，然後使用您所需的[內建技能](cognitive-search-predefined-skills.md)透過索引管線推送出去。 其架構是可延伸的，因此如果內建技能不敷使用，您可以建立及附加[自訂技能](cognitive-search-create-custom-skill-example.md)，以整合自訂處理。 其範例包括以特定領域為目標 (例如金融、科學出版品或醫藥) 的自訂實體模組或文件分類器。

> [!NOTE]
> 當您透過增加處理頻率、新增更多文件或新增更多 AI 演算法來擴展範圍時，您必須[連結可計費的認知服務資源](cognitive-search-attach-cognitive-services.md)。 在認知服務中呼叫 API，以及在 Azure 搜尋服務的文件萃取階段中擷取影像時，都會產生費用。 從文件中擷取文字不會產生費用。
>
> 內建技能的執行會依現有的[認知服務預付型方案價格](https://azure.microsoft.com/pricing/details/cognitive-services/)收費。 影像擷取定價的說明請見 [Azure 搜尋服務價格頁面](https://go.microsoft.com/fwlink/?linkid=2042400)。

## <a name="when-to-use-cognitive-search"></a>使用認知搜尋的時機

使用預先建置技能的認知搜尋非常適用於下列應用程式案例：

+ 想要使其成為可供全文檢索搜尋的掃描文件 (JPEG)。 您可以連結光學字元辨識 (OCR) 技能以識別、擷取和內嵌 JPEG 檔案中的文字。

+ 結合了影像和文字的 PDF。 在進行 Azure 搜尋服務索引編制期間，可以在不使用認知搜尋的情況下擷取 PDF 中的文字，但新增影像和自然語言處理往往能夠產生比標準索引編制程序還要好的結果。

+ 想要對其套用語言偵測和 (可能的話) 文字轉譯的多語言內容。

+ 非結構化或半結構化的文件，其含有具有內在意義的內容或隱藏在較大型文件中的內容。 

  尤其是 Blob 往往會包含封裝到單一「欄位」的大型內容主體。 藉由將影像和自然語言處理技能連結至索引子，您可以建立現存於原始內容但又不會呈現為相異欄位的新資訊。 某些立即可用、對您有所幫助的內建認知技能：關鍵片語擷取、情感分析和實體辨識 (人員、組織和位置)。

  此外，預先建置的技能也可用來透過文字分割、合併和成形作業來重新建構內容。

自訂技能可支援更複雜的案例 (例如辨識表單) 或使用您在[自訂技能 Web 介面](cognitive-search-custom-skill-interface.md)中提供及包裝的模型來進行自訂實體偵測。 自訂技能的幾個範例包括[表單辨識器](/azure/cognitive-services/form-recognizer/overview)、[Bing 實體搜尋 API](https://docs.microsoft.com/azure/search/cognitive-search-create-custom-skill-example) 的整合，以及[自訂實體辨識](https://github.com/Microsoft/SkillsExtractorCognitiveSearch)。


## <a name="component-pipeline-of-cognitive-search"></a>認知搜尋的元件管線

認知搜尋管線以會搜耙資料來源並提供端對端索引處理的 [Azure 搜尋服務*索引子*](search-indexer-overview.md)為基礎。 技能現在已連結至索引子，會根據您所定義的技能集攔截及擴充文件。 完成索引編製後，您即可使用 [Azure 搜尋服務所支援的所有查詢類型](search-query-overview.md)，透過搜尋要求來存取內容。  如果您不熟悉索引子，本節將引導您逐步完成相關步驟。

### <a name="step-1-connection-and-document-cracking-phase"></a>步驟 1：連線和文件萃取階段

在一開始執行管線時，您會有非結構化的文字或非文字內容 (例如影像和掃描的文件 JPEG 檔案)。 資料必須存在於可由索引子存取的 Azure 資料儲存體服務中。 索引子可以「萃取」來源文件，以從來源資料中擷取文字。

![文件萃取階段](./media/cognitive-search-intro/document-cracking-phase-blowup.png "文件萃取")

 支援的來源包括 Azure Blob 儲存體、Azure 資料表儲存體、Azure SQL Database 和 Azure Cosmos DB。 文字型的內容可以從下列檔案類型中擷取：PDF、Word、PowerPoint、CSV 檔案。 如需完整的清單，請參閱[支援的格式](search-howto-indexing-azure-blob-storage.md#supported-document-formats)。

### <a name="step-2-cognitive-skills-and-enrichment-phase"></a>步驟 2：認知技能和擴充階段

擴充是經由*認知技能*執行不可部分完成作業而進行的。 例如，當您從 PDF 取得文字內容後，您可以套用實體辨識語言偵測或關鍵片語擷取，在您的索引中產生在來源中原本無法使用的新欄位。 技能的集合會統合用於您的管線中，我們稱之為*技能集*。  

![擴充階段](./media/cognitive-search-intro/enrichment-phase-blowup.png "擴充階段")

技能集以您所提供並連線至技能集的[預先定義的認知技能](cognitive-search-predefined-skills.md)或[自訂技能](cognitive-search-create-custom-skill-example.md)為基礎。 技能集可以是基本或高度複雜的，而且不但會決定處理類型，作業的順序也取決於它。 技能集加上定義為索引子一部分的欄位對應，即可完整指定擴充管線。 如需關於彙整前述各項組件的詳細資訊，請參閱[定義技能集](cognitive-search-defining-skillset.md)。

就內部而言，管線會產生擴充文件的集合。 您可以決定擴充文件的哪些部分應對應至搜尋索引中可編製索引的欄位。 例如，如果您套用關鍵片語擷取和實體辨識技能，則這些新欄位將會成為擴充文件的一部分，並且可對應至索引上的欄位。 若要深入了解輸入/輸出格式，請參閱[註解](cognitive-search-concept-annotations-syntax.md)。

#### <a name="add-a-knowledgestore-element-to-save-enrichments"></a>新增 knowledgeStore 元素以儲存擴充

[搜尋服務 REST api-version=2019-05-06](search-api-preview.md) 可利用提供 Azure 儲存體連線的 knowledgeStore 定義，以及描述擴充儲存方式的投影，來擴展技能集。 

將知識存放區新增到技能集時，讓您能夠針對非全文檢索搜尋案例，投射擴充的表示法。 如需詳細資訊，請參閱[什麼是知識存放區](knowledge-store-concept-intro.md)。

### <a name="step-3-search-index-and-query-based-access"></a>步驟 3：搜尋索引和以查詢為基礎的存取

處理完成後，您即具有由擴充文件組成、可在 Azure 搜尋服務中進行全文檢索搜尋的搜尋索引。 [查詢索引](search-query-overview.md)是開發人員和使用者存取管線所產生之擴充內容的具體方式。 

![搜尋的索引圖示](./media/cognitive-search-intro/search-phase-blowup.png "搜尋的索引圖示")

索引就像任何其他您可能為 Azure 搜尋服務建立的項目一樣：您可以使用自訂分析器進行補強、叫用模糊搜尋查詢、新增篩選搜尋，或試用評分設定檔以調整搜尋結果。

索引是從定義連結至特定索引的欄位、屬性和其他建構的索引結構描述產生的，例如評分設定檔和同義字對應。 在定義並填入索引後，而且會擴展，您即可以累加方式編製索引，以收取新的和更新的來源文件。 特定的修改需要完整重建。 在結構描述設計趨於穩定之前，您應使用小型資料集。 如需詳細資訊，請參閱[如何重建索引](search-howto-reindex.md)。

<a name="feature-concepts"></a>

## <a name="key-features-and-concepts"></a>重要功能與概念

| 概念 | 說明| 連結 |
|---------|------------|-------|
| 技能集 | 包含技能集合的最上層具名資源。 技能集是擴充管線。 在索引子編製索引期間，即會叫用技能集。 | [定義技能集](cognitive-search-defining-skillset.md) |
| 認知技能 | 擴充管線中不可部分完成的轉換。 它通常是一種擷取或推斷結構的元件，因此能提高我們對輸入資料的了解。 輸出幾乎都是以文字為基礎，而處理則是自然語言處理，或是從影像輸入擷取或產生的文字的影像處理。 技能的輸出可以對應至索引中的欄位，或作為下游擴充的輸入。 技能可以是 Microsoft 預先定義並提供的，或是自訂的：由您建立和部署。 | [預先定義的技能](cognitive-search-predefined-skills.md) |
| 資料擷取 | 涵蓋多種形式的處理，但與認知搜尋有關，實體辨識技能最常用來從原本未提供具體資訊的來源擷取資料 (實體)。 | [實體辨識技能](cognitive-search-skill-entity-recognition.md)| 
| 影像處理 | 從影像推斷文字 (例如辨識地標的能力)，或從影像擷取文字。 常見的範例包括從掃描的文件 (JPEG) 檔案中挑取字元的 OCR，或是在包含路標的相片中辨識街道名稱。 | [影像分析技能](cognitive-search-skill-image-analysis.md)或 [OCR 技能](cognitive-search-skill-ocr.md)
| 自然語言處理 | 文字輸入的相關深入解析和資訊的文字處理。 語言偵測、情感分析和關鍵片語擷取都是屬於自然語言處理的技能。  | [關鍵片語擷取技能](cognitive-search-skill-keyphrases.md)、[語言偵測技能](cognitive-search-skill-language-detection.md)、[情感分析技能](cognitive-search-skill-sentiment.md) |
| 文件萃取 | 在索引編製期間從非文字來源擷取或建立文字內容的程序。 光學字元辨識 (OCR) 也是範例之一，但它通常指涉索引子核心功能，因為索引子會從應用程式檔案中擷取內容。 提供來源檔案位置的資料來源，與提供欄位對應的索引子定義，都是文件萃取的關鍵因素。 | 請參閱[索引子](search-indexer-overview.md)。 |
| 塑形 | 將文字片段合併成較大的結構，或者反向將較大的文字區塊分解成可管理的大小，以進行進一步的下游處理。 | [塑形器技能](cognitive-search-skill-shaper.md)、[文字合併技能](cognitive-search-skill-textmerger.md)、[文字分割技能](cognitive-search-skill-textsplit.md) |
| 擴充的文件 | 在處理期間產生的暫時性內部結構，包含搜尋索引中反映的最後輸出。 技能集會決定要執行哪些擴充。 欄位對應將決定哪些資料元素會新增至索引。 (選擇性) 您可以建立知識存放區，以使用儲存體總管、Power BI 或連結到 Azure Blob 儲存體的任何其他工具來保存及探索擴充的文件。 | 請參閱[知識存放區 (預覽)](knowledge-store-concept-intro.md)。 |
| 索引器 |  一種編目程式，可從外部資料來源擷取可搜尋的資料和中繼資料，並根據索引和資料來源之間的欄位對欄位對應填入索引，以進行文件萃取。 在進行認知搜尋擴充時，索引子會叫用技能集，並且包含將擴充輸出與索引中的目標欄位產生關聯的欄位對應。 索引子定義中包含管線作業的所有指示和參考，當您執行索引子時，即會叫用管線。 | [索引子](search-indexer-overview.md) |
| 資料來源  | 索引子用來與 Azure 上支援的外部資料來源類型連線的物件。 | 請參閱[索引子](search-indexer-overview.md)。 |
| 索引 | 從定義欄位結構和使用方式的索引結構描述建置，並保存在 Azure 搜尋服務中的搜尋索引。 | [Azure 搜尋服務中的索引](search-what-is-an-index.md) | 

<a name="where-do-i-start"></a>

## <a name="where-do-i-start"></a>我該從哪裡開始？

**步驟 1：[建立 Azure 搜尋服務索引](search-create-service-portal.md)** 

**步驟 2：請嘗試一些快速入門和範例，以獲得實際操作經驗**

+ [快速入門 (入口網站)](cognitive-search-quickstart-blob.md)
+ [教學課程 (HTTP 要求)](cognitive-search-tutorial-blob.md)
+ [範例：建立認知搜尋的自訂技能 (C#)](cognitive-search-create-custom-skill-example.md)

基於學習目的，我們會建議使用免費服務，但可用的交易數目限制為每天 20 份文件。 若要在一天中執行快速入門和教學課程，請使用較小的檔案集 (10 份文件)，如此才能在限制內完成這兩項練習，否則請刪除您在快速入門或教學課程中使用的索引子。

**步驟 3：檢閱 API**

您可以在要求或 .NET SDK 上使用 REST `api-version=2019-05-06`。 

此步驟使用 REST API 建置認知搜尋解決方案。 針對認知搜尋僅新增或擴充了兩個 API。 其他 API 的語法與公開上市的版本相同。

| REST API | 說明 |
|-----|-------------|
| [建立資料來源](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | 一項資源，用以識別提供來源資料以建立擴充文件的外部資料來源。  |
| [建立技能集 (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | 此 API 專供認知搜尋使用。 其為在索引編製期間負責對擴充管線中使用的[預先定義的技能](cognitive-search-predefined-skills.md)和[自訂認知技能](cognitive-search-custom-skill-interface.md)進行用法協調的資源。 |
| [建立索引](https://docs.microsoft.com/rest/api/searchservice/create-index)  | 表示 Azure 搜尋服務索引的結構描述。 索引中與來源資料中的欄位或在擴充階段產生的欄位 (例如，實體辨識所建立之組織名稱的欄位) 相對應的欄位。 |
| [建立索引子 (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | 一項資源，用以定義在索引編製期間所使用的元件：包括資料來源、技能集、來源和中繼資料結構與目標索引的欄位關聯性，以及索引本身。 執行索引子是擷取和擴充資料的觸發程序。 輸出是以索引結構描述為基礎、以來源資料填入，並透過技能集擴充的搜尋索引。 此現有 API 已經過擴充而可用於納入技能集屬性的認知搜尋案例。 |

**檢查清單：一般工作流程**

1. 將您的 Azure 來源資料分成代表性範例子集。 索引編製會從小型、具代表性的資料集展開，然後再隨著解決方案的成熟而逐漸補強。

1. 在 Azure 搜尋服務中建立[資料來源物件](https://docs.microsoft.com/rest/api/searchservice/create-data-source)，以提供用來擷取資料的連接字串。

1. 透過擴充步驟建立[技能集](https://docs.microsoft.com/rest/api/searchservice/create-skillset)。

1. 定義[索引結構描述](https://docs.microsoft.com/rest/api/searchservice/create-index)。 *欄位*集合包含來源資料中的欄位。 您也應該清除多餘的欄位，以保存為擴充期間建立的內容產生的值。

1. 定義參考資料來源、技能集和索引的[索引子](https://docs.microsoft.com/rest/api/searchservice/create-skillset)。

1. 在索引子內新增 *outputFieldMappings*。 這部分會將技能集的輸出 (在步驟 3 中) 對應至索引結構描述中的輸入欄位 (在步驟 4 中)。

1. 傳送您剛剛建立的*建立索引子*要求 (在要求本文中具有索引子定義的 POST 要求)，以表示 Azure 搜尋服務中的索引子。 此步驟說明您叫用管線以執行索引子的方式。

1. 執行查詢以評估結果，並修改程式碼以更新技能集、結構描述或索引子組態。

1. 在[重設索引子](search-howto-reindex.md)後重建管線。

如需關於特定問題的詳細資訊，請參閱[疑難排解提示](cognitive-search-concept-troubleshooting.md)。

## <a name="next-steps"></a>後續步驟

+ [認知搜尋文件](cognitive-search-resources-documentation.md)
+ [快速入門：在入口網站中逐步試用認知搜尋](cognitive-search-quickstart-blob.md)
+ [教學課程：了解認知搜尋 API](cognitive-search-tutorial-blob.md)
+ [知識存放區概觀](knowledge-store-concept-intro.md)
+ [知識存放區逐步解說](knowledge-store-howto.md)
