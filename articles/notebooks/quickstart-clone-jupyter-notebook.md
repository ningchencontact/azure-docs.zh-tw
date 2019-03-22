---
title: 使用 Azure Notebooks 從 GitHub 複製 Jupyter 筆記本
description: 從 GitHub 存放庫中快速複製 Jupyter 筆記本，並在您的 Azure Notebooks 帳戶中執行。
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: d7122b78-6daa-4bea-883b-ff832cfecef3
ms.service: azure
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: cfce5765cda1e3a1dfb7eb073719191b13b0696e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "58089194"
---
# <a name="quickstart-clone-a-notebook"></a>快速入門：複製筆記本

許多資料科學家和開發人員將其筆記本儲存在 [GitHub 存放庫](https://github.com)中，這是一個免費的服務，可為許多不同的專案類型提供儲存體和版本控制。 GitHub 通常當作在本機執行的 Jupyter 筆記本上共同作業的一種方式使用。 在此種情況下，每個共同作業者都會維護一份存放庫的本機複本，並從該複本執行筆記本。

複製則可在您的 Azure Notebooks 帳戶中建立 GitHub 筆記本的複本。 此複本獨立於其原始的存放庫；變更僅會儲存在您的 Azure Notebooks 帳戶中，因此不會影響正本。 因為您的複本是在雲端，因此您可以與其他不需要任何本機複本，甚至在自己的電腦上有安裝 Jupyter 的共同作業者共用專案。 複製筆記本也可能只是當作您自己專案的起點，或是為了取得資料檔案。

## <a name="clone-azure-cognitive-services-notebooks"></a>複製 Azure 認知服務筆記本

1. 前往 [Azure Notebooks](https://notebooks.azure.com) 並登入  (如需詳細資訊，請參閱[快速入門 - 登入 Azure Notebooks](quickstart-sign-in-azure-notebooks.md))。

1. 從您的公用設定檔頁面中，選取頁面頂端的 [我的專案]：

    ![瀏覽器視窗頂端的 [我的專案] 連結](media/quickstarts/my-projects-link.png)

1. 在 [我的專案] 頁面上，選取向上鍵按鈕 (鍵盤快速鍵：U；當瀏覽器視窗夠寬時，此按鈕會顯示為 [上傳 GitHub 存放庫])：

    ![[我的專案] 頁面上的 [上傳 GitHub 存放庫] 命令](media/quickstarts/upload-github-repo-command.png)

1. 在顯示的 [上傳 GitHub 存放庫] 中，輸入或設定下列詳細資料，然後選取 [匯入]：

   - **GitHub 存放庫**：Microsoft/cognitive-services-notebooks (此名稱可在 [https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks) 複製 Azure 認知服務的 Jupyter 筆記本)。
   - **以遞迴方式複製**：(已清除)
   - **專案名稱**：認知服務複製
   - **專案識別碼**：cognitive-services-clone
   - **公用**：(已清除)

     ![上傳 GitHub 存放庫快顯視窗以收集存放庫資訊](media/quickstarts/upload-github-repo-popup.png)

1. 請耐心等候此程序完成；複製存放庫可能需要幾分鐘的時間。

1. 複製完成之後，Azure Notebook 會帶您前往新的專案，您可以在那裡看到所有檔案的複本。

    [![](media/quickstarts/completed-clone.png "檢視已完成的複本")](media/quickstarts/completed-clone.png#lightbox)

## <a name="share-a-notebook"></a>共用筆記本

1. 若要共用已複製專案的複本，請使用 [共用] 控制或取得連結、取得包含連結的 HTML 或 Markdown 程式碼，或建立包含結的電子郵件訊息：

    ![專案共用命令](media/quickstarts/share-project-command.png)

1. 因為您在複製專案時清除了 [公用] 選項，因此複本是私密的。 若要將您的複本設為公用，選取 [專案設定]、在快顯視窗中設定 [公用專案] 選項，然後選取 [儲存]。

1. 在專案中選取要執行的筆記本。 例如，Azure 認知服務存放庫中的每個筆記本都是自己獨立的快速入門。 下圖顯示新增認知服務 API 訂用帳戶金鑰，並將搜尋字詞從 "puppies" 變更為 "bunnies" 之後，使用 BingImageSearchAPI 筆記本的結果：

    ![執行從 GitHub 複製的 Jupyter 筆記本](media/quickstarts/clone-notebook-result.png)

1. 當您執行筆記本完畢之後，選取 [檔案] > [關閉並終止] 來關閉筆記本及其瀏覽器視窗。

1. 若要共用專案中的個別筆記本，以滑鼠右鍵按一下筆記本，然後選取 **複製連結** (鍵盤快速鍵：y)：

    ![將連結複製到個別筆記本的內容功能表命令](media/quickstarts/copy-link-to-individual-notebook.png)

1. 若要編輯筆記本以外的檔案，以滑鼠右鍵按一下專案中的檔案，然後選取 **編輯檔案** (鍵盤快速鍵：i)。 預設動作 **執行** (鍵盤快速鍵：r) 只會顯示檔案內容，而且不允許編輯。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [教學課程：建立和執行 Jupyter Notebook 來執行線性迴歸](tutorial-create-run-jupyter-notebook.md)
