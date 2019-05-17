---
title: 在 Azure 入口網站中建立和管理動作群組
description: 了解如何在 Azure 入口網站中建立和管理動作群組。
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 5/10/2019
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: fba5119feb1ff7a0170a573371e479caa5fc33eb
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544461"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>在 Azure 入口網站中建立和管理動作群組
## <a name="overview"></a>概觀 ##
動作群組是 Azure 訂用帳戶擁有者定義的通知喜好設定集合。 Azure 監視器和服務健康狀態警示使用動作群組來通知使用者警示已被觸發。 根據使用者的需求而定，不同的警示可能使用相同的動作群組或不同的動作群組。 一個訂用帳戶中最多可設定 2,000 個動作群組。

当配置操作来通过电子邮件或短信通知某个人员时，该人员将收到确认，指出其已被添加到操作组。

本文將說明如何在 Azure 入口網站中建立和管理動作群組。

每個動作是由下列屬性所組成：

* **名稱**：動作群組內的唯一識別碼。  
* **動作類型**：执行的操作。 範例包括傳送語音電話、SMS、電子郵件或觸發各種類型的自動化動作。 請參閱本文稍後的類型。
* **詳細資料**：因“操作类型”而异的相应详细信息。

如需如何使用 Azure Resource Manager 範本設定動作群組的資訊，請參閱[動作群組 Resource Manager 範本](../../azure-monitor/platform/action-groups-create-resource-manager-template.md)。

## <a name="create-an-action-group-by-using-the-azure-portal"></a>使用 Azure 入口網站建立動作群組 ##
1. 在 [入口網站](https://portal.azure.com) 中，選取 **監視**。 “监视器”窗格将所有监视设置和数据合并到一个视图中。

    ![監視」服務](./media/action-groups/home-monitor.png)
1. 選取 [警示]，然後選取 [管理動作群組]。

    ![[管理動作群組] 按鈕](./media/action-groups/manage-action-groups.png)
1. 選取 [新增動作群組]，並填寫各欄位。

    ![「新增動作群組」命令](./media/action-groups/add-action-group.png)
1. 在 [動作群組名稱] 方塊中輸入名稱，然後在 [簡短名稱] 方塊中，輸入名稱。 使用這個群組傳送通知時，會使用簡短名稱來取代完整的動作群組名稱。

      ![「新增動作群組」對話方塊](./media/action-groups/action-group-define.png)

1. [訂用帳戶] 方塊會自動填入您目前的訂用帳戶。 訂用帳戶是要儲存動作群組的位置。

1. 選取要在其中儲存動作群組的 [資源群組]。

1. 定义一个操作列表。 为每个操作提供以下信息：

    a. **名稱**：輸入此動作的唯一識別碼。

    b. **動作類型**：選取電子郵件/簡訊/推播/語音、邏輯應用程式、Webhook、ITSM 或自動化 Runbook。

    c. **詳細資料**：根據動作類型，輸入電話號碼、電子郵件地址、Webhook URI、Azure 應用程式、ITSM 連線或自動化 Runbook。 針對 ITSM 動作，請額外指定 [工作項目] 與您 ITSM 工具所需的其他欄位。

1. 選取 [確定] 來建立動作群組。

## <a name="manage-your-action-groups"></a>管理您的動作群組 ##
创建操作组后，它会显示在“监视器”边栏选项卡的“操作组”部分中。 選取您要管理的動作群組：

* 新增、編輯或移除動作。
* 刪除動作群組。

## <a name="action-specific-information"></a>動作特定資訊
> [!NOTE]
> 請參閱[監視的訂用帳戶服務限制](https://docs.microsoft.com/azure/azure-subscription-service-limits#monitor-limits)數值限制在每個以下項目。  

**Azure app 推播**-您可能在動作群組中的有限的數目的 Azure 應用程式動作。

**電子郵件** - 將會從下列電子郵件地址傳送電子郵件。 請確定已適當設定您的電子郵件篩選
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

一个操作组中可以存在有限数量的电子邮件操作。 請參閱[速率限制資訊](./../../azure-monitor/platform/alerts-rate-limiting.md)一文

**ITSM** -您可能在動作群組中的有限的數目的 ITSM 動作。 ITSM 動作需要 ITSM 連線。 了解如何建立 [ITSM 連線](../../azure-monitor/platform/itsmc-overview.md)。

**逻辑应用** - 一个操作组中可以存在有限数量的逻辑应用操作。

**Function App** - 配置为操作的 Function App 的函数密钥通过函数 API 读取，这当前需要 v2 函数应用将应用设置“AzureWebJobsSecretStorageType”配置为“文件”。 有关详细信息，请参阅 [Functions V2 中对密钥管理的更改]( https://aka.ms/funcsecrets)。

**Runbook** - 一个操作组中可以存在有限数量的 Runbook 操作。 有关 Runbook 有效负载的限制，请参考 [Azure 订阅服务限制](../../azure-subscription-service-limits.md)。

**短信** - 一个操作组中可以存在有限数量的短信操作。 有关其他重要信息，另请参阅[速率限制信息](./../../azure-monitor/platform/alerts-rate-limiting.md)和[短信警报行为](../../azure-monitor/platform/alerts-sms-behavior.md)。 

**语音** - 一个操作组中可以存在有限数量的语音操作。 请参阅[速率限制信息](./../../azure-monitor/platform/alerts-rate-limiting.md)一文。

**Webhook** - 一个操作组中可以存在有限数量的 Webhook 操作。 Webhook 使用以下规则进行重试。 当返回的 HTTP 状态代码为 408、429、503、504 或者 HTTP 终结点未响应时，408、429、503、504 或 HTTP 端點沒有回應。 第一次重試會在 10 秒後執行。 第二次重試會在 100 秒後執行。 在失败两次后，操作组在 30 分钟内不会再调用该终结点。 

來源 IP 位址範圍
 - 13.72.19.232
 - 13.106.57.181
 - 13.106.54.3
 - 13.106.54.19
 - 13.106.38.142
 - 13.106.38.148
 - 13.106.57.196
 - 52.244.68.117
 - 52.244.65.137
 - 51.4.138.199
 - 51.5.148.86
 - 51.5.149.19

若要接收有关这些 IP 地址更改的更新，我们建议你配置服务运行状况警报，以监视有关操作组服务的信息通知。

## <a name="next-steps"></a>後續步驟 ##
* 進一步了解 [SMS 警示行為](../../azure-monitor/platform/alerts-sms-behavior.md)。  
* 了解[活動記錄警示 Webhook 結構描述](../../azure-monitor/platform/activity-log-alerts-webhook.md)。  
* 深入了解 [ITSM 連接器](../../azure-monitor/platform/itsmc-overview.md)
* 深入了解警示的[速率限制](../../azure-monitor/platform/alerts-rate-limiting.md)。
* 取得[活動記錄警示的概觀](../../azure-monitor/platform/alerts-overview.md)，並了解如何收到警示。  
* 了解如何[設定每當服務健康狀態通知公佈時的警示](../../azure-monitor/platform/alerts-activity-log-service-notifications.md)。
