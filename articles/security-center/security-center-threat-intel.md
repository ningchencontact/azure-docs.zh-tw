---
title: Azure 資訊安全中心內的威脅情報和安全性警訊地圖 | Microsoft Docs
description: 了解如何使用「Azure 資訊安全中心」內的安全性警訊地圖和威脅情報功能，來識別您 VM 和電腦中的潛在威脅。
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: a771a3a1-2925-46ca-8e27-6f6a0746f58b
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/3/2018
ms.author: rkarlin
ms.openlocfilehash: 36201bad64e5516375afe1ec9ce141c3fd311d48
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "64574338"
---
# <a name="security-alerts-map-and-threat-intelligence"></a>安全性警示地圖和威脅情報
本文可協助您使用的 Azure 資訊安全中心安全性警訊地圖和以安全性事件為基礎的威脅情報地圖，以解決安全性相關問題。

> [!NOTE]
> 安全性*事件*引導模式 按鈕將於 2019 年 7 月 31 日淘汰。 如需詳細資訊和替代的服務，請參閱 <<c0> [ 停用的資訊安全中心功能 (7 月 2019)](security-center-features-retirement-july2019.md#menu_securityeventsmap)。


## <a name="how-the-security-alerts-map-works"></a>安全性警訊地圖地圖的運作方式
資訊安全中心會提供地圖，協助您識別環境的安全性威脅。 例如，您可以識別特定的電腦是否屬於殭屍網路，以及威脅來自何處。 攻擊者違法安裝惡意程式碼，暗中操作命令和控制項來管理殭屍網路時，則電腦會成為殭屍網路中的節點。 

為了建置此地圖，資訊安全中心會使用來自 Microsoft 內多個來源的資料。 資訊安全中心將會使用這項資料來對應您環境的潛在威脅。 

[安全性事件回應程序](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide#incident-response)的其中一個步驟是識別危害系統的嚴重性。 在這個階段中，您應該執行下列工作︰

- 判斷攻擊本質。
- 判斷攻擊起源點。
- 判斷攻擊意圖。 在組織中指示還是隨機指示攻擊取得特定資訊？
- 識別已遭入侵的系統。
- 識別已存取的檔案，並判斷這些檔案的敏感度。

您可以使用資訊安全中心的安全性警訊地圖來協助進行這些工作。

## <a name="access-the-security-alerts-map"></a>存取安全性警訊地圖
若要以視覺化方式檢視目前在環境中的威脅，請開啟安全性警訊地圖：

1. 開啟 [資訊安全中心]  儀表板。
2. 在左窗格中的 [威脅防護]  下，選取 [安全性警訊地圖]  。 地圖隨即開啟。
3. 若要取得警訊的詳細資訊，並獲得補救步驟，請按一下地圖上的警示點，並遵循指示進行操作。 
 
安全性警訊地圖是以警示為基礎。 這些警示所依據的網路通訊活動取決於成功解析的 IP 位址、IP 位址是否為已知有風險的 IP 位址 (例如，已知的 cryptominer) 或先前未辨識為有風險的 IP 位址。 地圖會提供您先前在 Azure 中選取任何訂用帳戶之間的警示。 

在地圖上的警示會根據偵測為來源的地理位置顯示，並且以色彩區分嚴重性。 
    ![威脅情報資訊](./media/security-center-threat-intel/security-center-alert-map.png)

## <a name="viewing-the-event-based-threat-intelligence-dashboard"></a>檢視以事件為基礎的威脅情報儀表板
若要檢視原始安全性事件為基礎的威脅情報地圖，您可以遵循此程序。 此地圖會顯示 IP 位址被視為風險的事件，例如已知殭屍網路的 IP 位址。

1. 開啟 [資訊安全中心]  儀表板。

1. 在左窗格中的 [威脅防護]  下，選取 [安全性警訊地圖]  。 地圖隨即開啟。
2. 在右上角，按一下**移至安全性事件地圖**。
3. 選取要檢視儀表板的工作區。
4. 在對應的頂端，選取 [檢視傳統威脅情報]  。 [威脅情報]  儀表板隨即開啟。

   > [!NOTE]
   > 如果最右側的資料行顯示 [升級計劃]  ，則此工作區是使用免費訂用帳戶。 升級至標準以使用此功能。 如果最右側資料行顯示**需要更新**，更新[Azure 監視器記錄](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)使用這項功能。 如需有關定價方案的詳細資訊，請閱讀 Azure 資訊安全中心定價。
   >
5. 如果您有多個工作區要調查，則根據 [惡意 IP]  資料行排定調查的優先順序。 它會顯示此工作區中的目前惡意 IP 數目。 選取您需要使用的工作區，[威脅情報]  儀表板隨即出現。

    ![威脅情報資訊](./media/security-center-threat-intel/security-center-threat-intel-fig5.png)

6. 此儀表板會分成四個圖格︰

    a.  **威脅類型**。 摘要說明選取工作區中偵測到的威脅類型。

    b.  **來源國家/地區**。 根據流量的來源位置來彙總流量。

    c.  **威脅位置**。 協助您識別在世界各地與您環境通訊的目前位置。 在顯示的對應中，橙色 (傳入) 和紅色 (傳出) 箭號可以識別流量方法。 如果您選取其中一個箭號，威脅類型和流量方向隨即顯示。

    d.  **威脅詳細資料**。 顯示您在對應中選取的威脅詳細資料。

不論您選擇哪個選項圖格，顯示的儀表板都是根據記錄搜尋查詢。 唯一的差別是查詢類型和結果。

### <a name="threat-types"></a>威脅類型
選取 [威脅類型]  圖格以開啟 [記錄搜尋]  儀表板。 篩選選項會出現在左邊，查詢結果會出現在右邊。

![記錄搜尋](./media/security-center-threat-intel/security-center-threat-intel-fig3.png)

查詢結果會依據名稱來顯示威脅。 您可以使用左窗格，選取您想要篩選的屬性。 例如，若只要查看目前連線到機器的威脅，在 [SESSIONSTATE]  中選取 [已連線]   >  [套用]  。

![工作階段狀態](./media/security-center-threat-intel/security-center-threat-intel-fig4.png)

針對 Azure VM，只有流經代理程式的網路資料會顯示在 [威脅情報]  儀表板中。 威脅情報也會使用下列資料類型：

- CEF 資料 (Type=CommonSecurityLog)
- WireData (Type= WireData)
- IIS 記錄 (Type=W3CIISLog)
- Windows 防火牆 (Type=WindowsFirewall)
- DNS 事件 (Type=DnsEvents)


## <a name="see-also"></a>另請參閱
在這篇文章中，您已學會如何使用資訊安全中心的威脅情報，可協助您識別可疑的活動。 如要深入了解資訊安全中心，請參閱下列文章：

* [在 Azure 資訊安全中心管理和回應安全性警示](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)。 了解如何在資訊安全中心管理警示，以及回應安全性事件。
* [Azure 資訊安全中心的安全性健全狀況監視](security-center-monitoring.md)。 了解如何監視 Azure 資源的健全狀況。
* [了解 Azure 資訊安全中心的安全性警示](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)。 了解不同類型的安全性警示。
* 《[Azure 資訊安全中心疑難排解指南](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide)》。 了解如何針對資訊安全中心的常見問題進行疑難排解。
* [Azure 資訊安全中心常見問題集](security-center-faq.md)。 尋找有關使用服務常見問題的答案。
* [Azure 安全性部落格](https://blogs.msdn.com/b/azuresecurity/)。 尋找有關 Azure 安全性與相容性的部落格文章。
