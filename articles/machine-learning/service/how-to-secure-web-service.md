---
title: 使用 SSL 保護 web 服務
titleSuffix: Azure Machine Learning service
description: 了解如何透過 Azure Machine Learning 服務部署啟用 HTTPS 的 web 服務安全。 HTTPS 會保護使用傳輸層安全性 (TLS) 安全通訊端層 (SSL) 來取代用戶端的資料。 用戶端也會使用 HTTPS 來驗證 web 服務的身分識別。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 04/29/2019
ms.custom: seodec18
ms.openlocfilehash: 779a34800057284ce77b6d76e030ddca5fadc25f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66393816"
---
# <a name="use-ssl-to-secure-a-web-service-through-azure-machine-learning"></a>使用 SSL 來保護透過 Azure Machine Learning web 服務

這篇文章會示範如何透過 Azure Machine Learning 服務部署 web 服務安全。

您使用[HTTPS](https://en.wikipedia.org/wiki/HTTPS)來限制存取 web 服務和保護用戶端送出的資料。 HTTPS 用戶端與 web 服務之間的安全通訊加密以協助兩者之間的通訊。 加密會使用[傳輸層安全性 (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security)。 TLS 仍有時稱為*安全通訊端層*(SSL)，這是 TLS 的前身。

> [!TIP]
> Azure 機器學習服務 SDK 會使用詞彙 [SSL]，以確保通訊安全相關的屬性。 這並不表示不使用您的 web 服務*TLS*。 SSL 指的只是更普遍認可。

TLS 和 SSL 同時依賴*數位憑證*，協助進行加密和身分識別驗證。 如需有關憑證的數位運作的詳細資訊，請參閱 Wikipedia 主題[公開金鑰基礎結構](https://en.wikipedia.org/wiki/Public_key_infrastructure)。

> [!WARNING]
> 如果您未使用 HTTPS，您的 web 服務，服務來回傳送的資料可能在網際網路上的其他人看到。
>
> HTTPS 也可讓用戶端確認它連接到伺服器的真確性。 這項功能可保護用戶端[攔截](https://en.wikipedia.org/wiki/Man-in-the-middle_attack)攻擊。

這是安全的 web 服務的一般程序：

1. 取得網域名稱。

2. 取得數位憑證。

3. 部署或更新 web 服務啟用 ssl。

4. 更新您的 DNS 以指向 Web 服務。

> [!IMPORTANT]
> 如果您要部署到 Azure Kubernetes Service (AKS) 中，您可以購買您自己的憑證，或使用由 Microsoft 所提供的憑證。 如果您使用來自 Microsoft 的憑證，您不需要取得網域名稱或 SSL 憑證。 如需詳細資訊，請參閱 <<c0> [ 啟用 SSL 並部署](#enable)一節。

保護跨 web 服務時，兩者的些微不同[部署目標](how-to-deploy-and-where.md)。

## <a name="get-a-domain-name"></a>取得網域名稱

如果您尚未擁有網域名稱，向購買*網域名稱註冊機構*。 處理程序和價格視註冊機構而有所不同。 在註冊機構會提供工具來管理網域名稱。 您可以使用這些工具來對應的完整的網域名稱 (FQDN) (例如 www\.contoso.com) 來裝載您的 web 服務的 IP 位址。

## <a name="get-an-ssl-certificate"></a>取得 SSL 憑證

有許多方法可取得的 SSL 憑證 （數位憑證）。 最常見的作法是向購買*憑證授權單位*(CA)。 無論您取得憑證，您需要下列檔案：

* **憑證**。 憑證必須包含完整的憑證鏈結，而且必須是"PEM 編碼。 」
* **金鑰**。 索引鍵也必須是 PEM 編碼。

當您要求憑證時，您必須提供您計劃使用 web 服務之位址的 FQDN (例如，www\.contoso.com)。 驗證 web 服務的身分識別與比較戳記到憑證的地址和用戶端使用的位址。 如果這些位址不相符，用戶端會取得錯誤訊息。

> [!TIP]
> 如果憑證授權單位無法提供的憑證和金鑰，以 PEM 編碼的檔案，您可以使用一個公用程式，例如[OpenSSL](https://www.openssl.org/)變更格式。

> [!WARNING]
> 使用*自我簽署*只能用於開發的憑證。 請勿在生產環境中使用它們。 自我簽署的憑證可能會造成用戶端應用程式的問題。 如需詳細資訊，請參閱用戶端應用程式所使用的網路程式庫的文件。

## <a id="enable"></a> 啟用 SSL 並部署

若要部署 （或重新部署） 已啟用 SSL 的服務，將*ssl_enabled*參數為"True"才會適用。 設定*ssl_certificate*的值的參數*憑證*檔案。 設定*ssl_key*的值*金鑰*檔案。

### <a name="deploy-on-aks-and-field-programmable-gate-array-fpga"></a>將部署在 AKS 和欄位可程式化 gate array (FPGA)

  > [!NOTE]
  > 當您部署安全的 web 服務的視覺介面時，也適用於這一節的資訊。 如果您不熟悉使用 Python SDK，請參閱[什麼是 Azure 機器學習服務 SDK for Python？](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)。

當您部署至 AKS 時，您可以建立新的 AKS 叢集，或連結現有。
  
-  如果您建立新的叢集，您會使用 **[AksCompute.provisionining_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#provisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none-)** 。
- 如果您附加現有的叢集，您會使用 **[AksCompute.attach_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none-)** 。 兩者都傳回組態物件具有**enable_ssl**方法。

**Enable_ssl**方法可以使用由 Microsoft 所提供的憑證或您購買的憑證。

  * 當您使用來自 Microsoft 的憑證時，您必須使用*leaf_domain_label*參數。 此參數會產生服務的 DNS 名稱。 例如，"myservice"的值建立的網域名稱"myservice\<六個隨機字元 >。\<azureregion >。 cloudapp.azure.com"，其中\<azureregion > 是包含服務的區域。 或者，您可以使用*overwrite_existing_domain*參數覆寫現有*leaf_domain_label*。

    若要部署 （或重新部署） 已啟用 SSL 的服務，將*ssl_enabled*參數為"True"才會適用。 設定*ssl_certificate*的值的參數*憑證*檔案。 設定*ssl_key*的值*金鑰*檔案。

    > [!IMPORTANT]
    > 當您使用來自 Microsoft 的憑證時，您不需要購買您自己的憑證或網域名稱。

    下列範例示範如何建立組態，可讓 Microsoft 的 SSL 憑證：

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable SSL
    provisioning_config = AksCompute.provisioning_configuration()
    provisioning_config.enable_ssl(leaf_domain_label = "myservice")
    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                          cluster_name = cluster_name)
    attach_config.enable_ssl(leaf_domain_label = "myservice")
    ```

  * 當您使用*購買的憑證*，您使用*ssl_cert_pem_file*， *ssl_key_pem_file*，以及*ssl_cname*參數。 下列範例示範如何使用 *.pem*檔案，以建立使用您所購買的 SSL 憑證的組態：

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable SSL
    provisioning_config = AksCompute.provisioning_configuration()
    provisioning_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
    attach_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

如需詳細資訊*enable_ssl*，請參閱[AksProvisioningConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksprovisioningconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-)並[AksAttachConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksattachconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-)。

### <a name="deploy-on-azure-container-instances"></a>在 Azure 容器執行個體上部署

當您部署至 Azure Container Instances 時，您提供值給 SSL 相關的參數，如下列程式碼程式碼片段所示：

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
```

如需詳細資訊，請參閱 < [AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none-)。

## <a name="update-your-dns"></a>更新您的 DNS

接下來，您必須更新 DNS 以指向 Web 服務。

+ **容器執行個體：**

  使用您的網域名稱註冊機構的工具來更新您的網域名稱的 DNS 記錄。 記錄必須指向該服務的 IP 位址。

  可以有延遲數分鐘或幾個小時後用戶端可解析的網域名稱，取決於該註冊機構和 「 存留時間 」 (TTL) 設定的網域名稱。

+ **供 AKS 使用：**

  > [!WARNING]
  > 如果您使用*leaf_domain_label*使用來自 Microsoft 的憑證，以建立服務，不要手動更新叢集的 DNS 值。 值應該會自動設定。

  在 [更新 DNS**組態**] 索引標籤的公用 IP 位址的 AKS 叢集。 （請參閱下圖）。公用 IP 位址會是包含 AKS 代理程式節點和其他網路資源的資源群組下建立的資源類型。

  ![Azure Machine Learning 服務：使用 SSL 保護 Web 服務](./media/how-to-secure-web-service/aks-public-ip-address.png)

## <a name="next-steps"></a>後續步驟
了解如何：
+ [使用部署為 Web 服務的機器學習模型](how-to-consume-web-service.md)
+ [安全地執行實驗和在 Azure 虛擬網路內的推斷](how-to-enable-virtual-network.md)
