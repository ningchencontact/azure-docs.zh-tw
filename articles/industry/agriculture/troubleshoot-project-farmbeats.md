---
title: 疑難排解
description: 如何針對 Azure FarmBeats 進行疑難排解。
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 88a1280fb4a91d7ba45f2d0cfe92e604cd66ff0b
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2019
ms.locfileid: "74672591"
---
# <a name="troubleshooting"></a>疑難排解

下列各節說明常見的 Azure FarmBeats 問題，以及如何加以修正。

如需其他說明，請在 farmbeatssupport@microsoft.com撰寫，並在此電子郵件中包含部署程式 .log 檔案。

 使用下列步驟來下載部署者 .log 檔案：

1. 反白顯示的圖示，然後從下拉式選單選取 [**下載**] 選項。

    ![專案伺服器陣列的節拍](./media/troubleshooting-farmbeats/download-deployer-log-1.png)

2. 在下一個畫面中，輸入您的部署程式 .log 檔案的路徑。 例如，farmbeats-deployer。

## <a name="sensor-telemetry"></a>感應器遙測

### <a name="telemetry-not-seen"></a>未看到遙測

**徵兆**：已部署裝置/感應器，而且您已將 FarmBeats 與裝置夥伴連結。但您無法在 FarmBeats 上取得/查看遙測資料。

**更正動作**：請造訪 Azure 入口網站，並遵循下列步驟：

1. 移至您的 FarmBeats 資料中樞資源群組。   
2. 選取**事件中樞**（[DatafeedEventHubNamespace ...]）。 並檢查傳入訊息的數目。   
3. 如果沒有**傳入訊息**，請聯絡您的裝置合作夥伴。  
4. 如果有內送**訊息**，請使用資料中樞和加速器記錄，以及已捕獲的遙測來 farmbeatssupport@microsoft.com。

請參閱檔中的[記錄一節](#collect-logs-manually)，以瞭解如何下載記錄。  

### <a name="dont-have-the-eventhub-connection-string"></a>沒有 Eventhub 連接字串

**更正動作**：請造訪 Datahub swagger，並遵循下列步驟：
1. 流覽至合作夥伴 API
2. 按一下 [> 立即試用-> 執行]
3. 記下您感興趣之感應器合作夥伴的合作夥伴識別碼
4. 返回合作夥伴 API，然後按一下 GET/{id}
5. 指定步驟3中的識別碼，然後按一下 [執行]
6. API 回應應具有 EventHub 連接字串

### <a name="device-appears-offline"></a>裝置顯示為離線

**徵兆**：已安裝裝置，且您的裝置夥伴已連結 FarmBeats。 裝置已上線並傳送遙測資料，但其顯示為離線。

**更正動作**：未針對此裝置設定報告間隔。 請洽詢您的裝置製造商以設定報告間隔。 

### <a name="error-deleting-a-resource"></a>刪除資源時發生錯誤

以下是刪除裝置時常見的錯誤案例：  

**訊息**：感應器中參照的裝置：有一或多個感應器與裝置相關聯。 刪除感應器，然後刪除該裝置。  

**意義**：裝置與伺服器陣列中部署的多個感應器相關聯。   

矯正**措施：**  

1. 透過加速器刪除與裝置相關聯的感應器。  
2. 如果您想要將感應器關聯至不同的裝置，請要求裝置夥伴執行相同動作。  
3. 使用刪除 API 呼叫來刪除裝置，並將 force 參數設定為 ' true '。  

**訊息**：裝置在 ParentDeviceId 中是以裝置的形式參考：有一或多個裝置與此裝置相關聯，做為子裝置。 將其刪除，然後再刪除此裝置。  

**意義**：您的裝置有其他相關聯的裝置  

**矯正措施**

1. 刪除與特定裝置相關聯的裝置  
2. 刪除特定裝置  

    > [!NOTE]
    > 如果感應器與裝置相關聯，您就無法將它刪除。 如需如何刪除相關聯感應器的詳細資訊，請參閱在取得感應器資料中[刪除感應器](get-sensor-data-from-sensor-partner.md)一節。


## <a name="issues-with-jobs"></a>作業的問題

### <a name="farmbeats-internal-error"></a>FarmBeats 內部錯誤

**訊息**： FarmBeats 內部錯誤，如需詳細資訊，請參閱疑難排解指南。

矯正**措施：這**可能是因為資料管線中的暫時性失敗所造成。 再次建立作業。 如果錯誤持續發生，請在 FarmBeats 論壇或連絡人 FarmBeatsSupport@microsoft.com的文章中新增錯誤。

## <a name="accelerator-troubleshooting"></a>加速器疑難排解

### <a name="access-control"></a>Access Control

**新增角色指派時發生錯誤**

**訊息**：找不到相符的使用者

**更正動作**：檢查您嘗試執行角色指派的電子郵件識別碼。 電子郵件識別碼必須與在 Active Directory 中為該使用者註冊的帳戶完全相符。 若錯誤持續發生，請在 FarmBeats 論壇或連絡人的文章中新增錯誤 FarmBeatsSupport@microsoft.com

### <a name="unable-to-log-in-to-accelerator"></a>無法登入加速器

**訊息**：錯誤：您沒有呼叫服務的授權。 請洽詢系統管理員以取得授權。

**更正動作**：要求系統管理員授權您存取 FarmBeats 部署。 這可以藉由在快速鍵的 [設定] 窗格中，執行 RoleAssignment Api 的 POST 或 [存取控制] 來完成。  

如果您已新增並遇到此錯誤，請重新整理頁面，然後再試一次。  如果錯誤持續發生，請在 FarmBeats 論壇或連絡人 FarmBeatsSupport@microsoft.com的文章中新增錯誤。

![專案伺服器陣列的節拍](./media/troubleshooting-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator---an-unknown-error-occurred"></a>加速器-發生未知的錯誤  

**訊息**：錯誤：發生未知的錯誤

**更正動作**：如果您讓頁面閒置太久，就會發生這種情況。 重新整理頁面。  

如果錯誤仍持續存在，請在 FarmBeats 論壇或連絡人的文章中新增錯誤 FarmBeatsSupport@microsoft.com

**即使在升級 FarmBeatsDeployment 之後，FarmBeats 加速器也不會顯示最新版本**

**更正動作**：這是因為瀏覽器中的服務工作者持續性所導致。
關閉所有已開啟快速鍵的瀏覽器索引標籤，然後關閉瀏覽器視窗。 啟動瀏覽器的新實例，然後再次載入快速鍵 URI。 這會載入新版本的加速器。

## <a name="sentinel-imagery-related-issues"></a>Sentinel 圖像相關問題

### <a name="sentinel-wrong-username-or-password"></a>Sentinel 錯誤的使用者名稱或密碼

**作業失敗訊息**;需要完整驗證才能存取此資源。

**更正動作**：重新執行安裝程式，以正確的使用者名稱和密碼來升級資料中樞。

矯正**措施：重新執行失敗**的工作，或執行5-7 天日期範圍的附屬索引作業，並檢查工作是否成功。

### <a name="sentinel-hub-wrongurlor-not-accessible"></a>Sentinel 中樞錯誤的 URL 或無法存取 

**作業失敗訊息**：糟糕，發生錯誤。 您嘗試存取的頁面是（暫時）無法使用。 

矯正**措施：**
1.  開啟 [Sentinel URL] （在瀏覽器中 https://scihub.copernicus.eu/dhus/) ，並檢查是否可存取網站。 
2.  如果無法存取網站，請檢查是否有任何防火牆/公司網路等等會封鎖網站，並採取必要的步驟以允許上述 URL。 
3.  重新執行失敗的作業，或在5-7 天的日期範圍內執行附屬索引作業，並檢查工作是否成功。  

### <a name="sentinel-server-down-for-maintenance"></a>Sentinel 伺服器關閉以進行維護

**作業失敗訊息**： Copernicus Open Access Hub 即將恢復！ 很抱歉造成您的不便，我們目前正在執行一些維護。 我們很快就會恢復上線！ 

矯正**措施：**

1.  如果在 Sentinel 伺服器上進行了任何維護活動，就會發生此問題。 
2.  如果任何作業/管線因上述原因而失敗，請在一段時間後重新提交作業。 
3.  使用者可以造訪 https://scihub.copernicus.eu/news/ ，以查看任何已規劃/非計畫的 Sentinel 維護活動的相關資訊。  
4.  重新執行失敗的作業，或在5-7 天的日期範圍內執行附屬索引作業，並檢查工作是否成功。

### <a name="sentinel-maximum-number-of-connections-reached"></a>已達到的 Sentinel 最大連接數目

**作業失敗訊息**：使用者「<username>」所達到的兩個並行流程的最大數目 

**矯正措施**
1.  如果任何作業因上述原因而失敗，則另一個部署/軟體中會使用相同的 sentinel 帳戶。 
2.  使用者可以建立新的 sentinel 帳戶，並重新執行安裝程式，以使用新的 sentinel 使用者名稱和密碼來升級資料中樞。  
3.  請重新執行失敗的工作，或在5-7 天的日期範圍內執行附屬索引作業，並檢查工作是否成功。

### <a name="sentinel-server-refused-connection"></a>Sentinel 伺服器拒絕連接 

**作業失敗訊息**：

伺服器拒絕連接的位置： http://172.30.175.69:8983/solr/dhus 

**更正動作**：如果在 Sentinel 伺服器上進行任何維護活動，就會發生此問題。 
1.  如果任何作業/管線因上述原因而失敗，請在一段時間後重新提交作業。 
2.  使用者可以造訪 https://scihub.copernicus.eu/news/ ，以查看任何已規劃/非計畫的 Sentinel 維護活動的相關資訊。  
3.  請重新執行失敗的工作，或在5-7 天的日期範圍內執行附屬索引作業，並檢查工作是否成功。


## <a name="collect-logs-manually"></a>手動收集記錄檔

[安裝並部署]( https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)至 Azure 儲存體總管。

### <a name="how-to-collect-data-hub-adf-job-logs"></a>如何收集資料中樞 ADF 作業記錄
1. 登入 https://portal.azure.com
2. 在 [**搜尋**] 文字方塊中，搜尋 FarmBeats 資料中樞資源群組。

    > [!NOTE]
    > 選取在 FarmBeats 部署時指定的資料中樞資源群組。

在資源群組儀表板中，搜尋（datahublogs ...）儲存體帳戶。 例如，datahublogsmvxmq  

1.  在 [**名稱**] 資料行中選取儲存體帳戶，以查看**儲存體帳戶**儀表板。
2.  在 [（datahubblogs ...）] 頁面中，選取 [**在 Explorer 中開啟**] 以查看**open Azure 儲存體總管**應用程式。
3.  在左面板中，選取 [（datahubblogs ...）]、[ **Blob 容器**]、[**作業記錄**]。
4.  在 [**作業-記錄**] 索引標籤中，選取 [**下載**]。
5.  選取要將記錄下載到您電腦上本機資料夾的位置。
6.  將下載的 zip 檔案以電子郵件傳送至 farmbeatssupport@microsoft.com

    ![專案伺服器陣列的節拍](./media/troubleshooting-farmbeats/collecting-logs-manually-1.png)

### <a name="how-to-collect-accelerator-adf-job-logs"></a>如何收集加速器 ADF 作業記錄

1.  登入 https://portal.azure.com
2.  在 [**搜尋**] 文字方塊中，搜尋 FarmBeats 加速器資源群組。

    > [!NOTE]
    > 選取在 FarmBeats 部署時指定的加速器資源群組。

3.  在資源群組儀表板中，搜尋 [儲存體]。 儲存體帳戶。 例如，storagedop4k
4.  在 [**名稱**] 資料行中選取儲存體帳戶，以查看儲存體帳戶儀表板。
5.  在 [（儲存 ...）] 頁面中，選取 [**在 Explorer 中開啟**] 以查看 open Azure 儲存體總管應用程式。
6.  在左面板中，< 儲存體 ...）、 **Blob 容器**，選取 **作業記錄**。
7.  在 [**作業-記錄**] 索引標籤中，選取 [**下載**]。
8.  選取要將記錄下載到您電腦上本機資料夾的位置。
9.  將下載的 zip 檔案以電子郵件傳送至 farmbeatssupport@microsoft.com


### <a name="how-to-collect-data-hub-app-service-logs"></a>如何收集資料中樞 app service 記錄

1.  登入 https://portal.azure.com
2.  在 [**搜尋**] 文字方塊中，搜尋 FarmBeats 資料中樞資源群組。

    > [!NOTE]
    > 選取在 FarmBeats 部署時指定的資料中樞資源群組。

3.  在資源群組中，搜尋（datahublogs ...）儲存體帳戶。 例如，datahublogsmvxmq
4.  在 [**名稱**] 資料行中選取儲存體帳戶，以查看**儲存體帳戶**儀表板。
5.  在 [（datahubblogs ...）] 頁面中，選取 [**在 Explorer 中開啟**] 以查看**open Azure 儲存體總管**應用程式。
6.  在左面板中，選取 [（datahubblogs ....）]、[ **Blob 容器**]、[appinsights 記錄]。
7.  在 [appinsights-記錄] 索引標籤中，選取 [**下載**]。
8.  選取路徑以將記錄下載到您電腦上的本機資料夾。
9.  將下載的資料夾以電子郵件傳送至 farmbeatssupport@microsoft.com

### <a name="how-to-collect-accelerator-app-service-logs"></a>如何收集加速器應用程式服務記錄

1.  登入 https://portal.azure.com
2.  在**搜尋**中，搜尋 FarmBeats 加速器資源群組。

    > [!NOTE]
    > 選取 FarmBeats 部署時所提供的 Farmbeats 加速器資源群組。

3.  在資源群組中，搜尋（儲存體 ...）儲存體帳戶。 例如，storagedop4k
4.  在 [**名稱**] 資料行中選取儲存體帳戶，以查看**儲存體帳戶**儀表板。
5.  在 [（儲存 ...）] 頁面中，選取 [**在 Explorer 中開啟**] 以查看**open Azure 儲存體總管**應用程式。
6.  在左面板中（[儲存體] ....）、[ **Blob 容器**] 中，選取 [appinsights-logs]。
7.  在 [appinsights-記錄] 索引標籤中，選取 [**下載**]。
8.  選取要將記錄下載到您電腦上本機資料夾的位置。
9.  將下載的資料夾以電子郵件傳送至 farmbeatssupport@microsoft.com

## <a name="known-issues"></a>已知問題

## <a name="batch-related-issues"></a>批次相關問題

**錯誤**：已達指定之訂用帳戶的 Batch 帳戶區域帳戶配額。

**更正動作**：增加配額或刪除未使用的 batch 帳戶，並重新執行部署。

### <a name="azure-active-directory-related-issues"></a>Azure Active Directory 相關問題

**錯誤**：無法將必要的設定更新為 Azure AD App d41axx40-xx21-4fbd-8xxf-97xxx9e2xxc0：許可權不足，無法完成作業。 請確定已針對 Azure AD App 正確設定上述設定。

**意義**： Azure AD 的應用程式註冊設定未正確地發生。  

**更正動作**：請要求 IT 系統管理員（擁有租使用者讀取權限）使用我們的[腳本](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect/tree/master/AppCreationScripts)來建立 Azure AD 應用程式註冊。 此腳本也會自動負責設定步驟。

**錯誤**：無法在此租使用者中建立新的 Active Directory 應用程式 "dummyname"：具有相同屬性識別碼 uri 值的另一個物件已經存在

**表示**：已有相同名稱的 Azure AD 應用程式註冊。

**更正動作**：刪除現有的 Azure AD 應用程式註冊或重複使用它進行安裝。 如果您重複使用現有的 Azure AD 將應用程式識別碼和用戶端秘密傳遞至安裝程式，並重新部署。

## <a name="inputjson-related-issues"></a>輸入 json 相關問題

從輸入 json 檔案讀取輸入**時發生錯誤**

**更正動作**：這個問題主要是因為遺漏了指定正確的輸入 json 路徑或安裝程式名稱。 進行適當的更正，並重試重新部署。

**剖析 json 輸入時發生錯誤**

**更正動作**：此問題主要是由於輸入 json 檔案中的值不正確所造成。 進行適當的更正並重試部署。

## <a name="high-cpu-usage"></a>高 CPU 用量

**錯誤**：您收到的電子郵件警示參考高 CPU 使用量警示。 

矯正**措施：** 
1.  移至您的 FarmBeats 資料中樞資源群組。
2.  選取 [App service]。  
3.  前往相應增加（App Service 方案）並選取適當的[定價層](https://azure.microsoft.com/pricing/details/app-service/windows/)

## <a name="next-steps"></a>後續步驟

如果您仍然遇到任何問題，請洽詢我們的[支援論壇](https://social.msdn.microsoft.com/Forums/home?forum=ProjectFarmBeats)。
