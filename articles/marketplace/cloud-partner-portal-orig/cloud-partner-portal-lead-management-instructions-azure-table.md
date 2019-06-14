---
title: Azure 資料表 |Azure Marketplace
description: 針對 Azure 資料表設定潛在客戶管理。
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: pabutler
ms.openlocfilehash: a1bcab9816627b453ba8b20b7bcd9402c2dfd151
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66240652"
---
# <a name="lead-management-instructions-for-azure-table"></a>Azure 資料表的潛在客戶管理指示

此文章說明如何設定 Azure 資料表以儲存業務潛在客戶。 Azure 資料表能讓您儲存並自訂客戶資訊。


## <a name="how-to-configure-azure-table"></a>如何設定 Azure 資料表

1. 如果您沒有 Azure 帳戶，您可以[建立免費的試用帳戶](https://azure.microsoft.com/pricing/free-trial/)。
2. 您的 Azure 帳戶啟用後，登入[Azure 入口網站](https://portal.azure.com)。
3. 在 Azure 入口網站中，建立儲存體帳戶，使用下列程序。  
    1. 選取  **+ 建立資源**左側的功能表列中。  **新增**（刀鋒視窗） 窗格會顯示右邊。
    2. 選取 **儲存體**中**新增**窗格。  A**精選**清單會顯示在右邊。
    3. 選取 **儲存體帳戶**開始建立帳戶。  請依照下列文章中的指示[建立儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)。

    ![建立 Azure 儲存體帳戶的步驟](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragecreate.png)

    如需有關儲存體帳戶的詳細資訊，請選取[快速入門教學課程](https://docs.microsoft.com/azure/storage/)。  如需儲存體定價的詳細資訊，請參閱[儲存體定價](https://azure.microsoft.com/pricing/details/storage/)。

4. 等候佈建您的儲存體帳戶的程序，通常只需要幾分鐘的時間。  然後存取您的儲存體帳戶，從**首頁**選取 Azure 入口網站頁面**查看所有資源**或選取**所有資源**從左側導覽列在 Azure 入口網站的功能表列。

    ![存取您的 Azure 儲存體帳戶](./media/cloud-partner-portal-lead-management-instructions-azure-table/azure-storage-access.png)

5. 從您儲存體帳戶] 窗格中，複製 [儲存體帳戶連接字串，該索引鍵，並將它貼至**儲存體帳戶連接字串**Cloud Partner 入口網站上的欄位。 連接字串的範例為：

```sql
DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
```

  ![Azure 儲存體金鑰](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragekeys.png)

您可以使用[Azure 儲存體總管](https://azurestorageexplorer.codeplex.com/)或其他類似的工具，以查看您的儲存體資料表中的資料。 您也可以從 Azure 資料表匯出資料。


## <a name="use-microsoft-flow-with-an-azure-table-optional"></a>使用 Microsoft Flow 與 Azure 資料表 (*選擇性*) 

您可以使用 [Microsoft Flow](https://docs.microsoft.com/flow/) 在每次有潛在客戶新增至 Azure 資料表時自動通知。 如果您還沒有帳戶，您可以[註冊免費帳戶](https://flow.microsoft.com/)。


### <a name="lead-notification-example"></a>潛在客戶通知範例

使用此範例做為指南，建立新的潛在客戶新增至 Azure 資料表時，會自動傳送電子郵件通知的基本流程。 此範例會設定表格儲存體更新時，每小時傳送潛在客戶資訊的週期。

1. 登入您的 Microsoft Flow 帳戶。
2. 在左側導覽列上，選取 [我的流程]  。
3. 在頂端導覽列上，選取 [+ 新增]  。  
4. 在下拉式清單上，選取 [+ 從空白建立] 
5. 在 [從空白建立流程] 底下，選取 [從空白建立]  。

   ![從空白建立新流程](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-create-from-blank.png)

6. 在連接器和觸發程序搜尋頁面上中，選取 [觸發程序]  。
7. 在 [觸發程序]  底下，選取 [週期]  。
8. 在 [週期]  視窗中，保留 [間隔]  的預設設定 1。 從 [頻率]  下拉式清單中，選取 [小時]  。

   >[!NOTE] 
   >雖然此範例使用 1 小時的間隔，但是您可以選取最適合您業務需求的間隔和頻率。

   ![設定循環頻率 1 小時](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-recurrence-dropdown.png)

9. 選取 [+ 新步驟]  。
10. 搜尋「取得過去時間」，然後選取 [動作] 底下的 [取得過去時間]  。 

    ![尋找並選取取得過去時間動作](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-search-getpasttime.png)

11. 在 [取得過去時間]  視窗中，將 [間隔]  設為 1。  從 [時間單位]  下拉式清單中，選取 [小時]  。
    >[!IMPORTANT] 
    >請確定此間隔及時間單位符合您針對 [週期] 設定的 [間隔] 和 [頻率]。

    ![設定取得過去時間間隔](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getpast-time.png)

    >[!TIP] 
    >您可以隨時檢查您的流程，以確認每個步驟都已正確設定。 若要檢查您的流程，請從 [流程] 功能表列中選取 [流程檢查工具]  。

在下一組步驟中，您將連接至 Azure 資料表，並設定處理邏輯來處理新的潛在客戶。

1. 在「取得過去時間」步驟中，選取 [+ 新步驟]  ，然後搜尋「取得實體」。
2. 在 [動作]  底下，選取 [取得實體]  ，然後選取 [顯示進階選項]  。
3. 在 [取得實體]  視窗中，提供下列欄位的資訊：

   - **資料表** – 輸入您 Azure 資料表儲存體的名稱。 下一個螢幕擷取會在針對此範例輸入 “MarketPlaceLeads” 時顯示提示。 

     ![為 Azure 資料表名稱挑選一個自訂值](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-table-name.png)

   - **篩選查詢**– 按一下此欄位與**取得過去時間**圖示會顯示在快顯視窗。 選取 [過去時間]  以使用這個作為時間戳記來篩選查詢。 或者，您可以將下列函式貼到欄位：CreatedTime `gt datetime'@{body('Get_past_time')}'` 

     ![設定篩選查詢函式](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-filterquery.png)

4. 選取 [新增步驟]  ，針對新的潛在客戶加入掃描 Azure 資料表的條件。

   ![使用 [新增步驟] 加入掃描 Azure 資料表的條件](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-add-filterquery-new-step.png)

5. 在 [選擇動作]  視窗中，選取 [動作]  ，然後選取 [條件]  控制項。

     ![加入條件控制項](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-action-condition-control.png)

6. 在 [條件]  視窗中，選取 [選擇值]  欄位，然後選取快顯視窗中的 [運算式]  。
7. 將 `length(body('Get_entities')?['value'])` 貼入 [fx] 欄位。 選取 [確定]  來新增此函式。 若要完成設定條件：

   - 從下拉式清單中選取 [大於]。
   - 輸入 0 作為值 

     ![將函式加入至條件](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-fx0.png)

8. 根據條件的結果設定要採取的動作。

     ![根據條件結果設定動作](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-pick-action.png)

9. 如果條件解析為 **If no**，則不會執行任何動作。 
10. 如果條件解析為 **If yes**，觸發連接 Office 365 帳戶傳送電子郵件的動作。 選取 [新增動作]  。
11. 選取 [傳送電子郵件]  。 
12. 在 [傳送電子郵件]  視窗中，提供下列欄位的資訊：

    - **收件者** - 輸入將會收到此通知的每個人的電子郵件地址。
    - **主旨** – 提供電子郵件的主旨。 例如: 新的潛在客戶！
    - **主體**： 加入您想要納入每封電子郵件的文字 (選擇性)，然後貼在本文 `body('Get_entities')?['value']` 中，作為要插入潛在客戶資訊的函式。

      >[!NOTE] 
      >您可以將額外的靜態或動態資料點插入到這封電子郵件的本文。

      ![設定電子郵件，以供潛在客戶通知使用](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-emailbody-fx.png)

13. 選取 [儲存]  以儲存流程。 Microsoft Flow 將會自動測試流程中是否有錯誤。 如果沒有任何錯誤，您的流程會在儲存之後開始執行。

下一個螢幕擷取會顯示最終流程外觀的範例。

[![最後一個流程順序](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end-thmb.png)](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end.png)

(*按一下影像以放大。* )


### <a name="manage-your-flow"></a>管理您的流程

執行流程之後加以管理很容易。  您可以完全控制您的流程。 例如，您可以停止流程、編輯流程、查看執行歷程記錄，以及取得分析。 下一個螢幕擷取會顯示可用來管理流程的選項。 

 ![管理流程](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-manage-completed.png)

流程會繼續執行，直到您使用 [關閉流程]  選項停止為止。

如果您未收到任何潛在客戶電子郵件通知，表示新的潛在客戶還未加入至 Azure 資料表。 如果有任何流程失敗，您會收到一封電子郵件，如下一個螢幕擷取中的範例所示。

 ![流程失敗電子郵件通知](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-failure-note.png)


## <a name="next-steps"></a>後續步驟

[設定潛在客戶](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads)
