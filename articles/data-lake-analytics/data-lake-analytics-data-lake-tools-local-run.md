---
title: 在本機電腦上執行 Azure Data Lake U-SQL 指令碼 | Microsoft Docs
description: 了解如何使用 Azure Data Lake Tools for Visual Studio 在本機電腦上執行 U-SQL 作業。
services: data-lake-analytics
documentationcenter: ''
author: yanancai
manager: ''
editor: ''
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/03/2018
ms.author: yanacai
ms.openlocfilehash: a7f43c7e17f36d9b4e0767744eee9604c2628ea8
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/06/2018
ms.locfileid: "37888961"
---
# <a name="run-u-sql-script-on-your-local-machine"></a>在本機電腦上執行 U-SQL 指令碼

開發 U-SQL 指令碼時，常會在本機執行 U-SQL 指令碼，因為這樣能節省成本和時間。 Azure Data Lake Tools for Visual Studio 可支援在本機電腦上執行 U-SQL 指令碼。 

## <a name="basic-concepts-for-local-run"></a>本機執行的基本概念

以下圖表顯示的是本機執行的元件，以及元件對應至雲端執行的方式。

|元件|本機執行|雲端執行|
|---------|---------|---------|
|儲存體|本機資料根資料夾|預設 Azure Data Lake Store 帳戶|
|計算|U-SQL 本機執行引擎|Azure Data Lake Analytics 服務|
|執行環境|本機電腦上的工作目錄|Azure Data Lake Analytics 叢集|

本機執行元件的詳細說明：

### <a name="local-data-root-folder"></a>本機資料根資料夾

本機根資料夾是本機計算帳戶的「本機存放區」。 在本機電腦上，本機檔案系統中的任何資料夾都可以作為本機資料根資料夾。 它就相當於 Data Lake Analytics 帳戶的預設 Azure Data Lake Store 帳戶。 切換到不同的資料根資料夾，就如同切換到不同的預設存放區帳戶。 

資料根資料夾是用來︰
- 儲存中繼資料，如資料庫、資料表，資料表值函式及組件。
- 查詢在 U-SQL 指令碼中定義為相對路徑的輸入和輸出路徑。 使用相對路徑能夠更容易將 U-SQL 指令碼部署至 Azure。

### <a name="u-sql-local-run-engine"></a>U-SQL 本機執行引擎

U-SQL 本機執行引擎是 U-SQL 作業的「本機計算帳戶」。 使用者能透過 Azure Data Lake Tools for Visual Studio 在本機執行 U-SQL 作業。 本機執行也支援透過 Azure Data Lake U-SQL SDK 命令列和程式設計介面來進行。 [深入了解 Azure Data Lake U-SQL SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/)。

### <a name="working-directory"></a>工作目錄

執行 U-SQL 指令碼時，需要有工作目錄資料夾將編譯結果、執行記錄等項目加入快取。 在 Azure Data Lake Tools for Visual Studio 中，工作目錄即是 U-SQL 專案的工作目錄 (通常位於 `<U-SQL project root path>/bin/debug>` 下方)。 每當觸發新執行時，工作目錄就會清空。

## <a name="local-run-in-visual-studio"></a>Visual Studio 中的本機執行

Azure Data Lake Tools for Visual Studio 擁有以本機計算帳戶形式呈現的內建本機執行引擎。 若要在本機執行 U-SQL 指令碼，請在指令碼的編輯器邊界下拉式清單中選取 [(Local-machine)] 或 [(Local-project)] 帳戶，然後按一下 [提交]。

![Data Lake Tools for Visual Studio 將指令碼提交到本機帳戶](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-submit-script-to-local-account.png) 
 
## <a name="local-run-with-local-machine-account"></a>使用 (Local-machine) 帳戶進行本機執行

(Local-machine) 帳戶是共用本機電腦帳戶，擁有一個作為本機存放區帳戶的本機資料根資料夾。 資料根資料夾的位置預設為 “C:\Users\<使用者名稱>\AppData\Local\USQLDataRoot”，您也可以透過 [工具] > [Data Lake] > [選項和設定] 加以設定。

![Data Lake Tools for Visual Studio 設定本機資料根](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-local-data-root.png)
  
本機執行須有 U-SQL 專案。 U-SQL 專案的工作目錄可以當做 U-SQL 本機執行工作目錄。 本機執行時產生的編譯結果、執行記錄和其他作業執行相關檔案會存放在工作目錄資料夾下。 請注意，每當您重新執行指令碼時，工作目錄中的所有檔案都會清空並重新產生。

## <a name="local-run-with-local-project-account"></a>使用 (Local-project) 帳戶進行本機執行

(Local-project) 帳戶是依照專案區分的本機計算帳戶，每個專案都有各自獨立的本機資料根資料夾。 在方案總管中開啟的每個使用中 U-SQL 專案都有對應的 `(Local-project: <project name>)` 帳戶，同時列於伺服器總管和 U-SQL 指令碼編輯器邊界中。 

(Local-project) 帳戶為開發人員提供了一個精簡又獨立的開發環境。 與 (Local-machine) 帳戶不同，(Local-machine) 帳戶擁有共用本機資料根資料夾，可供存放所有本機作業的中繼資料和輸入/輸出資料，(Local-project) 帳戶會在每次 U-SQL 指令碼執行時，在 U-SQL 專案工作目錄下建立暫存本機資料根資料夾。 這個暫存資料根資料夾會在重建或重新執行發生時清空。 

在管理這個隔離的本機執行環境時，U-SQL 專案能透過專案參考和屬性，提供良好的體驗。 您可以在專案中設定 U-SQL 指令碼的輸入資料來源，也可以設定參考資料庫環境。

### <a name="manage-input-data-source-for-local-project-account"></a>管理 (Local-project) 帳戶的輸入資料來源

U-SQL 專案負責為 (Local-project) 帳戶建立本機資料根資料夾及設定資料。 每當重建和本機執行發生時，U-SQL 專案工作目錄下的暫存資料根資料夾都會清空及重新建立。 在本機作業執行前，U-SQL 專案設定的所有資料來源都會複製到這個暫存本機資料根資料夾。 

您可以透過以滑鼠右鍵按一下 U-SQL 專案 > [屬性] > [測試資料來源] 設定資料來源的根資料夾。 以 (Local-project) 帳戶執行 U-SQL 指令碼時，[測試資料來源] 資料夾內的所有檔案和子資料夾 (包括子資料夾內的檔案) 都會複製到暫存本機資料根資料夾。 本機作業執行完成後，您可以在專案工作目錄的暫存本機資料根資料夾下方找到輸出結果。 請注意，在重建及清理專案時，所有輸出內容都會遭到刪除和清空。 

![Data Lake Tools for Visual Studio 設定專案測試資料來源](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-project-test-data-source.png)

### <a name="manage-referenced-database-environment-for-local-project-account"></a>管理 (Local-project) 帳戶的參考資料庫環境 

如果 U-SQL 查詢使用的是 U-SQL 資料庫物件，或是以該物件進行查詢，您必須先在本機將資料庫環境準備妥當，才能在本機執行該 U-SQL 指令碼。 對於 (Local-project) 帳戶，您可以透過 U-SQL 專案參考來管理 U-SQL 資料庫相依性。 您可以將 U-SQL 資料庫專案參考新增到 U-SQL 專案。 在以 (Local-project) 帳戶執行 U-SQL 指令碼之前，所有參考資料庫都會部署到暫存本機資料根資料夾。 而在每次執行時，暫存資料根資料夾都會清空，成為全新的隔離環境。

相關文章：
* [了解如何透過 U-SQL 資料庫專案管理 U-SQL 資料庫定義](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project)
* [了解如何在 U-SQL 專案中管理 U-SQL 資料庫參考](data-lake-analytics-data-lake-tools-develop-usql-database.md)

## <a name="difference-between-local-machine-and-local-project-account"></a>(Local-machine) 和 (Local-project) 帳戶之間的差異

(Local-machine) 帳戶的目的在於在使用者的本機電腦上模擬 Azure Data Lake Analytics 帳戶。 它的體驗與 Azure Data Lake Analytics 帳戶相同。 (Local-project) 的目的在於提供方便使用的本機開發環境，協助使用者在本機執行指令碼之前部署資料庫參考和輸入資料。 (Local-machine) 帳戶提供共用的永久環境，供使用者透過所有專案存取。 (Local-project) 帳戶提供每個專案各地獨立的開發環境，並且會在每次執行時重新整理環境。 根據以上說明，(Local-project) 帳戶能讓使用者迅速套用新變更，因而創造快速的開發體驗。

您可以在下表中了解 (Local-machine) 和 (Local-project) 帳戶之間的更多差異：

|差異角度|(Local-machine)|(Local-project)|
|----------------|---------------|---------------|
|本機存取|所有專案均可存取|只有對應的專案能存取這個帳戶|
|本機資料根資料夾|永久本機資料夾。 透過 [工具] > [Data Lake] > [選項和設定] 設定|每次本機執行時，會在 U-SQL 專案工作資料夾下建立暫存資料夾。 資料夾會在重建或重新執行發生時清空|
|U-SQL 指令碼的輸入資料|永久本機資料根資料夾下方的相對路徑|透過 U-SQL 專案屬性 > [測試資料來源] 設定。 所有檔案、子資料夾都會在本機執行前複製到暫存資料根資料夾|
|U-SQL 指令碼的輸出資料|永久本機資料根資料夾下方的相對路徑|輸出到暫存資料根資料夾。 結果會在重建或重新執行發生時清空。|
|參考資料庫部署|以 (Local-machine) 帳戶執行時，不會自動部署參考資料庫。 提交到 Azure Data Lake Analytics 帳戶時也是如此。|參考資料庫會在本機執行前自動部署到 (Local-project) 帳戶。 所有資料庫環境都會在重建或重新執行發生時清空及重新部署。|

## <a name="local-run-with-u-sql-sdk"></a>使用 U-SQL SDK 進行本機執行

除了在 Visual Studio 中本機執行 U-SQL 指令碼之外，您還可以利用 Azure Data Lake U-SQL SDK，使用命令列和程式設計介面在本機執行 U-SQL 指令碼。 透過這些介面，您可以將 U-SQL 本機執行和測試自動化。

[深入了解 Azure Data Lake U-SQL SDK](data-lake-analytics-u-sql-sdk.md)。

## <a name="next-steps"></a>後續步驟

- [Azure Data Lake U-SQL SDK](data-lake-analytics-u-sql-sdk.md)
- [如何設定 Azure Data Lake Analytics 的 CI/CD 管線](data-lake-analytics-cicd-overview.md)
- [使用 U-SQL 資料庫專案開發 U-SQL 資料庫](data-lake-analytics-data-lake-tools-develop-usql-database.md)
- [如何測試 Azure Data Lake Analytics 程式碼](data-lake-analytics-cicd-test.md)
