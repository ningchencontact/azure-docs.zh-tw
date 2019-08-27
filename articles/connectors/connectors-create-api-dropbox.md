---
title: 連接至 Dropbox-Azure Logic Apps
description: 使用 Dropbox REST API 和 Azure Logic Apps上傳及管理檔案
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 03/01/2019
tags: connectors
ms.openlocfilehash: 4e0689454ec074348fcbc775373a48d6825cfac4
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050986"
---
# <a name="upload-and-manage-files-in-dropbox-by-using-azure-logic-apps"></a>使用 Azure Logic Apps 上傳和管理 Dropbox 中的檔案

透過 Dropbox 連接器和 Azure Logic Apps, 您可以建立自動化工作流程, 以上傳和管理 Dropbox 帳戶中的檔案。 

本文說明如何從邏輯應用程式連接到 Dropbox, 然後**在建立檔案時**新增 dropbox, 並**使用路徑**動作來取得檔案內容。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* [Dropbox 帳戶](https://www.dropbox.com/), 您可以免費註冊。 您必須有帳號憑證, 才能建立邏輯應用程式與 Dropbox 帳戶之間的連線。

* [如何建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知識。 針對此範例，您需要空白的邏輯應用程式。

## <a name="add-trigger"></a>新增觸發程序

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 在搜尋方塊下方，選擇 [全部]。 在搜尋方塊中，輸入 "dropbox" 作為篩選條件。
從觸發程序清單中，選取此觸發程序：**當檔案建立時**

   ![選取 Dropbox 觸發程序](media/connectors-create-api-dropbox/select-dropbox-trigger.png)

1. 使用您的 Dropbox 帳戶認證登入，並授與 Azure Logic Apps 存取您的 Dropbox 資料的權限。

1. 為您的觸發程序提供必要的資訊。 

   在此範例中, 請選取您要追蹤檔案建立的資料夾。 若要流覽您的資料夾, 請選擇 [**資料夾**] 方塊旁的資料夾圖示。

## <a name="add-action"></a>新增動作

現在, 新增可從任何新檔案取得內容的動作。

1. 在觸發程序下方，選擇 [下一個步驟]。 

1. 在搜尋方塊下方，選擇 [全部]。 在搜尋方塊中，輸入 "dropbox" 作為篩選條件。
從 [動作] 清單中，選取此動作：**使用路徑來取得檔案內容**

1. 如果您尚未授權 Azure Logic Apps 存取 Dropbox, 請立即授權存取權。

1. 若要流覽至您想要使用的檔案路徑, 請在 [檔案**路徑**] 方塊旁, 選擇省略號 ( **...** ) 按鈕。 

   您也可以在 [檔案**路徑**] 方塊內按一下, 然後從動態內容清單中選取 [檔案**路徑**], 其值可當做您在上一節中新增之觸發程式的輸出。

1. 完成後，儲存邏輯應用程式。

1. 若要觸發邏輯應用程式, 請在 Dropbox 中建立新的檔案。

## <a name="connector-reference"></a>連接器參考

如需如連接器的 OpenAPI (先前為 Swagger) 檔案所述的技術詳細資料 (例如觸發程式、動作和限制), 請參閱[連接器的參考頁面](/connectors/dropbox/)。

## <a name="next-steps"></a>後續步驟

* 了解其他 [Logic Apps 連接器](../connectors/apis-list.md)
