---
title: Azure 資料表 |Azure Marketplace
description: 針對 Azure 資料表設定潛在客戶管理。
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: evansma
ms.openlocfilehash: 7151be3ac9f55825fd2e9dde35c9afda6a30726a
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/22/2019
ms.locfileid: "69902633"
---
# <a name="configure-lead-management-using-an-azure-table"></a>使用 Azure 資料表設定潛在客戶管理

如果合作夥伴中心未明確支援您的客戶關係管理 (CRM) 系統來接收 Azure Marketplace 和 AppSource 潛在客戶, 您可以使用 Azure 資料表來處理這些潛在客戶。 然後您可以選擇匯出資料, 並將它匯入到您的 CRM 系統。 本文中的指示將逐步引導您建立 Azure 儲存體帳戶, 以及該帳戶下的 Azure 資料表。 此外, 您可以使用 Microsoft Flow 建立新的流程, 以在您的供應專案收到潛在客戶時傳送電子郵件通知。

## <a name="configure-azure-table"></a>設定 Azure 資料表

1. 如果您沒有 Azure 帳戶，您可以[建立免費的試用帳戶](https://azure.microsoft.com/pricing/free-trial/)。
2. 當您的 Azure 帳戶處於作用中狀態時, 請登入[Azure 入口網站](https://portal.azure.com)。
3. 在 Azure 入口網站中, 使用下列程式來建立儲存體帳戶。  
    1. 選取左側功能表列中的 [ **+ 建立資源**]。  **新**的窗格 (blade) 就會顯示在右邊。
    2. 在 [**新增**] 窗格中選取 [**儲存體**]。  **精選**清單會顯示在右側。
    3. 選取 [**儲存體帳戶**] 以開始建立帳戶。  遵循[建立儲存體帳戶一](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)文中的指示。

        ![建立 Azure 儲存體帳戶的步驟](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-create.png)

        如需儲存體帳戶的詳細資訊, 請選取[快速入門教學](https://docs.microsoft.com/azure/storage/)課程。  如需儲存體定價的詳細資訊，請參閱[儲存體定價](https://azure.microsoft.com/pricing/details/storage/)。

4. 等到您的儲存體帳戶布建完成, 通常需要幾分鐘的時間。  接著, 選取 [**查看您的所有資源**], 或從 Azure 入口網站的左側導覽功能表列中選取 [**所有資源**], 以從 Azure 入口網站的**首頁**存取您的儲存體帳戶。

    ![存取您的 Azure 儲存體帳戶](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-access.png)

5. 從 [儲存體帳戶] 窗格中, 選取 [**存取金鑰**], 並複製金鑰的*連接字串*值。 儲存此值, 因為這是您必須在發佈入口網站中提供的*儲存體帳戶連接字串*值, 才能接收 marketplace 供應專案的潛在客戶。 

    連接字串的範例如下:

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
    ```

    ![Azure 儲存體金鑰](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-keys.png)

6. 從 [儲存體帳戶] 窗格中, 選取 [資料表], 然後選取 [ **+ 資料表** **]** 來建立資料表。 輸入資料表的名稱, 然後選取 **[確定]** 。 如果您想要設定 MS Flow 在收到潛在客戶時接收電子郵件通知, 請儲存此值。

    ![Azure 資料表](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-tables.png)

    您可以使用 [Azure 儲存體總管](https://archive.codeplex.com/?p=azurestorageexplorer) \(英文\) 或任何其他工具，來檢視儲存體資料表中的資料。 您也可以匯出 Azure 資料表中的資料。 

## <a name="optional-use-microsoft-flow-with-an-azure-table"></a>選擇性搭配使用 Microsoft Flow 與 Azure 資料表  

您可以使用 [Microsoft Flow](https://docs.microsoft.com/flow/) 在每次有潛在客戶新增至 Azure 資料表時自動通知。 如果您沒有帳戶, 您可以[註冊免費帳戶](https://flow.microsoft.com/)。

### <a name="lead-notification-example"></a>潛在客戶通知範例

使用此範例作為指南，以建立簡單的流程，在新的潛在客戶新增至 Azure 資料表時，自動傳送電子郵件通知。 這個範例會設定當資料表儲存體已更新時, 每小時傳送潛在客戶資訊的週期。

1. 登入您的 Microsoft Flow 帳戶。
2. 在左側導覽列上，選取 [我的流程]。
3. 在頂端導覽列上，選取 [+ 新增]。  
4. 在下拉式清單中, 選取 [ **+ 排程-從空白**]

   ![我的流程 * * + 已排程-從空白 * *](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-scheduled-from-blank.png)

5.  在 [依間隔*重複每個*選取 "1"] 和 [小時] 的 [*建立排程的流程*] 視窗中, 為 [頻率]。 此外, 如有需要, 請提供流程名稱。 選取 [建立]。

    >[!Note]
    >雖然此範例使用 1 小時的間隔，但是您可以選取最適合您業務需求的間隔和頻率。

    ![建立排定的流程。](./media/commercial-marketplace-lead-management-instructions-azure-table/build-scheduled-flow.png)

6. 選取 [+ 新步驟]。
7. 在 [*選擇動作*] 視窗中, 搜尋「取得過去時間」, 然後選取 [動作] 底下的 [**取得過去時間**]。

   ![選擇動作。](./media/commercial-marketplace-lead-management-instructions-azure-table/choose-an-action.png)

8. 在 [取得過去時間] 視窗中，將 [間隔] 設為 1。 從 [時間單位] 下拉式清單中，選取 [小時]。

    >[!Important]
    >請確定此間隔和時間單位符合您在步驟5中為週期設定的間隔和頻率。

    ![設定取得過去時間間隔](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-getpast-time.png)

>[!TIP] 
>您可以隨時檢查您的流程，以確認每個步驟都已正確設定。 若要檢查您的流程，請從 [流程] 功能表列中選取 [流程檢查工具]。

在下一組步驟中，您將連接至 Azure 資料表，並設定處理邏輯來處理新的潛在客戶。

9. 在 [取得過去時間] 步驟之後, 選取 [ **+ 新增步驟**], 然後在 [*選擇動作*] 視窗中搜尋「取得實體」。
10. 在 [**動作**] 底下, 選取 **[取得實體 (Azure 表格儲存體)** ]。
11. 在 [ **Azure 表格儲存體**] 視窗中, 提供下欄欄位的資訊, 然後選取 [**建立**]:

    * 連線*名稱*–針對您在此流程與 Azure 資料表之間建立的連線提供有意義的名稱。
    * *儲存體帳戶名稱*–提供 Azure 資料表的儲存體帳戶名稱。 您可以在儲存體帳戶的 [**存取金鑰**] 頁面中找到此功能。
    * *共用儲存體金鑰*–為您的 Azure 資料表的存放區帳戶提供金鑰值。 您可以在儲存體帳戶的 [**存取金鑰**] 頁面中找到此功能。

        ![Azure 資料表儲存體。](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-storage.png)

    按一下 [建立] 之後, 您會看到 [*取得實體*] 視窗。 這裡選取 [ **Show advanced options** ], 並提供下欄欄位的資訊:

       * *資料表*–選取您的 Azure 表格儲存體名稱 (從步驟6的指示, 瞭解如何設定 azure 資料表)。 下一個螢幕擷取畫面會顯示針對此範例選取 "marketplaceleads" 資料表時的提示。

            ![Azure 資料表取得實體。](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities.png)

        * *篩選查詢*–選取此欄位, 並將此函數貼到欄位中:`Timestamp gt datetime'@{body('Get_past_time')}'`

            ![Azure 資料表取得實體-篩選 Querry。](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities-filter-query.png)

12. 既然您已完成設定 Azure 資料表的連線, 請選取 [**新增步驟**] 來新增條件, 以掃描 azure 資料表中的新潛在客戶。 

13. 在 [**選擇動作**] 視窗中, 選取 [**動作**], 然後選取 [**條件] 控制項**。

    ![Azure 資料表-選擇動作。](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-choose-an-action.png)

14. 在 [條件] 視窗中，選取 [選擇值] 欄位，然後選取快顯視窗中的 [運算式]。

15. 將 `length(body('Get_entities')?['value'])` 貼入 [fx] 欄位。 選取 [確定] 來新增此函式。 

16. 若要完成設定條件：
    1. 從下拉式清單中選取 [大於]。
    2. 輸入 0 作為值

        ![Azure 資料表-條件。](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition.png)

在接下來的幾個步驟中, 您將根據條件的結果設定要採取的動作。

* 如果條件解析為 **If no**，則不會執行任何動作。
* 如果條件解析為 **If yes**，觸發連接 Office 365 帳戶傳送電子郵件的動作。 

17. 在 **[如果是]** 底下選取 [**新增動作**]。

    ![Azure 資料表-條件, * * 如果是 * *。](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes.png)

18. 選取 **[傳送電子郵件 (Office 365 Outlook)** ]。

    ![Azure 資料表-條件, * * 如果是 * *, 則傳送電子郵件。](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-send-email.png)

    >[!Note]
    >如果您想要使用不同的電子郵件提供者搜尋, 請改為選取 [傳送電子郵件通知 (郵件)] 做為動作。 指示會示範如何使用 Office 365 Outlook 進行設定, 但不同的電子郵件提供者的指示類似。

19. 在 [ **Office 365 Outlook** ] 視窗中, 提供下欄欄位的資訊:

    1. **收件者** - 輸入將會收到此通知的每個人的電子郵件地址。
    1. **主旨** – 提供電子郵件的主旨。 例如: 新的潛在客戶！
    1. 內文-新增您想要包含在每個電子郵件中的文字 (選擇性), 然後貼`body('Get_entities')?['value']`上 [主體]。

    >[!Note]
    >您可以將額外的靜態或動態資料點插入到這封電子郵件的本文。

    ![Azure 資料表-條件, * * 如果是 * *, 則為 Office 365 Outlook 視窗。](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-outlook.png)

20. 選取 [儲存] 以儲存流程。 Microsoft Flow 將會自動測試流程中是否有錯誤。 如果沒有任何錯誤，您的流程會在儲存之後開始執行。

下一個螢幕擷取會顯示最終流程外觀的範例。

![最終流程的範例。](./media/commercial-marketplace-lead-management-instructions-azure-table/final-flow.png)

### <a name="manage-your-flow"></a>管理您的流程

執行流程之後加以管理很容易。 您可以完全控制您的流程。 例如，您可以停止流程、編輯流程、查看執行歷程記錄，以及取得分析。 下一個螢幕擷取會顯示可用來管理流程的選項。 

 ![管理流程](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-manage-completed.png)

流程會繼續執行，直到您使用 [關閉流程] 選項停止為止。

如果您未收到任何潛在客戶電子郵件通知，表示新的潛在客戶還未加入至 Azure 資料表。 如果有任何流程失敗，您會收到一封電子郵件，如下一個螢幕擷取中的範例所示。

 ![流程失敗電子郵件通知](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-failure-note.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-table"></a>設定您的供應專案以將潛在客戶傳送至 Azure 資料表

當您準備好在發佈入口網站中為您的供應專案設定潛在客戶管理資訊時, 請遵循下列步驟:

1. 流覽至供應專案的 [**供應專案設定**] 頁面。
2. 選取 [潛在客戶管理] 區段下的 **[連接]** 。
3. 在 [連線詳細資料] 快顯視窗中, 選取 [ **Azure 資料表**] 作為 [**潛在客戶目的地**], 並將您在先前步驟所建立的 azure 儲存體帳戶中的連接字串貼到**儲存體帳戶連接字串**欄位.
4. 選取 [ **儲存**]。 

>[!Note]
>您必須先完成供應專案的其餘部分設定並加以發佈, 才能接收供應專案的潛在客戶。

當產生潛在客戶時, Microsoft 會將潛在客戶傳送至 Azure 資料表。 如果您已設定流程, 電子郵件也會傳送至您所設定的電子郵件地址。

![潛在客戶管理](./media/commercial-marketplace-lead-management-instructions-azure-table/lead-management.png)

![潛在客戶管理-連線詳細資料](./media/commercial-marketplace-lead-management-instructions-azure-table/connection-details.png)

![潛在客戶管理-連線詳細資料儲存體帳戶](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-connection-details.png)

