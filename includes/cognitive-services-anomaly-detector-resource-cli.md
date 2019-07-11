---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: 1ea0b01997d3d5cecff73f951c51de5898c2f07a
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503700"
---
下列 Azure CLI 命令會在免費定價層上佈建異常偵測器資源。 按一下 [試試看]  按鈕並貼上程式碼，然後按 Enter 鍵，即可在 Azure Cloud Shell 中執行該命令。 此命令會輸出用於驗證您應用程式的金鑰。 完成之後，針對您名為 `ANOMALY_DETECTOR_KEY` 的任一金鑰[建立環境變數](../articles/cognitive-services/cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication)。

> [!NOTE]
> * 您可以選擇性地執行下列動作：
>    * 使用 [Azure 入口網站](../articles/cognitive-services/cognitive-services-apis-create-account.md)或 [Azure CLI](../articles/cognitive-services/cognitive-services-apis-create-account.md) 在本機電腦上建立資源。
>    * 取得可免費使用 7 天的[試用版金鑰](https://azure.microsoft.com/try/cognitive-services/#decision)。 註冊之後，即可在 [Azure 網站](https://azure.microsoft.com/try/cognitive-services/my-apis/)上取得該金鑰。
>    * 在 [Azure 入口網站](https://portal.azure.com/)上檢視此資源。 

認知服務會由您佈建的 Azure 資源呈現。 每個認知服務帳戶 (和其相關聯的 Azure 資源) 都必須屬於 Azure 資源群組。

1. 建立 Azure 資源群組

    ```azurecli-interactive
    az group create \
        --name example-anomaly-detector-resource-group \
        --location westus2
    ```

2. 在免費層上建立異常偵測器資源

    ```azurecli-interactive
    az cognitiveservices account create \
        --name example-anomaly-detector-resource \
        --resource-group example-anomaly-detector-resource-group \
        --kind AnomalyDetector \
        --sku F0 \
        --location westus2 \
        --yes
    ```

3. 列出您資源的金鑰

    ```azurecli-interactive
    az cognitiveservices account keys list \
        --name example-anomaly-detector-resource \
        --resource-group example-anomaly-detector-resource-group
    ```