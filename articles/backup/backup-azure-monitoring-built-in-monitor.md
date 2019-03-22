---
title: Azure 備份：監視 Azure 備份保護工作負載
description: 監視 Azure 備份使用 Azure 入口網站的工作負載
services: backup
author: pvrk
manager: shivamg
keywords: Azure 備份;警示;
ms.service: backup
ms.topic: conceptual
ms.date: 03/05/2019
ms.author: pullabhk
ms.assetid: 86ebeb03-f5fa-4794-8a5f-aa5cbbf68a81
ms.openlocfilehash: 8d3e3257f16fe4e0f846c2268bfefc2771387de6
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "58112495"
---
# <a name="monitoring-azure-backup-workloads"></a>監視 Azure 備份工作負載

Azure 備份提供多個備份的需求和基礎結構拓撲 （在內部部署環境與 Azure） 為基礎的備份解決方案。 任何備份使用者或系統管理員應該了解跨所有的解決方案，並預期會收到重要的案例中的通知。 本文詳細說明 Azure 備份服務所提供的監視和通知功能。

## <a name="backup-jobs-in-recovery-services-vault"></a>在 復原服務保存庫的備份作業

Azure 備份提供內建監視和警示功能，針對受保護的 Azure 備份的工作負載。 在 [復原服務保存庫設定]**監視**區段提供內建的作業和警示。

![RS 保存庫內建的監視](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltmonitoring.png)

執行作業，例如設定備份、 備份、 還原、 刪除備份，並依此類推時，會產生作業。

從下列的 Azure 備份解決方案的作業如下所示：

  - Azure VM 備份
  - Azure 檔案備份
  - SQL 之類的 azure 工作負載備份
  - Azure 備份代理程式 (MAB)

不會顯示從 System Center Data Protection Manager (SC-DPM)，Microsoft Azure 備份伺服器 (MABS) 的作業。

> [!NOTE]
> Azure 工作負載，例如 SQL 備份，在 Azure Vm 中有大量的備份作業。 例如，記錄備份可以每隔 15 分鐘執行。 因此，對於這類 DB 工作負載中，會顯示唯一的使用者觸發作業。 不會顯示已排程的備份作業。

## <a name="backup-alerts-in-recovery-services-vault"></a>在 復原服務保存庫的備份警示

警示是主要使用者，會收到通知，讓他們可以採取相關動作的案例。 **備份警示**區段會顯示 Azure 備份服務所產生的警示。 這些警示由服務定義和自訂使用者，無法建立任何警示。

### <a name="alert-scenarios"></a>警示的案例
下列案例可警示的情況下為服務所定義。

  - 備份/還原失敗
  - 成功但有警告的備份
  - 停止保護保留資料/停止保護並刪除資料

### <a name="exceptions-when-an-alert-is-not-raised"></a>不會引發警示時的例外狀況
有一些例外狀況時失敗不會產生警示，分別是：

  - 使用者明確地取消執行中的工作
  - 作業會失敗，因為另一個備份作業正在進行 (因為我們只需要等候上一個工作完成，在這裡做為 nothing)
  - VM 的備份作業會失敗，因為備份 Azure VM 已不存在

上述的例外狀況是從這些作業 （主要使用者觸發） 的結果顯示立即在入口網站/PS/CLI 用戶端的了解設計。 因此，使用者會立即注意的而且不需要通知。

### <a name="alerts-from-the-following-azure-backup-solutions-are-shown-here"></a>來自下列的 Azure 備份解決方案的警示會如下所示：

  - Azure VM 備份
  - Azure 檔案備份
  - SQL 之類的 azure 工作負載備份
  - Azure 備份代理程式 (MAB)

> [!NOTE]
> 這裡不會顯示警示從 System Center Data Protection Manager (SC-DPM)，Microsoft Azure 備份伺服器 (MABS)。

### <a name="alert-types"></a>警示類型
根據警示的嚴重性，警示可以定義三種類型：

  - **重大**：在主體、 備份或復原失敗 （排程或使用者觸發） 會導致產生的警示，並會顯示為重大警示，且具破壞性作業，例如刪除備份。
  - **警告**：如果備份作業成功但有幾個警告，它們會列為警告警示。
  - **參考**:目前，Azure 備份服務會不產生任何資訊的警示。

## <a name="notification-for-backup-alerts"></a>備份警示的通知

> [!NOTE]
> 只能透過 Azure 入口網站，可以完成的通知設定。 不支援 PS/CLI/REST API/Azure Resource Manager 範本支援。

一旦產生警示時，使用者會收到通知。 Azure 備份提供的內建的通知機制，透過電子郵件。 另一個則可以指定個別的電子郵件地址或通訊群組清單，就會產生警示時收到通知。 您也可以選擇是否每個個別的警示時收到通知，或將它們群組成每小時的摘要，然後收到通知。

![RS 保存庫內建電子郵件通知](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltnotification.png)

設定通知時，您會收到歡迎或簡介的電子郵件。 這可確認 Azure 備份在產生警示時，，可以傳送電子郵件給這些位址。<br>

如果頻率設定為每小時的摘要和警示已引發，並在一小時內已解決，它不會，即將推出的每小時摘要的一部分。

> [!NOTE]
> 
> * 如果破壞性作業，例如**停止保護並刪除資料**會執行，會引發警示，即使復原服務未設定通知，傳送電子郵件訂用帳戶擁有者、 系統管理員和共同管理員保存庫。
> * 若要設定通知，對於成功的作業，請使用[Log Analytics](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-workspace)。

## <a name="next-steps"></a>後續步驟

[監視 Azure 備份的工作負載，使用 Azure 監視器](backup-azure-monitoring-use-azuremonitor.md)
