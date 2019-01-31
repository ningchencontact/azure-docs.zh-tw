---
title: 將 Azure 認知服務部署至 Azure Stack |Microsoft Docs
description: 了解如何將 Azure 認知服務部署至 Azure Stack。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2018
ms.author: mabrigg
ms.reviewer: guanghu
ms.lastreviewed: 12/11/2018
ms.openlocfilehash: 331a71d4f807e1e596a91c1463064e3f6dcbd1e1
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/30/2019
ms.locfileid: "55247029"
---
# <a name="deploy-azure-cognitive-services-to-azure-stack"></a>將 Azure 認知服務部署至 Azure Stack

*適用於：Azure Stack 整合式系統和 Azure Stack 開發套件*

> [!Note]  
> Azure Stack 上的 Azure 認知服務為預覽階段。

您可以在 Azure Stack 上使用 Azure 認知服務與容器支援。 Azure 認知服務中的容器支援可讓您使用 Azure 中可用的相同豐富應用程式開發介面。 使用容器可讓您靈活地部署和裝載 [Docker 容器](https://www.docker.com/what-container)中提供的服務。 容器支援現為預覽階段，提供給部分 Azure 認知服務使用，包括[電腦視覺](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)、[臉部](https://docs.microsoft.com/azure/cognitive-services/face/overview)、[文字分析](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview)和 [Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/luis-container-howto) (LUIS)。

容器化是散發軟體的方法，它會將應用程式或服務 (包括其相依性及設定) 封裝成容器映像。 只需進行少量修改或無須修改，您就可以將映像部署至容器主機。 每個容器都與其他容器和基礎作業系統隔離。 系統本身僅包含執行映像所需的元件。 容器主機的磁碟使用量比虛擬機器的使用量小。 此外，您可以從映像為短期工作建立容器，並於不再需要時移除。

## <a name="use-containers-with-cognitive-services-on-azure-stack"></a>在 Azure Stack 上使用具有認知服務的容器

- **對資料的控制**  
  允許您的應用程式使用者在使用認知服務時控制其資料。 您可以提供認知服務給無法傳送資料給全域 Azure 或公用雲端的應用程式使用者。

- **對模型更新的控制**  
  為應用程式使用者提供部署在其解決方案中的模型版本和更新。

- **可攜式架構**  
  啟用建立可攜式應用程式架構，以便您可以將解決方案部署到公用雲端、私人雲端內部部署或邊緣裝置。 您可以將容器部署至 Azure Kubernetes Service、Azure 容器執行個體，或是至 Azure Stack 的 Kubernetes 叢集。 如需詳細資訊，請參閱[將 Kubernetes 部署至 Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)。

- **高輸送量與低延遲**  
   為您的應用程式使用者提供能夠調整流量暴增的能力，以實現高輸送量與低延遲。 在 Azure Kubernetes Service 中啟用認知服務，實體接近其應用程式邏輯和資料。

利用 Azure Stack，在 Kubernetes 叢集中部署認知服務容器及您的應用程式容器，以實現高可用性和彈性調整。 您可以透過將認知服務與在應用程式服務、Functions、Blob 儲存體、SQL 或 mySQL 資料庫上建立的元件加以結合，以開發應用程式。 

如需認知服務容器的詳細資訊，請移至[Azure 認知服務中的容器支援](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support)。

## <a name="deploy-the-azure-face-api"></a>部署 Azure 臉部 API

本文說明如何在 Azure Stack 的 Kubernetes 叢集上部署 Azure 臉部 API。 您可以使用相同方法將其他認知服務容器部署在 Azure Stack 的 Kubernetes 叢集上。

## <a name="prerequisites"></a>必要條件

開始之前，您需要下列項目：

1.  要求從 Azure 認知服務容器登錄存取臉部容器映像的容器登錄存取權。 如需詳細資料請移至[要求私人容器登錄的存取權](https://docs.microsoft.com/azure/cognitive-services/face/face-how-to-install-containers#request-access-to-the-private-container-registry)區段。

2.  準備 Azure Stack 上的 Kubernetes 叢集。 您可以遵循[將 Kubernetes 部署至 Azure Stack](azure-stack-solution-template-kubernetes-deploy.md)一文。

## <a name="create-azure-resources"></a>建立 Azure 資源

在 Azure 上建立認知服務資源，以分別預覽臉部、LUIS 或辨識文字容器。 您需要使用來自資源的訂用帳戶金鑰和端點 URL 來將認知服務容器具現化。

1.  在 Azure 入口網站中建立 Azure 資源。 如果您想要預覽臉部容器，便必須先在 Azure 入口網站中建立相對應的臉部資源。 如需詳細資訊，請參閱[快速入門：在 Azure 入口網站中建立認知服務帳戶](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。

    >  [!Note]  
    >  臉部或電腦視覺資源必須使用 F0 定價層。

2.  取得 Azure 資源的端點 URL 和訂用帳戶金鑰。 Azure 資源建立之後，您必須使用來自該資源的訂用帳戶金鑰和端點 URL，將相對應的臉部、LUIS 或辨識文字容器預覽具現化。

## <a name="create-a-kubernetes-secret"></a>建立 Kubernetes 祕密 

使用 Kubectl create secret 命令，存取私人容器登錄。 將 **&lt;username&gt;** 取代為使用者名稱，並將 **&lt;password&gt;** 取代為您從 Azure 認知服務小組收到的認證所提供的密碼。

```bash  
    kubectl create secret docker-registry <secretName> \
        --docker-server='containerpreview.azurecr.io' \
        --docker-username='<username>' \
        --docker-password='<password>' 
```

## <a name="prepare-a-yaml-configure-file"></a>準備 YAML 設定檔

您將使用 YAML 設定檔來簡化 Kubernetes 叢集上的認知服務部署。

以下是 YAML 範例設定檔以將臉部服務部署至 Azure Stack：

```Yaml  
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: <deploymentName>
spec:
  replicas: <replicaNumber>
  template:
    metadata:
      labels:
        app: <appName>
    spec:
      containers:
      - name: <containersName>
        image: <ImageLocation>
        env: 
        - name: EULA
          value: accept 
        - name: Billing
          value: <billingURL> 
        - name: apikey
          value: <apiKey>
        tty: true
        stdin: true
        ports:
        - containerPort: 5000 
      imagePullSecrets:
        - name: <secretName>
---
apiVersion: v1
kind: Service
metadata:
  name: <LBName>
spec:
  type: LoadBalancer
  ports:
  - port: 5000
    targetPort : 5000
    name: <PortsName>
  selector:
    app: <appName>
```

在此 YAML 設定檔中，使用您用來從 Azure Container Registry 取得認知服務容器映像的密碼。 您與使用秘密檔案來部署容器的特定複本。 您也會建立負載平衡器，以確保使用者可以存取這項外部服務。

索引鍵欄位的詳細資料：

| 欄位 | 注意 |
| --- | --- |
| replicaNumber | 定義要建立執行個體的初始複本。 您可以部署之後再進行調整。 |
| ImageLocation | 表示 ACR 中特定認知服務容器映像的位置。 例如，臉部服務：`aicpppe.azurecr.io/microsoft/cognitive-services-face` |
| BillingURL |在[建立 Azure 資源](#create-azure-resources)的步驟中記下端點 URL |
| ApiKey | 在[建立 Azure 資源](#create-azure-resources)的步驟中記下訂用帳戶金鑰 |
| SecretName | 剛才在[建立密碼來存取私人容器登錄](#create-secrete-to-access-the-private-container-registry)的步驟中記下密碼名稱 |

## <a name="deploy-the-cognitive-service"></a>部署認知服務

使用下列命令來部署認知服務容器

```bash  
    Kubectl apply -f <yamlFineName>
```
使用下列命令來監視部署方式： 
```bash  
    Kubectl get pod – watch
```

## <a name="test-the-cognitive-service"></a>測試認知服務

從該容器的 **/swagger** 相對 URI 存取 [OpenAPI 規格](https://swagger.io/docs/specification/about/) (英文) (先前為 Swagger 規格)，其中描述具現化容器支援的作業。 例如，透過下列 URI 可存取適用於在上述範例中具現化之情感分析容器的 OpenAPI 規格：

```HTTP  
http:<External IP>:5000/swagger
```

您可以從下列命令取得外部 IP 位址： 

```bash  
    Kubectl get svc <LoadBalancerName>
```

## <a name="try-the-services-with-python"></a>嘗試使用具有 Python 的服務

您可以嘗試執行一些簡單的 Python 指令碼來驗證 Azure Stack 上的認知服務。 有[電腦視覺](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)、[臉部](https://docs.microsoft.com/azure/cognitive-services/face/overview)和[文字分析](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview)，以及[Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/luis-container-howto) (LUIS) 等官方 Python 快速入門範例供您參考。

要使 Python 應用程式對容器上執行的服務加以運作，則需要需要記住兩件事： 
1. 容器中的認知服務不需要子機碼 進行驗證，但仍然需要輸入任何字串作為預留位置來滿足 SDK。 
2. 以實際的服務端點 IP 位址取代 base_URL 

以下是範例 Python 指令碼，使用臉部服務 Python SDK 來偵測和構建影像中的臉部。 

```Python  
import cognitive_face as CF

# Cognitive Services in container do not need sub keys for authentication
# Keep the invalid key to satisfy the SDK inputs requirement. 
KEY = '0'  #  (keeping the quotes in place).
CF.Key.set(KEY)

# Get your actual Ip Address of service endpoint of your cognitive service on Azure Stack
BASE_URL = 'http://<svc IP Address>:5000/face/v1.0/'  
CF.BaseUrl.set(BASE_URL)

# You can use this example JPG or replace the URL below with your own URL to a JPEG image.
img_url = 'https://raw.githubusercontent.com/Microsoft/Cognitive-Face-Windows/master/Data/detection1.jpg'
faces = CF.face.detect(img_url)
print(faces)

```

## <a name="next-steps"></a>後續步驟

[如何安裝及執行電腦視覺 API 容器。](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-how-to-install-containers)

[如何安裝及執行臉部 API 容器](https://docs.microsoft.com/azure/cognitive-services/face/face-how-to-install-containers#create-a-face-resource-on-azure)

[如何安裝及執行文字分析 API 容器](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers)

[如何安裝並執行 Language Understanding (LIUS) 容器](https://docs.microsoft.com/azure/cognitive-services/luis/luis-container-howto)