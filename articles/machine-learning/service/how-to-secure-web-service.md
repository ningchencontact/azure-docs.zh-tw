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
ms.openlocfilehash: 885d867d0733ef923d327d8d6a36fc1588fd4961
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2018
ms.locfileid: "48801007"
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

> [!IMPORTANT]
> 自我簽署的憑證應只用於開發。 這些憑證不應用於實際生產環境。 如果您使用自我簽署憑證，請參閱[使用自我簽署憑證來取用 Web 服務](#self-signed)一節以取得特定指示。


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

  ![Azure Machine Learning services：使用 SSL 保護 Web 服務](./media/how-to-secure-web-service/aks-public-ip-address.png)

## <a name="consume-authenticated-services"></a>取用已驗證的服務

### <a name="how-to-consume"></a>如何取用 
+ **對於 ACI 和 AKS**： 

  對於 ACI 和 AKS Web 服務，了解如何取用這些文章中的 Web 服務：
  + [如何部署至 ACI](how-to-deploy-to-aci.md)

  + [如何部署至 AKS](how-to-deploy-to-aks.md)

+ **對於 FPGA**：  

  下列範例示範如何使用 Python 和 C# 來取用已驗證的 FPGA 服務。
  將 `authkey` 取代為部署服務時所傳回的主要或次要金鑰。

  Python 範例：
    ```python
    from amlrealtimeai import PredictionClient
    client = PredictionClient(service.ipAddress, service.port, use_ssl=True, access_token="authKey")
    image_file = R'C:\path_to_file\image.jpg'
    results = client.score_image(image_file)
    ```

  C# 範例：
    ```csharp
    var client = new ScoringClient(host, 50051, useSSL, "authKey");
    float[,] result;
    using (var content = File.OpenRead(image))
        {
            IScoringRequest request = new ImageRequest(content);
            result = client.Score<float[,]>(request);
        }
    ```

### <a name="set-the-authorization-header"></a>設定驗證標頭
其他 gRPC 用戶端可以藉由設定授權標頭來驗證要求。 一般方法是建立結合 `SslCredentials` 與 `CallCredentials` 的 `ChannelCredentials` 物件。 這會新增至要求的授權標頭。 如需有關如何為特定標頭實作支援的詳細資訊，請參閱 [https://grpc.io/docs/guides/auth.html](https://grpc.io/docs/guides/auth.html)。

下列範例示範如何使用 C# 與 Go 來設定標頭：

+ 使用 C# 來設定標頭：
    ```csharp
    creds = ChannelCredentials.Create(baseCreds, CallCredentials.FromInterceptor(
                          async (context, metadata) =>
                          {
                              metadata.Add(new Metadata.Entry("authorization", "authKey"));
                              await Task.CompletedTask;
                          }));
    
    ```

+ 使用 Go 來設定標頭：
    ```go
    conn, err := grpc.Dial(serverAddr, 
        grpc.WithTransportCredentials(credentials.NewClientTLSFromCert(nil, "")),
        grpc.WithPerRPCCredentials(&authCreds{
        Key: "authKey"}))
    
    type authCreds struct {
        Key string
    }
    
    func (c *authCreds) GetRequestMetadata(context.Context, uri ...string) (map[string]string, error) {
        return map[string]string{
            "authorization": c.Key,
        }, nil
    }
    
    func (c *authCreds) RequireTransportSecurity() bool {
        return true
    }
    ```

<a id="self-signed"></a>

## <a name="consume-services-with-self-signed-certificates"></a>使用自我簽署憑證來取用服務

針對受到自我簽署憑證保護的伺服器，有兩種方式可讓用戶端向伺服器進行驗證：

* 在用戶端系統上，將用戶端系統上的 `GRPC_DEFAULT_SSL_ROOTS_FILE_PATH` 環境變數設定為指向憑證檔案。

* 建構 `SslCredentials` 物件時，將憑證檔案的內容傳遞給建構函式。

使用任一種方法都會導致 gRPC 使用憑證作為根憑證。

> [!IMPORTANT]
> gRPC 不會接受未受信任的憑證。 使用未受信任的憑證將會失敗，並顯示 `Unavailable` 狀態碼。 失敗的詳細資料包含 `Connection Failed`。
