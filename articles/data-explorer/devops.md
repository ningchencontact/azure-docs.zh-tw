---
title: Azure 的 DevOps 工作，如 Azure 資料總管
description: 本主題中，您學習如何建立發行管線，及部署
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/05/2019
ms.openlocfilehash: a70a887ccb19d9c1cbdb5f8ebf6aa8d4b25a0dfd
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65161066"
---
# <a name="azure-devops-task-for-azure-data-explorer"></a>Azure 的 DevOps 工作，針對 Azure 的資料總管

[Azure 的 DevOps 服務](https://azure.microsoft.com/services/devops/)提供開發共同作業工具，例如高效能管線、 免費私人 Git 存放庫，可設定工作流程看板，以及廣泛的自動化和連續測試功能。 [Azure 的管線](https://azure.microsoft.com/services/devops/pipelines/)是一種 Azure DevOps 功能，可讓您管理要部署您的程式碼，與高效能管線的 CI/CD 搭配任何語言、 平台和雲端。
[Azure 資料總管-系統管理命令](https://marketplace.visualstudio.com/items?itemName=Azure-Kusto.PublishToADX)是 Azure 管線工作，可讓您建立發行管線，並部署您的資料庫變更為您的 Azure 資料總管資料庫。 它是免費提供[Visual Studio Marketplace](https://marketplace.visualstudio.com/)。

本文件說明一個簡單的範例使用**系統管理命令 – Azure 資料總管**工作來部署您的結構描述變更為您的資料庫。 完整的 CI/CD 管線，請參閱[Azure DevOps 文件](/azure/devops/user-guide/what-is-azure-devops?view=azure-devops#vsts)。

## <a name="prerequisites"></a>必要條件

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。
* Azure 資料總管叢集設定：
    * [Azure 資料總管叢集和資料庫](/azure/data-explorer/create-cluster-database-portal)
    * 建立 Azure Active Directory (Azure AD) 應用程式[佈建 Azure AD 應用程式](/azure/kusto/management/access-control/how-to-provision-aad-app)。
    * 授與存取權由您 Azure 資料總管資料庫上的 Azure AD 應用程式[管理 Azure 資料總管資料庫權限](/azure/data-explorer/manage-database-permissions)。
* Azure DevOps 設定：
    * [註冊免費的組織](/azure/devops/user-guide/sign-up-invite-teammates?view=azure-devops)
    * [建立組織](/azure/devops/organizations/accounts/create-organization?view=azure-devops)
    * [建立 Azure DevOps 專案](/azure/devops/organizations/projects/create-project?view=azure-devops)
    * [使用 Git 的程式碼](/azure/devops/user-guide/code-with-git?view=azure-devops)

## <a name="create-folders"></a>建立資料夾

建立下列的範例資料夾 (*函式*，*原則*，*資料表*) 在您的 Git 儲存機制中。 中的檔案複製[此處](https://github.com/Azure/azure-kusto-docs-samples/tree/master/DevOps_release_pipeline)到個別的資料夾所示下方並認可變更。 若要執行下列工作流程提供的範例檔案。

![建立資料夾](media/devops/create-folders.png)

> [!TIP]
> 在建立您自己的工作流程時，建議您讓您的程式碼具有等冪性。 例如，使用[建立合併式資料表](/azure/kusto/management/tables#create-merge-tables)而不是[建立資料表](/azure/kusto/management/tables#create-table)，並使用[建立或更改](/azure/kusto/management/functions#create-or-alter-function)函式，而不是[建立](/azure/kusto/management/functions#create-function)函式。

## <a name="create-a-release-pipeline"></a>建立發行管線

1. 登入您[Azure DevOps 的組織](https://dev.azure.com/)。
1. 選取 **管線** > **版本**從左側功能表，然後選取**新管線**。

    ![新增管線](media/devops/new-pipeline.png)

1. **的新發行管線**視窗隨即開啟。 在 **管線**索引標籤中，於**選取範本**窗格中，選取**空白的工作**。

     ![選取範本](media/devops/select-template.png)

1. 選取 [**階段**] 按鈕。 在 **階段**窗格中，新增**階段名稱**。 選取 **儲存**以儲存您的管線。

    ![名稱 「 階段](media/devops/stage-name.png)

1. 選取 [**將成品加入**] 按鈕。 在 **新增成品**窗格中選取您的程式碼所在的存放庫中，填妥相關資訊，然後按一下**新增**。 選取 **儲存**以儲存您的管線。

    ![新增構件](media/devops/add-artifact.png)

1. 在 **變數**索引標籤上，選取 **+ 新增**若要建立的變數**端點 URL** ，用以在工作中。 撰寫**名稱**並**值**的端點。 選取 **儲存**以儲存您的管線。 

    ![建立變數](media/devops/create-variable.png)

    若要尋找您 Endpoint_URL、 [概觀] 頁面的您**Azure 資料總管叢集**在 Azure 入口網站包含 Azure 資料總管叢集 URI。 建構下列格式的 URI `https://<Azure Data Explorer cluster URI>?DatabaseName=<DBName>`。  例如， https://kustodocs.westus.kusto.windows.net?DatabaseName=SampleDB

    ![Azure 的資料總管叢集 URI](media/devops/adx-cluster-uri.png)

## <a name="create-tasks-to-deploy"></a>建立要部署工作

1. 在 **管線**索引標籤上，按一下**1 個工作，0 的工作**加入工作。 

    ![新增工作](media/devops/add-task.png)

1. 建立要部署的三個工作**資料表**，**函式**，並**原則**，依此順序。 

1. 在 **任務**索引標籤上，選取**+** 由**代理程式作業**。 搜尋 **Azure 資料總管**。 在  **Marketplace**，安裝**Azure 資料總管 – 系統管理員命令**延伸模組。 然後，選取**新增**中**執行 Azure 資料總管命令**。

     ![新增系統管理命令](media/devops/add-admin-commands.png)

1. 按一下  **Kusto 命令**左邊及更新的工作包含下列資訊：
    * **顯示名稱**：工作的名稱
    * **檔案路徑**:在 **資料表**工作中，指定 */tables/{資料表*.csl，因為資料表建立檔案位於*資料表*資料夾。
    * **端點 URL**： 輸入`EndPoint URL`在上一個步驟中建立的變數。
    * 選取 **使用服務端點**，然後選取 **+ 新增**。

    ![更新 Kusto 命令工作](media/devops/kusto-command-task.png)

1. 完成中的下列資訊**加入 Azure 資料總管服務連線**視窗：

    |設定  |建議的值  |
    |---------|---------|
    |**連線名稱**     |    輸入名稱來識別此服務端點     |
    |**叢集 Url**    |    值可在您的 Azure 資料總管叢集在 Azure 入口網站中的 [概觀] 區段 | 
    |**服務主體識別碼**    |    輸入 （建立作為必要條件） 的 AAD 應用程式識別碼     |
    |**服務主體應用程式金鑰**     |    輸入 AAD 應用程式金鑰 （建立作為必要條件）    |
    |**AAD 租用戶識別碼**    |      輸入您的 AAD 租用戶 （例如 microsoft.com，contoso.com...)    |

    選取 **允許使用這個連線的所有管線**核取方塊。 選取 [確定] 。

    ![新增服務連線](media/devops/add-service-connection.png)

1. 重複步驟 1-5 另兩次以將檔案從部署*函式*並*原則*資料夾。 選取 [ **儲存**]。 在 **任務**索引標籤上，請參閱 < 建立三個工作：**部署資料表**，**部署 Functions**，以及**將原則部署**。

    ![部署所有的資料夾](media/devops/deploy-all-folders.png)

1. 選取  **+ 發行** > **建立發行**建立發行。

    ![建立發行](media/devops/create-release.png)

1. 在 **記錄檔**索引標籤上，檢查部署狀態為成功。

    ![成功部署，但](media/devops/deployment-successful.png)

您現在已完成發行管線部署到生產階段前三項工作的建立。