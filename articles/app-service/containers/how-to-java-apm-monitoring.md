---
title: 設定 Linux 上的 Java APM 和監視工具 - Azure App Service
description: 了解如何將 App Service Linux 上執行的 Java 應用程式記錄和計量傳送給 NewRelic 與 AppDynamics APM 的提供者
services: app-service\web
author: rloutlaw
manager: angerobe
ms.service: app-service-web
ms.workload: web
ms.topic: article
ms.date: 03/21/2019
ms.author: astay;routlaw
ms.custom: seodec18
ms.openlocfilehash: e6a22258266bda18c9ff79590d88e70d512f6c77
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60849960"
---
# <a name="how-to-application-performance-monitoring-tools-with-java-apps-on-azure-app-service-on-linux"></a>操作說明：與 Linux 之 Azure App Service 上的 Java 應用程式搭配使用的應用程式效能監視工具

本文說明如何在 Linux 上的 Azure App Service 中，將其中所部署的 Java 應用程式連線至 NewRelic 和 AppDynamics 應用程式效能監視 (APM) 平台。

## <a name="configure-new-relic"></a>設定 NewRelic

1. 在 [NewRelic.com](https://newrelic.com/signup) 建立 NewRelic 帳戶
2. 從 NewRelic 下載 Java 代理程式，其檔案名稱類似 `newrelic-java-x.x.x.zip`。
3. 複製您的授權金鑰，以供稍後設定代理程式時使用。
4. [透過 SSH 連線到您的 App Service 執行個體](/azure/app-service/containers/app-service-linux-ssh-support)，然後建立新目錄 `/home/site/wwwroot/apm`。
5. 將解壓縮的 NewRelic Java 代理程式檔案上傳至 `/home/site/wwwroot/apm` 之下的目錄。 您的代理程式檔案應位於 `/home/site/wwwroot/apm/newrelic`。
6. 在 `/home/site/wwwroot/apm/newrelic/newrelic.yml` 修改 YAML 檔案，並將預留位置授權值取代為您自己的授權金鑰。
7. 在 Azure 入口網站中，瀏覽至您在 App Service 中的應用程式，並建立新的應用程式設定。
    - 如果您的應用程式使用 **Java SE**，請使用 `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` 值建立名為 `JAVA_OPTS` 的環境變數。
    - 如果您使用 **Tomcat**，請使用 `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` 值建立名為 `CATALINA_OPTS` 的環境變數。
    - 如果您使用**WildFly**，請參閱 New Relic 的文件[這裡](https://docs.newrelic.com/docs/agents/java-agent/additional-installation/wildfly-version-11-installation-java)安裝 Java 代理程式和 JBoss 設定的相關指引。
    - 如果您已經有 `JAVA_OPTS` 或 `CATALINA_OPTS` 的環境變數，請將 `javaagent` 選項附加至目前值的結尾。

## <a name="configure-appdynamics"></a>設定 AppDynamics

1. 在 [AppDynamics.com](https://www.appdynamics.com/community/register/) 建立 AppDynamics 帳戶
1. 從 AppDynamics 網站下載 Java 代理程式，其檔案名稱會類似 `AppServerAgent-x.x.x.xxxxx.zip`
1. [透過 SSH 連線到您的 App Service 執行個體](/azure/app-service/containers/app-service-linux-ssh-support)，然後建立新目錄 `/home/site/wwwroot/apm`。
1. 將 Java 代理程式檔案上傳至 `/home/site/wwwroot/apm` 之下的目錄。 您的代理程式檔案應位於 `/home/site/wwwroot/apm/appdynamics`。
1. 在 Azure 入口網站中，瀏覽至您在 App Service 中的應用程式，並建立新的應用程式設定。
    - 如果您使用 **Java SE**，請使用 `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<YOUR_SITE_NAME>` 值建立名為 `JAVA_OPTS` 的環境變數，其中 `<YOUR_SITE_NAME>` 是您的 App Service 名稱。
    - 如果您使用 **Tomcat**，請使用 `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<YOUR_SITE_NAME>` 值建立名為 `CATALINA_OPTS` 的環境變數，其中 `<YOUR_SITE_NAME>` 是您的 App Service 名稱。
    - 如果您使用**WildFly**，請參閱 AppDynamics 文件[這裡](https://docs.appdynamics.com/display/PRO45/JBoss+and+Wildfly+Startup+Settings)安裝 Java 代理程式和 JBoss 設定的相關指引。
