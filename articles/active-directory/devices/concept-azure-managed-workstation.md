---
title: 為什麼安全的工作站是重要事項-Azure Active Directory
description: 了解為什麼組織應該建立安全的 Azure 管理工作站
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: frasim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 05d21910d1b3601346fbd038cbc25f8f2be61f99
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67110707"
---
# <a name="building-secure-workstations"></a>建置安全的工作站

受保護的隔離的工作站是至關重要的機密的角色，例如系統管理員、 開發人員和操作員關鍵服務的安全性。 許多其他的安全性管控和保證將會失敗，或不有任何作用，如果基礎的用戶端工作站安全性已遭入侵。

本文件說明如何建置安全的用戶端工作站與詳細的逐步指示，包括如何設定啟動安全性控制。 這種類型的工作站有時稱為 「 特殊權限的存取工作站 (PAW)，此參考，並建置 」。 不過指引以雲端為基礎的技術，來管理服務，看起來，並引進了安全性功能引進了從 Windows 10RS5、 Microsoft Defender ATP、 Azure Active Directory 和 Intune。

> [!NOTE]
> 這篇文章說明安全的工作站和其重要性的概念。 如果您已熟悉的概念，並想要跳至部署，請閱讀[如何部署安全的工作站](https://docs.microsoft.com/azure/active-directory/devices/howto-azure-managed-workstation)。

## <a name="why-securing-workstation-access-is-important"></a>保護工作站存取為何如此重要

快速採用雲端服務，並且能夠從任何地方工作已建立新的方法，可被利用。 攻擊者所利用弱式的安全性控制項，其中系統管理員工作，而能夠存取具有特殊權限資源的裝置上。

中所述[Verizon 威脅報告](https://enterprise.verizon.com/resources/reports/dbir/)，並[Security Intelligence Report](https://aka.ms/sir)濫用權限，以及供應鏈攻擊是違反組織中，使用的前五個機制，第二個最常在偵測到策略，就在 2018 年所回報的事件。

大部分的攻擊者請遵循下列路徑：

* 開始探查，通常特定產業中，若要尋找的方法
* 分析所收集的資訊來識別最佳的方式來存取 （滲透） 的低價值的工作站
* 持續性和方法來移動看[橫向](https://en.wikipedia.org/wiki/Network_Lateral_Movement)
* 竊取機密和敏感資料

攻擊者經常滲透探查裝置看起來很低風險或低估。 這些易受攻擊的裝置會接著可用來找出橫向移動的機會，尋找 系統管理使用者和裝置及識別高價值的資料的詳細資訊，成功竊取資訊一旦他們取得這些權限的使用者角色。

![一般的入侵模式](./media/concept-azure-managed-workstation/typical-timeline.png)

本文件提供解決方案來協助保護您的運算裝置，藉由隔離管理與服務，協助防止橫向移動，或從較不重要的產能裝置攻擊。 設計可協助減少能夠成功地執行缺口中斷之前用來管理或存取機密的雲端資源的裝置的滲透鏈結。 所描述的解決方案會利用原生的 Azure 服務屬於 Microsoft 365 企業版堆疊包括：

* Intune 裝置管理，包括應用程式和 URL 加入允許清單
* Autopilot 裝置設定和部署和重新整理 
* Azure AD 進行使用者管理、 條件式存取，以及多重要素驗證
* Windows 10 裝置健康情況證明和使用者體驗 （目前的版本）
* Microsoft Defender 進階威脅防護 (ATP) 的端點保護、 偵測及回應透過雲端管理
* Azure AD PIM 來管理授權，包括 Just In Time (JIT) 特殊權限資源的存取權

## <a name="who-benefit-from-using-a-secure-workstation"></a>人員受益於使用安全的工作站

所有使用者和運算子，都受益於使用安全的工作站。 攻擊者入侵電腦或裝置可以執行幾件事，包括模擬所有快取的帳戶，並使用認證和登入該裝置上使用的權杖。 這項風險會保護使用任何特殊權限的角色，包括系統管理權限很重要，因為裝置用於特殊權限的帳戶是橫向移動和權限提升攻擊目標的裝置。 這些帳戶可用於各種不同的資產例如：

* 在內部部署與雲端式系統的系統管理員
* 關鍵系統的開發人員工作站
* 具有高風險的社交媒體帳戶系統管理員
* 高度機密的工作站，例如 SWIFT 付款終端機
* 工作站處理營業祕密

Microsoft 建議實作，將這些帳戶用來降低風險的特殊權限工作站的提高權限的安全性控制項。 可以在中找到的其他指導方針[Azure Active Directory 功能部署指南](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-checklist-p2)， [Office 365 藍圖](https://aka.ms/o365secroadmap)，並[保護特殊權限存取計劃](https://aka.ms/sparoadmap))。

## <a name="why-dedicated-workstations"></a>為什麼專用的工作站

雖然您可以將安全性新增至現有的裝置，最好是開始使用安全的基礎。 開頭為已知良好的裝置和一組已知的安全性控制項將您的組織所維護的最佳位置中增加安全性層的級。 業餘的電子郵件和瀏覽的網頁所允許的攻擊媒介數目不斷成長，就越來越難確保裝置可以信任。 專用的工作站，假設此指南適用於分隔從標準的產能，瀏覽，並且會完成電子郵件工作。 移除生產力、 網頁瀏覽，以及從裝置的電子郵件會對產能的負面影響，但這個防衛機制就通常可接受的案例，其中 「 作業 」 工作不明確需要它，在安全性事件的風險很高。

> [!NOTE]
> 網頁瀏覽此處是指任意的網站，也就是使用網頁瀏覽器存取少量的已知的系統管理網站，例如 Azure、 Office 365、 其他雲端提供者和 SaaS 服務完全不同的高風險的一般存取應用程式。

內含項目策略提供更高的安全性保證，藉由增加的數目和類型的攻擊者必須解決才能存取機密資產的控制項。 這裡開發模型會提供給使用分層式權限模型的特定裝置的系統管理權限的內含項目。

## <a name="supply-chain-management"></a>供應鏈管理

務必在安全的工作站是供應鏈解決方案其中您所使用的工作站是受信任，'信任根'。 此解決方案可滿足使用信任的根[Microsoft Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-autopilot)技術。 針對受保護的工作站 Microsoft Autopilot 讓您能夠運用 Microsoft OEM 最佳化 Windows 10 裝置製造商提供已知的良好狀態。 而不是重新安裝映像可能不受信任的裝置，Microsoft Autopilot 可將轉換的 Windows 裝置的 「 商務就緒 」 狀態，套用的設定和原則，安裝應用程式，以及甚至變更的 Windows 10 版本 （例如，從正在使用Windows 10 專業版到 Windows 10 企業版，以支援進階的功能)。

![安全的工作站層級](./media/concept-azure-managed-workstation/supplychain.png)

## <a name="device-roles-and-profiles"></a>裝置角色和設定檔

整本指南中，多個安全性設定檔和角色將會解決，以實現更安全的解決方案使用者、 開發人員及 IT 作業人員。 這些設定檔具有已對齊，以支援受益於增強，或安全的工作站，同時可平衡使用性和風險的組織中的一般使用者。 本指南將提供設定接受的業界標準為基礎的組態。 本指南用來說明中強化 Windows 10，並減少使用原則和技術來協助您管理的安全性功能和風險的裝置或使用者入侵的相關風險的方法。
![安全的工作站層級](./media/concept-azure-managed-workstation/seccon-levels.png)

* **低安全性**-受管理的標準工作站提供不錯的起點，大部分的家用和小型企業使用。 這些裝置已註冊的 Azure AD 和 Intune 受管理。 設定檔可讓使用者執行任何應用程式，並瀏覽任何網站。 反惡意程式碼解決方案喜歡[Microsoft Defender](https://www.microsoft.com/windows/comprehensive-security)應該啟用。
* **增強式安全性**– 是一項目層級保護解決方案，好的家庭使用者、 小型企業使用者，以及一般的開發人員。
   * 增強型工作站提供以原則為基礎來增強安全性的低安全性設定檔所表示。 此設定檔可讓您安全的方法，可使用客戶資料，而且能夠使用產能工具，例如檢查電子郵件和網頁瀏覽。 增強型工作站可用來稽核使用者的行為，與工作站的設定檔使用啟用稽核原則和 Intune 的記錄。 在這個設定檔，工作站會啟用安全性控制項，然後原則內容中所述，並部署在強化工作站-windows 10 (1809) 指令碼。 部署也會利用進階的惡意程式碼保護使用[進階威脅防護 (ATP)](https://docs.microsoft.com/office365/securitycompliance/office-365-atp)
* **高安全性**– 來減少工作站的受攻擊面最有效的方式是移除 self-administer 工作站的能力。 移除本機系統管理權限是以增強安全性的步驟，並且會影響產能，如果未正確實作。 高安全性設定檔為基礎的增強式的安全性設定檔，有一個相當大的變更，移除的本機系統管理員。此設定檔被設計來協助與或許例如行政人員高設定檔使用者或使用者可能連絡敏感性資料，例如薪資、 或服務，以及處理程序的核准的使用者。
   * 高安全性的使用者設定檔要求更高版本的受控制的環境，同時仍然能夠執行他們的產能活動，例如郵件和網頁瀏覽功能，同時維持簡單的使用體驗。 使用者會預期功能，例如 cookie、 我的最愛，以及運作所能使用其他快速鍵。 不過這些使用者可能不需要修改，或偵錯他們的裝置的能力，而且不需要安裝驅動程式。 使用高安全性-windows 10 (1809) 指令碼部署的高安全性設定檔。
* **特製化**– 開發人員和 IT 系統管理員會引來攻擊者，因為這些角色可以改變給攻擊者感興趣的系統。 特製化的工作站會部署在高安全性的工作站和它所管理的本機應用程式、 限制網際網路的網站，以及限制很高的生產力功能的安全性風險，例如 ActiveX，進一步 emphases 投入時間Java、 瀏覽器外掛程式和 Windows 裝置上的數個其他已知的高風險控制項。 在這個設定檔，工作站會啟用安全性控制項，然後原則內容中所述，並部署在 DeviceConfiguration_NCSC-windows 10 (1803) SecurityBaseline 指令碼。
* **保護**– 攻擊者可能會危害系統管理帳戶通常會導致資料遭竊、 資料變更，或服務中斷的重大商務損毀。 在這個強化後的狀態，工作站會啟用所有的安全性控制和限制直接控制的本機應用程式管理原則並在移除產能工具。 如此一來，社交媒體會封鎖其反映最常用的方法能順利進行網路釣魚攻擊和危害裝置會為郵件更困難。  可以使用安全的工作站-windows 10 (1809) SecurityBaseline 指令碼部署安全的工作站。

   ![安全的工作站](./media/concept-azure-managed-workstation/secure-workstation.png)

   安全的工作站提供系統管理員具有清楚的應用程式的控制，以及應用程式防護的強化後的工作站。 工作站將使用的認證、 裝置及惡意探索防護來防範惡意行為的主機。 此外所有本機的磁碟會加密與 Bitlocker 加密。

* **隔離**– 此自訂的離線表示極端 （沒有安裝指令碼會在此情況下提供） 的範圍結尾。 組織可能需要管理隔離的商務關鍵需要函式如高價值產品線或生命週期支援系統不支援未/執行補充程式的舊版作業系統。 因為安全性至關重要，且雲端服務無法使用，組織可能會以手動方式管理/更新這些電腦或是使用隔離的 Active Directory 樹系架構 (例如增強的安全性管理環境 (ESAE)) 來管理它們。 在這些情況下，移除所有的存取權，除了基本的 Intune 和 ATP 應該視為健康情況檢查。
   * [Intune 的網路通訊需求](https://docs.microsoft.com/intune/network-bandwidth-use)
   * [ATP 網路通訊需求](https://docs.microsoft.com/azure-advanced-threat-protection/configure-proxy)

## <a name="next-steps"></a>後續步驟

[部署安全的 Azure 管理工作站](howto-azure-managed-workstation.md)
