---
title: 使用 CI/CD 搭配 Azure Dev Spaces | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: DrEsteban
ms.author: stevenry
ms.date: 12/17/2018
ms.topic: article
manager: yuvalm
description: 在 Azure 上使用容器和微服務快速進行 Kubernetes 開發
keywords: Docker、Kubernetes、Azure、AKS、Azure Container Service、容器
ms.openlocfilehash: 0abe2902248c8203046cfe891d136ca7d5d0a75b
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/02/2019
ms.locfileid: "55665969"
---
# <a name="use-cicd-with-azure-dev-spaces"></a>使用 CI/CD 搭配 Azure Dev Spaces

此文章將引導您在啟用 Dev Spaces 的情況下為 Azure Kubernetes Service (AKS) 設定持續整合/持續部署 (CI/CD)。 CI/CD 到 AKS 允許在將認可的程式碼推送至來源存放庫時，自動部署應用程式更新。 將 CI/CD 與啟用 Dev Spaces 的叢集搭配使用非常有用，因為它可以讓應用程式的基準保持最新，以便小組使用。

![CI/CD 圖表範例](../media/common/ci-cd-simple.png)

雖然此文章將引導您使用 Azure DevOps，但相同的概念適用於 Jenkins、TeamCity 等 CI/CD 系統。

## <a name="prerequisites"></a>先決條件
* [啟用 Azure Dev Spaces 的 Azure Kubernetes Service (AKS) 叢集](../get-started-netcore.md)
* [已安裝 Azure Dev Spaces CLI](upgrade-tools.md)
* [Azure DevOps 組織與專案](https://docs.microsoft.com/azure/devops/user-guide/sign-up-invite-teammates?view=vsts)
* [Azure Container Registry (ACR)](../../container-registry/container-registry-get-started-azure-cli.md)
    * 有 Azure Container Registry [系統管理員帳戶](../../container-registry/container-registry-authentication.md#admin-account)詳細資料可用
* [授權您的 AKS 叢集從 Azure Container Registry 提取](../../container-registry/container-registry-auth-aks.md)

## <a name="download-sample-code"></a>下載範例程式碼
為了節省時間，我們將建立一個範例程式碼 GitHub 存放庫的分支。 移至 https://github.com/Azure/dev-spaces 並選取 [分支]。 分支程序完成後，請在本機**複製**分支版本的存放庫。 根據預設，_主要_分支將會簽出，但我們在 _azds_updates_ 分支中包含了一些節省時間的變更，這些變更也應該在分支期間轉移。 _azds_updates_ 分支包含我們要求您在 Dev Spaces 教學課程小節中以手動方式進行的更新，以及一些預先製作的 YAML 和 JSON 檔案，以簡化 CI/CD 系統的部署。 您可以使用 `git checkout -b azds_updates origin/azds_updates` 之類的命令檢查本機存放庫中的 _azds_updates_ 分支。

## <a name="dev-spaces-setup"></a>Dev Spaces 設定
使用 `azds space select` 命令建立名為 _dev_ 的新空間。 您的 CI/CD 管線將使用 _dev_ 空間來推送您的程式碼變更。 它還將用於根據 _dev_ 建立 _子空間_。

```cmd
azds space select -n dev
```

出現選取父代開發人員空間的提示時，請選取\<無\>。

_dev_ 空間將始終包含存放庫的最新狀態 (即基線)，以便開發人員可以從 _dev_ 建立_子空間_，以測試較大應用程式之內容中的隔離變更。 此概念在 Dev Spaces 教學課程中會更詳細地討論。

## <a name="creating-the-build-definition"></a>建立組建定義
在網頁瀏覽器中開啟您的 Azure DevOps 小組專案，並瀏覽至 [管線] 區段。 首先，按一下 Azure DevOps 網站右上角的個人檔案相片、開啟 [預覽功能] 窗格，然後停用 [新的 YAML 管線建立體驗]：

![正在開啟 [預覽功能] 窗格](../media/common/preview-feature-open.png)

要停用的選項：

![[新的 YAML 管線建立體驗] 選項](../media/common/yaml-pipeline-preview-feature.png)

> [!Note]
> 此時 Azure DevOps [新的 YAML 管線建立體驗] 預覽功能與建立預先定義的建置管線衝突。 您現在需要停用它才能部署我們預先定義的建置管線。

在 _azds_updates_ 分支中，我們包含了一個簡單的 [Azure 管線 YAML](https://docs.microsoft.com/azure/devops/pipelines/yaml-schema?view=vsts&tabs=schema)，它定義了 *mywebapi* 和 *webfrontend* 所需的建置步驟。

根據您所選擇的語言，管線 YAML 已在類似於 `samples/dotnetcore/getting-started/azure-pipelines.dotnetcore.yml` 的路徑中簽入

若要從此檔案建立管線：
1. 在 DevOps 專案主頁面上，瀏覽至 [管線] > [建置]
1. 選取選項以建立**新的**建置管線
1. 選取 [GitHub] 作為來源，必要時使用您的 GitHub 帳戶進行授權，然後從 dev-spaces sampleapp 存放庫的分支版本中選取 _azds_updates_ 分支
1. 選擇 [組態即程式碼] 或 [YAML] 作為範本
1. 現在，您將看到建置管線的組態頁面。 如先前所述，輸入 **YAML 檔案路徑**的特定語言路徑。 例如，`samples/dotnetcore/getting-started/azure-pipelines.dotnetcore.yml`
1. 移至 [變數] 索引標籤
1. 手動將 _dockerId_ 新增為變數，該變數是 [Azure Container Registry 系統管理員帳戶](../../container-registry/container-registry-authentication.md#admin-account)的使用者名稱。 (如文章先決條件中所述)
1. 手動將 _dockerPassword_ 新增為變數，該變數是 [Azure Container Registry 系統管理員帳戶](../../container-registry/container-registry-authentication.md#admin-account)的密碼。 基於安全性考量，請務必將 _dockerPassword_ 指定為祕密 (藉由選取鎖頭圖示)。
1. 選取 [儲存並排入佇列]

您現在擁有一個 CI 解決方案，可以自動建置 *mywebapi* 和 *webfrontend*，以便將任何更新推送到 GitHub 分支的 _azds_updates_ 分支。 您可以透過瀏覽至 Azure 入口網站，選取您的 Azure Container Registry，並瀏覽 [存放庫] 索引標籤來驗證 Docker 映像是否已推送：

![Azure Container Registry 存放庫](../media/common/ci-cd-images-verify.png)

## <a name="creating-the-release-definition"></a>建立發行定義

1. 在 DevOps 專案主頁面上，瀏覽至 [管線] > [發行]
1. 如果您正在使用尚未包含發行定義的全新 DevOps 專案，則必須先建立一個空的發行定義，然後再繼續。 除非您有現有發行定義，否則 [匯入] 選項不會顯示在 UI 中。
1. 在左側，按一下 [+ 新增] 按鈕，然後按一下 [匯入管線]
1. 在 `samples/release.json` 選取 .json 檔案
1. 按一下 [確定]。 請注意，會載入 [管線] 窗格並顯示 [發行定義編輯] 頁面。 另請注意，有一些紅色警告圖示表示仍然必須設定特定叢集的詳細資料。
1. 在 [管線] 窗格的左側，按一下 [新增成品] 泡泡。
1. 在 [來源] 下拉式清單中，選取我們稍早在此文件中建立的建置管線。
1. 針對 [預設版本]，我們建議您**從建置管線預設分支中選擇最新版本**。 您不需要指定任何標記。
1. 將 [來源別名] 設定為 `drop`。 預先定義的發行工作使用**來源別名**，因此必須設定它。
1. 按一下 [新增]。
1. 現在按一下新建立的 `drop` 成品來源上的閃電圖示，如下所示：

    ![發行成品持續部署設定](../media/common/release-artifact-cd-setup.png)
1. 啟用**持續部署觸發程序**
1. 現在，移至 [工作] 窗格。 應選取 _dev_ 階段，您應該會看到三個紅色的下拉式清單控制項，如下所示：

    ![發行定義設定](../media/common/release-setup-tasks.png)
1. 指定您正在搭配 Azure Dev Spaces 使用的 Azure 訂用帳戶、資源群組與叢集。
1. 此時應僅有一個區段顯示紅色，亦即 [代理程式作業] 區段。 前往該處，並指定**託管 Ubuntu 1604** 作為此階段的代理程式集區。
1. 將滑鼠暫留在頂端的 [工作] 選取器上，選取 [prod]。
1. 指定您正在搭配 Azure Dev Spaces 使用的 Azure 訂用帳戶、資源群組與叢集。
1. 在 [代理程式作業] 下，將**託管 Ubuntu 1604** 設定為代理程式集區。
1. 按一下右上方的 [儲存]，然後按一下 [確定]。
1. 按一下 [+ 發行] ([儲存] 按鈕旁邊)，然後按一下 [建立發行]。
1. 確定已選取 [成品] 區段中建置管線的最新組建，然後點擊 [建立]。

自動化發行處理程序將立即開始，將 *mywebapi* 和 *webfrontend* 圖表部署至 _dev_ 最上層空間中的 Kubernetes 叢集。 您可以在 Azure DevOps Web 入口網站上監視發行的進度。

> [!TIP]
> 如果您的發行失敗，並顯示類似 *UPGRADE FAILED: timed out waiting for the condition* 的錯誤訊息，請嘗試[使用 Kubernetes 儀表板](../../aks/kubernetes-dashboard.md)檢查叢集中的 Pod。 如果您看到 Pod 無法啟動且出現類似 *Failed to pull image "azdsexample.azurecr.io/mywebapi:122": rpc error: code = Unknown desc = Error response from daemon:Get https://azdsexample.azurecr.io/v2/mywebapi/manifests/122: unauthorized: authentication required* 的錯誤訊息，可能是因為您的叢集未獲授權從 Azure Container Registry 中提取。 請確定您已完成[授權您的 AKS 叢集從 Azure Container Registry 提取](../../container-registry/container-registry-auth-aks.md)的先決條件。

您現在擁有一個完全自動的 CI/CD 管線，用於 Dev Spaces 範例應用程式的 GitHub 分支。 每次認可並推送程式碼時，建置管線都會建置並將 *mywebapi* 和 *webfrontend* 映像推送至您的自訂 ACR 執行個體。 然後，發行管線會將每個應用程式的 Helm 圖表部署到啟用 Dev Spaces 之叢集上的 _dev_ 空間。

## <a name="accessing-your-dev-services"></a>存取您的 _dev_ 服務
部署之後，可以使用公用 URL 存取 _dev_ 版本的 *webfrontend*，例如：`http://dev.webfrontend.<hash>.<region>.aksapp.io`。

您可以使用 *kubectl* CLI 尋找此 URL：
```cmd
kubectl get ingress -n dev webfrontend -o=jsonpath="{.spec.rules[0].host}"
```

## <a name="deploying-to-production"></a>部署至生產環境
按一下發行定義中的 [編輯]，並注意已定義 _prod_ 階段：

![建立生產環境階段](../media/common/prod-env.png)

使用此教學課程中建立的 CI/CD 系統手動將特定發行升級到 _prod_：
1. 在 DevOps 入口網站上開啟先前成功的發行
1. 將滑鼠移到 'prod' 階段上方
1. 選取 [部署]

![升級到生產環境](../media/common/prod-promote.png)

我們的 CI/CD 管線範例會利用變數來變更 *webfrontend* 的 DNS 前置詞，具體取決於正在部署的環境。 因此，若要存取您的 'prod' 服務，可以使用以下網址：`http://prod.webfrontend.<hash>.<region>.aksapp.io`。

部署後，您可以使用 *kubectl* CLI <!-- TODO update below to use list-uris when the product has been updated to list non-azds ingresses #769297 --> 尋找此 URL：

```cmd
kubectl get ingress -n prod webfrontend -o=jsonpath="{.spec.rules[0].host}"
```

## <a name="dev-spaces-instrumentation-in-production"></a>生產環境中的 Dev Spaces 檢測
雖然 Dev Spaces 檢測設計為_不會_妨礙應用程式正常運作，但我們建議您在未啟用 Dev Spaces 的 Kubernetes 命名空間中執行生產工作負載。 使用這種類型的 Kubernetes 命名空間表示您應該使用 `kubectl` CLI 建立生產環境命名空間，或者允許您的 CI/CD 系統在第一次的 Helm 部署期間建立它。 _選取_或使用 Dev Spaces 工具建立空間，會將 Dev Spaces 檢測新增至該命名空間。

下面是一個範例命名空間結構，它支援功能開發、'dev'環境、_和_ 生產環境，全都在單一的 Kubernetes 叢集中：

![範例命名空間的結構](../media/common/cicd-namespaces.png)

> [!Tip]
> 如果您已經建立 `prod` 空間，並且只想將其從 Dev Spaces 檢測中排除 (而不刪除它！)，則可以使用下列 Dev Spaces CLI 命令執行此操作：
>
> `azds space remove -n prod --no-delete`
>
> 執行此操作後，您可能需要刪除 `prod` 命名空間中的所有 Pod，以便可以在不使用 Dev Spaces 檢測的情況下重新建立它們。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [了解使用 Azure Dev Spaces 的小組開發](../team-development-netcore.md)