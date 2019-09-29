---
title: 啟用 Azure Cosmos DB 帳戶中的筆記本
description: Azure Cosmos DB 的內建筆記本可讓您從入口網站中分析資料並加以視覺化。 本文說明如何啟用 Cosmos 帳戶的這項功能。
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/22/2019
ms.author: dech
ms.openlocfilehash: 824a562cb5f0562a235d903fbd4e575bc9c22572
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2019
ms.locfileid: "71672741"
---
# <a name="enable-notebooks-for-azure-cosmos-db-accounts"></a>啟用 Azure Cosmos DB 帳戶的筆記本

Azure Cosmos DB 中的內建 Jupyter 筆記本可讓您從 Azure 入口網站分析資料並將其視覺化。 本文說明如何為您的 Azure Cosmos DB 帳戶啟用這項功能。

## <a name="enable-notebooks-in-a-new-cosmos-account"></a>在新的 Cosmos 帳戶中啟用筆記本
1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. 選取 [建立資源] > [資料庫] > [Azure Cosmos DB]。
1. 在 [**建立 Azure Cosmos DB 帳戶**] 頁面上，選取 [**筆記本**]。 
 
    ![在 Azure Cosmos DB 建立 分頁中選取 筆記本 選項](media/enable-notebooks/create-new-account-with-notebooks.png)
1. 選取 [檢閱 + 建立]。 您可以略過 [**網路**和**標記**] 選項。 
1. 檢閱帳戶設定，然後選取 [建立]。 建立帳戶需要幾分鐘的時間。 等候入口網站頁面顯示 [您的部署已完成] 訊息。 

    ![Azure 入口網站的 [通知] 窗格](media/enable-notebooks/create-new-account-with-notebooks-complete.png)
1. 選取 [移至資源] 以移至 Azure Cosmos DB 帳戶頁面。 

    ![Azure Cosmos DB 帳戶頁面](../../includes/media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-3.png)

1. 流覽至 [**資料總管**] 窗格。 您現在應該會看到 [筆記本] 工作區。

    ![新增 Azure Cosmos DB 筆記本工作區](media/enable-notebooks/new-notebooks-workspace.png)

## <a name="enable-notebooks-in-an-existing-cosmos-account"></a>在現有的 Cosmos 帳戶中啟用筆記本
您也可以在現有的帳戶上啟用筆記本。 每個帳戶只需執行一次此步驟。

1. 流覽至 Cosmos 帳戶中的 [**資料總管**] 窗格。
1. 選取 [**啟用筆記本**]。

    ![在資料總管中建立新的 [筆記本] 工作區](media/enable-notebooks/enable-notebooks-workspace.png)
1. 這會提示您建立新的 [筆記本] 工作區。 選取 [**完成設定]。**
1. 您的帳戶現在已啟用使用筆記本！

## <a name="create-and-run-your-first-notebook"></a>建立並執行您的第一個筆記本

若要確認您可以使用筆記本，請選取 [範例筆記本] 底下的其中一個筆記本。 這會將筆記本的複本儲存至您的工作區並加以開啟。

在此範例中，我們將使用**GettingStarted. ipynb**。 

![View GettingStarted. ipynb 筆記本](media/enable-notebooks/select-getting-started-notebook.png)

若要執行筆記本：
1. 選取包含 Python 程式碼的第一個程式碼儲存格。 
1. 選取 [**執行**] 以執行儲存格。 您也可以使用**Shift + Enter**來執行資料格。
1. 重新整理資源窗格，以查看已建立的資料庫和容器。

    ![執行開始使用筆記本](media/enable-notebooks/run-first-notebook-cell.png)

您也可以選取 [**新增筆記本**] 來建立新的筆記本，或從 [**我的筆記本**] 功能表中選取 [**上傳**檔案] 來上傳現有的筆記本（. ipynb）檔案。 

![建立或上傳新的筆記本](media/enable-notebooks/create-or-upload-new-notebook.png)

## <a name="next-steps"></a>後續步驟

- 瞭解[Azure Cosmos DB Jupyter 筆記本](cosmosdb-jupyter-notebooks.md)的優點
- 深入瞭解[如何使用筆記本功能和命令](use-notebook-features-and-commands.md)