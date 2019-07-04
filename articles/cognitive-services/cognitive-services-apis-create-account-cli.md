---
title: 建立認知服務帳戶使用 Azure CLI
titlesuffix: Azure Cognitive Services
description: 如何建立使用 Azure CLI 的 Azure 認知服務 Api 帳戶。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: aahi
ms.openlocfilehash: 26f7f3ab60347d9ec5f2a144410ad3de436f5b5c
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444877"
---
# <a name="create-a-cognitive-services-account-using-the-azure-command-line-interfacecli"></a>建立認知服務帳戶使用 Azure 命令列 Interface(CLI)

在本快速入門中，您將了解如何註冊 Azure 認知服務，並建立具有單一服務 」 或 「 多服務的訂用帳戶的帳戶使用[Azure 命令列 Interface(CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。 這些服務都由 Azure[資源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)，可讓您連接到一或多個 Azure 認知服務 Api。

## <a name="prerequisites"></a>必要條件

* 有效的 Azure 訂用帳戶。 免費[建立帳戶](https://azure.microsoft.com/free/)。
* [Azure 命令列 Interface(CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="install-the-azure-cli-and-sign-in"></a>安裝 Azure CLI 並登入 

安裝 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。 若要登入您的本機安裝的 cli，執行[az 登入](https://docs.microsoft.com/cli/azure/reference-index#az-login)命令：

```console
az login
```

您也可以使用綠色**試試** 按鈕，在您的瀏覽器中執行這些命令。
 
## <a name="create-a-new-azure-cognitive-services-resource-group"></a>建立新的 Azure 認知服務資源群組

認知服務訂用帳戶會以 Azure 資源。 每個認知服務帳戶 （和其相關聯的 Azure 資源） 必須屬於 Azure 資源群組。

### <a name="choose-your-resource-group-location"></a>選擇您的資源群組位置

若要建立資源時，您必須可用的 Azure 位置的其中一個訂用帳戶。 您可以擷取一份可用的位置，與[az 帳戶列出位置](/cli/azure/account#az_account_list)命令。 大部分的認知服務可以從數個位置存取。 選擇最靠近您的位置，或查看哪些位置可供該服務。

> [!IMPORTANT]
> * 因為您將需要呼叫 Azure 認知服務時，請記住您的 Azure 位置。
> * 一些認知服務的可用性會因地區而定。 如需詳細資訊，請參閱 <<c0> [ 依區域的 Azure 產品](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)。  

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

您的 azure 位置之後，請在使用 Azure CLI 建立新的資源群組[az 群組建立](/cli/azure/group#az_group_create)命令。

在下列範例中，將 azure 位置`westus2`與其中一個訂用帳戶可用的 Azure 位置。

```azurecli-interactive
az group create \
    --name cognitive-services-resource-group \
    --location westus2
```

## <a name="create-a-cognitive-services-resource"></a>建立認知服務資源

### <a name="choose-a-cognitive-service-and-pricing-tier"></a>選擇認知服務和定價層

在建立新的資源時，您必須知道您想要使用，以及與服務的 「 類型 」[定價層](https://azure.microsoft.com/pricing/details/cognitive-services/)（或 sku） 您想要。 您將使用這個和其他資訊做為參數建立的資源時。

> [!NOTE]
> 許多的認知服務有免費層試用服務時，您可以使用。 若要使用免費層，請使用`F0`做為資源的 sku。

### <a name="vision"></a>視覺

| 服務                    | 種類                      |
|----------------------------|---------------------------|
| 電腦視覺            | `ComputerVision`          |
| 自訂辨識-預測 | `CustomVision.Prediction` |
| Custom Vision-訓練   | `CustomVision.Training`   |
| 人臉識別 API                   | `Face`                    |
| 表單辨識器            | `FormRecognizer`          |
| 筆跡辨識器             | `InkRecognizer`           |

### <a name="search"></a>Search

| 服務            | 種類                  |
|--------------------|-----------------------|
| Bing 自動建議   | `Bing.Autosuggest.v7` |
| Bing 自訂搜尋 | `Bing.CustomSearch`   |
| Bing 實體搜尋 | `Bing.EntitySearch`   |
| Bing 搜尋        | `Bing.Search.v7`      |
| Bing 拼字檢查   | `Bing.SpellCheck.v7`  |

### <a name="speech"></a>語音

| 服務            | 種類                 |
|--------------------|----------------------|
| 語音服務    | `SpeechServices`     |
| 語音辨識 | `SpeakerRecognition` |

### <a name="language"></a>語言

| 服務            | 種類                |
|--------------------|---------------------|
| 形成了解 | `FormUnderstanding` |
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| 文字分析     | `TextAnalytics`     |
| Text Translation (文字翻譯)   | `TextTranslation`   |

### <a name="decision"></a>決策

| 服務           | 種類               |
|-------------------|--------------------|
| 異常偵測器  | `AnomalyDetector`  |
| 內容仲裁 | `ContentModerator` |
| 個人化工具      | `Personalizer`     |

您可以找到一份可用的認知服務 「 類型 」 與[az cognitiveservices 帳戶清單類型](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-list-kinds)命令：

```azurecli-interactive
az cognitiveservices account list-kinds
```

### <a name="add-a-new-resource-to-your-resource-group"></a>將新的資源新增至您的資源群組

若要建立並訂閱新的認知服務資源，請使用[az cognitiveservices 帳戶建立](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create)命令。 此命令會將新的計費資源新增至稍早建立的資源群組。 在建立新的資源時，您必須知道您想要使用，以及其定價層 （或 sku） 的服務 「 種類 」 和 Azure 的位置：

您可以建立異常偵測器，名為 F0 （免費） 資源`anomaly-detector-resource`使用下列命令。

```azurecli-interactive
az cognitiveservices account create \
    --name anomaly-detector-resource \
    --group cognitive-services-resource-group \
    --kind AnomalyDetector \
    --sku F0 \
    --location westus2 \
    --yes
```

## <a name="get-the-keys-for-your-subscription"></a>取得訂用帳戶金鑰

若要登入您的本機安裝的命令列 Interface(CLI)，使用[az 登入](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login)命令。

```console
az login
```

使用[az cognitiveservices 帳戶金鑰清單](https://docs.microsoft.com/cli/azure/cognitiveservices/account/keys?view=azure-cli-latest#az-cognitiveservices-account-keys-list)命令，以取得您的認知服務資源的索引鍵。

```azurecli-interactive
    az cognitiveservices account keys list \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group
```

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>清除資源

如果您想要清除和移除的認知服務訂用帳戶，您可以刪除資源或資源群組。 刪除資源群組時，也會刪除資源群組相關聯的任何其他資源。

若要移除資源群組和其相關聯的資源，包括新的儲存體帳戶，請使用 az group delete 命令。

```azurecli-interactive
az group delete --name storage-resource-group
```

## <a name="see-also"></a>請參閱

* [驗證 Azure 認知服務要求](authentication.md)
* [什麼是 Azure 認知服務？](Welcome.md)
* [自然語言支援](language-support.md)
* [Docker 容器支援](cognitive-services-container-support.md)
