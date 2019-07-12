---
title: 在 Microsoft Azure 上設計安全的應用程式
description: 這篇文章會討論最佳作法，在您的 web 應用程式專案的需求和設計階段決定。
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/11/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 12b9793cabb261368c437bd2ae2dbb39cf078bef
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2019
ms.locfileid: "67653289"
---
# <a name="design-secure-applications-on-azure"></a>在 Azure 上設計安全的應用程式
在本文中我們將會呈現安全性活動和設計的雲端應用程式時要考量的控制項。 訓練資源，以及安全性問題和概念，以納入考量的需求和設計階段的 Microsoft[安全性開發生命週期 (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx)涵蓋。 目標是為了協助您定義活動和 Azure 服務可供您設計更安全的應用程式。

此文章涵蓋下列 SDL 階段：

- 訓練
- 需求
- 設計

## <a name="training"></a>訓練
在開始開發您的雲端應用程式之前，需要了解安全性和隱私權，在 Azure 上的時間。 藉由採取此步驟中，您可以減少應用程式中的次數和嚴重性可利用來攻擊的弱點。 您將會更完善地準備要對瞬息萬變的威脅型態做出適當回應。

使用在訓練階段期間的下列資源，以熟悉開發人員可使用的 Azure 服務與 Azure 上的安全性最佳作法：

  - [Azure 開發人員指南](https://azure.microsoft.com/campaigns/developer-guide/)示範您如何開始使用 Azure。 本指南會顯示可用來執行您的應用程式，請儲存您的資料、 納入智慧、 建置 IoT 應用程式，更有效率且更安全的方式，將您的解決方案部署的服務。

  - [Azure 開發人員的快速入門的指南](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide)提供開發人員想要開始使用 Azure 平台，以符合其開發需求的重要資訊。

  - [Sdk 和工具](https://docs.microsoft.com/azure/index#pivot=sdkstools)說明 Azure 提供的工具。

  - [Azure 的 DevOps 服務](https://docs.microsoft.com/azure/devops/)提供開發共同作業工具。 這些工具包括高效能管線、 免費的 Git 儲存機制、 可設定的工作流程看板和廣泛的自動化和雲端式負載測試。
    [DevOps 資源中心](https://docs.microsoft.com/azure/devops/learn/)結合了我們的資源，以了解 DevOps 做法，Git 版本控制、 敏捷式軟體開發方法，如何使用 microsoft 的 DevOps 和可以評估您自己的 DevOps 進展的方式。

  - [前 5 個安全性項目，若要推送到生產環境之前，請考慮](https://docs.microsoft.com/learn/modules/top-5-security-items-to-consider/index?WT.mc_id=Learn-Blog-tajanca)示範您如何協助保護您在 Azure 上的 web 應用程式，並保護您的應用程式，針對最常見且最危險的 web 應用程式攻擊。

  - [Secure DevOps Kit for Azure](https://azsk.azurewebsites.net/index.html)是為完整 Azure 訂用帳戶和資源的安全性需求使用廣泛的自動化的 DevOps 小組所提供的指令碼、 工具、 擴充功能，以及自動化的集合。 Secure DevOps Kit for Azure 可以為您示範如何順暢地將安全性整合至原生的 DevOps 工作流程。 套件處理工具，例如安全性驗證測試 (SVTs)，可協助開發人員撰寫安全程式碼和程式碼撰寫和早期開發階段中測試雲端應用程式安全的設定。

  - [Azure 解決方案的安全性最佳作法](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions)提供的安全性集合使用，因為您設計、 部署及管理雲端解決方案，藉由使用 Azure 的最佳作法。

## <a name="requirements"></a>需求
需求定義階段是很重要的步驟，在您的應用程式是什麼用途以及它將會作用在其發行定義。 在需求階段也是思考您將建置到您的應用程式的安全性控制的時間。 在這個階段，您也會開始將整個 SDL 以確保您發行和部署安全的應用程式採取的步驟。

### <a name="consider-security-and-privacy-issues"></a>請考慮安全性與隱私權的問題
此階段，請考慮基本的安全性和隱私權問題的最佳時機。 定義可接受的層級的安全性和隱私權，在專案開始，可協助小組：

- 了解相關聯的安全性問題的風險。
- 識別並修正在開發期間的安全性錯誤。
- 適用於已建立的層級的安全性和隱私權，在整個專案。

當您撰寫您的應用程式的需求時，請務必考慮安全性控制，可協助保護您的應用程式和資料的安全。

### <a name="ask-security-questions"></a>詢問安全性問題
詢問安全性問題，例如：

  - 我的應用程式是否包含敏感性資料？

  - 不會我的應用程式收集或儲存資料，需要符合業界標準和合規性計劃，等我[聯邦金融機構檢查委員會 (符合 FFIEC)](https://docs.microsoft.com/azure/security/blueprints/ffiec-analytics-overview)或[支付卡產業資料安全性標準 (PCI DSS)](https://docs.microsoft.com/azure/security/blueprints/pcidss-analytics-overview)嗎？

  - 沒有我的應用程式收集或含有機密個人或客戶資料可用，單獨或與其他資訊，來識別，請連絡，或找出單一的人員嗎？

  - 我的應用程式不會收集或包含可用來存取個人的醫療、 教育、 財務、 資料或雇用資訊嗎？ 在需求階段期間識別資料的機密性，可協助您為資料分類，並識別資料保護方式，您將使用您的應用程式。

  - 位置和方式是我的資料儲存？ 請考慮將如何監視儲存體服務應用程式所使用的任何非預期的變更 （例如回應時間更慢）。 您能夠影響來收集更多詳細的資料和分析深入了解問題的記錄？

  - 我的應用程式將會開放給大眾 （網際網路） 上或在內部嗎？ 如果您的應用程式開放給大眾時，要如何保護可能會從正在使用中錯誤的方式收集的資料？ 如果您的應用程式僅提供在內部，請考慮組織中的使用者應該可以存取應用程式和其個別應該擁有存取權的時間長度。

  - 當您開始設計您的應用程式之前，並了解您的身分識別模型嗎？ 如何將決定使用者是他們所聲稱的人和哪些使用者的權限？

  - 我的應用程式不會執行機密或重要的工作 （例如轉帳、 解除鎖定的門，或傳遞失誤）？
    請考慮如何將驗證執行敏感性工作的使用者已獲授權執行此工作，並將驗證的人員是他們所聲稱的人的方式。 授權 (AuthZ) 是授與已驗證的安全性主體的權限進行的動作。 驗證 （驗證） 是指具挑戰性的合法認證的合作對象。

  - 我的應用程式不會執行任何具風險的軟體活動，例如允許使用者上傳或下載檔案或其他資料？ 如果您的應用程式並執行有風險的活動，請考慮如何您的應用程式會保護使用者免於惡意檔案或資料處理。

### <a name="review-owasp-top-10"></a>檢閱 OWASP 前 10 個
請考慮檢閱[ <span class="underline">OWASP 前 10 應用程式的安全性風險</span>](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)。
OWASP 前 10 個位址的 web 應用程式的重要安全性風險。
知道這些安全風險可協助您做出這些應用程式中的風險降到最低的需求和設計決策。

思考安全性控制項，以防止外洩很重要。
不過，您也想要[假想缺口](https://docs.microsoft.com/azure/devops/learn/devops-at-microsoft/security-in-devops)就會發生。 假設缺口可協助回答一些有關安全性的重要問題事先，讓他們不必自己回答在發生緊急狀況：

  - 如何將會偵測攻擊？

  - 如果沒有入侵或破壞，我會做什麼？

  - 我如何從資料流失，或竄改攻擊復原？

## <a name="design"></a>設計

在設計階段是建立設計和功能規格的最佳作法的關鍵。 也務必執行風險分析，可協助降低安全性和隱私權的問題，整個專案。

當您備妥的安全性需求，並使用安全的設計概念時，您可以避免或減少安全性缺陷的機會。 安全性缺陷是在設計中的應用程式可能會允許使用者執行惡意或意外動作，您的應用程式發行之後的疏忽。

在設計階段中，同時也要考量如何套用安全性中的圖層。一個層級的防禦並不一定足夠。 如果攻擊者取得過去的 web 應用程式防火牆 (WAF) 發生什麼事？ 您想要就地保護，防止此攻擊的另一個安全性控制項。

這一點，我們將討論下列安全設計概念和設計安全的應用程式時，您應該處理的安全性控制：

- 使用安全的程式碼程式庫和軟體架構。
- 掃描易受攻擊的元件。
- 使用威脅分析模型應用程式的設計。
- 減少受攻擊面。
- 作為主要安全性周邊，採用身分識別的原則。
- 重要的交易，需要重新驗證。
- 您可以使用金鑰管理解決方案來保護金鑰、 認證和其他祕密。
- 保護機密資料。
- 實作保全的量值。
- 利用錯誤和例外狀況處理。
- 使用記錄和警示。

### <a name="use-a-secure-coding-library-and-a-software-framework"></a>使用安全的程式碼程式庫和軟體架構

程式開發中，使用安全的程式碼程式庫和包含內嵌安全性的軟體架構。 開發人員可以使用現有的、 證明 （加密、 輸入的清理、 輸出編碼方式、 金鑰或連接字串和其他項目就會被視為安全性控制項） 的功能，而不是開發全新的安全性控制。 這有助於防範與安全性相關的設計和實作問題。

請確定您使用您的架構和 framework 中可用的所有安全性功能的最新版本。 Microsoft 提供全方位[開發工具組](https://azure.microsoft.com/product-categories/developer-tools/)適用於所有開發人員，使用任何平台或語言，傳遞雲端應用程式。 您可以選擇從各種程式碼使用您選擇的語言[Sdk](https://azure.microsoft.com/downloads/)。
您可以利用功能完整的整合式的開發環境 (Ide) 和具有進階偵錯功能以及內建的 Azure 支援的編輯器。

Microsoft 提供多種[語言、 架構和工具](https://docs.microsoft.com/azure/index#pivot=sdkstools&panel=sdkstools-all)，您可以使用 Azure 上開發應用程式。 例如，[適用於.NET 和.NET Core 開發人員的 Azure](https://docs.microsoft.com/dotnet/azure/)。 針對每個語言和架構，我們提供了，您可以找到快速入門、 教學課程和 API 參考，可協助您快速開始。

Azure 提供各種服務來裝載網站和 web 應用程式，您可以使用。 這些服務可讓您開發您慣用的語言，無論是.NET、.NET Core、 Java、 Ruby、 Node.js、 PHP 或 Python。
[Azure App Service Web Apps](https://docs.microsoft.com/azure/app-service/app-service-web-overview) (Web Apps) 是其中一個服務。

Web 應用程式會將您的應用程式中的 Microsoft Azure 的強大功能。 它包括安全性、 負載平衡、 自動調整，以及自動化的管理。 您也可以利用的 DevOps 功能在 Web 應用程式，例如套件管理、 預備環境，自訂網域、 SSL/TLS 憑證，以及從 Azure DevOps、 GitHub、 Docker Hub 和其他來源進行持續部署。

Azure 提供您可用來裝載網站和 web 應用程式的其他服務。 大部分的情況下，Web Apps 是最佳選擇。 若是微服務架構，請考慮[Azure Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric)。
如果您需要能更加充分地掌控程式碼執行所在的 VM，則請考慮使用 [Azure 虛擬機器](https://azure.microsoft.com/documentation/services/virtual-machines/)。
如需如何選擇這些 Azure 服務的詳細資訊，請參閱[Azure App Service、 虛擬機器、 Service Fabric 和雲端服務的比較](https://docs.microsoft.com/azure/app-service/choose-web-site-cloud-service-vm)。

### <a name="apply-updates-to-components"></a>將更新套用至元件

若要防止弱點，您應該持續清查用戶端和伺服器端元件 （例如，架構和程式庫） 和其相依性更新。 新的弱點和更新的軟體版本會持續發行。 請確定您已設定進行中的方案，以便監視、 分級和套用更新，或組態變更為程式庫和您所使用的元件。

請參閱[Open Web Application Security Project (OWASP)](https://www.owasp.org/index.php/Main_Page)頁面上[元件使用已知的弱點](https://www.owasp.org/index.php/Top_10-2017_A9-Using_Components_with_Known_Vulnerabilities)工具建議。 您也可以訂閱您所使用的元件相關的安全性弱點的電子郵件警示。

### <a name="use-threat-modeling-during-application-design"></a>在應用程式設計期間使用威脅模型化

威脅分析模型是用來識別潛在的安全性威脅，您的商務和應用程式，以及確保適當的安全防護已就位的程序。 SDL 會指定小組應該與威脅分析模型在設計階段中，解決潛在問題時相當輕鬆且具成本效益。 使用威脅模型化，在設計階段可以大幅降低總開發成本。

為了協助推動威脅分析模型的程序，我們設計[SDL Threat Modeling Tool](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool)與非安全性專家，記住。 這項工具可威脅分析模型適用於所有的開發人員提供有關如何建立和分析威脅模型有清楚的指引。

模型化應用程式的設計和列舉[STRIDE](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy)威脅 — 詐騙、 竄改、 否認、 資訊洩漏、 阻絕服務和提高權限，跨所有信任界限已證明有效的方式若要早期攔截設計錯誤。 下表列出 STRIDE 威脅，並提供一些範例緩和措施，以使用 Azure 所提供的功能。 這些風險降低措施並非能在各個情況下運作。

| Threat | 安全性屬性 | Azure 平台可能的補救措施 |
| ---------------------- | --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 詐騙               | 驗證        | [需要 HTTPS 連線](https://docs.microsoft.com/aspnet/core/security/enforcing-ssl?view=aspnetcore-2.1&tabs=visual-studio)。 |
| 竄改              | 完整性             | 驗證 SSL/TLS 憑證。 使用 SSL/TLS 的應用程式必須完全驗證其連接到實體的 X.509 憑證。 使用 Azure Key Vault 憑證[管理您的 x509 憑證](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-certificates)。 |
| 否認性            | 不可否認性       | 啟用 Azure [監視和診斷](https://docs.microsoft.com/azure/architecture/best-practices/monitoring)。|
| 資訊洩漏 | 機密性       | 敏感性資料加密[待用](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)並[傳輸中](https://docs.microsoft.com/azure/security/azure-security-data-encryption-best-practices#protect-data-in-transit)。 |
| 阻斷服務      | 可用性          | 監視潛在的阻絕服務狀況的效能計量。 實作 IP 連線篩選。 [Azure DDoS 保護](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview#next-steps)，結合應用程式設計最佳做法，提供 DDoS 攻擊的防禦。|
| 提高權限 | Authorization         | 使用 Azure Active Directory <span class="underline"> </span> [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure)。|

### <a name="reduce-your-attack-surface"></a>減少受攻擊面

受攻擊面是總計的潛在的弱點可能會發生的總和。 在本文中，我們會著重於應用程式的受攻擊面。
重點在於保護攻擊應用程式。 若要從您的應用程式中移除未使用的資源和程式碼是簡單且快速的方式，將您的受攻擊面降至最低。 較小應用程式、 較小攻擊面。 比方說，移除：

- 您還沒有在尚未發行的功能的程式碼。
- 偵錯支援程式碼。
- 網路介面和通訊協定，不會使用或其已被取代。
- 虛擬機器和其他您未使用的資源。

執行您的資源的正規清除工作，並確定您移除未使用的程式碼會確定沒有惡意執行者攻擊的機會也更少的不錯方式。

若要完成的攻擊面分析是更詳細的深入的方式，可減少受攻擊面。 攻擊面分析可協助您將需要檢閱和測試有安全性弱點的系統組件的對應。

攻擊面分析的目的是了解應用程式中的風險區域，讓開發人員和安全性專家會留意應用程式的哪些部分會受到攻擊。 然後，您可以在何時及如何攻擊就會變更，以及這表示從風險觀點來看，找到方法可以減少此可能性、 播放軌。

攻擊面分析可協助您識別：

- 函式和系統的部分您需要檢閱和測試有安全性弱點。
- 高風險區域的程式碼需要深度防禦保護 （您要保護的系統組件）。
- 當您改變的受攻擊面，並需要重新整理的威脅評估。

降低攻擊者利用的潛在的弱式位置或的弱點可能會要求您徹底分析您的應用程式整體的攻擊面。 它也包含停用或限制對系統服務中套用最低權限和原則的存取，並盡可能採用分層式防禦措施。

我們會討論[進行攻擊面的檢閱](secure-develop.md#conduct-attack-surface-review)SDL 的驗證階段期間。

> [!NOTE]
> **威脅分析模型和攻擊面分析之間的差異為何？**
威脅分析模型是找出潛在的安全性威脅，您的應用程式，並確保適當的防護功能抵禦威脅就地程序。 攻擊面分析識別程式碼遭受攻擊的高風險的範圍。 它牽涉到在想辦法保護應用程式並檢閱和測試這些區域的程式碼，然後再部署應用程式的高風險範圍。

### <a name="adopt-a-policy-of-identity-as-the-primary-security-perimeter"></a>採用以身分識別作為主要安全性周邊的原則

當您設計雲端應用程式時，務必展開您的安全性周邊焦點從以網路為中心的方法來識別為中心的方法。 在過去，主要內部部署安全性周邊是組織的網路。 大部分的內部部署安全性設計皆使用網路做為主要安全性樞紐分析。 對於雲端應用程式，您較佳的服務身分識別作為主要安全性周邊視為。

可執行的工作來開發身分識別為中心的方法，來開發 web 應用程式：

- 強制執行多重要素驗證的使用者。
- 使用增強式驗證與授權平台。
- 套用最低權限原則。
- 實作中即時存取。

#### <a name="enforce-multi-factor-authentication-for-users"></a>強制執行 multi-factor authentication 的使用者

使用雙因素驗證。 雙因素驗證是驗證的驗證和授權的目前標準，因為它可避免使用者名稱和密碼的類型中固有的安全性弱點。 應該設計並設定為使用 Azure 的管理介面 （Azure 入口網站/遠端 PowerShell） 和客戶面向服務的存取權[Azure Multi-factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)。

#### <a name="use-strong-authentication-and-authorization-platforms"></a>使用強式驗證與授權平台

使用平台提供的驗證與授權機制，而不要使用自訂程式碼。 這是因為開發自訂驗證程式碼可能很容易發生錯誤。 商業的程式碼 （例如，來自 Microsoft) 通常會廣泛地檢閱安全性。 [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) 是用於管理身分識別和存取的 Azure 解決方案。 這些 Azure AD 的工具和服務協助與安全開發：

- [Azure AD 身分識別平台 (適用於開發人員的 Azure AD)](https://docs.microsoft.com/azure/active-directory/develop/about-microsoft-identity-platform)是一種雲端識別服務，開發人員用來建置安全地登入使用者的應用程式。 要建置單一租用戶的特定業務 (LOB) 應用程式開發人員和開發人員想要開發多租用戶應用程式，可協助 azure AD。 除了基本登入時，Microsoft Api 與 Azure AD 平台為基礎的自訂 Api，可以呼叫內建使用 Azure AD 應用程式。 Azure AD 身分識別平台支援業界標準通訊協定，例如 OAuth 2.0 和 OpenID Connect。

- [Azure Active Directory B2C (Azure AD B2C)](https://docs.microsoft.com/azure/active-directory-b2c/)自訂及控制如何客戶註冊、 登入，及管理其設定檔，在使用您的應用程式時，您可以使用身分識別管理服務。 這包括適用於 iOS、 Android 和.NET 開發以及其他應用程式。 Azure AD B2C 可讓這些動作，同時保護客戶身分識別。

#### <a name="apply-the-principle-of-least-privilege"></a>套用最低權限原則

概念[最低權限](https://en.wikipedia.org/wiki/Principle_of_least_privilege)表示讓使用者存取權和控制權完成其工作而不需要其他所需的精確層級。

軟體開發人員需要網域系統管理員權限嗎？ 行政助理需要存取其個人電腦上的系統管理控制項嗎？ 評估軟體的存取權並無不同。 如果您使用[角色型存取控制 (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview)到您的應用程式可讓使用者不同的功能和授權單位，您不會授與所有人存取權的所有項目。 藉由限制存取權有何需求為每個角色，您可以限制發生安全性問題的風險。

請確定您的應用程式會強制[最低權限](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models#in-applications)在其存取模式。

> [!NOTE]
>  最低權限的規則，就必須套用至軟體和建立軟體的人員。 軟體開發人員可能會有很大的風險，到 IT 安全性，如果它們有太多的存取。 如果開發人員有惡意企圖，或有太多的存取權，可能會嚴重後果。 我們建議的最低權限的規則會套用至整個開發生命週期的開發人員。

#### <a name="implement-just-in-time-access"></a>實作在 just-in-time 存取

實作 *-just-in-time* (JIT) 存取權，以進一步降低權限的曝光時間。 使用[Azure AD Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure#stage-3-build-visibility-and-take-full-control-of-admin-activity)來：

- 為使用者提供他們需要只 JIT 權限。
- 指派縮短持續時間的角色，而且有信心會自動撤銷權限。

### <a name="require-re-authentication-for-important-transactions"></a>需要重新驗證很重要的交易

[跨網站要求偽造](https://docs.microsoft.com/aspnet/core/security/anti-request-forgery?view=aspnetcore-2.1)(也稱為*XSRF*或是*CSRF*) 是惡意的 web 應用程式會影響用戶端瀏覽器與 web 之間的互動的 web 應用程式對抗攻擊信任該瀏覽器的應用程式。 跨網站偽造要求攻擊可能會因為網頁瀏覽器會將某些類型的驗證權杖會自動隨著每個要求傳送至網站。
這種形式的入侵，也就是*單鍵攻擊*或是*工作階段乘載*因為攻擊會利用使用者先前的驗證工作階段。

若要防止這類攻擊的最佳方式是要求使用者提供只有使用者可以提供每個重要的交易，例如購物、 帳戶停用或變更密碼之前的項目。 您可能會要求使用者重新輸入密碼，請完成文字驗證，或提交只，使用者需要祕密權杖。 最常見的方法是祕密的語彙基元。

### <a name="use-a-key-management-solution-to-secure-keys-credentials-and-other-secrets"></a>使用金鑰管理解決方案來保護金鑰、 認證和其他祕密

遺失金鑰和認證是相當常見的問題。 唯一比遺失金鑰和認證更糟的情況，就是讓未經授權者能夠存取這些機密資料。 攻擊者可以利用自動化和手動的技術，來尋找金鑰和祕密儲存在 GitHub 等的程式碼儲存機制中。 在這些公用程式碼存放庫或任何其他伺服器上不要放置金鑰和祕密。

一律將您的金鑰、 憑證、 密碼和連接字串放在金鑰管理解決方案。 您可以使用集中式的解決方案中金鑰和祕密儲存在硬體安全性模組 (Hsm)。 Azure 會將您提供在雲端中使用 HSM [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)。

Key vault*密碼存放區*： 它是集中式的雲端服務來儲存應用程式祕密。 Key Vault 會保留您的機密資料安全藉由將應用程式密碼保留在單一集中位置，並提供安全存取、 權限控制和存取記錄。

密碼會儲存在個別*保存庫*。 每個保存庫都有自己的組態和安全性原則來控制存取。 您可以透過 REST API 或 SDK，但適用於大部分的程式設計語言的用戶端資料。

> [!IMPORTANT]
> Azure 金鑰保存庫被設計來儲存伺服器應用程式的設定祕密。 它並不適用於儲存應用程式使用者所屬的資料。 這會反映在其效能特性、 API 和成本模型。
>
> 使用者資料應該儲存在其他地方，像是 Azure SQL Database 執行個體中具有透明資料加密 (TDE)，或儲存體帳戶中使用 Azure 儲存體服務加密。 您的應用程式用來存取這些資料存放區的機密資料可以保留在 Azure 金鑰保存庫中。

### <a name="protect-sensitive-data"></a>保護機密資料

保護資料是您的安全性策略中不可或缺的一部分。
分類資料，並識別您的資料保護必須可以協助您設計您的應用程式與資料安全性納入考量。 敏感度分類 （分類） 儲存資料和業務衝擊可協助開發人員判斷與資料相關聯的風險。

當您設計您的資料格式時，請為機密標籤所有適用的資料。 請確定應用程式會將適當的資料，為機密。 這些作法可協助您保護您的機密資料：

- 使用加密。
- 避免硬式編碼祕密，例如金鑰和密碼。
- 請確定存取控制和稽核會在進行中。

#### <a name="use-encryption"></a>使用加密

保護資料，應該是您的安全性策略中不可或缺的一部分。
如果您的資料儲存在資料庫或位置之間來回移動，使用的加密[待用資料](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)（雖然在資料庫中） 和加密[傳輸中的資料](https://docs.microsoft.com/azure/security/azure-security-data-encryption-best-practices#protect-data-in-transit)（在它的方式，以及來自使用者的資料庫、 API 或服務端點）。 我們建議您一律使用 SSL/TLS 通訊協定來交換資料。 請務必使用最新版的 TLS 加密 （目前，這是 1.2 版）。

#### <a name="avoid-hard-coding"></a>避免硬式編碼

一些事項應該永遠不會是硬式編碼在您的軟體。 一些範例包括主機名稱或 IP 位址、 Url、 電子郵件地址、 使用者名稱、 密碼、 儲存體帳戶金鑰和其他密碼編譯金鑰。 請考慮實作什麼可以或不能是硬式編碼在您的程式碼，包括您的程式碼的註解區段中的要求。

當您在程式碼中將註解時，請確定您未儲存任何敏感性資訊。 這包括您的電子郵件地址、 密碼、 連接字串、 您的應用程式會只知道有人在您的組織，和任何其他項目，可讓攻擊者攻擊您的應用程式或組織中的一項優點的相關資訊.

基本上，假設部署時，在您的開發專案中的所有項目將會公開資訊。 請避免在專案中包含的任何種類的敏感性資料。

我們所討論的稍早[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)。 您可以使用 Key Vault 來儲存祕密，例如金鑰和密碼，而不是硬式編碼。 當您使用金鑰保存庫中受管理的身分識別搭配適用於 Azure 資源時，Azure web 應用程式可以存取祕密的組態值輕鬆且安全地而不將任何機密資料儲存在您的原始檔控制或組態。 若要進一步了解，請參閱[管理您的伺服器應用程式使用 Azure Key Vault 中祕密](https://docs.microsoft.com/learn/modules/manage-secrets-with-azure-key-vault/)。

### <a name="implement-fail-safe-measures"></a>實作保全措施

您的應用程式必須能夠處理[錯誤](https://docs.microsoft.com/dotnet/standard/exceptions/)以一致的方式執行期間所發生。 應用程式應該攔截所有的錯誤並失敗安全或已關閉。

您也應該確定有足夠的使用者內容，以找出可疑或惡意活動會記錄錯誤。 記錄應保留足夠的時間，以允許延遲蒐證分析。 記錄檔應該是由記錄管理解決方案所使用的格式。 請確定會觸發警示與安全性相關的錯誤。 沒有足夠的記錄和監視可讓攻擊者進一步攻擊系統，並維護持續性。

### <a name="take-advantage-of-error-and-exception-handling"></a>利用錯誤和例外狀況處理

實作正確的錯誤並[例外狀況處理](https://docs.microsoft.com/dotnet/standard/exceptions/best-practices-for-exceptions)是撰寫程式碼防禦性很重要的一部分。 錯誤和例外狀況處理會使系統，可靠且安全的關鍵。 不同種類的安全性弱點，例如洩漏給攻擊者的資訊，協助深入了解您的平台和設計的攻擊者可能會導致錯誤處理中的錯誤。

請確定：

- 您可以處理例外狀況以集中式的方式，以避免重複[try/catch 區塊](https://docs.microsoft.com/dotnet/standard/exceptions/how-to-use-the-try-catch-block-to-catch-exceptions)程式碼中。

- 在應用程式內部處理所有非預期的行為。

- 會向使用者顯示的訊息不會遺漏重要的資料，但提供足夠的資訊來說明問題。

- 已記錄例外狀況，並提供足夠的資訊進行鑑識調查或事件回應小組調查。

[Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)提供的頂級體驗[錯誤和例外狀況處理](https://docs.microsoft.com/azure/logic-apps/logic-apps-exception-handling)相依系統所造成的。 您可以使用 Logic Apps 建立自動化工作和整合各企業或組織的應用程式、 資料、 系統和服務的程序的工作流程。

### <a name="use-logging-and-alerting"></a>使用記錄和警示

[記錄](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1)您安全性問題的安全性調查，並觸發警示的相關問題，確保使用者及時知道問題。 在所有元件上啟用稽核和記錄。 稽核記錄檔應擷取使用者內容，並識別所有重要的事件。

請檢查您不到您的網站記錄使用者送出任何機密資料。 敏感性資料的範例包括︰

- 使用者認證
- 社會安全號碼或其他識別資訊
- 信用卡號碼或其他財務資訊
- 健康狀態資訊
- 私用金鑰或其他可以用來解密已加密的資訊的資料
- 可用來更有效地攻擊應用程式的系統或應用程式資訊

請確定應用程式會監視使用者管理事件，例如成功和失敗的使用者登入、 重設密碼、 密碼變更、 帳戶鎖定，以及使用者註冊。 這些事件的記錄功能可協助您偵測及回應可疑的行為。 它也可讓您收集操作資料，例如誰正在存取應用程式。

## <a name="next-steps"></a>後續步驟
在下列文章中，我們建議的安全性控制和活動，可協助您開發和部署安全的應用程式。

- [開發安全應用程式](secure-develop.md)
- [部署安全的應用程式](secure-deploy.md)
