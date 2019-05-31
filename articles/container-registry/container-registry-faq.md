---
title: Azure 容器登錄庫-常見問題集
description: Azure Container Registry 服務的相關常見問題的解答
services: container-registry
author: sajayantony
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 5/13/2019
ms.author: sajaya
ms.openlocfilehash: 1400c023e43179a9c8490334e262711486c75a2d
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/30/2019
ms.locfileid: "66417934"
---
# <a name="frequently-asked-questions-about-azure-container-registry"></a>關於 Azure Container Registry 的常見問題集

本文說明常見問題集與 Azure Container Registry 的已知的問題。

## <a name="resource-management"></a>資源管理

- [我可以建立 Azure container registry 使用 Resource Manager 範本嗎？](#can-i-create-an-azure-container-registry-using-a-resource-manager-template)
- [是否有安全性弱點掃描在 ACR 中映像？](#is-there-security-vulnerability-scanning-for-images-in-acr)
- [如何在使用 Azure Container Registry 設定 Kubernetes？](#how-do-i-configure-kubernetes-with-azure-container-registry)
- [如何取得容器登錄的系統管理員認證？](#how-do-i-get-admin-credentials-for-a-container-registry)
- [如何取得 Resource Manager 範本中的系統管理員認證？](#how-do-i-get-admin-credentials-in-a-resource-manager-template)
- [雖然使用 Azure CLI 或 Azure PowerShell 刪除複寫刪除的複寫會失敗並禁止狀態](#delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell)

### <a name="can-i-create-an-azure-container-registry-using-a-resource-manager-template"></a>我可以建立 Azure Container Registry 使用 Resource Manager 範本嗎？

是。 以下是[範本](https://github.com/Azure/azure-cli/blob/master/src/command_modules/azure-cli-acr/azure/cli/command_modules/acr/template.json)可用來建立登錄庫。

### <a name="is-there-security-vulnerability-scanning-for-images-in-acr"></a>是否有安全性弱點掃描在 ACR 中映像？

是。 請參閱文件[Twistlock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry/)並[Aqua](http://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry)。

### <a name="how-do-i-configure-kubernetes-with-azure-container-registry"></a>如何在使用 Azure Container Registry 設定 Kubernetes？

請參閱文件[Kubernetes](http://kubernetes.io/docs/user-guide/images/#using-azure-container-registry-acr)步驟並[Azure Kubernetes Service](container-registry-auth-aks.md)。

### <a name="how-do-i-get-admin-credentials-for-a-container-registry"></a>如何取得容器登錄的系統管理員認證？

> [!IMPORTANT]
> 系統管理員使用者帳戶是針對單一使用者存取登錄中的，主要是基於測試目的而設計的。 不建議將管理帳戶認證與其他使用者共用。 針對遠端控制案例的使用者和服務主體，建議使用個人身分識別。 請參閱[驗證概觀](container-registry-authentication.md)。

之前收到系統管理員認證，請確定已啟用登錄的系統管理員使用者。

若要取得使用 Azure CLI 的認證：

```azurecli
az acr credential show -n myRegistry
```

使用 Azure Powershell:

```powershell
Invoke-AzureRmResourceAction -Action listCredentials -ResourceType Microsoft.ContainerRegistry/registries -ResourceGroupName myResourceGroup -ResourceName myRegistry
```

### <a name="how-do-i-get-admin-credentials-in-a-resource-manager-template"></a>如何取得 Resource Manager 範本中的系統管理員認證？

> [!IMPORTANT]
> 系統管理員使用者帳戶是針對單一使用者存取登錄中的，主要是基於測試目的而設計的。 不建議將管理帳戶認證與其他使用者共用。 針對遠端控制案例的使用者和服務主體，建議使用個人身分識別。 請參閱[驗證概觀](container-registry-authentication.md)。

之前收到系統管理員認證，請確定已啟用登錄的系統管理員使用者。

若要取得第一個密碼：

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[0].value]"
}
```

若要取得第二個密碼：

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[1].value]"
}
```

### <a name="delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell"></a>雖然使用 Azure CLI 或 Azure PowerShell 刪除複寫刪除的複寫會失敗並禁止狀態

當使用者對登錄庫的權限，但訂用帳戶沒有讀取器層級的權限時，會出現錯誤。 若要解決此問題，將指派給使用者的訂用帳戶的讀取器權限：


```azurecli  
az role assignment create --role "Reader" --assignee user@contoso.com --scope /subscriptions/<subscription_id> 
```

## <a name="registry-operations"></a>登錄作業

- [如何存取 Docker Registry HTTP API V2？](#how-do-i-access-docker-registry-http-api-v2)
- [如何刪除存放庫中的任何標記所未參考的所有資訊清單？](#how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository)
- [為什麼不會登錄配額使用量減少之後刪除映像？](#why-does-the-registry-quota-usage-not-reduce-after-deleting-images)
- [如何驗證儲存體配額變更？](#how-do-i-validate-storage-quota-changes)
- [如何驗證我的登錄在容器中執行 CLI 時？](#how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container)
- [Azure Container Registry 提供 TLS 1.2 的僅限設定以及如何啟用 TLS v1.2？](#does-azure-container-registry-offer-tls-v12-only-configuration-and-how-to-enable-tls-v12)
- [支援內容信任 Azure Container Registry？](#does-azure-container-registry-support-content-trust)
- [如何授與存取 pull 或 push 映像，而不需要管理登錄資源的權限？](#how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource)
- [如何啟用自動的映像登錄的隔離](#how-do-i-enable-automatic-image-quarantine-for-a-registry)

### <a name="how-do-i-access-docker-registry-http-api-v2"></a>如何存取 Docker Registry HTTP API V2？

ACR 支援 Docker Registry HTTP API V2。 Api 可在存取`https://<your registry login server>/v2/`。 範例： `https://mycontainerregistry.azurecr.io/v2/`

### <a name="how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository"></a>如何刪除存放庫中的任何標記所未參考的所有資訊清單？

如果您在 bash 中：

```bash
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv  | xargs -I% az acr repository delete -n myRegistry -t myRepository@%
```

Powershell:

```powershell
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv | %{ az acr repository delete -n myRegistry -t myRepository@$_ }
```

注意：您可以新增`-y`中 [刪除] 命令，以略過確認。

如需詳細資訊，請參閱 <<c0> [ 刪除在 Azure Container Registry 的容器映像](container-registry-delete.md)。

### <a name="why-does-the-registry-quota-usage-not-reduce-after-deleting-images"></a>為什麼不會登錄配額使用量減少之後刪除映像？

基礎層仍會由其他的容器映像參考，就可能發生這種情況。 如果您刪除任何參考的映像時，登錄使用量更新幾分鐘的時間。

### <a name="how-do-i-validate-storage-quota-changes"></a>如何驗證儲存體配額變更？

1 GB 的圖層，使用下列 docker 檔案建立映像。 這可確保映像不會在登錄中的任何其他映像所共用的圖層。

```dockerfile
FROM alpine
RUN dd if=/dev/urandom of=1GB.bin  bs=32M  count=32
RUN ls -lh 1GB.bin
```

建置，並將映像推送至登錄使用 docker CLI。

```bash
docker build -t myregistry.azurecr.io/1gb:latest .
docker push myregistry.azurecr.io/1gb:latest
```

您應該可以看到在 Azure 入口網站中，已增加的儲存體使用量，或您可以查詢使用 CLI 的使用方式。

```bash
az acr show-usage -n myregistry
```

刪除使用 Azure CLI 或入口網站的映像，並檢查更新的使用方式，在幾分鐘的時間。

```bash
az acr repository delete -n myregistry --image 1gb
```

### <a name="how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container"></a>如何驗證我的登錄在容器中執行 CLI 時？

您需要掛接 Docker 通訊端，執行 Azure CLI 的容器：

```bash
docker run -it -v /var/run/docker.sock:/var/run/docker.sock azuresdk/azure-cli-python:dev
```

在容器中，安裝`docker`:

```bash
apk --update add docker
```

然後使用您的登錄進行驗證：

```azurecli
az acr login -n MyRegistry
```

### <a name="does-azure-container-registry-offer-tls-v12-only-configuration-and-how-to-enable-tls-v12"></a>Azure Container Registry 提供 TLS 1.2 的僅限設定以及如何啟用 TLS v1.2？

是。 啟用 TLS 使用任何最新的 docker 用戶端 (版本 18.03.0 和更新版本)。 

### <a name="does-azure-container-registry-support-content-trust"></a>支援內容信任 Azure Container Registry？

是，您可以使用受信任的映像在 Azure Container Registry，因為[Docker 公證](https://docs.docker.com/notary/getting_started/)已整合，可啟用。 如需詳細資訊，請參閱 <<c0> [ 內容的信任，在 Azure Container Registry](container-registry-content-trust.md)。


####  <a name="where-is-the-file-for-the-thumbprint-located"></a>其中是位於憑證指紋的檔案？

在下`~/.docker/trust/tuf/myregistry.azurecr.io/myrepository/metadata`:

* 公開金鑰和憑證的所有角色 （除了委派角色） 會儲存在`root.json`。
* 公開金鑰和委派角色的憑證會儲存在其父代角色的 JSON 檔案 (例如`targets.json`針對`targets/releases`角色)。

建議您整體的 TUF 驗證 Docker 和公證的用戶端，即可完成後，確認這些公用金鑰和憑證。

### <a name="how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource"></a>如何授與存取 pull 或 push 映像，而不需要管理登錄資源的權限？

支援 ACR[自訂角色](container-registry-roles.md)可提供不同層級的權限。 具體而言，`AcrPull`和`AcrPush`角色，可讓使用者提取及/或推送映像，而不需要管理 Azure 中的登錄資源的權限。

* Azure 入口網站：您的登錄存取控制 (IAM)]-> [新增]-> [(選取`AcrPull`或`AcrPush`角色)。
* Azure CLI：執行下列命令來尋找登錄的資源識別碼：

  ```azurecli
  az acr show -n myRegistry
  ```
  
  則您可以指派給`AcrPull`或是`AcrPush`給使用者的角色 (下列範例會使用`AcrPull`):

  ```azurecli
    az role assignment create --scope resource_id --role AcrPull --assignee user@example.com
    ```

  或者，您也可以將角色指派給其應用程式識別碼所識別的服務主體：

  ```
  az role assignment create --scope resource_id --role AcrPull --assignee 00000000-0000-0000-0000-000000000000
  ```

受託人就無法進行驗證，並存取登錄中的映像。

* 若要向登錄庫：
    
  ```azurecli
  az acr login -n myRegistry 
  ```

* 清單存放庫：

  ```azurecli
  az acr repository list -n myRegistry
  ```

 若要提取的映像：
    
  ```azurecli
  docker pull myregistry.azurecr.io/hello-world
  ```

使用的唯一`AcrPull`或`AcrPush`角色、 受託人沒有權限來管理 Azure 中的登錄資源。 例如，`az acr list`或`az acr show -n myRegistry`不會顯示登錄。

### <a name="how-do-i-enable-automatic-image-quarantine-for-a-registry"></a>如何啟用自動的映像登錄的隔離？

映像隔離目前為預覽功能的 ACR。 您可以啟用登錄庫的隔離模式，如此只有這些映像已成功傳遞安全性掃描會對一般使用者顯示。 如需詳細資訊，請參閱 < [ACR GitHub 存放庫](https://github.com/Azure/acr/tree/master/docs/preview/quarantine)。

## <a name="diagnostics"></a>診斷

- [docker 提取失敗，發生錯誤： net/http： 要求取消時等候連接 (Client.Timeout 超過等待標頭)](#docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers)
- [docker 推播會成功，但 docker 提取失敗，發生錯誤： 未經授權： 需要驗證](#docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required)
- [啟用與取得 docker 精靈的偵錯記錄檔](#enable-and-get-the-debug-logs-of-the-docker-daemon) 
- [新的使用者權限可能不會生效之後立即更新](#new-user-permissions-may-not-be-effective-immediately-after-updating)
- [正確的格式，直接的 REST API 呼叫中未指定驗證資訊](#authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls)
- [為什麼不會在 Azure 入口網站列出我所有的存放庫或標記？](#why-does-the-azure-portal-not-list-all-my-repositories-or-tags)
- [如何收集 Windows 上的 http 追蹤？](#how-do-i-collect-http-traces-on-windows)

### <a name="docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers"></a>docker 提取失敗，發生錯誤： net/http： 要求取消時等候連接 (Client.Timeout 超過等待標頭)

 - 如果此錯誤是暫時性的問題，然後重試將會成功。
 - 如果`docker pull`持續失敗，則可能有問題，並使用 docker 精靈。 重新啟動 docker 精靈通常可以降低問題。 
 - 如果您繼續看到此問題，重新啟動 docker 精靈之後，問題可能是某些與機器的網路連線問題。 若要檢查電腦上的一般網路是否狀況良好，請嘗試命令這類`ping www.bing.com`。
 - 您一律應該有重試機制在所有的 docker 用戶端作業。

### <a name="docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required"></a>docker 推播會成功，但 docker 提取失敗，發生錯誤： 未經授權： 需要驗證

Red Hat 版本的 docker 精靈，就會發生此錯誤其中`--signature-verification`預設會啟用。 您可以執行下列命令來檢查 Red Hat Enterprise Linux (RHEL) 或 Fedora 的 docker 精靈選項：

```bash
grep OPTIONS /etc/sysconfig/docker
```

比方說，Fedora 28 伺服器具有下列的 docker 精靈選項：

```
OPTIONS='--selinux-enabled --log-driver=journald --live-restore'
```

具有`--signature-verification=false`遺漏，`docker pull`失敗並發生類似的錯誤：

```bash
Trying to pull repository myregistry.azurecr.io/myimage ...
unauthorized: authentication required
```

若要解決此錯誤：
1. 將選項加入`--signature-verification=false`至 docker 精靈組態檔`/etc/sysconfig/docker`。 例如:

  ```
  OPTIONS='--selinux-enabled --log-driver=journald --live-restore --signature-verification=false'
  ```
2. 執行下列命令，重新啟動 docker 精靈服務：

  ```bash
  sudo systemctl restart docker.service
  ```

詳細資料`--signature-verification`可以找到執行`man dockerd`。

### <a name="enable-and-get-the-debug-logs-of-the-docker-daemon"></a>啟用與取得 docker 精靈的偵錯記錄檔  

開始`dockerd`與`debug`選項。 首先，建立 docker 精靈的組態檔 (`/etc/docker/daemon.json`) 如果不存在，並新增`debug`選項：

```json
{   
    "debug": true   
}
```

然後，重新啟動精靈。 例如，使用 Ubuntu 14.04:

```bash
sudo service docker restart
```

詳細資料可在[Docker 文件](https://docs.docker.com/engine/admin/#enable-debugging)。 

 * 記錄檔可能會產生不同的位置，根據您的系統。 例如，對於 Ubuntu 14.04，它有`/var/log/upstart/docker.log`。   
請參閱[Docker 文件](https://docs.docker.com/engine/admin/#read-the-logs)如需詳細資訊。    

 * Docker for Windows，如 %localappdata%/docker/ 情況下，所產生的記錄檔。 不過它不可以包含所有的偵錯資訊尚未。   

   若要存取完整的服務精靈記錄檔，您可能需要一些額外的步驟：

    ```console
    docker run --privileged -it --rm -v /var/run/docker.sock:/var/run/docker.sock -v /usr/local/bin/docker:/usr/local/bin/docker alpine sh

    docker run --net=host --ipc=host --uts=host --pid=host -it --security-opt=seccomp=unconfined --privileged --rm -v /:/host alpine /bin/sh
    chroot /host
    ```
    現在您可以存取的 VM 正在執行的所有檔案`dockerd`。 記錄檔位於`/var/log/docker.log`。

### <a name="new-user-permissions-may-not-be-effective-immediately-after-updating"></a>新的使用者權限可能不會生效之後立即更新

當您新增授與權限 （新的角色） 的服務主體，變更可能不會立即生效。 有兩個可能的原因：

* Azure Active Directory 角色指派延遲。 通常很快速，但可能需要由於傳播延遲的分鐘。
* ACR 語彙基元的伺服器上的權限延遲。 這可能需要最多 10 分鐘的時間。 若要解決，您可以`docker logout`，然後驗證一次使用相同的使用者在 1 分鐘後：

  ```bash
  docker logout myregistry.azurecr.io
  docker login myregistry.azurecr.io
  ```

目前 ACR 不支援使用者的主要複寫刪除。 因應措施，包括家用的複寫建立在範本中，但略過建立新增`"condition": false`，如下所示：

```json
{
    "name": "[concat(parameters('acrName'), '/', parameters('location'))]",
    "condition": false,
    "type": "Microsoft.ContainerRegistry/registries/replications",
    "apiVersion": "2017-10-01",
    "location": "[parameters('location')]",
    "properties": {},
    "dependsOn": [
        "[concat('Microsoft.ContainerRegistry/registries/', parameters('acrName'))]"
     ]
},
```

### <a name="authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls"></a>正確的格式，直接的 REST API 呼叫中未指定驗證資訊

您可能會遇到`InvalidAuthenticationInfo`錯誤，特別使用`curl`工具搭配選項`-L`， `--location` （若要遵循重新導向）。
例如，正在擷取 blob 使用`curl`與`-L`選項和基本驗證：

```bash
curl -L -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest
```

可能會產生下列回應：

```xml
<?xml version="1.0" encoding="utf-8"?>
<Error><Code>InvalidAuthenticationInfo</Code><Message>Authentication information is not given in the correct format. Check the value of Authorization header.
RequestId:00000000-0000-0000-0000-000000000000
Time:2019-01-01T00:00:00.0000000Z</Message></Error>
```

根本原因是某些`curl`實作遵循從原始要求的標頭的重新導向。

若要解決此問題，您需要遵循重新導向，以手動方式不含標頭。 列印的回應標頭`-D -`的選項`curl`然後解壓縮：`Location`標頭：

```bash
redirect_url=$(curl -s -D - -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest | grep "^Location: " | cut -d " " -f2 | tr -d '\r')
curl $redirect_url
```

### <a name="why-does-the-azure-portal-not-list-all-my-repositories-or-tags"></a>為什麼不會在 Azure 入口網站列出我所有的存放庫或標記？ 

如果您使用 Microsoft Edge 瀏覽器，您可以看到最多 100 個存放庫或列出的標記。 如果您的登錄具有 100 個以上的存放庫或標記，我們建議您若要列出所有使用 Firefox 或 Chrome 瀏覽器。

### <a name="how-do-i-collect-http-traces-on-windows"></a>如何收集 Windows 上的 http 追蹤？

#### <a name="prerequisites"></a>必要條件

- 啟用在 fiddler 中的解密 https:  <https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS>
- 啟用 Docker 以使用 proxy，以透過 Docker ui: <https://docs.docker.com/docker-for-windows/#proxies>
- 請務必還原完成時。  Docker 將不會啟用此工作並不在執行 fiddler。

#### <a name="windows-containers"></a>Windows 容器

設定 Docker proxy 127.0.0.1: 8888

#### <a name="linux-containers"></a>Linux 容器

找到的 ip 的 Docker vm 的虛擬交換器：

```powershell
(Get-NetIPAddress -InterfaceAlias "*Docker*" -AddressFamily IPv4).IPAddress
```

設定 Docker proxy 前, 一個命令和連接埠 8888 (例如 10.0.75.1:8888) 的輸出

## <a name="tasks"></a>工作

- [如何批次 [取消] 執行？](#how-do-i-batch-cancel-runs)
- [我要如何 az acr 組建命令中包含的.git 資料夾？](#how-do-i-include-the-git-folder-in-az-acr-build-command)

### <a name="how-do-i-batch-cancel-runs"></a>如何批次 [取消] 執行？

下列命令會取消所有正在執行的工作，在指定的登錄中。

```azurecli
az acr task list-runs -r $myregistry --run-status Running --query '[].runId' -o tsv \
| xargs -I% az acr task cancel-run -r $myregistry --run-id %
```

### <a name="how-do-i-include-the-git-folder-in-az-acr-build-command"></a>我要如何 az acr 組建命令中包含的.git 資料夾？

如果您傳遞的本機來源資料夾`az acr build`命令，`.git`預設會排除已上傳的封裝上的資料夾。 您可以建立`.dockerignore`使用下列設定的檔案。 它會告訴要還原下的所有檔案的命令`.git`上載封裝中。 

```
!.git/**
```

這項設定也適用於`az acr run`命令。

## <a name="cicd-integration"></a>CI/CD 整合

- [CircleCI](https://github.com/Azure/acr/blob/master/docs/integration/CircleCI.md)
- [GitHub 動作](https://github.com/Azure/acr/blob/master/docs/integration/github-actions/github-actions.md)


## <a name="next-steps"></a>後續步驟

* [了解更多](container-registry-intro.md)關於 Azure Container Registry。
