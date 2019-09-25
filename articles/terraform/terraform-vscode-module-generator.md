---
title: 使用 Yeoman 在 Azure 中建立 Terraform 基底範本
description: 了解如何使用 Yeoman 在 Azure 中建立 Terraform 基底範本。
services: terraform
ms.service: azure
keywords: terraform, devops, virtual machine, azure, yeoman, 虛擬機器
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/20/2019
ms.openlocfilehash: 7a628eb02170346a826cab19498d6fdf40cebddd
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/20/2019
ms.locfileid: "71173236"
---
# <a name="create-a-terraform-base-template-in-azure-using-yeoman"></a>使用 Yeoman 在 Azure 中建立 Terraform 基底範本

[Terraform](https://docs.microsoft.com/azure/terraform/
) 能提供在 Azure 上輕鬆建立基礎結構的方法。 [Yeoman](https://yeoman.io/) 能大幅簡化模組開發人員建立 Terraform 模組的作業，並提供絕佳的*最佳做法*架構。

在此文章中，您會了解如何使用 Yeoman 模組產生器來建立基底 Terraform 範本。 然後，您將了解如何以兩種方法測試新的 Terraform 範本：

- 使用您在本文中建立的 Docker 檔案執行 Terraform 模組。
- 在 Azure Cloud Shell 中以原生方式執行您的 Terraform 模組。

## <a name="prerequisites"></a>必要條件

- **Azure 訂用帳戶**：如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/) 。
- **Visual Studio Code**：我們將使用 [Visual Studio Code](https://www.bing.com/search?q=visual+studio+code+download&form=EDGSPH&mkt=en-us&httpsmsn=1&refig=dffc817cbc4f4cb4b132a8e702cc19a3&sp=3&ghc=1&qs=LS&pq=visual+studio+code&sk=LS1&sc=8-18&cvid=dffc817cbc4f4cb4b132a8e702cc19a3&cc=US&setlang=en-US) 來檢查由 Yeoman 產生器建立的檔案。 不過，您也可以使用自己偏好的程式碼編輯器。
- **Terraform**：您需要安裝 [Terraform](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-install-configure ) 以執行由 Yeoman 建立的模組。
- **Docker**：我們將使用 [Docker](https://www.docker.com/get-started) 來執行由 Yeoman 產生器建立的模組。 (如果想要的話，您也可以使用 Ruby 來代替 Docker 執行範例模組)。
- **Go 程式設計語言**：您需要安裝 [Go](https://golang.org/)，因為由 Yeoman 產生的測試案例將會以 Go 撰寫。

>[!NOTE]
>此教學課程中大部分的程序都牽涉到命令列輸入。 這裡所描述的步驟皆適用於所有作業系統與命令列工具。 在我們的範例中，我們已選擇對本機環境使用 PowerShell，並對 Cloud Shell 環境使用 Git Bash。

## <a name="prepare-your-environment"></a>準備您的環境

### <a name="install-nodejs"></a>安裝 Node.js

若要在 Cloud Shell 中使用 Terraform，您必須[安裝 Node.js](https://nodejs.org/en/download/) 6.0+。

>[!NOTE]
>若要確認是否已安裝 Node.js，請開啟終端機視窗並輸入 `node --version`。

### <a name="install-yeoman"></a>安裝 Yeoman

從命令提示字元，輸入 `npm install -g yo`。

![安裝 Yeoman](media/terraform-vscode-module-generator/ymg-npm-install-yo.png)

### <a name="install-the-yeoman-template-for-terraform-module"></a>安裝適用於 Terraform 模組的 Yeoman 範本

從命令提示字元，輸入 `npm install -g generator-az-terra-module`。

![安裝 generator-az-terra-module](media/terraform-vscode-module-generator/ymg-pm-install-generator-module.png)

>[!NOTE]
>若要確認是否已安裝 Yeoman，請從終端機視窗輸入 `yo --version`。

### <a name="create-an-empty-folder-to-hold-the-yeoman-generated-module"></a>建立空白資料夾以保存 Yeoman 所產生的模組

Yeoman 範本會在**目前的目錄**中產生檔案。 因此，您必須建立目錄。

>[!Note]
>您必須將這個空白目錄放在 $GOPATH/src 底下。您可以在[這裡](https://github.com/golang/go/wiki/SettingGOPATH)找到完成此作業的指示。

從命令提示字元：

1. 瀏覽到您要新增空白目錄的父目錄。
1. 輸入 `mkdir <new-directory-name>` 。

    > [!NOTE]
    > 將 `<new-directory-name>` 替換為您的新目錄名稱。 在此範例中，我們將新目錄命名為 `GeneratorDocSample`。

    ![mkdir](media/terraform-vscode-module-generator/ymg-mkdir-GeneratorDocSample.png)

1. 輸入 `cd <new directory's name>` 以瀏覽至新目錄，然後按 **Enter**。

    ![瀏覽到您的新目錄](media/terraform-vscode-module-generator/ymg-cd-GeneratorDocSample.png)

    >[!NOTE]
    >若要確認此目錄是空的，請輸入 `ls`。 這個命令所輸出的結果不應該列出任何檔案。

## <a name="create-a-base-module-template"></a>建立基底模組範本

從命令提示字元：

1. 輸入 `yo az-terra-module` 。

1. 依照螢幕上的指示提供下列資訊：

    - *Terraform module project Name* \(Terraform 模組專案名稱\)

        ![專案名稱](media/terraform-vscode-module-generator/ymg-project-name.png)       

        >[!NOTE]
        >在此範例中，我們輸入的是 `doc-sample-module`。

    - *Would you like to include the Docker image file?* \(是否要包含 Docker 映像檔案？\)

        ![包含 Docker 映像檔案？](media/terraform-vscode-module-generator/ymg-include-docker-image-file.png) 

        >[!NOTE]
        >輸入 `y` 。 如果您輸入 **n**，產生的模組程式碼將僅支援以原生模式執行。

3. 輸入 `ls` 以檢視所建立的檔案。

    ![列出所建立檔案](media/terraform-vscode-module-generator/ymg-ls-GeneratorDocSample-files.png)

## <a name="review-the-generated-module-code"></a>檢閱所產生的模組程式碼

1. 啟動 Visual Studio Code

1. 從功能表列，選取 [檔案] > [開啟資料夾]  ，然後選取您所建立的資料夾。

    ![Visual Studio Code](media/terraform-vscode-module-generator/ymg-open-in-vscode.png)

讓我們看看由 Yeoman 模組產生器所建立的一些檔案。

>[!Note]
>在此文章中，我們將會使用由 Yeoman 模組產生器所建立的 main.tf、variables.tf 與 outputs.tf 檔案。 不過，當您自行建立模組時，您將會編輯這些檔案以配合您 Terraform 模組的功能。 如需這些檔案及其使用方式的更深入討論，請參閱 [Terraform 模組中的 Terratest](https://mseng.visualstudio.com/VSJava/_git/Terraform?path=%2FTerratest%20Introduction.md&version=GBmaster) \(英文\)。

### <a name="maintf"></a>main.tf

定義名為 *random-shuffle* 的模組。 輸入是 *string_list*。 輸出是排列的計數。

### <a name="variablestf"></a>variables.tf

定義模組所使用的輸入與輸出變數。

### <a name="outputstf"></a>outputs.tf

定義模組的輸出內容。 在這裡，該內容為由內建的 Terraform 模組 **random_shuffle** 所傳回的值。

### <a name="rakefile"></a>Rakefile

定義建置步驟。 這些步驟包括：

- **build**：驗證 main.tf 檔案的格式。
- **unit**：產生的模組基本架構不會包含單元測試的程式碼。 如果您想要指定單元測試案例，便必須在此新增該程式碼。
- **e2e**：執行模組的端對端測試。

### <a name="test"></a>test

- 測試案例是以 Go 撰寫的。
- test 中的所有程式碼都是端對端測試。
- 端對端測試會嘗試使用 Terraform 來佈建定義於 **fixture** 底下的所有項目，然後將 **template_output.go** 程式碼中的輸出與預先定義的預期值比較。
- **Gopkg.lock** 與 **Gopkg.toml**：定義您的相依性。 

## <a name="test-your-new-terraform-module-using-a-docker-file"></a>使用 Docker 檔案測試新的 Terraform 模組

>[!NOTE]
>在我們的範例中，我們是以本機模組的形式執行模組，而沒有實際碰觸到 Azure。

### <a name="confirm-docker-is-installed-and-running"></a>確認 Docker 已安裝且正在執行

從命令提示字元，輸入 `docker version`。

![Docker 版本](media/terraform-vscode-module-generator/ymg-docker-version.png)

輸出結果確認 Docker 已安裝。

若要確認 Docker 是否正在執行，請輸入 `docker info`。

![Docker 資訊](media/terraform-vscode-module-generator/ymg-docker-info.png)

### <a name="set-up-a-docker-container"></a>設定 Docker 容器

1. 從命令提示字元，輸入

    `docker build --build-arg BUILD_ARM_SUBSCRIPTION_ID= --build-arg BUILD_ARM_CLIENT_ID= --build-arg BUILD_ARM_CLIENT_SECRET= --build-arg BUILD_ARM_TENANT_ID= -t terra-mod-example .` 。

    系統將會顯示訊息 **Successfully built** (成功建置)。

    ![成功建置](media/terraform-vscode-module-generator/ymg-successfully-built.png)

1. 從命令提示字元，輸入 `docker image ls`。

    螢幕將會列出您新建立的模組 *terra-mod-example*。

    ![存放庫結果](media/terraform-vscode-module-generator/ymg-repository-results.png)

    >[!NOTE]
    >模組的名稱 *terra-mod-example* 已於您在先前步驟 1 中所輸入的命令中指定。

1. 輸入 `docker run -it terra-mod-example /bin/sh` 。

    您現已在 Docker 中執行，並可以輸入 `ls` 來列出檔案。

    ![列出 Docker 檔案](media/terraform-vscode-module-generator/ymg-list-docker-file.png)

### <a name="build-the-module"></a>建置模組

1. 輸入 `bundle install` 。

    等候 **Bundle complete** (組合完成) 的訊息，然後繼續進行下個步驟。

1. 輸入 `rake build` 。

    ![Rake build](media/terraform-vscode-module-generator/ymg-rake-build.png)

### <a name="run-the-end-to-end-test"></a>執行端對端測試

1. 輸入 `rake e2e` 。

1. 過幾分鐘之後，將會出現 **PASS** (通過) 訊息。

    ![PASS](media/terraform-vscode-module-generator/ymg-pass.png)

1. 輸入 `exit` 來完成端對端測試，並結束 Docker 環境。

## <a name="use-yeoman-generator-to-create-and-test-a-module-in-cloud-shell"></a>使用 Yeoman 產生器來建立和測試 Cloud Shell 中的模組

在上一節中，您已了解如何使用 Docker 檔案測試 Terraform 模組。 在本節中，您將使用 Yeoman 產生器來建立和測試 Cloud Shell 中的模組。

比起使用 Docker 檔案，使用 Cloud Shell 可大幅簡化程序。 使用 Cloud Shell：

- 您不需要安裝 Node.js
- 您不需要安裝 Yeoman
- 您不需要安裝 Terraform

所有這些項目都已預先安裝在 Cloud Shell 中。

### <a name="start-a-cloud-shell-session"></a>啟動 Cloud Shell 工作階段

1. 透過 [Azure 入口網站](https://portal.azure.com/)、[shell.azure.com](https://shell.azure.com)或 [Azure 行動應用程式](https://azure.microsoft.com/features/azure-portal/mobile-app/)來啟動 Azure Cloud Shell 工作階段。

1. [歡迎使用 Azure Cloud Shell]  頁面隨即開啟。 選取 **Bash (Linux)** 。 (不支援 Power Shell。)

    ![歡迎使用 Azure Cloud Shell](media/terraform-vscode-module-generator/ymg-welcome-to-azure-cloud-shell.png)

    >[!NOTE]
    >在此範例中，我們選取 [Bash (Linux)]。

1. 若您尚未設定 Azure 儲存體帳戶，會出現下列畫面。 選取 [建立儲存體]  。

    ![您未掛接任何儲存體](media/terraform-vscode-module-generator/ymg-you-have-no-storage-mounted.png)

1. Azure Cloud Shell 會在您先前選取的殼層中啟動，並顯示剛為您建立之雲端磁碟機的資訊。

    ![已建立您的雲端磁碟機](media/terraform-vscode-module-generator/ymg-your-cloud-drive-has-been-created-in.png)

### <a name="prepare-a-folder-to-hold-your-terraform-module"></a>準備資料夾以保存您的 Terraform 模組

1. 此時，Cloud Shell 已為您在環境變數中設定 GOPATH。 若要查看此路徑，請輸入 `go env`。

1. 如果尚無 $GOPATH 資料夾，請建立一個：輸入 `mkdir ~/go` 。

1. 在 $GOPATH 資料夾內建立資料夾：輸入 `mkdir ~/go/src` 。 此資料夾將用來保存及組織您可能建立的其他專案資料夾，例如我們將在下個步驟中建立的 `<your-module-name>` 資料夾。

1. 建立資料夾以保存您的 Terraform 模組：輸入 `mkdir ~/go/src/<your-module-name>` 。

    >[!NOTE]
    >在此範例中，我們選擇 `my-module-name` 作為資料夾名稱。

1. 瀏覽至您的模組資料夾：輸入 `cd ~/go/src/<your-module-name>`

### <a name="create-and-test-your-terraform-module"></a>建立和測試您的 Terraform 模組

1. 輸入 `yo az-terra-module` 並依照精靈中的指示。

    >[!NOTE]
    >如果系統詢問您是否要建立 Docker 檔案，您可以輸入 `N`。

1. 輸入 `bundle install` 以安裝相依性。

    等候 **Bundle complete** (組合完成) 的訊息，然後繼續進行下個步驟。

1. 輸入 `rake build` 來建置您的模組。

    ![Rake build](media/terraform-vscode-module-generator/ymg-rake-build.png)

1. 輸入 `rake e2e` 以執行端對端測試。

1. 過幾分鐘之後，將會出現 **PASS** (通過) 訊息。

    ![PASS](media/terraform-vscode-module-generator/ymg-pass.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [安裝和使用 Azure Terraform Visual Studio Code 擴充功能。](https://docs.microsoft.com/azure/terraform/terraform-vscode-extension)
