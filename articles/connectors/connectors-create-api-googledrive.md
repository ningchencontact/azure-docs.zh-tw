---
title: 連接到 Google 雲端硬碟
description: 使用 Azure Logic Apps，自動建立及管理 Google 雲端硬碟檔案的工作流程
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: 155a579438747ca7de67eba6449b8a0191e86e2c
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2020
ms.locfileid: "75666834"
---
# <a name="create-and-manage-files-for-google-drive-by-using-azure-logic-apps"></a>使用 Azure Logic Apps 建立及管理 Google 雲端硬碟的檔案

連線到 Google 雲端硬碟來建立檔案、取得資料列等等。 您可以利用 Google 雲端硬碟來： 

* 根據您透過搜尋所取得的資料，來建置您的商務流程。 
* 使用動作來搜尋圖像、搜尋新聞等等。 這些動作會收到回應，然後輸出能讓其他動作使用的資料。 舉例來說，您可以搜尋某支影片，然後利用 Twitter 把該影片張貼在某個 Twitter 摘要上。

您可以從建立邏輯應用程式立即開始，請參閱[建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="create-the-connection-to-google-drive"></a>建立至 Google 雲端硬碟的連線

當您將這個 API 新增到邏輯應用程式時，您必須授權邏輯應用程式，使其能夠連線到您的 Google 雲端硬碟。

> [!INCLUDE [Steps to create a connection to googledrive](../../includes/connectors-create-api-googledrive.md)]

當您建立連線之後，請輸入 Google 雲端硬碟的屬性，例如資料夾路徑或檔案名稱。 

## <a name="connector-specific-details"></a>連接器特定的詳細資料

如需有關觸發程式、動作和限制的技術詳細資訊（由連接器的 Swagger 描述所描述），請參閱[連接器的參考頁面](/connectors/googledrive/)。

## <a name="next-steps"></a>後續步驟

* 了解其他 [Logic Apps 連接器](apis-list.md)
