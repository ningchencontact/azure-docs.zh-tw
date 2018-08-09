---
title: 在 Azure App Service 中建置 Python 和 PostgreSQL Web 應用程式 | Microsoft Docs
description: 了解如何在 Azure 中連線至 PostgreSQL 資料庫，以執行資料驅動的 Python 應用程式。
services: app-service\web
documentationcenter: python
author: berndverst
manager: jeconnoc
ms.service: app-service-web
ms.workload: web
ms.devlang: python
ms.topic: tutorial
ms.date: 07/13/2018
ms.author: beverst;cephalin
ms.custom: mvc
ms.openlocfilehash: ce84498ab89891bd7b96cfcc6b0c7ac029c93cbd
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/02/2018
ms.locfileid: "39423074"
---
# <a name="build-a-docker-python-and-postgresql-web-app-in-azure"></a>在 Azure 中建置 Docker Python 和 PostgreSQL Web 應用程式

用於容器的 Web 應用程式提供可高度擴充、自我修復的 Web 主機服務。 本教學課程說明如何以 PostgreSQL 作為資料庫後端，建立資料驅動 Python Web 應用程式。 完成之後，您在 [Linux 上的 App Service](app-service-linux-intro.md) 上就會有 Docker 容器內執行的 Python Flask 應用程式。

![Linux 上的 App Service 中的 Docker Python Flask 應用程式](./media/tutorial-docker-python-postgresql-app/docker-flask-in-azure.png)

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 在 Azure 中建立 PostgreSQL 資料庫
> * 將 Python 應用程式連線至 PostgreSQL
> * 將應用程式部署至 Azure
> * 將資料模型更新並將應用程式重新部署
> * 在 Azure 入口網站中管理應用程式

您可以在 macOS 上依照本文中的步驟進行。 Linux 和 Windows 指示在大部分情況下都相同，本教學課程對差異不加詳述。
 
[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>必要條件

若要完成本教學課程：

1. [安裝 Git](https://git-scm.com/)
1. [安裝 Python](https://www.python.org/downloads/)
1. [安裝及執行 PostgreSQL](https://www.postgresql.org/download/)
1. [安裝 Docker Community 版本](https://www.docker.com/community-edition)

## <a name="test-local-postgresql-installation-and-create-a-database"></a>測試本機 PostgreSQL 安裝並建立資料庫

在本機終端機視窗中，執行 `psql` 以連線至本機 PostgreSQL 伺服器。

```bash
sudo -u postgres psql
```

如果連線成功，則您的 PostgreSQL 資料庫就已在執行中。 如果沒有，請確定您的本機 PostgresQL 資料庫已遵循[下載 - PostgresQL 核心散發](https://www.postgresql.org/download/)中的步驟來啟動。

建立名為 eventregistration 的資料庫，並且設定名為 manager、密碼為 supersecretpass 的個別資料庫使用者。

```sql
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```

輸入 `\q` 來結束 PostgreSQL 用戶端。 

<a name="step2"></a>

## <a name="create-local-python-app"></a>建立本機 Python 應用程式

在此步驟中，您要設定本機 Python Flask 專案。

### <a name="clone-the-sample-app"></a>複製範例應用程式

開啟終端機視窗，然後 `CD` 至工作目錄。

執行下列命令來複製範例存放庫，然後前往 0.1-initialapp 版本。

```bash
git clone https://github.com/Azure-Samples/docker-flask-postgres.git
cd docker-flask-postgres
git checkout tags/0.1-initialapp
```

此範例存放庫包含 [Flask](http://flask.pocoo.org/) 應用程式。 

### <a name="run-the-app-locally"></a>在本機執行應用程式

安裝必要的封裝，然後啟動應用程式。

```bash
pip install virtualenv
virtualenv venv
source venv/bin/activate
pip install -r requirements.txt
cd app
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

當應用程式完全載入時，您會看到類似下列的訊息：

```bash
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 791cd7d80402, empty message
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

在瀏覽器中，瀏覽至 `http://localhost:5000` 。 按一下 [註冊!] 並且建立測試使用者。

![在本機執行的 Python Flask 應用程式](./media/tutorial-docker-python-postgresql-app/local-app.png)

Flask 範例應用程式會將使用者資料儲存於資料庫中。 如果您成功註冊使用者，您的應用程式會將資料寫入本機 PostgreSQL 資料庫。

如需隨時停止 Flask 伺服器，請在終端機上輸入 Ctrl+C。 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-production-postgresql-database"></a>建立生產環境 PostgreSQL 資料庫

在此步驟中，您要在 Azure 中建立 PostgreSQL 資料庫。 當您的應用程式部署至 Azure 時，它會使用此雲端資料庫。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-a-resource-group"></a>建立資源群組

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-an-azure-database-for-postgresql-server"></a>建立適用於 PostgreSQL 的 Azure 資料庫伺服器

在 Cloud Shell 中使用 [`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create) 命令建立 PostgreSQL 伺服器。

在下列範例命令中，請將 *\<postgresql_name>* 取代為唯一的伺服器名稱，並將 *\<admin_username>* 和 *\<admin_password>* 取代為所需的使用者認證。 這個伺服器名稱會用來作為 PostgreSQL 端點 (`https://<postgresql_name>.postgres.database.azure.com`) 的一部分，所以在 Azure 的所有伺服器中必須是唯一的名稱。 使用者認證是用於資料庫管理使用者帳戶。 

```azurecli-interactive
az postgres server create --resource-group myResourceGroup --name <postgresql_name> --location "West Europe" --admin-user <admin_username> --admin-password <admin_password> --sku-name GP_Gen4_2
```

建立適用於 PostgreSQL 的 Azure 資料庫伺服器後，Azure CLI 會顯示類似下列範例的資訊：

```json
{
  "administratorLogin": "<admin_username>",
  "fullyQualifiedDomainName": "<postgresql_name>.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql_name>",
  "location": "westus",
  "name": "<postgresql_name>",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capacity": 100,
    "family": null,
    "name": "PGSQLS3M100",
    "size": null,
    "tier": "Basic"
  },
  "sslEnforcement": null,
  "storageMb": 2048,
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": null
}
```

### <a name="create-a-firewall-rule-for-the-postgresql-server"></a>建立 PostgreSQL 伺服器的防火牆規則

在 Cloud Shell 中執行下列 Azure CLI 命令，以允許從所有 IP 位址存取資料庫。 當起始 IP 和結束 IP 都設為 `0.0.0.0` 時，防火牆只會為其他 Azure 資源開啟。 

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=0.0.0.0 --end-ip-address=0.0.0.0 --name AllowAzureIPs
```

> [!TIP] 
> [僅使用您的應用程式所用的輸出 IP 位址](../app-service-ip-addresses.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips)，讓您的防火牆規則更具限制性。
>

在 Cloud Shell 中，將 *\<you_ip_address>* 取代為[您的本機 IPv4 IP 位址](https://whatismyipaddress.com/)並再次執行命令，以允許從您的本機電腦存取資料庫。 

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=<you_ip_address> --end-ip-address=<you_ip_address> --name AllowLocalClient
```

## <a name="connect-python-app-to-production-database"></a>將 Python 應用程式連線至生產資料庫

在此步驟中，您會將 Flask 範例應用程式連線至您所建立的「適用於 PostgreSQL 的 Azure 資料庫」伺服器。

### <a name="create-empty-database-and-user-access"></a>建立空的資料庫和使用者存取

在 Cloud Shell 中，執行 `psql` 以連線至資料庫。 在出現管理員密碼的提示時，請使用您在[建立適用於 PostgreSQL 的 Azure 資料庫伺服器](#create-an-azure-database-for-postgresql-server)中指定的相同密碼。

```bash
psql -h <postgresql_name>.postgres.database.azure.com -U <my_admin_username>@<postgresql_name> postgres
```

從 PostgreSQL CLI 建立資料庫和使用者。

```bash
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```

輸入 `\q` 來結束 PostgreSQL 用戶端。

### <a name="test-app-connectivity-to-production-database"></a>測試應用程式與生產資料庫的連線

回到本機終端機視窗並執行下列命令，以執行 Flask 資料庫移轉和 Flask 伺服器。

```bash
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

當應用程式完全載入時，您會看到類似下列的訊息：

```bash
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 791cd7d80402, empty message
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

在瀏覽器中，瀏覽至 http://localhost:5000 。 按一下 [註冊!] 並且建立測試註冊。 現在您要將資料寫入 Azure 中的資料庫。

![在本機執行的 Python Flask 應用程式](./media/tutorial-docker-python-postgresql-app/local-app.png)

## <a name="upload-app-to-a-container-registry"></a>將應用程式上傳至容器登錄

在此步驟中，您會建立 Docker 映像，並將其上傳至 Azure Container Registry。 您也可以使用廣受使用的登錄，例如 Docker Hub。

### <a name="build-the-docker-image-and-test-it"></a>建置 Docker 映像，並加以測試

在本機終端機視窗中，建置 Docker 映像。

```bash
cd ..
docker build -t flask-postgresql-sample .
```

Docker 會顯示它已成功建立容器的確認訊息。

```bash
Successfully built 7548f983a36b
```

在存放庫根路徑中，新增名為 db.env 的環境變數檔案，然後在其中新增下列資料庫環境變數。 應用程式會連線到「適用於 PostgreSQL 的 Azure 資料庫」生產資料庫。

```text
DBHOST=<postgresql_name>.postgres.database.azure.com
DBUSER=manager@<postgresql_name>
DBNAME=eventregistration
DBPASS=supersecretpass
```

在 Docker 容器中的本機位置執行映像。 下列命令會指定環境變數檔案，並將預設 Flask 連接埠 5000 對應至本機連接埠 5000。

```bash
docker run -it --env-file db.env -p 5000:5000 flask-postgresql-sample
```

輸出類似您稍早所見的範例。 不過，不再需要執行初始資料庫移轉，因此會予以略過。

```bash
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
 * Serving Flask app "app"
 * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
```

資料庫已包含您先前建立的註冊。

![以 Docker 容器為基礎在本機執行的 Python Flask 應用程式](./media/tutorial-docker-python-postgresql-app/local-docker.png)

現在您已確認容器可在本機運作，請刪除 _db.env_。 在 Azure App Service 中，您將使用應用程式設定來定義環境變數。  

### <a name="create-an-azure-container-registry"></a>建立 Azure Container Registry

在 Cloud Shell 中，使用下列命令在 Azure Container Registry 中建立登錄。 請將 *\<registry_name>* 取代為唯一的登錄名稱。

```azurecli-interactive
az acr create --name <registry_name> --resource-group myResourceGroup --location "West US" --sku Basic
```

### <a name="retrieve-registry-credentials"></a>擷取登錄認證

在 Cloud Shell 中執行下列命令，以擷取登錄認證。 必須要有這些認證，才能推送和提取映像。

```azurecli-interactive
az acr update --name <registry_name> --admin-enabled true
az acr credential show -n <registry_name>
```

在輸出中，您會看到兩個密碼。 請記下使用者名稱 (預設為登錄名稱) 和第一個密碼。

```json
{
  "passwords": [
    {
      "name": "password",
      "value": "<registry_password>"
    },
    {
      "name": "password2",
      "value": "<registry_password2>"
    }
  ],
  "username": "<registry_name>"
}
```

### <a name="upload-docker-image-to-registry"></a>將 Docker 映像上傳至登錄

在本機終端機視窗中，使用 `docker` 登入您新的登錄。 出現提示時，提供您所擷取的密碼。

```bash
docker login <registry_name>.azurecr.io -u <registry_name>
```

將您的 Docker 映像推送至登錄。

```bash
docker tag flask-postgresql-sample <registry_name>.azurecr.io/flask-postgresql-sample
docker push <registry_name>.azurecr.io/flask-postgresql-sample
```

## <a name="create-web-app-with-uploaded-image"></a>使用已上傳的映像建立 Web 應用程式

在此步驟中，您會在 Azure App Service 中建立應用程式，並將其設定為使用在 Azure Container Registry 中上傳的 Docker 映像。

### <a name="create-an-app-service-plan"></a>建立應用程式服務方案

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>建立 Web 應用程式

在 Cloud Shell 中使用 [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) 命令，在 *myAppServicePlan* App Service 方案中建立 Web 應用程式。

在下列命令中，將 \<app_name> 預留位置取代為唯一的應用程式名稱。 這個名稱是 Web 應用程式預設 URL 的一部分，因此，這個名稱在 Azure App Service 的所有應用程式中必須是唯一的。

```azurecli-interactive
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan --deployment-container-image-name "<registry_name>.azurecr.io/flask-postgresql-sample"
```

建立 Web 應用程式後，Azure CLI 會顯示類似下列範例的資訊：

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  ...
  < Output has been truncated for readability >
}
```

### <a name="configure-environment-variables"></a>設定環境變數

稍早在本教學課程中，您定義了環境變數來連線至 PostgreSQL 資料庫。

在 App Service 中，您可以使用 [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) 命令將環境變數設定為「應用程式設定」。

下列範例會指定資料庫連線詳細資料作為應用程式設定。 它也會對容器連接埠 5000 使用 *WEBSITES_PORT* 變數，以允許該容器在連接埠 80 上接收 HTTP 流量。

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="eventregistration" WEBSITES_PORT=5000
```

### <a name="configure-custom-container-deployment"></a>設定自訂容器部署

即使您已指定容器映像名稱，您仍然需要指定自訂的登錄 URL 和使用者認證。 在 Cloud Shell 中，執行 [az webapp config container set](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set) 命令。

```azurecli-interactive
az webapp config container set --resource-group myResourceGroup --name <app_name> --docker-registry-server-user "<registry_name>" --docker-registry-server-password "<registry_password>" --docker-registry-server-url "https://<registry_name>.azurecr.io"
```

在 Cloud Shell 中重新啟動應用程式。 重新啟動可確保套用所有設定，以及從登錄提取最新容器。

```azurecli-interactive
az webapp restart --resource-group myResourceGroup --name <app_name>
```

### <a name="browse-to-the-azure-web-app"></a>瀏覽至 Azure Web 應用程式 

瀏覽至已部署的 Web 應用程式。 

```bash 
http://<app_name>.azurewebsites.net 
```

> [!NOTE]
> Web 應用程式需要一些時間才能啟動，因為在第一次要求應用程式時必須下載並執行容器。 如果您在長時間後先看到錯誤，請重新整理頁面。

您會看到先前已註冊的來賓，儲存至上一個步驟中的 Azure 生產環境資料庫。

![以 Docker 容器為基礎在本機執行的 Python Flask 應用程式](./media/tutorial-docker-python-postgresql-app/docker-app-deployed.png)

**恭喜！** 您正在適用於容器的 Web 應用程式中執行 Python 應用程式。

## <a name="update-data-model-and-redeploy"></a>更新資料模型並重新部署

在此步驟中，您會更新 `Guest` 模式，將出席者的人數新增至每個事件註冊。

請在本機終端機視窗中，使用下列 git 命令查看 0.2-migration 版本：

```bash
git checkout tags/0.2-migration
```

此版本已對模型、檢視和控制器進行必要變更。 它也會包含透過 alembic (`flask db migrate`) 產生的資料庫移轉。 您可以看到透過下列 git 命令所進行的所有變更：

```bash
git diff 0.1-initialapp 0.2-migration
```

### <a name="test-your-changes-locally"></a>本機測試您的變更

在本機終端機視窗中執行下列命令，以藉由執行 Flask 伺服器在本機測試您的變更。

```bash
source venv/bin/activate
cd app
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

在瀏覽器中瀏覽至 http://localhost:5000 可檢視變更。 建立測試註冊。

![以 Docker 容器為基礎在本機執行的 Python Flask 應用程式](./media/tutorial-docker-python-postgresql-app/local-app-v2.png)

### <a name="publish-changes-to-azure"></a>將變更發佈至 Azure

在本機終端機視窗中建置新的 Docker 映像，並將其推送至您的登錄。

```bash
cd ..
docker build -t flask-postgresql-sample .
docker tag flask-postgresql-sample <registry_name>.azurecr.io/flask-postgresql-sample
docker push <registry_name>.azurecr.io/flask-postgresql-sample
```

在 Cloud Shell 中重新啟動應用程式，以確定已從登錄中提取最新的容器。

```azurecli-interactive
az webapp restart --resource-group myResourceGroup --name <app_name>
```

瀏覽至 Azure Web 應用程式，然後再次嘗試執行新功能。 建立另一個事件註冊。

```bash 
http://<app_name>.azurewebsites.net 
```

![Azure App Service 中的 Docker Python Flask 應用程式](./media/tutorial-docker-python-postgresql-app/docker-flask-in-azure.png)

## <a name="manage-your-azure-web-app"></a>管理您的 Azure Web 應用程式

請移至 [Azure 入口網站](https://portal.azure.com)，以查看您所建立的 Web 應用程式。

按一下左側功能表中的 [應用程式服務]，然後按一下 Azure Web 應用程式的名稱。

![入口網站瀏覽至 Azure Web 應用程式](./media/tutorial-docker-python-postgresql-app/app-resource.png)

根據預設，入口網站會顯示 Web 應用程式的 [概觀] 分頁。 此頁面可讓您檢視應用程式的執行方式。 您也可以在這裡執行基本管理工作，像是瀏覽、停止、啟動、重新啟動及刪除。 分頁左側的索引標籤會顯示您可開啟的各種設定分頁。

![Azure 入口網站中的 App Service 頁面](./media/tutorial-docker-python-postgresql-app/app-mgmt.png)

## <a name="next-steps"></a>後續步驟

前往下一個教學課程，了解如何將自訂的 DNS 名稱對應至 Web 應用程式。

> [!div class="nextstepaction"]
> [將現有的自訂 DNS 名稱對應至 Azure Web Apps](../app-service-web-tutorial-custom-domain.md)
