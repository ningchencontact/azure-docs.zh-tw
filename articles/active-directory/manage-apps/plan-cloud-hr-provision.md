---
title: 規劃雲端 HR 應用程式以 Azure Active Directory 使用者布建
description: 本文說明將雲端 HR 系統（例如 Workday 和 Sucessfactors）與 Azure Active Directory 整合的部署流程。 將 Azure AD 與您的雲端 HR 系統整合，會產生完整的身分識別生命週期管理系統。
services: active-directory
author: martincoetzer
manager: CelesteDG
tags: azuread
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 11/22/2019
ms.author: martinco
ms.reviewer: arvindha
ms.openlocfilehash: 5d55aafc29b3b022d1023077d2d8f459b0608ae7
ms.sourcegitcommit: 428fded8754fa58f20908487a81e2f278f75b5d0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/27/2019
ms.locfileid: "74555648"
---
# <a name="plan-cloud-hr-application-to-azure-active-directory-user-provisioning"></a>規劃雲端 HR 應用程式以 Azure Active Directory 使用者布建

在過去，IT 人員依賴手動方法來建立、更新和刪除員工，例如上傳 CSV 檔案，或使用自訂腳本來同步處理員工資料。 這些布建程式既容易出錯、不安全，而且難以管理。

為了順暢地管理員工、廠商或臨時員工的端對端身分識別生命週期， [Azure Active Directory （Azure AD）使用者](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)布建服務會提供與雲端式人力資源（HR）應用程式（例如 Workday 或 SuccessFactors）的整合。

Azure AD 使用這項整合來啟用下列雲端 HR 應用程式（應用程式）工作流程：

- 將雲端 HR 應用程式中的**AD**布建所選使用者集合布建到一或多個 ACTIVE DIRECTORY （AD）網域。
- **將僅限雲端的使用者**布建至未使用 AD 的 Azure AD 案例，將使用者直接從雲端 HR 應用程式布建到 Azure AD。
- **回寫至雲端 HR 應用程式。** -將電子郵件地址和使用者名稱屬性從 Azure AD 寫回雲端 HR 應用程式。

> [!NOTE]
> 此部署計畫會引導您使用 Azure AD 使用者布建來部署您的雲端 HR 應用程式工作流程。 如需將自動使用者布建部署至 SaaS 應用程式的相關資訊，請參閱[規劃自動使用者](https://aka.ms/deploymentplans/provisioning)布建部署。

## <a name="enabled-hr-scenarios"></a>啟用的 HR 案例

Azure AD 的使用者布建服務可自動化下列以 HR 為基礎的身分識別生命週期管理案例：

- **雇用新員工**-將新員工新增至雲端 hr 應用程式時，會在 AD 中自動建立使用者帳戶，並使用將電子郵件地址和使用者名稱屬性寫回至雲端 HR 應用程式的選項 Azure AD。
- **員工屬性和設定檔更新**-在雲端 HR 應用程式中更新員工記錄時（例如其名稱、標題或管理員），其使用者帳戶將會在 AD 中自動更新，並 Azure AD。
- **員工終止**-當員工在雲端 HR 應用程式中終止時，其使用者帳戶會在 AD 中自動停用，並 Azure AD。
- **Employee rehires** -當員工在雲端 HR 應用程式中 workday 時，其舊帳戶可以自動重新啟用或重新布建至 AD 和 Azure AD。

## <a name="who-is-this-integration-best-suited-for"></a>此整合最適合哪一種？

雲端 HR 應用程式與 Azure AD 使用者布建整合非常適合下列組織：

- 為雲端 HR 使用者布建提供預先建立的雲端式解決方案
- 需要從雲端 HR 應用程式直接將使用者布建至 AD，或 Azure AD
- 需要使用從雲端 HR 應用程式取得的資料來布建使用者
- 視雲端 HR 應用程式中偵測到的變更資訊而定，需要加入、移動及讓使用者同步至一或多個 AD 樹系、網域和 Ou
- 在電子郵件中使用 Office 365

## <a name="learn"></a>學習

使用者布建會建立持續身分識別管理的基礎，並增強依賴授權身分識別資料的商務程式品質。

### <a name="terms"></a>詞彙

本文使用下列詞彙：

- **來源系統**-Azure AD 布建的使用者存放庫（例如 Workday 和 SuccessFactors 等雲端 HR 應用程式）。
- **目標系統**-Azure AD 布建到的使用者存放庫（例如，AD、Azure AD、Office365 或其他 SaaS 應用程式）。
- **權變-權變**程式-用於新進員工、轉移及終止的詞彙，使用雲端 HR 應用程式作為記錄系統。 當服務成功將必要的屬性布建到目標系統時，此程式就會完成。

### <a name="key-benefits"></a>主要權益

這項 HR 驅動的 IT 布建功能提供顯著的商業優勢，如下所示：

- **提高生產力**-您現在可以自動指派使用者帳戶、Office365 授權，並提供金鑰群組的存取權。 自動指派可讓新進員工立即存取其作業工具並提高生產力。
- **管理風險**-您可以使用從雲端 HR 應用程式流入的資料，根據員工狀態或群組成員資格來自動化變更，以提高安全性。 自動化變更可確保使用者身分識別和金鑰應用程式的存取權會在使用者轉換或離開組織時自動更新。
- **位址合規性和治理**-Azure AD 支援來源和目標系統的應用程式所執行的使用者布建要求的原生審核記錄。 「審核」可讓您從單一畫面追蹤可存取應用程式的人員。
- **管理成本**-自動布建可避免與手動布建相關聯的效率不佳和人為錯誤，以降低成本。 藉由使用舊版和過時的平臺，可以減少隨著時間建立的自訂開發使用者布建解決方案的需求。

### <a name="licensing"></a>授權

若要將雲端 HR 應用程式設定為 Azure AD 使用者布建整合，您需要有效的[Azure AD Premium 授權](https://azure.microsoft.com/pricing/details/active-directory/)和雲端 HR 應用程式的授權，例如 Workday 或 SuccessFactors。

對於將來自雲端 HR 應用程式並布建到 AD 或 Azure AD 的每個使用者，您也需要有效的 Azure AD Premium P1 或更高的訂用帳戶授權。 在雲端 HR 應用程式中所擁有的任何不正確授權數目，可能會在使用者布建期間導致錯誤。

### <a name="prerequisites"></a>必要條件

- Azure AD 全域管理員存取權來設定 Azure AD Connect 布建代理程式。
- 雲端 HR 應用程式的測試和實際執行實例。
- 雲端 HR 應用程式中的系統管理員許可權，用來建立系統整合使用者並進行變更以測試員工資料，以供測試之用。
- 若要將使用者布建至 AD，必須要有執行 Windows Server 2012 或更新版本的伺服器，才能裝載[Azure AD Connect 布建代理程式](https://go.microsoft.com/fwlink/?linkid=847801)。
- [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-azure-ad-connect)在 AD 和 Azure AD 之間同步處理使用者。

### <a name="training-resources"></a>訓練資源

| **資源** | **連結和描述** |
|:-|:-|
| 影片 | [Azure Active Directory 中的使用者布建是什麼？](https://youtu.be/_ZjARPpI6NI) |
| | [如何在 Active Directory 中部署使用者布建？](https://youtu.be/pKzyts6kfrw) |
| 教學課程 | 請參閱[教學課程清單，以瞭解如何整合 SaaS 應用程式與 Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) |
| | [教學課程：設定 Workday 來自動布建使用者](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#frequently-asked-questions-faq) |
| 常見問題集 | [自動化使用者布建](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#frequently-asked-questions) |
| | [從 Workday 布建至 Azure AD](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#frequently-asked-questions-faq) |

### <a name="solution-architecture"></a>方案架構

下列範例說明適用于一般混合式環境的端對端使用者布建解決方案架構，包括：

- **授權 HR 資料流程–從雲端 HR 應用程式到 AD** -在此流程中，會在雲端 hr 應用程式租使用者中起始 HR 事件（權變-權變進程）。 Azure AD 的布建服務和 Azure AD Connect 布建代理程式會將使用者資料從雲端 HR 應用程式租使用者布建到 AD。 視事件而定，它可能會在 AD 中產生建立/更新/啟用/停用作業。
- **從內部部署 AD 同步處理 Azure AD 和回寫電子郵件和使用者名稱至雲端 HR 應用程式**-一旦帳戶在 AD 中更新，就會透過 Azure AD Connect 與 Azure AD 同步，而電子郵件地址和使用者名稱屬性則可回寫到雲端 hr 應用程式租使用者。

![Workflow diagram](./media/plan-cloudhr-provisioning/plan-cloudhr-provisioning-img1.png)

#### <a name="description-of-workflow"></a>工作流程的描述

圖表中指出的主要步驟如下：  

1. **Hr 小組**會在雲端 hr 應用程式租使用者中執行交易。
2. **Azure AD**布建服務會從雲端 HR 應用程式租使用者執行排程週期，並識別需要處理以與 AD 同步的變更。
3. **Azure AD**布建服務會使用包含 AD 帳戶建立/更新/啟用/停用作業的要求承載，叫用 Azure AD Connect 布建代理程式。
4. **Azure AD Connect 布建代理程式**會使用服務帳戶來管理 AD 帳戶資料。
5. **Azure AD Connect**在 AD 中執行差異[同步](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis)處理以提取更新。
6. **AD**更新會與 Azure AD 同步處理。
7. **Azure AD**布建服務回寫電子郵件屬性，以及從 Azure AD 到雲端 HR 應用程式租使用者的使用者名稱。

## <a name="plan-the-deployment-project"></a>規劃部署專案

當您在環境中判斷此部署的策略時，請考慮您的組織需求。

### <a name="engage-the-right-stakeholders"></a>參與適當的專案關係人

當技術專案失敗時，通常是因為對影響、結果和責任的預期不相符所造成。 若要避免這些錯誤，請[確定您參與的是正確的專案關係人](https://aka.ms/deploymentplans)，而且可以記錄專案關係人和其專案輸入和標準責任，以充分瞭解專案中的專案關係人角色。

您必須包含 HR 組織的代表，他可以提供現有 HR 商務程式的輸入，以及背景工作角色身分識別 + 作業資料處理需求。

### <a name="plan-communications"></a>規劃通訊

通訊對於任何新服務的成功非常重要。 您應該主動與使用者溝通他們的經驗如何改變、何時會變更，以及如何在遇到問題時取得支援。

### <a name="plan-a-pilot"></a>規劃試驗

將 HR business process 和身分識別工作流程從雲端 HR 應用程式整合到目標系統需要進行大量的資料驗證、資料轉換、資料清理，以及端對端測試，再將解決方案部署到生產環境。

建議您先在[試驗環境](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans#best-practices-for-a-pilot)中執行初始設定，然後再將其調整到生產環境中的所有使用者。

## <a name="select-cloud-hr-provisioning-connector-apps"></a>選取雲端 HR 布建連接器應用程式

為了協助 Azure AD 在雲端 HR 應用程式和 AD 之間布建工作流程，您可以從 Azure AD 應用程式庫新增多個布建連接器應用程式：

- **雲端 hr 應用程式至 AD 使用者**布建–此布建連接器應用程式可協助將使用者帳戶從雲端 HR 應用程式布建至單一 AD 網域。 如果您有多個網域，您可以從 Azure AD 應用程式資源庫，為您需要布建的每個 AD 網域新增一個此應用程式的實例。
- **雲端 hr 應用程式可 Azure AD 使用者**布建-雖然 Azure AD Connect 是應該用來將 AD 使用者同步至 Azure AD 的工具，但此布建連接器應用程式可用來協助將雲端 HR 應用程式的僅限雲端使用者布建至單一 Azure AD 租使用者。
- **雲端 hr 應用程式回寫**-此布建連接器應用程式可協助將使用者的電子郵件地址回寫，從 Azure AD 到雲端 HR 應用程式。

例如，下列影像列出 Azure AD 應用程式庫中可用的 Workday 連接器應用程式。

![Azure Active Directory 入口網站應用程式庫](./media/plan-cloudhr-provisioning/plan-cloudhr-provisioning-img2.png)

### <a name="decision-flowchart"></a>決策流程圖

使用下面的決策流程圖表來識別哪些雲端 HR 布建應用程式與您的案例相關。

![決策流程圖](./media/plan-cloudhr-provisioning/plan-cloudhr-provisioning-img3.png)

## <a name="design-azure-ad-connect-provisioning-agent-deployment-topology"></a>設計 Azure AD Connect 布建代理程式部署拓撲

雲端 HR 應用程式與 AD 之間的布建整合需要下列四個元件：

- 雲端 HR 應用程式租使用者
- 布建連接器應用程式
- Azure AD Connect 布建代理程式
- AD 網域

Azure AD Connect 布建代理程式部署拓撲取決於您打算整合的雲端 HR 應用程式租使用者和 AD 子域數目。 如果您有多個 AD 網域，則取決於 AD 網域是[連續或脫離](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/disjoint-namespace)。

根據您的決定，選擇其中一個部署案例：

- 單一雲端 HR 應用程式租使用者-> 以受信任樹系中的單一或多個 AD 子域為目標
- 單一雲端 HR 應用程式租使用者-> 在脫離的 AD 樹系中以多個子域為目標

### <a name="single-cloud-hr-app-tenant---single-or-multiple-target-ad-child-domains-in-a-trusted-forest"></a>單一雲端 HR 應用程式租使用者-> 受信任樹系中的單一或多個目標 AD 子域

我們建議您執行下列生產環境設定：

|需求|建議|
|:-|:-|
|要部署的 Azure AD Connect 布建代理程式數目|2（適用于高可用性和故障切換）
|要設定的布建連接器應用程式數目|每個子網域一個應用程式|
|Azure AD Connect 布建代理程式的伺服器主機|Windows 2012 R2 +，具備地理位置的 AD 網域控制站的視線</br>可以與 Azure AD Connect 服務並存|

![流向內部部署代理程式](./media/plan-cloudhr-provisioning/plan-cloudhr-provisioning-img4.png)

### <a name="single-cloud-hr-app-tenant---target-multiple-child-domains-in-a-disjoint-ad-forest"></a>單一雲端 HR 應用程式租使用者-> 在脫離的 AD 樹系中以多個子域為目標

此案例牽涉到將使用者從雲端 HR 應用程式布建到脫離的 AD 樹系中的網域。

我們建議您執行下列生產環境設定：

|需求|建議|
|:-|:-|
|要部署在內部部署的 Azure AD Connect 布建代理程式數目|2每個脫離的 AD 樹系|
|要設定的布建連接器應用程式數目|每個子網域一個應用程式|
|Azure AD Connect 布建代理程式的伺服器主機|Windows 2012 R2 +，具備地理位置的 AD 網域控制站的視線</br>可以與 Azure AD Connect 服務並存|

![單一雲端 HR 應用程式租使用者脫離 AD 樹系](./media/plan-cloudhr-provisioning/plan-cloudhr-provisioning-img5.png)

### <a name="azure-ad-connect-provisioning-agent-requirements"></a>Azure AD Connect 布建代理程式需求

雲端 HR 應用程式至 AD 使用者布建解決方案需要在執行 Windows 2012 R2 或更新版本的伺服器上部署一或多個 Azure AD Connect 布建代理程式，且至少要有 4 GB RAM 和 .NET 4.7.1 + 執行時間。 確定主機伺服器具有目標 AD 網域的網路存取權。

為了準備內部內部部署環境，Azure AD Connect 布建代理程式設定 wizard 會向您的 Azure AD 租使用者註冊代理程式、[開啟埠](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#open-ports)、[允許存取 url](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#allow-access-to-urls)，並支援[輸出 HTTPS proxy](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication)設定。

布建代理程式會使用服務帳戶來與 AD 網域進行通訊。 在安裝代理程式之前，建議您在 AD 使用者和電腦中建立符合下列需求的服務帳戶：

- 未過期的密碼
- 委派控制許可權以讀取、建立、刪除及管理使用者帳戶

您可以選取應處理布建要求的網域控制站。 如果您有數個地理位置分散的網域控制站，請在與您慣用的網域控制站相同的網站中安裝布建代理程式，以改善端對端解決方案的可靠性和效能。

為了達到高可用性，您可以部署多個 Azure AD Connect 布建代理程式並加以註冊，以處理同一組內部部署 AD 網域。

## <a name="plan-scoping-filters-and-attribute-mapping"></a>規劃範圍篩選器和屬性對應

當您啟用從雲端 HR 應用程式布建至 AD 或 Azure AD 時，Azure 入口網站會透過屬性對應控制屬性值。

### <a name="define-scoping-filters"></a>定義範圍篩選器

使用[範圍篩選器](https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters)來定義以屬性為基礎的規則，以決定哪些使用者應該從雲端 HR 應用程式布建到 AD 或 Azure AD。

當您起始權變流程時，請收集下列需求：

- 雲端 HR 應用程式是用來將員工和臨時背景工作用在面板上嗎？
- 您是否計畫使用雲端 HR 應用程式來 Azure AD 使用者布建，以同時管理員工和臨時員工？
- 您是否計畫推出雲端 HR 應用程式，只針對雲端 HR 應用程式使用者的子集 Azure AD 使用者布建（例如，員工）？

視您的需求而定，當您設定屬性對應時，可以設定 [**來源物件範圍**] 欄位，以選取要將雲端 HR 應用程式中的哪些使用者集合布建到 AD 的範圍。 請參閱雲端 HR 應用程式教學課程，以取得常用的範圍篩選器。

### <a name="determine-matching-attributes"></a>判斷相符的屬性

有了布建，您就能夠比對來源與目標系統之間的現有帳戶。 當您整合雲端 HR 應用程式與 Azure AD 布建服務時，您可以[設定屬性對應](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-automatic-user-provisioning-portal#mappings)，以決定哪些使用者資料應該從雲端 HR 應用程式流向 AD 或 Azure AD。

當您起始權變流程時，請收集下列需求：

- 此雲端 HR 應用程式中用來識別每個使用者的唯一識別碼為何？
- 從身分識別生命週期的觀點來看，您要如何處理 rehires？ Rehires 是否保留舊的員工識別碼？
- 您是否要事先處理未來的員工和建立的 AD 帳戶？
- 從身分識別生命週期的觀點來看，您要如何處理員工來進行背景工作的轉換，否則呢？
- 轉換後的使用者是否保留舊的 AD 帳戶，或是否取得新的？

視您的需求而定，Azure AD 支援直接屬性與屬性的對應、提供常數值，或[撰寫屬性對應的運算式](https://docs.microsoft.com/azure/active-directory/active-directory-saas-writing-expressions-for-attribute-mappings)。 這項彈性可讓您對將在目標應用程式屬性中填入的內容進行最終控制。 您可以使用[MICROSOFT GRAPH API](https://docs.microsoft.com/azure/active-directory/manage-apps/export-import-provisioning-configuration)和 Graph Explorer，將您的使用者布建屬性對應和架構匯出至 JSON 檔案，並將其匯入回 Azure AD。

**根據預設**，雲端 HR 應用程式中代表唯一員工識別碼的屬性會用來做為對應*至 AD 中唯一屬性*的比對屬性。 例如，在 Workday 應用程式案例中， *workday* *WorkerID*屬性會對應到 AD 「*員工 id* 」屬性。

您可以設定多個相符的屬性，並指派相符的優先順序。 它們會根據比對優先順序進行評估。 只要找到相符項目，便不會評估進一步比對屬性。

您也可以[自訂預設的屬性](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes#understanding-attribute-mapping-types)對應，例如變更或刪除現有的屬性對應，或根據您的業務需求建立新的屬性對應。 如需要對應的自訂屬性清單，請參閱雲端 HR 應用程式教學課程（例如[Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)）。

### <a name="determine-user-account-status"></a>判斷使用者帳戶狀態

根據預設，布建連接器應用程式會將**HR 使用者設定檔狀態**對應到 AD/Azure AD 中的**使用者帳戶狀態**，以決定是否要啟用或停用使用者帳戶。

當您起始權變/權變進程時，請收集下列需求：

| 流程 | 要求 |
| - | - |
| **權變** | 從身分識別生命週期的觀點來看，您要如何處理 rehires？ Rehires 是否保留舊的員工識別碼？ |
| | 您是否處理未來的員工，並事先為他們建立 AD 帳戶？ 這些帳戶是以啟用/停用狀態建立的嗎？ |
| | 從身分識別生命週期的觀點來看，您要如何處理員工來進行背景工作的轉換，否則呢？ |
| | 轉換後的使用者是否保留舊的 AD 帳戶，或是否取得新的？ |
| **權變** | 終止是否會以不同的方式處理 AD 中的員工和臨時員工？ |
| | 處理使用者終止時，會考慮哪些有效日期？ |
| | 員工和臨時的背景工作轉換如何影響現有的 AD 帳戶？ |
| | 如何處理 AD 中的 "Rescind" 作業？ 如果未來雇用的員工在 AD 中建立為加入程式的一部分，則需要處理 Rescind 作業。 |

根據您的需求，您可以使用[Azure AD 運算式](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data)來自訂對應邏輯，以便根據資料點的組合來啟用或停用 AD 帳戶。

### <a name="map-cloud-hr-app-to-ad-user-attributes"></a>將雲端 HR 應用程式對應至 AD 使用者屬性

每個雲端 HR 應用程式都會隨附預設的雲端 HR 應用程式來進行 AD 對應。

當您起始權變/移動器/權變進程時，請收集下列需求：

| 流程 | 要求 |
| - | - |
| **權變** | AD 帳戶建立程式是手動、自動或部分自動化？ |
| | 您打算將自訂屬性從雲端 HR 應用程式傳播到 AD 嗎？ |
| **知名** | 每當雲端 HR 應用程式中發生「移動」作業時，您要處理哪些屬性？ |
| | 您是否在使用者更新時執行任何特定的屬性驗證？ 如果是，請提供詳細資料。 |
| **權變** | 終止是否會以不同的方式處理 AD 中的員工和臨時員工？ |
| | 處理使用者終止時，會考慮哪些有效日期？ |
| | 員工和臨時的背景工作轉換如何影響現有的 AD 帳戶？ |

視您的需求而定，您可以修改對應以符合您的整合目標。 如需要對應的自訂屬性清單，請參閱特定的雲端 HR 應用程式教學課程（例如[Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)）。

### <a name="generate-unique-attribute-value"></a>產生唯一的屬性值

當您起始權變程式時，您可能需要在設定具有唯一條件約束的 CN、samAccountName 和 UPN 等屬性時，產生唯一的屬性值。

Azure AD 函數[SelectUniqueValues](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data#selectuniquevalue)會評估每個規則，然後檢查在目標系統中為唯一性而產生的值。 請參閱[產生 userPrincipalName （UPN）屬性的唯一值](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data#generate-unique-value-for-userprincipalname-upn-attribute)範例。

> [!NOTE]
> 此函式目前僅支援「Workday 到 Active Directory 使用者布建」。 它不能與其他布建應用程式搭配使用。

### <a name="configure-ad-ou-container-assignment"></a>設定 AD OU 容器指派

根據業務單位、位置和部門，將 AD 使用者帳戶放置到容器中是常見的需求。 當您起始移動程式進程，且如果有監督的組織變更時，您可能需要在 AD 中將使用者移至另一個 OU。

使用[Switch （）](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data#switch)函數來設定 OU 指派的商務邏輯，並將其對應至 AD 屬性*parentDistinguishedName*。

例如，如果您想要根據 HR 屬性 "Municipality" 在 OU 中建立使用者，您可以使用下列運算式。

`
Switch([Municipality], "OU=Default,OU=Users,DC=contoso,DC=com", "Dallas", "OU=Dallas,OU=Users,DC=contoso,DC=com", "Austin", "OU=Austin,OU=Users,DC=contoso,DC=com", "Seattle", "OU=Seattle,OU=Users,DC=contoso,DC=com", "London", "OU=London,OU=Users,DC=contoso,DC=com")
`

使用此運算式時，如果 Municipality 值為達拉斯、奧斯丁、西雅圖或倫敦，則會在對應的 OU 中建立使用者帳戶。 如果沒有相符的值，則會在預設 OU 中建立帳戶。

## <a name="plan-for-password-delivery-of-new-user-accounts"></a>新使用者帳戶的密碼傳遞規劃

當您起始權變程式時，您將需要設定並傳遞新使用者帳戶的暫時密碼。 使用雲端 HR Azure AD 使用者布建，您可以在第1天推出使用者 Azure AD 的[自助式密碼重設](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)（SSPR）功能。

SSPR 是一種簡單的方法，可讓 IT 系統管理員用來重設其密碼或解除鎖定其帳戶。 您可以從雲端 HR 應用程式將 [**行動電話號碼**] 屬性布建到 AD，並將其與 Azure AD 同步。 一旦 [**行動電話號碼**] 屬性在 Azure AD 中，您就可以啟用使用者帳戶的 SSPR，如此一來，在第1天，新的使用者就可以使用已註冊和已驗證的行動電話號碼進行驗證。

## <a name="plan-for-initial-cycle"></a>規劃初始週期

當 Azure AD 布建服務第一次執行時，它會對雲端 HR 應用程式執行[初始迴圈](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#what-happens-during-provisioning)，以建立雲端 hr 應用程式中所有使用者物件的快照集。 初始週期所花費的時間，會直接取決於來源系統中有多少使用者。 有些雲端 HR 應用程式租使用者的初始週期若有超過100000個使用者，可能需要很長的時間。

**針對大型雲端 HR 應用程式租使用者（> 30000 使用者），建議**您在漸進階段執行初始迴圈，並只在驗證 AD 中針對不同使用者布建案例設定正確的屬性之後，才啟動累加式更新。 遵循下列順序：

1. 藉由設定[範圍篩選器](#plan-scoping-filters-and-attribute-mapping)，只針對一組有限的使用者執行初始迴圈。
2. 確認 AD 帳戶的布建和為第一次執行選取的使用者設定的屬性值。 如果結果符合您的預期，請展開 [範圍] 篩選器，以漸進方式包含更多使用者，並確認第二次執行的結果。

一旦您滿意測試使用者的初始週期結果之後，就可以啟動累加[式更新](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#incremental-cycles)。

## <a name="plan-testing-and-security"></a>規劃測試和安全性

在您部署的每個階段，透過啟用使用者布建來進行初始試驗，請確定您正在測試結果是否如預期般進行，以及審核布建週期。

### <a name="plan-testing"></a>規劃測試

設定雲端 HR 應用程式以 Azure AD 使用者布建之後，您必須執行測試案例，以確認此解決方案是否符合您組織的需求。

|案例|預期的結果|
|:-|:-|
|雲端 HR 應用程式中的新員工雇用| -使用者帳戶會布建在 AD 中。</br>-使用者可以登入 AD 網域應用程式，並執行所需的動作。</br>-如果已設定 AAD Connect 同步處理，則也會在 Azure AD 中建立使用者帳戶。
|使用者已在雲端 HR 應用程式中終止|-AD 中的使用者帳戶已停用。</br>-使用者無法登入任何受 AD 保護的企業應用程式。
|雲端 HR 應用程式中的使用者監督組織已更新|根據屬性對應，使用者帳戶會在 AD 中從一個 OU 移到另一個。|
|HR 更新雲端 HR 應用程式中的使用者經理|AD 中的 [管理員] 欄位會更新，以反映新管理員的名稱。|
|HR rehires 員工成為新的角色。|行為取決於雲端 HR 應用程式設定來產生員工識別碼的方式：</br>-如果舊員工識別碼重複用於 rehires，則連接器會為使用者啟用現有的 AD 帳戶。</br>-如果 rehires 取得新的員工識別碼，連接器將會為使用者建立新的 AD 帳戶。|
|HR 將員工轉換成合約工作者，反之亦然|新的角色會建立新的 AD 帳戶，而舊的帳戶會在轉換生效日期停用。|

使用上述結果來判斷如何根據您所建立的時程表，將自動使用者布建實施轉換成生產環境。

> [!TIP]
> 我們建議您在以生產資料重新整理測試環境時，使用資料縮減和資料清除等技術，來移除/遮罩敏感性 PII （個人識別資訊）資料，以符合隱私權和安全性標準。

### <a name="plan-security"></a>規劃安全性

在部署新服務時，需要進行安全性審查，這是很常見的情況。 如果需要安全性審查或尚未進行，請參閱許多 Azure AD[白皮書](https://www.microsoft.com/download/details.aspx?id=36391)，其中提供身分識別即服務的總覽。

### <a name="plan-rollback"></a>計畫復原

如果雲端 HR 使用者布建實現在生產環境中無法如預期般運作，下列的復原步驟可協助您還原至先前已知良好的狀態：

1. 檢查布建[摘要報告](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting#getting-provisioning-reports-from-the-azure-management-portal)和布建[記錄](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting#provisioning-audit-logs)（請參閱[管理雲端 HR 應用程式使用者](#manage-your-configuration)布建），以判斷哪些不正確的作業在受影響的使用者和/或群組上執行。
2. 受影響的使用者和/或群組的最後已知良好狀態可以透過布建 audit 記錄，或藉由檢查目標系統（Azure AD 或 AD）來決定。
3. 與應用程式擁有者合作，使用最後一個已知的正確狀態值來更新直接在應用程式中影響的使用者和/或群組。

## <a name="deploy-the-cloud-hr-app"></a>部署雲端 HR 應用程式

請選擇符合您解決方案需求的雲端 HR 應用程式。

**Workday** -請參閱[教學課程：設定 workday 來自動](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#planning-your-deployment)布建使用者，以執行將背景工作設定檔從 WORKDAY 匯入到 AD 和 Azure AD 的步驟，並將電子郵件地址和使用者名稱的選擇性回寫至 Workday。

## <a name="manage-your-configuration"></a>管理您的設定

Azure AD 可以透過 audit 記錄和報告，為您組織的使用者布建使用量和操作健全狀況提供額外的深入解析。

### <a name="gain-insights-from-reports-and-logs"></a>從報表和記錄中取得見解

在成功的[初始迴圈](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#what-happens-during-provisioning)之後，Azure AD 布建服務會以無限期的間隔，以每個應用程式特定的教學課程中所定義的間隔持續執行，直到發生下列其中一個事件為止：

- 此服務會以手動方式停止，並使用[Azure 入口網站](https://portal.azure.com/)或使用適當的[Microsoft Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)命令來觸發新的初始迴圈。
- 因為屬性對應或範圍篩選器的變更，而觸發了新的初始迴圈。
- 布建程式會因為高錯誤率而進入隔離狀態，並會持續隔離超過四周，這會自動停用。

若要檢查這些事件以及布建服務所執行的所有其他活動，請[瞭解如何檢查記錄並取得](https://docs.microsoft.com/azure/active-directory/manage-apps/check-status-user-account-provisioning)布建活動的報告。

#### <a name="azure-monitor-logs"></a>Azure 監視器記錄

布建服務所執行的所有活動都會記錄在**Azure AD audit 記錄**檔中。 您可以將 Azure AD 審核記錄路由至 Azure 監視器記錄，以進行進一步的分析。 **Azure 監視器記錄（也稱為 Log Analytics 工作區）** 可讓您查詢資料，以尋找事件、分析趨勢，以及執行各種資料來源之間的相互關聯。 觀看這段[影片](https://youtu.be/MP5IaCTwkQg)，以瞭解在實際使用者案例中，使用 Azure 監視器記錄來 Azure AD 記錄的優點。

您可以安裝[Azure AD 活動記錄的 Log analytics 查看](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-install-use-log-analytics-views)，以存取[預先建立的報表](https://github.com/AzureAD/Deployment-Plans/tree/master/Log%20Analytics%20Views)，以在您的環境中布建事件。

如需詳細資訊，請參閱[如何使用您的 Azure 監視器記錄來分析 Azure AD 活動記錄](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics)

### <a name="manage-personal-data"></a>管理個人資料

安裝在 Windows server 上的 Azure AD Connect 布建代理程式會在 Windows 事件記錄檔中建立記錄，其中可能包含個人資料（視您的雲端 HR 應用程式與 AD 屬性對應而定）。 為符合使用者隱私權義務，您可以設定 Windows 排程工作來清除事件記錄檔，並確保不會保留超過48小時的資料。

Azure AD 布建服務不會產生報告、執行分析，或提供超過30天的深入解析。 因此，Azure AD 佈建服務不會儲存、處理或保留任何資料達 30 天以上。 

### <a name="troubleshoot"></a>疑難排解

請參閱下列連結，以針對可能在布建期間開啟的任何問題進行疑難排解：

- [設定 Azure AD 資源庫應用程式的使用者布建時發生問題](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem)
- [將內部部署 Active Directory 的屬性同步處理至 Azure AD 以布建至應用程式](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning-sync-attributes-for-mapping)
- [Azure AD 資源庫應用程式的使用者布建需要數小時或更長的時間](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish)
- [在設定 Azure Active Directory 資源庫應用程式之使用者佈建的情況下儲存系統管理員認證時發生問題](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem-storage-limit)
- [未將任何使用者布建到 Azure AD 資源庫應用程式](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem-no-users-provisioned)
- [將一組錯誤的使用者布建到 Azure AD 資源庫應用程式](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem-wrong-users-provisioned)
- [設定 Windows 事件檢視器以進行代理程式疑難排解](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#setting-up-windows-event-viewer-for-agent-troubleshooting)
- [設定 Azure 入口網站稽核記錄以進行服務疑難排解](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
- [了解 AD 使用者帳戶建立作業的記錄](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#understanding-logs-for-ad-user-account-create-operations)
- [了解管理員更新作業的記錄](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#understanding-logs-for-manager-update-operations)
- [解決常見的錯誤](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#resolving-commonly-encountered-errors)

### <a name="next-steps"></a>後續步驟

- [撰寫屬性對應的運算式](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data)
- [Azure AD 同步處理 API 概觀](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
- [略過刪除超出範圍的使用者帳戶](https://docs.microsoft.com/azure/active-directory/manage-apps/skip-out-of-scope-deletions)
- [Azure AD Connect 布建代理程式：版本發行歷程記錄](https://docs.microsoft.com/azure/active-directory/manage-apps/provisioning-agent-release-version-history)
