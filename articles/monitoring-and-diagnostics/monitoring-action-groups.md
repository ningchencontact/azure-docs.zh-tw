---
title: 在 Azure 入口網站中建立和管理動作群組 | Microsoft Docs
description: 了解如何在 Azure 入口網站中建立和管理動作群組。
author: dkamstra
manager: chrad
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2018
ms.author: dukek
ms.openlocfilehash: e3185b8d8ce97ffd04188b2b49a457bd14d5c6c8
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2018
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>在 Azure 入口網站中建立和管理動作群組
## <a name="overview"></a>概觀 ##
本文將說明如何在 Azure 入口網站中建立和管理動作群組。

您可以設定包含動作群組的動作清單。 然後您所定義的每個警示就可以使用這些群組，確保在每次觸發警示時皆採取相同的動作。

每個動作是由下列屬性所組成：

* **名稱**：動作群組內的唯一識別碼。  
* **動作類型**：傳送語音通話或簡訊、傳送電子郵件、呼叫 Webhook、將資料傳送至 ITSM 工具、呼叫 Logic App、將推播通知傳送至 Azure 應用程式，或執行自動化 Runbook。
* **詳細資料**：對應的電話號碼、電子郵件地址、Webhook URI 或 ITSM 連線詳細資料。

如需如何使用 Azure Resource Manager 範本設定動作群組的資訊，請參閱[動作群組 Resource Manager 範本](monitoring-create-action-group-with-resource-manager-template.md)。

## <a name="create-an-action-group-by-using-the-azure-portal"></a>使用 Azure 入口網站建立動作群組 ##
1. 在 [入口網站](https://portal.azure.com) 中，選取 **監視**。 [監視] 刀鋒視窗會將所有監視設定和資料合併在一個檢視中。

    ![「監視」服務](./media/monitoring-action-groups/home-monitor.png)
2. 在 [設定] 區段上，選取 [動作群組]。

    ![使用 [動作群組] 索引標籤](./media/monitoring-action-groups/action-groups-blade.png)
3. 選取 [新增動作群組]，並填寫各欄位。

    ![「新增動作群組」命令](./media/monitoring-action-groups/add-action-group.png)
4. 在 [動作群組名稱] 方塊中輸入名稱，然後在 [簡短名稱] 方塊中，輸入名稱。 使用這個群組傳送通知時，會使用簡短名稱來取代完整的動作群組名稱。

      ![「新增動作群組」對話方塊](./media/monitoring-action-groups/action-group-define.png)

5. [訂用帳戶] 方塊會自動填入您目前的訂用帳戶。 訂用帳戶是要儲存動作群組的位置。

6. 選取要在其中儲存動作群組的 [資源群組]。

7. 針對每個動作提供下列資訊，來定義動作的清單：

    a. **名稱**：輸入此動作的唯一識別碼。

    b. **動作類型**：選取電子郵件/簡訊/推送/語音、邏輯應用程式、Webhook、ITSM 或自動化 Runbook。

    c. **詳細資料**：根據動作類型，輸入電話號碼、電子郵件地址、Webhook URI、Azure 應用程式、ITSM 連線或自動化 Runbook。 針對 ITSM 動作，請額外指定 [工作項目] 與您 ITSM 工具所需的其他欄位。

8. 選取 [確定] 來建立動作群組。

## <a name="action-specific-information"></a>動作特定資訊
<dl>
<dt>Azure 應用程式推送</dt>
<dd>您在動作群組中最多可擁有 10 個 Azure 應用程式動作。</dd>
<dd>Azure 應用程式動作目前只支援 ServiceHealth 警示。 將會忽略所有其他的警示時間。 請參閱[設定每當服務健康狀態通知公佈時的警示](monitoring-activity-log-alerts-on-service-notifications.md)。</dd>

<dt>電子郵件</dt>
<dd>您在動作群組中最多可擁有 50 個電子郵件動作</dd>
<dd>請參閱[速率限制資訊](./monitoring-alerts-rate-limiting.md)一文</dd>

<dt>ITSM</dt>
<dd>您在動作群組中最多可擁有 10 個 ITSM 動作</dd>
<dd>ITSM 動作需要 ITSM 連線。 了解如何建立 [ITSM 連線](../log-analytics/log-analytics-itsmc-overview.md)。</dd>

<dt>邏輯應用程式</dt>
<dd>您在動作群組中最多可擁有 10 個邏輯應用程式動作</dd>

<dt>Runbook</dt>
<dd>您在動作群組中最多可擁有 10 個 Runbook 動作</dd>

<dt>簡訊</dt>
<dd>您在動作群組中最多可擁有 10 個簡訊動作</dd>
<dd>請參閱[速率限制資訊](./monitoring-alerts-rate-limiting.md)一文</dd>
<dd>請參閱[簡訊警示行為](monitoring-sms-alert-behavior.md)一文</dd>

<dt>語音</dt>
<dd>您在動作群組中最多可擁有 10 個語音動作</dd>
<dd>請參閱[速率限制資訊](./monitoring-alerts-rate-limiting.md)一文</dd>

<dt>Webhook</dt>
<dd>您在動作群組中最多可擁有 10 個 Webhook 動作
<dd>重試邏輯 - 傳回下列 HTTP 狀態碼時，最多會重試 3 次 Webhook 呼叫：408、429、503、504</dd>
</dl>

## <a name="manage-your-action-groups"></a>管理您的動作群組 ##
建立動作群組之後，將會在 [監視] 刀鋒視窗的 [動作群組] 區段中顯示。 選取您要管理的動作群組：

* 新增、編輯或移除動作。
* 刪除動作群組。

## <a name="next-steps"></a>後續步驟 ##
* 進一步了解 [SMS 警示行為](monitoring-sms-alert-behavior.md)。  
* 了解[活動記錄警示 Webhook 結構描述](monitoring-activity-log-alerts-webhook.md)。  
* 深入了解 [ITSM 連接器](../log-analytics/log-analytics-itsmc-overview.md)
* 深入了解警示的[速率限制](monitoring-alerts-rate-limiting.md)。
* 取得[活動記錄警示的概觀](monitoring-overview-alerts.md)，並了解如何收到警示。  
* 了解如何[設定每當服務健康狀態通知公佈時的警示](monitoring-activity-log-alerts-on-service-notifications.md)。
