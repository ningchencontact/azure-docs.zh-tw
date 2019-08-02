---
title: Microsoft Azure 安全性程式碼分析工作自訂指南
description: 本文說明如何在安全性程式碼分析延伸模組中自訂工作
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
ms.openlocfilehash: ab219b71eb8cd6f6172b7d02a639301c67811b49
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/01/2019
ms.locfileid: "68718349"
---
# <a name="how-to-configure--customize-the-build-tasks"></a>如何：設定 & 自訂群組建工作

此頁面詳細說明每個組建工作中可用的設定選項, 從安全性程式碼分析工具的工作開始, 然後再進行後續的處理工作。

## <a name="anti-malware-scanner-task"></a>反惡意程式碼掃描程式工作

> [!NOTE]
> 反惡意程式碼組建工作需要已啟用 Windows Defender 的組建代理程式, 這在「Hosted VS2017」和更新版本的組建代理程式上為 true。 (它不會在舊版/VS2015 「託管」代理程式上執行)無法更新這些代理程式上的簽章, 但是簽章應該一律是相對最新的, 但不到3小時的舊。

下列設定的螢幕擷取畫面和詳細資料。

![自訂反惡意程式碼掃描器組建工作](./media/security-tools/5-add-anti-malware-task600.png) 

- 類型 =**基本**的設定
- 使用 Type = **Custom**時, 可以提供命令列引數來自訂掃描。

Windows Defender 會使用 Windows Update 用戶端來下載和安裝簽章。 如果您的組建代理程式上的簽章更新失敗, 則 HRESULT 錯誤碼可能來自 Windows Update。 如需有關 Windows Update 錯誤和緩和措施的詳細資訊, 請造訪[此頁面](https://docs.microsoft.com/windows/deployment/update/windows-update-error-reference)和這個[technet 頁面](https://social.technet.microsoft.com/wiki/contents/articles/15260.windows-update-agent-error-codes.aspx)

## <a name="binskim-task"></a>BinSkim 工作

> [!NOTE]
> 您的組建必須符合下列其中一項條件, 才能執行 BinSkim 工作。
>    - 您的組建會從 managed 程式碼產生二進位成品。
>    - 您已認可二進位成品, 您想要使用 BinSkim 進行分析。

下列設定的螢幕擷取畫面和詳細資料。 

![BinSkim 設定](./media/security-tools/7-binskim-task-details.png)  
1. 將組建設定設為 Debug, 以產生 * **.pdb** debug 檔案。 BinSkim 會使用它們來將輸出二進位檔中找到的問題對應回原始程式碼。 
2. 選擇 Type = **Basic** & Function = [**分析**] 以避免研究及建立您自己的命令列。 
3. **目標**: 一個或多個規範, 用於解析為一或多個要分析之二進位檔的檔案、目錄或篩選模式。 
    - 多個目標應該以分號分隔 **(;)** 。 
    - 可以是單一檔案或包含萬用字元。
    - 目錄應一律以結尾\*
    - 範例:

           *.dll;*.exe
           $(BUILD_STAGINGDIRECTORY)\*
           $(BUILD_STAGINGDIRECTORY)\*.dll;$(BUILD_STAGINGDIRECTORY)\*.exe;
4. 如果您選取 [類型 =**命令列**], 
     - 請確定**BinSkim**的第一個引數是使用完整路徑**分析**的動詞命令, 或相對於來原始目錄的路徑。
     - 對於**命令列**輸入, 應以空格分隔多個目標。
     - 您可以省略 **/o**或 **/output**檔參數;系統會為您新增或取代它。 
     - **標準命令列設定** 

           analyze $(Build.StagingDirectory)\* --recurse --verbose
           analyze *.dll *.exe --recurse --verbose
          > [!NOTE]
          > 當指定\*目標的目錄或目錄時, 尾端是非常重要的。 

如需有關命令列引數、依識別碼或結束代碼的規則 BinSkim 的詳細資訊, 請造訪[BinSkim 使用者指南](https://github.com/Microsoft/binskim/blob/master/docs/UserGuide.md)

## <a name="credential-scanner-task"></a>認證掃描器工作
下列設定的螢幕擷取畫面和詳細資料。
 
![認證掃描器自訂](./media/security-tools/3-taskdetails.png)

可用的選項包括 
  - **輸出格式**– TSV/CSV/SARIF/PREfast
  - **工具版本**使用新版
  - **掃描資料夾**–存放庫中要掃描的資料夾
  - **Searchers 檔案類型**-用來尋找用於掃描的 Searchers 檔案的選項。
  - 隱藏式檔案– [JSON](https://json.org/)檔案可用來抑制輸出記錄檔中的問題 (資源一節中有更多詳細資料)。 
  - **詳細資訊輸出**-自我理解 
  - [**批次大小**]-用來平行執行認證掃描器的平行線程數目。 預設為 20 (值必須在1到2147483647的範圍內)。
  - 比對**超時**-放棄檢查之前, 嘗試搜尋搜尋所花費的時間量。 
  - 檔案**掃描讀取緩衝區大小**-讀取內容時的緩衝區大小 (以位元組為單位)。 (預設為 524288) 
  - 檔案**掃描讀取位元組上限**-內容分析期間, 要從指定檔案讀取的最大位元組數目。 (預設為 104857600) 
  - **執行此**工作(在 [**控制選項**] 下)-指定工作應該執行的時間。 選擇 [自訂條件] 以指定更複雜的條件。 
  - Azure DevOps 內的**版本**組建工作版本。 不常使用。 

## <a name="microsoft-security-risk-detection-task"></a>Microsoft 安全性風險偵測工作
> [!NOTE]
> 您必須建立並設定具有風險偵測服務的帳戶, 才能使用這項工作。 此服務需要個別的上架程式;這不是「隨插即用」, 而是此延伸模組中的大部分其他工作。 請參閱[microsoft 安全性風險偵測](https://aka.ms/msrddocs)和[microsoft 安全性風險偵測:](https://docs.microsoft.com/security-risk-detection/how-to/)如何取得指示。

下列設定的詳細資料。

輸入必要的資料;每個選項都有滑鼠停留文字說明。
   - **MSRD 的 Azure DevOps 服務端點名稱**:如果您已建立 Azure DevOps 服務端點的泛型型別來儲存 MSRD 實例 URL (您已上架至) 和 REST API 存取權杖, 則可以選擇該服務端點。 如果沒有, 請按一下 [管理] 連結, 以建立及設定此 MSRD 工作的新服務端點。 
   - **帳戶識別碼**:它是可從 MSRD 帳戶 URL 抓取的 GUID。
   - **二進位檔的 url**:以分號分隔的公開可用 Url 清單 (要由模糊化機器用來下載二進位檔)。
   - **種子檔的 url**:以分號分隔的公開可用 Url 清單 (要由模糊化機器用來下載植入)。 如果 seed 檔案與二進位檔一起下載, 此欄位是選擇性的。
   - **OS 平臺類型**:要在其上執行模糊作業的電腦 OS 平臺類型 (Windows 或 Linux)。
   - **Windows edition/Linux 版本**:要在其上執行模糊化作業的電腦作業系統版本。 如果您的電腦有不同的 OS 版本, 您可以覆寫預設值。
   - **封裝安裝腳本**:提供要在測試電腦上執行的腳本, 以在提交模糊化作業之前, 先安裝測試目的程式及其相依性。
   - **作業提交參數**:
       - **種子目錄**:模糊電腦上包含種子的目錄路徑。
       - **種子延伸**模組:種子的副檔名
       - **測試驅動程式可執行檔**:模糊電腦上目標可執行檔的路徑。
       - **測試驅動程式可執行檔架構**:目標可執行檔架構 (x86 或 amd64)。
       - **測試驅動程式引數**:傳遞至測試目標可執行檔的命令列引數。 請注意, "% testfile.txt%" 符號 (包括雙引號) 將會自動取代為測試驅動程式預期要剖析的目標檔案的完整路徑, 而且是必要的。
       - 測試**驅動程式進程會在測試完成時結束**:檢查以在完成時終止測試驅動程式;如果需要強制關閉測試驅動程式, 請取消核取。
       - **持續時間上限 (以秒為單位)** :提供目的程式剖析輸入檔案所需的最長合理預期時間的估計。 這種估計越精確, 模糊執行的效率就愈高。
       - **測試驅動程式可以重複**執行:檢查測試驅動程式是否可以重複執行, 而不需視保存/共用全域狀態而定。
       - **測試驅動程式可以重新命名**:檢查是否可以重新命名測試驅動程式可執行檔, 而且仍然可以正常運作。
       - **模糊化應用程式會以單一作業系統進程的形式執行**:檢查測試驅動程式是否在單一作業系統進程下執行;取消檢查測試驅動程式是否會產生額外的進程。


## <a name="roslyn-analyzers-task"></a>Roslyn 分析器工作
> [!NOTE]
> 若要執行 Roslyn 分析器工作, 您的組建應符合下列條件。
>  - 您的組建定義包含用來編譯C# (或 VB) 程式碼的內建 MSBuild 或 VSBuild 組建工作。 此工作依賴該特定組建工作的輸入和輸出, 以在啟用 Roslyn 分析器的情況下重新執行 MSBuild 編譯。
>  - 執行此組建工作的組建代理程式已安裝 Visual Studio 2017 v 15.5 或更新版本 (編譯器 2.6. x 版)。
>

下列設定的詳細資料。

可用的選項包括 
- **規則集**-sdl 需要、sdl 建議, 或者您也可以使用自己的自訂規則集。
- **分析器版本**使用新版
- **編譯器警告**隱藏檔案-具有應隱藏之警告識別碼清單的文字檔。 
- **執行此**工作(在 [**控制選項**] 下)-指定工作應該執行的時間。 選擇 [**自訂條件**] 以指定更複雜的條件。 

> [!NOTE]
> - Roslyn 分析器是編譯器整合的, 而且只能當做 CSC 編譯的一部分來執行。 因此, 此工作需要重新執行稍早在組建中執行的編譯器命令。 其做法是查詢 MSBuild 組建工作記錄的 VSTS (沒有其他方式可讓工作從組建定義中可靠地取得 MSBuild 編譯命令列; 我們已考慮新增自由格式的 textbox, 讓使用者輸入其命令列, 但很難保持這些最新狀態並與主要組建同步處理。 自訂群組建需要重新執行整組命令, 而不只是編譯器命令, 而且在這些情況下, 啟用 Roslyn 分析器並不簡單也不可靠。 
> - Roslyn 分析器會與編譯器整合, 而且需要叫用編譯。 此組建工作的執行方式是C#在相同的組建/組建定義中, 重新編譯已經使用 MSBuild/VSBuild 組建工作所建立的專案, 但在此情況下, 已啟用分析器。 如果此組建工作在與原始組建工作相同的代理程式上執行, 則此組建工作的輸出將會覆寫 ' 源資料夾中的原始 MSBuild/VSBuild 組建工作的輸出。 組建輸出將會相同, 但建議您執行 MSBuild、將輸出複製到成品臨時目錄, 然後執行 Roslyn。
>

如需 Roslyn 分析器工作的其他資源, 請查看[Microsoft Docs 上的 Roslyn 分析器](https://docs.microsoft.com/dotnet/standard/analyzers/)

您可以在[這裡](https://www.nuget.org/packages/Microsoft.CodeAnalysis.FxCopAnalyzers)找到此組建工作所安裝和使用的分析器套件 

## <a name="tslint-task"></a>TSLint 工作

如需 TSLint 的詳細資訊, 請造訪[TSLint GitHub](https://github.com/palantir/tslint)存放庫
>[!NOTE] 
>您可能會注意到, TSLint 會在2019中淘汰一段時間 (來源:[TSLint GitHub](https://github.com/palantir/tslint)存放庫)小組目前正在調查[ESLint](https://github.com/eslint/eslint)的替代方案, 並為 ESLint 建立新的工作。

## <a name="publish-security-analysis-logs-task"></a>發行安全性分析記錄工作
下列設定的螢幕擷取畫面和詳細資料。

![自訂發行安全性分析](./media/security-tools/9-publish-security-analsis-logs600.png)  

- 成品**名稱**-可以是任何字串識別碼
- 成品**類型**-您可以將記錄發佈到 Azure DevOps 伺服器, 或發行至可供組建代理程式存取的檔案共用。 
- **工具**-您可以選擇保留個別/特定工具的記錄, 或選取**所有工具**來保留所有記錄。 

## <a name="security-report-task"></a>安全性報告工作
下列設定的螢幕擷取畫面和詳細資料。  
![設定後續分析](./media/security-tools/4-createsecurityanalysisreport600.png) 
- **報表**-選擇要建立的報表檔案;其中一個將會在每個格式的**主控台**、 **TSV**和/或**HTML**中建立 
- **工具**-在您的組建定義中, 選取您想要偵測到的問題摘要的工具。 針對每個選取的工具, 可以選擇是否要只查看錯誤或報表中的錯誤和警告。 
- **Advanced Options** -如果未選取其中一個工具的記錄檔, 您可以選擇記錄警告或錯誤 (並使工作失敗)。
您可以自訂要在其中尋找記錄檔的 [基底記錄檔] 資料夾, 但這不是典型的案例。 

## <a name="post-analysis-task"></a>後續分析工作
下列設定的螢幕擷取畫面和詳細資料。

![自訂後續分析](./media/security-tools/a-post-analysis600.png) 
- **工具**-在您的組建定義中, 選取您想要根據其結果插入組建中斷的工具。 針對每個選取的工具, 可能會有選項可選取您是否只會在發生錯誤時中斷, 還是會發生錯誤和警告。 
- **報表**-您可以選擇性地將找到的結果寫入, 並導致 Azure DevOps 主控台視窗和記錄檔的組建中斷。 
- **Advanced Options** -如果未選取其中一個工具的記錄檔, 您可以選擇記錄警告或錯誤 (並使工作失敗)。

## <a name="next-steps"></a>後續步驟

如果您有關于擴充功能和所提供工具的進一步問題, 請[查看我們的常見問題頁面。](security-code-analysis-faq.md)


