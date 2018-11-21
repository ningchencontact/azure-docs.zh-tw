---
title: 安裝及執行容器
titleSuffix: Text Analytics - Cognitive Services - Azure
description: 本逐步解說教學課程的內容包含如何下載、安裝及執行適用於文字分析的容器。
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 11/14/2018
ms.author: diberry
ms.openlocfilehash: 99bdb42d9a0d86d0d2acc4a6272e0c802042e6b5
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2018
ms.locfileid: "51635079"
---
# <a name="install-and-run-containers"></a>安裝及執行容器

容器化是散發軟體的方法，它將應用程式或服務封裝成容器映像。 應用程式或服務的設定和相依性，都包含在容器映像中。 接著可以將容器映像部署在容器主機上，且只需要小幅修改或不修改。 容器之間彼此隔離，也與基礎作業系統隔離，磁碟使用量比虛擬機器更小。 容器可以從容器將映像具現化以進行短期工作，並於不再需要時移除。

文字分析提供下列 Docker 容器，每個都包含一個功能子集：

| 容器| 說明 |
|----------|-------------|
|關鍵片語擷取 | 擷取關鍵片語來識別重點。 例如，若輸入文字為 "The food was delicious and there were wonderful staff"，API 即會傳回主要討論要點："food" 和 "wonderful staff"。 |
|語言偵測 | 適用於超過 120 種語言，可偵測並回報輸入文字以何種語言撰寫。 容器會針對要求中包含的每一個文件回報單一語言代碼。 語言代碼各配有一個分數，表示分數的強度。 |
|情感分析 | 分析原始文字以尋找正面或負面情感的線索。 此 API 會為每份文件傳回 0 到 1 之間的情感分數，1 代表最正面的情感。 分析模型是使用大量文字主體和 Microsoft 的自然語言技術預先定型的。 針對[選取的語言](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages.md)，API 可對您所提供的任何原始文字進行分析及評分，並直接將結果傳回至呼叫端應用程式。 |

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="preparation"></a>準備工作

使用文字分析容器之前，您必須符合下列必要條件：

**Docker 引擎**：您必須在本機安裝 Docker 引擎。 Docker 提供可在 [macOS](https://docs.docker.com/docker-for-mac/) \(英文\)、[Linux](https://docs.docker.com/engine/installation/#supported-platforms) \(英文\) 和 [Windows](https://docs.docker.com/docker-for-windows/) \(英文\) 上設定 Docker 環境的套件。 在 Windows 上，必須將 Docker 設定為支援 Linux 容器。 您也可以將 Docker 容器直接部署至 [Azure Kubernetes Service](/azure/aks/)、[Azure 容器執行個體](/azure/container-instances/)，或至部署至 [Azure Stack](/azure/azure-stack/) 的 [Kubernetes](https://kubernetes.io/) 叢集。 如需將 Kubernetes 部署至 Azure Stack 的詳細資訊，請參閱[將 Kubernetes 部署至 Azure Stack](/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)。

Docker 必須設定為允許容器與 Azure 連線，以及傳送計費資料至 Azure。

**對 Microsoft Container Registry 和 Docker 的熟悉度**：您對登錄、存放庫、容器和容器映像等 Microsoft Container Registry 和 Docker 概念，以及基本 `docker` 命令的知識應有基本的了解。  

如需 Docker 和容器基本概念的入門，請參閱 [Docker 概觀](https://docs.docker.com/engine/docker-overview/)。

### <a name="server-requirements-and-recommendations"></a>伺服器需求和建議

下表說明針對每個文字分析容器配置的最低和建議 CPU 核心 (至少 2.6 GHz 或更快的版本) 與記憶體 (以 GB 為單位)。

| 容器 | 最小值 | 建議 |
|-----------|---------|-------------|
|關鍵片語擷取 | 1 核心，2 GB 記憶體 | 1 核心，4 GB 記憶體 |
|語言偵測 | 1 核心，2 GB 記憶體 | 1 核心，4 GB 記憶體 |
|情感分析 | 1 核心，8 GB 記憶體 | 1 核心，8 GB 記憶體 |

## <a name="download-container-images-from-microsoft-container-registry"></a>從 Microsoft Container Registry 下載容器映像

文字分析的容器映像可從 Microsoft Container Registry 取得。 下表列出可從 Microsoft Container Registry 取得文字分析容器的存放庫。 每個存放庫都包含容器映像，該容器映像必須下載並在本機上執行容器。

| 容器 | 存放庫 |
|-----------|------------|
|關鍵片語擷取 | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
|語言偵測 | `mcr.microsoft.com/azure-cognitive-services/language` |
|情感分析 | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

使用 [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) 命令來從存放庫下載容器映像。 例如，若要從存放庫下載最新的關鍵片語擷取容器映像，請使用下列命令：

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```

如需文字分析提供之標籤的完整說明，請參閱下列 Docker Hub 上的容器：

* [關鍵片語擷取](https://go.microsoft.com/fwlink/?linkid=2018757)
* [語言偵測](https://go.microsoft.com/fwlink/?linkid=2018759)
* [情感分析](https://go.microsoft.com/fwlink/?linkid=2018654)

> [!TIP]
> 您可以使用 [docker images](https://docs.docker.com/engine/reference/commandline/images/) 命令來列出已下載的容器映像。 例如，下列命令會列出識別碼、存放庫和每個已下載的容器映像的標籤，並格式化為表格：
>
>  ```Docker
>  docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
>  ```
>

## <a name="instantiate-a-container-from-a-downloaded-container-image"></a>從已下載的映象將容器具現化

使用 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令來從已下載的容器映像將容器具現化。 例如下列命令：

* 從情感分析容器映像將容器具現化
* 配置一個 CPU 核心和 8 GB 的記憶體
* 公開 TCP 連接埠 5000，並為容器配置虛擬 TTY
* 容器結束之後自動將它移除

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 1 mcr.microsoft.com/azure-cognitive-services/sentiment Eula=accept Billing=https://westus.api.cognitive.microsoft.com/text/analytics/v2.0 ApiKey=0123456789

```

> [!IMPORTANT]
> 必須指定 `Eula`、`Billing` 及 `ApiKey` 命令列選項以將容器具現化，否則容器將不會啟動。  如需詳細資訊，請參閱[帳單](#billing)。

具現化之後，您可以使用容器的主機 URI，從容器呼叫作業。 例如，下列主機 URI 代表在上述範例中具現化的情感分析容器：

```http
http://localhost:5000/
```

> [!TIP]
> 您可以從該容器的 `/swagger` 相對 URI 存取 [OpenAPI 規格](https://swagger.io/docs/specification/about/) \(英文\) (先前稱為 Swagger 規格)，其中會描述已具現化之容器所支援的作業。 例如，透過下列 URI 可存取適用於在上述範例中具現化之情感分析容器的 OpenAPI 規格：
>
>  ```http
>  http://localhost:5000/swagger
>  ```

您可以[呼叫 REST API 作業](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-call-api) \(可從容器取得\)，或使用 [Azure 認知服務文字分析 SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics) \(英文\) 用戶端程式庫來呼叫那些作業。  
> [!IMPORTANT]
> 如果您想要搭配使用用戶端程式庫和您的容器，您必須有 Azure 認知服務文字分析 SDK 2.1.0 版或更新版本。

從容器呼叫指定作業，與從 Azure 上的相對應服務呼叫相同作業之間的唯一差異，在於您針對前者會使用容器的主機 URI (而非 Azure 區域的主機 URI) 來呼叫該作業。 例如，如果您想要使用在美國西部 Azure 區域中執行的文字分析執行個體，便應該呼叫下列 REST API 作業：

```http
POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases
```

如果您想要使用在本機電腦上以預設設定執行的關鍵片語擷取容器，您會呼叫下列 REST API 作業：

```http
POST http://localhost:5000/text/analytics/v2.0/keyPhrases
```

### <a name="billing"></a>計費

文字分析容器會使用您 Azure 帳戶上相對應的文字分析資源傳送計費資訊至 Azure。 文字分析容器會針對帳單目的使用下列命令列選項：

| 選項 | 說明 |
|--------|-------------|
| `ApiKey` | 用來追蹤帳單資訊之文字分析資源的 API 金鑰。<br/>必須將此選項的值設定為適用於在 `Billing` 中指定之已佈建文字分析 Azure 資源的 API 金鑰。 |
| `Billing` | 用來追蹤帳單資訊之文字分析資源的端點。<br/>此選項的值必須設定為已佈建文字分析 Azure 資源的端點 URI。|
| `Eula` | 表示您已接受容器的授權。<br/>此選項的值必須設定為 `accept`。 |

> [!IMPORTANT]
> 這三個選項都必須指定為有效的值，否則將無法啟動容器。

如需這些選項的詳細資訊，請參閱[設定容器](../text-analytics-resource-container-config.md)。

## <a name="summary"></a>總結

在本文中，您已了解下載、安裝及執行文字分析容器的概念和工作流程。 摘要說明：

* 文字分析提供三個適用於 Docker 的 Linux 容器，封裝了關鍵片語擷取、語言偵測和情感分析。
* 容器映像是從 Azure 中的私人容器登錄下載。
* 容器映像是在 Docker 中執行。
* 您可以指定容器的主機 URI，來使用 REST API 或 SDK 呼叫文字分析容器中的作業。
* 將容器具現化時，您必須指定帳單資訊。
* ** 認知服務容器在未連線至 Azure 以進行計量的情況下，將無法被授權以執行。 客戶必須啟用容器以持續與計量服務進行帳單資訊的通訊。 認知服務容器不會將客戶資料 (例如正在分析的影像或文字) 傳送至 Microsoft。  

## <a name="next-steps"></a>後續步驟

* 檢閱[設定容器](../text-analytics-resource-container-config.md)以了解組態設定
* 檢閱[文字分析概觀](../overview.md)以深入了解關鍵片語偵測、語言偵測和情感分析  
* 參閱[文字分析 API](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) \(英文\) 以取得容器所支援之方法的詳細資訊。
* 參閱[常見問題集 (FAQ)](../text-analytics-resource-faq.md) 來解決與電腦視覺功能相關的問題。