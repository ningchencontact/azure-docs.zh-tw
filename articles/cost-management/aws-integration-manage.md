---
title: 管理 AWS 成本和在 Azure 成本管理中的使用方式
description: 這篇文章可協助您了解如何使用成本分析 」 和 「 預算的成本管理來管理您的 AWS 成本和使用方式。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: cost-management
manager: ormaoz
ms.custom: ''
ms.openlocfilehash: 57e66d449b194662bfc03f7e130cf49c02a15793
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275703"
---
# <a name="manage-aws-costs-and-usage-in-azure"></a>管理 AWS 成本和在 Azure 中的使用方式

您已安裝並設定 Azure 成本管理的 AWS 成本和使用方式報表整合之後，您即可開始管理您的 AWS 成本和使用方式。 這篇文章可協助您了解如何使用成本分析 」 和 「 預算的成本管理來管理您的 AWS 成本和使用方式。

如果您尚未設定整合，請參閱[設定安裝及設定 AWS 使用量報表整合](aws-integration-set-up-configure.md)。

_開始之前_:如果您不熟悉成本分析，請參閱[探索及分析成本，以及成本分析](quick-acm-cost-analysis.md)快速入門。 而且，如果您不熟悉 Azure 中的預算，請參閱[建立和管理 Azure 的預算](tutorial-acm-create-budgets.md)教學課程。

## <a name="view-aws-costs-in-cost-analysis"></a>在 成本分析檢視 AWS 成本

在下列範圍中的成本分析 AWS 成本有：

- 管理群組下的連結的 AWS 帳戶
- 連結的 AWS 帳戶成本
- 合併的 AWS 帳戶成本

下一節會說明如何使用範圍，讓您查看針對每個的成本與使用量資料。

### <a name="view-aws-linked-accounts-under-a-management-group"></a>管理群組下的檢視連結的 AWS 帳戶

使用管理群組範圍中檢視成本是唯一的方法以查看彙總來自不同訂用帳戶的成本和連結的帳戶。 使用管理群組提供跨雲端檢視。

在 成本分析，開啟範圍選擇器並選取保留連結的 AWS 帳戶的管理群組。 在 Azure 入口網站中，以下是範例映像：

![選取範圍檢視的範例](./media/aws-integration-manage/select-scope01.png)



以下是範例成本分析，依提供者 （Azure 和 AWS） 中顯示的管理群組的成本。

![範例，示範 Azure 與 AWS 的成本中之季的成本分析](./media/aws-integration-manage/cost-analysis-aws-azure.png)

### <a name="view-aws-linked-account-costs"></a>檢視連結的 AWS 帳戶的成本

若要檢視 AWS 連結帳戶成本開啟範圍選擇器，然後選取 AWS 連結的帳戶。 請注意，連結的帳戶相關聯的管理群組，AWS 連接器中所定義。

以下是範例顯示如何選取 AWS 連結帳戶範圍內。

![選取範圍檢視的範例](./media/aws-integration-manage/select-scope02.png)



### <a name="view-aws-consolidated-account-costs"></a>合併的檢視 AWS 帳戶成本

若要檢視 AWS 成本，開啟範圍選擇器，然後選取 AWS 的彙總的帳戶彙總帳戶。 以下是範例顯示如何選取 AWS 彙總 」 帳戶範圍。

![選取範圍檢視的範例](./media/aws-integration-manage/select-scope03.png)



此範圍可提供所有的 AWS 的彙總的檢視連結的彙總的 AWS 帳戶相關聯的帳戶。 以下是範例顯示的 AWS 成本彙總依服務名稱分組的帳戶。

![範例，示範 AWS 彙總成本中成本分析](./media/aws-integration-manage/cost-analysis-aws-consolidated.png)

### <a name="dimensions-available-for-filtering-and-grouping"></a>維度可用以篩選與分組

下表描述可用來群組和篩選的成本分析中的維度。

| 維度 | Amazon CUR 標頭 | 範圍 | 註解 |
| --- | --- | --- | --- |
| 可用性區域 | lineitem/AvailabilityZone | 全部 |   |
| 位置 | 產品/地區 | 全部 |   |
| 計量 |   | 全部 |   |
| 計量類別 | lineItem/ProductCode | 全部 |   |
| 計量子類別 | lineitem/UsageType | 全部 |   |
| 作業 | lineItem/Operation | 全部 |   |
| 資源 | lineItem/ResourceId | 全部 |   |
| 資源類型 | product/instanceType | 全部 | 如果產品/instanceType 為 null，則會使用 lineItem/UsageType。 |
| ResourceGuid | N/A | 全部 | Azure 計量的 GUID。 |
| 服務名稱 | product/ProductName | 全部 | 如果產品/ProductName 為 null，則會使用 lineItem/ProductCode。 |
| 服務層 |   |   |   |
| 订阅 ID | lineItem/UsageAccountId | 合併的帳戶和管理群組 |   |
| 訂用帳戶名稱 | N/A | 合併的帳戶和管理群組 | 帳戶名稱會收集使用 AWS 組織 API。 |
| Tag | resourceTags/\* | 全部 | _使用者：_ 前置詞會從使用者定義的標籤，以允許跨雲端標記中移除。 _Aws:_ 前置詞會保留不變。 |
| 計費帳戶識別碼 | bill/PayerAccountId | 管理群組 |   |
| 計費帳戶名稱 | N/A | 管理群組 | 帳戶名稱會收集使用 AWS 組織 API。 |
| 提供者 | N/A | 管理群組 | AWS 或 Azure 中。 |

## <a name="set-budgets-on-aws-scopes"></a>在 AWS 範圍集預算

您可以使用預算來主動管理您組織中的成本和磁碟機的責任。 連結的帳戶範圍時，會在合併的 AWS 帳戶和 AWS 設定預算。 成本管理中顯示的彙總的 AWS 帳戶預算的範例如下：

![顯示 AWS 預算的範例整合帳戶](./media/aws-integration-manage/budgets-aws-consolidated-account01.png)

## <a name="aws-data-collection-process"></a>AWS 資料集合程序

設定好 AWS 連接器之後，資料收集和探索處理序啟動。 可能需要幾個小時才能將所有的使用量資料收集。 時間長短取決於：

- CUR 會處理檔案中的 AWS S3 貯體所需的時間。
- 建立 AWS 合併的帳戶和 AWS 連結的帳戶領域所需的時間。
- AWS 的頻率與時間 S3 貯體中寫入的成本和使用量報告檔案

## <a name="aws-integration-pricing"></a>AWS 整合定價

每個 AWS 連接器取得可免費試用版的 90 天。 公開預覽期間，完全免費。

定價為您的 AWS 每月成本 1%。 根據您開立發票的成本，從上個月向您收取每個月。

存取 AWS Api 可能會產生額外費用。

## <a name="aws-integration-limitations"></a>AWS 整合的限制

- 成本管理不支援包含多種貨幣的成本報告。 如果您選取的範圍包含多個貨幣來顯示錯誤訊息。
- AWS GovCloud （美國）、 AWS 政府或 AWS 中國，不支援雲端連接器。
- 成本的管理顯示 AWS_使用量成本_只。 稅、 支援、 退款、 RI、 信用額度或尚不支援類型的任何其他費用。

## <a name="troubleshooting-aws-integration"></a>AWS 整合疑難排解

若要解決常見的問題，使用下列疑難排解資訊。

### <a name="no-permission-to-aws-linked-accounts"></a>AWS 連結的帳戶沒有權限

**錯誤碼：** _未經授權_

有兩種方式，以取得權限來存取連結的 AWS 帳戶費用：

- 取得管理群組，其包含 AWS 連結的帳戶。
- 讓其他人提供您連結的 AWS 帳戶的權限。

根據預設，AWS 連接器建立者會是此連接器建立的所有物件的擁有者。 其中包括 AWS 合併的帳戶和 AWS 連結帳戶。

為了能夠確認連接器設定中，您需要至少一個參與者角色，讀取器無法驗證連接器設定

### <a name="collection-failed-with-assumerole"></a>集合與 AssumeRole 失敗

**錯誤碼：** _FailedToAssumeRole_

此錯誤表示 Cost Management 是無法呼叫 AWS AssumeRole API。 這個問題可能是因為角色定義的問題。 請確認下列條件成立：

- 外部識別碼是在角色定義和連接器定義一個相同。
- 角色類型設為**另一個 AWS 帳戶屬於您或第 3 方。**
- **要求 MFA**選擇已清除。
- AWS 角色中受信任的 AWS 帳戶會_432263259397_。

### <a name="collection-failed-with-access-denied"></a>失敗，發生拒絕存取的集合

- **錯誤碼：** _AccessDeniedReportDefinitions_ 
- **錯誤碼：** _AccessDeniedListReports_ 
- **錯誤碼：** _AccessDeniedDownloadReport_ 

此錯誤訊息表示 Cost Management 是無法存取所儲存的 Amazon S3 貯體的 CUR 檔案。 請確定連接至角色的 AWS JSON 原則類似於在底部顯示的範例[在 AWS 中建立的角色和原則](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws)一節。

### <a name="collection-failed-since-we-did-not-find-the-cost-and-usage-report"></a>集合失敗，因為我們找不到的成本和使用方式報表

**錯誤碼：** _FailedToFindReport_

此錯誤表示成本管理找不到成本和使用方式報表中的連接器所定義。 請確定它不會刪除並附加至角色的 AWS JSON 原則類似於在底部顯示的範例[在 AWS 中建立的角色和原則](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws)一節。

### <a name="unable-to-create-or-verify-connector-due-to-cost-and-usage-report-definitions-mismatch"></a>無法建立或確認因為成本和使用方式報表定義不相符的連接器

**錯誤碼：** _ReportIsNotValid_

此錯誤與 AWS 成本和使用量 報表中，定義相關我們對此報表需要特定的設定，請參閱中的需求[在 AWS 中建立成本和使用方式報表](aws-integration-set-up-configure.md#create-a-cost-and-usage-report-in-aws)

## <a name="next-steps"></a>後續步驟

- 如果您尚未使用管理群組設定 Azure 環境，請參閱[初始的管理群組安裝](../governance/management-groups/index.md#initial-setup-of-management-groups)。
