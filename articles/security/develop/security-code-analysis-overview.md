---
title: Microsoft Azure 安全性程式碼分析檔總覽
description: 本文概述安全性程式碼分析延伸模組
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
ms.openlocfilehash: 283d63bafc583f2ac9da3294644aaebd17d7c950
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/01/2019
ms.locfileid: "68718284"
---
# <a name="about-microsoft-security-code-analysis"></a>關於 Microsoft 安全性程式碼分析

**Microsoft 安全性程式碼分析延伸**模組可讓小組將安全性程式碼分析順暢地整合到其 Azure DevOps CI/CD 管線中, 如同 Microsoft 的[安全開發生命週期 (SDL)](https://www.microsoft.com/securityengineering/sdl/practices)專家所建議。 安全性已透過一致的 UX 簡化, 可抽象化執行各種工具的複雜性。 有了以 NuGet 為基礎的工具傳遞, 小組就不再需要管理安裝或更新工具。 透過命令列和基本組建工作介面, 所有使用者 (從熟悉的工具專家到日常開發人員) 幾乎可以視需要對工具有很大的控制權。 小組也可以利用功能強大的後置處理功能, 例如發佈記錄以進行保留, 產生可操作的開發人員焦點報表 & 在回歸上設定組建中斷。

## <a name="why-microsoft-security-code-analysis"></a>為何要進行 Microsoft 安全性程式碼分析

### <a name="security-simplified"></a>簡化安全性

將安全性程式碼分析工具新增至您的 Azure DevOps 管線, 就像加入新工作一樣簡單。 使用預設值加以自訂或移出。 這些工作會做為 DevOps 管線的一部分來執行, 並產生記錄以詳述所有種類的結果。

### <a name="clean-builds"></a>清除組建

解決工具所報告的初始問題之後, 您可以設定延伸模組, 以中斷新問題的組建。 在每個提取要求上設定持續整合組建從未如此簡單!

### <a name="set-it-and-forget-it"></a>加以設定並忘記

[組建工作] 和 [工具] 可以設定為保持最新狀態 (而且預設為)。 如果有更新版本的工具, 就不需要下載並安裝它。此延伸模組會為您負責。 

>>>
### <a name="under-the-hood"></a>幕後

Microsoft 安全性程式碼分析延伸模組組建工作摘要了下列各項的複雜性:
  - 執行安全性靜態分析工具, 以及
  - 處理記錄檔的結果來建立摘要報告或中斷組建。
>>>

## <a name="security-code-analysis-toolset"></a>安全性程式碼分析工具組

Microsoft 安全性程式碼分析延伸模組可讓您立即取得最新版本的重要分析工具。 此延伸模組包含 Microsoft 內部和開放原始碼工具。 當您設定 & 使用對應的組建工作執行管線時, 工具會在雲端主控的代理程式上自動下載。 以下是目前延伸模組中提供的一組工具。 隨時掌握更多資訊, 並將您的建議傳送給我們, 以取得可新增的工具。

### <a name="anti-malware-scanner"></a>反惡意程式碼掃描器

[反惡意程式碼掃描器] 組建工作現在包含在 [Microsoft 安全性程式碼分析] 延伸模組中。 它必須在已經安裝 Windows Defender 的組建代理程式上執行。 如需詳細資訊, 請造訪[Defender 網站](https://aka.ms/defender) 

### <a name="binskim"></a>BinSkim

BinSkim 是可移植的執行檔 (PE) 輕量掃描器, 可驗證編譯器/連結器設定和其他安全性相關的二進位特性。 Build 工作提供 BinSkim 應用程式的命令列包裝函式。 BinSkim 是一種開放原始碼工具, 如需詳細資訊, 請造訪[GitHub 上的 BinSkim](https://github.com/Microsoft/binskim)

### <a name="credential-scanner"></a>認證掃描器

原始碼中儲存的密碼和其他秘密, 都是很重要的問題。 認證掃描器是一種專屬的靜態分析工具, 可在您的原始程式碼和組建輸出中偵測認證、秘密、憑證和其他機密內容。

### <a name="microsoft-security-risk-detection"></a>Microsoft 安全性風險偵測

安全性風險偵測是 Microsoft 獨特的雲端架構模糊測試服務, 用以識別軟體中可利用的安全性錯誤。 此服務需要個別的上架程式。 如需詳細資訊, 請造訪[docs.microsoft.com 上的 MSRD](https://docs.microsoft.com/security-risk-detection/)

### <a name="roslyn-analyzers"></a>Roslyn 分析器

Microsoft 編譯器整合的靜態分析工具, 用來分析 managed 程式C#代碼 (和 VB)。 如需詳細資訊, 請造訪[docs.microsoft.com 上的 Roslyn 分析器](https://docs.microsoft.com/dotnet/standard/analyzers/)

### <a name="tslint"></a>TSLint

TSLint 是可延伸的靜態分析工具, 可檢查 TypeScript 程式碼的可讀性、可維護性和功能錯誤。 它在現代化的編輯器和組建系統上廣泛支援, 而且可以使用您自己不起毛的規則、設定和格式器進行自訂。 TSLint 是一種開放原始碼工具, 如需詳細資訊, 請造訪[GitHub 上的 TSLint](https://github.com/palantir/tslint)

## <a name="analysis-and-post-processing-of-results"></a>分析和後置處理結果

Microsoft 安全性程式碼分析延伸模組也有三個有用的後置處理工作, 可協助您處理和分析安全性工具工作所找到的結果。 它們通常會在所有其他工具工作之後新增到管線中。

### <a name="publish-security-analysis-logs"></a>發行安全性分析記錄
「發行安全性分析記錄」組建工作會保留在組建期間執行之安全性工具的記錄檔, 以便進行調查和追蹤。

它們可以發佈至 Azure 伺服器成品 (作為 zip 檔案), 或從您的私用組建代理程式複製到可存取的檔案共用。

### <a name="security-report"></a>安全性報告
「安全性報告組建」工作會剖析在組建期間, 安全性工具所建立的記錄檔, 並建立單一摘要報告檔案, 其中包含分析工具所找到的所有問題。

此工作可設定為報告特定工具或所有工具的結果, 而且您也可以選擇要報告的問題層級 (錯誤或錯誤和警告)。

### <a name="post-analysis-build-break"></a>後續分析 (組建中斷)
[後期分析組建] 工作可讓客戶插入組建中斷, 並在程式碼中有一或多個分析工具報告結果或問題時使組建失敗。

此工作可設定為針對特定工具或所有工具所找到的問題, 以及根據找到的問題嚴重性 (錯誤或警告) 來中斷組建。

>[!NOTE]
>根據設計, 個別組建工作將會成功, 只要工具順利完成, 不論是否有發現, 而組建可以執行到完成, 以允許執行所有工具。

## <a name="next-steps"></a>後續步驟

如需有關上架和安裝安全性程式碼分析的指示, 請參閱我們的上線[和安裝指南](security-code-analysis-onboard.md)

如需設定組建工作的詳細資訊, 請參閱我們的設定[指南](security-code-analysis-customize.md)

如果您有關于擴充功能和所提供工具的進一步問題, 請[查看我們的常見問題頁面。](security-code-analysis-faq.md)