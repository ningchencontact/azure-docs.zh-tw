---
title: 在 Azure Functions 中的部署技術 |Microsoft Docs
description: 了解不同的方式，您可以將程式碼部署至 Azure Functions 的優缺點。
services: functions
documentationcenter: .net
author: ColbyTresness
manager: dariac
ms.service: azure-functions
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: cotresne
ms.openlocfilehash: 10976c9cf16dfab4c31d0d77c519dc3277204a51
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2019
ms.locfileid: "67293051"
---
# <a name="deployment-technologies-in-azure-functions"></a>在 Azure Functions 中的部署技術

有幾個不同的技術，可用來將您的 Azure Functions 專案程式碼部署至 Azure。 這篇文章提供這些技術的完整清單、 通知的技術可供函式的類別、 說明實際發生的事情時每個方法使用時，並提供最好的方法中使用的建議各種案例。 支援部署至 Azure Functions 的各種工具，會調整成適當的技術，根據其內容。

## <a name="deployment-technology-availability"></a>部署技術的可用性

> [!IMPORTANT]
> Azure Functions 支援跨平台本機開發和裝載兩個作業系統上：Windows 和 Linux。 有三個主控方案目前可用，各有不同的行為-[耗用量](functions-scale.md#consumption-plan)， [Premium](functions-scale.md#premium-plan)，並[專用 (App Service)](functions-scale.md#app-service-plan)。 並非所有的部署技術可供 Azure Functions 的每個類別。

| 部署技術 | Windows 耗用量 | Windows premium （預覽） | 專用的 Windows  | Linux 耗用量 （預覽） | 專用的 Linux |
|-----------------------|:-------------------:|:-------------------------:|:-----------------:|:---------------------------:|:---------------:|
| 外部套件 URL<sup>1</sup> |✔|✔|✔|✔|✔|
| 部署 zip |✔|✔|✔| |✔|
| Docker 容器 | | | | |✔|
| Web Deploy |✔|✔|✔| | |
| 原始檔控制 |✔|✔|✔| |✔|
| 本機 Git<sup>1</sup> |✔|✔|✔| |✔|
| 雲端同步處理<sup>1</sup> |✔|✔|✔| |✔|
| FTP<sup>1</sup> |✔|✔|✔| |✔|
| 入口網站編輯 |✔|✔|✔| |✔<sup>2</sup>|

<sup>1</sup>需要的部署技術[手動觸發程序同步](#trigger-syncing)。
<sup>2</sup>入口網站啟用編輯只有 HTTP 和計時器觸發程序使用 「 專用計劃在 Linux 上的函式。

## <a name="key-concepts"></a>重要概念

在繼續之前，請務必了解一些重要的概念，請務必了解部署 Azure Functions 中的運作方式。

### <a name="trigger-syncing"></a>同步處理觸發程序

當您變更任何程式觸發程序時，函式基礎結構必須注意這些變更。 這項同步處理會自動執行許多部署技術。 不過，在某些情況下您必須手動同步您的觸發程序。 當您部署您使用外部套件 URL、 本機 Git、 雲端同步處理或 FTP 的更新時，您必須手動同步處理您的觸發程序。 您可以同步處理觸發程序在三種方式之一：

* 在 Azure 入口網站中重新啟動您的函式應用程式
* 傳送 HTTP POST 要求來`https://www.{functionappname}.azurewebsites.net/admin/host/synctriggers?code=<API_KEY>`使用[主鍵](functions-bindings-http-webhook.md#authorization-keys)。
* 傳送 HTTP POST 要求到`https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/sites/<FUNCTION_APP_NAME>/syncfunctiontriggers?api-version=2016-08-01`。 您的訂用帳戶識別碼、 資源群組名稱，與您的函式應用程式的名稱來取代預留位置。

## <a name="deployment-technology-details"></a>部署技術詳細資料  

### <a name="external-package-url"></a>外部套件 URL

可讓您參考包含函式應用程式的遠端封裝 (.zip) 檔案。 從提供的 URL，下載檔案，並在中執行的應用程式[執行從套件](run-functions-from-deployment-package.md)模式。

>__如何使用它：__ 新增`WEBSITE_RUN_FROM_PACKAGE`至您的應用程式設定。 此設定的值應該是 URL-您想要執行特定封裝檔案的位置。 您可以新增設定任一[在入口網站](functions-how-to-use-azure-function-app-settings.md#settings)或是[藉由使用 Azure CLI](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set)。 如果使用 Azure blob 儲存體，您應該使用的私用容器[共用存取簽章 (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#attach-a-storage-account-by-using-a-shared-access-signature-sas)以便封裝函式存取。 隨時在應用程式重新啟動它會擷取一份內容，這表示應用程式的存留期內必須是有效參考之用。

>__使用時機：__ 這是在取用方案 （預覽） 在 Linux 上執行的 Azure Functions 的支援僅部署方法。 在更新函式應用程式所參考的封裝檔案時，您必須[手動同步處理觸發程序](#trigger-syncing)告訴 Azure 您的應用程式已變更。

### <a name="zip-deploy"></a>部署 zip

可讓您將包含函式應用程式至 Azure 的 zip 檔案。 （選擇性） 您也可以指定將您的應用程式的開頭[執行從套件](run-functions-from-deployment-package.md)模式。

>__如何使用它：__ 使用您最愛的部署用戶端工具- [VS Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure)， [Visual Studio](functions-develop-vs.md#publish-to-azure)，或有[Azure CLI](functions-create-first-azure-function-azure-cli.md#deploy-the-function-app-project-to-azure)。 若要以手動方式將 zip 檔案部署至函式應用程式中，請遵循指示[部署從 zip 檔案或 url](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url)。
>
>此外，當透過 zip 部署部署，則使用者可以執行其應用程式，從指定[執行從套件](run-functions-from-deployment-package.md)藉由設定模式`WEBSITE_RUN_FROM_PACKAGE`應用程式設定值，做為`1`。 此選項建議，並產生更快速的載入時間，您的應用程式。 這是由上述的用戶端工具的預設值。

>__使用時機：__ 這是執行 Windows，Azure Functions 和 Azure Functions 中的專用的計劃在 Linux 上執行的建議的部署技術。

### <a name="docker-container"></a>Docker 容器

部署 Linux 容器映像，其中包含您的函式應用程式。

>__如何使用它：__ 在專用的方案中，建立 Linux 函數應用程式，並指定從執行的容器映像。 您可以使用兩種方式執行此動作：
>
>* 在 Azure 入口網站中的 App Service 方案上建立 Linux 函數應用程式。 選取  **Docker 映像**for**發佈**，並設定容器，並提供映像所在的位置。
>* 在 App Service 方案中，透過 Azure CLI 建立 Linux 函數應用程式。 了解如何藉由檢閱[在 Linux 上使用自訂映像建立的函式](functions-create-function-linux-custom-image.md#create-and-deploy-the-custom-image)。
>
>若要部署現有的應用程式使用自訂容器，使用[ `func deploy` ](functions-run-local.md#publish)命令[Azure Functions Core Tools](functions-run-local.md)。

>__使用時機：__ 使用此選項時，您需要更充分掌控 Linux 環境函式應用程式執行。 這種部署機制僅適用於在 Linux 上的 App Service 方案中執行的函式。

### <a name="web-deploy-msdeploy"></a>Web 部署 (MSDeploy)

封裝和部署您的 Windows 應用程式至任何 IIS 伺服器，包括您在 Windows 上執行的 Azure 函式應用程式。

>__如何使用它：__ 使用[適用於 Azure Functions 的 Visual Studio tools](functions-create-your-first-function-visual-studio.md)，而不刻度`Run from package file (recommended)`核取方塊。
>
>或者，呼叫`MSDeploy.exe`下載之後，直接[Web 部署 3.6](https://www.iis.net/downloads/microsoft/web-deploy)。

>__使用時機：__ 這項部署技術支援，而且不有任何問題，但是慣用的機制現在[Zip Deploy 執行從套件啟用](#zip-deploy)。 若要進一步了解，請造訪[Visual Studio 開發指南](functions-develop-vs.md#publish-to-azure)。

### <a name="source-control"></a>原始檔控制

可讓您連接到 git 存放庫的函式應用程式，使該存放庫中的程式碼的任何更新會觸發部署。 如需詳細資訊，看看[Kudu Wiki](https://github.com/projectkudu/kudu/wiki/VSTS-vs-Kudu-deployments)。

>__如何使用它：__ 您可以使用部署中心，在 Azure Functions 入口網站來設定從原始檔控制發行。 如需詳細資訊，請參閱 [Azure Functions 的持續部署](functions-continuous-deployment.md)。

>__使用時機：__ 使用原始檔控制會在他們的函式應用程式上共同作業小組的最佳做法，這是絕佳的選項，可讓更複雜的部署管線。

### <a name="local-git"></a>本機 git

可讓您將程式碼從本機電腦推送至 Azure Functions 使用 Git。

>__如何使用它：__ 請遵循指示[本機 Git 部署至 Azure App Service](../app-service/deploy-local-git.md)。

>__使用時機：__ 一般情況下，建議使用其他部署方法。 發行時從本機 git，您必須[手動同步處理觸發程序](#trigger-syncing)。

### <a name="cloud-sync"></a>雲端同步

可讓您同步處理您的內容從 Dropbox 與 OneDrive Azure Functions。

>__如何使用它：__ 請依照下列中的指示[從雲端資料夾同步內容](../app-service/deploy-content-sync.md)。

>__使用時機：__ 一般情況下，建議使用其他部署方法。 當發佈與雲端同步處理時，您必須[手動同步處理觸發程序](#trigger-syncing)。

### <a name="ftp"></a>FTP

可讓您直接傳送檔案至 Azure Functions。

>__如何使用它：__ 請依照下列中的指示[部署內容使用 FTP/s](../app-service/deploy-ftp.md)。

>__使用時機：__ 一般情況下，建議使用其他部署方法。 發行時透過 FTP，您必須[手動同步處理觸發程序](#trigger-syncing)。

### <a name="portal-editing"></a>入口網站編輯

使用入口網站為基礎的編輯器可讓您直接編輯函式應用程式上的檔案 (基本上每當您按一下在部署**儲存**)。

>__如何使用它：__ 若要能夠編輯您的函式，在 Azure 入口網站中，您必須擁有[入口網站中建立您的函式](functions-create-first-azure-function.md)。 使用任何其他部署方法可讓您的函式只讀取，並防止持續入口網站編輯，以保留單一真實來源。 若要回到的狀態，您可以在其中編輯您在 Azure 入口網站的檔案，您可以手動開啟編輯模式回到`Read/Write`，並移除任何與部署相關的應用程式設定 (例如`WEBSITE_RUN_FROM_PACKAGE`)。 

>__使用時機：__ 入口網站開始使用 Azure Functions 的好方法，但建議任何更密集的開發工作，使用用戶端工具：
>
>* [開始使用 VS Code](functions-create-first-function-vs-code.md)
>* [開始使用 Azure Functions Core Tools](functions-run-local.md)
>* [開始使用 Visual Studio](functions-create-your-first-function-visual-studio.md)

下表顯示的作業系統和語言的支援入口網站編輯：

| | Windows 耗用量 | Windows Premium （預覽） | 專用的 Windows | Linux 耗用量 （預覽） | 專用的 Linux |
|-|:-----------------: |:-------------------------:|:-----------------:|:---------------------------:|:---------------:|
| C# | | | | | |
| C# 指令碼 |✔|✔|✔| |✔<sup>*</sup>|
| F# | | | | | |
| Java | | | | | |
| JavaScript (Node.js) |✔|✔|✔| |✔<sup>*</sup>|
| Python (預覽) | | | | | |
| PowerShell （預覽） |✔|✔|✔| | |
| TypeScript (Node.js) | | | | | |

<sup>*</sup> 入口網站啟用編輯只有 HTTP 和計時器觸發程序使用 「 專用計劃在 Linux 上的函式。

## <a name="deployment-slots"></a>部署位置

當您將函式應用程式部署至 Azure 時，您可以部署到不同的部署位置，而不是直接到生產環境。 如需有關部署位置的詳細資訊，請參閱 <<c0> [ 的 Azure 應用程式服務位置的文件](../app-service/deploy-staging-slots.md)。

### <a name="deployment-slots-levels-of-support"></a>部署位置支援層的級

有兩個層級的支援：

* _正式推出 (GA)_ - 完整支援且已核准用於生產環境。
* _預覽_ - 尚未支援，但預期未來會正式推出。

| OS/主控方案 | 層級的支援 |
| --------------- | ------ |
| Windows 耗用量 | 預覽 |
| Windows Premium （預覽） | 預覽 |
| 專用的 Windows | 正式推出 |
| Linux 耗用量 | 不支援 |
| 專用的 Linux | 正式推出 |

## <a name="next-steps"></a>後續步驟

深入了解部署您的函式應用程式，在下列文章： 

+ [Azure Functions 的持續部署](functions-continuous-deployment.md)
+ [使用 Azure DevOps 的持續傳遞](functions-how-to-azure-devops.md)
+ [適用於 Azure Functions 的 zip 部署](deployment-zip-push.md)
+ [執行您的 Azure Functions 從套件檔案](run-functions-from-deployment-package.md)
+ [在 Azure Functions 中函數應用程式的自動化資源部署](functions-infrastructure-as-code.md)
