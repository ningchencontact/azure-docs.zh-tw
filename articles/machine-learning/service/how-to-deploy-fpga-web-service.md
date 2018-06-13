---
title: 如何使用 Azure Machine Learning 在 FPGA 上將模型部署為 Web 服務
description: 了解如何使用 Azure Machine Learning 以 FPGA 上執行的模型來部署 Web 服務。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 05/07/2018
ms.openlocfilehash: f3237980a1ad1969b5cf8d42d547ddf96608dd97
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
ms.locfileid: "33784467"
---
# <a name="deploy-a-model-as-a-web-service-on-an-fpga-with-azure-machine-learning"></a>使用 Azure Machine Learning 在 FPGA 上將模型部署為 Web 服務

在本文件中，您會了解如何設定工作站環境，並在[現場可程式化閘陣列 (FPGA)](concept-accelerate-with-fpgas.md) 上，將模型部署為 Web 服務。 Web 服務會使用 Project Brainwave，在 FPGA 上執行模型。

使用 FPGA 時，即使是透過單一批次大小，也提供超低延遲推斷。

## <a name="create-an-azure-machine-learning-model-management-account"></a>建立 Azure Machine Learning 模型管理帳戶

1. 移至 [Azure 入口網站](https://aka.ms/aml-create-mma)上的「模型管理帳戶」建立頁面。

2. 在入口網站的 [美國東部 2] 區域中，建立「模型管理帳戶」。

   ![[建立模型管理帳戶] 畫面的影像](media/how-to-deploy-fpga-web-service/azure-portal-create-mma.PNG)

3. 為您的「模型管理帳戶」指定名稱、選擇訂用帳戶，然後選擇資源群組。

   >[!IMPORTANT]
   >在 [位置] 中，您必須選擇 [美國東部 2] 作為區域。  目前沒有其他可用的區域。

4. 選擇定價層 (S1 已足夠，但 S2 和 S3 也可以)。  不支援 DevTest 層。  

5. 按一下 [選取] 以確認定價層。

6. 在左側的 [ML 模型管理] 上，按一下 [建立]。

## <a name="get-model-management-account-information"></a>取得「模型管理帳戶」資訊

若要取得「模型管理帳戶」(MMA) 的相關資訊，請按一下 Azure 入口網站上的 [模型管理帳戶]。

複製下列項目的值︰

+ 「模型管理帳戶」名稱 (位於左上角)
+ 資源群組名稱
+ 訂用帳戶識別碼
+ 位置 (使用 "eastus2")

![「模型管理帳戶」資訊](media/how-to-deploy-fpga-web-service/azure-portal-mma-info.PNG)

## <a name="set-up-your-machine"></a>設定您的電腦

若要設定用於 FPGA 部署的工作站，請使用下列步驟：

1. 下載並安裝最新版的 [Git](https://git-scm.com/downloads)。

2. 安裝 [Anaconda (Python 3.6)](https://conda.io/miniconda.html)。

3. 若要下載 Anaconda 環境，請從 Git 提示字元使用下列命令：

    ```
    git clone https://aka.ms/aml-real-time-ai
    ```

4. 若要建立環境，請開啟 **Anaconda 提示字元** (非 Azure Machine Learning Workbench 提示字元)，然後執行下列命令：

    > [!IMPORTANT]
    > `environment.yml` 檔案位於您在上一個步驟所複製的 git 存放庫中。 視需要變更路徑，以指向您工作站上的檔案。

    ```
    conda env create -f environment.yml
    ```

5. 若要啟用環境，請使用下列命令：

    ```
    conda activate amlrealtimeai
    ```

6. 若要啟動 Jupyter Notebook 伺服器，請使用下列命令：

    ```
    jupyter notebook
    ```

    此命令的輸出類似下列文字：

    ```text
    Copy/paste this URL into your browser when you connect for the first time, to login with a token:
        http://localhost:8888/?token=bb2ce89cc8ae931f5df50f96e3a6badfc826ff4100e78075
    ```

    > [!TIP]
    > 每次執行命令時，您都會取得不同的權杖。

    如果您的瀏覽器不會自動開啟至 Jupyter Notebook，請使用前一個命令所傳回的 HTTP URL 來開啟頁面。

    ![Jupyter Notebook 網頁的影像](./media/how-to-deploy-fpga-web-service/jupyter-notebook.png)

## <a name="deploy-your-model"></a>部署模型

從 Jupyter Notebook 開啟 `notebooks/resnet50` 目錄中的 `00_QuickStart.ipynb` Notebook。 依照 Notebook 中的指示進行下列操作：

* 定義服務
* 部署模型
* 取用已部署的模型
* 刪除已部署的服務

> [!IMPORTANT]
> 若要將延遲和輸送量最佳化，您的工作站應該與端點位於相同 Azure 區域中。  目前 API 是在美國東部的 Azure 區域中建立的。

## <a name="ssltls-and-authentication"></a>SSL/TLS 和驗證

Azure Machine Learning 提供 SSL 支援和金鑰型驗證。 這可讓您限制對服務的存取，並保護用戶端所提交的資料。

> [!NOTE]
> 本節中的步驟僅適用於 Azure Machine Learning 硬體加速模型。 如需標準 Azure Machine Learning 服務，請參閱[如何在 Azure Machine Learning 計算叢集上設定 SSL](https://docs.microsoft.com/azure/machine-learning/preview/how-to-setup-ssl-on-mlc) 文件。

> [!IMPORTANT]
> 系統只會針對已經提供 SSL 憑證和金鑰的服務來啟用驗證。 
>
> 如果您未啟用 SSL，則網際網路上的任何使用者都將能夠呼叫服務。
>
> 如果您啟用了 SSL，則在存取服務時，便會要求驗證金鑰。

SSL 會加密在用戶端與服務之間傳送的資料。 用戶端也會使用 SSL 來驗證伺服器的身分識別。

您可以在 SSL 啟用時部署服務，或更新已部署的服務來加以啟用。 兩者步驟相同：

1. 取得網域名稱。

2. 取得 SSL 憑證。

3. 在 SSL 啟用時部署或更新服務。

4. 將您的 DNS 更新為指向此服務。

### <a name="acquire-a-domain-name"></a>取得網域名稱

如果您還未擁有網域名稱，可以向__網域名稱註冊機構__購買。 不同的註冊機構有不同的流程，而且費用也不同。 註冊機構也會為您提供管理網域名稱所使用的工具。 這類工具可用來將完整的網域名稱 (例如 www.contoso.com) 對應到裝載服務所在的 IP 位址。

### <a name="acquire-an-ssl-certificate"></a>取得 SSL 憑證

有很多種方法可以取得 SSL 憑證。 最常見的作法是向__憑證授權單位__ (CA) 購買。 不論您在哪裡取得憑證，都需要下列檔案：

* __憑證__。 憑證必須包含完整的憑證鏈結，而且必須以 PEM 編碼。
* __金鑰__。 此金鑰必須以 PEM 編碼。

> [!TIP]
> 如果憑證授權單位無法以 PEM 編碼的檔案來提供憑證和金鑰，您可以使用 [OpenSSL](https://www.openssl.org/) 之類的公用程式來變更格式。

> [!IMPORTANT]
> 自我簽署的憑證應只用於開發。 這些憑證不應用於實際生產環境。
>
> 如果您使用自我簽署的憑證，請參閱[使用自我簽署的憑證取用服務](#self-signed)一節的特定指示。

> [!WARNING]
> 要求憑證時，您必須提供打算用於服務的位址完整網域名稱 (FQDN)。 例如，www.contoso.com。驗證服務的身分識別時，會比較憑證上的位址戳記和用戶端所使用的位址。
>
> 如果位址不相符，用戶端就會收到錯誤。 

### <a name="deploy-or-update-the-service-with-ssl-enabled"></a>在 SSL 啟用時部署或更新服務

若要在 SSL 啟用時部署服務，請將 `ssl_enabled` 參數設定為 `True`。 將 `ssl_certificate` 參數設定為__憑證__檔案的值，並將 `ssl_key` 設定為__金鑰__檔案的值。 下列範例會示範在 SSL 啟用時部署服務：

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

`create_service` 作業的回應包含服務的 IP 位址。 將 DNS 名稱對應至服務的 IP 位址時，將會使用此 IP 位址。

回應也包含取用服務所使用的__主要金鑰__和__次要金鑰__。

### <a name="update-your-dns-to-point-to-the-service"></a>將您的 DNS 更新為指向此服務

使用網域名稱註冊機構所提供的工具來更新您網域名稱的 DNS 記錄。 這筆記錄必須指向此服務的 IP 位址。

> [!NOTE]
> 根據註冊機構以及針對網域名稱所設定的存留時間 (TTL)，可能需要等待數分鐘到數小時的時間，用戶端才能解析網域名稱。

### <a name="consuming-authenticated-services"></a>取用已驗證的服務

下列範例會示範如何使用 Python 和 C# 來取用已驗證的服務：

> [!NOTE]
> 將 `authkey` 取代為建立服務時所傳回的主要或次要金鑰。

```python
from amlrealtimeai import PredictionClient
client = PredictionClient(service.ipAddress, service.port, use_ssl=True, access_token="authKey")
image_file = R'C:\path_to_file\image.jpg'
results = client.score_image(image_file)
```

```csharp
var client = new ScoringClient(host, 50051, useSSL, "authKey");
float[,] result;
using (var content = File.OpenRead(image))
    {
        IScoringRequest request = new ImageRequest(content);
        result = client.Score<float[,]>(request);
    }
```

其他 gRPC 用戶端可以藉由設定授權標頭來驗證要求。 一般方法是建立結合 `SslCredentials` 與 `CallCredentials` 的 `ChannelCredentials` 物件。 這會新增至要求的授權標頭。 如需有關如何為特定標頭實作支援的詳細資訊，請參閱 [https://grpc.io/docs/guides/auth.html](https://grpc.io/docs/guides/auth.html)。

下列範例示範如何使用 C# 與 Go 來設定標頭：

```csharp
creds = ChannelCredentials.Create(baseCreds, CallCredentials.FromInterceptor(
                      async (context, metadata) =>
                      {
                          metadata.Add(new Metadata.Entry("authorization", "authKey"));
                          await Task.CompletedTask;
                      }));

```

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

### <a id="self-signed"></a>使用自我簽署的憑證來取用服務

針對受到自我簽署憑證保護的伺服器，有兩種方式可讓用戶端向伺服器進行驗證：

* 在用戶端系統上，將用戶端系統上的 `GRPC_DEFAULT_SSL_ROOTS_FILE_PATH` 環境變數設定為指向憑證檔案。

* 建構 `SslCredentials` 物件時，將憑證檔案的內容傳遞給建構函式。

使用任一種方法都會導致 gRPC 使用憑證作為根憑證。

> [!IMPORTANT]
> gRPC 將不會接受未受信任的憑證。 使用未受信任的憑證將會失敗，並顯示 `Unavailable` 狀態碼。 失敗的詳細資料包含 `Connection Failed`。
