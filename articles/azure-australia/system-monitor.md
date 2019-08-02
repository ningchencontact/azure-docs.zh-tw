---
title: Azure 澳大利亞的安全性系統監視
description: 在澳大利亞地區內設定系統監視的指引, 以符合澳大利亞政府原則、法規及法規的特定需求。
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: b68bdb6d1c349df67943889b511eac87c940b531
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68571883"
---
# <a name="system-monitoring-for-security-in-azure-australia"></a>Azure 澳大利亞的安全性系統監視

擁有健全的安全性策略, 包括即時監視和例行的安全性評量, 對於您的 IT 環境 (包括雲端) 的日常作業安全性而言, 是很重要的。

雲端安全性是客戶與雲端提供者之間的共同工作。 有四項服務 Microsoft Azure 提供來協助這些需求, 並考慮[澳大利亞網路安全中心 (ACSC) 資訊安全手動控制項](https://acsc.gov.au/infosec/ism/index.htm)(ISM) 中所包含的建議。具體而言, 是集中式事件記錄、事件記錄檔的執行, 以及安全性弱點評估與管理。 Microsoft Azure 服務包括:

* Azure 資訊安全中心
* Azure 監視器
* Azure 建議程式
* Azure 原則

ACSC 建議您針對**受保護**的資料使用這些服務。 藉由使用這些服務, 您可以主動監視及分析您的 IT 環境, 並做出明智的決策, 以充分運用資源來增強您的安全性。 這些服務都是組合解決方案的一部分, 可為您提供最佳的深入解析、建議和保護。

## <a name="azure-security-center"></a>Azure 資訊安全中心

[Azure 資訊安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)提供統一的安全性管理主控台, 可讓您用來監視及增強 Azure 資源和託管資料的安全性。 Azure 資訊安全中心包含安全分數, 這是根據 Azure Advisor 的最佳做法設定, 以及 Azure 原則的整體合規性分析的分數。

Azure 資訊安全中心為 Azure 客戶提供下列功能:

* 安全性原則、評定與建議
* 安全性事件集合與搜尋
* 存取和應用程式控制
* 先進的威脅偵測
* 即時虛擬機器存取控制
* 混合式安全性

Azure 資訊安全中心所監視的資源範圍可以擴充, 以包含混合式雲端環境中支援的內部部署資源。 這包括目前受支援的 System Center Operations Manager 版本所監視的內部部署資源。

資訊安全中心「標準」層也會提供「.Asd」必要的雲端式安全性控制項, 這是「[第8個](https://acsc.gov.au/publications/protect/essential-eight-explained.htm)」。 其中包括應用程式允許清單, 以及透過即時存取來限制系統管理許可權。

### <a name="azure-monitor"></a>Azure 監視器

[Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/overview)是適用于所有 Azure 資源的集中式記錄解決方案, 其中包含 Log Analytics 和 Application Insights。 系統會從 Azure 資源收集兩個主要資料類型: 記錄和計量。 一旦收集到 Azure 監視器, 各種不同的工具都可以使用記錄資訊, 而且有各種用途。

![Azure 監視器概觀](media/overview.png)

Azure 監視器也包含「Azure 活動記錄」。 SActivity 記錄會儲存在 Azure 內發生的所有訂用帳戶層級事件。 它可讓 Azure 客戶查看在其 Azure 資源上所進行的「誰」、「什麼」和「何時」作業。 使用內建的 Kusto 查詢語言, 可以分析傳送至 Azure 監視器和 Azure 活動記錄事件的資源型記錄。 然後可以匯出這些記錄檔, 用來建立自訂儀表板和視圖, 並設定來觸發警示和通知。

### <a name="azure-advisor"></a>Azure 建議程式

[Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-overview)分析 azure 訂用帳戶中支援的 azure 資源、系統產生的記錄檔, 以及目前的資源設定。 Azure Advisor 中提供的分析會即時產生, 並以 Microsoft 建議的最佳作法為基礎。 任何新增至您環境的受支援 Azure 資源都會分析, 並提供適當的建議。 Azure Advisor 建議會服裝依照為四個最佳做法分類:

* 安全性
* 高可用性
* 效能
* 成本

Azure Advisor 由 Azure 資訊安全中心所提供的整體安全性分析的一部分所產生的安全性建議。

Azure Advisor 所收集的資訊會提供系統管理員下列:

* 深入瞭解不符合建議最佳作法的資源設定
* 要執行之特定補救動作的指引
* 指出應採取哪些補救動作做為高優先順序的排名

### <a name="azure-policy"></a>Azure 原則

[Azure 原則](https://docs.microsoft.com/azure/governance/policy/overview)可讓您套用規則來管理 Azure 資源的類型和其允許的設定。 原則可以用來控制資源的建立和設定, 也可以用來在整個環境中審核設定。 這些審核結果可以用來形成補救活動的基礎。 Azure 原則不同于角色型存取控制 (RBAC);Azure 原則用來限制資源及其設定, RBAC 可用來限制對 Azure 使用者的特殊許可權存取。

無論是強制執行特定原則, 或正在進行原則的影響, 原則合規性都會持續受到監視, 而且系統管理員會提供整體和資源特定的合規性資訊。 Azure 原則的相容性資料會提供給 Azure 資訊安全中心, 並形成安全分數的一部分。

## <a name="key-design-considerations"></a>主要設計考慮

在執行事件記錄檔策略時, ACSC ISM 會強調下列考慮:

* 集中式記錄功能
* 要記錄的特定事件
* 事件記錄檔保護
* 事件記錄檔保留
* 事件記錄檔的審核

此外, 若要收集和記錄管理, ISM 也建議組織 IT 環境的例行弱點評估。

### <a name="centralised-logging"></a>集中式記錄

任何記錄解決方案都應該盡可能將已捕捉的記錄合併成單一資料存放庫。 這不僅可以降低操作複雜度, 也無法建立多個資料定址接收器, 它可讓從多個來源收集的資料分析在一起, 以允許識別任何相互關聯事件。 這對於偵測和管理任何網路安全性事件的範圍而言非常重要。

所有具有 Azure 監視器的 Azure 客戶都符合這項需求。 這項供應專案不僅在 Azure 中為所有 Azure 資源提供集中式記錄存放庫, 也可讓您將資料串流至 Azure 事件中樞。 Azure 事件中樞提供完全受控的即時資料內嵌服務。 一旦 Azure 監視器資料串流處理至 Azure 事件中樞, 資料也可以輕鬆地連接到現有支援的安全性資訊和事件管理 (SIEM) 存放庫, 以及其他協力廠商監視工具。

Microsoft 也提供自己的 Azure 原生 SIEM 解決方案 Azure Sentinel。 Azure Sentinel 支援各種不同的資料連線器, 並可用來監視整個企業的安全性事件。 藉由結合受支援[資料連線器](https://docs.microsoft.com/azure/sentinel/connect-data-sources)的資料、Azure Sentinel 的內建機器學習服務, 以及 Kusto 查詢語言, 系統會提供安全性系統管理員單一解決方案來偵測警示、威脅可見度、主動式搜尋、和威脅回應。 Sentinel 也提供搜尋和筆記本功能, 可讓安全性系統管理員在 reuseable 腳本中記錄可在組織內共用的所有步驟, 做為安全性調查的一部分。 安全性系統管理員甚至可以使用內建的[使用者分析](https://docs.microsoft.com/azure/sentinel/user-analytics)來調查單一提名使用者的動作。

### <a name="logged-events-and-log-detail"></a>記錄的事件和記錄詳細資料

ISM 提供事件記錄檔類型的詳細清單, 其中包含在任何記錄策略中。 任何已捕捉的記錄都必須包含足夠的詳細資料, 以做為分析和調查的實際使用。

在 Azure 中收集的記錄屬於下列三種類別的其中一種:

* **控制和記錄管理**檔:這些記錄會提供 Azure Resource Manager 建立、更新和刪除作業的相關資訊。

* **資料平面記錄**:這些包含在 Azure 資源使用量中引發的事件。 包含像是 Windows 事件記錄檔的來源, 包括系統、安全性和應用程式記錄檔。

* 已**處理的事件**:這些事件包含客戶代表 Azure 自動處理的事件和警示的相關資訊。 已處理事件的範例是 Azure 資訊安全中心警示。

針對 Windows 和 Linux 部署虛擬機器代理程式, 可增強 Azure 虛擬機器的監視功能。 這會明顯增加所搜集之記錄資訊的廣度。 此代理程式的部署可設定為透過 Azure 資訊安全中心自動進行。

Microsoft 會提供有關 Azure 資源特定記錄及其[架構](https://docs.microsoft.com/azure/security/azure-log-audit)的詳細資訊。

### <a name="log-retention-and-protection"></a>記錄保留和保護

 事件記錄檔必須安全地儲存, 以取得所需的保留期間。 ISM 會建議記錄最少保留七年。 Azure 提供了數種方法來確保收集記錄的長時間。 根據預設, Azure 記錄事件會儲存90天。 Azure 監視器所捕獲的記錄資料可以視長期保留的需要, 移至 Azure 儲存體帳戶並加以儲存。 儲存在 Azure 儲存體帳戶上的活動記錄可以保留一組天數, 或在必要時無限期保存。

用來儲存 Azure 記錄事件的 Azure 儲存體帳戶可以進行異地多餘, 而且可以使用 Azure 備份進行備份。 一旦由 Azure 備份所捕獲, 任何包含記錄的備份刪除都需要系統管理核准, 而標示為刪除的備份仍會保留14天, 以便進行復原。 Azure 備份允許受保護實例9999個複本, 提供超過27年的每日備份。

以角色為基礎的存取控制應該用來控制用於 Azure 記錄的資源存取權。 Azure 監視器、Azure 儲存體帳戶和 Azure 備份應該使用角色型存取控制進行設定, 以確保記錄中包含的資料安全性。

### <a name="log-auditing"></a>記錄檔的審核

分析之後, 就會 realised 記錄的 true 值。 使用自動化和手動分析, 並熟悉可用的工具, 將可協助您偵測及管理組織安全性原則和網路安全性事件的漏洞。 Azure 監視器提供一組豐富的工具來分析收集到的記錄。 這項分析的結果可以在系統、visualised 或簡易性之間以多種格式共用。

儲存在 Azure 監視器中的記錄資料會保留在 Log Analytics 工作區中。 所有分析都會以查詢開始。 Azure 監視器查詢是以 Kusto 查詢語言撰寫。 查詢會形成來自 Azure 監視器的所有輸出 (從 Azure 儀表板到警示規則) 的基礎。

![Azure 記錄查詢總覽](media/queries-overview.png)

您可以透過使用監視解決方案來增強記錄的審核。 這些是預先封裝的解決方案, 其中包含集合邏輯、查詢和資料視覺化 views。 Microsoft[提供](https://docs.microsoft.com/azure/azure-monitor/insights/solutions-inventory)一些監視解決方案, 以及產品廠商的其他解決方案, 可以在 Azure Marketplace 中找到。

### <a name="vulnerability-assessment-and-management"></a>弱點評估和管理

ISM 會注意到例行的弱點評估和管理是不可或缺的。 您的 IT 環境不斷演進, 而外部安全性威脅則不斷改變。 有了 Azure 資訊安全中心, 您就可以執行自動化弱點評估, 並取得如何規劃和執行補救活動的指引。

Azure 資訊安全中心中的安全分數會提供一份建議清單, 當您套用時, 將會改善您的環境安全性。 此清單會依對整體安全分數從最高到最低的影響進行排序。 依影響排序清單可讓您專注于最高優先順序的建議, 以增強安全性的最大價值。

Azure 原則也在進行中的弱點評估中扮演著重要的部分。 中可用的原則類型 Azure 原則範圍是強制資源標籤和值, 以限制可在其中建立資源的 Azure 區域, 同時封鎖特定資源類型的建立。 一組 Azure 原則可以分組為方案。 應用程式會用來套用相關的 Azure 原則, 並將其當做群組一起套用時, 形成特定安全性或合規性目標的基礎。

Azure 原則具有內建原則定義的程式庫, 其持續成長。 Azure 入口網站也可讓您選擇撰寫自己的自訂 Azure 原則定義。 一旦您在現有的程式庫中找到原則, 或建立一個新的, 您就可以將原則指派給 Azure 資源。 這些指派的[範圍](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)可以設定為資源管理階層中的各種層級。 繼承原則指派, 這表示範圍內的所有子資源都會收到相同的原則指派。 您也可以視需要將資源從範圍原則指派中排除。

所有已部署的 Azure 原則都會促成組織的安全分數。 在高度定制的環境中, 您可以建立和部署自訂 Azure 原則定義, 以提供專為特定工作負載量身打造的審核資訊。

## <a name="getting-started"></a>使用者入門

若要開始使用 Azure 資訊安全中心, 並充分利用 Azure 監視器、Advisor 和原則, Microsoft 建議使用下列初始步驟:

* 啟用 Azure 資訊安全中心
* 升級為標準層
* 啟用 Microsoft Monitoring Agent 自動布建至支援的 Azure 虛擬機器
* 在資訊安全中心儀表板上審查、prioritise 及緩和安全性建議和警示

## <a name="next-steps"></a>後續步驟

如需有關 Azure 澳大利亞資源的治理和控制的詳細資訊, 請參閱[Azure 原則和 Azure 藍圖](azure-policy.md), 以確保原則和法規合規性。
