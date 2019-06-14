---
title: Azure 監視器服務提供者 |Microsoft Docs
description: Azure 監視器可協助管理服務提供者 (Msp)、 大型企業、 獨立軟體廠商 (Isv) 和主機服務提供者管理和監視客戶的內部部署或雲端基礎結構中的伺服器。
services: log-analytics
documentationcenter: ''
author: MeirMen
manager: jochan
editor: ''
ms.assetid: c07f0b9f-ec37-480d-91ec-d9bcf6786464
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: meirm
ms.openlocfilehash: 97d8d6fac93ebabac8fb319ce2f1ab8719f5f86b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60452648"
---
# <a name="azure-monitor-for-service-providers"></a>Azure 監視器服務提供者
受管理的服務提供者 (Msp)、 大型企業、 獨立軟體廠商 (Isv) 和主機服務提供者管理和監視客戶的內部部署或雲端基礎結構中的伺服器，可協助 Azure 監視器中的 log Analytics 工作區。 

大型企業與服務提供者有許多相似之處，特別是當有集中式的 IT 團隊負責管理許多不同業務單位的 IT 時。 為了簡單起見，本文件會使用「服務提供者」  這個詞彙，但是相同的功能也適用於企業或其他客戶。

合作夥伴與服務提供者是一部分的[雲端解決方案提供者 (CSP)](https://partner.microsoft.com/Solutions/cloud-reseller-overview)程式，Azure 監視器中的 Log Analytics 是 Azure 中所提供的服務的其中一個[Azure CSP 訂用帳戶](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview)。 

## <a name="architectures-for-service-providers"></a>服務提供者的架構

Log Analytics 工作區可讓系統管理員控制記錄的流程與隔離，以及建立因應其特定商務需求的記錄架構。 [本文](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access)說明工作區管理的一般考量。 服務提供者會有其他考量。

服務提供者對於 Log Analytics 工作區有三個可能的架構：

### <a name="1-distributed---logs-are-stored-in-workspaces-located-in-the-customers-tenant"></a>1.分散式：記錄會儲存在客戶租用戶的工作區 

在此架構中，會在用於該客戶所有記錄的客戶租用戶中部署工作區。 藉由使用 [Azure Active Directory 來賓使用者 (B2B)](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b)，可以授與服務提供者系統管理員對於此工作區的存取權。 服務提供者管理員將必須在 Azure 入口網站中切換至其客戶的目錄，才能存取這些工作區。

此架構的優點包括：
* 客戶可以使用自己的[角色型存取](https://docs.microsoft.com/azure/role-based-access-control/overview)，以管理記錄存取。
* 每個客戶對於其工作區都可以有不同的設定，例如保留和資料限定。
* 基於法規和合規性而隔離客戶。
* 每個工作區的費用都將會累計到客戶的訂用帳戶中。
* 可以從所有類型的資源來收集記錄，而不只是代理程式型資源。 例如，Azure 稽核記錄。

此架構的缺點包括：
* 服務提供者比較難以同時管理大量的客戶租用戶。
* 服務提供者管理員必須佈建在客戶目錄上。
* 服務提供者無法跨其客戶分析資料。

### <a name="2-central---logs-are-stored-in-a-workspace-located-in-the-service-provider-tenant"></a>2.集中式：記錄會儲存在服務提供者租用戶的工作區

在此架構中，記錄不會儲存在客戶的租用戶中，但只會儲存在其中一個服務提供者訂用帳戶內的集中位置。 客戶 VM 上安裝的代理程式會設定為使用工作區識別碼和祕密金鑰以傳送記錄至此工作區。

此架構的優點包括：
* 容易管理大量客戶，並將其整合至各種後端系統。
* 服務提供者對於記錄和各種成品 (例如函式和儲存的查詢) 都具備完整的擁有權。
* 服務提供者可以跨其所有客戶執行分析。

此架構的缺點包括：
* 此架構僅適用於代理程式型 VM 資料，不涵蓋 PaaS、SaaS 與 Azure 網狀架構資料來源。
* 當客戶的資料合併到單一工作區時，區隔彼此的資料可能會很困難。 唯一的好方法是使用電腦的完整網域名稱 (FQDN) 或透過 Azure 訂用帳戶識別碼。 
* 來自所有客戶的所有資料都會儲存在相同的區域中，帳單只有一份，而且有相同的保留和組態設定。
* Azure 網狀架構和 PaaS 服務 (例如 Azure 診斷和 Azure 稽核記錄) 會要求工作區位於與資源相同的租用戶中，因此無法將記錄傳送至集中式工作區。
* 來自所有客戶的所有 VM 代理程式都會使用相同的工作區識別碼和金鑰，向集中式工作區進行驗證。 在不中斷其他客戶的情況下，沒有任何方法可以封鎖來自特定客戶的記錄。


### <a name="3-hybrid---logs-are-stored-in-workspace-located-in-the-customers-tenant-and-some-of-them-are-pulled-to-a-central-location"></a>3.混合式：記錄會儲存在客戶租用戶的工作區，而且其中部分記錄會提取到集中位置。

第三個架構是兩個選項的混合。 這個架構是以第一個分散式架構為基礎，其中記錄儲存在每個客戶的本機，但使用特定機制來建立記錄的集中存放區。 一部分的記錄會提取到用於報告和分析的集中位置。 這個部分可能是少量的資料類型或活動摘要 (例如每日統計資料)。

有兩個選項，來實作記錄在集中位置：

1. 集中式工作區：服務提供者可以在其租用戶中建立工作區，並使用搭配[資料收集 API](../../azure-monitor/platform/data-collector-api.md) 和[查詢 API](https://dev.loganalytics.io/) 的指令碼，將不同工作區的資料帶到這個集中位置。 指令碼以外的另一個選項是使用 [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)。

2. Power BI 當作集中位置：Power BI 可以作為中央位置時的各種不同的工作區將資料匯出至使用 Log Analytics 工作區之間的整合及[Power BI](../../azure-monitor/platform/powerbi.md)。 


## <a name="next-steps"></a>後續步驟
* 使用 [Resource Manager 範本](template-workspace-configuration.md)建立和設定工作區
* 使用 [PowerShell](../../azure-monitor/platform/powershell-workspace-configuration.md)自動建立工作區 
* 使用[警示](../../azure-monitor/platform/alerts-overview.md)與現有的系統整合
* 使用 [Power BI](../../azure-monitor/platform/powerbi.md) 來產生摘要報告
* 檢閱[設定 Log Analytics 與 Power BI 來監視多個 CSP 客戶](https://docs.microsoft.com/azure/cloud-solution-provider/support/monitor-multiple-customers)的程序
