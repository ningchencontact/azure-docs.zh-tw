---
title: Microsoft Azure 安全性程式碼分析檔常見問題
description: 本文包含安全性程式碼分析延伸模組的相關常見問題
author: vharindra
manager: sukhans
ms.author: terrylan
ms.date: 07/31/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 8038b7bd60ac771c798a1a8645022b0bf9e142a9
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934854"
---
# <a name="frequently-asked-questions"></a>常見問答集
有疑問嗎？ 如需詳細資訊, 請參閱下列常見問題。

## <a name="general-faqs"></a>一般常見問題

### <a name="can-i-install-the-extension-on-my-tfs-not-azure-devops-server"></a>我可以在 TFS (不是 Azure DevOps) 伺服器上安裝擴充功能嗎？ 

否, 延伸模組無法供 TFS 下載和安裝。

### <a name="do-i-have-to-run-microsoft-security-code-analysis-with-my-build"></a>我是否必須使用我的組建來執行 Microsoft 安全性程式碼分析？ 

是和否。 根據分析工具的類型而定, 原始程式碼本身可能是唯一需要的東西, 或可能需要組建的輸出。 例如, 因為認證掃描器會分析程式碼存放庫的資料夾結構內的檔案, 所以您可以執行認證掃描器, 並在獨立組建中發佈安全性分析記錄組建工作, 以取得結果。
對於分析文章組建成品 (例如 BinSkim) 的其他工具, 首先需要建立組建。

### <a name="can-i-break-my-build-when-results-are-found"></a>可以在找到結果時中斷我的組建嗎？ 
是的, 您可以在任何工具回報問題時 (尋找, 在其記錄檔中) 引進組建中斷。 只要新增 [後期分析] 組建工作, 並核取您要中斷組建的任何工具的核取方塊即可。 當任何工具報告錯誤或警告和錯誤時, 您可以選擇在分析後工作的 UI 中, 將組建中斷。

### <a name="how-are-the-command-line-arguments-different-in-azure-devops-than-they-are-in-the-standalone-desktop-tools"></a>在 Azure DevOps 中, 命令列引數與獨立桌面工具中的不同之處為何？ 

在大部分的情況下, Azure DevOps 組建工作都是安全性工具的命令列引數的直接包裝函式。 您通常會從桌面的命令列傳遞至工具的任何專案, 都可以傳遞至組建工作的引數輸入。
以下是明顯的差異清單:
 - 此工具將從代理程式 $ (SourcesDirectory) 或% BUILD_SOURCESDIRECTORY% 的源資料夾執行。 範例:C:\agent\_work\1\s 
 - 引數中的路徑可以相對於上列來原始目錄的根目錄, 或使用 Azure DevOps 組建變數來執行具有已知部署位置的內部內部部署代理程式
 - 如果提供輸出路徑, 則工具會自動提供輸出檔案路徑或資料夾, 並將其移除, 並將其取代為組建代理程式上已知記錄檔位置的路徑
 - 某些工具上已清理並移除一些額外的命令列參數, 例如新增或移除選項, 以確保不會啟動 GUI。

### <a name="can-i-run-a-build-task-for-example-credential-scanner-across-multiple-repositories-in-an-azure-devops-build"></a>我可以在 Azure DevOps 組建中跨多個存放庫執行組建工作 (例如認證掃描器) 嗎？ 

否, 目前不支援對單一管線中的多個存放庫執行安全的開發工具。

###  <a name="the-output-file-i-specified-is-not-being-created--i-cant-find-the-output-file-i-specified"></a>未建立我指定的輸出檔/找不到我指定的輸出檔 

組建工作目前會淨化使用者輸入, 並將產生的輸出檔位置更新為組建代理程式上的通用位置。 如需此位置的詳細資訊, 請參閱下列問題。

### <a name="where-are-the-output-files-generated-by-the-tools-saved"></a>這些工具所產生的輸出檔會儲存在哪裡？ 

組建工作會自動將輸出路徑加入至組建代理程式 $ (BuildDirectory)\_sdt\logs. 上的下列知名位置 藉由在此位置上標準化, 我們可以確保產生程式碼分析記錄或使用它們的其他小組能夠存取。

### <a name="can-i-queue-a-build-to-run-these-tasks-on-a-hosted-build-agent"></a>我可以將組建排入佇列, 以便在裝載的組建代理程式上執行這些工作嗎？ 

是, 擴充功能中的所有工作和工具都可以在裝載的組建代理程式上執行。

>[!NOTE]
> 反惡意程式碼組建工作需要已啟用 Windows Defender 的組建代理程式, 這在「Hosted VS2017」或更新版本的組建代理程式上為 true。 (它不會在舊版/VS2015 「託管」代理程式上執行)。無法更新這些代理程式上的簽章, 但是簽章應該一律是相對最新的, 但不到3小時的舊。
>

### <a name="can-i-run-these-build-tasks-as-part-of-a-release-pipeline-as-opposed-to-a-build-pipeline"></a>我可以將這些組建工作當做發行管線的一部分來執行 (相對於組建管線) 嗎？ 
在大部分情況下為 [是]。 不過, Azure DevOps 從發行管線內執行時, 不支援發行成品的工作:「不預期使用發行的唯一工作類別是發佈成品的作業。 這是因為目前我們不支援在發行中發佈成品」。
這可防止「發行安全性分析記錄」工作順利從發行管線執行;它將會失敗, 並顯示描述性錯誤訊息。

### <a name="from-where-do-the-build-tasks-download-the-tools"></a>組建工作從何處下載工具？ 
組建工作 a) 下載下列 Azure DevOps 工具的 NuGet 套件[套件管理](https://securitytools.pkgs.visualstudio.com/_packaging/SecureDevelopmentTools/nuget/v3/index.json)摘要, 或使用節點套件管理員 (必須預先安裝在組建代理程式上) (範例: "npm install tslint")。

### <a name="what-effect-will-installing-the-extension-have-on-my-azure-devops-organization"></a>在我的 Azure DevOps 組織上安裝延伸模組的效果為何？ 

在安裝時, 延伸模組所提供的安全性組建工作將會變成可供組織中的所有使用者使用。 建立或編輯 Azure 管線時, 將可從 [組建工作集合] 清單中加入這些工作。 否則, 在您的 Azure DevOps 組織中安裝擴充功能不會有任何影響。 它不會修改任何帳戶或專案設定或管線。

### <a name="will-installing-the-extension-modify-my-existing-azure-pipelines"></a>安裝延伸模組會修改我現有的 Azure Pipelines 嗎？ 

資料分割 安裝擴充功能會讓安全性組建工作可新增至您的 Azure Pipelines。 使用者仍然需要新增或更新組建定義, 才能將工具整合到您的組建程式。

## <a name="task-specific-faqs"></a>工作特有的常見問題

這一節將會列出組建工作特有的常見問題。

### <a name="credential-scanner-faqs"></a>認證掃描器常見問題

#### <a name="what-are-common-suppressions-scenarios-and-examples"></a>什麼是常見的隱藏專案案例和範例？ 
以下詳細說明兩個最常見的隱藏專案案例:
##### <a name="suppress-all-occurrences-of-a-given-secret-within-the-specified-path"></a>隱藏指定路徑內所有出現的特定密碼 
如下列範例所示, 需要認證掃描器輸出檔案中的密碼雜湊金鑰
   
        {
            "tool": "Credential Scanner",
            "suppressions": [
            {
                "hash": "CLgYxl2FcQE8XZgha9/UbKLTkJkUh3Vakkxh2CAdhtY=",
                "_justification": "Secret used by MSDN sample, it is fake."
            }
          ]
        }

>[!WARNING]
> 雜湊索引鍵是由相符值或檔案內容的一部分所產生。 任何原始程式碼修訂都可能會變更雜湊索引鍵, 並停用隱藏專案規則。 

##### <a name="to-suppress-all-secrets-in-a-specified-file-or-to-suppress-the-secrets-file-itself"></a>隱藏指定檔案中的所有秘密 (或隱藏秘密檔案本身) 
檔案運算式可以是檔案名或完整檔案路徑/名稱的任何後置部分。 不支援萬用字元。 

**範例** 

要隱藏的檔案: [InputPath] \src\JS\lib\angular.js 

有效的隱藏專案規則: 
- [InputPath] \src\JS\lib\angular.js--隱藏指定路徑中的檔案
- \src\JS\lib\angular.js
- \JS\lib\angular.js
- \lib\angular.js
- 角 .js--隱藏所有具有相同名稱的檔案

        {
            "tool": "Credential Scanner",
            "suppressions": [
            {
                "file": "\\files\\AdditonalSearcher.xml", 
                "_justification": "Additional CredScan searcher specific to my team"
            },
            {
                "file": "\\files\\unittest.pfx", 
                "_justification": "Legitimate UT certificate file with private key"
            }
          ]
        }      

>[!WARNING] 
> 所有未來新增至檔案的秘密也會自動隱藏。 

#### <a name="what-are-recommended-secrets-management-guidelines"></a>什麼是建議的秘密管理指導方針？ 
雖然及時偵測硬式編碼的秘密並降低風險會很有説明, 但如果有可能導致秘密無法全部簽入, 則更是更好的方法。 就這一點而言, Microsoft 已發行 CredScan 程式碼分析器, 做為 Visual Studio 的[Microsoft DevLabs 延伸](https://marketplace.visualstudio.com/items?itemName=VSIDEDevOpsMSFT.ContinuousDeliveryToolsforVisualStudio)模組的一部分。 在早期預覽期間, 它會為開發人員提供在其程式碼中偵測潛在秘密的內嵌體驗, 讓他們有機會即時修正這些問題。 如需詳細資訊, 請參閱[此](https://devblogs.microsoft.com/visualstudio/managing-secrets-securely-in-the-cloud/)blog, 以瞭解如何在雲端中安全地管理秘密。 以下是一些額外的資源, 可協助您以安全的方式從應用程式中管理秘密及存取機密資訊: 
 - [Azure 金鑰保存庫](../../key-vault/index.yml)
 - [Azure Active Directory](../../sql-database/sql-database-aad-authentication.md)
 - [Azure AD 受控服務識別](https://azure.microsoft.com/blog/keep-credentials-out-of-code-introducing-azure-ad-managed-service-identity/)
 - [適用於 Azure 資源的受控服務識別 (MSI)](../../active-directory/managed-identities-azure-resources/overview.md)
 - [Azure 受控服務識別](../../app-service/overview-managed-identity.md)
 - [AppAuthentication 程式庫](../../key-vault/service-to-service-authentication.md)

#### <a name="can-i-write-my-own-custom-searchers"></a>我可以撰寫自己的自訂 searchers 嗎？

認證掃描器依賴一組通常在**buildsearchers**檔案中定義的內容 searchers。 檔案包含代表 ContentSearcher 物件的 XML 序列化物件陣列。 此程式會與一組經過妥善測試的 searchers 一起散發, 但它也可讓您執行自己的自訂 searchers。 

內容搜尋會定義如下: 

- **Name** –要在認證掃描器輸出檔中使用的描述性搜尋名稱。 建議針對搜尋程式名稱使用 camel 大小寫命名慣例。 
- **RuleId** –搜尋者的穩定不透明識別碼。 
    - 認證掃描器的預設 searchers 會使用 RuleIds (例如 CSCAN0010、CSCAN0020、CSCAN0030 等) 來指派。最後一個數位會保留給潛在的搜尋者 RegEx 群組合並或分割。
    - 自訂 searchers 的 RuleId 應該有自己的命名空間, 其格式如下:CSCAN-{Namespace} mylnxstorage02、CSCAN-{Namespace} 0020、CSCAN-{Namespace} 0030 等。
    - 完整的搜尋者名稱是 RuleId 和搜尋者名稱的組合。 範例 CSCAN0010。KeyStoreFiles, CSCAN0020.Base64EncodedCertificate 等等。
- **ResourceMatchPattern** –要針對搜尋者檢查的檔案副檔名 Regex
- **ContentSearchPatterns** –包含要比對之 Regex 語句的字串陣列。 如果未定義任何搜尋模式, 則會傳回符合資源符合模式的所有檔案。
- **ContentSearchFilters** –包含 Regex 語句的字串陣列, 用來篩選搜尋程式特定的誤報。
- **Matchdetails** –要針對每個搜尋的相符新增的描述性訊息和/或緩和指示。
- **建議**–使用 PREfast 報表格式提供相符的建議欄位內容。
- **嚴重性**–用來反映問題嚴重性的整數 (最高 = 1)。
![認證掃描器設定](./media/security-tools/6-credscan-customsearchers.png)

### <a name="roslyn-analyzers-faqs"></a>Roslyn 分析器常見問題

#### <a name="what-are-the-most-common-errors-when-using-the-roslyn-analyzers-task"></a>使用 Roslyn 分析器工作時最常見的錯誤為何？

**錯誤：已使用 NETCore. x 版還原專案, 但使用目前的設定, 將改用版本 y. y。若要解決此問題, 請確定已使用相同的設定來進行還原, 以及進行後續作業 (例如組建或發行)。一般來說, 如果在組建或發佈期間設定 RuntimeIdentifier 屬性, 但在還原期間不會, 就會發生此問題:**

Roslyn 分析器會在編譯過程中執行, 因此組建電腦上的來源樹狀結構必須處於可建置狀態。 您的主要組建與 Roslyn 分析器之間的步驟 (可能是 "dotnet") 可能會使來源樹狀結構處於 unbuildable 狀態。 可能是複製在 Roslyn 分析器步驟之前進行 Nuget 還原的步驟, 會使來源樹狀結構回到可建置狀態。

**"csc" 已結束, 錯誤碼為 1--無法從 C:\BBBB.dll 建立分析器 AAAA 的實例:無法載入檔案或元件 ' CodeAnalysis, Version = X. x, Culture = 中性, PublicKeyToken = 31bf3856ad364e35 ' 或其相依性的其中之一。系統找不到指定的檔案。**

請確定您的編譯器支援 Roslyn 分析器。 "csc/version" 應該會報告最少的 2.6. x。 在某些情況下, 個別 .csproj 檔案可以藉由參考 Microsoft.Net 中的封裝, 來覆寫組建電腦的 Visual Studio 安裝。 如果未預期使用特定版本的編譯器, 請移除 Microsoft.Net 的參考。 否則, 請確定參考的套件也至少是 v1.1. x。 嘗試取得錯誤記錄檔, 您可以在/errorlog: 參數中, 從 csc 命令列 (在 Roslyn 組建工作的記錄檔中找到) 找到它。 看起來可能像這樣:/errorlog: f:\ts-services-123\_work\456\s\Some\Project\Code\Code.csproj.sarif

**C#編譯器不夠新 (必須 > = 2.6)**

最新版本的C#編譯器會在此發行: https://www.nuget.org/packages/Microsoft.Net.Compilers 。 若要取得已安裝的版本, 您`C:\>csc.exe /version`使用的是從命令提示字元執行。 請確定您沒有任何 < 2.6 版 Microsoft.Net NuGet 套件的參考。

**找不到 MSBuild/VSBuild 記錄**

由於工作的運作方式, 這項工作必須從 MSBuild 組建工作查詢 MSBuild 記錄 Azure DevOps。 如果此工作會在 MSBuild 組建工作之後立即執行, 則記錄檔將無法使用;在 MSBuild 組建工作和 Roslyn 分析器組建工作之間, 放置其他組建工作, 包括 SecDevTools 組建工作, 例如 Binskim、反惡意程式碼掃描等等。 

## <a name="next-steps"></a>後續步驟

如果您需要其他協助, Microsoft 安全性程式碼分析支援將于星期一到星期五下午 5:00 9:00 太平洋標準時間提供

  - 上架-請洽詢您的技術客戶經理以開始使用。 
  >[!NOTE] 
  >如果您還沒有與 Microsoft 的付費支援關係, 或如果您有不允許您從 Phoenix 目錄購買服務的支援供應專案, 請造訪我們的[支援服務首頁](https://www.microsoft.com/enterprise/services/support)以取得詳細資訊。

  - 支援-以電子郵件寄給我們的小組[Microsoft 安全性程式碼分析支援](mailto:mscahelp@microsoft.com?Subject=Microsoft%20Security%20Code%20Analysis%20Support%20Request)