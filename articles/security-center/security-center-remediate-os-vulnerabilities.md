---
title: "修復 Azure 資訊安全中心中的安全性設定 |Microsoft 文件"
description: "本文件將示範如何實作 Azure 資訊安全中心建議**修復安全性設定**。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 991d41f5-1d17-468d-a66d-83ec1308ab79
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2018
ms.author: terrylan
ms.openlocfilehash: 412234b1486fa15cbc399bcf43be8ce90aac252a
ms.sourcegitcommit: 719dd33d18cc25c719572cd67e4e6bce29b1d6e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2018
---
# <a name="remediate-security-configurations-in-azure-security-center"></a>修復 Azure 資訊安全中心中的安全性設定
Azure 資訊安全中心會針對可能讓虛擬機器 (VM) 和電腦更容易遭受攻擊的設定，每天分析虛擬機器和電腦的作業系統。 資訊安全中心建議您解決弱點，當作業系統設定不符合建議的安全性設定規則，並建議解決這些弱點的組態變更。

如需受監視之特定設定的詳細資訊，請參閱[建議的設定規則清單](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335)。 請參閱[自訂作業系統安全性組態](security-center-customize-os-security-config.md)以了解如何自訂在資訊安全中心的安全性組態評估。

## <a name="implement-the-recommendation"></a>實作建議
請修正的安全性組態不相符會以資訊安全中心中的建議。 此建議將顯示在[建議] 和 [計算] 下方。

在此範例中，我們將探討**修復安全性設定**建議下的**計算**。
1. 在 [資訊安全中心] 主功能表下選取 [計算]。

   ![修復安全性設定][1]

2. 在下**計算**，選取**修復安全性設定**。 **安全性組態**隨即開啟。

   ![安全性組態][2]

  儀表板的頂端提供：

  - 規則總數，依作業系統設定在虛擬機器和電腦上發生故障的嚴重性。
  - 規則總數，依作業系統設定在虛擬機器和電腦上發生故障的類型。
  - 您的 Windows OS 設定和 Linux OS 設定失敗的規則總數。

  儀表板底部會列出虛擬機器和電腦之間所有失敗的規則，以及缺少更新的嚴重性。 此清單包括：

  - **CCEID**：規則的 CCE 唯一識別碼。 資訊安全中心會使用一般設定列舉 (CCE) 指派設定規則的唯一識別碼。
  - **名稱**：規則的名稱
  - **規則類型**：登錄機碼、安全性原則或稽核原則
  - **虛擬機器和電腦數目**：無法套用規則的虛擬機器和計算機總數
  - **規則嚴重性**：嚴重、重要或警告的 CCE 嚴重性值
  - **狀態**：建議的目前狀態：

    - **未處理**：建議尚未處理
    - **進行中**：正在將建議套用到資源，且您不需要採取任何動作
    - **已解決**︰建議動作已完成  (若問題已解決，該項目會呈現暗灰色)

3. 選取清單中失敗的規則以檢視詳細資料。

   ![失敗的設定規則][3]

  此刀鋒視窗上提供下列資訊︰

  - 名稱 -- 規則的名稱
  - CCIED -- 規則的 CCE 唯一識別碼
  - 作業系統版本 - 虛擬機器或電腦的作業系統版本
  - 規則嚴重性 -- 嚴重、重要或警告的 CCE 嚴重性值
  - 完整說明 -- 規則的說明
  - 弱點 -- 不套用規則的弱點或風險說明
  - 潛在影響 -- 套用規則時的業務影響
  - 因應對策 – 補救步驟
  - 預期值 -- 資訊安全中心對照規則分析 VM OS 設定時的預期值
  - 實際值 -- 對照規則分析 VM OS 設定時的傳回值
  - 規則作業 -- 資訊安全中心對照規則分析 VM OS 設定時使用的規則作業

4. 選取頂端功能區中的 [搜尋] 圖示。 搜尋會開啟清單工作區的 Vm 和電腦，而選取的安全性設定不相符。 只有在已選取適用於連線至不同工作區之多個虛擬機器的規則時，才會顯示 [工作區選擇] 刀鋒視窗。

  ![列出的工作區][4]

5. 選取工作區。 記錄分析搜尋查詢會開啟篩選至工作區的安全性設定不相符。

  ![具有作業系統漏洞的工作區][5]

6. 從清單中選取電腦以了解更多資訊。 會出現僅針對該電腦篩選出的其他搜尋結果。

  ![篩選該電腦][6]

## <a name="next-steps"></a>後續步驟
這篇文章會示範如何實作資訊安全中心建議 < 補救安全性組態 >。 請參閱[自訂作業系統安全性組態](security-center-customize-os-security-config.md)以了解如何自訂在資訊安全中心的安全性組態評估。

您可在[這裡](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335)檢閱設定規則集。 資訊安全中心會使用 CCE (一般設定列舉) 指派設定規則的唯一識別碼。 如需詳細資訊，請造訪 [CCE](https://nvd.nist.gov/cce/index.cfm) 網站。

若要深入了解資訊安全中心，請參閱下列資源：

* [Azure 資訊安全中心支援的平台](security-center-os-coverage.md) - 提供支援的 Windows 與 Linux VM 清單。
* [在 Azure 資訊安全中心設定安全性原則](security-center-policies.md) - 了解如何為您的 Azure 訂用帳戶及資源群組設定安全性原則。
* [在 Azure 資訊安全中心管理安全性建議](security-center-recommendations.md) - 了解建議如何協助您保護您的 Azure 資源。
* [Azure 資訊安全中心的安全性健全狀況監視](security-center-monitoring.md) - 了解如何監視 Azure 資源的健全狀況。
* [在 Azure 資訊安全中心管理與回應安全性警示](security-center-managing-and-responding-alerts.md) - 了解如何管理與回應安全性警示。
* [使用 Azure 資訊安全中心監視合作夥伴解決方案](security-center-partner-solutions.md) - 了解如何監視合作夥伴解決方案的健康情況狀態。
* [Azure 資訊安全中心常見問題集](security-center-faq.md) - 尋找有關使用服務的常見問題。
* [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/) - 尋找有關 Azure 安全性與合規性的部落格文章。

<!--Image references-->
[1]: ./media/security-center-remediate-os-vulnerabilities/compute-blade.png
[2]:./media/security-center-remediate-os-vulnerabilities/os-vulnerabilities.png
[3]: ./media/security-center-remediate-os-vulnerabilities/vulnerability-details.png
[4]: ./media/security-center-remediate-os-vulnerabilities/search.png
[5]: ./media/security-center-remediate-os-vulnerabilities/log-search.png
[6]: ./media/security-center-remediate-os-vulnerabilities/search-results.png
