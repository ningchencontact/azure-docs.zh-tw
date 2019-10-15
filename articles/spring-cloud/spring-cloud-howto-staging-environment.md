---
title: 在 Azure Spring Cloud 中設定預備環境 | Microsoft Docs
description: 了解如何搭配 Azure Spring Cloud 使用藍色-綠色部署
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: v-vasuke
ms.openlocfilehash: 454eeaa2568891ec35fe698cdb20c5448e10887e
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038297"
---
# <a name="how-to-set-up-a-staging-environment"></a>如何設定預備環境

此文章將會示範如何在 Azure Spring Cloud 中使用藍色-綠色部署模式來利用預備部署。 它也會示範如何在不直接變更生產環境部署的情況下，將預備部署置於生產環境中。

## <a name="prerequisites"></a>必要條件

此文章假設您已根據我們[關於啟動應用程式的教學課程](spring-cloud-quickstart-launch-app-portal.md)部署 PiggyMetrics 應用程式。 PiggyMetrics 包含三個應用程式："gateway"、"account-service" 及 "auth-service"。  

如果您想要搭配此範例使用不同的應用程式，您必須在該應用程式對外公開的部分作出一個簡單的變更。  此變更會將您的預備部署與生產環境區分開來。

>[!NOTE]
> 開始本快速入門之前，請確定您的 Azure 訂用帳戶可以存取 Azure Spring Cloud。  作為預覽服務，我們要求您與我們聯繫，讓我們可以將您的訂用帳戶新增至我們的允許清單。  如果您想要探索 Azure Spring Cloud 的功能，請透過電子郵件與我們聯繫：azure-spring-cloud@service.microsoft.com。

>[!TIP]
> Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。  它已預先安裝常用的 Azure 工具，包括 Git、JDK、Maven 和 Azure CLI 的最新版本。 如果您已登入 Azure 訂用帳戶，請從 shell.azure.com 啟動您的 [Azure Cloud Shell](https://shell.azure.com) \(英文\)。  您可以[閱讀我們的文件](../cloud-shell/overview.md)，以深入了解 Azure Cloud Shell

若要完成此文章：

1. [安裝 Git](https://git-scm.com/)
1. [安裝 JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
1. [安裝 Maven 3.0 或更新版本](https://maven.apache.org/download.cgi) \(英文\)
1. [安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
1. [註冊 Azure 訂用帳戶](https://azure.microsoft.com/free/)

## <a name="install-the-azure-cli-extension"></a>安裝 Azure CLI 擴充功能

使用下列命令安裝適用於 Azure CLI 的 Azure Spring Cloud 擴充功能

```azurecli
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```
    
## <a name="view-all-deployments"></a>檢視所有部署

在 Azure 入口網站中移至您的服務執行個體，然後選取 [部署管理]  以檢視所有部署。 您可以選取每個部署以查看詳細資訊。

## <a name="create-a-staging-deployment"></a>建立預備部署

1. 在您的本機開發環境中，對 Piggy Metric 的閘道應用程式作出一個小修改。 例如，變更 `gateway/src/main/resources/static/css/launch.css` 中的色彩。 這將能讓您輕鬆區分這兩個部署。 執行下列命令以取得 jar 套件： 

    ```azurecli
    mvn clean package
    ```

1. 使用 Azure CLI 建立新部署，並將它的預備部署名稱命名為 "green"。

    ```azurecli
    az spring-cloud app deployment create -g <resource-group-name> -s <service-instance-name> --app gateway -n green --jar-path gateway/target/gateway.jar
    ```

1. 在部署成功完成之後，請從 [應用程式儀表板]  存取閘道頁面，並在左側的 [應用程式執行個體]  索引標籤中查看您所有的執行個體。
  
> [!NOTE]
> 探索狀態為 "OUT_OF_SERVICE"，來使流量不會在驗證完成之前路由傳送到這個部署。

## <a name="verify-the-staging-deployment"></a>確認預備部署

返回 [部署管理]  頁面並選取您的新部署。 部署狀態應該會顯示為 [正在執行]  。 [指派/解除指派網域] 按鈕將會停用，因為它是預備環境。

1. 在 [概觀]  頁面中，您應該會看到 [測試端點]  。 將它複製並貼到新的瀏覽器頁面，您應該會看到新的 Piggy Metrics 頁面。

>[!TIP]
> * 確認您測試端點的結尾是 "/"，以確保 CSS 能正確載入。  
> * 如果您的瀏覽器需要您輸入登入認證來查看頁面，請使用 [URL decode](https://www.urldecoder.org/) \(英文\) 來將您的測試端點解碼。 URL decode 會以 "https://\<使用者名稱>:\<密碼>@\<叢集名稱>.test.azureapps.io/gateway/green" 的格式傳回 URL。  請使用這個來存取您的端點。

>[!NOTE]    
> 設定伺服器設定會套用到您的預備環境和生產環境。 例如，如果您將設定伺服器中應用程式閘道的內容路徑 (`server.servlet.context-path`) 設定為「某個路徑」  ，您綠色部署的路徑就會變更為："https://\<使用者名稱>:\<密碼>@\<叢集名稱>.test.azureapps.io/gateway/green/某個路徑/..."
 
 如果您在此時造訪您對外公開的應用程式閘道，您應該會看到沒有新變更的舊頁面。
    
## <a name="set-the-green-as-production-deployment"></a>將綠色設定為生產環境部署

1. 在預備環境中確認變更之後，您便可以將它推送到生產環境。 返回 [部署管理]  並選取 "gateway" 應用程式之前的核取方塊。
2. 選取 [設定部署]。
3. 從 [生產環境部署] 功能表中選取 [綠色]，然後選取 [套用] 
4. 移至您閘道應用程式的 [概觀]  頁面。 如果您已經針對閘道應用程式指派網域，URL 將會出現在 [概觀]  頁面上。 造訪該 URL，您將會看見修改過的 Piggy Metrics 頁面。

>[!NOTE]
> 在將綠色部署設定為生產環境之後，先前的部署將會變成預備部署。

## <a name="modify-the-staging-deployment"></a>修改預備部署

如果您對變更不滿意，則可以修改應用程式程式碼，建置新的 jar 套件，然後使用 Azure CLI 將它上傳到您的綠色部署。

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-a-staging-deployment"></a>刪除預備部署

若要從 Azure 入口網站刪除預備部署，請瀏覽到您的預備部署頁面，然後選取 [刪除]  按鈕。

或者，使用下列命令從 Azure CLI 刪除您的預備部署：

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```
