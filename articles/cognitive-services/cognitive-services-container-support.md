---
title: 容器支援
titleSuffix: Azure Cognitive Services
description: 了解 Docker 容器如何使認知服務更接近您的資料。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: article
ms.date: 04/16/2019
ms.author: diberry
ms.openlocfilehash: 172774c90633c96c3a8e2c128df050fedeb8b52b
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2019
ms.locfileid: "60005956"
---
# <a name="container-support-in-azure-cognitive-services"></a>Azure 認知服務中的容器支援

Azure 認知服務中的容器支援可讓開發人員使用 Azure 中可用的相同豐富 API，並同時享有 [Docker 容器](https://www.docker.com/what-container) \(英文\) 所帶來的部署及裝載服務彈性。 容器支援現為預覽階段，提供給部分 Azure 認知服務使用，包括[電腦視覺](Computer-vision/Home.md)、[臉部](Face/Overview.md)[文字分析](text-analytics/overview.md)和 [Language Understanding](LUIS/luis-container-howto.md) (LUIS)。

容器化是散發軟體的方法，它會將應用程式或服務 (包括其相依性及設定) 一起封裝成容器映像。 在只需要小幅修改或不修改的情況下，便可將容器映像部署在容器主機上。 容器之間會彼此隔離，也會與基礎作業系統隔離，這使其磁碟使用量比虛擬機器更小。 容器可以從容器映像具現化以進行短期工作，並於不再需要時移除。

以下影片將示範如何使用認知服務容器。

[![認知服務的容器示範](./media/index/containers-video-image.png)](https://azure.microsoft.com/resources/videos/containers-support-of-cognitive-services)

[電腦視覺](Computer-vision/Home.md)、[臉部](Face/Overview.md)[文字分析](text-analytics/overview.md)和 [Language Understanding (LUIS)](LUIS/what-is-luis.md) 服務可在 [Microsoft Azure](https://azure.microsoft.com) 上取得。 請登入 [Azure 入口網站](https://portal.azure.com/)以建立並探索適用於這些服務的 Azure 資源。

## <a name="features-and-benefits"></a>功能與優點

- **對資料的控制**：讓客戶選擇這些認知服務在哪裡處理他們的資料。 這對於無法將資料傳送到雲端，但需要存取認知服務技術的客戶來說，這是不可或缺的。 支援混合式環境中的一致性，橫跨資料、管理、身分識別及安全性。
- **對模型更新的控制**：為客戶針對部署於其解決方案中的模型，提供版本控制和更新上的彈性。
- **可攜式架構**：能建立可攜式的應用程式架構，並將它部署至 Azure、內部部署及邊緣。 您可以將容器直接部署至 [Azure Kubernetes Service](../aks/index.yml)、[Azure 容器執行個體](../container-instances/index.yml)，或是已部署至 [Azure Stack](/azure-stack/operator) 的 [Kubernetes](https://kubernetes.io/) 叢集。 如需詳細資訊，請參閱[將 Kubernetes 部署至 Azure Stack](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)。
- **高輸送量 / 低延遲**：透過讓認知服務在實體鄰近客戶應用程式邏輯和資料執行，為客戶提供針對高輸送量及低延遲需求進行調整的能力。 容器不會限制每秒交易 (TPS)，而且如果您提供必要的硬體資源，會相應增加和相應放大來處理要求。 


## <a name="containers-in-azure-cognitive-services"></a>Azure 認知服務中的容器

Azure 認知服務容器能提供下列 Docker 容器集合，每個容器都包含 Azure 認知服務中服務之功能的子集：

| 服務 | 支援的定價層 | 容器 | 描述 |
|---------|----------|----------|-------------|
|[電腦視覺](Computer-vision/computer-vision-how-to-install-containers.md) |F0, S1|**辨識文字** |從具不同表面和背景之各種物件 (例如收據、海報和名片) 的影像擷取印刷文字。<br/><br/>**重要事項：** 辨識文字容器目前只適用於英文。<br>[要求存取](Computer-vision/computer-vision-how-to-install-containers.md#request-access-to-the-private-container-registry)|
|[臉部](Face/face-how-to-install-containers.md) |F0, S0|**臉部** |能偵測影像中的人臉並識別其特性，包括臉部特徵點 (例如鼻子和眼睛)、性別、年齡及其他機器預測的臉部容貌。 除了偵測以外，臉部也可以使用信賴分數檢查相同或不同影像中的兩張臉是否相同，或將臉部向資料庫進行比對，看看是否有樣貌相似或相同的臉部。 它也能夠使用共同視覺特徵，將相似臉部分組。<br>[要求存取](Face/face-how-to-install-containers.md#request-access-to-the-private-container-registry) |
|[LUIS](LUIS/luis-container-howto.md) |F0, S0|**LUIS** ([影像](https://go.microsoft.com/fwlink/?linkid=2043204))|將已定型或發佈的 Language Understanding 模型 (也稱為 LUIS 應用程式) 載入 Docker 容器中，並提供從容器的 API 端點存取查詢預測的權限。 您可以從容器收集查詢記錄，並將這些記錄重新上傳至 [LUIS 入口網站](https://www.luis.ai)，以改善應用程式的預測精確度。|
|[文字分析](text-analytics/how-tos/text-analytics-how-to-install-containers.md) |其中 F0、 S|**關鍵片語擷取** ([影像](https://go.microsoft.com/fwlink/?linkid=2018757)) |擷取關鍵片語來識別重點。 例如，若輸入文字為 "The food was delicious and there were wonderful staff"，API 即會傳回主要討論要點："food" 和 "wonderful staff"。 |
|[文字分析](text-analytics/how-tos/text-analytics-how-to-install-containers.md)|其中 F0、 S|**語言偵測** ([影像](https://go.microsoft.com/fwlink/?linkid=2018759)) |偵測輸入文字是以何種語言撰寫的，並針對要求所提交的每份文件回報單一語言代碼，最多可達 120 種語言。 語言代碼各配有一個分數，表示分數的強度。 |
|[文字分析](text-analytics/how-tos/text-analytics-how-to-install-containers.md)|其中 F0、 S|**情感分析** ([影像](https://go.microsoft.com/fwlink/?linkid=2018654)) |分析原始文字以尋找正面或負面情感的線索。 此 API 會為每份文件傳回 0 到 1 之間的情感分數，1 代表最正面的情感。 分析模型是使用大量文字主體和 Microsoft 的自然語言技術預先定型。 針對[選取的語言](./text-analytics/language-support.md)，API 可對您所提供的任何原始文字進行分析及評分，並直接將結果傳回至呼叫端應用程式。 |

此外，認知服務中支援容器[-全方位供應項目](https://azure.microsoft.com/pricing/details/cognitive-services/)。 您可以建立一個單一的認知服務-全方位資源，並如先前所述的所有容器類型中使用相同的計費金鑰。

## <a name="container-availability-in-azure-cognitive-services"></a>Azure 認知服務中的容器可用性

Azure 認知服務容器可透過您的 Azure 訂用帳戶公開取得，而 Docker 容器映像則可以從 Microsoft Container Registry 或 Docker Hub 提取。 您可以使用 [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) \(英文\) 命令來從適當的登錄下載容器映像。

> [!IMPORTANT]
> 目前您必須完成註冊程序以存取[臉部](Face/face-how-to-install-containers.md)和[辨識文字](Computer-vision/computer-vision-how-to-install-containers.md)容器；在該程序中您必須填寫並提交問卷，其中會包含有關您本身、您的公司，以及您要實作容器之使用案例的問題。 在您被授與存取權並取得認證之後，接著便可以從由 Azure Container Registry 所裝載的私人容器登錄，提取適用於臉部和辨識文字容器的容器映像。

## <a name="prerequisites"></a>必要條件

您必須滿足下列必要條件才能使用 Azure 認知服務容器：

**Docker 引擎**：您必須在本機安裝 Docker 引擎。 Docker 提供可在 [macOS](https://docs.docker.com/docker-for-mac/) \(英文\)、[Linux](https://docs.docker.com/engine/installation/#supported-platforms) \(英文\) 和 [Windows](https://docs.docker.com/docker-for-windows/) \(英文\) 上設定 Docker 環境的套件。 在 Windows 上，必須將 Docker 設定為支援 Linux 容器。 您也可以將 Docker 容器直接部署至 [Azure Kubernetes Service](../aks/index.yml) 或 [Azure 容器執行個體](../container-instances/index.yml)。

Docker 必須設定為允許容器與 Azure 連線，以及傳送帳單資料至 Azure。

**對 Microsoft Container Registry 和 Docker 的熟悉度**：您應具備對 Microsoft Container Registry 和 Docker 概念 (例如登錄、存放庫、容器和容器映像等) 的基本了解，以及基本 `docker` 命令的知識。

如需 Docker 和容器基本概念的入門，請參閱 [Docker 概觀](https://docs.docker.com/engine/docker-overview/) \(英文\)。

個別的容器可能也會有各自的需求，包括伺服器和記憶體配置需求。

## <a name="developer-samples"></a>開發人員範例

開發人員範例可從我們的 [GitHub 存放庫](https://github.com/Azure-Samples/cognitive-services-containers-samples)取得。

## <a name="next-steps"></a>後續步驟

安裝並探索由 Azure 認知服務中的容器所提供的功能：

* [安裝並使用電腦視覺容器](Computer-vision/computer-vision-how-to-install-containers.md)
* [安裝並使用臉部容器](Face/face-how-to-install-containers.md)
* [安裝並使用文字分析容器](text-analytics/how-tos/text-analytics-how-to-install-containers.md)
* [安裝和使用 Language Understanding (LUIS) 容器](LUIS/luis-container-howto.md)
