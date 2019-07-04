---
title: 了解安全、 受 Azure 管理工作站-Azure Active Directory
description: 深入了解安全、 受 Azure 管理的工作站，並了解為什麼這是很重要。
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
ms.openlocfilehash: 02a6ddef294c4872f2d7e50e8940ecbb4b4b7bc4
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491601"
---
# <a name="understand-secure-azure-managed-workstations"></a>了解安全、 受 Azure 管理的工作站

安全且獨立的工作站是至關重要的機密系統管理員、 開發人員等重要服務操作員的角色安全性。 如果用戶端工作站的安全性遭到入侵，許多安全性控制和保證可以失敗或無效。

本文件說明您需要建置安全的工作站，通常也稱為特殊權限的存取工作站 (PAW)。 該文章也包含詳細的指示，來設定初始的安全性控制。 本指南說明如何以雲端為基礎的技術可以管理服務。 它需倚賴 Windows 10RS5、 Microsoft Defender 進階威脅防護 (ATP)、 Azure Active Directory 和 Intune 中導入的安全性功能。

> [!NOTE]
> 這篇文章說明安全的工作站和其重要性的概念。 如果您已熟悉的概念，並想要跳至部署，請瀏覽[部署安全的工作站](https://docs.microsoft.com/azure/active-directory/devices/howto-azure-managed-workstation)。

## <a name="why-secure-workstation-access-is-important"></a>安全的工作站存取為何如此重要

快速採用雲端服務，並且能夠從任何地方工作已建立新的惡意探索方法。 利用弱式安全性系統管理員的工作所在的裝置上的控制項，攻擊者能夠取得特殊權限資源的存取權。

特殊權限的濫用和供應鏈攻擊是攻擊者使用違反組織的前五個方法。 它們也是第二個最常在事件報告在 2018 年根據偵測到策略[Verizon 威脅報告](https://enterprise.verizon.com/resources/reports/dbir/)，而[Security Intelligence Report](https://aka.ms/sir)。

大部分的攻擊者請遵循下列步驟：

1. 若要尋找的方法，通常的產業特定的探查。
1. 收集資訊，並識別滲透會被視為較低的值在工作站的最佳方式的分析。
1. 若要尋找的方法來移動的持續性[橫向](https://en.wikipedia.org/wiki/Network_Lateral_Movement)。
1. 資料外洩的機密和敏感。

在探查，攻擊者經常滲透裝置看起來很低風險或低估。 尋找橫向移動的機會，以及尋找系統管理使用者與裝置，他們會使用這些易受攻擊的裝置。 它們存取特殊權限的使用者角色之後，攻擊者識別高價值的資料和成功竊取資料。

![一般的入侵模式](./media/concept-azure-managed-workstation/typical-timeline.png)

本文件說明解決方案，可協助保護您的運算裝置，從這類橫向攻擊。 解決方案會隔離管理和服務，從較不重要的產能裝置中斷鏈結之前可以滲入具有機密的雲端資源的存取權的裝置。 解決方案會使用原生的 Azure 服務，Microsoft 365 企業版堆疊的一部分：

* Intune 裝置管理和安全的應用程式和 Url 清單
* Autopilot 裝置安裝程序、 部署和重新整理
* Azure AD 進行使用者管理、 條件式存取，以及多重要素驗證
* Windows 10 裝置健康情況證明和使用者體驗 （目前的版本）
* Defender ATP 的雲端管理端點保護、 偵測及回應
* Azure AD PIM 來管理授權和在 just-in-time (JIT) 特殊權限資源的存取權

## <a name="who-benefits-from-a-secure-workstation"></a>人員受益於安全的工作站？

使用安全的工作站時，可有助於所有使用者與運算子。 攻擊者危害電腦或裝置可以模擬所有快取的帳戶。 當登入裝置，他們也可能會使用認證和權杖。 這項風險使其重要用於特殊權限的角色，包括系統管理權限的保護裝置。 具有特殊權限帳戶的裝置是橫向移動和權限提升攻擊目標。 這些帳戶可以用於各種不同的資產例如：

* 在內部部署或雲端式系統的系統管理員
* 關鍵系統的開發人員工作站
* 社交媒體帳戶具有高風險的系統管理員
* 高度機密的工作站上，例如終端機 SWIFT 付款
* 工作站處理營業祕密

為了降低風險，您應該實作提升權限的安全性控制，特殊權限的工作站，請使用這些帳戶。 如需詳細資訊，請參閱 < [Azure Active Directory 功能部署指南](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-checklist-p2)， [Office 365 藍圖](https://aka.ms/o365secroadmap)，並[保護特殊權限存取計劃](https://aka.ms/sparoadmap))。

## <a name="why-use-dedicated-workstations"></a>為何要使用專用的工作站？

雖然您可以將安全性新增至現有的裝置，最好是開始使用安全的基礎。 若要使您的組織中維持高安全性層級的最佳位置，開始使用您知道是安全的裝置並實作一組已知的安全性控制項。

有愈來愈多的攻擊媒介，透過電子郵件和網頁瀏覽可讓您愈來愈難確定裝置可以信任。 本指南假設專用的工作站是標準的產能、 瀏覽，以及電子郵件與隔離。 移除的生產力、 網頁瀏覽，以及從裝置的電子郵件會對產能的負面影響。 不過，這個保護措施是通常可接受的案例，其中 「 作業 」 工作不明確需要它，在安全性事件的風險很高。

> [!NOTE]
> 網頁瀏覽這裡指的是任意的網站都可以是高風險活動的一般存取。 這類瀏覽是使用網頁瀏覽器存取少量的已知的系統管理網站，例如 Azure、 Office 365、 其他雲端提供者和 SaaS 應用程式的服務完全不同。

內含項目策略會加強安全性，藉由增加的數目和類型的控制項，使其無法存取機密資產遏止攻擊者。 這篇文章中所述的模型使用分層式權限設計，並限制到特定裝置的系統管理權限。

## <a name="supply-chain-management"></a>供應鏈管理

務必在安全的工作站是信任的供應鏈解決方案，您可在其中使用受信任的工作站稱為 '的根'。 此解決方案中，使用信任的根[Microsoft Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-autopilot)技術。 若要保護的工作站，Autopilot 能讓您運用 Microsoft OEM 最佳化 Windows 10 裝置。 這些裝置製造商有已知的良好狀態。 而不是重新安裝映像可能不安全的裝置，自動駕駛可以將 Windows 裝置轉換成 「 商務就緒 」 狀態。 適用於設定和原則、 安裝應用程式，以及甚至變更的 Windows 10 版本。 比方說，Autopilot 可能會變更裝置的 Windows 安裝 Windows 10 專業版到 Windows 10 企業版，讓它可以使用進階的功能。

![安全的工作站層級](./media/concept-azure-managed-workstation/supplychain.png)

## <a name="device-roles-and-profiles"></a>裝置角色和設定檔

本指南參考數個安全性設定檔和角色，可協助您建立更安全的解決方案，使用者、 開發人員及 IT 人員。 可用性和一般使用者可以受益於增強或安全的工作站的風險，這些設定檔之間取得平衡。 此處提供的組態會根據已接受的業界標準的設定。 本指南示範如何強化 Windows 10，並降低相關聯的裝置或使用者入侵的風險。 做法是使用原則和技術來協助您管理的安全性功能和風險。
![安全的工作站層級](./media/concept-azure-managed-workstation/seccon-levels.png)

* **低安全性**-受管理的標準工作站提供不錯的起點，大部分的家用和小型企業使用。 這些裝置會在 Azure AD 中註冊，並使用 Intune 進行管理。 此設定檔可讓使用者執行任何應用程式，並瀏覽任何網站。 反惡意程式碼解決方案喜歡[Microsoft Defender](https://www.microsoft.com/windows/comprehensive-security)應該啟用。

* **增強式安全性**– 此入門等級、 受保護的解決方案適用於家庭使用者、 小型企業使用者，以及一般的開發人員。

   增強的工作站是這個以原則為基礎的方式，可增加的低安全性設定檔的安全性。 它提供安全的方法，可使用客戶資料，也可以使用產能工具，例如電子郵件和網頁瀏覽。 您可以使用稽核原則和 Intune 來監視使用者的行為和設定檔的使用方式的增強型的工作站。 您部署 windows 10 (1809) 指令碼的增強型的工作站設定檔，而且它會利用進階的惡意程式碼保護使用[進階威脅防護 (ATP)](https://docs.microsoft.com/office365/securitycompliance/office-365-atp)。

* **高安全性**– 來減少工作站的受攻擊面最有效的方式是移除 self-administer 工作站的能力。 移除本機系統管理權限的步驟，可改善安全性，但如果未正確實作，它會影響產能。 有一個相當大的增強式的安全性設定檔為基礎的高安全性設定檔： 移除的本機系統管理員。此設定檔專為高設定檔的使用者： 主管、 薪資和敏感性資料使用者、 服務和處理序的核准者。

   高安全性使用者要求更受控制的環境，同時仍然能夠執行活動，例如電子郵件和網頁瀏覽簡單易用的經驗。 使用者會預期功能，例如 cookie、 我的最愛，以及其他快速鍵運作。 不過，這些使用者可能不需要修改，或偵錯他們的裝置的能力。 它們也不需要安裝驅動程式。 使用高安全性-windows 10 (1809) 指令碼部署的高安全性設定檔。

* **特製化**– 攻擊者會以開發人員和 IT 系統管理員因為它們可以改變給攻擊者感興趣的系統。 特製化的工作站擴充的高安全性的工作站的原則所管理的本機應用程式和限制的網站。 它也會限制高風險的生產力功能，例如 ActiveX、 Java、 瀏覽器外掛程式和其他 Windows 控制項。 您部署 DeviceConfiguration_NCSC-windows 10 (1803) SecurityBaseline 指令碼使用此設定檔。

* **保護**– 攻擊者危害系統管理帳戶可能會導致資料遭竊、 資料變更，或服務中斷的重大商務損毀。 在這個強化後的狀態，所有的安全性控制和限制直接控制的本機應用程式管理原則，可讓工作站。 安全的工作站中的任何產能工具因此更難危害的裝置。 它會封鎖網路釣魚攻擊的最常見的向量： 電子郵件及社交媒體。  可以使用安全的工作站-windows 10 (1809) SecurityBaseline 指令碼部署安全的工作站。

   ![安全的工作站](./media/concept-azure-managed-workstation/secure-workstation.png)

   已清除的應用程式控制和應用程式防護的強化後工作站為系統管理員提供安全的工作站。 工作站使用 credential guard，device guard 和惡意探索防護來防範惡意行為的主機。 所有的本機磁碟也會使用 BitLocker 加密。

* **隔離**– 此自訂的離線案例中代表極端極端情況下的結束。 在此情況下不提供任何安裝指令碼。 您可能需要管理需要不受支援或未更新舊版作業系統的商務關鍵性函式。 例如，高價值產品線或生命週期-支援系統。 因為安全性至關重要，且雲端服務無法使用，您可以管理，並更新這些電腦，以手動方式或使用獨立的 Active Directory 樹系架構等增強式安全性管理環境 (ESAE)。 在這些情況下，請考慮移除所有的存取權，除了基本的 Intune 和 ATP 健康情況檢查。

  * [Intune 的網路通訊需求](https://docs.microsoft.com/intune/network-bandwidth-use)
  * [ATP 網路通訊需求](https://docs.microsoft.com/azure-advanced-threat-protection/configure-proxy)

## <a name="next-steps"></a>後續步驟

[部署安全的 Azure 管理工作站](howto-azure-managed-workstation.md)。
