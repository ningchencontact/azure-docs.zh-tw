---
title: Azure Terraform 與 VS Code 延伸模組 | Microsoft Docs
description: 在此文章中，您將了解如何在 Visual Studio Code 中安裝及使用 Terraform 延伸模組。
keywords: terraform, devops, virtual machine, azure, 虛擬機器
author: v-mavick
ms.author: v-mavick
ms.date: 08/14/2018
ms.topic: article
ms.prod: vs-code
ms.openlocfilehash: 0c88879faae100372055479ad4edb8c36d8f557d
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/17/2018
ms.locfileid: "40190808"
---
# <a name="azure-terraform-vs-code-extension"></a>Azure Terraform VS Code 延伸模組

Microsoft Azure Terraform Visual Studio Code (VS Code) 延伸模組的設計旨在提高開發人員生產力，同時搭配 Azure 製作、測試及使用 Terraform。 此延伸模組提供 Terraform 命令支援、資源圖表視覺效果，以及 Visual Studio Code 內的 CloudShell 整合。

## <a name="what-you-do"></a>您要做什麼

- 在您的機器上安裝開放原始碼 HashiCorp Terraform 可執行檔。
- 在您的本機 VS Code 安裝上安裝適用於 Azure 的 Terraform 與 Code 延伸模組。

## <a name="what-you-learn"></a>您學到什麼

在此教學課程中，您將會學到：

- Terraform 如何自動化及簡化 Azure 服務的佈建。
- 如何安裝及使用適用於 Azure 服務的 Microsoft Terraform VS Code 延伸模組。
- 如何使用 VS Code 來撰寫、規劃及執行 Terraform 方案。

## <a name="what-you-need"></a>您需要什麼

- 執行 Windows 10、Linux 或 macOS 10.10+ 的電腦。
- [Visual Studio Code](https://www.bing.com/search?q=visual+studio+code+download&form=EDGSPH&mkt=en-us&httpsmsn=1&refig=dffc817cbc4f4cb4b132a8e702cc19a3&sp=3&ghc=1&qs=LS&pq=visual+studio+code&sk=LS1&sc=8-18&cvid=dffc817cbc4f4cb4b132a8e702cc19a3&cc=US&setlang=en-US)。
- 有效的 Azure 訂用帳戶。 [啟動 30 天免費試用 Microsoft Azure 帳戶](https://azure.microsoft.com/free/)。
- 在您的本機電腦上安裝 [Terraform](https://www.terraform.io/) 開放原始碼工具。
  
## <a name="prepare-your-dev-environment"></a>準備您的開發環境

### <a name="install-git"></a>安裝 Git

若要完成此文章中的練習，您必須[安裝 Git](https://git-scm.com/)。

### <a name="install-hashicorp-terraform"></a>安裝 HashiCorp Terraform

依照 the HashiCorp [安裝 Terraform](https://www.terraform.io/intro/getting-started/install.html) 網頁上的指示執行，這些指示涵蓋：

- 下載 Terraform
- 安裝 Terraform
- 確認已正確安裝 Terraform

>[!Tip]
>務必依照有關設定 PATH 系統變數的指示執行。

### <a name="install-nodejs"></a>安裝 Node.js

若要在 Cloud Shell 中使用 Terraform，您必須[安裝 Node.js](https://nodejs.org/) 6.0+。

>[!NOTE]
>若要確認是否已正確安裝 Node.js，請開啟終端機視窗並輸入：`node -v`

### <a name="install-graphviz"></a>安裝 GraphViz

若要使用 Terraform 視覺化函式，您必須[安裝 GraphViz](http://graphviz.org/)。

>[!NOTE]
>若要確認是否已正確安裝 GraphViz，請開啟終端機視窗並輸入：`dot -V`

### <a name="install-the-azure-terraform-vs-code-extension"></a>安裝 Azure Terraform VS Code 延伸模組：

1. 啟動 VS Code。
2. 按一下 [延伸模組] 圖示。

    ![[延伸模組] 按鈕](media/terraform-vscode-extension/tf-vscode-extensions-button.png)

3. 使用 [在 Marketplace 中搜尋擴充功能] 文字方塊來搜尋 Azure Terraform 延伸模組：

    ![在 Marketplace 中搜尋 VS Code 延伸模組](media/terraform-vscode-extension/tf-search-extensions.png)

4. 按一下 [安裝]。

    >[!NOTE]
    >當您針對 Azure Terraform 按一下 [安裝]**Install** 時，VS Code 將會自動安裝 Azure Account 延伸模組。 Azure Account 是 Azure Terraform 延伸模組的相依性檔案，Azure Terraform 延伸模組使用它來執行 Azure 訂用帳戶驗證與 Azure 相關程式碼延伸模組。

#### <a name="verify-the-terraform-extension-is-installed-in-visual-studio-code"></a>確認是否已正確在 Visual Studio Code 中安裝 Terraform 延伸模組

1. 按一下 [延伸模組] 圖示。
2. 在 [搜尋] 文字方塊中輸入 `@installed`。

    ![已安裝的延伸模組](media/terraform-vscode-extension/tf-installed-extensions.png)

Azure Terraform 延伸模組將會出現在已安裝的延伸模組清單中。

![已安裝的 Terraform 延伸模組](media/terraform-vscode-extension/tf-installed-terraform-extension-button.png)

您現在可以從 VS Code 在您的 Cloud Shell 環境中執行所有支援的 Terraform 命令。

## <a name="exercise-1-basic-terraform-commands-walk-through"></a>練習 1: 基本 Terraform 命令逐步解說

在此練習中，您會建立及執行基本 Terraform 設定檔，此設定檔會佈建新的 Azure 資源群組。

### <a name="prepare-a-test-plan-file"></a>準備測試方案檔案

1. 在 VS Code 中，從功能表列選取 [檔案] > [新增檔案]。
2. 瀏覽到 [azurerm_resource_group](https://www.terraform.io/docs/providers/azurerm/r/resource_group.html#) 並複製 [範例使用方式] 程式碼區塊中的程式碼：
3. 將複製的程式碼貼到您在 VS Code 中建立的新檔案中。

    ![貼上範例使用方式程式碼](media/terraform-vscode-extension/tf-paste-example-usage-code.png)

    >[!NOTE]
    >您可以變更資源群組的**名稱**值，但它在您的 Azure 訂用帳戶中必須是唯一的。

4. 從功能表列，選取 [檔案] > [另存新檔]。
5. 在 [另存新檔] 對話方塊中，瀏覽以尋找您要的位置，然後按一下 [新增資料夾]。 (將新資料夾的名稱變更為比*新資料夾*更具描述性的名稱)。

    >[!NOTE]
    >在此範例中，我們將資料夾命名為 TERRAFORM-TEST-PLAN。

6. 確定您的新資料夾已反白顯示 (已選取)，然後按一下 [開啟]。
7. 在 [另存新檔] 對話方塊中，將檔案的預設名稱變更為 *main.tf*。

    ![另存為 main.tf](media/terraform-vscode-extension/tf-save-as-main.png)

8. 按一下 [檔案]。
- 在功能表列中，選取 [檔案] > [開啟資料夾]。 瀏覽並選取您建立的新資料夾。

### <a name="run-terraform-init-command"></a>執行 Terraform *init* 命令

1. 啟動 Visual Studio Code。
2. 從 VS Code 功能表列，選取 [檔案] > [開啟資料夾]，然後尋找並選取您的 *main.tf* 檔案。

    ![main.tf 檔案](media/terraform-vscode-extension/tf-main-tf.png)

3. 從功能表列，選取 [檢視] > [命令選擇區] > [Azure Terraform: 初始化]。
4. 隨後系統將詢問您「是否要開啟 Cloud Shell?」。 按一下 [確定]。

    ![是否要開啟 Cloud Shell?](media/terraform-vscode-extension/tf-do-you-want-to-open-cloud-shell.png)

5. 第一次從新資料夾啟動 Cloud Shell 時，系統會要求您設定 Web 應用程式。 按一下 [開啟]。

    ![第一次啟動 Cloud Shell](media/terraform-vscode-extension/tf-first-launch-of-cloud-shell.png)

6. [歡迎使用 Azure Cloud Shell] 頁面隨即開啟。 選 [Bash] 或 [PowerShell]。

    ![歡迎使用 Azure Cloud Shell](media/terraform-vscode-extension/tf-welcome-to-azure-cloud-shell.png)

    >[!NOTE]
    >在此範例中，我們選取 [Bash (Linux)]。

7. 若您尚未設定 Azure 儲存體帳戶，會出現下列畫面。 按一下 [建立儲存體]。

    ![您未掛接任何儲存體](media/terraform-vscode-extension/tf-you-have-no-storage-mounted.png)

1. Azure Cloud Shell 會在您先前選取的殼層中啟動，並顯示剛為您建立之雲端磁碟機的資訊。

    ![已建立您的雲端磁碟機](media/terraform-vscode-extension/tf-your-cloud-drive-has-been-created-in.png)

9. 您現在可以結束 Cloud Shell
10. 從功能表列，選取 [檢視] > [命令選擇區] > [Azure Terraform: 初始化]。

    ![Terraform 已成功初始化](media/terraform-vscode-extension/tf-terraform-has-been-successfully-initialized.png)

### <a name="visualize-the-plan"></a>將方案視覺化

在此教學課程稍早，您已安裝 GraphViz。 Terraform 可以使用 GraphViz 來產生設定或執行計畫的視覺表示。 Azure Terraform VS Code 延伸模組會透過 *visualize* 命令來實作此功能。

- 從功能表列，選取 [檢視] > [命令選擇區] > [Azure Terraform: 視覺化]。

    ![將方案視覺化](media/terraform-vscode-extension/tf-graph.png)

### <a name="run-terraform-plan-command"></a>執行 Terraform *plan* 命令

Terraform *plan* 命令是用來檢查變更集的執行計畫是否會執行您要執行的動作。

>[!NOTE]
>Terraform *plan* 不會對您的實際 Azure 資源進行任何變更。 若要實際變更您方案中包含的項目，我們會使用 Terraform *apply* 命令。

- 從功能表列，選取 [檢視] > [命令選擇區] > [Azure Terraform: 方案]。

    ![Terraform 方案](media/terraform-vscode-extension/tf-terraform-plan.png)

### <a name="run-terraform-apply-command"></a>執行 Terraform *apply* 命令

滿意 Terraform *plan*, 的結果之後，您可以執行 *apply* 命令。

1. 從功能表列，選取 [檢視] > [命令選擇區] > [Azure Terraform: 套用]。

    ![Terraform 套用](media/terraform-vscode-extension/tf-terraform-apply.png)

2. 輸入 *yes*。

    ![Terraform 套用 > 是](media/terraform-vscode-extension/tf-terraform-apply-yes.png)

### <a name="verify-your-terraform-plan-was-executed"></a>確認您的 Terraform 方案已執行

查看是否已成功建立您的新 Azure 資源群組：

1. 開啟 Azure 入口網站。
2. 在左側瀏覽窗格中，選取 [資源群組]。

    ![確認您的新資源群組是否存在](media/terraform-vscode-extension/tf-verify-resource-group-created.png)

您的新資源群組應該會列在 [名稱] 欄。

>[!NOTE]
>現在您可以將您的 Azure 入口網站視窗維持開啟，們將會在下一個步驟中使用它。

### <a name="run-terraform-destroy-command"></a>執行 Terraform *destroy* 命令

1. 從功能表列，選取 [檢視] > [命令選擇區] > [Azure Terraform: 摧毀]。

    ![Terraform 摧毀](media/terraform-vscode-extension/tf-terraform-destroy.png)

2. 輸入 *yes*。

    ![Terraform 摧毀 > 是](media/terraform-vscode-extension/tf-terraform-destroy-yes.png)

### <a name="verify-your-resource-group-was-destroyed"></a>確認是否已摧毀您的資源群組

確認 Terraform 是否已成功摧毀您的資源群組：

1. 在 Azure 入口網站的 [資源群組] 頁面上，按一下 [重新整理]。
2. 將不再列出您的資源群組。

    ![確認已摧毀資源群組](media/terraform-vscode-extension/tf-refresh-resource-groups-button.png)

## <a name="exercise-2-terraform-compute-module"></a>練習 2：Terraform *compute* 模組

在此練習中，您將了解如何將 Terraform *compute* 模組載入到 VS Code 環境。

### <a name="clone-the-terraform-azurerm-compute-module"></a>複製 terraform-azurerm-compute 模組

1. 使用[此連結](https://github.com/Azure/terraform-azurerm-compute)來存取 GitHub 上的 Terraform Azure Rm Compute 模組。
2. 按一下 [複製或下載]。

    ![複製或下載](media/terraform-vscode-extension/tf-clone-with-https.png)

    >[!NOTE]
    >在此範例中，我們的資料夾是命名為 *terraform-azurerm-compute*。

### <a name="open-the-folder-in-vs-code"></a>在 VS Code 中開啟該資料夾

1. 啟動 Visual Studio Code。
2. 從功能表列，選取 [檔案] > [開啟資料夾]，然後瀏覽並選取您在上一個步驟中建立的資料夾。

    ![terraform-azurerm-compute 資料夾](media/terraform-vscode-extension/tf-terraform-azurerm-compute-folder.png)

### <a name="initialize-terraform"></a>初始化 Terraform

在您可以開始從 VS Code 使用 Terraform 命令之前，您必須先下載兩個 Azure 提供者的外掛程式：random 與 azurerm。

1. 在 VS Code IDE 的 [Terminal] 窗格中，輸入：`terraform init`

    ![terraform init 命令](media/terraform-vscode-extension/tf-terraform-init-command.png)

2. 輸入 `az login` 並依照畫面上的指示執行。

### <a name="module-test-lint"></a>模組清單：*lint*

1. 從功能表列，選取 [檢視] > [命令選擇區] > [Azure Terraform: 執行測試]。
2. 從測試類型選項清單，選取 [lint]。

    ![選取測試類型](media/terraform-vscode-extension/tf-select-type-of-test-lint.png)

3. 當系統詢問您「是否要開啟 CloudShell?」時，請按一下 [確定] 並依照畫面上的指示執行。

    ![是否要開啟 CloudShell?](media/terraform-vscode-extension/tf-do-you-want-to-open-cloudshell-small.png)

>[!NOTE]
>當您執行 **lint** 或**端對端**測試時，Azure 會使用容器服務來佈建測試機器以執行實際測試。 因此，您的測試結果可能需要數分鐘才能傳回。

不久之後，您會看到 [終端機] 窗格顯示一個清單，如此範例所示：

![Lint 測試結果](media/terraform-vscode-extension/tf-lint-test-results.png)

### <a name="module-test-end-to-end"></a>模組測試：*端對端*

1. 從功能表列，選取 [檢視] > [命令選擇區] > [Azure Terraform: 執行測試]。
2. 從測試類型選項清單，選取 [端對端]。

    ![選取測試類型](media/terraform-vscode-extension/tf-select-type-of-test-end-to-end.png)

3. 若系統詢問您「是否要開啟 CloudShell?」，請按一下 [確定] 並依照畫面上的指示執行。

    ![是否要開啟 CloudShell?](media/terraform-vscode-extension/tf-do-you-want-to-open-cloudshell-small.png)

>[!NOTE]
>當您執行 **lint** 或**端對端**測試時，Azure 會使用容器服務來佈建測試機器以執行實際測試。 因此，您的測試結果可能需要數分鐘才能傳回。

不久之後，您會看到 [終端機] 窗格顯示一個清單，如此範例所示：

![端對端測試結果](media/terraform-vscode-extension/tf-end-to-end-test-results.png)

## <a name="next-steps"></a>後續步驟

您已經看過 Terraform 可從 Visual Studio Code 簡化您 Azure 服務佈建的幾種方式。 現在，您可以檢閱這些資源：
- [Terraform 模組登錄](https://registry.terraform.io/)列出適用於 Azure 與其他支援之提供者的所有 Terraform 模組。

針對這些模組，我們提供下列資訊：

- 模組一般功能與其特性的說明
- 使用範例
- 測試設定，這顯示如何在您的本機開發機器上建置、執行及測試每個模組
- Dockerfile，允許您在本機建置及執行模組開發環境。
