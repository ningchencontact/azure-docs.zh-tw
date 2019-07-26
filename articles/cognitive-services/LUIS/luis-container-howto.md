---
title: Docker 容器
titleSuffix: Language Understanding - Azure Cognitive Services
description: LUIS 容器會將您已定型或發佈的應用程式載入 Docker 容器中，並提供從容器的 API 端點存取查詢預測的權限。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: dapine
ms.openlocfilehash: ae2f24c83cb0de054cc97bf0be8ada35a568ad82
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360559"
---
# <a name="install-and-run-luis-docker-containers"></a>安裝和執行 LUIS Docker 容器
 
Language Understanding (LUIS) 容器會將您已定型或發佈的 Language Understanding 模型 (也稱為 [LUIS 應用程式](https://www.luis.ai)) 載入 Docker 容器中，並提供從容器的 API 端點存取查詢預測的權限。 您可以從容器收集查詢記錄, 並將其上傳回 Language Understanding 應用程式, 以改善應用程式的預測精確度。

以下影片將示範如何使用此容器。

[![認知服務的容器示範](./media/luis-container-how-to/luis-containers-demo-video-still.png)](https://aka.ms/luis-container-demo)

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>先決條件

若要執行 LUIS 容器，您必須具備下列項目： 

|必要項|用途|
|--|--|
|Docker 引擎| 您必須在[主機電腦](#the-host-computer)上安裝 Docker 引擎。 Docker 提供可在 [macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 和 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上設定 Docker 環境的套件。 如需 Docker 和容器基本概念的入門，請參閱 [Docker 概觀](https://docs.docker.com/engine/docker-overview/) \(英文\)。<br><br> Docker 必須設定為允許容器與 Azure 連線，以及傳送帳單資料至 Azure。 <br><br> **在 Windows 上**，也必須將 Docker 設定為支援 Linux 容器。<br><br>|
|熟悉 Docker | 您應具備對 Docker 概念 (例如登錄、存放庫、容器和容器映像等) 的基本了解，以及基本 `docker` 命令的知識。| 
|Azure `Cognitive Services`資源和 LUIS 已[封裝的應用程式](luis-how-to-start-new-app.md#export-app-for-containers)檔 |若要使用此容器，您必須具備：<br><br>* A_認知服務_Azure 資源和相關聯的帳單金鑰計費端點 URI。 這兩個值都可在資源的 [總覽] 和 [金鑰] 頁面上取得, 而且必須要有才能啟動容器。 您需要將`luis/v2.0`路由新增至端點 URI, 如下列 BILLING_ENDPOINT_URI 範例所示。 <br>* 已定型或發佈的應用程式，並封裝為掛接形式連同相關聯的應用程式識別碼輸入容器中。 您可以從 LUIS 入口網站或撰寫 Api 取得封裝的檔案。 如果您要從[撰寫 api](#authoring-apis-for-package-file)取得 LUIS 的已封裝應用程式, 則也需要您的_撰寫金鑰_。<br><br>這些需求可用來將命令列引數傳至下列變數：<br><br>**{AUTHORING_KEY}** ：此金鑰可用來從雲端中的 LUIS 服務取得已封裝的應用程式，並將查詢記錄重新上傳至雲端。 格式為 `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`。<br><br>**{APPLICATION_ID}** ：此識別碼可用來選取應用程式。 格式為 `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`。<br><br>**{API_KEY}** :此金鑰可用來啟動容器。 您可以在兩個位置找到端點金鑰。 第一個是_認知服務_資源的 [金鑰] 清單中的 Azure 入口網站。 端點金鑰也可以在 LUIS 入口網站中的 [金鑰和端點] 設定頁面上取得。 請勿使用入門金鑰。<br><br>**{ENDPOINT_URI}** :[總覽] 頁面上所提供的端點。<br><br>[撰寫金鑰和端點金鑰](luis-boundaries.md#key-limits)有不同的用途。 請勿將其交替使用。 |

### <a name="authoring-apis-for-package-file"></a>撰寫套件檔案的 Api

撰寫適用于已封裝應用程式的 Api:

* [已發佈的封裝 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagepublishedapplicationasgzip)
* [未發佈, 僅限定型的套件 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagetrainedapplicationasgzip)

### <a name="the-host-computer"></a>主機電腦

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>容器的需求和建議

此容器支援設定的最小值和建議值：

|容器| 最小值 | 建議 | TPS<br>(最小值, 最大值)|
|-----------|---------|-------------|--|
|LUIS|1核心, 2 GB 記憶體|1核心, 4 GB 記憶體|20、40|

* 每個核心必須至少 2.6 GHz 或更快。
* TPS - 每秒的交易數

核心和記憶體會對應至 `--cpus` 和 `--memory` 設定，用來作為 `docker run` 命令的一部分。

## <a name="get-the-container-image-with-docker-pull"></a>使用 `docker pull` 取得容器映像

使用 [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) 命令從 `mcr.microsoft.com/azure-cognitive-services/luis` 存放庫下載容器映像：

```
docker pull mcr.microsoft.com/azure-cognitive-services/luis:latest
```

使用 [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) 命令下載容器映像。

如需可用標記 (例如在前述命令中使用的 `latest`) 的完整說明，請參閱 Docker Hub 上的 [LUIS](https://go.microsoft.com/fwlink/?linkid=2043204)。

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]


## <a name="how-to-use-the-container"></a>如何使用容器

容器位於[主機電腦](#the-host-computer)上時，請透過下列程序來使用容器。

![使用 Language Understanding (LUIS) 容器的程序](./media/luis-container-how-to/luis-flow-with-containers-diagram.jpg)

1. 從 LUIS 入口網站或 LUIS API 為容器[匯出套件](#export-packaged-app-from-luis)。
1. 將套件檔案移至[主機電腦](#the-host-computer)上所需的**輸入**目錄中。 請勿重新命名、改變、覆寫或解壓縮 LUIS 套件檔案。
1. 使用所需的_輸入掛接_和計費設定[執行容器](##run-the-container-with-docker-run)。 `docker run` 命令有相關[範例](luis-container-configuration.md#example-docker-run-commands)可供參考。 
1. [查詢容器的預測端點](#query-the-containers-prediction-endpoint)。 
1. 容器使用完畢後，請從 LUIS 入口網站中的輸出掛接[匯入端點記錄](#import-the-endpoint-logs-for-active-learning)，並[停止](#stop-the-container)容器。
1. 從 LUIS 入口網站使用 [檢閱端點語句] 頁面上的[主動式學習](luis-how-to-review-endpoint-utterances.md)，來改善應用程式。

在容器中執行的應用程式無法變更。 若要變更容器中的應用程式，您必須使用 [LUIS](https://www.luis.ai) 入口網站或 LUIS [撰寫 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) 變更 LUIS 服務中的應用程式。 接著，請進行定型和 (或) 發佈，然後下載新套件並重新執行容器。

容器內的 LUIS 應用程式無法重新匯出至 LUIS 服務。 只有查詢記錄可供上傳。 

## <a name="export-packaged-app-from-luis"></a>從 LUIS 匯出已封裝的應用程式

LUIS 容器需要以已定型或發佈的 LUIS 應用程式來回應使用者語句的預測查詢。 若要取得 LUIS 應用程式，請使用已定型或發佈的套件 API。 

預設位置是相對於執行 `docker run` 命令所在之處的 `input` 子目錄。  

將套件檔案放入目錄中，並在執行 Docker 容器時以輸入掛接的形式參考此目錄。 

### <a name="package-types"></a>套件類型

輸入掛接目錄可同時包含**生產**、**預備**和**定型**版本的應用程式。 所有套件都會掛接。 

|套件類型|查詢端點 API|查詢可用性|套件檔案名稱格式|
|--|--|--|--|
|已定型|Get, Post|僅限容器|`{APPLICATION_ID}_v{APPLICATION_VERSION}.gz`|
|預備環境|Get, Post|Azure 和容器|`{APPLICATION_ID}_STAGING.gz`|
|生產|Get, Post|Azure 和容器|`{APPLICATION_ID}_PRODUCTION.gz`|

> [!IMPORTANT]
> 請勿重新命名、改變、覆寫或解壓縮 LUIS 套件檔案。

### <a name="packaging-prerequisites"></a>封裝必要條件

在封裝 LUIS 應用程式之前，您必須符合下列需求：

|封裝需求|詳細資料|
|--|--|
|Azure_認知服務_資源實例|支援的區域包括<br><br>美國西部 (```westus```)<br>西歐 (```westeurope```)<br>澳洲東部 (```australiaeast```)|
|已定型或發佈的 LUIS 應用程式|不含[不支援的相依性](#unsupported-dependencies)。 |
|可存取[主機電腦](#the-host-computer)的檔案系統 |主機電腦必須允許[輸入掛接](luis-container-configuration.md#mount-settings)。|
  
### <a name="export-app-package-from-luis-portal"></a>從 LUIS 入口網站匯出應用程式套件

LUIS [入口網站](https://www.luis.ai)可讓您匯出已定型或發佈的應用程式套件。 

### <a name="export-published-apps-package-from-luis-portal"></a>從 LUIS 入口網站匯出已發佈的應用程式套件

已發佈的應用程式套件可從 [我的應用程式] 清單頁面取得。 

1. 登入 LUIS [入口網站](https://www.luis.ai)。
1. 選取清單中位於應用程式名稱左側的核取方塊。 
1. 從清單上方的內容相關工具列中選取 [匯出] 項目。
1. 選取 [匯出容器 (GZIP)]。
1. 選取 [生產位置] 或 [預備位置] 的環境。
1. 從瀏覽器下載套件。

![從 [應用程式] 頁面的 [匯出] 功能表為容器匯出已發佈的套件](./media/luis-container-how-to/export-published-package-for-container.png)

### <a name="export-trained-apps-package-from-luis-portal"></a>從 LUIS 入口網站匯出已定型的應用程式套件

已定型的應用程式套件可從 [版本] 清單頁面取得。 

1. 登入 LUIS [入口網站](https://www.luis.ai)。
1. 選取清單中的應用程式。 
1. 在應用程式的導覽列中選取 [管理]。
1. 在左側導覽列中選取 [版本]。
1. 選取清單中位於版本名稱左側的核取方塊。
1. 從清單上方的內容相關工具列中選取 [匯出] 項目。
1. 選取 [匯出容器 (GZIP)]。
1. 從瀏覽器下載套件。

![從 [版本] 頁面的 [匯出] 功能表為容器匯出已定型的套件](./media/luis-container-how-to/export-trained-package-for-container.png)


### <a name="export-published-apps-package-from-api"></a>從 API 匯出已發佈的應用程式套件

使用下列 REST API 方法，封裝您已[發佈](luis-how-to-publish-app.md)的 LUIS 應用程式。 使用 HTTP 規格下方的表格，以您自己的適當值取代 API 呼叫中的預留位置。

```http
GET /luis/api/v2.0/package/{APPLICATION_ID}/slot/{APPLICATION_ENVIRONMENT}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| 預留位置 | 值 |
|-------------|-------|
|{APPLICATION_ID} | 已發佈 LUIS 應用程式的應用程式識別碼。 |
|{APPLICATION_ENVIRONMENT} | 已發佈 LUIS 應用程式的環境。 請使用下列其中一個值：<br/>```PRODUCTION```<br/>```STAGING``` |
|{AUTHORING_KEY} | 已發佈 LUIS 應用程式的 LUIS 帳戶所適用的撰寫金鑰。<br/>您可以從 LUIS 入口網站中的 [使用者設定] 頁面取得撰寫金鑰。 |
|{AZURE_REGION} | 適當的 Azure 區域：<br/><br/>```westus``` - 美國西部<br/>```westeurope``` - 西歐<br/>```australiaeast``` - 澳洲東部 |

若要下載已發行的封裝, 請參閱[這裡的 API 檔][download-published-package]。 如果成功下載, 回應會是 LUIS 套件檔案。 請將此檔案儲存在為容器的輸入掛接指定的儲存位置中。 

### <a name="export-trained-apps-package-from-api"></a>從 API 匯出已定型的應用程式套件

使用下列 REST API 方法，封裝您已[定型](luis-how-to-train.md)的 LUIS 應用程式。 使用 HTTP 規格下方的表格，以您自己的適當值取代 API 呼叫中的預留位置。

```http
GET /luis/api/v2.0/package/{APPLICATION_ID}/versions/{APPLICATION_VERSION}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| 預留位置 | 值 |
|-------------|-------|
|{APPLICATION_ID} | 已定型 LUIS 應用程式的應用程式識別碼。 |
|{APPLICATION_VERSION} | 已定型 LUIS 應用程式的應用程式版本。 |
|{AUTHORING_KEY} | 已發佈 LUIS 應用程式的 LUIS 帳戶所適用的撰寫金鑰。<br/>您可以從 LUIS 入口網站中的 [使用者設定] 頁面取得撰寫金鑰。  |
|{AZURE_REGION} | 適當的 Azure 區域：<br/><br/>```westus``` - 美國西部<br/>```westeurope``` - 西歐<br/>```australiaeast``` - 澳洲東部 |

若要下載已定型的套件, 請參閱[這裡的 API 檔][download-trained-package]。 如果成功下載, 回應會是 LUIS 套件檔案。 請將此檔案儲存在為容器的輸入掛接指定的儲存位置中。 

## <a name="run-the-container-with-docker-run"></a>透過 `docker run` 執行容器

將 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令執行容器。 此命令會使用下列參數：

| 預留位置 | 值 |
|-------------|-------|
|{API_KEY} | 此金鑰可用來啟動容器。 請勿使用入門金鑰。 |
|{ENDPOINT_URI} | 端點值可在 Azure 入口網站的`Cognitive Services` [總覽] 頁面上取得。 |

請以您自己的值取代下列範例 `docker run` 命令中的參數。 在 Windows 主控台中執行命令。

```console
docker run --rm -it -p 5000:5000 ^
--memory 4g ^
--cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output\,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis ^
Eula=accept ^
Billing={ENDPOINT_URI} ^
ApiKey={API_KEY}
```

* 這個範例會使用`C:`磁片磁碟機上的目錄, 以避免在 Windows 上發生任何許可權衝突。 如果您需要使用特定目錄作為輸入目錄，您可能需要授與 Docker 服務權限。 
* 除非您熟悉 docker 容器, 否則請勿變更引數的順序。
* 如果您使用不同的作業系統, 請使用正確的主控台/終端機、裝載的資料夾語法, 以及系統的行接續字元。 這些範例假設 Windows 主控台包含行接續字元`^`。 因為容器是 Linux 作業系統, 所以目標掛接會使用 Linux 樣式的資料夾語法。

此命令：

* 從 LUIS 容器映像執行容器
* 從 c:\input 上的輸入掛接 (位於容器主機上) 載入 LUIS 應用程式
* 配置兩個 CPU 核心和 4 GB 的記憶體
* 公開 TCP 連接埠 5000，並為容器配置虛擬 TTY
* 將容器和 LUIS 記錄儲存到 c:\output 上的輸出掛接 (位於容器主機上)
* 在容器結束之後自動將其移除。 容器映像仍可在主機電腦上使用。 

`docker run` 命令有相關[範例](luis-container-configuration.md#example-docker-run-commands)可供參考。 

> [!IMPORTANT]
> 必須指定 `Eula`、`Billing` 及 `ApiKey` 選項以執行容器，否則容器將不會啟動。  如需詳細資訊，請參閱[帳單](#billing)。
> ApiKey 值是 LUIS 入口網站中 [金鑰] 和 [端點] 頁面的**金鑰**, 也可以在 [Azure `Cognitive Services`資源金鑰] 頁面上取得。  

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="endpoint-apis-supported-by-the-container"></a>容器支援的端點 Api

容器的 V2 和[V3 (預覽)](luis-migration-api-v3.md)版本都可供使用。 

## <a name="query-the-containers-prediction-endpoint"></a>查詢容器的預測端點

容器會提供以 REST 為基礎的查詢預測端點 API。 已發佈 (預備或生產) 應用程式的端點具有與已定型應用程式的端點_不同_的路由。 

針對容器 API 請使用主機 `https://localhost:5000`。 

|封裝類型|方法|路由|查詢參數|
|--|--|--|--|
|已發行|[Get](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78), [Post](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)|/luis/v2.0/apps/{appId}?|q={q}<br>&staging<br>[&timezoneOffset]<br>[&verbose]<br>[&log]<br>|
|已定型|Get, Post|/luis/v2.0/apps/{appId}/versions/{versionId}?|q={q}<br>[&timezoneOffset]<br>[&verbose]<br>[&log]|

查詢參數會設定傳回查詢回應的方式和內容：

|查詢參數|Type|用途|
|--|--|--|
|`q`|string|使用者的語句。|
|`timezoneOffset`|號|TimezoneOffset 可讓您[變更時區](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) (預先建置的實體 datetimeV2 所使用的時區)。|
|`verbose`|boolean|設為 true 時，會傳回所有意圖及其分數。 預設值為 false，只會傳回最高分意圖。|
|`staging`|boolean|設為 true 時，會從預備環境的結果中傳回查詢。 |
|`log`|boolean|記錄查詢，可供後續的[主動式學習](luis-how-to-review-endpoint-utterances.md)使用。 預設值為 true。|

### <a name="query-published-app"></a>查詢已發佈的應用程式

對容器查詢已發佈應用程式的範例 CURL 命令為：

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APPLICATION_ID}?q=turn%20on%20the%20lights&staging=false&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
若要對**預備**環境進行查詢，請將 **staging** 查詢字串參數值變更為 true： 

`staging=true`

### <a name="query-trained-app"></a>查詢已定型的應用程式

對容器查詢已定型應用程式的範例 CURL 命令為： 

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APPLICATION_ID}/versions/{APPLICATION_VERSION}?q=turn%20on%20the%20lights&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
版本名稱的長度上限為 10 個字元，且只能包含 URL 中允許的字元。 

## <a name="import-the-endpoint-logs-for-active-learning"></a>匯入用於主動式學習的端點記錄

如果已指定 LUIS 容器的輸出掛接，則應用程式查詢記錄檔會儲存在 {INSTANCE_ID} 為容器識別碼的輸出目錄中。 應用程式查詢記錄中包含已提交至 LUIS 容器的每個預測查詢的查詢、回應和時間戳記。 

下列位置會顯示容器記錄檔的巢狀目錄結構。
```
/output/luis/{INSTANCE_ID}/
```
 
在 LUIS 入口網站中選取您的應用程式，然後選取 [匯入端點記錄] 以上傳這些記錄。 

![匯入容器的記錄檔供主動式學習使用](./media/luis-container-how-to/upload-endpoint-log-files.png)

在上傳記錄後，在 LUIS 入口網站中[檢閱端點](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-review-endpoint-utterances)語句。

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>停止容器

若要關閉容器，請在正在執行容器的命令列環境中按 **Ctrl+C**。

## <a name="troubleshooting"></a>疑難排解

如果您在啟用輸出[掛接](luis-container-configuration.md#mount-settings)和記錄的情況下執行容器，容器將會產生記錄檔，有助於排解在啟動或執行容器時所發生的問題。 

## <a name="billing"></a>帳務

LUIS 容器會使用您 Azure 帳戶上的_認知服務_資源, 將帳單資訊傳送至 azure。 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

如需這些選項的詳細資訊，請參閱[設定容器](luis-container-configuration.md)。

## <a name="supported-dependencies-for-latest-container"></a>支援的`latest`容器相依性

最新的容器 (于 2019 build 發行) 將支援:

* Bing 拼寫檢查: 使用`&spellCheck=true&bing-spell-check-subscription-key={bingKey}`查詢字串參數要求查詢預測端點。 若要深入瞭解, 請使用[Bing 拼寫檢查 v7 教學](luis-tutorial-bing-spellcheck.md)課程。 如果使用這項功能, 容器會將語句傳送至您的 Bing 拼寫檢查 V7 資源。
* [新的預建網域](luis-reference-prebuilt-domains.md): 這些企業導向的網域包括實體、範例語句和模式。 擴充這些網域以供您自己使用。 

<a name="unsupported-dependencies"></a>

## <a name="unsupported-dependencies-for-latest-container"></a>不支援的`latest`容器相依性

如果您的 LUIS 應用程式具有不支援的相依性, 您將無法[匯出容器](#export-packaged-app-from-luis), 除非您移除不支援的功能。 當您嘗試匯出容器時, LUIS 入口網站會報告您需要移除的不支援功能。

您可以使用**不含**下列任何相依性的 LUIS 應用程式：

不支援的應用程式組態|詳細資料|
|--|--|
|不支援的容器文化特性| 荷蘭文 (nl-NL)<br>日文 (ja-JP)<br>只有[1.0.2 tokenizer](luis-language-support.md#custom-tokenizer-versions)支援德文。|
|所有文化特性皆不支援的實體|適用於所有文化特性的 [KeyPhrase](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-prebuilt-keyphrase) 預先建置實體|
|英文 (en-US) 文化特性不支援的實體|[GeographyV2](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-prebuilt-geographyv2) 預先建置的實體|
|語音預備|在容器中不支援外部相依性。|
|情感分析|在容器中不支援外部相依性。|

<!--blogs/samples/video courses -->
[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>總結

在本文中，您已了解下載、安裝及執行 Language Understanding (LUIS) 容器的概念和工作流程。 摘要說明：

* Language Understanding (LUIS) 提供了一個適用於 Docker 的 Linux 容器，具備端點語句查詢預測的功能。
* 容器映像可從 Microsoft Container Registry (MCR) 下載取得。
* 容器映像是在 Docker 中執行。
* 您可以使用 REST API，藉由指定容器的主機 URI 來查詢容器端點。
* 將容器具現化時，您必須指定帳單資訊。

> [!IMPORTANT]
> 認知服務容器在未連線至 Azure 以進行計量的情況下，將無法被授權以執行。 客戶必須啟用容器以持續與計量服務進行帳單資訊的通訊。 認知服務容器不會將客戶資料 (例如正在分析的影像或文字) 傳送至 Microsoft。

## <a name="next-steps"></a>後續步驟

* 檢閱[設定容器](luis-container-configuration.md)以了解組態設定
* 請參閱[疑難排解](troubleshooting.md)來解決與 LUIS 功能相關的問題。
* 使用更多[認知服務容器](../cognitive-services-container-support.md)

<!-- Links - external -->
[download-published-package]: https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagepublishedapplicationasgzip
[download-trained-package]: https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagetrainedapplicationasgzip