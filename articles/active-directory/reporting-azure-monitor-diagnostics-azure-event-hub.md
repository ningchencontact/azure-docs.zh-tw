---
title: 教學課程 - 將 Azure Active Directory 記錄串流到 Azure 事件中樞 (預覽) | Microsoft Docs
description: 深入了解如何設定 Azure 診斷以將 Azure Active Directory 記錄推送到事件中樞 (預覽)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 045f94b3-6f12-407a-8e9c-ed13ae7b43a3
ms.service: active-directory
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: c4f5bf1b49d7f59b2bb938a7ddc53b96c1d354ef
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/25/2018
ms.locfileid: "39240146"
---
# <a name="tutorial-stream-azure-active-directory-logs-to-an-azure-event-hub-preview"></a>教學課程：將 Azure Active Directory 記錄串流到 Azure 事件中樞 (預覽)

在本教學課程中，深入了解如何設定 Azure 監視器診斷設定，以將 Azure Active Directory 記錄串流到 Azure 事件中樞。 使用這個機制將您的記錄與像是 Splunk 和 QRadar 的第三方 SIEM 工具整合在一起。

## <a name="prerequisites"></a>必要條件 

您需要：

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，您可以註冊[免費試用](https://azure.microsoft.com/free/)。
* Azure Active Directory 租用戶
* 使用者，身分是該租用戶的全域管理員或安全性管理員
* Azure 訂用帳戶中的事件中樞命名空間和事件中樞。 深入了解如何[在這裡建立](https://docs.microsoft.com/azure/event-hubs/event-hubs-create.md)。

## <a name="archive-logs-to-event-hub"></a>將記錄封存到事件中樞

1. 登入 [Azure 入口網站](https://portal.azure.com)。 
2. 按一下 [Azure Active Directory]  ->  [活動]  ->  [稽核記錄]。 
3. 按一下 [匯出設定] 以開啟 [診斷設定] 刀鋒視窗。 如果您想要變更現有的設定則按一下 [編輯設定]，或者按一下 [新增診斷設定] 以新增一個設定。 您最多可以有三個設定。 
    ![匯出設定](./media/reporting-azure-monitor-diagnostics-azure-event-hub/ExportSettings.png "匯出設定")

4. 請勾選 [串流到事件中樞] 核取方塊，然後按一下 [事件中樞/設定]。
5. 選取您想要將記錄路由傳送到其中的 Azure 訂用帳戶和事件中樞命名空間。 訂用帳戶和事件中樞命名空間都必須與記錄串流來源的 Active Directory 租用戶產生關聯。 您也可以在要將記錄傳送到其中的事件中樞命名空間內，指定事件中樞。 如果未指定事件中樞，則會在命名空間中建立一個事件中樞，具有預設名稱 **insights-logs-audit**。
6. 按一下 [確定] 以結束事件中樞設定。
7. 勾選 [AuditLogs] 核取方塊，以將稽核記錄傳送到儲存體帳戶。 
8. 勾選 [SignInLogs] 核取方塊，以將登入記錄傳送到儲存體帳戶。
9. 按一下 [儲存] 來儲存這些設定。
    ![診斷設定](./media/reporting-azure-monitor-diagnostics-azure-event-hub/DiagnosticSettings.png "診斷設定")

10. 大約 15 分鐘之後，確認事件出現在事件中樞中。 若要完成這項操作，請從入口網站瀏覽至事件中樞，並確認**內送郵件**計數大於零。 
    ![稽核記錄檔](./media/reporting-azure-monitor-diagnostics-azure-event-hub/InsightsLogsAudit.png "稽核記錄檔")


## <a name="access-data-from-event-hubs"></a>存取事件中樞的資料

一旦資料出現在事件中樞中，您就可以透過兩種方式來存取。

* **設定支援的 SIEM 工具來讀取資料**：大部分工具皆需要事件中樞連接字串和 Azure 訂用帳戶的特定權限，才能讀取事件中樞的資料。 可與 Azure 監視器整合的部分工具清單如下：
    1. **Splunk**：如需如何將 Azure AD 記錄與 Splunk 整合在一起的詳細資訊，請參閱[如何使用 Azure 監視器將 Azure Active Directory 記錄與 Splunk 整合在一起](reporting-azure-monitor-diagnostics-splunk-integration.md)。
    
    2. **IBM QRadar**：Microsoft Azure DSM 與 Microsoft Azure 事件中樞通訊協定均可從 [IBM 支援網站](http://www.ibm.com/support)下載。 您可以[從這裡深入了解與 Azure 的整合](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0) \(英文\)。
    
    3. **SumoLogic**：遵循[這些指示](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub)以設定 SumoLogic 從事件中樞取用資料。 

* **設定自訂工具以讀取資料**：如果 Azure 監視器診斷尚未支援您目前的 SIEM，可以使用事件中樞 API 來設定自訂工具。 若要深入了解，請參閱[事件中樞 API](https://docs.microsoft.com/en-us/azure/event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph)。


## <a name="next-steps"></a>後續步驟

* [使用 Azure 監視器診斷將 Azure Active Directory 記錄與 Splunk 整合在一起](reporting-azure-monitor-diagnostics-splunk-integration.md)
* [解譯 Azure 監視器診斷中的稽核記錄結構描述](reporting-azure-monitor-diagnostics-audit-log-schema.md)
* [解譯 Azure 監視器診斷中的登入記錄結構描述](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)