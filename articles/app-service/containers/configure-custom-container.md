---
title: 設定自訂容器-Azure App Service |Microsoft Docs
description: 了解如何設定 Azure App Service 中運作的 Node.js 應用程式
services: app-service
documentationcenter: ''
author: cephalin
manager: jpconnock
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/28/2019
ms.author: cephalin
ms.openlocfilehash: 9bfd6c34d81e193fe31610f840474f1e4c91170d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66430921"
---
# <a name="configure-a-custom-linux-container-for-azure-app-service"></a>針對 Azure App Service 中設定自訂的 Linux 容器

這篇文章會示範如何設定 Azure App Service 上執行的自訂 Linux 容器。

本指南提供重要概念和 App Service 中的 Linux 應用程式的容器化的指示。 如果您從未使用過 Azure App Service，請遵循[自訂容器快速入門](quickstart-docker-go.md)並[教學課程](tutorial-custom-docker-image.md)第一次。 另外還有[多容器應用程式快速入門](quickstart-multi-container.md)並[教學課程](tutorial-multi-container-app.md)。

## <a name="configure-port-number"></a>設定連接埠號碼

在您的自訂映像的 web 伺服器可以使用 80 以外的連接埠。 藉由使用您的自訂容器使用的連接埠中告訴 Azure`WEBSITES_PORT`應用程式設定。 [本教學課程中的 Python 範例](https://github.com/Azure-Samples/docker-django-webapp-linux) GitHub 頁面說明您必須將 `WEBSITES_PORT` 設定為 _8000_。 您可以執行來設定[ `az webapp config appsettings set` ](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set)命令在 Cloud Shell 中。 例如︰

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

## <a name="configure-environment-variables"></a>設定環境變數

您的自訂容器可能會使用必須從外部提供的環境變數。 您可以將其傳遞執行[ `az webapp config appsettings set` ](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set)命令在 Cloud Shell 中。 例如︰

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WORDPRESS_DB_HOST="myownserver.mysql.database.azure.com"
```

這個方法適用於單一容器應用程式或多容器應用程式、 環境變數中指定的位置*docker compose.yml*檔案。

## <a name="use-persistent-shared-storage"></a>使用持續性的共用存放裝置

您可以使用 */home*目錄中您的應用程式檔案系統，以在重新啟動之間保存檔案，並加以共用跨執行個體。 `/home`應用程式中，可讓您存取永續性儲存體的容器應用程式。

永續性儲存體時已停用，然後將寫入`/home`目錄不保存應用程式重新啟動或跨越多個執行個體。 唯一的例外是`/home/LogFiles`用來儲存 Docker 和容器的記錄檔的目錄。 當啟用永續性儲存體時，所有寫入`/home`目錄會保存，並且可以存取的向外延展應用程式的所有執行個體。

根據預設，是永續性儲存體*停用*。 若要啟用或停用它，請設定`WEBSITES_ENABLE_APP_SERVICE_STORAGE`所執行的應用程式設定[ `az webapp config appsettings set` ](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set)命令在 Cloud Shell 中。 例如︰

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=true
```

> [!NOTE]
> 您也可以[設定您自己的永續性儲存體](how-to-serve-content-from-azure-storage.md)。

## <a name="enable-ssh"></a>啟用 SSH

SSH 可讓容器和用戶端之間進行安全通訊。 為了讓自訂容器，以支援 SSH，您必須將它新增至本身的 Dockerfile。

> [!TIP]
> 所有內建的 Linux 容器已新增 SSH 指示，在其映像儲存機制。 您可以瀏覽 使用下列指示[Node.js 10.14 存放庫](https://github.com/Azure-App-Service/node/blob/master/10.14)若要查看如何啟用那里。

- 使用[執行](https://docs.docker.com/engine/reference/builder/#run)指示來安裝 SSH 伺服器，並設定到根帳號密碼`"Docker!"`。 例如，若為基礎的映像[Alpine Linux](https://hub.docker.com/_/alpine)，您需要下列命令：

    ```Dockerfile
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 
    ```

    此設定不允許容器的外部連線。 SSH 是僅透過`https://<app-name>.scm.azurewebsites.net`和使用發佈認證通過驗證。

- 新增[這個 sshd_config 檔案](https://github.com/Azure-App-Service/node/blob/master/10.14/sshd_config)至您的映像儲存機制，並使用[複製](https://docs.docker.com/engine/reference/builder/#copy)將檔案複製到指令 */etc/ssh/* 目錄。 如需詳細資訊*sshd_config*檔，請參閱[OpenBSD 文件](https://man.openbsd.org/sshd_config)。

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > sshd_config  檔案必須包含下列項目︰
    > - `Ciphers` 必須在此清單中包含至少一個項目：`aes128-cbc,3des-cbc,aes256-cbc`。
    > - `MACs` 必須在此清單中包含至少一個項目：`hmac-sha1,hmac-sha1-96`。

- 使用[公開](https://docs.docker.com/engine/reference/builder/#expose)指令，以便在容器中開啟連接埠 2222年。 雖然已知根密碼，連接埠 2222年。 無法從網際網路存取 它是可存取只有私人虛擬網路之橋接網路內的容器。

    ```Dockerfile
    EXPOSE 80 2222
    ```

- 在您的容器的啟動指令碼，啟動 SSH 伺服器。

    ```bash
    /usr/sbin/sshd
    ```

    如需範例，請參閱如何預設[Node.js 10.14 容器](https://github.com/Azure-App-Service/node/blob/master/10.14/startup/init_container.sh)啟動 SSH 伺服器。

## <a name="access-diagnostic-logs"></a>存取診斷記錄

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="configure-multi-container-apps"></a>設定多容器應用程式

- [在 Docker Compose 使用永續性儲存體](#use-persistent-storage-in-docker-compose)
- [預覽限制](#preview-limitations)
- [Docker Compose 選項](#docker-compose-options)

### <a name="use-persistent-storage-in-docker-compose"></a>在 Docker Compose 使用永續性儲存體

WordPress 這類的多容器應用程式需要永續性儲存體，才能正確運作。 若要啟用它，您的 Docker Compose 設定必須指向儲存體位置*外部*您的容器。 在您的容器內的儲存體位置，不會保存超過應用程式重新啟動的變更。

藉由設定啟用永續性儲存體`WEBSITES_ENABLE_APP_SERVICE_STORAGE`應用程式設定、 使用[az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) Cloud Shell 中的命令。

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

在您*docker compose.yml*檔案中，對應`volumes`選項設定為`${WEBAPP_STORAGE_HOME}`。 

`WEBAPP_STORAGE_HOME` 是 App Service 中與您應用程式的永續性儲存體相對應的環境變數。 例如︰

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
  - ${WEBAPP_STORAGE_HOME}/phpmyadmin:/var/www/phpmyadmin
  - ${WEBAPP_STORAGE_HOME}/LogFiles:/var/log
```

### <a name="preview-limitations"></a>預覽限制

多容器目前為預覽狀態。 不支援下列的 App Service 平台功能：

- 驗證 / 授權
- 受控識別

### <a name="docker-compose-options"></a>Docker Compose 選項

下列清單顯示支援與不支援 Docker Compose 的組態選項：

#### <a name="supported-options"></a>支援的選項

- 命令
- entrypoint
- Environment
- 映像
- 連接埠
- restart
- 服務
- 磁碟區

#### <a name="unsupported-options"></a>不支援的選項

- build (不允許)
- depends_on (已忽略)
- networks (已忽略)
- secrets (已忽略)
- 80 和 8080 以外的連接埠 (已忽略)

> [!NOTE]
> 未明確標註的其他任何選項會忽略處於公開預覽狀態。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [教學課程：從私人容器存放庫部署](tutorial-custom-docker-image.md)

> [!div class="nextstepaction"]
> [教學課程：多容器 WordPress 應用程式](tutorial-multi-container-app.md)
