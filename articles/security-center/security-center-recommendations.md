---
title: 管理 Azure 資訊安全中心的安全性建議 | Microsoft Docs
description: 本文件將逐步引導您了解「Azure 資訊安全中心」的建議如何協助您保護 Azure 資源及遵守安全性原則。
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/13/2018
ms.author: rkarlin
ms.openlocfilehash: a5ca8efd43244825eef412cf3c05c8df20094112
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2019
ms.locfileid: "56109534"
---
# <a name="managing-security-recommendations-in-azure-security-center"></a>管理 Azure 資訊安全中心的安全性建議
本文件將逐步引導您了解如何使用「Azure 資訊安全中心」的建議來協助您保護 Azure 資源。

> [!NOTE]
> 本文件將使用範例部署來介紹服務。  本文件不是一份逐步解說指南。
>
>

## <a name="what-are-security-recommendations"></a>有哪些安全性建議？
資訊安全中心會定期分析 Azure 資源的安全性狀態。 當資訊安全中心識別潛在的安全性弱點時，它會建立建議。 這些建議會引導您完成設定所需控制項的程序。

## <a name="implementing-security-recommendations"></a>實作安全性建議
### <a name="set-recommendations"></a>設定建議
在 [在 Azure 資訊安全中心設定安全性原則](tutorial-security-policy.md)中，您將了解如何：

* 設定安全性原則。
* 開啟資料收集。
* 選擇要在您的安全性原則中看見的建議。

目前的原則建議是以系統更新、基準規則、反惡意程式碼程式、子網路與網路介面上的[網路安全性群組](../virtual-network/security-overview.md)、SQL Database 稽核、SQL Database 透明資料加密及 Web 應用程式防火牆為中心。  [設定安全性原則](tutorial-security-policy.md) 提供每個建議選項的描述。

### <a name="monitor-recommendations"></a>監視建議
設定安全性原則之後，「資訊安全中心」會分析您資源的安全性狀態，以識別潛在的弱點。 [概觀] 下方的 [建議] 圖格可讓您知道資訊安全中心識別出的建議總數量。

![建議圖格][1]

若要查看每個建議的詳細資訊，請選取 [概觀] 下方的 [建議] 圖格。 [建議] 隨即開啟。

![篩選建議][2]

您可以篩選建議。 若要篩選建議，請選取 [建議] 刀鋒視窗上的 [篩選]。 即會開啟 [篩選]  刀鋒視窗，您可以選取想要查看的嚴重性和狀態值。

系統會以表格格式顯示建議，其中每一行代表一個特定的建議。 這個表格的資料行包括：

* **描述**：說明建議及要如何處理它。
* **資源**：列出這個建議適用的資源。
* **狀態**：描述建議的目前狀態：
  * **未處理**：建議尚未處理。
  * **進行中**：正在將建議套用到資源，且您不需要採取任何動作。
  * **已解決**：建議已經完成 (在此情況下，該行會呈現灰色)。
* **嚴重性**：描述該特定建議的嚴重性：
  * **高**：某個有意義的資源 (例如應用程式、VM 或網路安全性群組) 有弱點存在，並且需要注意。
  * **中**：有弱點存在，需要非關鍵性步驟或其他步驟才能將其消除或完成程序。
  * **低**：應該處理但不需要立即注意的弱點存在。 (預設不會顯示嚴重性低的建議，但是如果您要查看它們，則可以篩選嚴重性低的建議。)

請使用下表做為參考，以協助您了解可用的建議，以及如果套用建議，每一個建議所產生的作用。

> [!NOTE]
> 您會想要了解 Azure 資源的 [傳統和 Resource Manager 部署模型](../azure-classic-rm.md) 。
>
>
### <a name="apply-recommendations"></a>套用建議
檢視所有建議之後，請決定應該先套用哪一個建議。 建議您使用嚴重性分級做為主要參數，以評估應該先套用哪些建議。



## <a name="next-steps"></a>後續步驟
在本文件中，已向您介紹「資訊安全中心」的安全性建議。 如要深入了解資訊安全中心，請參閱下列主題：

* [在 Azure 資訊安全中心設定安全性原則](tutorial-security-policy.md) — 了解如何為您的 Azure 訂用帳戶及資源群組設定安全性原則。
* [Azure 資訊安全中心的安全性健全狀況監視](security-center-monitoring.md) — 了解如何監視 Azure 資源的健全狀況。
* [管理與回應 Azure 資訊安全中心的安全性警示](security-center-managing-and-responding-alerts.md) — 了解如何管理與回應安全性警示。
* [使用 Azure 資訊安全中心監視合作夥伴解決方案](security-center-partner-solutions.md) — 了解如何監視合作夥伴解決方案的健全狀況。
* [Azure 資訊安全中心常見問題集](security-center-faq.md) — 尋找有關使用服務的常見問題。
* [Azure 安全性部落格](https://blogs.msdn.com/b/azuresecurity/) — 尋找有關 Azure 安全性與相容性的部落格文章。

<!--Image references-->
[1]: ./media/security-center-recommendations/recommendations-tile.png
[2]: ./media/security-center-recommendations/filter-recommendations.png
