---
title: Azure 資訊安全中心安全性原則與 Azure 原則的整合 | Microsoft Docs
description: 本文件可協助您設定 Azure 資訊安全中心安全性原則與 Azure 原則的整合。
services: security-center
documentationcenter: na
author: TerryLanfear
manager: mbaldwin
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/21/2018
ms.author: terrylan
ms.openlocfilehash: b3d6d15d41fece613290deb2c77e980caa5dcfef
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/27/2018
ms.locfileid: "37018558"
---
# <a name="integrate-security-center-security-policies-with-azure-policy"></a>整合資訊安全中心安全性原則與 Azure 原則
本文件可協助您設定 Azure 資訊安全中心安全性原則 (採用 [Azure 原則](../azure-policy/azure-policy-introduction.md)技術)。

## <a name="how-security-policies-work"></a>安全性原則的運作方式
資訊安全中心會為每個 Azure 訂用帳戶自動建立預設安全性原則。 您可以編輯資訊安全中心的原則，或使用 Azure 原則執行下列動作：
- 建立新原則定義。
- 將原則指派給代表整個組織或組織內業務單位的各個管理群組和訂用帳戶。
- 監視原則相容性。

如需 Azure 原則的詳細資訊，請參閱[建立和管理原則來強制執行合規性](../azure-policy/create-manage-policy.md)。

Azure 原則由下列元件組成：

- **原則**是一個規則
- **方案**是一組規則
- **指派**是將方案或原則套用至特定範圍 (管理群組、訂用帳戶或資源群組)

資源會根據指派給它的原則進行評估，並根據資源符合的原則數目來接收合規比率。

## <a name="edit-security-policies"></a>編輯安全性原則
您可以在資訊安全中心內，編輯每個 Azure 訂用帳戶和管理群組的預設安全性原則。 若要修改安全性原則，您必須是該訂用帳戶或所在管理群組的擁有者、參與者或安全性系統管理員。 若要在資訊安全中心檢視安全性原則：

1. 在 [資訊安全中心] 儀表板的 [原則與合規性] 底下，選取 [安全性原則]。 [原則管理] 隨即開啟。

    ![原則管理窗格](./media/security-center-azure-policy/security-center-policies-fig10.png)

  原則管理會顯示管理群組、訂用帳戶和工作區數目，以及您的管理群組結構。

  > [!NOTE]
  > 資訊安全中心儀表板在 [訂用帳戶涵蓋範圍] 之下顯示的訂用帳戶數目，比在 [原則管理] 之下顯示的訂用帳戶數目還要多。 訂用帳戶涵蓋範圍會顯示標準、免費和「未涵蓋」的訂用帳戶數目。 「未涵蓋」訂用帳戶並未啟用資訊安全中心，而且不會顯示在 [原則管理] 之下。
  >
  >

  資料表中的資料行會顯示：

 - 原則方案指派 – 資訊安全中心內建的原則和方案，已指派給訂用帳戶或管理群組。
 - 合規性 - 管理群組、訂用帳戶或工作區的整體合規性分數。 此分數是指派的加權平均值。 單一指派中原則數目的加權平均因子，以及套用指派的資源數目。

 例如，如果訂用帳戶有兩個 VM 以及已指派五個原則的方案，則您的訂用帳戶中有 10 項評估。 如果其中一個 VM 不符合兩個原則，您訂用帳戶指派的整體合規性分數為 80%。

 - 涵蓋範圍 - 識別執行管理群組、訂用帳戶或工作區的定價層 (免費或標準)。  若要深入了解資訊安全中心的定價層，請參閱[價格](security-center-pricing.md)。
 - 設定 – 訂用帳戶具有 [編輯設定] 連結。 選取 [編輯設定] 可讓您更新訂用帳戶設定，例如資料收集、定價層和電子郵件通知。

2. 選取您要啟用安全性原則的訂用帳戶或管理群組。 [安全性原則] 隨即開啟。

3.  在 [安全性原則] 之下，選取您希望資訊安全中心監視的控制項，並藉由選取 [開啟] 提供開啟建議。  如果您不希望資訊安全中心監視該控制項，請選取 [關閉]。

    ![原則元件](./media/security-center-azure-policy/security-policy.png)

4. 選取 [ **儲存**]。

## <a name="available-security-policy-definitions"></a>可用的安全性原則定義

若要了解預設安全性原則中可用的原則定義，請參考下表：

| 原則 | 原則啟用後的功能 |
| --- | --- |
| 系統更新 |從 Windows Update 或 Windows Server Update Services (WSUS) 擷取每天可用的安全性和重大更新清單。 擷取的清單取決於為您虛擬機器設定的服務，並建議要套用的遺漏更新。 對於 Linux 系統，此原則會使用散發套件提供的套件管理系統，來判斷哪些套件有可用的更新。 它也會檢查來自 [Azure 雲端服務](../cloud-services/cloud-services-how-to-configure-portal.md) 虛擬機器的安全性和重大更新。 |
| 安全性設定 |每天分析作業系統組態，以判斷讓虛擬機器容易遭受攻擊的問題。 此原則還會建議可解決這些弱點的組態變更。 如需受監視之特定組態的詳細資訊，請參閱[建議的基準清單](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335)。 (目前，未完全支援 Windows Server 2016)。 |
| 端點保護 |建議為所有 Windows 虛擬機器 (VM) 設定端點保護，以協助識別和移除病毒、間諜軟體及其他惡意軟體。 |
| 磁碟加密 |建立在所有虛擬機器中啟用磁碟加密以增強待用資料保護。 |
| 網路安全性群組 |建議設定 [網路安全性群組](../virtual-network/security-overview.md) 來控制具有公用端點之 VM 的輸入與輸出流量。 除非另有指定，否則所有虛擬機器網路介面都會繼承為子網路設定的網路安全性群組。 除了檢查是否已設定網路安全性群組之外，此原則還會評估輸入安全性規則來識別可允許連入流量的規則。 |
| Web 應用程式防火牆 |建議您，當下列其中一項為真時，在虛擬機器上設定 Web 應用程式防火牆︰ <ul><li>會使用[執行個體層級公開 IP](../virtual-network/virtual-networks-instance-level-public-ip.md)，且會設定相關聯網路安全性群組的輸入安全性規則，以允許存取連接埠 80/443。</li><li>會使用負載平衡 IP，且會設定相關聯的負載平衡和輸入網路位址轉譯 (NAT) 規則，以允許存取連接埠 80/443。 如需詳細資料，請參閱 [Azure Resource Manager 的負載平衡器支援](../load-balancer/load-balancer-arm.md)。</li> |
| 新一代防火牆 |提供超越 Azure 內建網路安全性群組的網路保護。 資訊安全中心會探索建議使用新一代防火牆的部署，然後您就可以設定虛擬設備。 |
| SQL 稽核與威脅偵測 |建議針對法規遵循、進階威脅偵測及調查用途，啟用 Azure 資料庫的存取稽核。 |
| SQL 加密 |建議為您的 Azure SQL Database、關聯的備份及交易記錄檔啟用待用期加密。 您的資料即使遭到入侵，也無法被讀取。 |
| 弱點評估 |建議在 VM 上安裝弱點評估解決方案。 |
| 儲存體加密 |這項功能目前可用於 Azure Blob 儲存體和 Azure 檔案。 啟用儲存體服務加密之後，只會加密新的資料，而此儲存體帳戶中任何現有的檔案都會保持未加密狀態。 |
| JIT 網路存取 |啟用 Just-In-Time 網路存取時，資訊安全中心會建立網路安全性群組規則，藉此鎖定進入 Azure VM 的流量。 系統應會鎖定選取的 VM 連接埠的輸入流量。 如需詳細資訊，請參閱[使用 Just-In-Time 管理虛擬機器存取](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)。 |

## <a name="management-groups"></a>管理群組
如果貴組織有多個訂用帳戶，您可能需要一個方法來有效率地管理這些訂用帳戶的存取、原則和相容性。 Azure 管理群組會提供上述訂用帳戶的範圍層級。 您要將訂用帳戶整理到稱為「管理群組」的容器中，並將治理原則套用至管理群組。 管理群組內的所有訂用帳戶都會自動繼承套用到管理群組的原則。 每個目錄會都會有一個最上層管理群組，名為「根」管理群組。 這個根管理群組會建置於階層內，讓所有的管理群組和訂用帳戶摺疊於其中。 這個根管理群組可讓全域原則和 RBAC 指派在目錄層級套用。 若要設定可搭配 Azure 資訊安全中心使用的管理群組，請遵循[取得 Azure 資訊安全中心的全租用戶可見度](security-center-management-groups.md)一文中的指示。 

> [!NOTE]
> 請務必了解管理群組和訂用帳戶的階層。 若要深入了解管理群組、根管理和管理群組存取，請參閱[使用 Azure 管理群組來組織資源](../azure-resource-manager/management-groups-overview.md#root-management-group-for-each-directory)。
>
>


## <a name="next-steps"></a>後續步驟
在本文中，您了解到如何在資訊安全中心設定安全性原則。 如要深入了解資訊安全中心，請參閱下列文章：

* [Azure 資訊安全中心規劃和操作指南](security-center-planning-and-operations-guide.md)：了解如何規劃及了解 Azure 資訊安全中心的設計考量。
* [Azure 資訊安全中心的安全性健全狀況監視](security-center-monitoring.md)：了解如何監視 Azure 資源的健全狀況。
* [管理與回應 Azure 資訊安全中心的安全性警示](security-center-managing-and-responding-alerts.md)：了解如何管理與回應安全性警示。
* [使用 Azure 資訊安全中心監視合作夥伴解決方案](security-center-partner-solutions.md)：了解如何監視合作夥伴解決方案的健全狀況。
* [取得 Azure 資訊安全中心的全租用戶可見度](security-center-management-groups.md)：了解如何設定 Azure 資訊安全中心的管理群組。 
* [Azure 資訊安全中心常見問題集](security-center-faq.md)：取得使用服務常見問題的解答。
* [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/)：尋找有關 Azure 安全性與相容性的部落格文章。

若要深入了解 Azure 原則，請參閱[什麼是 Azure 原則？](../azure-policy/azure-policy-introduction.md)
