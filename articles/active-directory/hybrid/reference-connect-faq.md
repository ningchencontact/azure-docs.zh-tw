---
title: Azure Active Directory Connect 常見問題集 - | Microsoft Docs
description: 本文將回答有關 Azure AD Connect 的常見問題。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 4e47a087-ebcd-4b63-9574-0c31907a39a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 05/03/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2caca430de5ad666f4f4341e0723bc3173d6d91a
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65137791"
---
# <a name="azure-active-directory-connect-faq"></a>Azure Active Directory Connect 常見問題集

## <a name="general-installation"></a>一般安裝

**问：如何強化我的 Azure AD Connect 伺服器，來減少安全性攻擊？**

Microsoft 建議強化您的 Azure AD Connect 伺服器，可減少安全性攻擊面，您的 IT 環境的這個重要元件。  遵循下列建議將會降低您的組織的安全性風險。

* 部署在加入網域的伺服器上的 Azure AD Connect 和網域系統管理員或其他具有受到嚴格控制的安全性群組來限制系統管理存取權

若要深入了解，請參閱： 

* [保護系統管理員群組](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-g--securing-administrators-groups-in-active-directory)

* [保護內建的 administrator 帳戶](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-d--securing-built-in-administrator-accounts-in-active-directory)

* [改進安全性和 sustainment 藉由降低受攻擊面](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access#2-reduce-attack-surfaces )

* [減少 Active Directory 攻擊面](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface)

**问：如果 Azure Active Directory (Azure AD) 全域管理員已啟用雙重要素驗證 (2FA)，安裝是否可以運作？**  
從 2016 年 2 月的組建開始，系統已可支援此情況。

**问：是否有方法可自動安裝 Azure AD Connect？**  
您只可使用安裝精靈來進行 Azure AD Connect 安裝。 不支援自動和無訊息安裝。

**问：我有一個樹系，但無法連線到其中一個網域。如何安裝 Azure AD Connect？**  
從 2016 年 2 月的組建開始，系統已可支援此情況。

**问：Azure Active Directory Domain Services (Azure AD DS) 的健康情況代理程式是否可以在伺服器核心上運作？**  
是。 安裝代理程式之後，您可以使用下列 PowerShell Cmdlet 來完成註冊程序︰ 

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

**问：Azure AD Connect 是否支援從兩個網域同步至 Azure AD？**  
是，支援這種情況。 請參閱[多個網域](how-to-connect-install-multiple-domains.md)。
 
**问：Azure AD Connect 中的同一個 Active Directory 網域是否可以有多個連接器？**  
不行，相同 AD 網域不能有多個連接器。 

**问：是否可以將 Azure AD Connect 資料庫從本機資料庫移至遠端 SQL Server 執行個體？**   
是，下列步驟將提供如何執行這項操作的一般指引。 我們目前正在製作更詳細的文件。
1. 備份 LocalDB ADSync 資料庫。
執行此操作最簡單的方式是使用與 Azure AD Connect 安裝在同一部機器上的 SQL Server Management Studio。 請連線至 *(LocalDb).\ADSync*，然後備份 ADSync 資料庫。

2. 將 ADSync 資料庫還原至您的遠端 SQL Server 執行個體。

3. 針對現有的[遠端 SQL 資料庫](how-to-connect-install-existing-database.md)安裝 Azure AD Connect。
   本文將示範如何遷移至使用本機 SQL 資料庫。 如果您要遷移至使用遠端 SQL 資料庫，則在此程序的步驟 5 中，您也必須輸入現有服務帳戶作為 Windows 同步服務執行時的依據。 此同步引擎服務帳戶的描述如下：
   
      **使用現有的服務帳戶**：Azure AD Connect 預設會使用虛擬服務帳戶，以供同步處理服務使用。 如果您是使用遠端 SQL Server 執行個體或需要驗證的 Proxy，請使用受控服務帳戶，或使用網域中知道密碼的服務帳戶。 在這類情況下，請輸入要使用的帳戶。 請確定執行安裝的使用者是 SQL 中的系統管理員，以便建立服務帳戶的登入認證。 如需詳細資訊，請參閱 [Azure AD Connect 帳戶與權限](reference-connect-accounts-permissions.md#adsync-service-account)。 
   
      使用最新的組建，SQL 管理員即可執行頻外資料庫佈建，然後由具有資料庫擁有者權限的 Azure AD Connect 管理員進行安裝。 如需詳細資訊，請參閱[使用 SQL 委派的管理員權限安裝 Azure AD Connect](how-to-connect-install-sql-delegation.md)。

為了簡單起見，我們建議安裝 Azure AD Connect 的使用者就是 SQL 中的系統管理員。 不過，在最新的組建中，您現在已可使用委派的 SQL 系統管理員，如[使用 SQL 委派管理員權限安裝 Azure AD Connect](how-to-connect-install-sql-delegation.md)中所述。

**问：有哪些欄位中的最佳做法為何？**  

以下是提供一些最佳做法工程、 支援的參考文件和我們的顧問已經開發多年。  項目符號清單可快速參考 所示。  雖然這份清單會嘗試在完整，但可能會有其他可能不導致它在清單尚未的最佳作法。

- 如果使用完整的 SQL，則它應該保留本機與遠端
    - 躍點較少
    - 更容易進行疑難排解
    - 較不複雜
    - 必須指定 SQL 的資源，並允許 Azure AD Connect 和 OS 的額外負荷
- 略過 Proxy 時，如果可以的話，如果您無法略過 proxy，則您需要確定的逾時值是 5 分鐘以上。
- 如果 proxy 是必要的則您必須將 proxy 加入至 machine.config 檔案
- 請注意本機的 SQL 作業和維護，以及它們會如何影響 Azure AD Connect-特別重新編製索引
- 確保可外部解析 DNS
- 請確認[伺服器規格](how-to-connect-install-prerequisites.md#hardware-requirements-for-azure-ad-connect)建議每個您使用實體或虛擬伺服器
- 如果您使用所需的資源為專用的虛擬伺服器，請確認
- 請確定您有磁碟和磁碟組態符合適用於 SQL Server 的最佳作法
- 安裝和設定 Azure AD Connect Health 進行監視
- 使用內建於 Azure AD Connect 的刪除閾值。
- 仔細檢閱版本更新的所有變更和新的屬性，可加入準備
- 備份所有項目
    - 備份金鑰
    - 備份的同步處理規則
    - 備份伺服器設定
    - 備份 SQL 資料庫
- 請確定沒有任何第 3 個合作對象備份代理程式沒有 SQL VSS 寫入器 （在第 3 個合作對象的快照集的虛擬伺服器的一般） 的 SQL 備份
- 限制的自訂同步處理規則所使用，因為它們會增加複雜度
- 將 Azure AD 連接的伺服器，如層 0 個伺服器
- 是 leery 修改沒有很好的影響，以及正確的商務驅動程式的了解雲端同步處理規則
- 請確定正確的 URL 和防火牆連接埠已開啟以支援 Azure AD Connect 與 Azure AD Connect Health
- 利用雲端已篩選的屬性，以進行疑難排解，並避免虛設的物件
- 預備伺服器以確保您使用 Azure AD Connect 組態文件產生器的伺服器之間的一致性
- 預備伺服器應位於不同的資料中心 （實體位置
- 預備伺服器不是用來提供高可用性解決方案，但是您可以有多個預備伺服器
- 簡介 「 延遲 」 的預備伺服器可以減輕一些可能的停機時間，如果發生錯誤
- 測試並先驗證預備伺服器上的所有升級
- 一律驗證匯出之前先切換至預備 serverLeverage 完整匯入和完整的同步處理，以減少營運影響預備伺服器
- 讓 Azure AD Connect 伺服器之間的版本一致性盡量 

**问：我要允許在工作群組機器上建立的 Azure AD 連接器帳戶的 Azure AD Connect？**
沒有。  為了讓 Azure AD Connect 來自動建立 Azure AD Connector 帳戶，電腦必須已加入網域。  

## <a name="network"></a>網路
**问：我的防火牆、網路裝置或其他軟硬體會限制連線在網路上保持開啟的時間。當我使用 Azure AD Connect 時，用戶端逾時閥值的時間應該多長？**  
所有網路軟體、實體裝置或其他軟硬體限制連線開啟時間上限的閥值應該至少為 5 分鐘 (300 秒)，以便讓安裝 Azure AD Connect 用戶端的伺服器與 Azure Active Directory 連線。 此建議也適用於所有先前發行的 Microsoft 身分識別同步處理工具。

**问：是否支援單一標籤網域 (SLD)？**  
雖然非常不建議您使用這種網路設定 ([請參閱文章](https://support.microsoft.com/help/2269810/microsoft-support-for-single-label-domains))，但只要單一層級網域的網路設定可正常運作，便支援搭配使用 Azure AD Connect 同步處理與單一標籤網域。

**问：是否支援含有斷續 AD 網域的樹系？**  
否，Azure AD Connect 不支援包含斷續命名空間的內部部署樹系。

**问：是否支援「有句點」的 NetBIOS 名稱？**  
否，Azure AD Connect 不支援 NetBIOS 名稱包含句點 (.) 的內部部署樹系或/網域。

**问：是否支援純 IPv6 環境？**  
否，Azure AD Connect 不支援純 IPv6 環境。

**問︰我有多樹系環境，並在兩個樹系之間的網路使用 NAT (網路位址轉譯)。是否支援在這兩個個樹系之間使用 Azure AD Connect？**</br>
 否，不支援透過 NAT 使用 Azure AD Connect。 

## <a name="federation"></a>同盟
**问：如果我收到一封電子郵件，要求我更新我的 Office 365 憑證，該怎麼辦？**  
如需有關更新憑證的指引，請參閱[更新憑證](how-to-connect-fed-o365-certs.md)。

**问：我已經針對 Office 365 信賴憑證者設定 [自動更新信賴憑證者]。當我的權杖簽署憑證自動換用時，需要採取任何動作嗎？**  
請參考[更新憑證](how-to-connect-fed-o365-certs.md)一文中概述的指導方針。

## <a name="environment"></a>環境
**问：是否支援在安裝 Azure AD Connect 之後重新命名伺服器？**  
沒有。 變更伺服器名稱會使同步引擎無法連線到 SQL 資料庫執行個體，並且無法啟動此服務。

**问：下一代密碼編譯 (NGC) 同步處理規則支援在啟用 FIPS 的電腦上？**  
沒有。  不支援。

## <a name="identity-data"></a>身分識別資料
**问：Azure AD 中的 userPrincipalName (UPN) 屬性為什麼與內部部署的 UPN 不符？**  
如需詳細資訊，請參閱這些文章：

* [Office 365、Azure 或 Intune 中的使用者名稱不符合內部部署的 UPN 或替代登入識別碼](https://support.microsoft.com/kb/2523192)
* [在您將使用者帳戶的 UPN 變更為使用不同的同盟網域後，Azure Active Directory 同步作業工具未同步處理變更](https://support.microsoft.com/kb/2669550)

您也可以將 Azure AD 設定為允許同步處理引擎更新 UPN，如 [Azure AD Connect 同步處理服務功能](how-to-connect-syncservice-features.md)中所述。

**问：是否支援將內部部署 Azure AD 群組或連絡人物件與現有的 Azure AD 群組或連絡人物件進行大致相符比對？**  
是，此大致相符比對會依據 proxyAddress。 針對未啟用郵件功能的群組，不支援大致相符比對。

**问：是否支援將現有 Azure AD 群組或連絡人物件上的 ImmutableId 屬性手動設定成與內部部署 Azure AD 群組或連絡人物件完全相符？**  
否，目前不支援將現有 Azure AD 群組或連絡人物件上的 ImmutableId 屬性手動設定成與之完全相符。

## <a name="custom-configuration"></a>自訂組態
**问：哪裡記載了適用於 Azure AD Connect 的 PowerShell Cmdlet？**  
除了記載於本網站上的 Cmdlet，在 Azure AD Connect 中找到的其他 PowerShell Cmdlet 不支援客戶使用。

**问：我是否可以使用在 Synchronization Service Manager 中找到的 [伺服器匯出/伺服器匯入] 選項，在伺服器之間移動組態？**  
沒有。 此選項不會擷取所有組態設定，因此不應使用。 應改用精靈在第二部伺服器上建立基底組態，並使用同步處理規則編輯器產生 PowerShell 指令碼，以在伺服器之間移動任何自訂規則。 如需詳細資訊，請參閱[變換移轉](how-to-upgrade-previous-version.md#swing-migration)。

**问：是否可以針對 Azure 登入頁面進行密碼快取，以及是否可以因為此快取包含具有 *autocomplete = "false"* 屬性的密碼輸入元素而防止進行此快取？**  
目前不支援修改**密碼**欄位的 HTML 屬性，包括自動完成標記。 我們目前正在開發適用於自訂 JavaScript 的功能，可讓您將任何屬性新增至 [密碼] 欄位。

**问：Azure 登入頁面會顯示先前登入成功的使用者名稱。可以關閉此行為嗎？**  
目前不支援修改**密碼**輸入欄位的 HTML 屬性，包括自動完成標記。 我們目前正在開發適用於自訂 JavaScript 的功能，可讓您將任何屬性新增至 [密碼] 欄位。

**问：是否有方法可防止並行的工作階段？**  
沒有。

## <a name="auto-upgrade"></a>自動升級

**问：使用自動升級的優點和後果為何？**  
我們建議所有客戶針對其 Azure AD Connect 安裝啟用自動升級。 優點是您一律會收到最新修補程式，包括在 Azure AD Connect 中發現的弱點安全性更新。 升級程序輕鬆無比，而且在有新版本可用時就會自動執行。 有數千個 Azure AD Connect 客戶會透過自動升級來使用每個新版本。

自動升級程序一律會先確定安裝是否符合自動升級資格。 若符合資格，就會執行和測試升級。 此程序也包含尋找規則的自訂變更和特定環境因素。 如果測試顯示升級不成功，則會自動還原到先前的版本。

根據環境的大小，此程序可能需要幾個小時。 正在進行升級時，不會發生任何 Windows Server Active Directory 與 Azure AD 之間的同步處理。

**问：我收到一封電子郵件，通知我自動升級不再有效，而需要安裝新的版本。我為何需要這麼做？**  
在某些情況下，我們去年所發行的 Azure AD Connect 版本，可能已停用您的伺服器上的自動升級功能。 我們已在 Azure AD Connect 1.1.750.0 版中修正此問題。 如果您已受到此問題的影響，您可以藉由執行 PowerShell 指令碼進行修復，或以手動方式升級至最新版 Azure AD Connect 來解決此問題。 

若要執行 PowerShell 指令碼，請[下載指令碼](https://aka.ms/repairaadconnect)，並在系統管理 PowerShell 視窗中的 Azure AD Connect 伺服器上執行指令碼。 若要了解如何執行指令碼，[請檢視此短片](https://aka.ms/repairaadcau)。

若要手動升級，您必須下載並執行最新版的 AADConnect.msi 檔案。
 
-  如果您目前的版本早於 1.1.750.0，[請下載最新版本並進行升級](https://www.microsoft.com/download/details.aspx?id=47594)。
- 如果您的 Azure AD Connect 版本是 1.1.750.0 或更新版本，則不需要執行進一步的動作。 您已使用包含自動升級修正程式的版本。 

**问：我收到一封電子郵件，通知我要升級至最新版本以重新啟用自動升級。我目前使用 1.1.654.0 版。是否需要升級？**  
是，您要升級至 1.1.750.0 版或更新版本，以重新啟用自動升級。 [下載最新版本並進行升級](https://www.microsoft.com/download/details.aspx?id=47594)。

**问：我收到一封電子郵件，通知我要升級至最新版本以重新啟用自動升級。如果我已使用 PowerShell 來啟用自動升級，我還需要安裝最新版本嗎？**  
是，您仍需要升級至 1.1.750.0 版或更新版本。 使用 PowerShell 啟用自動升級服務，並不能減輕在 1.1.750.0 之前版本中找到的自動升級問題。

**问：我想要升級至較新版本，但我不確定當初是誰安裝 Azure AD Connect，而且沒有使用者名稱和密碼。我需要此資訊嗎？**
您不需要知道原先用來升級 Azure AD Connect 的使用者名稱和密碼。 使用具有全域管理員角色的任何 Azure AD 帳戶即可。

**问：如何找出我目前使用的 Azure AD Connect 是哪一個版本？**  
若要確認您伺服器上所安裝的 Azure AD Connect 是哪個版本，請移至 [控制台] 並選取 [程式] > [程式和功能]，來查看所安裝的 Microsoft Azure AD Connect 版本，如下所示：

![控制台中的 Azure AD Connect 版本](./media/reference-connect-faq/faq1.png)

**问：如何升級至最新版的 Azure AD Connect？**  
若要了解如何升級至最新版本，請參閱 [Azure AD Connect：從舊版升級到最新版本](how-to-upgrade-previous-version.md)。 

**问：我們去年已升級至最新版 Azure AD Connect。我們需要再次升級嗎？**  
Azure AD Connect 小組會對此服務提出頻繁的更新。 若要獲取錯誤修正程式、安全性更新及新功能，請務必讓您的伺服器使用最新版本以保持最新狀態。 如果您啟用自動升級，您的軟體版本就會自動更新。 若要尋找 Azure AD Connect 的版本發行歷程記錄，請參閱[Azure AD Connect：版本發行歷程記錄](reference-connect-version-history.md)。

**问：執行升級需要多久時間，而且對我的使用者有何影響？**  
升級所需的時間需視您的租用戶大小而定。 對於較大的組織，可能適合在在晚上或週末執行升級。 升級期間不會發生任何同步處理活動。

**问：我確信我已升級到 Azure AD Connect，但 Office 入口網站中仍提及 DirSync。這是為什麼？**  
Office 小組正在處理 Office 入口網站更新，以反映目前的產品名稱。 它不會反映您所使用的同步處理工具。

**问：我的自動升級狀態顯示「已暫停」。它為何會擱置？應該加以啟用嗎？**  
在某些情況下，先前版本中引入的錯誤會讓自動升級狀態設定為「擱置」。 以手動方式啟用在技術上是可行的，但需要幾個複雜的步驟。 最佳做法是下載並安裝最新版的 Azure AD Connect。

**问：我的公司有嚴格的變更管理需求，我想要控制其推出時間。我可以控制何時啟動自動升級嗎？**  
不行，目前尚未有這類功能。 我們正在針對未來版本評估此功能。

**问：如果自動升級失敗，我是否會收到電子郵件？如何知道它是否成功？**  
您不會收到升級結果的通知。 我們正在針對未來版本評估此功能。

**问：您是否會發佈有關何時打算推出自動升級的時間表？**  
在較新版本的發行程序中，自動升級是第一個步驟。 每當有新版本時，升級就會自動推出。 [Azure AD 藍圖](../fundamentals/whats-new.md)會預先宣布較新的 Azure AD connect 版本。

**问：自動升級是否也會升級 Azure AD Connect Health？**  
是，自動升級也會升級 Azure AD Connect Health。

**问：您是否也會自動升級處於預備模式的 Azure AD Connect 伺服器？**  
是，您可以自動升級處於預備模式的 Azure AD Connect 伺服器。

**问：如果自動升級失敗，而我的 Azure AD Connect 伺服器並未啟動，該怎麼辦？**  
在少數情況下，Azure AD Connect 服務不會在執行升級之後啟動。 在這些情況下，請重新啟動伺服器，這樣通常會修正此問題。 如果 Azure AD Connect 服務還是不會啟動，請開啟支援票證。 如需詳細資訊，請參閱[ 建立服務要求以連絡 Office 365 支援人員](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/)。 

**问：我不確定升級至較新的 Azure AD Connect 版本時有何風險。您可以打電話給我來協助我升級嗎？**  
如果您需要升級至較新 Azure AD Connect 版本的協助，請在[建立服務要求以連絡 Office 365 支援人員](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/)上開啟支援票證。

## <a name="troubleshooting"></a>疑難排解
**问：如何取得 Azure AD Connect 的說明？**

[搜尋 Microsoft 知識庫 (KB)](https://www.microsoft.com/en-us/search/result.aspx?q=azure+active+directory+connect)

* 針對 Azure AD Connect 支援，在 KB 中搜尋常見協助修正問題的技術解決方案。

[Azure Active Directory 論壇](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)

* 您可以前往 [Azure AD 社群](https://social.msdn.microsoft.com/Forums/azure/en-US/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required) 來搜尋技術問題與解答，或詢問您自己的問題。

[取得 Azure AD 支援](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto)
