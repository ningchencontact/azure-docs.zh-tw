---
title: Azure 備份監視警示常見問題
description: 有關以下常見問題的解答：Azure 備份監視警示
author: srinathvasireddy
manager: sivan
ms.service: backup
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: srinathv
ms.openlocfilehash: dc68b68ffa0ae2ee4f1bdd8f2c7ac0d7ff5ebb8c
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465846"
---
# <a name="azure-backup-monitoring-alert---faq"></a>Azure 備份監視警示-常見問題
本文會回答有關 Azure 監視警示的常見問題。

## <a name="configure-azure-backup-reports"></a>設定 Azure 備份報告

### <a name="how-do-i-check-if-reporting-data-has-started-flowing-into-a-storage-account"></a>如何確認報告資料是否已開始流入儲存體帳戶？
請前往您所設定的儲存體帳戶，然後選取容器。 如果容器有 insights-logs-azurebackupreport 的項目，則表示報告資料已經開始流入。

### <a name="what-is-the-frequency-of-data-push-to-a-storage-account-and-the-azure-backup-content-pack-in-power-bi"></a>將資料推送到儲存體帳戶和 Power BI 中 Azure 備份內容套件的頻率為何？
  針對使用時間尚未達一天的使用者，大約需要 24 小時的時間，才能將資料推送到儲存體帳戶。 完成此初始推送之後，就會依下圖所示的頻率重新整理資料。

  * 與 [作業]、[警示]、[備份項目]、[保存庫]、[受保護的伺服器]及 [原則] 相關的資料會在記錄資料的同時推送到客戶儲存體帳戶。

  * 與 [儲存體] 相關的資料會每 24 小時推送到客戶儲存體帳戶。

       ![Azure 備份報表資料推送頻率](./media/backup-azure-configure-reports/reports-data-refresh-cycle.png)

  * Power BI 具有[一天一次的排程重新整理](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/#what-can-be-refreshed)。 您可以手動重新整理 Power BI 中內容套件的資料。

### <a name="how-long-can-i-retain-reports"></a>報告可以保留多久？
設定儲存體帳戶時，您可以為儲存體帳戶中的報告資料選取保留期間。 請依照[針對報告設定儲存體帳戶](backup-azure-configure-reports.md#configure-storage-account-for-reports)一節中的步驟 6 進行操作。 您也可以根據需求，[在 Excel 中分析報告](https://powerbi.microsoft.com/documentation/powerbi-service-analyze-in-excel/)並儲存它們，以延長保留期間。

### <a name="will-i-see-all-my-data-in-reports-after-i-configure-the-storage-account"></a>在設定儲存體帳戶之後，我是否會在報告中看到所有的資料？
 在您設定儲存體帳戶之後產生的所有資料都會被推送到儲存體帳戶，並會出現在報告中。 系統不會推送進行中的作業來提供報告。 在作業完成或失敗之後，才會將其傳送至報告。

### <a name="if-i-already-configured-the-storage-account-to-view-reports-can-i-change-the-configuration-to-use-another-storage-account"></a>如果我已經設定儲存體帳戶以檢視報告，是否可以變更設定以使用其他儲存體帳戶？
是。您可以變更設定以指向不同的儲存體帳戶。 請在您連線到「Azure 備份」內容套件時，使用新設定的儲存體帳戶。 此外，在設定不同的儲存體帳戶之後，新資料便會流入這個儲存體帳戶。 較舊的資料 (在您變更設定之前的資料) 仍會保留在較舊的儲存體帳戶中。

### <a name="can-i-view-reports-across-vaults-and-subscriptions"></a>我是否可以跨保存庫和訂用帳戶檢視報告？
是。您可以在不同的保存庫上設定相同的儲存體帳戶，以檢視跨保存庫的報告。 您也可以針對跨訂用帳戶的保存庫設定相同的儲存體帳戶。 接著，您便可以在於 Power BI 中連線至「Azure 備份」內容套件時，使用這個儲存體帳戶來檢視報告。 所選儲存體帳戶必須與「復原服務」保存庫位於相同的區域。

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Azure 備份代理程式作業狀態需要多久時間才會反映在入口網站中？
Azure 入口網站最多可能需要 15 分鐘，才會反映 Azure 備份代理程式作業狀態。

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>當備份作業失敗時，需要多久的時間才會引發警示？
在 Azure 備份失敗的 20 分鐘內就會引發警示。

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>是否會有已設定通知但不會傳送電子郵件的情況？
是的。 在下列情況下，不會傳送通知。

* 如果通知設為每小時，而且在一小時內引發警示並加以解決
* 作業遭到取消時
* 如果第二項備份作業失敗，因為原始備份作業正在進行中

## <a name="recovery-services-vault"></a>復原服務保存庫

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Azure 備份代理程式作業狀態需要多久時間才會反映在入口網站中？
Azure 入口網站最多可能需要 15 分鐘，才會反映 Azure 備份代理程式作業狀態。

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>當備份作業失敗時，需要多久的時間才會引發警示？
在 Azure 備份失敗的 20 分鐘內就會引發警示。

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>是否會有已設定通知但不會傳送電子郵件的情況？
是的。 在下列情況下，不會傳送通知。

* 如果通知設為每小時，而且在一小時內引發警示並加以解決
* 作業遭到取消時
* 如果第二項備份作業失敗，因為原始備份作業正在進行中

## <a name="next-steps"></a>後續步驟

閱讀其他常見問題集：

- Azure VM 的相關[常見問題](backup-azure-vm-backup-faq.md)。
- 「Azure 備份」代理程式的相關[常見問題](backup-azure-file-folder-backup-faq.md)
