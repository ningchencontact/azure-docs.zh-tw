---
title: 啟用 Azure Container Instances 中的 SSL
description: 建立 Azure Container Instances 中執行的容器群組的 SSL 或 TLS 端點
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 04/03/2019
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 10c015a9aee4ed8be54805f7adaae5bb4b5c422f
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870383"
---
# <a name="enable-an-ssl-endpoint-in-a-container-group"></a>啟用容器群組中的 SSL 端點

這篇文章示範如何建立[容器群組](container-instances-container-groups.md)應用程式容器與執行 SSL 提供者的側車容器。 藉由設定個別的 SSL 端點的容器群組，您啟用 SSL 連線應用程式而不需要變更您的應用程式程式碼。

您會設定兩個容器所組成的容器群組：
* 執行簡單的 web 應用程式使用的公用的 Microsoft 應用程式容器[aci helloworld](https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld)映像。 
* 側車容器執行大眾[Nginx](https://hub.docker.com/_/nginx)映像，設定為使用 SSL。 

在此範例中，容器群組只會公開連接埠 443 nginx 具有其公用 IP 位址。 Nginx HTTPS 要求路由傳送至隨附 web 應用程式，在內部會接聽連接埠 80。 您可以調整接聽其他連接埠的容器應用程式的範例。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

您可以使用 Azure Cloud Shell 或安裝在本機的 Azure CLI 來完成本文。 如果您想要在本機使用，建議使用 2.0.55 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="create-a-self-signed-certificate"></a>建立自我簽署憑證

若要設定 Nginx 做為 SSL 提供者，您需要 SSL 憑證。 本文說明如何建立和設定自我簽署的 SSL 憑證。 針對實際執行案例中，您應該從憑證授權單位取得憑證。

若要建立自我簽署的 SSL 憑證，請使用[OpenSSL](https://www.openssl.org/)工具可在 Azure Cloud Shell 和許多 Linux 散發套件，或在您的作業系統中使用類似的用戶端工具。

先在本機工作目錄中建立憑證要求 （.csr 檔案）：

```console
openssl req -new -newkey rsa:2048 -nodes -keyout ssl.key -out ssl.csr
```

遵循提示來加入的識別資訊。 一般名稱，請輸入與憑證相關聯的主機名稱。 出現提示時輸入密碼，請按 Enter 鍵而不需要輸入，以略過新增密碼。

執行下列命令來建立憑證要求中的自我簽署的憑證 （.crt 檔案）。 例如︰

```console
openssl x509 -req -days 365 -in ssl.csr -signkey ssl.key -out ssl.crt
```

您現在應該會看到目錄中的三個檔案： 將憑證要求 (`ssl.csr`)，私用金鑰 (`ssl.key`)，和自我簽署的憑證 (`ssl.crt`)。 您使用`ssl.key`和`ssl.crt`在稍後的步驟。

## <a name="configure-nginx-to-use-ssl"></a>將 Nginx 設定為使用 SSL

### <a name="create-nginx-configuration-file"></a>建立 Nginx 組態檔

在本節中，您可以建立使用 SSL 的 nginx 組態檔。 藉由將下列文字複製到新的檔案，名為啟動`nginx.conf`。 在 Azure Cloud Shell 中，您可以使用 Visual Studio Code，在您的工作目錄中建立檔案：

```console
code nginx.conf
```

在  `location`，請務必設定`proxy_pass`與應用程式的正確連接埠。 在此範例中，我們會設定連接埠 80`aci-helloworld`容器。

```console
# nginx Configuration File
# http://wiki.nginx.org/Configuration

# Run as a less privileged user for security reasons.
user nginx;

worker_processes auto;

events {
  worker_connections 1024;
}

pid        /var/run/nginx.pid;

http {

    #Redirect to https, using 307 instead of 301 to preserve post data

    server {
        listen [::]:443 ssl;
        listen 443 ssl;

        server_name localhost;

        # Protect against the BEAST attack by not using SSLv3 at all. If you need to support older browsers (IE6) you may need to add
        # SSLv3 to the list of protocols below.
        ssl_protocols              TLSv1 TLSv1.1 TLSv1.2;

        # Ciphers set to best allow protection from Beast, while providing forwarding secrecy, as defined by Mozilla - https://wiki.mozilla.org/Security/Server_Side_TLS#Nginx
        ssl_ciphers                ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-DSS-AES128-GCM-SHA256:kEDH+AESGCM:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA256:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA:DHE-RSA-AES256-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:ECDHE-RSA-RC4-SHA:ECDHE-ECDSA-RC4-SHA:AES128:AES256:RC4-SHA:HIGH:!aNULL:!eNULL:!EXPORT:!DES:!3DES:!MD5:!PSK;
        ssl_prefer_server_ciphers  on;

        # Optimize SSL by caching session parameters for 10 minutes. This cuts down on the number of expensive SSL handshakes.
        # The handshake is the most CPU-intensive operation, and by default it is re-negotiated on every new/parallel connection.
        # By enabling a cache (of type "shared between all Nginx workers"), we tell the client to re-use the already negotiated state.
        # Further optimization can be achieved by raising keepalive_timeout, but that shouldn't be done unless you serve primarily HTTPS.
        ssl_session_cache    shared:SSL:10m; # a 1mb cache can hold about 4000 sessions, so we can hold 40000 sessions
        ssl_session_timeout  24h;


        # Use a higher keepalive timeout to reduce the need for repeated handshakes
        keepalive_timeout 300; # up from 75 secs default

        # remember the certificate for a year and automatically connect to HTTPS
        add_header Strict-Transport-Security 'max-age=31536000; includeSubDomains';

        ssl_certificate      /etc/nginx/ssl.crt;
        ssl_certificate_key  /etc/nginx/ssl.key;

        location / {
            proxy_pass http://localhost:80; # TODO: replace port if app listens on port other than 80
            
            proxy_set_header Connection "";
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $remote_addr;
        }
    }
}
```

### <a name="base64-encode-secrets-and-configuration-file"></a>Base64 編碼的祕密和組態檔

Base64 編碼 Nginx 組態檔、 SSL 憑證，以及 SSL 金鑰。 您可以使用已編碼的內容來設定 Nginx 容器。

```console
cat nginx.conf | base64 -w 0 > base64-nginx.conf
cat ssl.crt | base64 -w 0 > base64-ssl.crt
cat ssl.key | base64 -w 0 > base64-ssl.key
```

## <a name="deploy-container-group"></a>部署容器群組

現在藉由指定容器中的組態中部署容器群組[YAML 檔案](container-instances-multi-container-yaml.md)。

### <a name="create-yaml-file"></a>建立 YAML 檔案

將下列 YAML 複製到新的檔案，名為`deploy-aci.yaml`。 在 Azure Cloud Shell 中，您可以使用 Visual Studio Code，在您的工作目錄中建立檔案：

```console
code deploy-aci.yaml
```

輸入的 base64 編碼內容檔案，其中的指示下`secret`。 在部署期間，這些檔案會新增至[祕密磁碟區](container-instances-volume-secret.md)群組中的容器。 在此範例中，祕密磁碟區會掛接到 Nginx 容器。

```YAML
api-version: 2018-10-01
location: westus
name: app-with-ssl
properties:
  containers:
  - name: nginx-with-ssl
    properties:
      image: nginx
      ports:
      - port: 443
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - name: nginx-config
        mountPath: /etc/nginx
  - name: my-app
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      ports:
      - port: 80
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  volumes:
  - secret:
      ssl.crt: <base64-ssl.crt>
      ssl.key: <base64-ssl.key>
      nginx.conf: <base64-nginx.conf>
    name: nginx-config
  ipAddress:
    ports:
    - port: 443
      protocol: TCP
    type: Public
  osType: Linux
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

### <a name="deploy-the-container-group"></a>部署容器群組

建立的資源群組[az 群組建立](/cli/azure/group#az-group-create)命令：

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

部署在容器群組[az 容器建立](/cli/azure/container#az-container-create)命令，傳遞做為引數的 YAML 檔案。

```azurecli
az container create --resource-group <myResourceGroup> --file deploy-aci.yaml
```

### <a name="view-deployment-state"></a>檢視部署狀態

若要檢視部署的狀態，請使用下列[az container show](/cli/azure/container#az-container-show)命令：

```azurecli
az container show --resource-group <myResourceGroup> --name app-with-ssl --output table
```

成功部署，輸出大致如下：

```console
Name          ResourceGroup    Status    Image                                                    IP:ports             Network    CPU/Memory       OsType    Location
------------  ---------------  --------  -------------------------------------------------------  -------------------  ---------  ---------------  --------  ----------
app-with-ssl  myresourcegroup  Running   mcr.microsoft.com/azuredocs/nginx, aci-helloworld        52.157.22.76:443     Public     1.0 core/1.5 gb  Linux     westus
```

## <a name="verify-ssl-connection"></a>確認 SSL 連線

若要檢視執行的應用程式，瀏覽至您的瀏覽器中其 IP 位址。 例如，在此範例中顯示的 IP 位址是`52.157.22.76`。 您必須使用`https://<IP-ADDRESS>`以查看執行中應用程式，由於 Nginx 伺服器組態。 嘗試連接與`http://<IP-ADDRESS>`失敗。

![顯示在 Azure 容器執行個體中執行之應用程式的瀏覽器螢幕擷取畫面](./media/container-instances-container-group-ssl/aci-app-ssl-browser.png)

> [!NOTE]
> 此範例會使用自我簽署的憑證，而不從憑證授權單位，因為瀏覽器透過 HTTPS 連線至站台時，就會顯示安全性警告。 這是預期的行為。
>

## <a name="next-steps"></a>後續步驟

本文說明如何設定 Nginx 容器，以便在容器群組中執行的 web 應用程式的 SSL 連線。 您可以調整本範例中的連接埠 80 以外的連接埠接聽的應用程式。 您也可以更新 Nginx 組態檔，以自動重新導向連接埠 80 (HTTP) 會使用 HTTPS 的伺服器連接。

雖然這篇文章中的側車使用 Nginx，您可以使用另一個 SSL 提供者這類[Caddy](https://caddyserver.com/)。

若要啟用 SSL 容器群組中的另一個方法是部署中的群組[Azure 虛擬網路](container-instances-vnet.md)具有[Azure 應用程式閘道](../application-gateway/overview.md)。 閘道可以設定為 SSL 端點。 請參閱範例[部署範本](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet)閘道上啟用 SSL 終止時，您可以採用。
