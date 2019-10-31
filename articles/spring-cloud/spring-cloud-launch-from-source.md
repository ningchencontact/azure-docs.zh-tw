---
title: 快速入門：從原始程式碼啟動您的春天雲端應用程式
description: 瞭解如何直接從原始程式碼啟動 Azure 春季雲端應用程式
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: jeconnoc
ms.openlocfilehash: 573baa242c06868326568a82bc358e136f1ece2c
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2019
ms.locfileid: "73177967"
---
# <a name="launch-your-spring-cloud-application-from-source-code"></a>從原始程式碼啟動您的春天雲端應用程式

Azure Spring Cloud 可讓您輕鬆地在 Azure 上執行以 Spring Cloud 為基礎的微服務應用程式。

Azure 春季雲端可讓您直接從 java 原始程式碼或預先建立的 JAR 啟動應用程式。 本文會引導您完成必要的步驟。

透過遵循本快速入門，您將能了解如何：

> [!div class="checklist"]
> * 佈建服務執行個體
> * 設定執行個體的設定伺服器
> * 於本機建置微服務應用程式
> * 部署每個微服務
> * 為您的應用程式指派公用端點

## <a name="prerequisites"></a>必要條件

>[!Note]
> 開始本快速入門之前，請確定您的 Azure 訂用帳戶可以存取 Azure Spring Cloud。  作為預覽服務，我們要求您與我們聯繫，讓我們可以將您的訂用帳戶新增至我們的允許清單。  如果您想要探索 Azure Spring Cloud 的功能，請[填寫此表單](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR-LA2geqX-ZLhi-Ado1LD3tUNDk2VFpGUzYwVEJNVkhLRlcwNkZFUFZEUS4u) \(英文\)。  雖然 Azure 春季雲端處於預覽狀態，但 Microsoft 在沒有 SLA 的情況下提供有限的支援。  如需預覽期間支援的詳細資訊，請參閱此[支援常見問題](https://azure.microsoft.com/support/faq/)。

開始之前，請確定您的 Azure 訂用帳戶具有必要的相依性：

1. [安裝 Git](https://git-scm.com/)
2. [安裝 JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
3. [安裝 Maven 3.0 或更新版本](https://maven.apache.org/download.cgi) \(英文\)
4. [安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [註冊 Azure 訂用帳戶](https://azure.microsoft.com/free/)

> [!TIP]
> Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。  它已預先安裝常用的 Azure 工具，包括 Git、JDK、Maven 和 Azure CLI 的最新版本。 如果您已登入 Azure 訂用帳戶，請從 shell.azure.com 啟動您的 [Azure Cloud Shell](https://shell.azure.com) \(英文\)。  您可以[閱讀我們的文件](../cloud-shell/overview.md)，以深入了解 Azure Cloud Shell

## <a name="install-the-azure-cli-extension"></a>安裝 Azure CLI 擴充功能

使用下列命令來安裝適用于 Azure CLI 的 Azure 春季雲端擴充功能

```Azure CLI
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

## <a name="provision-a-service-instance-using-the-azure-cli"></a>使用 Azure CLI 布建服務實例

登入 Azure CLI 並選擇您的有效定用帳戶。 請務必選擇已加入 Azure Spring Cloud 白清單的有效訂用帳戶

```Azure CLI
az login
az account list -o table
az account set --subscription
```

開啟 Azure CLI 視窗並執行下列命令來佈建 Azure Spring Cloud 的執行個體。 請注意，我們也會告訴 Azure 春季雲端在這裡指派公用網域。

```azurecli
    az spring-cloud create -n <resource name> -g <resource group name> --is-public true
```

服務實例將需要約五分鐘的時間來部署。

使用下列命令來設定預設資源群組名稱及叢集名稱：

```azurecli
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```

## <a name="create-the-spring-cloud-application"></a>建立春季雲端應用程式

下列命令會在您的訂用帳戶中建立春季雲端應用程式。  這會建立空的彈簧雲端服務，讓我們可以將應用程式上傳到其中。

```azurecli
az spring-cloud app create -n <app-name>
```

## <a name="deploy-your-spring-cloud-application"></a>部署您的春天雲端應用程式

您可以從預先建立的 JAR，或從 Gradle 或 Maven 存放庫部署您的應用程式。  請在下方尋找每個案例的指示。

### <a name="deploy-a-built-jar"></a>部署建立的 JAR

若要從您的本機電腦上建立的 JAR 進行部署，請確定您的組建會產生一個[FAT JAR](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-build.html#howto-create-an-executable-jar-with-maven)。

將 fat JAR 部署至作用中的部署

```azurecli
az spring-cloud app deploy -n <app-name> --jar-path <path-to-fat-JAR>
```

將 fat JAR 部署至特定部署

```azurecli
az spring-cloud app deployment create --app <app-name> -n <deployment-name> --jar-path <path-to-built-jar>
```

### <a name="deploy-from-source-code"></a>從原始程式碼部署

Azure 春季雲端會使用[kpack](https://github.com/pivotal/kpack)來建立您的專案。  您可以使用 Azure CLI 來上傳原始程式碼、使用 kpack 建立您的專案，並將其部署至目標應用程式。

> [!WARNING]
> 專案必須在 `target` 的 `MANIFEST.MF` 中只產生一個具有 `main-class` 專案的 JAR 檔案（適用于 Maven 部署或 `build/libs` （適用于 Gradle 部署）。  具有 `main-class` 專案的多個 JAR 檔案會導致部署失敗。

針對單一模組 Maven/Gradle 專案：

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy -n <app-name>
```

針對具有多個模組的 Maven/Gradle 專案，針對每個模組重複執行：

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy -n <app-name> --target-module <relative-path-to-module>
```

### <a name="show-deployment-logs"></a>顯示部署記錄

使用下列命令來檢查 kpack 組建記錄檔：

```azurecli
az spring-cloud app show-deploy-log -n <app-name> [-d <deployment-name>]
```

> [!NOTE]
> 如果部署是使用 kpack 從來源建立的，則 kpack 記錄只會顯示最新的部署。

## <a name="assign-a-public-endpoint-to-gateway"></a>將公用端點指派到閘道

1. 開啟 [應用程式儀表板] 頁面。
2. 選取 `gateway` 應用程式以顯示 [應用程式詳細資料] 頁面。
3. 選取 [指派網域] 來將公用端點指派到閘道。 這可能會需要幾分鐘的時間。 
4. 將已指派的公用 IP 輸入至您的瀏覽器，以檢視正在執行的應用程式。

## <a name="next-steps"></a>後續步驟

在此快速入門中，您已了解如何：

> [!div class="checklist"]
> * 佈建服務執行個體
> * 設定執行個體的設定伺服器
> * 於本機建置微服務應用程式
> * 部署每個微服務
> * 編輯應用程式的環境變數
> * 指派應用程式閘道的公用 IP

> [!div class="nextstepaction"]
> [準備您的 Azure Spring Cloud 應用程式以進行部署](spring-cloud-tutorial-prepare-app-deployment.md)
