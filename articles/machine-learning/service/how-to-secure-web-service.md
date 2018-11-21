---
title: 使用 SSL 保護 Azure Machine Learning Web 服務
description: 了解如何保護使用 Azure Machine Learning services 部署的 Web 服務。 您可以限制對 Web 服務的存取，以及使用安全通訊端層 (SSL) 和金鑰型驗證來保護用戶端所提交的資料。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 10/02/2018
ms.openlocfilehash: ec7b956f080837b297bac56e6237ac0672601ce7
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/09/2018
ms.locfileid: "51344479"
---
# <a name="secure-azure-machine-learning-web-services-with-ssl"></a>使用 SSL 保護 Azure Machine Learning Web 服務

在此文章中，您將了解如何保護使用 Azure Machine Learning services 部署的 Web 服務。 您可以限制對 Web 服務的存取，以及使用安全通訊端層 (SSL) 和金鑰型驗證來保護用戶端所提交的資料。

> [!Warning]
> 如果您未啟用 SSL，則網際網路上的任何使用者都將能夠呼叫 Web 服務。

SSL 會加密在用戶端與 Web 服務之間傳送的資料。 用戶端也會使用 SSL 來驗證伺服器的身分識別。 系統只會針對已經提供 SSL 憑證和金鑰的服務來啟用驗證。  如果您啟用 SSL，則會在存取 Web 服務時要求驗證金鑰。

無論您要部署使用 SSL 啟用的 Web 服務，或者要針對現有已部署的 Web 服務啟用 SSL，步驟都一樣：

1. 取得網域名稱。

2. 取得 SSL 憑證。

3. 部署或更新已啟用 SSL 設定的 Web 服務。

4. 更新您的 DNS 以指向 Web 服務。

跨[部署目標](how-to-deploy-and-where.md)保護 Web 服務時有些微差異。 

## <a name="get-a-domain-name"></a>取得網域名稱

如果您還未擁有網域名稱，可以向__網域名稱註冊機構__購買。 不同的註冊機構有不同的流程，而且費用也不同。 註冊機構也會為您提供管理網域名稱所使用的工具。 這類工具可用來將完整網域名稱 (例如 www.contoso.com) 對應到裝載您 Web 服務的 IP 位址。

## <a name="get-an-ssl-certificate"></a>取得 SSL 憑證

有很多種方法可以取得 SSL 憑證。 最常見的作法是向__憑證授權單位__ (CA) 購買。 不論您在哪裡取得憑證，都需要下列檔案：

* __憑證__。 憑證必須包含完整的憑證鏈結，而且必須以 PEM 編碼。
* __金鑰__。 此金鑰必須以 PEM 編碼。

要求憑證時，您必須提供打算用於 Web 服務的位址完整網域名稱 (FQDN)。 例如，www.contoso.com。 驗證 Web 服務的身分識別時，會比較在憑證上加上戳記的位址和用戶端所使用的位址。 如果位址不相符，用戶端就會收到錯誤。 

> [!TIP]
> 如果憑證授權單位無法以 PEM 編碼的檔案來提供憑證和金鑰，您可以使用 [OpenSSL](https://www.openssl.org/) 之類的公用程式來變更格式。

> [!WARNING]
> 自我簽署的憑證應只用於開發。 這些憑證不應用於實際生產環境。 自我簽署的憑證可能會造成用戶端應用程式的問題。 如需詳細資訊，請參閱用戶端應用程式中所用網路程式庫的文件。

## <a name="enable-ssl-and-deploy"></a>啟用 SSL 並部署

若要在 SSL 啟用時部署 (重新部署) 服務，請在適當的情況下，將 `ssl_enabled` 參數設定為 `True`。 將 `ssl_certificate` 參數設定為__憑證__檔案的值，並將 `ssl_key` 設定為__金鑰__檔案的值。 

+ **在 Azure Kubernetes Service (AKS) 上部署**
  
  佈建 AKS 叢集時，提供 SSL 相關參數的值，如下列程式碼片段所示：

    ```python
    from azureml.core.compute import AksCompute
    
    provisioning_config = AksCompute.provisioning_configuration(ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

+ **在 Azure 容器執行個體 (ACI) 上部署**
 
  部署到 ACI 時，提供 SSL 相關參數的值，如下列程式碼片段所示：

    ```python
    from azureml.core.webservice import AciWebservice
    
    aci_config = AciWebservice.deploy_configuration(ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

+ **在現場可程式化閘陣列 (FPGA) 上部署**

  `create_service` 作業的回應包含服務的 IP 位址。 將 DNS 名稱對應至服務的 IP 位址時，將會使用此 IP 位址。 回應也包含取用服務所使用的__主要金鑰__和__次要金鑰__。 提供 SSL 相關參數的值，如下列程式碼片段所示：

    ```python
    from amlrealtimeai import DeploymentClient
    
    subscription_id = "<Your Azure Subscription ID>"
    resource_group = "<Your Azure Resource Group Name>"
    model_management_account = "<Your AzureML Model Management Account Name>"
    location = "eastus2"
    
    model_name = "resnet50-model"
    service_name = "quickstart-service"
    
    deployment_client = DeploymentClient(subscription_id, resource_group, model_management_account, location)
    
    with open('cert.pem','r') as cert_file:
        with open('key.pem','r') as key_file:
            cert = cert_file.read()
            key = key_file.read()
            service = deployment_client.create_service(service_name, model_id, ssl_enabled=True, ssl_certificate=cert, ssl_key=key)
    ```

## <a name="update-your-dns"></a>更新您的 DNS

接下來，您必須更新 DNS 以指向 Web 服務。

+ **對於 ACI 和 FPGA**：  

  使用網域名稱註冊機構所提供的工具來更新您網域名稱的 DNS 記錄。 這筆記錄必須指向此服務的 IP 位址。  

  根據註冊機構以及針對網域名稱所設定的存留時間 (TTL)，可能需要等待數分鐘到數小時的時間，用戶端才能解析網域名稱。

+ **對於 AKS**： 

  在 AKS 叢集之 [公用 IP 位址] 的 [設定] 索引標籤底下更新 DNS，如下圖所示。 您可以找到公用 IP 位址，作為在包含 AKS 代理程式節點和其他網路資源之資源群組下方所建立的其中一個資源類型。

  ![Azure Machine Learning services：使用 SSL 保護 Web 服務](./media/how-to-secure-web-service/aks-public-ip-address.png)self-

## <a name="next-steps"></a>後續步驟

了解如何[取用部署為 Web 服務的 ML 模型](how-to-consume-web-service.md)。