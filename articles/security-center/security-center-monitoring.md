---
title: Azure 資訊安全中心的安全性監視 | Microsoft Docs
description: 本文可協助您開始使用「Azure 資訊安全中心」的監視功能。
services: security-center
documentationcenter: na
author: TerryLanfear
manager: mbaldwin
editor: ''
ms.assetid: 3bd5b122-1695-495f-ad9a-7c2a4cd1c808
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/26/2018
ms.author: terrylan
ms.openlocfilehash: dc362306e20b4d39aa73a552e47cbcbd3037edbd
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063386"
---
# <a name="security-health-monitoring-in-azure-security-center"></a>Azure 資訊安全中心的安全性健康情況監視
本文可協助您使用 Azure 資訊安全中心內的監視功能，監視是否符合原則。

## <a name="what-is-security-health-monitoring"></a>什麼是安全性健康情況監視？
我們通常都將監視想像成監看及等候事件發生，以便對該情況作出反應。 安全性監視是指擁有一個主動式策略來稽核您的資源，以識別出不符合組織標準或最佳做法的系統。

## <a name="monitoring-security-health"></a>監視安全性健全狀況
在您為訂用帳戶的資源啟用[安全性原則](security-center-policies.md)之後，資訊安全中心會分析您資源的安全性狀態，以找出潛在的弱點。 您可以立即取得網路組態的相關資訊。 視您已安裝代理程式的 VM 和電腦數目而定，可能需要一個小時以上的時間才能收集有關 VM 和電腦的組態資訊，例如安全性更新狀態和作業系統組態。 您可以在 [防護] 刀鋒視窗中，檢視資源的安全性狀態及任何問題。 您也可以在 [建議] 圖格上檢視這些問題的清單。

如需如何套用建議的詳細資訊，請參閱[實作 Azure 資訊安全中心的安全性建議](security-center-recommendations.md)。

您可在 [資源健康情況監視] 下，監視資源的安全性狀態。 在下列範例中，您可以看到每個資源的圖格 ([計算和應用程式]、[網路]、[資料安全性] 及 [身分識別和存取]) 都有已識別的問題總數。

![資源安全性健康情況圖格](./media/security-center-monitoring/security-center-monitoring-fig1-newUI-2017.png)


### <a name="monitor-compute--apps"></a>監視計算和應用程式
如需詳細資訊，請參閱[在 Azure 資訊安全中心保護機器和應用程式](security-center-virtual-machine-recommendations.md)。

### <a name="monitor-virtual-networks"></a>監視虛擬網路
按一下 [網路] 圖格時，隨即開啟包含更多詳細資料的 [網路] 刀鋒視窗，如下列螢幕擷取畫面所示：

![網路刀鋒視窗](./media/security-center-monitoring/security-center-monitoring-fig9-new3.png)

#### <a name="networking-recommendations"></a>網路功能的建議
和虛擬機器的資源健康情況資訊一樣，您會在上半部看到問題的摘要清單，在下半部看到受監督的網路清單。

網路狀態明細區段會列出潛在的安全性問題，並提供 [建議](security-center-network-recommendations.md)。 可能的問題包括：

* 未安裝新一代防火牆 (NGFW)
* 未啟用子網路上的網路安全性群組
* 未啟用虛擬機器上的網路安全性群組
* 限制透過公用外部端點的外部存取
* 狀況良好的網際網路面向端點

當您按一下建議時，您會看到有關建議的詳細資料，如下列範例所示：

![網路中的建議詳細資料](./media/security-center-monitoring/security-center-monitoring-fig9-ga.png)

在此範例中，[設定子網路的遺失網路安全性群組] 有缺少網路安全性群組保護的子網路和虛擬機器清單。 如果您按一下要對其套用網路安全性群組的子網路，您會看到 [選擇網路安全性群組]。 您可以在這裡為子網路選取最適當的網路安全性群組，也可以建立新的網路安全性群組。

#### <a name="internet-facing-endpoints-section"></a>網際網路面向端點區段
在 [網際網路面向端點] 區段中，您可以看到目前使用網際網路面向端點所設定的虛擬機器和其目前的狀態。

![使用網際網路面向端點所設定的虛擬機器和狀態](./media/security-center-monitoring/security-center-monitoring-fig10-ga.png)

此資料表有代表虛擬機器的端點名稱、網際網路面向的 IP 位址，以及網路安全性群組和 NGFW 目前的嚴重性狀態。 此資料表是依嚴重性排序︰

* 紅色 (在頂端)：高優先順序，應立即處理
* 橘色︰中等優先順序，應儘速處理
* 綠色 (最後一個)︰健康狀態

#### <a name="networking-topology-section"></a>網路拓撲區段
[網路拓撲] 區段具有資源的階層式檢視，如下列螢幕擷取畫面所示：

![網路拓撲區段中資源的階層式檢視](./media/security-center-monitoring/security-center-monitoring-fig121-new4.png)

此資料表是依嚴重性排序 (虛擬機器和子網路)︰

* 紅色 (在頂端)：高優先順序，應立即處理
* 橘色︰中等優先順序，應儘速處理
* 綠色 (最後一個)︰健康狀態

在此拓撲檢視中，第一層有[虛擬網路](../virtual-network/virtual-networks-overview.md)、[虛擬網路閘道](/vpn-gateway/vpn-gateway-site-to-site-create.md)和[虛擬網路 (傳統)](/virtual-network/virtual-networks-create-vnet-classic-pportal.md)。 第二層有子網路，第三層有屬於這些子網路的虛擬機器。 右側資料行有這些資源的網路安全性群組的目前狀態，如以下範例所示：

![網路拓撲區段中網路安全性群組的狀態](./media/security-center-monitoring/security-center-monitoring-fig12-ga.png)

此刀鋒視窗的下半部有此虛擬機器的建議 (類似上面所述)。 您可以按一下建議以深入了解或套用所需的安全性控制或組態。

### <a name="monitor-data-security"></a>監視資料安全性

當您按一下 [防護] 區段中的 [資料安全性] 時，隨即會開啟包含 SQL 和儲存體建議的 [資料資源]。 它也具有資料庫的一般健全狀況狀態 [建議](security-center-sql-service-recommendations.md) 。 如需儲存體加密的詳細資訊，請閱讀[在 Azure 資訊安全中心啟用 Azure 儲存體帳戶的加密](security-center-enable-encryption-for-storage-account.md)。

![資料資源](./media/security-center-monitoring/security-center-monitoring-fig13-newUI-2017.png)

在 [SQL 建議] 底下，您可以按一下任何建議，取得解決問題所需之進一步動作的更多詳細資料。 下列範例顯示已展開的 **SQL Database 上的資料庫稽核和威脅偵測**建議。

![有關 SQL 建議的詳細資料](./media/security-center-monitoring/security-center-monitoring-fig14-ga-new.png)

[在 SQL 資料庫上啟用稽核和威脅偵測] 具有下列資訊：

* SQL 資料庫的清單
* 其所在的伺服器
* 此設定是否繼承自伺服器，或此設定在此資料庫中是否為唯一的相關資訊
* 目前的狀態
* 問題的嚴重性

當您在資料庫上按一下以解決這項建議時，便會開啟 [稽核與威脅的偵測]，如下列畫面所示。

![稽核與威脅偵測](./media/security-center-monitoring/security-center-monitoring-fig15-ga.png)

若要啟用稽核，請選取 [稽核] 選項底下的 [開啟]。

### <a name="monitor-identity--access"></a>監視身分識別和存取

如需詳細資訊，請參閱[在 Azure 資訊安全中心監視身分識別和存取](security-center-identity-access.md)。

## <a name="see-also"></a>另請參閱
在本文中，您已來了解如何使用「Azure 資訊安全中心」的監視功能。 若要深入了解「Azure 資訊安全中心」，請參閱下列主題：

* [在 Azure 資訊安全中心設定安全性原則](security-center-policies.md)：了解如何在 Azure 資訊安全中心設定安全性設定。
* [管理與回應 Azure 資訊安全中心的安全性警示](security-center-managing-and-responding-alerts.md)：了解如何管理與回應安全性警示。
* [使用 Azure 資訊安全中心監視合作夥伴解決方案](security-center-partner-solutions.md)：了解如何監視合作夥伴解決方案的健全狀況。
* [Azure 資訊安全中心常見問題集](security-center-faq.md)：尋找有關使用服務的常見問題。
* [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/)：尋找有關 Azure 安全性與相容性的部落格文章。
