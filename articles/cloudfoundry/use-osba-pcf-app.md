---
title: 搭配使用 Open Service Broker for Azure 資料庫與 Pivotal Cloud Foundry 上的應用程式
author: zr-msft
manager: jeconnoc
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 01/11/2019
ms.topic: tutorial
description: 說明如何設定 Pivotal Cloud Foundry 應用程式以使用 Open Service Broker for Azure 資料庫
keywords: Pivotal Cloud Foundry, Cloud Foundry, Open Service Broker, Open Service Broker for Azure
tags: Cloud-Foundry
ms.openlocfilehash: d553cd09ef42e47e3a10fb96039063b8aae665cb
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2019
ms.locfileid: "54258855"
---
# <a name="tutorial-use-an-open-service-broker-for-azure-database-with-an-application-on-pivotal-cloud-foundry"></a>教學課程：搭配使用 Open Service Broker for Azure 資料庫與 Pivotal Cloud Foundry 上的應用程式

搭配使用 Open Service Broker for Azure 與 Pivotal Cloud Foundry 執行個體可讓您直接從 Cloud Foundry CLI 與 Pivotal Cloud Foundry 執行個體在 Azure 中佈建服務 (例如，資料庫)。 您也可以將這些服務繫結至在 Pivotal Cloud Foundry 執行個體中執行的應用程式。 當您以這種方式將應用程式繫結至服務時，將不必更新應用程式內的任何程式碼或組態。 本文說明如何搭配使用資料庫的 Open Service Broker for Azure 服務與 Pivotal Cloud Foundry 中的應用程式。

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 在 Pivotal Cloud Foundry 中準備應用程式空間。
> * 從 GitHub 複製範例應用程式來源。
> * 準備應用程式以便進行部署。
> * 部署應用程式。
> * 使用 Open Service Broker for Azure 建立資料庫。
> * 將資料庫繫結至應用程式。

## <a name="prerequisites"></a>必要條件

在繼續之前，您必須先：

* [安裝及設定 Pivotal Cloud Foundry](create-cloud-foundry-on-azure.md)
* 搭配 Cloud Foundry 執行個體來[安裝及設定 Open Service Broker for Azure](https://network.pivotal.io/products/azure-open-service-broker-pcf)

以下範例是已安裝並設定 Open Service Broker for Azure 圖格的 Pivotal Cloud Foundry Ops Manager 畫面：

![已安裝 Open Service Broker for Azure 的 Pivotal Cloud Foundry](media/pcf-ops-manager.png)

## <a name="prepare-your-application-space-in-pivotal-cloud-foundry"></a>在 Pivotal Cloud Foundry 中準備應用程式空間

為了將應用程式部署至 Pivotal Cloud Foundry 執行個體，您必須使用 `cf` 命令列工具來登入。 您也必須鎖定您所需的組織和空間。

若要確認您已登入並顯示您鎖定的空間，請使用 `cf target`。 下列範例示範已經以「管理員」身分登入、使用 myorg 組織並鎖定 dev 空間的使用者：

```cmd
$ cf target

api endpoint:   https://api.system.40.85.111.222.cf.pcfazure.com
api version:    2.120.0
user:           admin
org:            myorg
space:          dev
```

若要登入，請使用 `cf login`：

```cmd
cf login -a https://api.SYSTEMDOMAINURL --skip-ssl-validation
```

若要建立新的組織和空間，請使用 `cf create-org` 和 `cf create-space`：

```cmd 
cf create-org myorg
cf create-space dev -o myorg
```

若要鎖定空間，請使用 `cf target`：

```cmd
cf target -o myorg -s dev
```

## <a name="get-application-code"></a>取得應用程式程式碼

為了讓應用程式使用 Open Service Broker for Azure，您必須以資料來源的形式 (例如，資料庫) 使用應用程式。 在本文中，我們會使用 [Cloud Foundry 的春天音樂範例應用程式](https://github.com/cloudfoundry-samples/spring-music)來示範使用資料來源的應用程式。

從 GitHub 複製應用程式，並瀏覽至其目錄：

```cmd
git clone https://github.com/cloudfoundry-samples/spring-music
cd spring music
```

> [!TIP]
> 若要檢視此應用程式的資料來源，請開啟 [src/main/resources/application.yml](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/resources/application.yml) 檔案。


## <a name="prepare-the-application-for-deployment"></a>準備應用程式以便進行部署

您必須先建置應用程式，才能再部署至 Pivotal Cloud Foundry 執行個體。 為了進行示範，我們也啟用了一些 DEBUG 記錄，以便記錄資料來源的連線資訊。

若要針對資料庫連線詳細資料啟用 DEBUG 記錄，請將下列 yaml 屬性新增至 application.yml 的結尾：

```yaml
---
logging:
  level:
    com.zaxxer.hikari: DEBUG
```

範例應用程式會使用 Gradle 將應用程式建置到 Spring Boot 可執行的 jar。 可執行的 jar 會部署至 Pivotal Cloud Foundry 執行個體。 若要建置應用程式：

```cmd
./gradlew clean assemble

Starting a Gradle Daemon (subsequent builds will be faster)

BUILD SUCCESSFUL in 10s
4 actionable tasks: 4 executed
```


## <a name="deploy-your-application"></a>部署應用程式

使用 `cf push` 命令將應用程式部署至 Pivotal Cloud Foundry 執行個體：

```cmd
cf push

Pushing from manifest to org myorg / space dev as admin...
Using manifest file /path/to/spring-music/manifest.yml
Getting app info...
Creating app with these attributes...
+ name:       spring-music
...
Waiting for app to start...

name:              spring-music
requested state:   started
routes:            spring-music-wacky-oribi.app.40.85.111.222.cf.pcfazure.com
...
     state     since                  cpu    memory         disk           details
#0   running   2018-12-06T21:24:06Z   0.0%   313.3M of 1G   170.7M of 1G
```

從顯示在 `cf push` 輸出中的「路由」複製值。 此路由是您會用來存取執行中應用程式的 URL。 例如︰

```cmd
...
routes:            spring-music-wacky-oribi.app.40.85.111.222.cf.pcfazure.com
...
```


應用程式部署好之後，您就可以檢視應用程式的記錄，以查看應用程式所使用的連線 URL。 下列命令會顯示應用程式的記錄，並使用 `grep` 來搜尋 jdbcUrl 組態。

```cmd
cf logs spring-music --recent | grep jdbcUrl

2018-12-07T14:44:30.57-0600 [APP/PROC/WEB/0] OUT 2018-12-07 20:44:30.574 DEBUG 24 --- [           main] com.zaxxer.hikari.HikariConfig           : jdbcUrl.........................jdbc:h2:mem:testdb;DB_CLOSE_DELAY=-1;DB_CLOSE_ON_EXIT=FALSE
```

請注意，應用程式所使用的 h2:mem:testdb 是記憶體內部資料庫。 當記憶體內部資料庫相依於 classpath 且[自動設定](https://docs.spring.io/spring-boot/docs/current/reference/html/using-boot-auto-configuration.html)已啟用時，Spring 應用程式會自動設定為使用記憶體內部資料庫。 範例應用程式[已設定 h2 記憶體內部資料庫相依性](https://github.com/cloudfoundry-samples/spring-music/blob/master/build.gradle#L49)，並已在 [src/main/java/org/cloudfoundry/samples/music/Application.java](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/java/org/cloudfoundry/samples/music/Application.java#L8) 中啟用自動設定。

在瀏覽器中使用應用程式的路由瀏覽至該應用程式。 路由或 URL 會顯示在 `cf push` 命令的輸出中。

> [!TIP]
> 您也可以執行 `cf apps` 來顯示應用程式的 URL。

一旦您使用瀏覽器瀏覽至應用程式後，請藉由刪除一些現有專輯並建立幾個新專輯，來與該應用程式互動。 範例應用程式會使用記憶體內部資料庫來儲存變更。 您也會發現應用程式已填入一些[預設資料](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/resources/albums.json)。 

![有預設資料的春天音樂應用程式](media/music-app.png)

因為應用程式使用記憶體內部資料庫，如果您重新啟動或重新部署應用程式，所做的變更將不會保存下來。 為了示範在您進行一些變更後，所做的變更不會保留下來，請使用 `cf restage` 重新暫存應用程式：

```cmd
cf restage spring-music
```

應用程式重新暫存之後，請在瀏覽器中使用相同的 URL 瀏覽至應用程式。 請注意您所做的變更都已消失，顯示的是預設資料。

![有預設資料的春天音樂應用程式](media/music-app.png)

## <a name="create-a-database"></a>建立資料庫

若要使用 Open Service Broker 在 Azure 上建立永續性資料庫，請使用 `cf create-service` 命令。 下列命令會在 Azure 中，於 eastus 區域的資源群組 MyResourceGroup 中佈建 PostgreSQL 資料庫。 [PostgreSQL 模組參考文件](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/postgresql.md#provision)中有更多關於 resourceGroup、location 和其他 Azure 專屬 JSON 參數的資訊可供您參考：

```cmd
cf create-service azure-postgresql-9-6 general-purpose mypgsql -c '{"resourceGroup":"MyResourceGroup", "location":"eastus"}'
```

在 Pivotal Cloud Foundry 執行個體中，資料庫會顯示為名為 mypgsql 的服務。 資料庫完成佈建後 (應該會花費幾分鐘的時間)，您可以將其繫結至應用程式。 若要確認資料庫是否已完成佈建，請使用 `cf services` 命令：

```cmd
cf services

Getting services in org myorg / space dev as admin...

name      service                plan              bound apps     last operation
mypgsql   azure-postgresql-9-6   general-purpose                  create succeeded
```

如果服務已完成佈建，其「最後一項作業」值會是「建立成功」。

## <a name="bind-the-database-to-your-application"></a>將資料庫繫結至應用程式

使用 `bind-service` 命令將服務繫結至應用程式。

```cmd
cf bind-service spring-music mypgsql
```

在將服務繫結至應用程式之後，您必須重新暫存應用程式，變更才會生效。

```cmd
cf restage spring-music
```

應用程式已[設定為使用 Spring Cloud 連接器](https://github.com/cloudfoundry-samples/spring-music/blob/master/build.gradle#L45...L46)，以便讓 Pivotal Cloud Foundry 執行個體在應用程式的資料來源上使用[自動重新設定](https://docs.cloudfoundry.org/buildpacks/java/configuring-service-connections/spring-service-bindings.html#auto)。 在此情況下，Pivotal Cloud Foundry 執行個體會自動重新設定應用程式，讓應用程式在重新暫存時使用其針對資料來源所繫結的服務。

應用程式重新暫存後，就會使用 mypgsql 來儲存資料，而不是儲存在記憶體內部資料庫中。

您所做的變更現在都會跨重新啟動和重新部署作業而保存下來。 您也可以再次檢視應用程式的記錄，來查看應用程式所使用的連線 URL。

```cmd
cf logs spring-music --recent | grep jdbcUrl

2018-12-07T14:44:30.57-0600 [APP/PROC/WEB/0] OUT 2018-12-07 20:44:30.574 DEBUG 24 --- [           main] com.zaxxer.hikari.HikariConfig           : jdbcUrl.........................jdbc:h2:mem:testdb;DB_CLOSE_DELAY=-1;DB_CLOSE_ON_EXIT=FALSE
2018-12-07T14:48:58.10-0600 [APP/PROC/WEB/0] OUT 2018-12-07 20:48:58.107 DEBUG 16 --- [           main] com.zaxxer.hikari.HikariConfig           : jdbcUrl.........................jdbc:postgresql://12345678-aaaa-bbbb-cccc-1234567890ab.postgres.database.azure.com:5432/123456789?user=aabbcc1122@12345678-aaaa-bbbb-cccc-1234567890ab&password=<masked>&&sslmode=require
```

請注意，記錄中有兩個項目：

* h2:mem:testdb 的原始值。
* 應用程式重新暫存時，PostgreSQL 資料庫的更新值。

若要確認資料會保存至 PostgreSQL 資料庫，請在瀏覽器中瀏覽至應用程式、進行一些變更，然後重新暫存應用程式：

```cmd
cf restage spring-music
```

應用程式重新暫存之後，請在瀏覽器中使用相同的 URL 瀏覽至應用程式。 請注意，您所做的變更還在。

## <a name="cleanup"></a>清除

如果您想要讓應用程式與資料庫中斷連線，則可以使用 `cf unbind-service` 命令解除繫結。

```cmd
cf unbind-service spring-music mypgsql
```

類似於將應用程式繫結至服務，您必須重新暫存應用程式，這些變更才會生效。 此動作會讓 mypgsql 和應用程式維持不變，但應用程式會開始使用記憶體內部資料庫，而不是 mypgsql。

若要刪除資料庫，您可以使用 `cf delete-service` 命令。 您無法復原此動作，因此請先確定您想要刪除資料庫後再繼續。

```cmd
cf delete-service mypgsql
```

若要從 Pivotal Cloud Foundry 執行個體中移除應用程式：
 
```cmd
cf delete spring-music
```

## <a name="next-steps"></a>後續步驟

本教學課程的內容涵蓋如何將應用程式部署至 Pivotal Cloud Foundry，以及如何使用 Open Service Broker for Azure 建立資料庫。 文中也說明了如何將資料庫繫結至 Pivotal Cloud Foundry 執行個體內的應用程式。 如需如何將應用程式部署至 Azure 上 Cloud Foundry 的詳細資訊，請參閱：

* [Azure 上的 Cloud Foundry](../virtual-machines/linux/cloudfoundry-get-started.md)
* [將第一個應用程式部署到 Microsoft Azure 上的 Cloud Foundry](../virtual-machines/linux/cloudfoundry-deploy-your-first-app.md)