---
title: Azure Active Directory Connect 常見問題集 - | Microsoft Docs
description: 此頁面包含有關 Azure AD Connect 的常見問題集。
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 4e47a087-ebcd-4b63-9574-0c31907a39a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: billmath
ms.openlocfilehash: 46a9bf47b4998c4d5be47f67556fbdb3ba7b71db
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/11/2018
ms.locfileid: "34054089"
---
# <a name="frequently-asked-questions-for-azure-active-directory-connect"></a>Azure Active Directory Connect 的常見問題集

## <a name="general-installation"></a>一般安裝
**問：如果 Azure AD 全域管理員已啟用 2FA，安裝是否能夠運作？**  
從 2016 年 2 月的組建開始便提供這項支援。

**問：是否有方法可自動安裝 Azure AD Connect？**  
它僅支援使用安裝精靈來安裝 Azure AD Connect。 不支援自動和無訊息安裝。

**問：我有一個樹系，但無法連線到其中的網域。如何安裝 Azure AD Connect？**  
從 2016 年 2 月的組建開始便提供這項支援。

**問︰AD DS 健康情況代理程式是否是在伺服器核心上運作？**  
是。 安裝代理程式之後，您可以使用下列 PowerShell Cmdlet 來完成註冊程序︰ 

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

**問：AADConnect 是否支援從兩個網域同步至 Azure AD？**</br>
是，支援此做法。 請參閱[多個網域](active-directory-aadconnect-multiple-domains.md)
 
**問：Azure AD Connect 中是否支援相同 Active Directory 網域有多個連接器？**</br> 否，不提供支援。 

## <a name="network"></a>網路
**問：我的防火牆、網路裝置或其他軟硬體會限制在網路上開啟連線的時間上限。使用 Azure AD Connect 時，用戶端逾時閥值時間應該多長？**  
所有網路軟體、實體裝置或其他軟硬體限制連線開啟時間上限的閥值應該至少為 5 分鐘 (300 秒)，以便讓安裝 Azure AD Connect 用戶端的伺服器與 Azure Active Directory 連線。 這也適用於所有先前發行的  Microsoft Identity 同步處理工具。

**問：是否支援 SLD (單一標籤網域)？**  
否，Azure AD Connect 不支援使用 SLD 的內部部署樹系/網域。

**問：是否支援使用斷續 AD 網域的樹系？**  
否，Azure AD Connect 不支援包含斷續命名空間的內部部署樹系。

**問：是否支援有句點的 NetBios 名稱？**  
否，Azure AD Connect 不支援 NetBios 名稱包含句點的內部部署樹系/網域。

**問：是否支援純 IPv6 環境？**  
否，Azure AD Connect 不支援純 IPv6 環境。

## <a name="federation"></a>同盟
**問：如果我收到一封電子郵件，要求我更新我的 Office 365 憑證，該怎麼辦？**  
請參考 [更新憑證](active-directory-aadconnect-o365-certs.md) 主題中概述的指導方針來了解如何更新憑證。

**問：我已經針對 O365 信賴憑證者設定「自動更新信賴憑證者」。當我的權杖簽署憑證自動換用時，需要採取任何動作嗎？**  
請參考 [更新憑證](active-directory-aadconnect-o365-certs.md)一文中概述的指導方針。

## <a name="environment"></a>環境
**問：安裝 Azure AD Connect 之後，是否支援重新命名伺服器？**  
編號 變更伺服器名稱將會導致同步處理引擎無法連接到 SQL 資料庫，服務將無法啟動。

## <a name="identity-data"></a>身分識別資料
**問：Azure AD 中的 UPN (userPrincipalName) 屬性不符合內部部署的 UPN，為什麼？**  
請參閱以下文章：

* [Office 365、Azure 或 Intune 中的使用者名稱不符合內部部署的 UPN 或替代登入識別碼](https://support.microsoft.com/en-us/kb/2523192)
* [在您將使用者帳戶的 UPN 變更為使用不同的同盟網域後，Azure Active Directory 同步作業工具未同步處理變更](https://support.microsoft.com/en-us/kb/2669550)

您也可以將 Azure AD 設定為允許同步處理引擎更新 userPrincipalName，如 [Azure AD Connect 同步處理服務功能](active-directory-aadconnectsyncservice-features.md)中所述。

**問：是否支援將內部部署 AD「群組/連絡人」物件與現有的 Azure AD「群組/連絡人」物件進行大致相符比對？**  
是，這將會依據 proxyAddress。  針對未啟用郵件功能的群組，不支援大致相符比對。

**問：是否支援將現有 Azure AD「群組/連絡人」物件上的 ImmutableId 屬性手動設定成與內部部署 AD「群組/連絡人」物件完全相符？**  
否，目前不支援。

## <a name="custom-configuration"></a>自訂組態
**問：Azure AD Connect 適用的 PowerShell Cmdlet 記載於何處？**  
除了記載於本網站上的 Cmdlet，在 Azure AD Connect 中找到的其他 PowerShell Cmdlet 不支援客戶使用。

**問：我是否可以使用在 Synchronization Service Manager 中找到的「伺服器匯出/伺服器匯入」，在伺服器之間移動組態？**  
編號 此選項將不會擷取所有組態設定，因此不應使用。 您應該改用精靈在第二部伺服器上建立基底組態，並使用同步處理規則編輯器產生 PowerShell 指令碼，以在伺服器之間移動任何自訂規則。 請參閱[變換移轉](active-directory-aadconnect-upgrade-previous-version.md#swing-migration)。

**問︰是否可針對 Azure 登入頁面密碼快取，而且是否可以因為它包含自動完成 ="false" 屬性的密碼輸入元素而避免此行為？**</br>
我們目前不支援修改密碼輸入欄位的 HTML 屬性，包括自動完成標記。 我們目前正在開發適用於自訂 javascript 的功能，可讓您將任何屬性新增密碼欄位。 應可在 2017 年下半年使用這項功能。

**問︰在 Azure 登入頁面上，會顯示先前登入成功之使用者的使用者名稱。可以關閉此行為嗎？**</br>
我們目前不支援修改登入頁面的 HTML 屬性。 我們目前正在開發適用於自訂 javascript 的功能，可讓您將任何屬性新增密碼欄位。 應可在 2017 年下半年使用這項功能。

**問︰是否辦法避免並行的工作階段？**</br>
編號

## <a name="auto-upgrade"></a>自動升級

**問：使用自動升級的優點和後果為何？**</br>
我們建議所有客戶針對其 Azure AD Connect 安裝啟用自動升級。 優點是它們一律會收到最新修補程式，包括我們在 Azure AD Connect 中發現之弱點的安全性更新。 升級程序輕鬆無比，而且在有新版本可用時就會自動執行。 我們透過自動升級將每個新版本提供給數千個 Azure AD Connect 客戶。

不論安裝是否適合自動升級，一律先建立自動升級程序 (這包括尋找規則的自訂變更、特定環境因素等)，如此一來，就會執行並測試升級。 如果測試顯示升級不成功，則會自動還原先前的版本。

視環境的大小而定，此程序可能需要幾小時的時間，而在進行升級時，Windows Server AD 與 Azure AD 之間不會進行同步處理。

**問：我收到一封電子郵件告訴我，我的自動升級不再有效，我需要安裝新的版本。我為何需要這麼做？**</br>
在某些情況下，我們去年所發行的 Azure AD Connect 版本，可能已停用您的伺服器上的自動升級功能。 我們已在 Azure AD Connect 1.1.750.0 版 (上個月月底發行) 中修正此問題。 我們需要受此問題影響的客戶手動升級至最新版的 Azure AD Connect，以解決這個問題。 若要手動升級，您必須下載並執行最新版的 AADConnect.msi 檔案。
 
-  如果您目前的版本早於 1.1.750.0，您必須升級至最新版本 ([可在這裡下載](https://www.microsoft.com/en-us/download/details.aspx?id=47594))。
- 如果您的 Azure AD Connect 版本為 1.1.750.0 或更新版本，您不必採取任何動作來解決自動升級問題，因為您已經使用有這個修正程式的版本。 

**問：我收到一封電子郵件通知我升級至最新版本，以重新啟用自動升級。我目前使用 1.1.654.0，是否需要升級？** </br>    
是，您要升級至 1.1.750 或更新版本，以重新啟用自動升級。 以下是說明如何升級至較新版本的連結

**問：我收到一封電子郵件通知我升級至最新版本，以重新啟用自動升級。我已使用 PowerShell 來啟用自動升級，我還需要安裝最新版本嗎？**</br>    
是，您仍需要升級至 1.1.750.0 版或更新版本。 使用 PowerShell 啟用自動升級服務，並不能減輕在 1.1.750 之前的版本中找到的自動升級問題

**問：我想要升級至較新版本，但我不確定當初是誰安裝 Azure AD Connect，而且沒有使用者名稱和密碼。我需要此資訊嗎？**</br>
您不需要知道最初用來升級 Azure AD Connect 的使用者名稱和密碼 – 可以使用任何具有全域管理員角色的 Azure AD 帳戶。

**問：如何找出我目前使用的 Azure AD Connect 版本？**</br>   
若要確認您的伺服器上所安裝的 Azure AD Connect 版本，請移至 [控制台] 並且在 [程式] > [程式和功能] 中查看所安裝的 Microsoft Azure AD Connect 版本：

![version](media/active-directory-aadconnect-faq/faq1.png)

**問：如何執行升級至最新版的 AADConnect？**</br>    
這篇[文章](active-directory-aadconnect-upgrade-previous-version.md)說明如何升級至較新的版本。 

**問：我們去年已升級為最新版的 AADConnect，我們需要再次升級嗎？**</br> Azure AD Connect 小組經常更新服務，如果您的伺服器保持使用最新版本，即可受惠於錯誤修正程式和安全性更新，以及使用新功能。 如果您啟用自動升級，您的軟體版本就會自動更新。 若要尋找 Azure AD Connect 的版本發行歷程記錄，請遵循此[連結](active-directory-aadconnect-version-history.md)。

**問：執行升級需要多久時間，而且對我的使用者有何影響？**</br>    
升級所需的時間取決於您的租用戶大小，對於大型組織而言，最好在夜間或週末進行升級。 請注意，升級期間不會發生任何同步處理活動。

**問：我認為我已升級到 AADConnect，但是在 Office 入口網站中，它仍然提及 DirSync。這是為什麼？**</br>    
Office 小組正在處理 Office 入口網站更新，以反映目前的產品名稱 – 它不會反映您所使用的同步處理工具。

**問：我檢查了我的自動升級狀態，其顯示「擱置」。它為何會擱置？應該加以啟用嗎？**</br>     
在某些情況下，先前版本中引入的錯誤會讓自動升級狀態設定為「擱置」。 技術上有可能以手動方式啟用它，但是需要幾個複雜步驟，所以最佳做法是安裝最新版的 Azure AD Connect

**問：我的公司有嚴格的變更管理需求，我想要控制其推出的時間。我可以控制何時啟動自動升級嗎？**</br> 不可以，目前沒有這種功能，我們正評估在未來的版本中新增這種功能。

**問：如果自動升級失敗，我會收到電子郵件嗎？如何知道它是否成功？**</br>     
您不會獲知升級結果，我們正評估在未來的版本中新增這種功能。

**問：您是否會發佈有關何時打算推出自動升級的時間表？**</br>    
自動升級是較新版本發行程序中的第一個步驟，每當有新版本時，我們就會推送自動升級。 [Azure AD 藍圖](../../active-directory/whats-new.md)會預先宣布較新的 Azure AD connect 版本。

**問：自動升級是否會升級 AAD Connect Health？**</br>   是，自動升級也會升級 AAD Connect Health

**問：您也是在預備模式中自動升級 AAD Connect 伺服器嗎？**</br>   
不是，您無法自動升級處於預備模式的 Azure AD Connect 伺服器。

**問：如果自動升級失敗，而我的 AAD Connect 伺服器不會啟動，該怎麼辦？**</br>   
在少數情況下，Azure AD Connect 服務不會在執行升級之後啟動。 在這些情況下，請重新啟動伺服器，通常就會修正此問題。 如果 Azure AD Connect 服務還是不會啟動，請開啟支援票證。 這個[連結](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/)會說明如何執行該作業。 

**問：我不確定升級至較新的 Azure AD Connect 版本時有何風險。您可以打電話給我來協助我升級嗎？**</br>
如果您需要升級至較新 Azure AD Connect 版本的協助，請開啟支援票證，此[連結](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/)會示範如何執行該作業。

## <a name="troubleshooting"></a>疑難排解
**問：如何取得 Azure AD Connect 的說明？**

[搜尋 Microsoft 知識庫 (KB)](https://www.microsoft.com/en-us/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport)

* 在 Microsoft 知識庫 (KB) 中搜尋有關 Azure AD Connect 支援的常見協助修正問題的技術解決方案。

[Microsoft Azure Active Directory 論壇](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)

* 按一下 [這裡](https://social.msdn.microsoft.com/Forums/azure/en-US/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required)，即可在社群中搜尋和瀏覽技術問題和答案，或提出自己的問題。

[如何取得 Azure AD 支援](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto)

* 使用此連結透過 Azure 入口網站取得支援。

