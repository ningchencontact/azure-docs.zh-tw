---
title: 連接到 Azure blob 儲存體-Azure Logic Apps
description: 使用 Azure Logic Apps 在 Azure 儲存體中建立和管理 Blob
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 06/20/2019
tags: connectors
ms.openlocfilehash: ce59c238e50a1be6879b07e959b236f6181a8ce4
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703247"
---
# <a name="create-and-manage-blobs-in-azure-blob-storage-with-azure-logic-apps"></a>使用 Azure Logic Apps 在 Azure Blob 儲存體中建立和管理 Blob

本文說明如何使用 Azure Blob 儲存體連接器，從邏輯應用程式內存取和管理 Azure 儲存體帳戶中儲存為 Blob 的檔案。 這樣一來，您就可以建立邏輯應用程式，來自動執行用於管理檔案的工作和工作流程。 例如，您可以建置邏輯應用程式，以在儲存體帳戶中建立、取得、更新和刪除檔案。

假設您有一個會在 Azure 網站上進行更新的工具。 並將它作為邏輯應用程式的觸發程序。 當此事件發生時，您可以讓邏輯應用程式更新 Blob 儲存體容器中的某些檔案，而這是邏輯應用程式中的其中一個動作。

> [!NOTE]
>
> 邏輯應用程式無法直接存取具有[防火牆規則](../storage/common/storage-network-security.md)且存在於相同區域中的 Azure 儲存體帳戶。 不過，邏輯應用程式可以存取存在於不同區域中的 Azure 儲存體帳戶，因為公用 IP 位址是用來跨區域進行通訊。 請確定您允許[在您的區域中，針對受控連接器的輸出 IP 位址](../logic-apps/logic-apps-limits-and-config.md#outbound)。 或者，您可以在此使用更多的先進選項：
>
> * 建立[整合服務環境](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)，此環境可連線至 Azure 虛擬網路中的資源。
>
> * 如果您使用專用層進行 API 管理，您可以使用 API 管理來處理儲存體 API，並允許後者的 IP 位址通過防火牆。 基本上，將 API 管理所使用的 Azure 虛擬網路新增至儲存體帳戶的防火牆設定。 接著，您可以使用 API 管理動作或 HTTP 動作來呼叫 Azure 儲存體 Api。 不過，如果您選擇此選項，就必須自行處理驗證程式。 如需詳細資訊，請參閱[簡單的企業整合架構](https://aka.ms/aisarch)。

如果您不熟悉邏輯應用程式，請檢閱[什麼是 Azure Logic Apps](../logic-apps/logic-apps-overview.md) 和[快速入門：建立第一個邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 如需連接器專屬的技術資訊，請參閱 [Azure Blob 儲存體連接器參考](/connectors/azureblobconnector/)。

## <a name="limits"></a>限制

* 根據預設，Azure Blob 儲存體動作可以讀取或寫入*50 MB 或更小*的檔案。 若要處理大於 50 MB 但最多 1024 MB 的檔案，Azure Blob 儲存體動作支援[訊息區塊](../logic-apps/logic-apps-handle-large-messages.md)化。 「**取得 blob 內容**」動作會隱含地使用區塊化。

* Azure Blob 儲存體觸發程式不支援區塊化。 當要求檔案內容時，觸發程式只會選取 50 MB 或更小的檔案。 若要取得大於 50 MB 的檔案，請依照下列模式：

  * 使用會傳回檔案屬性的 Azure Blob 儲存體觸發程式，例如**新增或修改 Blob 時（僅限屬性）** 。

  * 遵循具有 [Azure Blob 儲存體**取得 Blob 內容**] 動作的觸發程式，它會讀取完整的檔案，並隱含地使用區塊化。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* [Azure 儲存體帳戶和儲存體容器](../storage/blobs/storage-quickstart-blobs-portal.md)

* 需要從中存取 Azure Blob 儲存體帳戶的邏輯應用程式。 若要使用 Azure Blob 儲存體觸發程序來啟動邏輯應用程式，您需要[空白邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

<a name="add-trigger"></a>

## <a name="add-blob-storage-trigger"></a>新增 Blob 儲存體觸發程序

在 Azure Logic Apps 中，每個邏輯應用程式都必須使用[觸發程序](../logic-apps/logic-apps-overview.md#logic-app-concepts)啟動，而該觸發程序會在特定事件發生或符合特定條件時引發。 每次引發觸發程序時，Logic Apps 引擎都會建立邏輯應用程式執行個體，並開始執行應用程式的工作流程。

這個範例會示範如何在儲存體容器中新增或更新 blob 的屬性時，使用**新增或修改 blob 時（僅限屬性）** 觸發程式來啟動邏輯應用程式工作流程。

1. 在[Azure 入口網站](https://portal.azure.com)或 Visual Studio 中，建立空白邏輯應用程式，以開啟邏輯應用程式設計工具。 這個範例會使用 Azure 入口網站。

2. 在搜尋方塊中，輸入「azure blob」作為篩選條件。 從觸發程序清單中，選取您想要的觸發程序。

   這個範例會使用此觸發程式：**新增或修改 blob 時（僅限屬性）**

   ![選取觸發程序](./media/connectors-create-api-azureblobstorage/azure-blob-trigger.png)

3. 如果系統提示您輸入連線詳細資料，請[立即建立 Blob 儲存體連線](#create-connection)。 或者，如果連線已存在，請提供觸發程序的必要資訊。

   在此範例中，請選取您想要監視的容器和資料夾。

   1. 在 [容器] 方塊中，選取 [資料夾] 圖示。

   2. 在資料夾清單中，選擇右角括弧 ( **>** )，然後一直瀏覽，直到您找到並選取您想要的資料夾。

      ![選取資料夾](./media/connectors-create-api-azureblobstorage/trigger-select-folder.png)

   3. 選取您想要讓觸發程序檢查資料夾是否有變更的間隔和頻率。

4. 當您完成時，請在設計工具的工具列上，選擇 [儲存]。

5. 現在，繼續針對您想要使用觸發程序結果來執行的工作，於邏輯應用程式中新增一或多個動作。

<a name="add-action"></a>

## <a name="add-blob-storage-action"></a>新增 Blob 儲存體動作

在 Azure Logic Apps 中，[動作](../logic-apps/logic-apps-overview.md#logic-app-concepts)是工作流程中跟隨在觸發程序或另一個動作之後的步驟。 在此範例中，邏輯應用程式會使用[循環觸發程序](../connectors/connectors-native-recurrence.md)來啟動。

1. 在 [Azure 入口網站](https://portal.azure.com)或 Visual Studio 的「邏輯應用程式設計工具」中，開啟邏輯應用程式。 這個範例會使用 Azure 入口網站。

2. 在邏輯應用程式設計工具中，于觸發程式或動作底下，選擇 [**新增步驟**]。

   ![新增動作](./media/connectors-create-api-azureblobstorage/add-action.png) 

   若要在現有步驟之間新增動作，請將滑鼠放在連接箭頭上。 選擇顯示的加號（ **+** ），然後選取 [**新增動作**]。

3. 在搜尋方塊中，輸入「azure blob」作為篩選條件。 從 [動作] 清單中，選取您想要的動作。

   這個範例會使用此動作：**取得 blob 內容**

   ![選取動作](./media/connectors-create-api-azureblobstorage/azure-blob-action.png)

4. 如果系統提示您輸入連線詳細資料，請[立即建立 Azure Blob 儲存體連線](#create-connection)。
或者，如果連線已存在，請提供動作的必要資訊。

   針對此範例，請選取您想要的檔案。

   1. 從 [Blob] 方塊中，選取 [資料夾] 圖示。
  
      ![選取資料夾](./media/connectors-create-api-azureblobstorage/action-select-folder.png)

   2. 根據 Blob 的**識別碼**，尋找並選取您想要的檔案。 您可以在先前所述 Blob 儲存體觸發程序所傳回的 Blob 中繼資料內，找到此**識別碼**。

5. 當您完成時，請在設計工具的工具列上，選擇 [儲存]。
若要測試邏輯應用程式，請確定所選取的資料夾包含 Blob。

此範例只會取得 Blob 的內容。 若要檢視內容，請新增另一個動作，以使用另一個連接器建立具有 Blob 的檔案。 例如，新增 OneDrive 動作來根據 Blob 內容建立檔案。

<a name="create-connection"></a>

## <a name="connect-to-storage-account"></a>連線至儲存體帳戶

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to Azure blob storage](../../includes/connectors-create-api-azureblobstorage.md)]

## <a name="connector-reference"></a>連接器參考

如需如連接器的 Open API （先前稱為 Swagger）檔案所述的技術詳細資料（例如觸發程式、動作和限制），請參閱[連接器的參考頁面](/connectors/azureblobconnector/)。

## <a name="next-steps"></a>後續步驟

* 了解其他 [Logic Apps 連接器](../connectors/apis-list.md)
