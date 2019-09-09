---
title: 部署最佳作法-Azure App Service |Microsoft Docs
description: 瞭解部署至 Azure App Service 的主要元件。
keywords: azure app service，web 應用程式，部署，部署，管線，組建
services: app-service
documentationcenter: ''
author: jasonfreeberg
manager: ''
editor: ''
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/31/2019
ms.author: jafreebe
ms.custom: ''
ms.openlocfilehash: d1b6444b8512b1b55ac46370e805f8f662f5f555
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70070688"
---
# <a name="deployment-best-practices"></a>部署最佳作法

每個開發小組都有獨特的需求，可讓您在任何雲端服務上執行有效率的部署管線。 本文介紹部署至 App Service 的三個主要元件：部署來源、組建管線和部署機制。 本文也涵蓋特定語言堆疊的一些最佳作法和秘訣。

## <a name="deployment-components"></a>部署元件

### <a name="deployment-source"></a>部署來源

部署來源是應用程式代碼的位置。 針對生產環境應用程式，部署來源通常是由版本控制軟體（例如[GitHub、BitBucket 或 Azure Repos）](deploy-continuous-deployment.md)所裝載的存放庫。 針對開發和測試案例，部署來源可能是[您本機電腦上的專案](deploy-local-git.md)。 App Service 也支援[OneDrive 和 Dropbox 資料夾](deploy-content-sync.md)作為部署來源。 雖然雲端資料夾可以讓您輕鬆地開始使用 App Service，但通常不建議將此來源用於企業層級的生產應用程式。 

### <a name="build-pipeline"></a>建置管線

一旦您決定部署來源，下一步就是選擇組建管線。 組建管線會從部署來源讀取您的原始程式碼，並執行一系列的步驟（例如編譯器代碼、縮小 HTML 和 JavaScript、執行測試和封裝元件），以讓應用程式處於可執行檔狀態。 組建管線所執行的特定命令取決於您的語言堆疊。 這些作業可以在 Azure Pipelines 的組建伺服器上執行，或在本機執行。

### <a name="deployment-mechanism"></a>部署機制

部署機制是用來將您的已建立應用程式放入 web 應用程式之 */home/site/wwwroot*目錄的動作。 */Wwwroot*目錄是由您 web 應用程式的所有實例所共用的掛接儲存位置。 當部署機制將您的應用程式放在此目錄中時，您的實例會收到同步處理新檔案的通知。 App Service 支援下列部署機制：

- Kudu 端點：[Kudu](https://github.com/projectkudu/kudu/wiki)是開放原始碼開發人員生產力工具，可在 Windows App Service 中以個別程式的形式執行，並作為 Linux App Service 中的第二個容器。 Kudu 會處理持續部署，並提供部署的 HTTP 端點（例如 zipdeploy）。
- FTP 和 WebDeploy：使用您的[網站或使用者認證](deploy-configure-credentials.md)，您可以透過[FTP](deploy-ftp.md)或 WebDeploy 上傳檔案。 這些機制不會經過 Kudu。  

部署工具（例如 Azure Pipelines、Jenkins 和編輯器外掛程式）會使用其中一種部署機制。

## <a name="language-specific-considerations"></a>語言特定考慮

### <a name="java"></a>Java

使用 Kudu [zipdeploy/](deploy-zip.md) API 來部署 JAR 應用程式，以及[WARDEPLOY/](deploy-zip.md#deploy-war-file)用於 WAR 應用程式。 如果您使用 Jenkins，您可以直接在部署階段中使用這些 Api。 如需詳細資訊，請參閱 [本篇文章](../jenkins/execute-cli-jenkins-pipeline.md)。

### <a name="node"></a>節點

根據預設，Kudu 會執行節點應用程式（`npm install`）的組建步驟。 如果您使用 Azure DevOps 之類的組建服務，則不需要 Kudu 組建。 若要停用 Kudu 組建，請建立應用程式`SCM_DO_BUILD_DURING_DEPLOYMENT`設定，其`false`值為。

### <a name="net"></a>.NET 

根據預設，Kudu 會執行 .Net 應用程式（`dotnet build`）的組建步驟。 如果您使用 Azure DevOps 之類的組建服務，則不需要 Kudu 組建。 若要停用 Kudu 組建，請建立應用程式`SCM_DO_BUILD_DURING_DEPLOYMENT`設定，其`false`值為。

## <a name="other-deployment-considerations"></a>其他部署考慮

### <a name="use-deployment-slots"></a>使用部署位置

請盡可能在部署新的生產組建時使用[部署](deploy-staging-slots.md)位置。 當使用標準 App Service 方案層或更佳時，您可以將應用程式部署至預備環境、驗證您的變更，以及執行冒煙測試。 當您準備好時，您可以交換預備和生產位置。 交換作業會會備妥必要的背景工作實例，以符合您的生產環境規模，因而排除停機時間。 

### <a name="local-cache"></a>本機快取

Azure App Service 的內容儲存在 Azure 儲存體中，並且會持久顯示為內容共用。 不過，有些應用程式只需要高效能、唯讀的內容存放區，就能以高可用性來執行。 這些應用程式可以因使用[本機](overview-local-cache.md)快取而獲益。 不建議將本機快取用於內容管理網站，例如 WordPress。

請一律使用本機快取搭配 [部署位置] （部署-臨時區域 md）來避免停機。 如需搭配使用這些功能的相關資訊，請[參閱本節。](overview-local-cache.md#best-practices-for-using-app-service-local-cache)

### <a name="high-cpu-or-memory"></a>高 CPU 或記憶體

如果您的 App Service 計畫使用超過 90% 的可用 CPU 或記憶體，則基礎虛擬機器在處理您的部署時可能會遇到問題。 發生這種情況時，請暫時相應增加您的實例計數，以執行部署。 部署完成之後，您可以將實例計數傳回先前的值。
