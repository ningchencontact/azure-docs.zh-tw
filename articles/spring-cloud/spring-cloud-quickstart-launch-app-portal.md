---
title: 使用 Azure 入口網站來啟動 Azure Spring Cloud 應用程式
description: 使用 Azure 入口網站將範例應用程式部署到 Azure Spring Cloud。
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/04/2019
ms.author: v-vasuke
ms.openlocfilehash: 59770bccec57220560eeb5a5204e574ce172fc80
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2019
ms.locfileid: "72296512"
---
# <a name="quickstart-launch-an-azure-spring-cloud-application-using-the-azure-portal"></a>快速入門：使用 Azure 入口網站來啟動 Azure Spring Cloud 應用程式

Azure Spring Cloud 可讓您輕鬆地在 Azure 上執行以 Spring Cloud 為基礎的微服務應用程式。

本快速入門會示範如何將現有的 Spring Cloud 應用程式部署到 Azure。 本教學課程所使用之範例應用程式程式碼的連結在[這裡](https://github.com/Azure-Samples/PiggyMetrics) \(英文\)。 當您完成時，所提供的範例應用程式將可在線上存取，並可透過 Azure 入口網站來管理。

透過遵循本快速入門，您將能了解如何：

> [!div class="checklist"]
> * 佈建服務執行個體
> * 設定執行個體的設定伺服器
> * 於本機建置微服務應用程式
> * 部署每個微服務
> * 為您的應用程式指派公用端點

## <a name="prerequisites"></a>必要條件

>[!Note]
> 開始本快速入門之前，請確定您的 Azure 訂用帳戶可以存取 Azure Spring Cloud。  作為預覽服務，我們要求客戶與我們聯繫，讓我們可以將您的訂用帳戶新增至我們的允許清單。  如果您想要探索 Azure Spring Cloud 的功能，請透過電子郵件與我們聯繫：azure-spring-cloud@service.microsoft.com。

>[!TIP]
> Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。  它已預先安裝常用的 Azure 工具，包括 Git、JDK、Maven 和 Azure CLI 的最新版本。 如果您已登入 Azure 訂用帳戶，請從 shell.azure.com 啟動您的 [Azure Cloud Shell](https://shell.azure.com) \(英文\)。  您可以[閱讀我們的文件](../cloud-shell/overview.md)，以深入了解 Azure Cloud Shell

若要完成本快速入門：

1. [安裝 Git](https://git-scm.com/)
2. [安裝 JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
3. [安裝 Maven 3.0 或更新版本](https://maven.apache.org/download.cgi) \(英文\)
4. [安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [註冊 Azure 訂用帳戶](https://azure.microsoft.com/free/)

## <a name="install-the-azure-cli-extension"></a>安裝 Azure CLI 擴充功能

使用下列命令安裝適用於 Azure CLI 的 Azure Spring Cloud 擴充功能

```Azure CLI
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

## <a name="provision-a-service-instance-on-the-azure-portal"></a>在 Azure 入口網站上佈建服務執行個體

1. 在網頁瀏覽器中，開啟 [Azure 入口網站中 Azure Spring Cloud 的這個連結](https://ms.portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=AppPlatformExtension#blade/Microsoft_Azure_Marketplace/MarketplaceOffersBlade/selectedMenuItemId/home/searchQuery/Azure%20Spring%20Cloud)。

    ![ASC 入口網站的螢幕擷取畫面](media/spring-cloud-quickstart-launch-app-portal/goto-portal.png)

1. 選取 [Azure Spring Cloud]  以移至 [概觀] 頁面。 然後選取 [建立]  按鈕以開始使用。

1. 填寫表單，並考慮下列指導方針：
    - 服務名稱：指定您服務執行個體的名稱。  名稱長度必須為 4 到 32 個字元，且只能包含小寫字母、數字及連字號。  服務名稱的第一個字元必須是字母，且最後一個字元必須是字母或數字。
    - 訂用帳戶：選取您想要用來支付此資源費用的訂用帳戶。  請確定此訂用帳戶已新增至我們的 Azure Spring Cloud 允許清單。
    - 資源群組：為新資源建立新資源群組是最佳做法。
    - 位置：選取您服務執行個體的位置。 目前支援的位置包括美國東部、美國西部 2、歐洲西部和東南亞。
    
部署服務需要約 5 分鐘。  一旦部署之後，服務執行個體的 [概觀]  頁面隨即出現。

## <a name="set-up-your-configuration-server"></a>設定您的設定伺服器

1. 移至服務 [概觀]  頁面並選取 [設定伺服器]  。

1. 在 [預設存放庫]  區段中，將 [URI]  設定為 "https://github.com/Azure-Samples/piggymetrics"，將 [標籤]  設定為 "config"，然後選取 [套用]  以儲存變更。

    ![ASC 入口網站的螢幕擷取畫面](media/spring-cloud-quickstart-launch-app-portal/portal-config.png)

## <a name="build-and-deploy-microservice-applications"></a>建置並部署微服務應用程式

1. 開啟命令視窗並執行下列命令，以將範例應用程式存放庫複製到本機電腦。

    ```cli
    git clone https://github.com/Azure-Samples/piggymetrics
    ```

1. 執行下列命令來建置專案。

    ```cli
    cd PiggyMetrics
    mvn clean package -DskipTests
    ```

1. 登入 Azure CLI 並設定您的有效訂用帳戶。

    ```cli
    # Login to Azure CLI
    az login

    # List all subscriptions
    az account list -o table

    # Set active subscription
    az account set --subscription <target subscription ID>
    ```

1. 將名稱指派給您的資源群組和服務。 務必將下列預留位置取代為您稍早在本教學課程中所佈建的資源群組名稱和服務名稱。

    ```azurecli
    az configure --defaults group=<resource group name>
    az configure --defaults spring-cloud=<service instance name>
    ```

1. 建立 `gateway` 應用程式並部署 JAR 檔案。

    ```azurecli
    az spring-cloud app create -n gateway
    az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar
    ```

1. 遵循相同的模式來建立 `account-service` 和 `auth-service` 應用程式，並部署它們的 JAR 檔案。

    ```cli
    az spring-cloud app create -n account-service
    az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar
    az spring-cloud app create -n auth-service
    az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
    ```

1. 系統需要幾分鐘的時間來完成部署應用程式。 若要確認它們已部署，請移至 Azure 入口網站的 [應用程式]  刀鋒視窗。 您應該會看到個別與這三個應用程式個別的行。

## <a name="assign-a-public-endpoint-to-gateway"></a>將公用端點指派到閘道

1. 開啟左側功能表中的 [應用程式]  索引標籤。
2. 選取 `gateway` 應用程式，以顯示 [概觀]  頁面。
3. 選取 [指派網域]  來將公用端點指派到閘道。 這可能會需要幾分鐘的時間。

    ![ASC 入口網站的螢幕擷取畫面](media/spring-cloud-quickstart-launch-app-portal/portal-endpoint.png)

1. 將已指派的公用端點 (標示為 **URL**) 輸入至您的瀏覽器，以檢視正在執行的應用程式。

    ![ASC 入口網站的螢幕擷取畫面](media/spring-cloud-quickstart-launch-app-portal/sample-app.png)


## <a name="next-steps"></a>後續步驟

在此快速入門中，您已了解如何：

> [!div class="checklist"]
> * 佈建服務執行個體
> * 設定執行個體的設定伺服器
> * 於本機建置微服務應用程式
> * 部署每個微服務
> * 為您的應用程式閘道指派公用端點

> [!div class="nextstepaction"]
> [準備您的 Azure Spring Cloud 應用程式以進行部署](spring-cloud-tutorial-prepare-app-deployment.md)
