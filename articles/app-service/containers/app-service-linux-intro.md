---
title: Linux 上的 App Service 簡介 - Azure | Microsoft Docs
description: 了解 Linux 上的 Azure App Service。
keywords: azure app service, linux, oss
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: bc85eff6-bbdf-410a-93dc-0f1222796676
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 1/11/2019
ms.author: msangapu;yili
ms.custom: seodec18
ms.openlocfilehash: 161b2054d690a2e609085852aa7caf2f02366f69
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/01/2019
ms.locfileid: "57215654"
---
# <a name="introduction-to-azure-app-service-on-linux"></a>Linux 上的 Azure App Service 簡介

[App Service 服務](../overview.md)是完全受控的計算平台，非常適合用來裝載網站和 Web Apps。 針對支援的應用程式堆疊，客戶可以使用 Linux 上的 App Service，以原生方式將 Web 應用程式裝載於 Linux。 [Languages](#languages) 區段會列出目前支援的應用程式堆疊。

## <a name="languages"></a>Languages

Linux 上的 App Service 可支援一些內建映像，以增加開發人員的生產力。 如果您的應用程式需要的執行階段在內建映像中不受支援，則有指示可讓您知道如何[建置自有 Docker 映像](tutorial-custom-docker-image.md)以部署至用於容器的 Web 應用程式。

| 語言 | 支援的版本 |
|---|---|
| Node.js | 4.4, 4.5, 4.8, 6.2, 6.6, 6.9, 6.10, 6.11, 8.0, 8.1, 8.2, 8.8, 8.9, 8.11, 9.4, 10.1,10.10 |
| Java * | Tomcat 8.5, 9.0, Java SE, WildFly 14 (全都執行 JRE 8) |
| PHP | 5.6, 7.0, 7.2 |
| Python (預覽) | 2.7, 3.6, 3.7 |
| .NET Core | 1.0, 1.1, 2.0, 2.1 |
| Ruby | 2.3 |

## <a name="deployments"></a>部署

* FTP
* 本機 Git
* GitHub
* Bitbucket

## <a name="devops"></a>DevOps

* 預備環境
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-intro) 和 DockerHub CI/CD

## <a name="console-publishing-and-debugging"></a>主控台、發佈和偵錯

* 環境
* 部署
* 基本主控台
* SSH

## <a name="scaling"></a>調整大小

* 客戶可以變更 [App Service 方案](https://docs.microsoft.com/azure/app-service/overview-hosting-plans?toc=%2fazure%2fapp-service-web%2ftoc.json)中的階層，即可相應增加和減少 Web Apps 的規模

## <a name="locations"></a>位置

查看 [Azure 狀態儀表板](https://azure.microsoft.com/status)。

## <a name="limitations"></a>限制

Azure 入口網站只會顯示 Web App for Containers 目前可用的功能。 隨著我們啟用更多功能，您會在入口網站中看到它們。

Linux 上的 App Service 只在[「基本」、「標準」和「進階」](https://azure.microsoft.com/pricing/details/app-service/plans/)App Service 方案中才支援，而且沒有[「免費」或「共用」](https://azure.microsoft.com/pricing/details/app-service/plans/)層。 在已裝載非 Linux Web Apps 的 App Service 方案中，您無法建立 Web App for Containers。  

此外，根據目前的限制，請勿在相同資源群組中混合使用 Windows 和 Linux 應用程式。

## <a name="troubleshooting"></a>疑難排解

當您的應用程式無法啟動或您想要檢查應用程式的記錄時，請檢查 LogFiles 目錄中的 Docker 記錄。 您可以透過 SCM 網站或 FTP 來存取此目錄。
若要從您的容器記錄 `stdout` 和 `stderr`，您必須啟用 [診斷記錄] 下的 [Docker 容器記錄]。

![啟用記錄][2]

設定會立即生效。 App Service 會偵測設定變更，而且會自動為您重新啟動容器。

您可以在 [開發工具] 功能表中從 [進階工具] 存取 SCM 網站。

![Using Kudu to view Docker logs][1]

## <a name="next-steps"></a>後續步驟

下列文章可協助您開始使用 Linux 上的 App Service，以及以各種語言撰寫的 Web 應用程式：

* [.NET Core](quickstart-dotnetcore.md)
* [PHP](https://docs.microsoft.com/azure/app-service/containers/quickstart-php)
* [Node.js](quickstart-nodejs.md)
* [Java](quickstart-java.md)
* [Python](quickstart-python.md)
* [Ruby](quickstart-ruby.md)
* [Go](quickstart-docker-go.md)
* [多容器應用程式](quickstart-multi-container.md)

如需 Linux 上的 App Service 詳細資訊，請參閱：

* [適用於 Linux 的 App Service 常見問題集](app-service-linux-faq.md)
* [Linux 上 App Service 的 SSH 支援](app-service-linux-ssh-support.md)
* [在 App Service 中設定預備環境](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Docker 中樞連續部署](app-service-linux-ci-cd.md)

您可以在[我們的論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview)張貼問題和疑難。

<!--Image references-->
[1]: ./media/app-service-linux-intro/kudu-docker-logs.png
[2]: ./media/app-service-linux-intro/logging.png
