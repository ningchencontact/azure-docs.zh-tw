---
title: 資訊安全中心的停用功能 (7 月 2019) |Microsoft Docs
description: 本文詳細說明將於 2019 年 7 月 31 日淘汰的資訊安全中心的功能。
services: security-center
author: yoavfrancis
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.date: 4/16/2019
ms.author: yoafr
ms.openlocfilehash: 392782310d8bc3b38a3dd1349cb1760ca287acd1
ms.sourcegitcommit: 2c09af866f6cc3b2169e84100daea0aac9fc7fd0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2019
ms.locfileid: "64875594"
---
# <a name="retirement-of-security-center-features-july-2019"></a>停用的資訊安全中心功能 (第 2019 年 7 月)

我們已進行數個[改進](https://azure.microsoft.com/updates/?product=security-center)過去 6 個月的 Azure 資訊安全中心。  
改進的功能我們會移除一些多餘的功能，以及相關的 Api，從資訊安全中心，於 2019 年 7 月 31 日。  

已淘汰的功能大多可以取代為 Azure 資訊安全中心或 Log Analytics; 中的新功能並有幾項功能可以使用來實作[Azure Sentinel （預覽）](https://azure.microsoft.com/services/azure-sentinel/)、 近期宣布。

從資訊安全中心即將淘汰的功能清單包括：

- [事件儀表板](#menu_events)
- [搜尋功能表項目](#menu_search)
- [檢視傳統身分識別和存取連結在身分識別與存取 （預覽）](#menu_classicidentity)
- [安全性事件對應上的安全性警示對應 （預覽） 按鈕](#menu_securityeventsmap)
- [自訂警示規則 （預覽）](#menu_customalerts)
- [調查威脅防護安全性警示中的按鈕](#menu_investigate)
- [子集合的安全性解決方案](#menu_solutions)
- [編輯安全性原則的安全性設定](#menu_securityconfigurations)
- [安全性和稽核儀表板 （原本是在 OMS 入口網站中使用） Log Analytics 工作區。](#menu_securityomsdashboard)

下面提供的詳細的資訊，針對每個已停用的功能和使用取代功能，您可以採取的步驟。

## 事件儀表板<a name="menu_events"></a>
資訊安全中心使用 Microsoft Monitoring Agent 收集機器的各種安全性相關組態和事件，並將這些事件儲存在您的工作區。 [事件儀表板](https://docs.microsoft.com/azure/security-center/security-center-events-dashboard)可檢視此資料，以及基本上至 Log Analytics 會提供其他的進入點。

接下來的事件儀表板將會淘汰：

![事件的工作區選取畫面][1]

一旦使用者按一下工作區中，會出現事件儀表板也將會淘汰：

![事件儀表板][2]

### <a name="events-dashboard---new-experience"></a>事件儀表板-新的體驗

客戶會建議使用 Log Analytics 的原生功能來檢視他們的工作區上的值得注意的事件。
如果您已經從資訊安全中心建立自訂值得注意的事件，這些都可以透過 Log analytics]-> [選取工作區]-> [已儲存的搜尋。 您的資料將不會遺失也不修改。 原生值得注意的事件也可從相同的畫面。

![工作區中儲存的搜尋][3]

## 搜尋功能表項目<a name="menu_search"></a>
目前，azure 資訊安全中心會使用 Azure 監視器記錄檔搜尋來擷取和分析安全性資料。 基本上，此畫面會做為 Log analytics 外觀 」[搜尋](https://docs.microsoft.com/azure/security-center/security-center-search)」 頁面-允許使用者在其選取的工作區上執行搜尋查詢。 接下來的這個 [搜尋] 視窗將會淘汰：

![Search page][4]

### <a name="search-menu-entry---new-experience"></a>搜尋功能表項目-新的體驗

客戶會建議您使用**Log Analytics**執行其工作區上的搜尋查詢的原生功能。 若要這樣做，他們可以移至在 Azure 中的 Log analytics，然後選取：「 記錄 」:

![Log Analytics 記錄檔 頁面][5]

## 傳統的身分識別與存取權 （預覽）<a name="menu_classicidentity"></a>
資訊安全中心中的 「 傳統 」 身分識別和存取體驗有提供一個方式，讓客戶檢視其身分識別和存取 log analytics 中的相關的資訊。 這樣做，請遵循下方按一下：

按一下 「 檢視傳統身分識別與存取權 」

![身分識別頁面][6]

畫面便會開啟此頁面，以及 「 身分識別和存取儀表板 」:

![身分識別] 頁面-[工作區選取][7]

按一下工作區會開啟 「 身分識別和存取 「 log analytics 儀表板客戶可在其中檢視識別和存取其工作區的詳細資訊：

![身分識別頁面-儀表板][8]

接下來的所有三個畫面上方將被淘汰。 您的資料會繼續保留在 log analytics 的安全性解決方案和將無法修改或移除。

### <a name="classic-identity--access-preview---new-experience"></a>傳統身分識別與存取權 （預覽）-新的體驗
原生的資訊安全中心功能雖然 Log analytics 儀表板上指定的工作區只提供深入解析，提供所有訂用帳戶和所有與其相關聯，在簡單的工作區的可視性-若要使用檢視，可讓您專注於功能的重要的是，根據您的身分識別和存取建議的安全分數。
身分識別和存取 Log analytics 儀表板的所有功能可以資訊安全中心內觸都達選取 「 身分識別與存取 （預覽） 」 之用：

![身分識別頁面-傳統體驗淘汰][9]

## 安全性事件對應<a name="menu_securityeventsmap"></a>
資訊安全中心可提供您[地圖](https://docs.microsoft.com/azure/security-center/security-center-threat-intel)，可協助您識別對環境的安全性威脅。 儀表板，讓使用者能夠檢視所選工作區上的原始安全性事件會導致該對應中的 移至安全性事件對應 按鈕。
[] 按鈕，以及每個工作區的儀表板，將會移除之後已被取代。

![安全性警示 map-按鈕][10]

現在當您按一下 [執行至安全性事件對應] 會開啟威脅情報儀表板。 威脅情報儀表板將被淘汰。  

![威脅情報儀表板][11]

當您選擇工作區以檢視其威脅情報儀表板時，安全性警示 map(Preview) 畫面*Log Analytics 中*開啟。 此畫面將被淘汰。

![在 Log Analytics 中的安全性警示對應][12]

您現有的資料會繼續保留在 log analytics 的安全性解決方案和將不會修改也不移除。

### <a name="security-events-map---new-experience"></a>安全性事件對應新的體驗
我們鼓勵客戶使用資訊安全中心內建的警示對應功能-[安全性警示對應 （預覽）]。 這會提供最佳的體驗，並允許巨集的可跨所有訂用帳戶和相關聯的工作區，檢視您的環境，並著重於單一工作區。

## 自訂警示規則 （預覽）<a name="menu_customalerts"></a>
自訂警示體驗會[淘汰](https://docs.microsoft.com/azure/security-center/security-center-custom-alert)2019 年 6 月 30 日，因為基礎結構的停用此功能建置在。 在取代之前時間範圍內，使用者將能夠編輯現有的自訂警示規則，但是不能加入新的。 若要啟用建議使用者[Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/)與自動移轉其現有的警示和建立新的或或者重新建立其警示與 Azure 監視器的單鍵登入的記錄警示。

若要保留您現有的警示，並將它們移轉到 Azure 的 Sentinel，請啟動 Azure Sentinel。 第一個步驟中，選取 [儲存您自訂的警示] 工作區，然後選取 「 分析 」 功能表項目，將會自動移轉您的警示。

![自訂警示][13]

客戶不想要上架到 Azure Sentinel 都來重新建立其警示與 Azure 監視器記錄檔會發出警示。 如需指示，請參閱： [建立、 檢視及管理使用 Azure 監視器的記錄警示](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)。 如需有關如何建立記錄警示的相關指示，請參閱：[Azure 監視器中的記錄警示](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log)。

如需有關自訂警示停用的詳細資訊，請參閱[自訂資訊安全中心警示文件](https://docs.microsoft.com/azure/security-center/security-center-custom-alert)。

## 安全性警示調查<a name="menu_investigate"></a>
[調查功能](https://docs.microsoft.com/azure/security-center/security-center-investigation)在資訊安全中心可讓您分級、 了解範圍，並追蹤潛在的安全性事件的根本原因。 這項功能將會移除從資訊安全中心，因為它已取代為較佳的體驗，在[Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/)。

![安全性事件][14]

當您按一下上方的 [調查] 按鈕時，會開啟 「 調查儀表板 （預覽） 」 在 Log Analytics 中。 調查儀表板將被淘汰。  
您現有的資料會繼續保留在 Log Analytics 的安全性解決方案和將不會修改也不移除。

![在 Log Analytics 中的調查儀表板][15]

### <a name="investigation---new-experience"></a>調查-新的體驗

我們鼓勵客戶上架[Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/)豐富的調查體驗，請由 捕捉功能的警示。 Azure 的 Sentinel 提供功能強大的追捕搜尋和查詢工具，來搜尋有安全性威脅，跨組織的資料來源。  

## 安全性解決方案的子集<a name="menu_solutions"></a>

資訊安全中心能夠啟用[整合式安全性解決方案，在 Azure 中的](https://docs.microsoft.com/azure/security-center/security-center-partner-integration)。 會移除，並支援下列合作夥伴解決方案[Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/)，以及更多的資料來源。

- 下一代防火牆及 Web 應用程式防火牆解決方案 (Azure Sentinel[文件](https://docs.microsoft.com/azure/sentinel/connect-data-sources))
- 支援常見事件格式 (CEF) 的安全性解決方案的整合 (Azure Sentinel[文件](https://docs.microsoft.com/azure/sentinel/connect-common-event-format))
- Microsoft Advanced Threat Analytics (Azure 的 Sentinel[文件](https://docs.microsoft.com/azure/sentinel/connect-azure-atp))
- Azure AD Identity Protection (Azure 的 Sentinel[文件](https://docs.microsoft.com/azure/sentinel/connect-azure-ad-identity-protection))

下列已被取代，使用者將無法加入新的或修改現有連線的解決方案，前面所述，從 UI 和 API 的類型。
現有連線的解決方案，建議將移至 Azure 的 Sentinel 這段期間的結尾。

![安全性中心解決方案][16]

## 編輯安全性原則的安全性設定<a name="menu_securityconfigurations"></a>
Azure 資訊安全中心藉由套用一組[具有超過 150 個建議規則的集合](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) \(英文\)，來監視安全性設定以強化 OS，其中包含與防火牆、稽核及密碼原則等相關的規則。 如果發現電腦設定有弱點，資訊安全中心會產生安全性建議。 [編輯安全性設定畫面](https://docs.microsoft.com/azure/security-center/security-center-customize-os-security-config)可讓客戶自訂資訊安全中心在預設 OS 安全性設定。

這項功能已在預覽中，並將停用。

![編輯安全性設定][17]

### <a name="edit-security-configurations---new-experience"></a>編輯安全性組態]-[新的體驗

資訊安全中心將會支援[客體代理程式](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration)在不久的將來，允許更豐富的功能集-包括支援其他作業系統和客體組態使用 Azure 原則 （在客體的整合原則）。 這也會提供大規模的控制，並自動套用新的資源的能力。

## 安全性和稽核儀表板 （原本是在 OMS 入口網站中使用） Log Analytics 工作區<a name="menu_securityomsdashboard"></a>

在 Log analytics 中的安全性儀表板提供值得注意的安全性事件和潛在威脅、 威脅情報對應，以及安全性事件儲存在工作區的身分識別和存取評估的每個工作區概觀。 接下來將移除儀表板。 我們已建議 UI 儀表板中，我們建議使用者使用 Azure 資訊安全中心從現在開始。

![Log analytics 的安全性儀表板][18]

### <a name="security--audit-dashboard---new-experience"></a>安全性和稽核儀表板-新的體驗
建議客戶使用 Azure 資訊安全中心，可跨多個訂用帳戶和與其相關聯，以及更豐富的功能集的工作區提供相同的安全性概觀。

## <a name="next-steps"></a>後續步驟
- 深入了解 [Azure 資訊安全中心](https://docs.microsoft.com/azure/security-center/)
- 深入了解[Azure Sentinel](https://docs.microsoft.com/azure/sentinel)

<!--Image references - events-->
[1]: ./media/security-center-features-retirement-july2019/asc_events_dashboard.png
[2]: ./media/security-center-features-retirement-july2019/asc_events_dashboard_inner.png
[3]: ./media/security-center-features-retirement-july2019/workspace_saved_searches.png
<!--Image references - search-->
[4]: ./media/security-center-features-retirement-july2019/asc_search.png
[5]: ./media/security-center-features-retirement-july2019/workspace_logs.png
<!--Image references - classic identity and access-->
[6]: ./media/security-center-features-retirement-july2019/asc_identity.png
[7]: ./media/security-center-features-retirement-july2019/asc_identity_workspace_selection.png
[8]: ./media/security-center-features-retirement-july2019/loganalytics_dashboard_identity.png
[9]: ./media/security-center-features-retirement-july2019/asc_identity_nobuttonhighlight.png
<!--Image references - alerts map-->
[10]: ./media/security-center-features-retirement-july2019/asc_security_alerts_map.png
[11]: ./media/security-center-features-retirement-july2019/asc_threat_intellignece_dashboard.png
[12]: ./media/security-center-features-retirement-july2019/loganalytics_security_alerts_map.png
<!--Image references - custom alerts-->
[13]: ./media/security-center-features-retirement-july2019/asc_custom_alerts.png
<!--Image references - Investigation-->
[14]: ./media/security-center-features-retirement-july2019/asc-security-incident.png
[15]: ./media/security-center-features-retirement-july2019/loganalytics_investigation_dashboard.png
<!--Image references - Solutions-->
[16]: ./media/security-center-features-retirement-july2019/asc_security_solutions.png
<!--Image references - Edit security configurations-->
[17]: ./media/security-center-features-retirement-july2019/asc_edit_security_configurations.png
<!--Image references - Security dashboard in log analytics-->
[18]: ./media/security-center-features-retirement-july2019/loganalytics_security_dashboard.png
