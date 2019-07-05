---
title: 在 Azure 入口網站中建立和管理動作群組
description: 了解如何在 Azure 入口網站中建立和管理動作群組。
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 5/30/2019
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 796ae0e3be3a2e43d7f27d5932cdba0cf660f36b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67441899"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>在 Azure 入口網站中建立和管理動作群組
## <a name="overview"></a>概觀 ##
動作群組是 Azure 訂用帳戶擁有者定義的通知喜好設定集合。 Azure 監視器和服務健康狀態警示使用動作群組來通知使用者警示已被觸發。 根據使用者的需求而定，不同的警示可能使用相同的動作群組或不同的動作群組。 一個訂用帳戶中最多可設定 2,000 個動作群組。

您設定要通知的人員，透過電子郵件或 SMS，他們會收到確認訊息，指出已新增到動作群組的動作。

本文將說明如何在 Azure 入口網站中建立和管理動作群組。

每個動作是由下列屬性所組成：

* **名稱**：動作群組內的唯一識別碼。  
* **動作類型**：執行此動作。 範例包括傳送語音電話、SMS、電子郵件或觸發各種類型的自動化動作。 請參閱本文稍後的類型。
* **詳細資料**：對應的詳細資料會因*動作類型*。

如需如何使用 Azure Resource Manager 範本設定動作群組的資訊，請參閱[動作群組 Resource Manager 範本](../../azure-monitor/platform/action-groups-create-resource-manager-template.md)。

## <a name="create-an-action-group-by-using-the-azure-portal"></a>使用 Azure 入口網站建立動作群組 ##
1. 在 [入口網站](https://portal.azure.com) 中，選取 **監視**。 **監視器**窗格會合併所有監視設定和一個檢視中的資料。

    ![監視」服務](./media/action-groups/home-monitor.png)
1. 選取 **警示**然後選取**管理動作**。

    ![管理動作按鈕](./media/action-groups/manage-action-groups.png)
1. 選取 [新增動作群組]  ，並填寫各欄位。

    ![「新增動作群組」命令](./media/action-groups/add-action-group.png)
1. 在 [動作群組名稱]  方塊中輸入名稱，然後在 [簡短名稱]  方塊中，輸入名稱。 使用這個群組傳送通知時，會使用簡短名稱來取代完整的動作群組名稱。

      ![「新增動作群組」對話方塊](./media/action-groups/action-group-define.png)

1. [訂用帳戶]  方塊會自動填入您目前的訂用帳戶。 訂用帳戶是要儲存動作群組的位置。

1. 選取要在其中儲存動作群組的 [資源群組]  。

1. 定義動作的清單。 提供下列每個動作：

    a. **名稱**：輸入此動作的唯一識別碼。

    b. **動作類型**：選取電子郵件/簡訊/推播/語音、邏輯應用程式、Webhook、ITSM 或自動化 Runbook。

    c. **詳細資料**：根據動作類型，輸入電話號碼、電子郵件地址、Webhook URI、Azure 應用程式、ITSM 連線或自動化 Runbook。 針對 ITSM 動作，請額外指定 [工作項目]  與您 ITSM 工具所需的其他欄位。
    
    d. **一般警示架構**:您可以選擇啟用[常見的警示結構描述](https://aka.ms/commonAlertSchemaDocs)、 提供一個可延伸的優點和 Azure 監視器中服務的統一的警示承載，在所有警示。

1. 選取 [確定]  來建立動作群組。

## <a name="manage-your-action-groups"></a>管理您的動作群組 ##
建立動作群組之後，它會顯示在**動作群組**一節**監視器**窗格。 選取您要管理的動作群組：

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

在動作群組中，您可能必須為數有限的電子郵件動作。 請參閱[速率限制資訊](./../../azure-monitor/platform/alerts-rate-limiting.md)一文

**ITSM** -您可能在動作群組中的有限的數目的 ITSM 動作。 ITSM 動作需要 ITSM 連線。 了解如何建立 [ITSM 連線](../../azure-monitor/platform/itsmc-overview.md)。

**邏輯應用程式**-您可能在動作群組中的有限的數量的邏輯應用程式動作。

**函式應用程式**-函式金鑰的函式應用程式設定為透過目前需要 v2 函式應用程式，若要設定之應用程式設定 」 AzureWebJobsSecretStorageType"函式 API 讀取動作，以 「 檔案 」。 如需詳細資訊，請參閱 <<c0> [ 變更為函式 V2 中的金鑰管理]( https://aka.ms/funcsecrets)。

**Runbook** -您可能在動作群組中的有限的數目的 Runbook 動作。 請參閱[Azure 訂用帳戶服務限制](../../azure-subscription-service-limits.md)如需 Runbook 承載的限制。

**SMS** -您可能必須為數有限的 SMS 動作的動作群組中。 另請參閱[速率限制資訊](./../../azure-monitor/platform/alerts-rate-limiting.md)並[SMS 警示行為](../../azure-monitor/platform/alerts-sms-behavior.md)如其他的重要資訊。 

**語音**-在動作群組中可能有限的數目的語音的動作。 請參閱[速率限制資訊](./../../azure-monitor/platform/alerts-rate-limiting.md)文章。

**Webhook** -您可能在動作群組中的有限的數目的 Webhook 動作。 Webhook 會使用下列規則來重試。 Webhook 呼叫重試一次最多 2 倍的下列的 HTTP 狀態碼傳回時：408、429、503、504 或 HTTP 端點沒有回應。 第一次重試會在 10 秒後執行。 第二次重試會在 100 秒後執行。 兩次失敗後, 沒有動作群組會在 30 分鐘內呼叫端點。 

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
 - 52.183.31.0
 - 52.184.145.166
 - 51.4.138.199
 - 51.5.148.86
 - 51.5.149.19

若要接收這些 IP 位址變更的相關的更新，建議您設定服務健康情況警示，會監視 「 動作群組 」 服務的相關資訊通知。

## <a name="next-steps"></a>後續步驟 ##
* 進一步了解 [SMS 警示行為](../../azure-monitor/platform/alerts-sms-behavior.md)。  
* 了解[活動記錄警示 Webhook 結構描述](../../azure-monitor/platform/activity-log-alerts-webhook.md)。  
* 深入了解 [ITSM 連接器](../../azure-monitor/platform/itsmc-overview.md)
* 深入了解警示的[速率限制](../../azure-monitor/platform/alerts-rate-limiting.md)。
* 取得[活動記錄警示的概觀](../../azure-monitor/platform/alerts-overview.md)，並了解如何收到警示。  
* 了解如何[設定每當服務健康狀態通知公佈時的警示](../../azure-monitor/platform/alerts-activity-log-service-notifications.md)。
