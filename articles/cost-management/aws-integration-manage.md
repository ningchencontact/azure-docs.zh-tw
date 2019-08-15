---
title: 管理 Azure 成本管理中的 AWS 成本和使用量
description: 本文可協助您瞭解如何在成本管理中使用成本分析和預算來管理 AWS 成本和使用量。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: cost-management
manager: ormaoz
ms.custom: ''
ms.openlocfilehash: 6767644e037c6abb3e3044c8707e923a9137a01c
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/14/2019
ms.locfileid: "69019538"
---
# <a name="manage-aws-costs-and-usage-in-azure"></a>管理 Azure 中的 AWS 成本和使用量

在您為 Azure 成本管理設定和設定 AWS 成本和使用量報告整合之後, 即可開始管理 AWS 成本和使用量。 本文可協助您瞭解如何在成本管理中使用成本分析和預算來管理 AWS 成本和使用量。

如果尚未設定整合, 請參閱[安裝和設定 AWS 使用量報告整合](aws-integration-set-up-configure.md)。

_開始之前_:如果您不熟悉成本分析, 請參閱[使用成本分析快速入門探索和分析成本](quick-acm-cost-analysis.md)。 此外, 如果您不熟悉 Azure 中的預算, 請參閱[建立和管理 azure 預算](tutorial-acm-create-budgets.md)教學課程。

## <a name="view-aws-costs-in-cost-analysis"></a>在成本分析中查看 AWS 成本

AWS 成本可用於下列範圍中的成本分析:

- AWS 管理群組下的連結帳戶
- AWS 連結的帳戶成本
- AWS 合併的帳戶成本

下一節將說明如何使用範圍, 讓您可以查看每一項的成本和使用量資料。

### <a name="view-aws-linked-accounts-under-a-management-group"></a>在管理群組下查看 AWS 連結的帳戶

使用管理群組範圍來查看成本, 是查看來自不同訂用帳戶和連結帳戶之匯總成本的唯一方法。 使用管理群組可提供跨雲端的觀點。

在 [成本分析] 中, 開啟 [範圍選取器], 然後選取保存您 AWS 連結帳戶的管理群組。 以下是 Azure 入口網站中的範例影像:

![選取範圍視圖的範例](./media/aws-integration-manage/select-scope01.png)



以下範例顯示成本分析中的管理群組成本 (依提供者 (Azure 和 AWS) 分組)。

![在成本分析中顯示一季的 Azure 和 AWS 成本範例](./media/aws-integration-manage/cost-analysis-aws-azure.png)

### <a name="view-aws-linked-account-costs"></a>View AWS 連結的帳戶成本

若要查看 AWS 連結帳戶成本, 請開啟範圍選取器, 然後選取 AWS 連結的帳戶。 請注意, 連結的帳戶會與 AWS 連接器中所定義的管理群組相關聯。

以下範例顯示如何選取 AWS 連結的帳戶範圍。

![選取範圍視圖的範例](./media/aws-integration-manage/select-scope02.png)



### <a name="view-aws-consolidated-account-costs"></a>View AWS 合併帳戶成本

若要查看 AWS 合併帳戶的成本, 請開啟 [範圍選取器], 然後選取 [AWS] 合併帳戶。 以下範例顯示如何選取 AWS 合併帳戶範圍。

![選取範圍視圖的範例](./media/aws-integration-manage/select-scope03.png)



此範圍會提供與 AWS 合併帳戶相關聯之所有 AWS 連結帳戶的匯總視圖。 以下範例顯示 AWS 合併帳戶的成本 (依服務名稱分組)。

![在成本分析中顯示 AWS 合併成本的範例](./media/aws-integration-manage/cost-analysis-aws-consolidated.png)

### <a name="dimensions-available-for-filtering-and-grouping"></a>可供篩選和分組的維度

下表描述在成本分析中可用於分組和篩選的維度。

| 維度 | Amazon 當前標頭 | 範圍 | 註解 |
| --- | --- | --- | --- |
| 可用性區域 | lineitem/AvailabilityZone | 全部 |   |
| Location | 產品/地區 | 全部 |   |
| 計量 |   | 全部 |   |
| 計量類別 | lineItem/ProductCode | 全部 |   |
| 計量子類別 | lineitem/UsageType | 全部 |   |
| 運算 | lineItem/Operation | 全部 |   |
| Resource | lineItem/ResourceId | 全部 |   |
| 資源類型 | product/instanceType | 全部 | 如果 product/instanceType 為 null, 則會使用 lineItem/UsageType。 |
| ResourceGuid | N/A | 全部 | Azure 計量 GUID。 |
| 服務名稱 | product/ProductName | 全部 | 如果 product/ProductName 是 null, 則會使用 lineItem/ProductCode。 |
| 服務層級 |   |   |   |
| 訂用帳戶識別碼 | lineItem/UsageAccountId | 合併的帳戶和管理群組 |   |
| 訂用帳戶名稱 | N/A | 合併的帳戶和管理群組 | 帳戶名稱是使用 AWS 組織 API 所收集。 |
| 標記 | resourceTags/\* | 全部 | _使用者:_ 前置詞會從使用者定義的標記中移除, 以允許跨雲端標籤。 _Aws:_ prefix 會保留不變。 |
| 帳務帳戶識別碼 | bill/PayerAccountId | 管理群組 |   |
| 帳單帳戶名稱 | N/A | 管理群組 | 帳戶名稱是使用 AWS 組織 API 所收集。 |
| 提供者 | N/A | 管理群組 | 可能是 AWS 或 Azure。 |

## <a name="set-budgets-on-aws-scopes"></a>設定 AWS 範圍的預算

使用預算以主動管理成本, 並在您的組織中推動責任。 預算是在 AWS 合併帳戶和 AWS 連結的帳戶範圍上設定。 以下是成本管理中顯示之 AWS 合併帳戶的預算範例:

![顯示 AWS 合併帳戶預算的範例](./media/aws-integration-manage/budgets-aws-consolidated-account01.png)

## <a name="aws-data-collection-process"></a>AWS 資料收集程式

設定 AWS 連接器之後, 會啟動資料收集和探索進程。 收集所有使用量資料可能需要幾小時的時間。 持續時間取決於:

- 處理 AWS S3 bucket 中目前檔案所需的時間。
- 建立 AWS 合併帳戶和 AWS 連結的帳戶範圍所需的時間。
- AWS 的時間和頻率會在 S3 bucket 中寫入成本和使用量報告檔案

## <a name="aws-integration-pricing"></a>AWS 整合定價

每個 AWS 連接器都會取得90的免費試用天。 在公開預覽期間, 不會產生任何費用。

標價是您 AWS 每月成本的 1%。 每個月都會根據您上個月的已開票成本向您收費。

存取 AWS Api 可能會產生額外的成本。

## <a name="aws-integration-limitations"></a>AWS 整合限制

- 成本管理不支援包含多個貨幣類型的成本報表。 如果您選取的範圍有多個貨幣, 則會顯示錯誤訊息。
- 雲端連接器不支援 AWS GovCloud (US)、AWS Gov 或 AWS 中國。
- 成本管理只顯示 AWS_使用量成本_。 尚不支援稅金、支援、退款、RI、點數或其他任何收費類型。

## <a name="troubleshooting-aws-integration"></a>針對 AWS 整合進行疑難排解

使用下列疑難排解資訊來解決常見的問題。

### <a name="no-permission-to-aws-linked-accounts"></a>沒有 AWS 連結帳戶的許可權

**錯誤碼:** _未經授權_

有兩種方式可取得存取 AWS 連結帳戶成本的許可權:

- 取得具有 AWS 連結帳戶之管理群組的存取權。
- 讓某人授與您 AWS 連結帳戶的許可權。

根據預設, AWS 連接器建立者是連接器所建立之所有物件的擁有者。 包括 AWS 合併帳戶和 AWS 連結的帳戶。

為了能夠驗證連接器設定, 您至少需要一個參與者角色, 讀取器無法驗證連接器設定

### <a name="collection-failed-with-assumerole"></a>收集失敗, AssumeRole

**錯誤碼:** _FailedToAssumeRole_

此錯誤表示成本管理無法呼叫 AWS AssumeRole API。 此問題可能是因為角色定義發生問題所造成。 確認下列條件是否成立:

- 外部識別碼與角色定義和連接器定義中的識別碼相同。
- 角色類型會設定為**屬於您或協力廠商的另一個 AWS 帳戶。**
- [**需要 MFA**選擇] 已清除。
- AWS 角色中的受信任 AWS 帳戶為_432263259397_。

### <a name="collection-failed-with-access-denied---cur-report-definitions"></a>收集因拒絕存取而失敗-當前報表定義

**錯誤碼:** _AccessDeniedReportDefinitions_ 

此錯誤表示成本管理無法看到 [成本] 和 [使用量] 報表定義。 這個許可權是用來驗證目前的是否已如預期般定義 Azure 成本管理。 請參閱[在 AWS 中建立成本和使用方式報表](aws-integration-set-up-configure.md#create-a-cost-and-usage-report-in-aws)。

### <a name="collection-failed-with-access-denied---list-reports"></a>收集因拒絕存取而失敗-清單報告

**錯誤碼:** _AccessDeniedListReports_ 

此錯誤表示成本管理無法列出目前所在的 S3 值區中的物件。 AWS IAM 原則需要值區的許可權和值區中的物件。 請參閱[在 AWS 中建立角色和原則](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws)。

### <a name="collection-failed-with-access-denied---download-report"></a>收集因拒絕存取而失敗-下載報表 

**錯誤碼:** _AccessDeniedDownloadReport_ 

此錯誤表示成本管理無法存取並下載 Amazon S3 bucket 中儲存的目前檔案。 請確定附加至角色的 AWS JSON 原則, 類似于在[AWS 中建立角色和原則](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws)一節中所顯示的範例。

### <a name="collection-failed-since-we-did-not-find-the-cost-and-usage-report"></a>收集失敗, 因為我們找不到成本和使用量報告

**錯誤碼:** _FailedToFindReport_

此錯誤表示成本管理找不到在連接器中定義的成本和使用量報告。 請確定它不會被刪除, 而且附加至角色的 AWS JSON 原則類似于在[AWS 中建立角色和原則](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws)一節中所顯示的範例。

### <a name="unable-to-create-or-verify-connector-due-to-cost-and-usage-report-definitions-mismatch"></a>因為成本和使用量報告定義不相符, 所以無法建立或驗證連接器

**錯誤碼:** _ReportIsNotValid_

此錯誤與 AWS 成本和使用方式報表的定義有關, 我們需要這份報告的特定設定, 請參閱在[AWS 中建立成本和使用方式報告](aws-integration-set-up-configure.md#create-a-cost-and-usage-report-in-aws)中的需求。

## <a name="next-steps"></a>後續步驟

- 如果您尚未使用管理群組設定 Azure 環境, 請參閱[管理群組的初始設定](../governance/management-groups/index.md#initial-setup-of-management-groups)。
