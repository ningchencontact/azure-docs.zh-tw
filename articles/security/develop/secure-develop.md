---
title: 開發在 Microsoft Azure 上安全的應用程式
description: 這篇文章會討論最佳作法，請考慮在您的 web 應用程式專案的實作及驗證階段。
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/12/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: f4add4bf07178aa616e86f8a64b313630466824f
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2019
ms.locfileid: "67653273"
---
# <a name="develop-secure-applications-on-azure"></a>在 Azure 上開發安全的應用程式
在本文中我們將會呈現安全性活動和您開發的雲端應用程式時要考量的控制項。 安全性問題和概念，請考慮在 Microsoft 的實作及驗證階段期間[安全性開發生命週期 (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx)涵蓋。 目標是為了協助您定義活動和 Azure 服務可供您開發更安全的應用程式。

此文章涵蓋下列 SDL 階段：

- 實作
- 驗證

## <a name="implementation"></a>實作
實作階段的重點是，建立早期預防的最佳作法並偵測和移除的程式碼中的安全性問題。
假設您的應用程式將用於您不想要使用它的方式。 這可協助您防止意外或刻意誤用您的應用程式。

### <a name="perform-code-reviews"></a>執行程式碼檢閱

簽入程式碼之前，請進行[程式碼檢閱](https://docs.microsoft.com/azure/devops/learn/devops-at-microsoft/code-reviews-not-primarily-finding-bugs)增加整體的程式碼品質，並降低製造 bug 的風險。 您可以使用[Visual Studio](https://docs.microsoft.com/azure/devops/repos/tfvc/get-code-reviewed-vs?view=vsts)管理程式碼檢閱程序。

### <a name="perform-static-code-analysis"></a>執行靜態程式碼分析

[靜態程式碼分析](https://www.owasp.org/index.php/Static_Code_Analysis)(也稱為*來源程式碼分析*) 通常執行程式碼檢閱的一部分。 靜態程式碼分析通常是指執行靜態程式碼分析工具，以利用之類的技術，在非執行程式碼中尋找潛在的弱點[誤導檢查](https://en.wikipedia.org/wiki/Taint_checking)並[資料流程分析](https://en.wikipedia.org/wiki/Data-flow_analysis)。

Azure Marketplace 供應項目[開發人員工具](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1&search=code%20review)，執行靜態程式碼分析，並協助進行程式碼檢閱。

### <a name="validate-and-sanitize-every-input-for-your-application"></a>驗證和處理您的應用程式的每個輸入

所有的輸入視為未受信任來保護您的應用程式，從最常見的 web 應用程式弱點。 不受信任的資料是資料隱碼攻擊的工具。 輸入您的應用程式包含在 URL 中，將資料從資料庫或從 API 的使用者輸入的參數，並傳入之使用者的任何項目無法操控。 應用程式應該[驗證](https://www.owasp.org/index.php/OWASP_Proactive_Controls_2016#4:_Validate_All_Inputs)資料是語法和語意有效的之前的應用程式使用的資料，以任何方式 （包括顯示給使用者）。

驗證及早在資料流程中，以確保只有正確格式的資料進入工作流程中的輸入。 您不想要在您的資料庫中保存或觸發的下游元件中故障的格式不正確資料。

兩種一般的方法，來執行輸入的語法驗證是 blacklisting 和列入允許清單：

  - 黑名單功能，嘗試檢查指定的使用者輸入不包含 「 已知為惡意程式 」 的內容。

  - 檢查指定的使用者輸入符合一組 「 已知的良好 」 輸入嘗試加入白名單。 字元為基礎的白名單是一種允許清單應用程式，會檢查使用者輸入包含唯一 「 已知良好 」 的字元，或輸入符合已知的格式。
    比方說，這可能需要檢查使用者名稱包含英數字元，或其中包含兩個的數字。

加入白名單是慣用的方法，供您建置安全的軟體。
黑名單功能，是容易發生錯誤，因為它是不可能將輸入可能錯誤的完整清單。

進行這項工作在伺服器上，不會在用戶端 （或伺服器上和用戶端上）。

### <a name="verify-your-applications-outputs"></a>請確認您的應用程式輸出

應該一律編碼和逸出您以視覺化方式或文件中出現的任何輸出。 [逸出](https://www.owasp.org/index.php/Injection_Theory#Escaping_.28aka_Output_Encoding.29)也稱為*輸出編碼*，用來協助確保不受信任的資料不是資料隱碼攻擊的工具。 逸出，結合資料驗證、 提供提高整體系統安全性的多層式防禦措施。

逸出可確保所有內容會顯示為*輸出。* 逸出，也可讓知道資料不用來執行，而這可防止工作攻擊的解譯器。 這是另一個常見的攻擊技巧稱為*跨網站指令碼*(XSS)。

如果您使用協力廠商的 web 架構，您可以確認使用輸出編碼方式在網站上的選項[OWASP XSS 防護功能提要](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.md)。

### <a name="use-parameterized-queries-when-you-contact-the-database"></a>當您連線到資料庫時，使用參數化的查詢

永遠不會在您的程式碼中建立 「 」 的內嵌資料庫查詢，並直接傳送到資料庫。 惡意程式碼插入您的應用程式可能導致您遭竊、 抹除，或修改的資料庫。 您的應用程式也可用來裝載您資料庫的作業系統上執行惡意的作業系統命令。

相反地，使用參數化的查詢或預存程序。 當您使用參數化的查詢時，您可以安全地從您的程式碼叫用程序，並將它而不必擔心，它將會視為查詢陳述式的一部分傳遞的字串。

### <a name="remove-standard-server-headers"></a>移除標準伺服器標頭

標頭 （例如 Server)，X 電源-依據，且 X AspNet 版本顯示的伺服器和基礎技術的相關資訊。 我們建議您隱藏這些標頭，以避免記錄特徵應用程式。
請參閱[移除 Azure 網站上的標準伺服器標頭](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/)。

### <a name="segregate-your-production-data"></a>區隔您的生產環境資料

您的生產環境資料或 「 真實 」 的資料，不應用於開發、 測試，或比是企業想要的任何其他用途。 不顯示字元 ([匿名](https://en.wikipedia.org/wiki/Data_anonymization)) 資料集應該是用於所有的開發和測試。

這表示較少的人可以存取您的實際資料，以減少受攻擊面。 這也表示較少的員工，請參閱 「 排除潛在的缺口以機密的個人資料。

### <a name="implement-a-strong-password-policy"></a>實作強式密碼原則

若要防止暴力和以字典為基礎的猜測，您必須實作強式密碼原則，以確保使用者建立複雜的密碼 （例如，12 個字元，最小長度和需要英數字元與特殊字元）。

您可以使用身分識別架構建立和強制執行密碼原則。 Azure AD B2C 可協助您使用密碼管理藉由提供[內建原則](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows#create-a-password-reset-user-flow)，[自助式密碼重設](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-sspr)，等等。

若要防禦攻擊預設帳戶，驗證所有金鑰和密碼都是可取代，而且它們是用來產生，或之後安裝資源取代。

如果應用程式必須自動產生密碼，請確定所產生的密碼是隨機的且具有高熵。

### <a name="validate-file-uploads"></a>驗證檔案上傳

如果您的應用程式可讓[檔案上傳](https://www.owasp.org/index.php/Unrestricted_File_Upload)，請考慮為有風險的活動，您可以採取的預防措施。 許多攻擊的第一個步驟是取得一些惡意的程式碼遭受攻擊的系統。 使用檔案上傳可幫助達成此目的，攻擊者。 OWASP 提供了如驗證檔案，以確保您上傳的檔案是安全的解決方案。

反惡意程式碼保護可協助識別並移除病毒、 間諜軟體及其他惡意軟體。 您可安裝 [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) 或 Microsoft 合作夥伴的端點保護解決方案 ([Trend Micro](https://www.trendmicro.com/azure/)、[Symantec](https://www.symantec.com/products)、[McAfee](https://www.mcafee.com/us/products.aspx)、[Windows Defender](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10) 及 [System Center Endpoint Protection](https://docs.microsoft.com/sccm/protect/deploy-use/endpoint-protection))。

[Microsoft 反惡意程式碼](https://docs.microsoft.com/azure/security/azure-security-antimalware)包含功能，例如即時保護、 排程掃描、 惡意程式碼補救、 簽章更新、 引擎更新、 範例報告、 和排除事件收集。 您可以將 Microsoft Antimalware 和合作夥伴解決方案與 [Azure 資訊安全中心](https://docs.microsoft.com/azure/security-center/security-center-partner-integration)整合，以方便部署和執行內建偵測 (警示與事件)。

### <a name="dont-cache-sensitive-content"></a>不要快取敏感性內容

不會快取敏感性內容的瀏覽器上。 瀏覽器可以儲存快取和歷程記錄資訊。 快取的檔案會儲存在像是 [Temporary Internet Files] 資料夾中，在 Internet Explorer 的情況下的資料夾。 同樣地，在瀏覽器時參考這些頁面會顯示從其快取的頁面。 如果 （地址、 信用卡詳細資料、 社會安全號碼、 使用者名稱） 的機密資訊顯示給使用者時，資訊可能儲存在瀏覽器的快取，並無法擷取，藉由檢查瀏覽器的快取或直接按瀏覽器的**上一步** 按鈕。

## <a name="verification"></a>驗證
驗證階段牽涉到完整的投入時間，以確保程式碼符合上述的階段中建立的安全性和隱私權原則。

### <a name="find-and-fix-vulnerabilities-in-your-application-dependencies"></a>找出並修正應用程式相依性中的弱點

您可以掃描您的應用程式和其相依的程式庫，以識別任何已知易受攻擊的元件。 包含可用來執行這項掃描的產品[OWASP 相依性檢查](https://www.owasp.org/index.php/OWASP_Dependency_Check)，[Snyk](https://snyk.io/)，並[Black Duck](https://www.blackducksoftware.com/)。

弱點掃描跨足[Tinfoil Security](https://www.tinfoilsecurity.com/)適用於 Azure App Service Web Apps。 [透過 App Service 的 tinfoil 安全性掃描](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/)提供開發人員和管理員的快速、 整合式，且符合經濟效益的探索與定址的弱點之前是惡意執行者可以利用這些, 方法。

> [!NOTE]
> 您也可以[整合 Tinfoil Security 與 Azure AD](https://docs.microsoft.com/azure/active-directory/saas-apps/tinfoil-security-tutorial)。 將 Tinfoil Security 與 Azure AD 整合提供下列優點：
>  - 在 Azure AD 中，您可以控制誰有權存取 Tinfoil Security。
>  - 您的使用者可以自動登入 Tinfoil security （單一登入） 使用其 Azure AD 帳戶。
>  - 您可以管理您在單一中央位置，在 Azure 入口網站中的帳戶。

### <a name="test-your-application-in-an-operating-state"></a>測試您的應用程式處於營運狀態

動態應用程式安全性測試 (DAST) 是測試應用程式中作業的狀態，以找出安全性弱點的程序。 DAST 工具分析程式，同時執行時找出安全性弱點，例如記憶體損毀、 不安全的伺服器組態、 跨網站指令碼、 使用者權限問題、 SQL 插入式攻擊和其他重要的安全性考量。

DAST 與靜態應用程式安全性測試 (SAST) 不同。 SAST 工具會分析原始程式碼或編譯版本的程式碼，以便找出安全性缺陷不執行程式碼時。

為安全性專業人員的協助，最好是執行 DAST，([滲透測試人員](https://docs.microsoft.com/azure/security/azure-security-pen-testing)或弱點評估工具)。 如果為安全性專業人員無法使用，您可以執行 DAST 您自己的 web proxy 掃描器及一些訓練。 插入 DAST 掃描器早期採用以確保不會明顯的安全性問題導致您的程式碼。 請參閱[OWASP](https://www.owasp.org/index.php/Category:Vulnerability_Scanning_Tools)取得一份 web 應用程式漏洞掃描器的站台。

### <a name="perform-fuzz-testing"></a>執行模糊 （fuzz） 測試

在 [模糊 （fuzz) 測試](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/)，刻意簡介格式不正確或隨機資料，應用程式引發程式失敗。 引發程式失敗，可協助應用程式發行之前顯示潛在的安全性問題。

[安全性風險偵測](https://docs.microsoft.com/security-risk-detection/)是 Microsoft 唯一模糊 （fuzz） 測試服務的軟體在發現安全性關鍵的 bug。

### <a name="conduct-attack-surface-review"></a>進行攻擊面的檢閱

程式碼完成功能可協助確保任何的設計或實作變更為應用程式，或已被視為系統之後，請檢閱受攻擊面。 它可協助確保任何新的攻擊媒介，因為變更，包括威脅分析模型，建立已檢閱並降低。

您可以建置受攻擊面的圖片掃描應用程式。 Microsoft 提供一個稱為的攻擊面分析工具[Attack Surface Analyzer](https://www.microsoft.com/download/details.aspx?id=24487)。 您可以選擇許多商業的動態測試和弱點掃描工具或服務，包括[OWASP Zed 攻擊 Proxy 專案](https://www.owasp.org/index.php/OWASP_Zed_Attack_Proxy_Project)， [Arachni](http://arachni-scanner.com/)， [Skipfish](https://code.google.com/p/skipfish/)，並[w3af](http://w3af.sourceforge.net/)。 這些掃描工具搜耙您的應用程式，並將對應的部分應用程式可透過 web 存取。 您也可以搜尋 Azure Marketplace 中的類似[開發人員工具](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1)。

### <a name="perform-security-penetration-testing"></a>執行安全性滲透測試

確保您的應用程式的安全是與測試任何其他功能一樣重要。 製作[滲透測試](https://docs.microsoft.com/azure/security/azure-security-pen-testing)建置和部署程序的標準部分。 排程定期安全性測試和弱點掃描上部署應用程式，並監視開啟連接埠、 端點和攻擊。

### <a name="run-security-verification-tests"></a>執行安全性驗證測試

[Secure DevOps Kit for Azure](https://azsk.azurewebsites.net/index.html) (AzSK) 包含 SVTs Azure 平台的多個服務。 您執行這些 SVTs 定期以確保您的 Azure 訂用帳戶和不同的資源組成您的應用程式處於安全狀態。 您也可以使用 AzSK，使 SVTs 成為 Visual Studio 擴充功能的持續整合/持續部署 (CI/CD) 擴充功能來自動化這些測試。

## <a name="next-steps"></a>後續步驟
在下列文章中，我們建議的安全性控制活動，可協助您設計並部署安全的應用程式。

- [設計安全的應用程式](secure-design.md)
- [部署安全的應用程式](secure-deploy.md)
