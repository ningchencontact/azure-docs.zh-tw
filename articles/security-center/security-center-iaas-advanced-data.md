---
title: Azure 資訊安全中心中 IaaS 的先進資料安全性 |Microsoft Docs
description: " 瞭解如何在 Azure 資訊安全中心中啟用 IaaS 的先進資料安全性。 "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ba46c460-6ba7-48b2-a6a7-ec802dd4eec2
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/29/2019
ms.author: memildin
ms.openlocfilehash: 287da68617a9527bc398df577cf8d10773fa8557
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202169"
---
# <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-public-preview"></a>Azure 上 SQL server 的 Advanced data security 虛擬機器 (公開預覽)
Azure 上 SQL Server 的 advanced data security 虛擬機器是先進 SQL 安全性功能的整合套件。 它目前 (處於公開預覽狀態) 包含可呈現和減輕潛在資料庫弱點, 以及偵測可能表示對資料庫有威脅之異常活動的功能。 

適用于 Azure Vm SQL server 的此安全性供應專案, 是以[Azure SQL Database Advanced Data security 套件](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)中所用的相同基本技術為基礎。


## <a name="overview"></a>總覽

先進的資料安全性提供一組先進的 SQL 安全性功能, 其中包含弱點評估和先進的威脅防護。

* [弱點評估](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment)是容易設定的服務，可探索、追蹤及協助您修復潛在的資料庫弱點。 它會顯示您的安全性狀態, 並包含解決安全性問題和增強資料庫防護的步驟。
* [先進的威脅防護](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)會偵測異常活動, 指出不尋常且可能有害的嘗試存取或惡意探索您的 SQL server。 它會持續監視您的資料庫是否有可疑的活動, 並針對異常的資料庫存取模式提供動作導向的安全性警示。 這些警示會提供可疑的活動詳細資料, 以及調查和緩和威脅的建議動作。

## <a name="get-started-with-advanced-data-security-for-sql-on-azure-vms"></a>開始使用 Azure Vm 上的 SQL Advanced Data Security

下列步驟可讓您開始使用 Azure Vm 上 SQL 的先進資料安全性公開預覽版。

### <a name="set-up-advanced-data-security-for-sql-on-azure-vms"></a>在 Azure Vm 上設定 SQL 的 Advanced Data Security

**開始之前**:您需要 Log Analytics 工作區來儲存要分析的安全性記錄。 如果您沒有帳戶, 您可以輕鬆地建立一個, 如在[Azure 入口網站中建立 Log Analytics 工作區](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)中所述。

1. 將裝載 SQL server 的 VM 連線到 Log Analytics 工作區。 如需指示, 請參閱[將 Windows 電腦連線到 Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows)。

1. 從 Azure Marketplace, 移至[SQL Advanced Data Security 解決方案](https://ms.portal.azure.com/#create/Microsoft.SQLAdvancedDataSecurity)。
(您可以使用 [marketplace 搜尋] 選項來尋找它, 如下圖所示)。[ **SQL Advanced Data Security** ] 頁面隨即開啟。

    ![IaaS 的先進資料安全性](./media/security-center-advanced-iaas-data/sql-advanced-data-security.png)

1. 按一下 [建立]。 隨即顯示工作場所。

    ![Advanced Data Security 建立](./media/security-center-advanced-iaas-data/sql-advanced-data-create.png)

1. 選取要使用的工作區, 然後按一下 [**建立**]。

   ![選取工作區](./media/security-center-advanced-iaas-data/sql-workspace.png)

1. 重新開機[VM 的 SQL server](https://docs.microsoft.com/sql/database-engine/configure-windows/start-stop-pause-resume-restart-sql-server-services?view=sql-server-2017)。


## <a name="explore-and-investigate-security-alerts"></a>探索和調查安全性警示

您可以查看和管理目前的安全性警示。

1. 按一下 [**資訊安全中心** > **安全性警示**], 然後按一下警示。

    ![尋找警示](./media/security-center-advanced-iaas-data/find-alert.png)

1. 從 [受**攻擊的資源**] 資料行中, 按一下已受到攻擊的資源。

1. 若要查看用來調查目前威脅及解決未來威脅的警示詳細資料和動作, 請在 [**一般資訊**] 頁面上按一下, 然後在 [**補救步驟**] 區段中, 按一下 [**調查步驟**] 連結。

    ![補救步驟](./media/security-center-advanced-iaas-data/remediation-steps.png)

1. 若要查看與觸發警示相關聯的記錄, 請移至**Log analytics 工作區**, 然後執行下列步驟:

     > [!NOTE]
     > 如果**log analytics 工作區**未出現在左側功能表上, 請按一下 [**所有服務**], 然後搜尋**log analytics 工作區**。

    1. 請確定資料行顯示的是 [**定價層**] 和 [ **WorkspaceID** ] 資料行。 (**Log analytics 工作區** > **編輯資料行**、新增**定價層**和**WorkspaceID**)。

     ![編輯資料行](./media/security-center-advanced-iaas-data/edit-columns.png)

    1. 按一下含有警示記錄的工作區。

    1. 在 [**一般**] 功能表底下, 按一下 [**記錄**]

    1. 按一下 [ **SQLAdvancedThreatProtection**資料表] 旁的眼睛。 記錄會列出。

     ![查看記錄](./media/security-center-advanced-iaas-data/view-logs.png)

## <a name="set-up-email-notification-for-atp-alerts"></a>設定 ATP 警示的電子郵件通知 

您可以設定要在產生 ASC 警示時接收電子郵件通知的收件者清單。 此電子郵件包含 Azure 資訊安全中心中警示的直接連結, 以及所有相關的詳細資料。 

1. 移至**資訊安全中心** > **定價 & 設定**, 然後按一下相關的訂用帳戶

    ![訂用帳戶設定](./media/security-center-advanced-iaas-data/subscription-settings.png)

1. 從 [**設定**] 功能表按一下 [**電子郵件通知**]。 
1. 在 [**電子郵件地址**] 文字方塊中, 輸入要接收通知的電子郵件地址。 您可以輸入一個以上的電子郵件地址, 方法是以逗號 (,) 分隔電子郵件地址。  admin1@mycompany.com例如,、admin2@mycompany.comadmin3@mycompany.com

      ![電子郵件設定](./media/security-center-advanced-iaas-data/email-settings.png)

1. 在**電子郵件通知**設定中, 設定下列選項:
  
    * **傳送高嚴重性警示的電子郵件通知**:不是傳送所有警示的電子郵件, 而是只傳送高嚴重性警示。
    * **也傳送電子郵件通知給訂**用帳戶擁有者:也將通知傳送給訂用帳戶擁有者。

1. 在 [**電子郵件通知**] 畫面頂端, 按一下 [**儲存**]。

  > [!NOTE]
  > 在關閉視窗之前, 請務必按一下 [**儲存**], 否則不會儲存新的**電子郵件通知**設定。

## <a name="explore-vulnerability-assessment-reports"></a>探索弱點評定報告

[弱點評定] 儀表板可讓您大致瞭解所有資料庫的評量結果。 您可以根據 SQL Server 版本來查看資料庫的散發, 以及失敗與傳遞資料庫的摘要, 以及根據風險散發而失敗檢查的整體摘要。

您可以直接從 Log Analytics 查看您的弱點評估結果和報告。

1. 使用 Advanced Data Security 解決方案, 流覽至您的 Log Analytics 工作區。
1. 流覽至 [**方案**], 然後選取 [ **SQL 弱點評定**] 解決方案。
1. 在 [**摘要**] 窗格中, 按一下 [**查看摘要**], 然後選取您的**SQL 弱點評定報告**。

    ![SQL 評定報表](./media/security-center-advanced-iaas-data/ads-sql-server-1.png)

    報表儀表板會載入。 請確定時間範圍至少已設定為**過去7天**, 因為弱點評估掃描是在您的資料庫上執行, 每7天一次的固定排程。

    ![設定過去7天](./media/security-center-advanced-iaas-data/ads-sql-server-2.png)

1. 若要向下切入以取得更多詳細資料, 請按一下任何儀表板元素。 例如:

   1. 按一下 [**失敗的檢查摘要**] 區段中的弱點檢查, 以查看 Log Analytics 資料表, 其中包含跨所有資料庫的此檢查結果。 會先列出具有結果的那些記錄。

   1. 然後, 按一下 [流覽] 以查看每個弱點的詳細資料, 包括弱點描述和影響、狀態、相關風險, 以及此資料庫上的實際結果。 您也可以查看執行此檢查所執行的實際查詢, 以及解決此弱點的補救資訊。

    ![選取工作區](./media/security-center-advanced-iaas-data/ads-sql-server-3.png)

    ![選取工作區](./media/security-center-advanced-iaas-data/ads-sql-server-4.png)

1. 您可以對您的弱點評量結果資料執行任何 Log Analytics 查詢, 以根據您的需求來分割和骰子資料。

## <a name="advanced-threat-protection-for-sql-servers-on-azure-vms-alerts"></a>Azure Vm 上 SQL Server 的先進威脅防護警示
警示是由不尋常且可能有害的嘗試存取或惡意探索 SQL Server 所產生。 這些事件會觸發下列警示：

### <a name="anomalous-access-pattern-alerts-supported-in-public-preview"></a>異常存取模式警示 (在公開預覽中支援)

* **從不尋常的位置存取:** 有人從不尋常的地理位置登入 SQL Server，而使 SQL Server 的存取模式有所變更時，會觸發此警示。 可能的原因:
     * 攻擊者或先前的惡意採用已存取您的 SQL Server。
     * 合法的使用者已從新的位置存取您的 SQL Server。
* **從可能有害的應用程式存取**: 當使用可能有害的應用程式來存取資料庫時, 就會觸發警示。 可能的原因:
     * 攻擊者嘗試使用常見的攻擊工具來危害您的 SQL。
     * 合法的滲透測試。
* **從不熟悉的主體存取**：有人使用不尋常的主體 (SQL 使用者) 登入 SQL Server，而使 SQL Server 的存取模式有所變更時，會觸發此警示。 可能的原因:
     * 攻擊者或先前的惡意採用已存取您的 SQL Server。 
     * 合法的使用者已從與新的主體存取您的 SQL Server。
* **暴力 SQL 認證**：有使用不同認證的異常大量登入失敗時，會觸發此警示。 可能的原因:
     * 攻擊者嘗試使用暴力密碼破解您的 SQL。
     * 合法的滲透測試。

### <a name="potential-sql-injection-attacks-coming"></a>潛在的 SQL 插入式攻擊 (即將推出)

* **SQL 插入式攻擊的弱點**：應用程式在資料庫中產生錯誤的 SQL 陳述式時，會觸發此警示。 此警示表示 SQL 插入式攻擊的可能弱點。 可能的原因:
     * 應用程式的程式碼中有缺失，而建構了錯誤的 SQL 陳述式
     * 應用程式的程式碼或預存程序在建構錯誤的 SQL 陳述式時未處理使用者輸入，這可能會遭到 SQL 插入式攻擊的侵害
* **潛在的 SQL 插入式攻擊**：若有主動攻擊是藉由 SQL 插入中已識別到的已知應用程式弱點來發動時，就會觸發此警示。 這表示有攻擊者嘗試使用有弱點的應用程式程式碼或預存程序插入惡意 SQL 陳述式。
