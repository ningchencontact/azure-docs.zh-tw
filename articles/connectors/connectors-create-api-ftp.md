---
title: 連接到 FTP 伺服器-Azure Logic Apps
description: 使用 Azure Logic Apps 在 FTP 伺服器上建立、監視和管理檔案
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divswa, LADocs
ms.topic: article
ms.date: 10/15/2018
tags: connectors
ms.openlocfilehash: e5aeaa707c7a839483484c524e982204d6fe055c
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2019
ms.locfileid: "58576321"
---
# <a name="create-monitor-and-manage-ftp-files-by-using-azure-logic-apps"></a>藉由使用 Azure Logic Apps 來建立、監視及管理 FTP 檔案

您可以使用 Azure Logic Apps 和 FTP 連接器，建立自動化的工作和工作流程，以便透過您在 FTP 伺服器上的帳戶，建立、監視、傳送及接收檔案，還有其他動作，例如：

* 監視檔案何時新增或變更。
* 取得、建立、複製、更新、列出以及刪除檔案。
* 取得檔案內容與中繼資料。
* 將封存檔案解壓縮到資料夾。

您可以使用觸發程序，從 FTP 伺服器收到回應，並且讓輸出可供其他動作使用。 您可以在邏輯應用程式中使用執行動作來管理 FTP 伺服器上的檔案。 您也可以讓其他動作使用 FTP 動作的輸出。 例如，如果您定期從 FTP 伺服器取得檔案，可以藉由使用 Office 365 Outlook 連接器或 Outlook.com 連接器，傳送關於這些檔案及其內容的電子郵件。 如果您不熟悉邏輯應用程式，請檢閱[什麼是 Azure Logic Apps？](../logic-apps/logic-apps-overview.md)

## <a name="limits"></a>限制

* FTP 動作支援的唯一檔案*50 MB 或更小*除非您使用[訊息區塊處理](../logic-apps/logic-apps-handle-large-messages.md)，這可讓您超過此限制。 目前，FTP 觸發程序不支援區塊處理。

* FTP 連接器支援只明確 FTP over SSL (FTPS)，且不與隱含 FTPS 相容。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先<a href="https://azure.microsoft.com/free/" target="_blank">註冊免費的 Azure 帳戶</a>。 

* FTP 主機伺服器位址和帳戶認證

  要使用 FTP 連接器，FTP 伺服器必須可從網際網路來存取，並設定為以「被動」模式運作。 認證允許邏輯應用程式建立連線並存取 FTP 帳戶。

* [如何建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知識

* 您要在其中存取 FTP 帳戶的邏輯應用程式。 若要開始使用 FTP 觸發程序，請[建立空白邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 若要使用 FTP 動作，請使用其他觸發程序來啟動邏輯應用程式，例如**週期**觸發程序。

## <a name="connect-to-ftp"></a>連接至 FTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 登入 [Azure 入口網站](https://portal.azure.com)，如果邏輯應用程式尚未開啟，請在邏輯應用程式設計工具中開啟邏輯應用程式。

1. 針對空白邏輯應用程式，請在搜尋方塊中輸入 "ftp" 作為篩選條件。 在觸發程序清單底下，選取您想要的觸發程序。

   -或-

   若是現有的邏輯應用程式，請在想要新增動作的最後一個步驟底下，選擇 [新增步驟]，然後選取 [新增動作]。 
   在搜尋方塊中，輸入 "ftp" 作為篩選條件。 
   在動作清單底下，選取您想要的動作。

   若要在步驟之間新增動作，將指標移至步驟之間的箭號。 
   選擇顯示的加號 (**+**)，然後選取 [新增動作]。

1. 為您的連線提供必要的詳細資料，然後選擇 [建立]。

1. 為您選取的觸發程序或動作提供必要的詳細資料，並且繼續建置邏輯應用程式的工作流程。

在要求檔案內容時，觸發程序不會取得大於 50 MB 的檔案。 若要取得大於 50 MB 的檔案，請依照下列模式：

* 使用會傳回檔案屬性的觸發程序，例如 [新增或修改檔案時 (僅限屬性)]。

* 依照具有讀取完整檔案之動作 (例如 [使用路徑取得檔案內容]) 的觸發程序進行操作，並讓動作使用[訊息區塊化](../logic-apps/logic-apps-handle-large-messages.md)。

## <a name="examples"></a>範例

<a name="file-added-modified"></a>

### <a name="ftp-trigger-when-a-file-is-added-or-modified"></a>FTP 觸發程序：當新增或修改檔案時

此觸發程序會在偵測到 FTP 伺服器上有檔案新增或變更時，啟動邏輯應用程式工作流程。 因此舉例來說，您可以新增條件，檢查檔案的內容，並且根據該內容是否符合指定條件，來決定是否取得該內容。 最後，您可以新增會取得檔案內容的動作，並將該內容放置於 SFTP 伺服器上的資料夾中。 

**企業範例**：您可以使用此觸發程序，來監視說明客戶訂單的新檔案 FTP 資料夾。 然後，您可以使用 FTP 動作 (例如**取得檔案內容**)，取得訂單的內容以進一步處理，並儲存在訂單資料庫中。

當要求檔案的內容時，觸發程序無法取得檔案超過 50 MB。 若要取得大於 50 MB 的檔案，請依照下列模式： 

* 使用會傳回檔案屬性的觸發程序，例如 [新增或修改檔案時 (僅限屬性)]。

* 依照具有讀取完整檔案之動作 (例如 [使用路徑取得檔案內容]) 的觸發程序進行操作，並讓動作使用[訊息區塊化](../logic-apps/logic-apps-handle-large-messages.md)。

有效且正常運作的邏輯應用程式需要有一個觸發程序和至少一個動作。 因此，請務必在新增觸發程序之後新增動作。

以下是顯示此觸發程序的範例：**當新增或修改檔案時**

1. 登入 [Azure 入口網站](https://portal.azure.com)，如果邏輯應用程式尚未開啟，請在邏輯應用程式設計工具中開啟邏輯應用程式。

1. 針對空白邏輯應用程式，請在搜尋方塊中輸入 "ftp" 作為篩選條件。 在觸發程序清單底下，選取此觸發程序：**當新增或修改欄位時 - FTP**

   ![尋找並選取 FTP 觸發程序](./media/connectors-create-api-ftp/select-ftp-trigger.png)  

1. 為您的連線提供必要的詳細資料，然後選擇 [建立]。

   根據預設，此連接器會以文字格式傳送檔案。 
   若要以二進位格式傳送檔案，例如，在使用編碼的位置和時間，選取**二進位傳輸**。

   ![建立 FTP 伺服器連線](./media/connectors-create-api-ftp/create-ftp-connection-trigger.png)  

1. 在 [資料夾] 方塊旁，選擇 [資料夾] 圖示以顯示清單。 若要尋找要在其中監視是否有新檔案或編輯過檔案的資料夾，請選取向右箭號 (**>**)，瀏覽至該資料夾，然後選取該資料夾。

   ![尋找並選取要監視的資料夾](./media/connectors-create-api-ftp/select-folder.png)  

   所選取的資料夾會出現在 [資料夾] 方塊中。

   ![選取的資料夾](./media/connectors-create-api-ftp/selected-folder.png)  

邏輯應用程式已具有觸發程序，接著請新增在邏輯應用程式發現新檔案或編輯過的檔案時，所要執行的動作。 針對此範例，您可以新增 FTP 動作，用以取得新內容或更新過的內容。

<a name="get-content"></a>

### <a name="ftp-action-get-content"></a>FTP 動作：取得文字

此動作會在 FTP 伺服器上有檔案新增或更新時，從該檔案取得內容。 舉例來說，您可以新增來自上一個範例中的觸發程序，以及會在有檔案新增或編輯後取得該檔案內容的動作。 

當要求檔案的內容時，觸發程序無法取得檔案超過 50 MB。 若要取得大於 50 MB 的檔案，請依照下列模式： 

* 使用會傳回檔案屬性的觸發程序，例如 [新增或修改檔案時 (僅限屬性)]。

* 依照具有讀取完整檔案之動作 (例如 [使用路徑取得檔案內容]) 的觸發程序進行操作，並讓動作使用[訊息區塊化](../logic-apps/logic-apps-handle-large-messages.md)。

以下是顯示此動作的範例：**取得內容**

1. 在該觸發程序或其他任何動作底下，選擇 [新增步驟]。 

1. 在搜尋方塊中，輸入 "ftp" 作為篩選條件。 在動作清單下方，選取此動作：**取得檔案內容 - FTP**

   ![選取 FTP 動作](./media/connectors-create-api-ftp/select-ftp-action.png)  

1. 如果您已經連線至 FTP 伺服器和帳戶，請移至下一個步驟。 否則，請提供該連線的必要詳細資料，然後選擇 [建立]。 

   ![建立 FTP 伺服器連線](./media/connectors-create-api-ftp/create-ftp-connection-action.png)

1. 在 [取得檔案內容] 動作開啟後，於 [檔案] 方塊內按一下，以顯示動態內容清單。 您現在可以選取先前步驟的輸出中所具有的屬性。 從動態內容清單中，選取 [檔案內容] 屬性，該屬性具有已新增或更新的檔案內容。  

   ![尋找並選取檔案](./media/connectors-create-api-ftp/ftp-action-get-file-content.png)

   [檔案內容] 屬性現在會出現在 [檔案] 方塊中。

   ![所選取的 [檔案內容] 屬性](./media/connectors-create-api-ftp/ftp-action-selected-file-content-property.png)

1. 儲存您的邏輯應用程式。 若要測試工作流程，請於邏輯應用程式目前監視的 FTP 資料夾中新增檔案。

## <a name="connector-reference"></a>連接器參考

如需觸發程序、 動作和限制的技術詳細資訊，其中會描述連接器的 OpenAPI (以前稱為 Swagger) 描述，檢閱[連接器的參考頁面](/connectors/ftpconnector/)。

## <a name="get-support"></a>取得支援

* 如有問題，請瀏覽 [Azure Logic Apps 論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。
* 若要提交或票選功能構想，請造訪 [Logic Apps 使用者意見反應網站](https://aka.ms/logicapps-wish)。

## <a name="next-steps"></a>後續步驟

* 了解其他 [Logic Apps 連接器](../connectors/apis-list.md)
