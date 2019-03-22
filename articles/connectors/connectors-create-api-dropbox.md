---
title: 連接到 Dropbox-Azure Logic Apps
description: 使用 Dropbox REST API 和 Azure Logic Apps上傳及管理檔案
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 03/01/2019
tags: connectors
ms.openlocfilehash: 5a1bfe8ca38fc23f09b13195fb8ca5bd443a4afd
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/21/2019
ms.locfileid: "58314411"
---
# <a name="upload-and-manage-files-in-dropbox-by-using-azure-logic-apps"></a>上傳及管理使用 Azure Logic Apps 的 Dropbox 中的檔案

使用 Dropbox 連接器與 Azure Logic Apps，您可以建立自動化工作流程，上傳及管理您的 Dropbox 帳戶中的檔案。 

本文說明如何從邏輯應用程式，連接到 Dropbox，然後新增 Dropbox**當檔案建立時**觸發程序，Dropbox**使用路徑取得檔案內容**動作。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先<a href="https://azure.microsoft.com/free/" target="_blank">註冊免費的 Azure 帳戶</a>。

* A [Dropbox 帳戶](https://www.dropbox.com/)，其中您可以免費註冊。 您的帳戶認證所需的邏輯應用程式與您的 Dropbox 帳戶之間建立連接。

* [如何建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知識。 針對此範例，您需要空白的邏輯應用程式。

## <a name="add-trigger"></a>新增觸發程序

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 在搜尋方塊下方，選擇 [全部]。 在搜尋方塊中，輸入 "dropbox" 作為篩選條件。
從觸發程序清單中，選取此觸發程序：**當檔案建立時**

   ![選取 Dropbox 觸發程序](media/connectors-create-api-dropbox/select-dropbox-trigger.png)

1. 使用您的 Dropbox 帳戶認證登入，並授與 Azure Logic Apps 存取您的 Dropbox 資料的權限。

1. 為您的觸發程序提供必要的資訊。 

   在此範例中，選取您要追蹤檔案建立的資料夾。 若要瀏覽您的資料夾，請選擇的資料夾圖示旁**資料夾** 方塊中。

## <a name="add-action"></a>新增動作

現在加入任何新的檔案中取得內容的動作。

1. 在觸發程序下方，選擇 [下一個步驟]。 

1. 在搜尋方塊下方，選擇 [全部]。 在搜尋方塊中，輸入 "dropbox" 作為篩選條件。
從 [動作] 清單中，選取此動作：**使用路徑來取得檔案內容**

1. 如果您還沒有已獲授權存取 Dropbox 的 Azure Logic Apps，請立即授權的存取。

1. 若要瀏覽至您想要使用，接下來的檔案路徑**檔案路徑**方塊中，選擇省略符號 (**...**) 按鈕。 

   您也可以按一下內**檔案路徑**方塊中，然後從動態內容清單中，選取**檔案路徑**，其值可為您在上一節中新增觸發程序的輸出。

1. 完成後，儲存邏輯應用程式。

1. 若要觸發邏輯應用程式，請在 Dropbox 中建立新的檔案。

## <a name="connector-reference"></a>連接器參考

技術的詳細資訊，例如觸發程序、 動作和限制，如所述的連接器的 OpenAPI (以前稱為 Swagger) 檔案，請參閱 <<c0> [ 連接器的參考頁面](/connectors/dropbox/)。

## <a name="get-support"></a>取得支援

* 如有問題，請瀏覽 [Azure Logic Apps 論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。
* 若要提交或票選功能構想，請造訪 [Logic Apps 使用者意見反應網站](https://aka.ms/logicapps-wish)。

## <a name="next-steps"></a>後續步驟

* 了解其他 [Logic Apps 連接器](../connectors/apis-list.md)
