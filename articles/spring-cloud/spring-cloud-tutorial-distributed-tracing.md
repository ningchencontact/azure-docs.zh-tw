---
title: 教學課程：搭配 Azure Spring Cloud 使用分散式追蹤 | Microsoft Docs
description: 了解如何透過 Azure Application Insights 使用 Spring Cloud 的分散式追蹤
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/06/2019
ms.author: v-vasuke
ms.openlocfilehash: 95aae47bddffd102c5d6d6dac67f22e6777cc3e7
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038227"
---
# <a name="tutorial-using-distributed-tracing-with-azure-spring-cloud"></a>教學課程：搭配 Azure Spring Cloud 使用分散式追蹤

Spring Cloud 的分散式追蹤工具可讓您輕鬆地對複雜問題進行偵錯及監視。 Azure Spring Cloud 能將 [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) \(英文\) 與 Azure 的 [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) 整合，以從 Azure 入口網站提供強大的分散式追蹤功能。

在本文中，您將了解如何：

> [!div class="checklist"]
> * 在 Azure 入口網站中啟用分散式追蹤
> * 將 Spring Cloud Sleuth 新增至您的應用程式
> * 檢視微服務應用程式的相依性對應
> * 搭配不同的篩選搜尋追蹤資料

## <a name="prerequisites"></a>必要條件

若要完成本教學課程：

* 已佈建且正在執行的 Azure Spring Cloud 服務。  完成[此快速入門](spring-cloud-quickstart-launch-app-cli.md)以佈建並啟動 Azure Spring Cloud 服務。
    
## <a name="add-dependencies"></a>新增相依性

將下列行新增至 application.properties 檔案，來讓 zipkin sender 能傳送至網路：

```xml
spring.zipkin.sender.type = web
```

如果您已遵循我們[針對準備 Azure Spring Cloud 應用程式的指南](spring-cloud-tutorial-prepare-app-deployment.md)，則可以略過下一個步驟。 否則，請移至您的本機開發環境，並編輯您的 `pom.xml` 檔案以包含 Spring Cloud Sleuth 相依性：

```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-sleuth</artifactId>
            <version>${spring-cloud-sleuth.version}</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
<dependencies>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-sleuth</artifactId>
    </dependency>
</dependencies>
```

* 再次針對您的 Azure Spring Cloud 服務進行建置及部署以反映這些變更。 

## <a name="modify-the-sample-rate"></a>修改採樣速率
您可以修改採樣速率來變更收集遙測的速率。 例如，如果您想要將採樣速率降低一半，請移至您的 `application.properties` 檔案，然後變更下列行：

```xml
spring.sleuth.sampler.probability=0.5
```

如果您已經建置並部署應用程式，則可以在 Azure CLI 或入口網站中將上述行新增為環境變數來修改採樣速率。 

## <a name="enable-application-insights"></a>啟用 Application Insights

1. 在 Azure 入口網站中，移至您的 Azure Spring Cloud 服務頁面。
1. 在 [監視] 區段中，選取 [分散式追蹤]  。
1. 選取 [編輯設定]  以編輯或新增新的設定。
1. 建立新的 Application Insights 查詢，或是選取現有查詢。
1. 選擇您想要監視的記錄類別，並指定保留期間 (以天為單位)。
1. 選取 [套用]  來套用新的追蹤。

## <a name="view-application-map"></a>檢視應用程式對應

返回 [分散式追蹤] 頁面，並選取 [檢視應用程式對應]  。 檢閱您應用程式及監視設定的視覺效果呈現。 若要了解如何使用應用程式對應，請參閱[這篇文章](https://docs.microsoft.com/azure/azure-monitor/app/app-map) \(部分機器翻譯\)。

## <a name="search"></a>Search

使用搜尋功能來查詢其他特定的遙測項目。 在 [分散式追蹤]  頁面上，選取 [搜尋]  。 如需如何使用搜尋功能的詳細資訊，請參閱[這篇文章](https://docs.microsoft.com/azure/azure-monitor/app/diagnostic-search) \(部分機器翻譯\)。

## <a name="application-insights-page"></a>Application Insights 頁面

除了應用程式對應和搜尋之外，Application Insights 也提供監視功能。 在 Azure 入口網站中搜尋您應用程式的名稱，然後啟動 Application Insights 頁面以尋找更多內容。 如需如何使用這些工具的詳細指引，請[參閱相關文件](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language) \(部分機器翻譯\)。


## <a name="disable-application-insights"></a>停用 Application Insights

1. 在 Azure 入口網站中，移至您的 Azure Spring Cloud 服務頁面。
1. 在 [監視] 區段中，按一下 [分散式追蹤]  。
1. 按一下 [停用]  以停用 Application Insights

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何在 Azure Spring Cloud 中啟用及理解分散式追蹤。 若要了解如何將應用程式繫結至 Azure CosmosDB，請繼續進行下一個教學課程。

> [!div class="nextstepaction"]
> [了解如何將您的應用程式繫結至 Azure CosmosDB](spring-cloud-tutorial-bind-cosmos.md)。
