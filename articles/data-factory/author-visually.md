---
title: Azure Data Factory 中的視覺化撰寫 | Microsoft Docs
description: 了解如何使用 Azure Data Factory 中的視覺化撰寫
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/09/2019
author: sharonlo101
ms.author: shlo
ms.reviewer: ''
manager: craigg
ms.openlocfilehash: b32e4abebdfb93312c60a25ca8b483f071e5e73c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65507803"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Azure Data Factory 中的視覺化撰寫
Azure Data Factory 使用者介面體驗 (UX) 可讓您透過視覺化方式撰寫及部署資料處理站的資源，而不必編寫任何程式碼。 您可以將活動拖放到管線畫布上、執行測試回合、反覆進行偵錯，以及部署和監視管線回合。 使用 UX 來執行視覺化撰寫的方法有兩種：

- 直接使用 Data Factory 服務來撰寫。
- 使用 Azure Repos Git 整合來撰寫，以進行共同作業、原始檔控制或版本設定。

## <a name="author-directly-with-the-data-factory-service"></a>直接使用 Data Factory 服務來撰寫
使用 Data Factory 服務來進行視覺化撰寫與使用 Git 整合來進行視覺化撰寫有兩個不同之處：

- Data Factory 服務未包含存放庫，此存放庫會用於儲存變更的 JSON 實體。
- Data Factory 服務並未針對共同作業或版本控制進行最佳化。

![設定 Data Factory 服務](media/author-visually/configure-data-factory.png)

當您使用 UX **撰寫畫布**來使用 Data Factory 服務來直接撰寫時，只能使用 [全部發行]  模式。 您所做的任何變更都會直接發佈至 Data Factory 服務。

![發佈模式](media/author-visually/data-factory-publish.png)

## <a name="author-with-azure-repos-git-integration"></a>使用 Azure Repos Git 整合來進行撰寫
使用 Azure Repos Git 整合來進行視覺化撰寫時，可針對您資料處理站管線上的工作支援原始檔控制和共同作業。 您可以將資料處理站與 Azure Repos Git 組織存放庫建立關聯，以進行原始檔控制、共同作業及版本設定等。 一個 Azure Repos Git 組織可以有多個存放庫，但一個 Azure Repos Git 存放庫只能與一個資料處理站建立關聯。 如果您沒有 Azure Repos 組織或存放庫，請依照[這些指示](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)來建立您的資源。

> [!NOTE]
> 您可以將指令碼和資料檔案儲存在 Azure Repos Git 存放庫中。 不過，您必須手動將檔案上載至 Azure 儲存體。 Data Factory 管線不會自動將儲存在 Azure Repos Git 存放庫中的指令碼或資料檔案上傳至「Azure 儲存體」。

### <a name="configure-an-azure-repos-git-repository-with-azure-data-factory"></a>設定搭配 Azure Data Factory 的 Azure Repos Git 存放庫
您可以透過兩種方法設定搭配資料處理站的 Azure Repos Git 存放庫。

#### <a name="method1"></a> 設定方法 1 (Azure Repos Git 存放庫)：現在就開始吧頁面

在 Azure Data Factory 中，移至 [現在就開始吧]  頁面。 選取 [設定程式碼存放庫]  ：

![設定 Azure Repos 程式碼存放庫](media/author-visually/configure-repo.png)

[存放庫設定]  設定窗格會隨即出現：

![設定程式碼存放庫設定](media/author-visually/repo-settings.png)

此窗格會顯示下列 Azure Repos 程式碼存放庫設定：

| 設定 | 描述 | 值 |
|:--- |:--- |:--- |
| **存放庫類型** | Azure Repos 程式碼存放庫的類型。<br/> | Azure Repos Git |
| **Azure Active Directory** | 您的 Azure AD 租用戶名稱。 | `<your tenant name>` |
| **Azure Repos 組織** | 您的 Azure Repos 組織名稱。 您可以在 `https://{organization name}.visualstudio.com` 找到您的 Azure Repos 組織名稱。 您可以[登入您的 Azure Repos 組織](https://www.visualstudio.com/team-services/git/)，以存取 Visual Studio 設定檔和查看您的存放庫與專案。 | `<your organization name>` |
| **ProjectName** | 您的 Azure Repos 專案名稱。 您可以在 `https://{organization name}.visualstudio.com/{project name}` 找到您的 Azure Repos 專案名稱。 | `<your Azure Repos project name>` |
| **RepositoryName** | 您的 Azure Repos 程式碼存放庫名稱。 Azure Repos 專案包含 Git 存放庫，可隨著您的專案成長管理原始程式碼。 您可以建立新的存放庫，或使用專案中既有的存放庫。 | `<your Azure Repos code repository name>` |
| **共同作業分支** | 用於進行發佈的 Azure Repos 共同作業分支。 根據預設，它是 `master`。 如果您想要從其他分支發行資源，請變更此設定。 | `<your collaboration branch name>` |
| **根資料夾** | 在您 Azure Repos 共同作業分支中的根資料夾。 | `<your root folder name>` |
| **將現有的 Data Factory 資源匯入存放庫** | 指定是否要從 UX **撰寫畫布**將現有的資料處理站資源匯入到 Azure Repos Git 存放庫。 選取此方塊可將您的資料處理站資源以 JSON 格式匯入到相關聯的 Git 存放庫。 此動作會將每個資源個別匯出 (亦即，已連結的服務和資料集會匯出至個別的 JSON)。 若未選取此方塊，則不會匯入現有資源。 | 已選取 (預設值) |

#### <a name="configuration-method-2-azure-repos-git-repo-ux-authoring-canvas"></a>設定方法 2 (Azure Repos Git 存放庫)：UX 撰寫畫布
在 Azure Data Factory UX **撰寫畫布**中，找到您的資料處理站。 選取 [Data Factory]  下拉式功能表，然後選取 [設定程式碼存放庫]  。

隨即會出現設定窗格。 如需有關組態設定的詳細資訊，請參閱<a href="#method1">設定方法 1</a> 中的說明。

![設定用於 UX 撰寫的程式碼存放庫設定](media/author-visually/configure-repo-2.png)

### <a name="use-a-different-azure-active-directory-tenant"></a>使用不同的 Azure Active Directory 租用戶

您可以在不同的 Azure Active Directory 租用戶中建立 Azure Repos Git 存放庫。 若要指定不同的 Azure AD 租用戶，您必須擁有所用 Azure 訂用帳戶的系統管理員權限。

### <a name="use-your-personal-microsoft-account"></a>使用您的個人 Microsoft 帳戶

若要使用個人 Microsoft 帳戶進行 Git 整合，您可以將個人的 Azure Repos 連結至貴組織的 Active Directory。

1. 以來賓身分將您的個人 Microsoft 帳戶新增到貴組織的 Active Directory。 如需詳細資訊，請參閱[在 Azure 入口網站中新增 Azure Active Directory B2B 共同作業使用者](../active-directory/b2b/add-users-administrator.md)。

2. 使用您的個人 Microsoft 帳戶登入 Azure 入口網站。 然後切換到貴組織的 Active Directory。

3. 移至 [Azure DevOps] 區段，您現在會在其中看到您的個人存放庫。 選取存放庫並與 Active Directory 連線。

在這些設定步驟之後，當您在 Data Factory UI 中設定 Git 整合時，可以使用您的個人存放庫。

如需將 Azure Repos 連線至貴組織 Active Directory 的詳細資訊，請參閱[將您的 Azure DevOps 組織連線至 Azure Active Directory](/azure/devops/organizations/accounts/connect-organization-to-azure-ad)。

### <a name="switch-to-a-different-git-repo"></a>切換至不同的 Git 存放庫

若要切換至不同的 Git 存放庫，請找出位於 [Data Factory 概觀] 頁面右上角的圖示，如下列螢幕擷取畫面所示。 如果看不到該圖示，請清除您的本機瀏覽器快取。 選取適當圖示以移除與目前存放庫的關聯。

移除與目前存放庫的關聯之後，您可以設定 Git 設定以使用不同的存放庫。 接著，您可以將現有的 Data Factory 資源匯入至新的存放庫。

![移除與目前 Git 存放庫的關聯](media/author-visually/remove-repo.png)

### <a name="use-version-control"></a>使用版本控制
版本控制系統 (也稱為_原始檔控制_) 可讓開發人員在程式碼上共同作業，並追蹤對程式碼基底所進行的變更。 來源控制是多開發人員專案的必要工具。

與資料處理站相關聯的每個 Azure Repos Git 存放庫都有共同作業分支。 (`master` 是預設的共同作業分支)。 使用者也可以按一下 [+ 新增分支]  來建立功能分支，並在功能分支中進行開發。

![藉由同步或發佈來變更程式碼](media/author-visually/sync-publish.png)

當在您的功能分支中的功能開發已經備妥時，您可以按一下 [建立提取要求]  。 此動作會將您帶往 Azure Repos Git，您可以在其中發出提取要求、執行程式碼檢閱，以及將變更合併到您的共同作業分支。 (`master` 是預設值)。 您只被允許從您的共同作業分支發佈到 Data Factory 服務。 

![建立新的提取要求](media/author-visually/create-pull-request.png)

### <a name="configure-publishing-settings"></a>設定發佈設定

若要設定發佈分支 (亦即儲存 Resource Manager 範本的分支)，請將 `publish_config.json` 檔案新增至共同作業分支中的根資料夾。 Data Factory 會讀取此檔案、尋找 `publishBranch` 欄位，然後使用所提供的值來建立新分支 (如果尚未存在)。 接著，它會將所有 Resource Manager 範本都儲存到指定的位置。 例如:

```json
{
    "publishBranch": "factory/adf_publish"
}
```

當您從 Git 模式進行發佈時，您可以確認 Data Factory 是否使用您所預期的發佈分支，如以下螢幕擷取畫面所示：

![確認正確的發佈分支](media/author-visually/configure-publish-branch.png)

當您指定新的發佈分支時，Data Factory 並不會刪除先前的發佈分支。 如果您想要從遠端處理先前的發佈分支，請手動刪除它。

Data Factory 只有在載入處理站時才會讀取 `publish_config.json` 檔案。 如果您已經在入口網站中載入處理站，請重新整理瀏覽器以讓變更生效。

### <a name="publish-code-changes"></a>發佈程式碼變更
將變更合併到共同作業分支之後 (`master` 是預設值)，請選取 [發佈]  來手動將主要分支中的程式碼變更發佈到 Data Factory 服務。

![將變更發佈到 Data Factory 服務](media/author-visually/publish-changes.png)

> [!IMPORTANT]
> 主要分支不代表在 Data Factory 服務中部署的內容。 「必須」  以手動方式將主要分支主發佈至 Data Factory 服務。

### <a name="advantages-of-git-integration"></a>Git 整合的優點

-   **原始程式碼控制**。 當您的資料處理站工作負載變得更重要時，您可以將您的處理站與 Git 整合，以利用數個原始程式碼控制優點，例如：
    -   追蹤/稽核變更的能力。
    -   還原造成錯誤 (Bug) 之變更的能力。
-   **部分儲存**。 當您在處理站中進行許多變更時，您將了解在一般 LIVE 模式中，您無法將變更儲存為草稿，因為您還沒有準備好，或您不想要在電腦當機的情況下損失您的變更。 使用 Git 整合時，您能繼續以增量方式儲存您的變更，並只在您準備好時才將其發行到處理站。 Git 可做為您工作的暫存位置，直到您測試變更直到滿意為止。
-   **共同作業與控制**. 若您有多個參與相同處理站的小組成員，您可以透過程式碼檢閱程序讓您的小組成員彼此共同作業。 您也可以設定您的處理站，使並非該處理站的所有參與者都有部署到處理站的權限。 您可能讓小組成員可以透過 Git 進行變更，但只有小組中的特定人員可以將變更「發行」到處理站。
-   **顯示差異**。 在 Git 模式中，您可能會看到即將發行到處理站的承載差異。 此差異會顯示上次發行到處理站之後修改/新增/刪除的所有資源/實體。 根據此差異，您可以繼續發行，或返回並檢查您的變更並稍後再返回。
-   **更好的 CI/CD**。 若您使用 Git 模式，您可以設定您的發行管線，以在開發人員處理站中發生任何變更時儘快自動觸發。 您也可以自訂處理站中的屬性，這些屬性是以 Resource Manager 範本中參數的形式提供。 只保留必要屬性集做為參數並將其他項目以硬式編碼方式撰寫非常實用。
-   **更好的效能**。 相較於一般 LIVE 模式，處理站在 Git 模式中載入的速度可達 10 倍快，因為資源是透過 Git 下載。

### <a name="best-practices-for-git-integration"></a>Git 整合的最佳做法

-   **權限**。 一般而言，您您不想讓所有小組成員都擁有更新處理站的權限。
    -   所有小組成員都應該有資料處理站的唯讀權限。
    -   只應該允許特定人員發行到處理站，而若要這樣做，他們必須屬於處理站上的「Data Factory 參與者」角色。
    -   原始程式碼控制的其中一個良好做法是一併不允許直接簽入到共同作業分支。 此需求可防止錯誤 (Bug)，因為每個簽入都是透過「提取要求」程序進行。
-   **切換模式**。
    -    一旦處於 Git 模式，我們就建議您不要切換到 LIVE 模式並來回切換，這主要是因為當您在 LIVE 模式中進行變更時，切換為 Git 模式時不會看到這些變更。 嘗試在 Git 模式本身中進行變更，然後透過 UI 發行變更。
    -   同樣地，也請勿使用任何資料處理站 PowerShell Cmdlet，因為它們的效果與直接將提供的變更套用到即時處理站相同。
-   **使用來自 Azure Key Vault 的密碼**。
    -   我們強烈建議您使用 AzureKeyVault 將任何連接字串或密碼儲存到 DataFactory 連結服務。
    -   我們不會在 Git 中儲存任何此類祕密資訊 (基於安全性理由)，因此對連結服務所做的任何變更都會立即發行到即時處理站。 有時候您可能不想要立即發行，因為變更可能尚未通過測試，而這與 Git 的用途衝突。
    -   因此，所有此類祕密都必須從使用 Azure Key Vault 基礎的連結服務擷取。
    -   使用 Key Vault 的一些其他優點是它可簡化 CICD 程序，因為它不需要您在 Resource Manager 範本部署期間提供這些祕密。

## <a name="author-with-github-integration"></a>使用 GitHub 整合進行撰寫

使用 GitHub 整合所進行的視覺化撰寫，會對資料處理站管線上的工作支援原始檔控制和共同作業功能。 您可以將資料處理站與 GitHub 帳戶存放庫建立關聯，以進行原始檔控制、共同作業及版本設定。 一個 GitHub 帳戶可以有多個存放庫，但一個 GitHub 存放庫只能與一個資料處理站建立關聯。 如果您沒有 GitHub 帳戶或存放庫，請遵循 [這些指示](https://github.com/join)  \(英文\) 來建立您的資源。

GitHub 與 Data Factory 的整合支援公用 GitHub (即 [https://github.com](https://github.com)) 與 GitHub Enterprise。 只要您具備 GitHub 中存放庫的讀取和寫入權限，就能搭配 Data Factory 使用公用和私人 GitHub 存放庫。

若要設定 GitHub 存放庫，您必須擁有所用 Azure 訂用帳戶的系統管理員權限。

如需此功能的 9 分鐘簡介與示範，請觀看下列影片：

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Azure-Data-Factory-visual-tools-now-integrated-with-GitHub/player]

### <a name="limitations"></a>限制

- 您可以將指令碼和資料檔案儲存在 GitHub 存放庫。 不過，您必須手動將檔案上載至 Azure 儲存體。 Data Factory 管線不會自動將 GitHub 存放庫中儲存的指令碼或資料檔案上傳至 Azure 儲存體。

- 早於 2.14.0 的 GitHub Enterprise 版本不適用於 Microsoft Edge 瀏覽器。

- 僅可以在正式推出的 Data Factory 版本中，使用 GitHub 與 Data Factory 視覺化撰寫工具的整合。

### <a name="configure-a-public-github-repository-with-azure-data-factory"></a>設定搭配 Azure Data Factory 的公用 GitHub 存放庫

您可以透過兩種方法設定搭配資料處理站的 GitHub 存放庫。

**設定方法 1 (公用存放庫)：現在就開始吧頁面**

在 Azure Data Factory 中，移至 [現在就開始吧] ****  頁面。 選取 [設定程式碼存放庫] **** ：

![Data Factory 開始使用網頁](media/author-visually/github-integration-image1.png)

[存放庫設定] ****  設定窗格隨即出現：

![GitHub 存放庫設定](media/author-visually/github-integration-image2.png)

此窗格會顯示下列 Azure Repos 程式碼存放庫設定：

| **設定**                                              | **說明**                                                                                                                                                                                                                                                                                                                                                                                                                   | **值**          |
|----------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------|
| **存放庫類型**                                      | Azure Repos 程式碼存放庫的類型。                                                                                                                                                                                                                                                                                                                                                                                             | GitHub             |
| **GitHub 帳戶**                                       | 您的 GitHub 帳戶名稱。 此名稱可從 https:\//github.com/{account 名稱} / {存放庫名稱}。 瀏覽到此頁面時，系統會提示您輸入 GitHub 帳戶的 GitHub OAuth 認證。                                                                                                                                                                                                                                               |                    |
| **RepositoryName**                                       | 您的 GitHub 程式碼存放庫名稱。 GitHub 帳戶包含 Git 存放庫，可用來管理原始程式碼。 您可以建立新的存放庫，或使用帳戶中既有的存放庫。                                                                                                                                                                                                                              |                    |
| **共同作業分支**                                 | 用於發行的 GitHub 共同作業分支。 根據預設，這是主要分支。 如果您想要從其他分支發行資源，請變更此設定。                                                                                                                                                                                                                                                               |                    |
| **根資料夾**                                          | 在您 GitHub 共同作業分支中的根資料夾。                                                                                                                                                                                                                                                                                                                                                                             |                    |
| **將現有的 Data Factory 資源匯入存放庫** | 指定是否要從 UX **撰寫畫布** 將現有的資料處理站資源匯入到 GitHub 存放庫。 選取此方塊可將您的資料處理站資源以 JSON 格式匯入到相關聯的 Git 存放庫。 此動作會將每個資源個別匯出 (亦即，已連結的服務和資料集會匯出至個別的 JSON)。 若未選取此方塊，則不會匯入現有資源。 | 已選取 (預設值) |
| **要匯入資源的分支**                       | 指定要匯入資料處理站資源 (管線、資料集、連結服務等等) 的分支。 您可以將資源匯入下列其中一個分支：a. 共同作業 b. 新建 c. 使用現有的                                                                                                                                                                                                     |                    |

#### <a name="configuration-method-2-public-repo-ux-authoring-canvas"></a>設定方法 2 (公用存放庫)：UX 撰寫畫布

在 Azure Data Factory UX **撰寫畫布**中，找到您的資料處理站。 選取 [Data Factory] ****  下拉式功能表，然後選取 [設定程式碼存放庫] **** 。

隨即會出現設定窗格。 如需有關組態設定的詳細資訊，請參閱上述「設定方法 1」 ** 中的說明。

### <a name="configure-a-github-enterprise-repository-with-azure-data-factory"></a>設定搭配 Azure Data Factory 的 GitHub Enterprise 存放庫

您可以透過兩種方法設定搭配資料處理站的 GitHub Enterprise 存放庫。

#### <a name="configuration-method-1-enterprise-repo-lets-get-started-page"></a>設定方法 1 (企業存放庫)：現在就開始吧頁面

在 Azure Data Factory 中，移至 [現在就開始吧] ****  頁面。 選取 [設定程式碼存放庫] **** ：

![Data Factory 開始使用網頁](media/author-visually/github-integration-image1.png)

[存放庫設定] ****  設定窗格隨即出現：

![GitHub 存放庫設定](media/author-visually/github-integration-image3.png)

此窗格會顯示下列 Azure Repos 程式碼存放庫設定：

| **設定**                                              | **說明**                                                                                                                                                                                                                                                                                                                                                                                                                   | **值**          |
|----------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------|
| **存放庫類型**                                      | Azure Repos 程式碼存放庫的類型。                                                                                                                                                                                                                                                                                                                                                                                             | GitHub             |
| **使用 GitHub Enterprise**                                | 選取 GitHub Enterprise 的核取方塊                                                                                                                                                                                                                                                                                                                                                                                              |                    |
| **GitHub Enterprise URL**                                | GitHub Enterprise 的根 URL。 例如： https://github.mydomain.com                                                                                                                                                                                                                                                                                                                                                          |                    |
| **GitHub 帳戶**                                       | 您的 GitHub 帳戶名稱。 此名稱可從 https:\//github.com/{account 名稱} / {存放庫名稱}。 瀏覽到此頁面時，系統會提示您輸入 GitHub 帳戶的 GitHub OAuth 認證。                                                                                                                                                                                                                                               |                    |
| **RepositoryName**                                       | 您的 GitHub 程式碼存放庫名稱。 GitHub 帳戶包含 Git 存放庫，可用來管理原始程式碼。 您可以建立新的存放庫，或使用帳戶中既有的存放庫。                                                                                                                                                                                                                              |                    |
| **共同作業分支**                                 | 用於發行的 GitHub 共同作業分支。 根據預設，這是主要分支。 如果您想要從其他分支發行資源，請變更此設定。                                                                                                                                                                                                                                                               |                    |
| **根資料夾**                                          | 在您 GitHub 共同作業分支中的根資料夾。                                                                                                                                                                                                                                                                                                                                                                             |                    |
| **將現有的 Data Factory 資源匯入存放庫** | 指定是否要從 UX **撰寫畫布** 將現有的資料處理站資源匯入到 GitHub 存放庫。 選取此方塊可將您的資料處理站資源以 JSON 格式匯入到相關聯的 Git 存放庫。 此動作會將每個資源個別匯出 (亦即，已連結的服務和資料集會匯出至個別的 JSON)。 若未選取此方塊，則不會匯入現有資源。 | 已選取 (預設值) |
| **要匯入資源的分支**                       | 指定要匯入資料處理站資源 (管線、資料集、連結服務等等) 的分支。 您可以將資源匯入下列其中一個分支：a. 共同作業 b. 新建 c. 使用現有的                                                                                                                                                                                                     |                    |

#### <a name="configuration-method-2-enterprise-repo-ux-authoring-canvas"></a>設定方法 2 (企業存放庫)：UX 撰寫畫布

在 Azure Data Factory UX **撰寫畫布**中，找到您的資料處理站。 選取 [Data Factory] ****  下拉式功能表，然後選取 [設定程式碼存放庫] **** 。

隨即會出現設定窗格。 如需有關組態設定的詳細資訊，請參閱上述「設定方法 1」 ** 中的說明。

## <a name="use-the-expression-language"></a>使用運算式語言
您可以藉由使用 Azure Data Factory 所支援的運算式語言，來指定屬性值的運算式。

選取 [新增動態內容]  來指定屬性值的運算式：

![使用運算式語言](media/author-visually/dynamic-content-1.png)

## <a name="use-functions-and-parameters"></a>使用函式和參數

您可以在 Data Factory **運算式建立幫手**中使用函式或指定管線和資料集的參數：

如需所支援運算式的相關資訊，請參閱 [Azure Data Factory 中的運算式和函式](control-flow-expression-language-functions.md)。

![新增動態內容](media/author-visually/dynamic-content-2.png)

## <a name="provide-feedback"></a>提供意見反應
選取 [意見反應]  可為功能加上註解，也可以向 Microsoft 通報工具問題：

![意見反應](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>後續步驟
若要深入了解如何監視和管理管線，請參閱[以程式設計方式監視和管理管線](monitor-programmatically.md)。
