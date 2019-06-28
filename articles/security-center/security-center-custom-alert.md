---
title: Azure 資訊安全中心內的自訂警示規則 | Microsoft Docs
description: 本文件可協助您在 Azure 資訊安全中心內建立自訂警示規則。
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: f335d8c4-0234-4304-b386-6f1ecda07833
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2018
ms.author: rkarlin
ms.openlocfilehash: ab7165c3315e3a53f90900be8eaf1b9c614a2b07
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341133"
---
# <a name="custom-alert-rules-in-azure-security-center-preview"></a>Azure 資訊安全中心內的自訂警示規則 (預覽)
本文件可協助您在 Azure 資訊安全中心內建立自訂警示規則。

> [!NOTE]
> 自訂警示將於 2019 年 6 月 30 日淘汰。

## <a name="retirement-of-custom-alert-rules-in-azure-security-center"></a>在 Azure 資訊安全中心中的停用的自訂警示規則

自訂警示體驗將淘汰 2019 年 6 月 30 日，因為停用的基礎結構為基礎。 在取代之前時間範圍內，使用者將能夠編輯現有的自訂警示規則，但是不能加入新的。 已被取代，下列任何定義的自訂警示不會生效，並不會產生這些自訂警示規則為基礎的安全性警示。
其中一個建議使用者：
- 啟用[Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) ，並使用內建[analytics](https://docs.microsoft.com/azure/sentinel/tutorial-detect-threats)功能來重新建立其警示的規則
- 重新建立其警示與 Azure 監視器記錄警示
                                     
若要保留您現有的警示，並將它們移轉到 Azure 的 Sentinel，請[啟動 Azure Sentinel](https://portal.azure.com/#create/Microsoft.ASI/preview)。 第一個步驟中，選取 [儲存您自訂的警示] 工作區，然後選取 「 分析 」 功能表項目，來設定您的自訂警示規則。 請瀏覽[文件](https://docs.microsoft.com/azure/sentinel/tutorial-detect-threats)如需詳細資訊。

> [!NOTE]
> 使用自訂的警示[搜尋](https://docs.microsoft.com/azure/azure-monitor/log-query/search-queries)或是[聯集](https://docs-analytics-eus.azurewebsites.net/queryLanguage/query_language_unionoperator.html)陳述式查詢不支援 Azure Sentinel。 請執行移轉之前，編輯這些警示。

若要重新建立您使用 Azure 監視器記錄警示的警示，請參閱：[建立、 檢視及管理使用 Azure 監視器的記錄警示](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)如需有關如何建立記錄警示。 針對 Azure 監視器中的記錄警示的一般概觀，請按一下[此處](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log)。

## <a name="what-are-custom-alert-rules-in-security-center"></a>何謂資訊安全中心內的自訂警示規則？

資訊安全中心有一組預先定義的[安全性警示](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)，一旦發生威脅或可疑活動時便會觸發這些警示。 在某些情況下，您可能會想要建立自訂警示以因應您環境的特定需求。

資訊安全中心內的自訂警示規則可讓您根據從環境所收集到的資料，定義新的安全性警示。 您可以建立查詢，並使用這些查詢的結果來作為自訂規則的準則，一旦符合此準則，便執行該項規則。 您可以使用電腦的安全性事件、夥伴的安全性解決方案記錄或透過 API 所擷取的資料，來建立您的自訂查詢。

> [!NOTE]
> 資訊安全中心的[調查功能](security-center-investigation.md)不支援自訂警示。
>
>

## <a name="how-to-create-a-custom-alert-rule-in-security-center"></a>如何在資訊安全中心建立自訂警示規則？

開啟 [資訊安全中心]  儀表板，並遵循下列步驟來建立自訂警示規則：

1.  在左窗格的 [偵測]  下，按一下 [自訂警示規則 (預覽)]  。
2.  在 [資訊安全中心 – 自訂警示規則 (預覽)]  頁面上，按一下 [新增自訂警示規則]  。

    ![自訂警示](./media/security-center-custom-alert/security-center-custom-alert-fig1.png)

3.  隨即會出現內含下列選項的 [建立自訂警示規則] 頁面：

    ![建立](./media/security-center-custom-alert/security-center-custom-alert-fig2.png)

4.  在 [名稱]  欄位中，輸入此自訂規則的名稱。
5.  在 [描述]  欄位中，輸入簡短描述以反映此規則的目的。
6.  在 [嚴重性]  欄位中，根據您的需求選取嚴重性層級 ([高]、[中]、[低])。
7.  在 [訂用帳戶]  欄位中，選取要適用此規則的訂用帳戶。
8.  在 [工作區]  欄位中，選取您想要使用此規則監視的工作區，然後在 [搜尋查詢]  欄位中，選取要用來取得結果的查詢。

    > [!NOTE]
    > 在您選取用來儲存自訂警示的工作區中，需要這個工作區的寫入權限。
    >
    >

    查詢的結果會觸發警示。 請注意，當您輸入的查詢有效時，此欄位的右上角會出現綠色核取記號：

    ![查詢](./media/security-center-custom-alert/security-center-custom-alert-fig3.png)

10. 在 [期間]  欄位中，選取要執行上述查詢的時間範圍。 請注意，此欄位底部的搜尋結果會根據您所選取的時間範圍而有所變更。

    ![期間](./media/security-center-custom-alert/security-center-custom-alert-fig4.png)

11. 在 [評估]  欄位中，選取此規則應有的評估和執行頻率。
12. 在 [結果數目]  欄位中，選取運算子 (大於或小於)。
13. 在 [閾值]  欄位中，輸入要供先前所選取之運算子參考的數字。
14. 如果您想要設定資訊安全中心在傳送此規則的另一個警示前所應該等候的時間，請啟用 [隱藏警示]  選項。
15. 按一下 [確定]  以完成。

新的警示規則在建立完成後，就會出現在自訂警示規則的清單中。 一旦有符合該規則的條件時，系統便會觸發新的警示，而您可以在 [安全性警示]  儀表板中看到。

![警示](./media/security-center-custom-alert/security-center-custom-alert-fig5.png)

請注意，規則建立期間所建立的參數 (搜尋查詢、閾值等) 均可在此自訂規則的警示中找到。

## <a name="see-also"></a>請參閱
在本文件中，您已了解如何在 Azure 資訊安全中心內建立自訂警示規則。 若要深入了解「Azure 資訊安全中心」，請參閱下列主題：

* [管理及回應 Azure 資訊安全中心的安全性警示](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)。 了解如何在資訊安全中心管理警示，以及回應安全性事件。
* [Azure 資訊安全中心的安全性健全狀況監視](security-center-monitoring.md)。 了解如何監視 Azure 資源的健全狀況。
* [了解 Azure 資訊安全中心的安全性警示](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)。 了解不同類型的安全性警示。
* [Azure 資訊安全中心疑難排解指南](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide)。 了解如何針對資訊安全中心的常見問題進行疑難排解。
* [Azure 資訊安全中心常見問題集](security-center-faq.md)。 尋找有關使用服務的常見問題。
* [Azure 安全性部落格](https://blogs.msdn.com/b/azuresecurity/)。 尋找有關 Azure 安全性與相容性的部落格文章。
