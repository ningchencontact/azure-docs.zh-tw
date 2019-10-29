---
title: 快速入門：使用 Maven 啟動應用程式 - Azure Spring Cloud
description: 使用 Maven 啟動範例應用程式
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/05/2019
ms.author: v-vasuke
ms.openlocfilehash: e773b997cca3fa9a1f11fec2ac449e1fc11c5364
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554565"
---
# <a name="quickstart-launch-an-azure-spring-cloud-app-by-using-the-maven-plug-in"></a>快速入門：使用 Maven 外掛程式啟動 Azure Spring Cloud 應用程式

您可以使用 Azure Spring Cloud 的 Maven 外掛程式來輕鬆地建立及更新 Azure Spring Cloud 服務應用程式。 藉由預先定義設定，您可以將應用程式部署到現有 Azure Spring Cloud 服務。 在此文章中，您將使用稱為 PiggyMetrics 的範例應用程式來示範此功能。

>[!Note]
> 開始本快速入門之前，請確定您的 Azure 訂用帳戶可以存取 Azure Spring Cloud。 作為預覽服務，我們邀請您與我們聯繫，讓我們可以將您的訂用帳戶新增至我們的允許清單。 如果您想要探索 Azure Spring Cloud 的功能，請填寫並提交 [Azure Spring Cloud (個人預覽版) - 意願表單](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR-LA2geqX-ZLhi-Ado1LD3tUNDk2VFpGUzYwVEJNVkhLRlcwNkZFUFZEUS4u)。

>[!TIP]
> Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的命令。 其中已預先安裝常用的 Azure 工具，包括 Git、Java 開發套件 (JDK)、Maven 和 Azure CLI 的最新版本。 如果您已登入 Azure 訂用帳戶，請啟動 [Azure Cloud Shell](https://shell.azure.com)。 如需詳細資訊，請參閱 [Azure Cloud Shell 概觀](../cloud-shell/overview.md)。

若要完成本快速入門：

1. [安裝 Git](https://git-scm.com/)。
2. [安裝 JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)。
3. [安裝 Maven 3.0 或更新版本](https://maven.apache.org/download.cgi)。
4. [安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。
5. [註冊免費的 Azure 訂用帳戶](https://azure.microsoft.com/free/)。

## <a name="install-the-azure-cli-extension"></a>安裝 Azure CLI 擴充功能

使用下列命令安裝適用於 Azure CLI 的 Azure Spring Cloud 擴充功能：

```Azure CLI
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

## <a name="provision-a-service-instance-on-the-azure-portal"></a>在 Azure 入口網站上佈建服務執行個體

1. 在網頁瀏覽器中，開啟 [Azure 入口網站](https://portal.azure.com)並登入您的帳戶。

1. 搜尋 **Azure Spring Cloud** 並加以選取。 
1. 在 [概觀] 頁面上選取 [建立]  ，然後執行下列動作：  

    a. 在 [服務名稱]  方塊中，指定您服務執行個體的名稱。 名稱長度必須為 4 到 32 個字元，且只能包含小寫字母、數字及連字號。 服務名稱的第一個字元必須是字母，且最後一個字元必須是字母或數字。  

    b. 在 [訂用帳戶]  下拉式清單中，選取您希望用來支付此資源的訂用帳戶。 請確定此訂用帳戶已新增至我們的 Azure Spring Cloud 允許清單。  

    c. 在 [資源群組]  方塊中，建立新的資源群組。 為新資源建立資源群組是最佳做法。  

    d. 在 [位置]  下拉式清單中，選取服務執行個體的位置。 目前支援的位置包括美國東部、美國西部 2、歐洲西部和東南亞。
    
部署服務需要約 5 分鐘。 部署好服務之後，服務執行個體的 [概觀]  頁面會隨即出現。

## <a name="set-up-your-configuration-server"></a>設定您的設定伺服器

1. 在服務的 [概觀]  頁面上選取 [設定伺服器]  。
1. 在 [預設存放庫]  區段中，將 [URI]  設定為 **https://github.com/Azure-Samples/piggymetrics** ，將 [標籤]  設定為 **config**，然後選取 [套用]  以儲存變更。

## <a name="clone-and-build-the-sample-application-repository"></a>複製並建置範例應用程式存放庫

1. 執行下列命令以複製 Git 存放庫：

    ```azurecli
    git clone https://github.com/Azure-Samples/PiggyMetrics
    ```
  
1. 變更目錄，並執行下列命令來建置專案：

    ```azurecli
    cd PiggyMetrics
    mvn clean package -DskipTests
    ```

## <a name="generate-and-deploy-the-azure-spring-cloud-configuration"></a>產生並部署 Azure Spring Cloud 設定

1. 若要讓 Maven 能夠與 Azure Spring Cloud 搭配使用，請將下列程式碼新增至您的 pom.xml  或 settings.xml  檔案。

    ```xml
    <pluginRepositories>
      <pluginRepository>
        <id>maven.snapshots</id>
        <name>Maven Central Snapshot Repository</name>
        <url>https://oss.sonatype.org/content/repositories/snapshots/</url>
        <releases>
          <enabled>false</enabled>
        </releases>
        <snapshots>
          <enabled>true</enabled>
        </snapshots>
      </pluginRepository>
    </pluginRepositories>
    ```

1. 執行下列命令來產生設定：

    ```azurecli
    mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:0.1.0-SNAPSHOT:config
    ```

    a. 選取模組 `gateway`、`auth-service` 和 `account-service`。

    b. 選取您的訂用帳戶和 Azure Spring Cloud 服務叢集。

    c. 從提供的專案清單中，輸入與 `gateway` 相對應的數字，以授與公用存取權。
    
    d. 確認設定。

1. 使用下列命令來部署應用程式：

   ```azurecli
   mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:0.1.0-SNAPSHOT:deploy
   ```

1. 您可以使用上述命令輸出中提供的 URL 來存取 PiggyMetrics。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已經從 Maven 存放庫部署 Spring Cloud 應用程式。 若要深入了解 Azure Spring Cloud，請繼續進行準備應用程式以進行部署的教學課程。

> [!div class="nextstepaction"]
> [準備您的 Azure Spring Cloud 應用程式以進行部署](spring-cloud-tutorial-prepare-app-deployment.md)
