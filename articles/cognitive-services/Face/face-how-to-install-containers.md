---
title: 安裝、執行容器
titlesuffix: Face - Azure Cognitive Services
description: 本逐步解說教學課程的內容包含如何下載、安裝及執行適用於臉部的容器。
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 11/14/2018
ms.author: diberry
ms.openlocfilehash: ac273ac7d4c6c371670e6b8a8170274602a5318b
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2018
ms.locfileid: "53536537"
---
# <a name="install-and-run-containers"></a>安裝及執行容器

容器化是散發軟體的方法，它會將應用程式或服務封裝成容器映像。 應用程式或服務的設定和相依性都包含在容器映像中。 接著可以將容器映像部署在容器主機上，且只需要稍微修改或不修改。 容器之間彼此隔離，也與基礎作業系統隔離，且磁碟使用量比虛擬機器更小。 容器可以從容器映像具現化以進行短期工作，並於不再需要時移除。

臉部會針對 Docker 提供名為「臉部」的標準化 Linux 容器，其能偵測影像中的人臉並識別其特性，包括臉部特徵點 (例如鼻子和眼睛)、性別、年齡及其他機器預測的臉部容貌。 除了偵測以外，臉部也可以使用信賴分數檢查相同或不同影像中的兩張臉是否相同，或將臉部向資料庫進行比對，看看是否有樣貌相似或相同的臉部。 它也能夠使用共同視覺特徵，將相似臉部分組。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="preparation"></a>準備工作

使用臉部容器之前，您必須符合下列必要條件：

**Docker 引擎**：您必須在本機安裝 Docker 引擎。 Docker 提供可在 [macOS](https://docs.docker.com/docker-for-mac/) \(英文\)、[Linux](https://docs.docker.com/engine/installation/#supported-platforms) \(英文\) 和 [Windows](https://docs.docker.com/docker-for-windows/) \(英文\) 上設定 Docker 環境的套件。 在 Windows 上，必須將 Docker 設定為支援 Linux 容器。 您也可以將 Docker 容器直接部署至 [Azure Kubernetes Service](../../aks/index.yml)、[Azure 容器執行個體](../../container-instances/index.yml)，或是已部署至 [Azure Stack](../../azure-stack/index.yml) 的 [Kubernetes](https://kubernetes.io/) \(英文\) 叢集。 如需將 Kubernetes 部署至 Azure Stack 的詳細資訊，請參閱[將 Kubernetes 部署至 Azure Stack](../../azure-stack/user/azure-stack-solution-template-kubernetes-deploy.md)。

Docker 必須設定為允許容器與 Azure 連線，以及傳送帳單資料至 Azure。

**對 Microsoft Container Registry 和 Docker 的熟悉度**：您應具備對 Microsoft Container Registry 和 Docker 概念 (例如登錄、存放庫、容器和容器映像等) 的基本了解，以及基本 `docker` 命令的知識。  

如需 Docker 和容器基本概念的入門，請參閱 [Docker 概觀](https://docs.docker.com/engine/docker-overview/) \(英文\)。

### <a name="container-requirements-and-recommendations"></a>容器的需求和建議

臉部容器需要最少 1 個 CPU 核心 (至少 2.6 GHz 或更快) 及 4 GB 的配置記憶體，但我們建議至少要有 2 個 CPU 核心及 6 GB 的配置記憶體。

## <a name="request-access-to-the-private-container-registry"></a>要求私人容器登錄的存取

您必須先填寫完成並提交[認知服務視覺容器要求表單](https://aka.ms/VisionContainersPreview) \(英文\)，以要求臉部容器的存取。 該表格需要有關您本身、您的公司，以及您將會使用該容器之使用者情節的資訊。 提交之後，Azure 認知服務小組會檢閱該表單，以確保您符合存取私人容器登錄的準則。

> [!IMPORTANT]
> 您必須在表單中使用與 Microsoft 帳戶 (MSA) 或 Azure Active Directory (Azure AD) 帳戶相關聯的電子郵件地址。

如果您的要求獲得核准，您會收到一封電子郵件，其中具有說明如何取得認證並存取私人容器登錄的指示。

## <a name="create-a-face-resource-on-azure"></a>在 Azure 上建立臉部資源

如果您想要使用臉部容器，便需要在 Azure 上建立臉部資源。 建立資源之後，您接著便會使用來自資源的訂用帳戶金鑰和端點 URL 來將容器具現化。 如需將容器具現化的詳細資訊，請參閱[從已下載的容器映像將容器具現化](#instantiate-a-container-from-a-downloaded-container-image)。

執行下列步驟來建立臉部資源，以及從臉部資源擷取資訊：

1. 在 Azure 入口網站中建立臉部資源。  
   如果您想要使用臉部容器，便必須先在 Azure 入口網站中建立相對應的臉部資源。 如需詳細資訊，請參閱[快速入門：在 Azure 入口網站中建立認知服務帳戶](../cognitive-services-apis-create-account.md)。

1. 取得 Azure 資源的端點 URL 和訂用帳戶金鑰。  
   Azure 資源建立之後，您必須使用來自該資源的端點 URL 和訂用帳戶金鑰，將相對應的臉部容器具現化。 您可以從 Azure 入口網站上臉部資源的 [快速入門] 和 [金鑰] 頁面，分別複製端點 URL 和訂用帳戶金鑰。

## <a name="log-in-to-the-private-container-registry"></a>登入私人容器登錄

有幾個方式可以向認知服務容器的私人容器登錄驗證，但從命令列進行的建議方法是使用 [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/) \(英文\)。

使用下列範例中所示的 [docker login](https://docs.docker.com/engine/reference/commandline/login/) 命令來登入 `containerpreview.azurecr.io` (認知服務容器的私人容器登錄)。 將 *\<username\>* 取代為使用者名稱，並將 *\<password\>* 取代為您從 Azure 認知服務小組收到的認證所提供的密碼。

```docker
docker login containerpreview.azurecr.io -u <username> -p <password>
```

如果您已經將您的認證保護在文字檔中，您可以將該文字檔的內容串連 (使用 `cat` 命令) 至 `docker login` 命令，如下列範例所示。 將 *\<passwordFile\>* 取代為包含密碼之文字檔的路徑和名稱，並將 *\<username\>* 取代為您認證中提供的使用者名稱。

```docker
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

## <a name="download-container-images-from-the-private-container-registry"></a>從私人容器登錄庫下載容器映像

臉部容器的容器映像可從 Azure Container Registry 中名為 `containerpreview.azurecr.io` 的私人 Docker 容器登錄中取得。 您必需從存放庫下載臉部容器的容器映像，以在本機執行。

使用 [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) \(英文\) 命令來從存放庫下載容器映像。 例如，若要從存放庫下載最新的臉部容器映像，請使用下列命令：

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

如需適用於臉部容器之可用標籤的完整描述，請參閱 Docker Hub 上的[辨識文字](https://go.microsoft.com/fwlink/?linkid=2018655) \(英文\)。

> [!TIP]
> 您可以使用 [docker images](https://docs.docker.com/engine/reference/commandline/images/) \(英文\) 命令來列出已下載的容器映像。 例如，下列命令會列出每個已下載之容器映像的識別碼、存放庫和標籤，並將它格式化為表格：
>
>  ```Docker
>  docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
>  ```
>

## <a name="instantiate-a-container-from-a-downloaded-container-image"></a>從已下載的容器映象將容器具現化

使用 [docker run](https://docs.docker.com/engine/reference/commandline/run/) \(英文\) 命令來從已下載的容器映像將容器具現化。 例如下列命令：

* 從臉部容器映象將容器具現化
* 配置兩個 CPU 核心和 6 GB 的記憶體
* 公開 TCP 連接埠 5000，並為容器配置虛擬 TTY
* 在容器結束之後自動將它移除

```Docker
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789
```

> [!IMPORTANT]
> 必須指定 `Eula`、`Billing` 及 `ApiKey` 選項以將容器具現化，否則容器將不會啟動。  如需詳細資訊，請參閱[帳單](#billing)。

具現化之後，您可以使用容器的主機 URI，從容器呼叫作業。 例如，下列主機 URI 代表在上述範例中具現化的臉部容器：

```http
http://localhost:5000/
```

> [!TIP]
> 您可以從該容器的 `/swagger` 相對 URI 存取 [OpenAPI 規格](https://swagger.io/docs/specification/about/) \(英文\) (先前稱為 Swagger 規格)，其中會描述已具現化之容器所支援的作業。 例如，透過下列 URI 可存取適用於在上述範例中具現化之臉部容器的 OpenAPI 規格：
>
>  ```http
>  http://localhost:5000/swagger
>  ```

您可以[呼叫可從容器取得的 REST API 作業](https://docs.microsoft.com/azure/cognitive-services/face/face-api-how-to-topics/howtodetectfacesinimage)，或使用 [Azure 認知服務臉部用戶端程式庫](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/) \(英文\) 用戶端程式庫來呼叫那些作業。  
> [!IMPORTANT]
> 如果您想要搭配容器使用用戶端程式庫，您必須有 Azure 認知服務臉部用戶端程式庫 2.0 版或更新版本。

從容器呼叫指定作業，與從 Azure 上的相對應服務呼叫相同作業之間的唯一差異，在於您針對前者會使用容器的主機 URI (而非 Azure 區域的主機 URI) 來呼叫該作業。 例如，如果您想要使用在美國西部 Azure 區域中執行的臉部執行個體來偵測臉部，便應該呼叫下列 REST API 作業：

```http
POST https://westus.api.cognitive.microsoft.com/face/v1.0/detect
```

如果您想要使用在本機電腦上以其預設設定執行的臉部容器來偵測臉部，便應該呼叫下列 REST API 作業：

```http
POST http://localhost:5000/face/v1.0/detect
```

### <a name="billing"></a>計費

臉部容器會使用您 Azure 帳戶上相對應的臉部資源傳送帳單資訊至 Azure。 臉部容器會針對帳單目的使用下列命令列選項：

| 選項 | 說明 |
|--------|-------------|
| `ApiKey` | 用來追蹤帳單資訊之臉部資源的 API 金鑰。<br/>此選項的值必須設定為適用於在 `Billing` 中指定之已佈建臉部 Azure 資源的 API 金鑰。 |
| `Billing` | 用來追蹤帳單資訊之臉部資源的端點。<br/>此選項的值必須設定為已佈建臉部 Azure 資源的端點 URI。|
| `Eula` | 表示您已接受容器的授權。<br/>此選項的值必須設定為 `accept`。 |

> [!IMPORTANT]
> 這三個選項都必須指定為有效的值，否則將無法啟動容器。

如需這些選項的詳細資訊，請參閱[設定容器](face-resource-container-config.md)。

## <a name="summary"></a>總結

在本文中，您已了解下載、安裝及執行臉部容器的概念和工作流程。 摘要說明：

* 臉部會針對 Docker 提供名為「臉部」的 Linux 容器，以偵測臉部或使用人員資料庫識別臉部。
* 容器映像是從 Azure 中的私人容器登錄下載。
* 容器映像是在 Docker 中執行。
* 您可以指定容器的主機 URI，來使用 REST API 或 SDK 呼叫臉部容器中的作業。
* 將容器具現化時，您必須指定帳單資訊。

> [!IMPORTANT]
> 認知服務容器在未連線至 Azure 以進行計量的情況下，將無法被授權以執行。 客戶必須啟用容器以持續與計量服務進行帳單資訊的通訊。 認知服務容器不會將客戶資料 (例如正在分析的影像或文字) 傳送至 Microsoft。

## <a name="next-steps"></a>後續步驟

* 檢閱[設定容器](face-resource-container-config.md)以了解組態設定
* 檢閱[臉部概觀](Overview.md)以深入了解偵測與識別臉部  
* 參閱[臉部 API](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) \(英文\) 以取得容器所支援之方法的詳細資訊。
* 參閱[常見問題集 (FAQ)](FAQ.md) 來解決與臉部功能相關的問題。
