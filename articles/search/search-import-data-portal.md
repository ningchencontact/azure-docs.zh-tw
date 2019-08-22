---
title: 使用 Azure 入口網站將資料匯入搜尋索引 - Azure 搜尋服務
description: 了解如何在 Azure 入口網站中使用匯入資料精靈，以從 Cosmos DB、Blob 儲存體、表格儲存體、SQL Database 和 Azure VM 上的 SQL Server 對 Azure 資料進行編目。
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: e784cbf351bd062712e0fd66332799907a3bcae8
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648257"
---
# <a name="import-data-wizard-for-azure-search"></a>Azure 搜尋服務的匯入資料精靈

Azure 入口網站在 Azure 搜尋服務儀表板上提供 [匯入資料] 精靈來將資料載入至索引。 在幕後，精靈會設定並叫用「資料來源」、「索引」和「索引子」，將索引編製程序的幾個步驟自動化︰ 

* 從相同的 Azure 訂用帳戶連線至外部資料來源。
* (選擇性) 整合光學字元辨識或自然語言處理，以從非結構化的資料中擷取文字。
* 根據資料取樣和外部資料來源的中繼資料來產生索引。
* 搜耙資料來源中的可搜尋內容，將 JSON 文件序列化並載入到索引中。

精靈無法連線至預先定義的索引，也無法執行現有的索引子，但在精靈中，您可以設定新的索引或索引子來支援您所需的結構和行為。

不熟悉 Azure 搜尋服務嗎？ 逐步執行[快速入門：使用入口網站工具](search-get-started-portal.md)匯入、編制索引和查詢, 以使用匯**入資料**和內建的房地產範例資料集來測試匯入和編制索引。

## <a name="start-importing-data"></a>開始匯入資料

本節說明如何啟動精靈，並提供每個步驟的高階概觀。

1. 在 [Azure 入口網站](https://portal.azure.com)中，從儀表板開啟搜尋服務分頁，或在服務清單中[尋找服務](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。

2. 在頂端的 [服務概觀] 頁面中，按一下 [匯入資料]。

   ![在入口網站匯入資料命令](./media/search-import-data-portal/import-data-cmd2.png "啟動匯入資料精靈")

   > [!NOTE]
   > 您可以從其他 Azure 服務 (包括 Azure Cosmos DB、Azure SQL Database 和 Azure Blob 儲存體) 啟動**匯入資料**。 在 [服務概觀] 頁面上的左側導覽窗格中，尋找 [新增 Azure 搜尋服務]。

3. 精靈會開啟至**連線至您的資料**，以供您選擇要用於這個匯入的外部資料來源。 此步驟有好幾件事要先了解，因此請務必先閱讀[資料來源輸入](#data-source-inputs)一節，以了解更多詳細資料。

   ![入口網站中的匯入資料精靈](./media/search-import-data-portal/import-data-wizard-startup.png "Azure 搜尋服務的匯入資料精靈")

4. 如果您想要包含針對影像檔文字的光學字元辨識 (OCR) 或針對非結構化資料的文字分析功能，接下來是 [新增認知搜尋]。 系統會提取認知服務的人工智慧演算法來進行這項工作。 此步驟分成兩個部分：
  
   首先，對 Azure 搜尋服務技能集[連結認知服務資源](cognitive-search-attach-cognitive-services.md)。
  
   接著，選擇要納入到技能集的 AI 擴充。 如需逐步解說示範，請參閱此[快速入門](cognitive-search-quickstart-blob.md)。

   如果您只想要匯入資料，請略過此步驟，並直接移至索引定義。

5. 接下來是 [自訂目標索引]，您可以在此接受或修改精靈所顯示的索引結構描述。 精靈會對資料取樣並從外部資料來源讀取中繼資料，以推斷欄位和資料類型。

   針對每一個欄位[檢查索引屬性](#index-definition)，以啟用特定行為。 如果您未選取任何屬性，索引將無法使用。 

6. 接下來是 [建立索引子]，這是本精靈的產物。 索引子是一種編目程式，會從外部 Azure 資料來源擷取可搜尋的資料和中繼資料。 藉由選取資料來源和連結技能集 (選擇性) 與索引，您已在逐步進行精靈的過程中設定了索引子。

   對索引子賦予名稱，然後按一下 [提交] 來開始匯入程序。 

您可以在 [索引子] 清單中按一下索引子，以在入口網站中監視索引編製。 隨著文件的載入，您所定義之索引的文件計數將會增加。 有時候入口網站頁面需要幾分鐘的時間來取得最近期的更新。

第一個文件載入後，索引便立即可供查詢。 您可以使用[搜尋總管](search-explorer.md)來進行這項工作。

<a name="data-source-inputs"></a>

## <a name="data-source-inputs"></a>資料來源輸入

**匯入資料**精靈會建立持續性資料來源物件，以指定外部資料來源的連線資訊。 資料來源物件會以獨佔方式與[索引子](search-indexer-overview.md)搭配使用，且可以針對下列資料來源來加以建立： 

* [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure Blob 儲存體](search-howto-indexing-azure-blob-storage.md)
* [Azure 表格儲存體](search-howto-indexing-azure-tables.md) (不支援[認知搜尋](cognitive-search-concept-intro.md)管線)

您只能從單一資料表、資料庫檢視或對等的資料結構匯入, 但結構可以包含階層式或嵌套子結構。 如需詳細資訊, 請參閱[如何建立複雜類型的模型](search-howto-complex-data-types.md)。

在執行 wizard 之前, 您應該先建立此資料結構, 而且它必須包含內容。 請勿在空的資料來源上執行 [匯**入資料**]。

|  選取範圍 | 描述 |
| ---------- | ----------- |
| **現有的資料來源** |如果您的搜尋服務中已經定義索引子，您可以針對另一次匯入選取現有的資料來源定義。 在 Azure 搜尋服務中，只有索引子會使用資料來源物件。 您可以利用程式設計方式或透過**匯入資料**精靈建立資料來源物件。|
| **範例**| Azure 搜尋服務裝載免費的全域 Azure SQL 資料庫, 您可以用來瞭解 Azure 搜尋服務中的匯入和查詢要求。 請參閱[快速入門：使用入口網站工具進行匯入、編製索引和查詢](search-get-started-portal.md)，來取得逐步解說。 |
| **Azure SQL Database** |您可以在頁面上或透過 ADO.NET 連接字串指定服務名稱、具有讀取權限的資料庫使用者認證以及資料庫名稱。 選擇連接字串選項以檢視或自訂屬性。 <br/><br/>提供資料列集的資料表或檢視必須在頁面上指定。 連線成功後會出現此選項，並透過下拉式清單供您選取。 |
| **在 Azure VM 上的 SQL Server** |指定完整服務名稱、使用者識別碼與密碼以及資料庫作為連接字串。 若要使用此資料來源，您必須先前就已在本機存放區中安裝用來加密連線的憑證。 如需指示，請參閱 [SQL VM 到 Azure 搜尋服務的連線](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)。 <br/><br/>提供資料列集的資料表或檢視必須在頁面上指定。 連線成功後會出現此選項，並透過下拉式清單供您選取。 |
| **Cosmos DB** |需求包括帳戶、資料庫和集合。 集合中的所有文件將會包含在索引中。 您可以定義要壓平合併或篩選資料列集的查詢, 或將查詢保留空白。 在此 wizard 中不需要查詢。|
| **Azure Blob 儲存體** |需求包括儲存體帳戶和容器。 如果 Blob 名稱為了分組而遵循虛擬命名慣例，您可以指定名稱的虛擬目錄部分做為容器下的資料夾 (選擇性)。 如需詳細資訊，請參閱[為 Blob 儲存體編製索引](search-howto-indexing-azure-blob-storage.md)。 |
| **Azure 資料表儲存體** |需求包括儲存體帳戶和資料表名稱。 您可以指定要擷取資料表子集的查詢 (選擇性)。 如需詳細資訊，請參閱[為表格儲存體編製索引](search-howto-indexing-azure-tables.md)。 |


<a name="index-definition"></a>

## <a name="index-attributes"></a>索引屬性

**匯入資料**精靈會產生索引，索引中會填入取自輸入資料來源的文件。 

如需功能索引，請確定您已定義下列項目。

1. 必須將一個欄位標示為**金鑰**，以用來唯一識別每個文件。 **金鑰**必須是 Edm.string。 

   如果欄位值包含空格或連字號，您必須在 [建立索引子] 步驟的 [進階選項] 底下設定 [Base-64 編碼金鑰] 選項，以隱藏這些字元的驗證檢查。

1. 設定每個欄位的索引屬性。 如果您未選取任何屬性，索引基本上會是空的，但會有必要的金鑰欄位。 請為每個欄位至少選擇一或多個這些屬性。
   
   + **Retrievable** 會在搜尋結果中傳回欄位。 提供內容來搜尋結果的每個欄位都必須有這個屬性。 設定此欄位對索引大小的影響很小。
   + **Filterable** 允許在篩選運算式中參考欄位。 用於 **$filter** 運算式的每個欄位都必須有這個屬性。 篩選條件運算式會用來進行精準比對。 因為文字字串會維持不變，所以需要額外的儲存體才能容納逐字的內容。
   + **Searchable** 能夠進行全文檢索搜尋。 用於自由格式查詢或查詢運算式的每個欄位都必須有這個屬性。 針對您標示為 **Searchable** 的每個欄位，系統會建立反向索引。

1. (選擇性) 請視需要設定這些屬性：

   + **Sortable** 允許欄位用於排序。 用於 **$Orderby** 運算式的每個欄位都必須有這個屬性。
   + **Facetable** 可讓欄位進行多面向導覽。 只有同時標示為 **Filterable** 的欄位才可以標示為 **Facetable**。

1. 如果您想要語言增強式索引編製和查詢，請設定**分析器**。 預設值是「標準 Lucene」，但如果您想要使用 Microsoft 的分析器來進行進階的語彙處理 (例如，解析不規則的名詞和動詞形式)，則可以選擇「Microsoft 英文」。

   + 選取 [Searchable] 以啟用 [分析器] 清單。
   + 選擇清單中提供的分析器。 
   
   此時只能指定語言分析器。 使用自訂分析器或非語言分析器 (如關鍵字、模式等等) 需要有程式碼。 如需分析器的詳細資訊，請參閱[以多種語言建立文件的索引](search-language-support.md)。

1. 選取 [建議工具] 核取方塊以在選取的欄位上啟用預先輸入的查詢建議。


## <a name="next-steps"></a>後續步驟
檢閱下列連結以深入了解索引子：

* [為 Azure SQL Database 編製索引](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB 編製索引](search-howto-index-cosmosdb.md)
* [為 Blob 儲存體編製索引](search-howto-indexing-azure-blob-storage.md)
* [為表格儲存體編製索引](search-howto-indexing-azure-tables.md)