---
title: 進階 IaaS Azure 資訊安全中心資料安全性 |Microsoft Docs
description: " 了解如何在 Azure 資訊安全中心中的 iaas 啟用進階的資料安全性。 "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: monhaber
ms.assetid: ba46c460-6ba7-48b2-a6a7-ec802dd4eec2
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/08/2019
ms.author: monhaber
ms.openlocfilehash: e601bbaa0d15078fc2b19b5b7c536e3a1f6d20ad
ms.sourcegitcommit: 4891f404c1816ebd247467a12d7789b9a38cee7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2019
ms.locfileid: "65442743"
---
# <a name="advanced-data-security-for-sql-servers-on-iaas"></a>IaaS 上的 SQL server 的進階的資料安全性
IaaS 上的 SQL server 的進階的資料安全性是 SQL 的進階安全性功能的整合的套件。 目前包含呈現及緩解潛在的資料庫弱點和偵測可能表示您的資料庫威脅的異常活動的功能。

此供應項目 IaaS SQL server 的安全性根據所使用的相同基本技術[Azure SQL Database 進階資料安全性封裝](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)。


## <a name="overview"></a>概觀

進階的資料安全性 (ADS) 提供一組的進階 SQL 安全性功能，其中包含弱點評量和進階威脅防護。

* [弱點評估](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment)是容易設定的服務，可探索、追蹤及協助您修復潛在的資料庫弱點。 它提供您的安全性狀態的可視性，並包括解決安全性問題和增強資料庫攻防的步驟。
* [進階威脅防護](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)偵測到異常活動時會不尋常且有危害的意圖存取或攻擊您的 SQL server。 持續監視您的資料庫的可疑活動，並提供動作導向的安全性警示的異常資料庫存取模式。 這些警示會提供可疑活動詳細資料，以及建議的動作，若要調查並減輕威脅。

## <a name="get-started-with-ads-for-iaas"></a>開始使用 ADS iaas

下列步驟可協助您開始使用 ADS iaas。

### <a name="set-up-ads-for-iaas"></a>為 IaaS 廣告設定

**開始之前**:您需要 Log Analytics 工作區來儲存要分析的安全性記錄檔。 如果您沒有的話，則您可以輕易地中所述建立一個[在 Azure 入口網站中建立 Log Analytics 工作區](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)。

1. 將 VM 連接主控 SQL server，Log Analytics 工作區。 如需相關指示，請參閱 <<c0> [ 連線的 Windows 電腦連線到 Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows)。

1. 從 Azure Marketplace 中，移至[SQL 進階的資料安全性解決方案](https://ms.portal.azure.com/#create/Microsoft.SQLAdvancedDataSecurity)。
（您可以尋找使用 marketplace 的 [搜尋] 選項，請參閱下圖）。**SQL 進階的資料安全性**頁面隨即開啟。

    ![IaaS 的進階的資料安全性](./media/security-center-advanced-iaas-data/sql-advanced-data-security.png)

1. 按一下頁面底部的 [新增] 。 會顯示 工作場所。

    ![建立進階的資料安全性](./media/security-center-advanced-iaas-data/sql-advanced-data-create.png)

1. 選取要使用，然後按一下工作區**建立**。

   ![選取工作區](./media/security-center-advanced-iaas-data/sql-workspace.png)

1. 重新啟動[VM 的 SQL server](https://docs.microsoft.com/sql/database-engine/configure-windows/start-stop-pause-resume-restart-sql-server-services?view=sql-server-2017)。


## <a name="explore-and-investigate-security-alerts"></a>瀏覽和調查安全性警示

您可以檢視和管理目前的安全性警示。

1. 按一下 **資訊安全中心** > **安全性警示**，然後按一下 警示。

    ![找不到警示](./media/security-center-advanced-iaas-data/find-alert.png)

1. 從**遭受攻擊的資源** 欄中，按一下遭到攻擊的資源。

1. 若要檢視警示詳細資料和調查目前的威脅，並解決未來的威脅的動作，向下捲動**的一般資訊**頁面上，然後在**補救步驟**區段中，按一下**調查步驟**連結。

    ![補救步驟](./media/security-center-advanced-iaas-data/remediation-steps.png)

1. 若要檢視與觸發此警示相關聯的記錄檔，請前往**Log analytics 工作區**並執行下列步驟：

     > [!NOTE]
     > 如果**Log analytics 工作區**不會出現在左側功能表中，按一下**所有服務**，並搜尋**Log analytics 工作區**。

    1. 請務必要顯示的資料行**定價層**並**WorkspaceID**資料行。 (**Log analytics 工作區** > **編輯資料行**，新增**定價層**並**WorkspaceID**。)

     ![編輯資料行](./media/security-center-advanced-iaas-data/edit-columns.png)

    1. 按一下工作區具有警示的記錄檔。

    1. 底下**一般**功能表上，按一下 **記錄檔**

    1. 旁按一下眼睛**SQLAdvancedThreatProtection**資料表。 列出記錄檔。

     ![檢視記錄檔](./media/security-center-advanced-iaas-data/view-logs.png)

## <a name="set-up-email-notification-for-atp-alerts"></a>設定 ATP 警示的電子郵件通知 

您可以設定要產生 ASC 警示時收到電子郵件通知收件者清單。 電子郵件會包含 Azure 資訊安全中心警示，以及所有相關的詳細資料的直接連結。 

1. 移至**資訊安全中心** > **安全性原則**以及相關訂用帳戶按一下 資料列內**編輯設定 >**。

    ![訂用帳戶設定](./media/security-center-advanced-iaas-data/subscription-settings.png)

1. 從**設定**功能表上，按一下**電子郵件通知**。 
1. 在 **電子郵件地址**文字中，輸入要接收通知電子郵件地址。 您可以輸入多個電子郵件地址，使用逗號 （，） 分隔電子郵件地址。  比方說admin1@mycompany.com，admin2@mycompany.com，admin3@mycompany.com

      ![電子郵件設定](./media/security-center-advanced-iaas-data/email-settings.png)

1. 在 **電子郵件通知**設定，設定下列選項：
  
    * **傳送電子郵件通知，針對高嚴重性警示**:而不是傳送所有警示的電子郵件，請傳送只針對高嚴重性警示。
    * **也傳送電子郵件通知給訂用帳戶擁有者**:也將通知傳送給訂用帳戶擁有者。

1. 在頂端**電子郵件通知**畫面上，按一下**儲存**。

  > [!NOTE]
  > 務必按一下 **儲存**之前關閉視窗，或新**電子郵件通知**將不會儲存設定。

## <a name="explore-vulnerability-assessment-reports"></a>探索的弱點可能會評估報告

弱點評定儀表板會提供評估結果的概觀，您所有的資料庫。 您可以檢視資料庫的分佈根據 SQL Server 版本，以及與資料庫和失敗的檢查，根據風險分佈進行分佈的整體摘要傳遞失敗的摘要。

您可以檢視您的弱點可能會評估結果和報表直接從 Log Analytics。

1. 瀏覽至您的 Log Analytics 工作區與廣告解決方案。
1. 瀏覽至**解決方案**，然後選取**SQL 弱點評量**解決方案。
1. 在 **摘要**窗格中，按一下**檢視摘要**，然後選取您**SQL 弱點評定報告**。

    ![SQL 評定報告](./media/security-center-advanced-iaas-data/ads-sql-server-1.png)

    載入報表儀表板。 請確定時間範圍設定為至少是**過去 7 天**因為您的資料庫上固定每 7 天一次的排程上執行弱點評定掃描。

    ![將過去 7 天](./media/security-center-advanced-iaas-data/ads-sql-server-2.png)

1. 若要向下鑽研，如需詳細資訊，請按一下任何儀表板項目。 例如：

   1. 按一下中的弱點可能會檢查**失敗檢查摘要**區段，以檢視 Log Analytics 含有結果的資料表進行這項檢查，跨所有資料庫。 結果項目會先列出。

   1. 然後，逐一點選以檢視每個弱點，包括的弱點可能會描述和影響、 狀態、 相關聯的風險，以及實際的結果，在此資料庫的詳細資料。 您也可以查看實際的查詢執行來執行此檢查和補救的資訊來解決這項弱點。

    ![選取工作區](./media/security-center-advanced-iaas-data/ads-sql-server-3.png)

    ![選取工作區](./media/security-center-advanced-iaas-data/ads-sql-server-4.png)

1. 您可以執行任何 Log Analytics 查詢，對您的弱點評定結果資料，來交叉分析的資料，根據您的需求。

## <a name="advanced-threat-protection-for-sql-servers-on-iaas-alerts"></a>進階威脅防護，SQL server IaaS 警示
不尋常且有危害意圖存取或惡意探索 SQL Server 會產生警示。 這些事件會觸發下列警示：

### <a name="anomalous-access-pattern-alerts"></a>異常存取模式的警示

* **從不尋常的位置的存取權：** 有人從不尋常的地理位置登入 SQL Server，而使 SQL Server 的存取模式有所變更時，會觸發此警示。 可能的原因：
     * 攻擊者或先前的惡意採用存取您的 SQL Server。
     * 合法使用者存取您的 SQL Server 從新的位置。
* **從可能有害的應用程式存取**： 可能有害的應用程式用來存取資料庫時，會觸發他的警示。 可能的原因：
     * 攻擊者嘗試入侵您的 SQL 使用常見攻擊工具。
     * 合法的滲透，用來測試作用中。
* **從不熟悉的主體存取**：有人使用不尋常的主體 (SQL 使用者) 登入 SQL Server，而使 SQL Server 的存取模式有所變更時，會觸發此警示。 可能的原因：
     * 攻擊者或先前的惡意採用存取您的 SQL Server。 
     * 合法使用者存取您的 SQL Server，從與新的主體。
* **暴力 SQL 認證**：有使用不同認證的異常大量登入失敗時，會觸發此警示。 可能的原因：
     * 嘗試入侵您的 SQL 使用暴力密碼破解攻擊者。
     * 合法的滲透，用來測試作用中。

### <a name="potential-sql-injection-attacks-coming"></a>潛在 SQL 插入式攻擊 （即將推出）

* **SQL 插入式攻擊的弱點**：應用程式在資料庫中產生錯誤的 SQL 陳述式時，會觸發此警示。 此警示表示 SQL 插入式攻擊的可能弱點。 可能的原因：
     * 應用程式的程式碼中有缺失，而建構了錯誤的 SQL 陳述式
     * 應用程式的程式碼或預存程序在建構錯誤的 SQL 陳述式時未處理使用者輸入，這可能會遭到 SQL 插入式攻擊的侵害
* **潛在的 SQL 插入式攻擊**：若有主動攻擊是藉由 SQL 插入中已識別到的已知應用程式弱點來發動時，就會觸發此警示。 這表示有攻擊者嘗試使用有弱點的應用程式程式碼或預存程序插入惡意 SQL 陳述式。
