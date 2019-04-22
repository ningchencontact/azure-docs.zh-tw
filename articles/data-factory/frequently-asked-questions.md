---
title: Azure Data Factory：常見問題集 | Microsoft Docs
description: 獲得 Azure Data Factory 常見問題的解答。
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 532dec5a-7261-4770-8f54-bfe527918058
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: shlo
ms.openlocfilehash: d704c32ee7417c6460ad6cc880e451adddfa61de
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59048209"
---
# <a name="azure-data-factory-faq"></a>Azure Data Factory 常見問題集
本文提供 Azure Data Factory 常見問題集的解答。  

## <a name="what-is-azure-data-factory"></a>Azure 資料處理站是什麼？ 
Data Factory 是完全受控並以雲端為基礎的資料整合服務，用來自動化資料的移動和轉換。 就像會運轉設備以將原物料轉換成成品的工廠一樣，Azure Data Factory 會協調現有的服務來收集未經處理資料，並將其轉換成隨時可用的資訊。 

您可使用 Azure Data Factory 建立資料驅動工作流程，以在內部部署與雲端資料存放區之間移動資料。 您可以處理和轉換資料，使用計算服務，例如 Azure HDInsight、 Azure Data Lake Analytics 和 SQL Server Integration Services (SSIS) 整合執行階段。 

透過 Data Factory，能夠在以 Azure 為基礎的雲端服務上執行資料處理，也可以使用自己的自我託管計算環境 (例如 SSIS、SQL Server 或 Oracle)。 建立管線，以執行您所需要的動作之後，您可以排定讓它定期執行 （每小時、 每天或每週，例如） 的時間範圍排程，或觸發的管線，在事件發生。 如需詳細資訊，請參閱 [Azure Data Factory 簡介](introduction.md)。

### <a name="control-flows-and-scale"></a>控制流程和規模 
若要支援的各種整合流程和模式，在新式資料倉儲中，Data Factory 可讓彈性資料管線模型。 這需要完整的控制流程程式設計範例，其中包括條件式執行、 分支中的資料管線，以及明確地傳遞參數內及跨這些流程的能力。 控制流程也會包含轉換活動分派到外部的執行引擎和資料流程功能，包括透過 「 複製活動的規模的資料移動到的資料。

Data Factory 可讓您自由地為資料整合所需的任何流程樣式建立模型。這些流程樣式可依需求分派或是依排程重複分派。 以下為此模型支援的一些常見流程：   

- 控制流程：
    - 活動可以鏈結在一起在管線中的序列。
    - 可將管線內分支的活動。
    - 參數：
        - 參數可以定義管線層級，並叫用管線依需求或從觸發程序時，就可以傳遞引數。
        - 活動可以取用傳遞給管線的引數。
    - 自訂狀態傳遞：
        - 活動輸出，包括狀態，可供管線中後續的活動。
    - 迴圈容器：
        - Foreach 活動會逐一查看指定之集合的迴圈中的活動。 
- 以觸發程序為基礎的流程：
    - 可依需求或依時鐘時間觸發管線。
- 差異流程：
    - 參數可用來從關聯式存放區，在內部部署或雲端，以將資料載入至 lake 移動維度或參考資料表時定義為差異複本-上限標準。 

如需詳細資訊，請參閱[教學課程：控制流程](tutorial-control-flow.md)。

### <a name="data-transformed-at-scale-with-code-free-pipelines"></a>在規模的無程式碼的管線轉換的資料
以瀏覽器為基礎的工具可讓使用者透過新式的互動式網頁，進行無程式碼管線的撰寫與開發。

視覺化資料開發人員及資料工程師，Data Factory web UI 會是您將用來建置管線的免程式碼設計環境。 它與 Visual Studio Online 的 Git 完全整合，並提供 CI/CD 整合與反覆開發與偵錯選項。

### <a name="rich-cross-platform-sdks-for-advanced-users"></a>豐富的跨平台 Sdk 適用於進階使用者
Data Factory V2 提供一組豐富的 Sdk，可用來撰寫、 管理及監視管線，使用您最喜愛的 IDE，包括：
* Python SDK
* PowerShell CLI
* C# SDK

使用者也可以使用文件的 REST Api 來與 Data Factory V2。

### <a name="iterative-development-and-debugging-by-using-visual-tools"></a>進行反覆開發與偵錯使用 visual tools
Azure Data Factory 的視覺化工具可讓進行反覆開發與偵錯。 您可以建立您的管線，並執行測試回合使用**偵錯**管線畫布上，而不需要撰寫一行程式碼的功能。 您可以檢視中的測試回合的結果**輸出**管線畫布的視窗。 您的測試回合成功之後，您可以將更多活動新增至您的管線，並繼續以反覆方式偵錯。 您也可以取消測試回合之後進行中。 

您不需要將變更發佈至 data factory 服務，然後再選取**偵錯**。 這是很有幫助您要確保新增或變更，將會運作如預期般運作，才能更新資料處理站工作流程開發、 測試或生產環境中。 

### <a name="ability-to-deploy-ssis-packages-to-azure"></a>將 SSIS 套件部署至 Azure 的能力 
如果您想要移動 SSIS 工作負載，可以建立 Data Factory，然後佈建 Azure-SSIS 整合執行階段。 Azure SSIS 整合執行階段是完全受控的叢集，專門用來在雲端中執行您的 SSIS 套件的 Azure vm （節點）。 如需逐步指示，請參閱教學課程：[將 SSIS 封裝部署至 Azure](tutorial-create-azure-ssis-runtime-portal.md)。 
 
### <a name="sdks"></a>SDK
如果您是進階的使用者且正在尋找程式設計介面，Data Factory 提供一組豐富的 Sdk，可用來撰寫、 管理或使用您最喜愛的 IDE 來監視管線。 .NET、PowerShell、Python 和 REST 皆提供語言支援。

### <a name="monitoring"></a>監視
您可以在瀏覽器使用者介面中透過 PowerShell、SDK 或視覺監視工具來監視自己的 Data Factory。 您可以監視，並以有效率且效益的方式管理依需求、 觸發程序為基礎和時鐘導向自訂的流程。 取消現有的工作，請參閱失敗，看一眼，向下切入以取得詳細的錯誤訊息，並偵錯問題，所有從沒有內容切換，或瀏覽來回螢幕之間的單一窗格。 

### <a name="new-features-for-ssis-in-data-factory"></a>適用於 Data Factory 中的 SSIS 新功能
因為初始公開預覽版本在 2017年中的，Data Factory 有適用於 SSIS 新增了下列功能：

-   多個組態/變體來裝載 SSIS 資料庫 (SSISDB) 的專案/套件的 Azure SQL Database 支援三個：
-   SQL Database 以虛擬網路服務端點
-   受控執行個體
-   彈性集區
-   傳統虛擬網路上被取代在未來，它可讓您的 Azure Resource Manager 虛擬網路支援插入/加入您的 Azure SSIS 整合執行階段使用虛擬網路服務設定為 SQL Database 的虛擬網路結束點/英哩/內部部署資料的存取。 如需詳細資訊，請參閱[將 Azure SSIS 整合執行階段加入虛擬網路](join-azure-ssis-integration-runtime-virtual-network.md)。
-   Azure Active Directory (Azure AD) 驗證和 SQL 驗證才能連線至 SSISDB，可讓您的 Data Factory 受控身分識別，適用於 Azure 資源與 Azure AD 驗證的支援
-   將您自己的內部部署 SQL Server 授權，而獲得大幅的成本節約，從 [Azure Hybrid Benefit] 選項的支援
-   可讓您的 Azure SSIS 整合執行階段的 Enterprise edition 的支援會使用進階/進階功能，安裝其他元件/延伸模組，與合作夥伴生態系統的自訂安裝介面。 如需詳細資訊，請參閱[Enterprise Edition、 自訂安裝程式及在 ADF 中 ssis 的第 3 個合作對象擴充性](https://blogs.msdn.microsoft.com/ssis/2018/04/27/enterprise-edition-custom-setup-and-3rd-party-extensibility-for-ssis-in-adf/)。 
-   SSIS 可讓您叫用/觸發程序在 Data Factory 管線中的第一級執行 SSIS 套件活動，並透過 SSMS 排程的 Data Factory 中的更深入的整合。 如需詳細資訊，請參閱[Modernize 及擴充您的 ETL/ELT 工作流程使用 ADF 管線中的 SSIS 活動](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/)。


## <a name="what-is-the-integration-runtime"></a>什麼是整合執行階段？
整合執行階段是 Azure Data Factory 用來跨各種網路環境提供下列資料整合功能的計算基礎結構：

- **資料移動**：針對資料移動，整合執行階段之間移動資料，來源和目的地資料存放區，同時提供內建連接器、 格式轉換、 資料行對應，以及高效能和可調式資料轉送支援。
- **分派活動**：針對轉換，整合執行階段會提供原生執行 SSIS 套件的功能。
- **執行 SSIS 套件**：整合執行階段會在 受控 Azure 計算環境中原生執行 SSIS 套件。 整合執行階段也支援分派和監視在各種計算服務，例如 Azure HDInsight、 Azure Machine Learning、 SQL Database 和 SQL Server 上執行的轉換活動。

您可以視需要來移動和轉換資料，以部署的整合執行階段的一或多個執行個體。 在 Azure 公用網路上或在私人網路 （內部部署、 Azure 虛擬網路或 Amazon Web Services 虛擬私人雲端 [VPC]），可以執行整合執行階段。 

如需詳細資訊，請參閱 [Azure Data Factory 中的整合執行階段](concepts-integration-runtime.md)。

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>整合執行階段數目的限制為何？
您在資料處理站中可擁有多少個整合執行階段個體，並無硬性限制。 不過，整合執行階段可根據訂用帳戶用於 SSIS 套件封裝的虛擬機器核心數目，將會受到限制。 如需詳細資訊，請參閱 [Data Factory 限制](../azure-subscription-service-limits.md#data-factory-limits)。

## <a name="what-are-the-top-level-concepts-of-azure-data-factory"></a>Azure Data Factory 的最上層概念為何？
Azure 訂用帳戶可能會有一或多個 Azure Data Factory 執行個體 (或資料處理站)。 Azure Data Factory 是由四個重要元件所組成，這些元件會一起運作，以提供平台讓您撰寫具有資料移動和轉換步驟的資料驅動工作流程。

### <a name="pipelines"></a>管線
資料處理站可以有一或多個管線。 管線是一個執行某個單位工作的活動邏輯群組。 管線中的活動會合作執行一項工作。 例如，管線可能包含一組活動，以從 Azure Blob 內嵌資料，然後對 HDInsight 叢集執行 Hive 查詢來分割資料。 其中的優勢在於，您可使用管線按組別管理活動，而無須個別管理每個活動。 您可將管線中的活動鏈結在一起以循序方式運作，或是以平行方式獨立運作。

### <a name="activities"></a>活動
活動代表管線中的處理步驟。 例如，您可以使用複製活動來將資料從一個資料存放區複製到另一個資料存放區。 同樣地，您可以使用在 Azure HDInsight 叢集上執行 Hive 查詢的 Hive 活動，來轉換或分析您的資料。 Data Factory 支援三種類型的活動︰資料移動活動、資料轉換活動，以及控制活動。

### <a name="datasets"></a>資料集
資料集代表資料存放區中的資料結構，其只會指出或參考您要在活動中作為輸入或輸出的資料。 

### <a name="linked-services"></a>連結的服務
已連結的服務非常類似連接字串，可定義 Data Factory 連接到外部資源所需的連線資訊。 請將它這種方式：連結的服務定義的連線資料來源，而資料集代表資料的結構。 例如，Azure 儲存體連結的服務會指定連接字串以連線到 Azure 儲存體帳戶。 和 Azure blob 資料集指定的 blob 容器和包含資料的資料夾。

Data Factory 中的連結服務，有兩個用途：

- 用來代表*資料存放區*，其包含 (但不限於) 內部部署 SQL Server 執行個體、Oracle 資料庫執行個體、檔案共用或 Azure Blob 儲存體帳戶。 如需支援的資料存放區清單，請參閱 [Azure Data Factory 中的複製活動](copy-activity-overview.md)。
- 用來代表可裝載活動執行的 *計算資源* 。 例如，HDInsight Hive 活動會在 HDInsight Hadoop 叢集上執行。 如需轉換活動和受支援計算環境的清單，請參閱 [Azure Data Factory 中的資料轉換](transform-data.md)。

### <a name="triggers"></a>觸發程序
觸發程序代表用來決定何時需要啟動管線執行的處理單位。 針對不同類型的事件，有不同類型的觸發程序。 

### <a name="pipeline-runs"></a>管線執行
管線執行是管線執行的執行個體。 通常會藉由將引數傳遞給管線中定義的參數，來具現化管線執行。 您可以藉由手動方式，或是在觸發程序定義內傳遞引數。

### <a name="parameters"></a>參數
參數是唯讀設定的機碼值組。 您可在管線中定義參數，並在執行內容的過程中傳遞已定義參數的引數。 執行內容是由觸發程序，或是您手動執行的管線所建立。 管線內的活動會取用參數值。

資料集是強型別的參數，您可以重複使用或參考的實體。 活動可以參考資料集，且可取用資料集定義中所定義的屬性。

連結服務亦是一種強型別參數，其包含資料存放區或計算環境的連線資訊。 它也是您可以重複使用或參考的實體。

### <a name="control-flows"></a>控制流程
控制流程負責協調管線活動，其中包括將活動循序鏈結、分支，以及可在管線層級定義的參數，還有依需求或從觸發程序叫用管線時所傳遞的引數。 控制流程也包括自訂狀態傳遞和迴圈容器 （也就是 foreach 迭代器）。


如需 Data Factory 概念的詳細資訊，請參閱下列文章：

- [資料集和連結服務](concepts-datasets-linked-services.md)
- [管線和活動](concepts-pipelines-activities.md)
- [整合執行階段](concepts-integration-runtime.md)

## <a name="what-is-the-pricing-model-for-data-factory"></a>Data Factory 的定價模型為何？
請參閱 [Data Factory 定價詳細資料](https://azure.microsoft.com/pricing/details/data-factory/)頁面，以瞭解 Azure Data Factory 的定價詳細資料。

## <a name="how-can-i-stay-up-to-date-with-information-about-data-factory"></a>如何掌握 Data Factory 的最新資訊？
移至下列網站掌握 Azure Data Factory 的最新資訊：

- [部落格](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [文件首頁](/azure/data-factory)
- [產品首頁](https://azure.microsoft.com/services/data-factory/)

## <a name="technical-deep-dive"></a>深入技術性討論 

### <a name="how-can-i-schedule-a-pipeline"></a>如何排程管線？ 
您可以利用排程器觸發程序，或時間範圍觸發程序來排程管線。 觸發程序使用時鐘日曆排程，以排程管線定期或行事曆為基礎的週期性模式，例如在 （星期一下午 6:00） 和星期四下午 9:00。 如需詳細資訊，請參閱[管道執行和觸發程序](concepts-pipeline-execution-triggers.md)。

### <a name="can-i-pass-parameters-to-a-pipeline-run"></a>可以將參數傳遞給執行的管線嗎？
是，參數會是第一級最上層概念在 Data Factory 中。 您可定義管線層級的參數並傳遞引數，同時依需求或使用觸發程序來執行管線執行。  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>可以定義管線參數的預設值嗎？ 
是。 您可以定義管線中參數的預設值。 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-that-are-passed-to-a-pipeline-run"></a>管線中的活動可否取用傳遞給管線執行的引數？ 
是。 管線內的每個活動皆可使用 `@parameter` 建構，來取用傳遞給管線執行的參數值。 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>可以在另一個活動中取用活動輸出屬性？ 
是。 可以使用 `@activity` 建構在後續活動中取用活動輸出。
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>如何正常處理活動輸出中的 Null 值？ 
您可以在運算式中使用 `@coalesce` 建構來正常處理 Null 值。 

## <a name="mapping-data-flows"></a>對應資料流程

### <a name="which-data-factory-version-do-i-use-to-create-data-flows"></a>若要建立資料流使用哪一個 Data Factory 版本？
您可以使用 Data Factory V2 版本來建立資料流。
  
### <a name="i-was-a-previous-private-preview-customer-who-used-data-flows-and-i-used-the-data-factory-v2-preview-version-for-data-flows"></a>我的上一個私人預覽客戶使用資料流，而且我可以使用 Data Factory V2 預覽版本的資料流。
此版本現在已過時。 使用 Data Factory V2 中的資料流。
  
### <a name="what-has-changed-from-private-preview-to-limited-public-preview-in-regard-to-data-flows"></a>功能已從私人預覽中變更為有限的公開預覽，對於資料流？
您不再必須攜帶您自己的 Azure Databricks 叢集。 Data Factory 會管理叢集中建立和終止程式碼。 Blob 資料集和 Azure Data Lake 儲存體 Gen2 資料集分成分隔的文字和 Apache Parquet 資料集。 您仍然可以使用 Data Lake 儲存體 Gen2 和 Blob 儲存體來儲存這些檔案。 使用適當的連結的服務，這些儲存體引擎。

### <a name="can-i-migrate-my-private-preview-factories-to-data-factory-v2"></a>我是否可以移轉我的私人預覽工廠到 Data Factory V2？

是。 [請依照下列指示](https://www.slideshare.net/kromerm/adf-mapping-data-flow-private-preview-migration)。

### <a name="i-need-help-troubleshooting-my-data-flow-logic-what-info-do-i-need-to-provide-to-get-help"></a>我需要協助疑難排解資料的流程邏輯。 若要取得說明提供是否需要哪些資訊？

當 Microsoft 提供協助，或使用資料流進行疑難排解時，請提供 DSL 程式碼計劃。 若要這樣做，請遵循下列步驟：

1. 從資料流程設計師，選取**程式碼**右上角。 這會顯示資料流程中的可編輯的 JSON 程式碼。
2. 從程式碼 檢視中，選取**計劃**右上角。 此切換會從 JSON 切換，唯讀格式化 DSL 指令碼方案。
3. 複製和貼上此指令碼，或將它儲存在文字檔中。

### <a name="how-do-i-access-data-by-using-the-other-80-dataset-types-in-data-factory"></a>如何使用 Data Factory 中的其他 80 的資料集型別存取資料？

對應資料流程的功能目前可讓 Azure SQL Database，Azure SQL 資料倉儲，分隔文字檔案，從 Azure Blob 儲存體或 Azure Data Lake 儲存體 Gen2 和原生支援來源和接收器的 Parquet 檔案從 Blob 儲存體或 Data Lake 儲存體 Gen2。 

使用 「 複製活動來接移資料，從任何其他連接器，並執行資料流程的活動轉換資料之後已預備。 例如，您的管線會先將複製到 Blob 儲存體，，然後資料流程的活動會使用資料集來源中來轉換該資料。

## <a name="next-steps"></a>後續步驟
如需建立資料處理站的逐步指示，請參閱下列教學課程：

- [快速入門：建立資料處理站](quickstart-create-data-factory-dot-net.md)
- [教學課程：複製雲端中的資料](tutorial-copy-data-dot-net.md)
