---
title: 即時串流 Azure 春季雲端應用程式記錄
description: 如何使用記錄串流立即查看應用程式記錄檔
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/14/2019
ms.openlocfilehash: fa2e7af51ff681da0bfdac928cc08bf75126a3b8
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156415"
---
# <a name="stream-azure-spring-cloud-app-logs-in-real-time"></a>即時串流 Azure 春季雲端應用程式記錄
Azure 春季雲端可讓 Azure CLI 中的記錄串流取得即時應用程式主控台記錄，以進行疑難排解。 您也可以[使用診斷設定來分析記錄和計量](./diagnostic-services.md)。

## <a name="prerequisites"></a>必要條件

* 針對春季雲端（最低版本0.2.0）安裝[Azure CLI 延伸](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli#install-the-azure-cli-extension)模組。
* 包含執行中應用程式的**Azure 春季雲端**實例，例如「[春季雲端應用程式](./spring-cloud-quickstart-launch-app-cli.md)」。

## <a name="use-cli-to-tail-logs"></a>使用 CLI 來記錄尾

若要避免重複指定您的資源群組和服務實例名稱，請設定預設的資源組名和叢集名稱。
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```
在下列範例中，命令中會省略資源群組和服務名稱。

### <a name="tail-log-for-app-with-single-instance"></a>具有單一實例之應用程式的尾記錄
如果名為 auth service 的應用程式只有一個實例，您可以使用下列命令來查看應用程式實例的記錄檔：
```
az spring-cloud app log tail -n auth-service
```
這會傳回記錄：
```
...
2020-01-15 01:54:40.481  INFO [auth-service,,,] 1 --- [main] o.apache.catalina.core.StandardService  : Starting service [Tomcat]
2020-01-15 01:54:40.482  INFO [auth-service,,,] 1 --- [main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.22]
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.a.c.c.C.[Tomcat].[localhost].[/uaa]  : Initializing Spring embedded WebApplicationContext
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.s.web.context.ContextLoader  : Root WebApplicationContext: initialization completed in 7203 ms

...
```

### <a name="tail-log-for-app-with-multiple-instances"></a>具有多個實例之應用程式的尾記錄
如果名為 `auth-service`的應用程式有多個實例，您可以使用 [`-i/--instance`] 選項來查看實例記錄檔。 例如，您可以藉由指定應用程式名稱和實例名稱來串流某個應用程式之實例的記錄檔：

```
az spring-cloud app log tail -n auth-service -i auth-service-default-12-75cc4577fc-pw7hb
```
您也可以從 Azure 入口網站取得應用程式實例。 
1. 流覽至您的資源群組，然後選取您的 Azure 春季雲端實例。
1. 從 Azure 春季 Cloud 實例總覽選取左側流覽窗格中的 [**應用程式**]。
1. 選取您的應用程式，然後按一下左側流覽窗格中的 [**應用程式實例**]。 
1. 將會顯示應用程式實例。

### <a name="continuously-stream-new-logs"></a>持續串流新的記錄檔
根據預設，`az spring-cloud ap log tail` 只會列印串流至應用程式主控台的現有記錄，然後結束。 如果您想要串流新的記錄檔，請新增-f （--遵循）：  

```
az spring-cloud app log tail -n auth-service -f
``` 
若要檢查所有支援的記錄選項：
``` 
az spring-cloud app log tail -h 
```

## <a name="next-steps"></a>後續步驟

* [使用診斷設定來分析記錄和計量](./diagnostic-services.md)

 





