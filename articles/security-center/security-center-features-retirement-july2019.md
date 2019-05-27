---
title: 資訊安全中心的停用功能 (7 月 2019) |Microsoft Docs
description: 本文說明將於 2019 年 7 月 31 日淘汰的資訊安全中心中的功能。
services: security-center
author: yoavfrancis
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.date: 4/16/2019
ms.author: yoafr
ms.openlocfilehash: 069345f9c2d0fff0b580365153d8be13bb4ba204
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2019
ms.locfileid: "65952147"
---
# <a name="retirement-of-security-center-features-july-2019"></a>停用的資訊安全中心功能 (第 2019 年 7 月)

我們做了數個[改進](https://azure.microsoft.com/updates/?product=security-center)過去六個月內的 Azure 資訊安全中心。
這些改良功能，我們會移除一些多餘的功能和相關的 Api 從資訊安全中心於 2019 年 7 月 31 日。  

大部分的這些淘汰的功能可以取代 Azure 資訊安全中心或 Azure Log Analytics 中的新功能。 可以使用來實作其他功能[Azure Sentinel （預覽）](https://azure.microsoft.com/services/azure-sentinel/)。

資訊安全中心即將淘汰的功能包括：

- [事件儀表板](#menu_events)
- [搜尋功能表項目](#menu_search)
- [檢視傳統身分識別和存取連結在身分識別與存取 （預覽）](#menu_classicidentity)
- [安全性事件對應上的安全性警示對應 （預覽） 按鈕](#menu_securityeventsmap)
- [自訂警示規則 （預覽）](#menu_customalerts)
- [調查威脅防護安全性警示中的按鈕](#menu_investigate)
- [子集合的安全性解決方案](#menu_solutions)
- [編輯安全性原則的安全性設定](#menu_securityconfigurations)
- [安全性和稽核儀表板 （原本是在 OMS 入口網站中使用） Log Analytics 工作區](#menu_securityomsdashboard)

這篇文章提供您可以採取來實作取代功能的每個已淘汰的功能和步驟詳細的資訊。

## 事件儀表板<a name="menu_events"></a>

資訊安全中心會使用 Microsoft Monitoring Agent，從機器收集各種安全性相關組態和事件。 它會將這些事件儲存在您的工作區中。 [事件儀表板](https://docs.microsoft.com/azure/security-center/security-center-events-dashboard)可讓您檢視這項資料，並提供您的進入點至 Log Analytics。

我們要淘汰選取的工作區時，會出現事件儀表板：

![事件儀表板][2]

### <a name="events-dashboard---the-new-experience"></a>事件儀表板-新的體驗

我們鼓勵您使用 Azure Log Analytics 的原生功能，若要檢視您的工作區上的值得注意的事件。

如果您已在資訊安全中心建立自訂值得注意的事件，它們會存取。 在 Log Analytics 中，移至**選取工作區** > **已儲存的搜尋**。 不會遺失或修改您的資料。 原生值得注意的事件也可從 Log Analytics 中相同的畫面。

![工作區中儲存的搜尋][3]

## 搜尋功能表項目<a name="menu_search"></a>

目前，azure 資訊安全中心會使用 Azure 監視器記錄檔搜尋來擷取和分析安全性資料。 此畫面做為 Log Analytics 搜尋頁面中，視窗，並可讓使用者在其選取的工作區上執行搜尋查詢。 如需詳細資訊，請參閱 < [Azure 資訊安全中心搜尋](https://docs.microsoft.com/azure/security-center/security-center-search)。 我們正在淘汰此搜尋 視窗：

![Search page][4]

### <a name="search-menu-entry---the-new-experience"></a>搜尋功能表項目-新的體驗

我們鼓勵您使用的 Azure Log Analytics 原生功能來執行您的工作區上的搜尋查詢。 移至 Azure Log Analytics 並選取**記錄檔**。

![Log Analytics 記錄檔 頁面][5]

## 傳統的身分識別與存取權 （預覽）<a name="menu_classicidentity"></a>

資訊安全中心的傳統身分識別和存取體驗目前顯示 Log Analytics 中的身分識別和存取資訊的儀表板。 若要檢視此儀表板：

1. 選取 **檢視傳統身分識別和存取**。

   ![身分識別頁面][6]

1. 檢視**身分識別和存取儀表板**。

    ![身分識別] 頁面-[工作區選取][7]

1. 選取要開啟的工作區**身分識別和存取**檢視身分識別和存取您的工作區的詳細資訊的 Log Analytics 中的儀表板。

   ![身分識別頁面-儀表板][8]

我們正在淘汰先前的步驟中所示的所有三個畫面。 您的資料仍可使用 Log Analytics 的安全性解決方案中，並不會修改或移除。

### <a name="classic-identity--access-preview---the-new-experience"></a>傳統身分識別與存取權 （預覽）-新的體驗

Log Analytics 儀表板上的單一工作區顯示深入解析。 不過，原生的資訊安全中心功能會提供所有訂用帳戶和所有與其相關聯的工作區的可視性。 您可以存取簡單-若要使用檢視，可讓您專注於重要與建議根據其安全分數排名。

所有功能**身分識別與存取權**選取 Log Analytics 中的儀表板可以觸達**身分識別與存取權 （預覽）** 資訊安全中心內。

![身分識別頁面-傳統體驗淘汰][9]

## 安全性事件對應<a name="menu_securityeventsmap"></a>

資訊安全中心可提供您[安全性警示對應](https://docs.microsoft.com/azure/security-center/security-center-threat-intel)來協助識別安全性威脅。 **移至安全性事件對應**該對應中的按鈕會開啟 儀表板，可讓您檢視所選工作區上的原始安全性事件。

我們正在消除**移至安全性事件對應**按鈕和每個工作區的儀表板。

![安全性警示 map-按鈕][10]

當您選取**移至安全性事件對應**] 按鈕，開啟 [威脅情報儀表板。 我們即將退休威脅情報儀表板。  

![威脅情報儀表板][11]

當您選擇工作區以檢視其威脅情報儀表板時，您會開啟 Log Analytics 中的 [安全性警示對應 （預覽）] 畫面。 我們正在淘汰此畫面。

![在 Log Analytics 中的安全性警示對應][12]

將現有的資料仍可使用 Log Analytics 的安全性解決方案中，而且不會修改和移除。

### <a name="security-events-map---the-new-experience"></a>安全性事件對應新的體驗

我們鼓勵您使用內建在資訊安全中心的警示對應功能：**安全性警示對應 （預覽）**。 這項功能提供最佳化的體驗，適用於所有訂用帳戶和相關聯的工作區。 它可讓您整個環境的高階檢視，並不著重於單一工作區。

## 自訂警示規則 （預覽）<a name="menu_customalerts"></a>

我們[淘汰自訂警示體驗](https://docs.microsoft.com/azure/security-center/security-center-custom-alert)於 2019 年 6 月 30 日淘汰與其基礎結構，因為。 在那之前，您可以編輯現有的自訂警示規則，但您無法新增新的。 我們建議您啟用[Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/)自動移轉您現有的警示，並建立新的。 或者，您可以建立您的警示與 Azure 監視器記錄檔會發出警示。

若要保留您現有的警示，並將它們移轉到 Azure Sentinel:

1. 開啟 Azure Sentinel，然後選取 儲存您自訂的警示 工作區。
1. 選取 [ **Analytics**自動移轉您的警示] 功能表中。

![自訂警示][13]

如果您不感興趣轉換到 Azure 的 Sentinel，我們鼓勵您使用 Azure 監視器記錄警示建立您的警示。 如需相關指示，請參閱 <<c0>  [ 建立、 檢視及管理使用 Azure 監視器的記錄警示](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)並[Azure 監視器中的記錄警示](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log)。

如需有關自訂警示停用的詳細資訊，請參閱[Azure 資訊安全中心 （預覽） 中的自訂警示規則](https://docs.microsoft.com/azure/security-center/security-center-custom-alert)。

## 安全性警示調查<a name="menu_investigate"></a>

[調查功能](https://docs.microsoft.com/azure/security-center/security-center-investigation)在資訊安全中心可協助您分級潛在的安全性事件。 此功能可讓您了解事件的範圍，並追蹤其根本原因。 我們要移除這項功能從資訊安全中心，因為它與改善的體驗已取代[Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/)。

![安全性事件][14]

當您選取**調查**按鈕**安全性事件** 畫面上，開啟 Log Analytics 中的 調查儀表板 （預覽）。 我們即將退休調查儀表板。  

將現有的資料仍可使用 Log Analytics 的安全性解決方案中，而且不會修改和移除。

![在 Log Analytics 中的調查儀表板][15]

### <a name="investigation---the-new-experience"></a>調查-新的體驗

我們建議您轉換到[Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/)豐富的調查體驗。 Azure 的 Sentinel 提供功能強大的搜尋和查詢工具来提升的安全性威脅，跨組織的資料來源。  

## 安全性解決方案的子集<a name="menu_solutions"></a>

資訊安全中心可以讓[整合式安全性解決方案，在 Azure 中的](https://docs.microsoft.com/azure/security-center/security-center-partner-integration)。 我們要從資訊安全中心中淘汰下列合作夥伴解決方案。 這些解決方案中已啟用[Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/)以及數個其他資料來源。

- [下一代防火牆及 web 應用程式防火牆解決方案](https://docs.microsoft.com/azure/sentinel/connect-data-sources)
- [支援常見事件格式 (CEF) 的安全性解決方案的整合](https://docs.microsoft.com/azure/sentinel/connect-common-event-format)
- [Microsoft Advanced Threat Analytics](https://docs.microsoft.com/azure/sentinel/connect-azure-atp)
- [Azure AD Identity Protection](https://docs.microsoft.com/azure/sentinel/connect-azure-ad-identity-protection)

停用之後, 您將無法新增或修改任何提及在上述清單中，從 UI 或 API 的方案類型。

如果您有現有的連線的解決方案，我們鼓勵您移至 Azure 的 Sentinel。

![安全性中心解決方案][16]

## 編輯安全性原則的安全性設定<a name="menu_securityconfigurations"></a>

Azure 資訊安全中心監視安全性組態所套用的一組[超過 150 個建議規則](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335)。 以強化 OS。 這些規則是有關防火牆、 稽核、 密碼原則，以及更多。 如果發現電腦設定有弱點，資訊安全中心會產生安全性建議。 [編輯安全性設定畫面](https://docs.microsoft.com/azure/security-center/security-center-customize-os-security-config)可讓客戶自訂資訊安全中心在預設 OS 安全性設定。

我們即將退休這項預覽功能。 如果在淘汰日期之後，您想要重設您的安全性組態，為其預設值，則可以透過 API 或 Powershell 使用[指示](https://aka.ms/ascresetsecurityconfigurations)

![編輯安全性設定][17]

### <a name="edit-security-configurations---the-new-experience"></a>編輯安全性組態]-[新的體驗

我們想要啟用資訊安全中心，以支援[客體設定代理程式](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration)。 這類更新，可讓更豐富的功能集，包括支援更多的作業系統和客體組態的 Azure 客體中原則的整合。 啟用這些變更後，您也必須能夠控制大規模的組態，並自動將它們套用至新的資源。

## Log Analytics 工作區的安全性和稽核儀表板<a name="menu_securityomsdashboard"></a>

原本在 OMS 入口網站中使用的安全性和稽核儀表板。 在 Log Analytics 中的儀表板會提供值得注意的安全性事件和威脅、 威脅情報對應及安全性事件儲存在工作區的身分識別和存取評估的每個工作區概觀。 我們要移除儀表板。 我們已建議 UI 儀表板中，我們建議您轉換至 Azure 資訊安全中心。

![Log Analytics 的安全性儀表板][18]

### <a name="security-and-audit-dashboard---the-new-experience"></a>安全性和稽核儀表板-新的體驗

我們建議您切換至 Azure 資訊安全中心。 它提供跨多個訂用帳戶的相同安全性概觀工作區相關聯，以及更豐富的功能集。

您可以取得原始的 Log Analytics 查詢來擴展中的安全性和稽核儀表板[GitHub 存放庫](https://github.com/Azure/Azure-Security-Center/tree/master/Legacy%20Log%20Analytics%20dashboards)資訊安全中心。

## <a name="next-steps"></a>後續步驟

- 深入了解 [Azure 資訊安全中心](https://docs.microsoft.com/azure/security-center/)。
- 深入了解[Azure Sentinel](https://docs.microsoft.com/azure/sentinel)。

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
