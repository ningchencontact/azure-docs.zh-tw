---
title: 在 Azure 春季雲端中設定預備環境 |Microsoft Docs
description: 瞭解如何使用 Azure 春季雲端的藍綠部署
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: jeconnoc
ms.openlocfilehash: 24ce4dee04e4daf3eaee4144f8dc56de5867bbca
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607217"
---
# <a name="how-to-set-up-a-staging-environment"></a>如何設定預備環境

本文將說明如何使用 Azure 春季雲端中的藍色綠色部署模式來運用預備環境部署。 它也會示範如何將該預備環境部署放到生產環境中，而不需要直接變更生產部署。

## <a name="prerequisites"></a>必要條件

本文假設您已從我們的[教學課程啟動應用](spring-cloud-quickstart-launch-app-portal.md)程式來部署 PiggyMetrics 應用程式。 PiggyMetrics 包含三個應用程式：「閘道」、「帳戶服務」和「驗證服務」。  

如果您有想要在此範例中使用的不同應用程式，您必須在應用程式的公開部分中進行簡單的變更。  這種變更會區分您的預備部署與生產環境。

>[!TIP]
> Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。  它已預先安裝常用的 Azure 工具，包括最新版本的 Git、JDK、Maven 和 Azure CLI。 如果您已登入 Azure 訂用帳戶，請從 shell.azure.com 啟動您的[Azure Cloud Shell](https://shell.azure.com) 。  若要深入瞭解 Azure Cloud Shell，請[閱讀我們的檔](../cloud-shell/overview.md)

若要完成這篇文章：


## <a name="install-the-azure-cli-extension"></a>安裝 Azure CLI 擴充功能

使用下列命令來安裝適用于 Azure CLI 的 Azure 春季雲端擴充功能

```azurecli
az extension add --name spring-cloud
```
    
## <a name="view-all-deployments"></a>查看所有部署

移至 Azure 入口網站中的服務實例，然後選取 [**部署管理**] 以查看所有部署。 您可以選取每個部署，以取得詳細資料。

## <a name="create-a-staging-deployment"></a>建立預備部署

1. 在您的本機開發環境中，對 Piggy-back 計量的閘道應用程式進行小型修改。 例如，變更 `gateway/src/main/resources/static/css/launch.css`中的色彩。 這可讓您輕鬆區分這兩個部署。 執行下列命令來建立 jar 套件： 

    ```azurecli
    mvn clean package
    ```

1. 使用 Azure CLI 建立新的部署，並為其提供預備部署名稱「綠色」。

    ```azurecli
    az spring-cloud app deployment create -g <resource-group-name> -s <service-instance-name> --app gateway -n green --jar-path gateway/target/gateway.jar
    ```

1. 一旦部署成功完成，請從**應用程式儀表板**存取 [閘道] 頁面，並在左側查看 [**應用程式實例**] 索引標籤中的所有實例。
  
> [!NOTE]
> 探索狀態為「OUT_OF_SERVICE」，因此在驗證完成之前，不會將流量路由傳送至這個部署。

## <a name="verify-the-staging-deployment"></a>確認預備部署

1. 返回 [**部署管理**] 頁面，然後選取您的新部署。 部署狀態應該會顯示 [**正在**執行]。 [指派/解除指派網域] 按鈕將會停用，因為它是預備環境。

1. 在 [**總覽**] 頁面中，您應該會看到**測試端點**。 將它複製並貼到新的瀏覽器頁面中，您應該會看到 [新增 Piggy-back 計量] 頁面。

>[!TIP]
> * 確認您的測試端點結尾為 "/"，以確保 CSS 正確載入。  
> * 如果您的瀏覽器要求您輸入登入認證來查看頁面，請使用[URL](https://www.urldecoder.org/)解碼來解碼您的測試端點。 URL 解碼會傳回格式為 "HTTPs://\<username >：\<密碼 > @\<叢集名稱 >. test. azureapps. io/gateway/綠色" 的 URL。  使用此來存取您的端點。

>[!NOTE]    
> Config 伺服器設定適用于您的預備環境和生產環境。 例如，如果您在 config server 中將應用程式閘道的內容路徑（`server.servlet.context-path`）設定為*somepath*，您的綠色部署的路徑就會變更： "HTTPs://\<username >：\<password > @\<叢集名稱 >。test.azureapps.io/gateway/green/somepath/... "
 
 如果您在此時造訪公開的應用程式閘道，您應該會看到舊的頁面，而不會有新的變更。
    
## <a name="set-the-green-as-production-deployment"></a>將綠色設定為生產部署

1. 在預備環境中驗證您的變更之後，您就可以將它推送到生產環境。 返回 [**部署管理**]，然後選取 [閘道] 應用程式前面的核取方塊。
2. 選取 [設定部署]。
3. 從 [生產部署] 功能表選取 [綠色]，**然後選取 [** 套用]
4. 移至您的閘道應用程式**總覽**頁面。 如果您已為閘道應用程式指派網域，則 URL 會出現在 [**總覽**] 頁面上。 流覽 URL，您將會看到 [已修改的 Piggy-back 計量] 頁面。

>[!NOTE]
> 一旦綠色部署設定為 [生產環境]，先前的部署就會變成預備部署。

## <a name="modify-the-staging-deployment"></a>修改預備環境部署

如果您不滿意變更，可以修改應用程式的程式碼、建立新的 jar 套件，然後使用 Azure CLI 將它上傳到您的環保部署。

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-a-staging-deployment"></a>刪除預備部署

藉由流覽至您的預備部署頁面並選取 [**刪除**] 按鈕，從 Azure 埠刪除您的預備環境部署。

或者，使用下列命令，將您的預備部署從 Azure CLI 中刪除：

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```
