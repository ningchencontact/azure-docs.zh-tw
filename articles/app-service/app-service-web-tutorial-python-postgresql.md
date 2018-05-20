---
title: 在 Azure 中建置 Python 和 PostgreSQL Web 應用程式 | Microsoft Docs
description: 了解如何取得在 Azure 中運作的 Python 應用程式，並連線至 PostgreSQL 資料庫。
services: app-service\web
documentationcenter: python
author: berndverst
manager: erikre
ms.service: app-service-web
ms.workload: web
ms.devlang: python
ms.topic: tutorial
ms.date: 01/25/2018
ms.author: beverst
ms.custom: mvc
ms.openlocfilehash: 49ec67d06446d6c48e45aef90e2bd528a1b541a9
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/04/2018
---
# <a name="tutorial-build-a-python-and-postgresql-web-app-in-azure"></a>教學課程：在 Azure 中建置 Python 和 PostgreSQL Web 應用程式

> [!NOTE]
> 本文會將應用程式部署至 Windows 上的 App Service。 若要在 _Linux_ 上部署至 App Service，請參閱[在 Azure 中建置 Docker Python 和 PostgreSQL Web 應用程式](./containers/tutorial-docker-python-postgresql-app.md)。
>

[Azure App Service](app-service-web-overview.md) 可提供可高度擴充、自我修復的 Web 主控服務。 本教學課程會示範如何在 Azure 中建立基本的 Python Web 應用程式。 您會將此應用程式連線至 PostgreSQL 資料庫。 完成之後，您就會有在 App Service 上執行的 Python Flask 應用程式。

![Linux 上的 App Service 中的 Python Flask 應用程式](./media/app-service-web-tutorial-python-postgresql/docker-flask-in-azure.png)

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 在 Azure 中建立 PostgreSQL 資料庫
> * 將 Python 應用程式連線至 MySQL
> * 將應用程式部署至 Azure
> * 將資料模型更新並將應用程式重新部署
> * 在 Azure 入口網站中管理應用程式

您可以在 macOS 上依照本教學課程中的步驟進行。 Linux 和 Windows 指示在大部分情況下都相同，本教學課程對差異不加詳述。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先決條件

若要完成本教學課程：

1. [安裝 Git](https://git-scm.com/)
1. [安裝 Python](https://www.python.org/downloads/)
1. [安裝及執行 PostgreSQL](https://www.postgresql.org/download/)

## <a name="test-local-postgresql-installation-and-create-a-database"></a>測試本機 PostgreSQL 安裝並建立資料庫

開啟終端機視窗，然後執行 `psql` 來連線至本機 PostgreSQL 伺服器。

```bash
sudo -u postgres psql
```

如果連線成功，則您的 PostgreSQL 資料庫就已在執行中。 如果沒有，請確定您的本機 PostgresQL 資料庫已遵循[下載 - PostgresQL 核心散發](https://www.postgresql.org/download/)中的步驟來啟動。

建立名為 eventregistration 的資料庫，並且設定名為 manager、密碼為 supersecretpass 的個別資料庫使用者。

```bash
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```
輸入 `\q` 來結束 PostgreSQL 用戶端。 

<a name="step2"></a>

## <a name="create-local-python-flask-application"></a>建立本機 Python Flask 應用程式

在此步驟中，您要設定本機 Python Flask 專案。

### <a name="clone-the-sample-application"></a>複製範例應用程式

開啟終端機視窗，然後 `CD` 至工作目錄。

執行下列命令來複製範例存放庫，然後還原到初始應用程式的認可 (在 `modelChange` 之前)。

```bash
git clone https://github.com/Azure-Samples/flask-postgresql-app
cd flask-postgresql-app
git revert modelChange --no-edit
```

此範例存放庫包含 [Flask](http://flask.pocoo.org/) 應用程式。 

### <a name="run-the-application"></a>執行應用程式

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

![在本機執行的 Python Flask 應用程式](./media/app-service-web-tutorial-python-postgresql/local-app.png)

Flask 範例應用程式會將使用者資料儲存於資料庫中。 如果您成功註冊使用者，您的應用程式會將資料寫入本機 PostgreSQL 資料庫。

如需隨時停止 Flask 伺服器，請在終端機上輸入 Ctrl+C。 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-postgresql-in-azure"></a>在 Azure 中建立 PostgreSQL

在此步驟中，您要在 Azure 中建立 PostgreSQL 資料庫。 當您的應用程式部署至 Azure 時，它會使用此雲端資料庫。

### <a name="create-a-resource-group"></a>建立資源群組

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-postgresql-server"></a>建立 PostgreSQL 伺服器

使用 [`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az_postgres_server_create) 命令來建立 PostgreSQL 伺服器。

在下列命令中，使用唯一的伺服器名稱取代 \<postgresql_name> 預留位置，用使用者名稱取代 \<admin_username> 預留位置，並使用密碼取代 \<admin_password> 預留位置。 這個伺服器名稱會用來作為 PostgreSQL 端點 (`https://<postgresql_name>.postgres.database.azure.com`) 的一部分，所以在 Azure 的所有伺服器中必須是唯一的名稱。

```azurecli-interactive
az postgres server create --resource-group myResourceGroup --name <postgresql_name> --location "West Europe" --admin-user <admin_username> --admin-password <server_admin_password> --sku-name GP_Gen4_2
```

建立適用於 PostgreSQL 的 Azure 資料庫伺服器後，Azure CLI 會顯示類似下列範例的資訊：

```json
{
  "location": "westeurope",
  "name": "<postgresql_server_name>",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "additionalProperties": {},
    "capacity": 2,
    "family": "Gen4",
    "name": "GP_Gen4_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  ...   +  
  -  < Output has been truncated for readability >
}
```

### <a name="configure-server-firewall"></a>設定伺服器防火牆

執行下列 Azure CLI 命令，允許從所有 IP 位址存取資料庫。 當起始 IP 和結束 IP 都設為 0.0.0.0 時，防火牆只會為其他 Azure 資源開啟。 

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0 --name AllowAzureIPs
```

Azure CLI 確認防火牆規則建立，具有類似下列範例的輸出：

```json
{
  "additionalProperties": {},
  "endIpAddress": "255.255.255.255",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql_name>/firewallRules/AllowAllIPs",
  "name": "AllowAllIPs",
 "resourceGroup": "myResourceGroup",
  "startIpAddress": "0.0.0.0",
  "type": "Microsoft.DBforPostgreSQL/servers/firewallRules"
}
```

> [!TIP] 
> [僅使用您的應用程式所用的輸出 IP 位址](app-service-ip-addresses.md#find-outbound-ips)，讓您的防火牆規則更具限制性。
>

### <a name="create-a-production-database-and-user"></a>建立生產環境資料庫和使用者

建立資料庫使用者，並僅提供單一資料庫的存取權。 您會使用這些認證以避免將伺服器的完整存取權給予應用程式。

連線至資料庫 (系統會提示您輸入管理員密碼)。

```bash
psql -h <postgresql_name>.postgres.database.azure.com -U <admin_username>@<postgresql_name> postgres
```

從 PostgreSQL CLI 建立資料庫和使用者。

```bash
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```

輸入 `\q` 來結束 PostgreSQL 用戶端。

### <a name="test-app-locally-with-azure-postgresql"></a>在本機使用 Azure PostgreSQL 測試應用程式

現在回到複製的 Github 存放庫 app 資料夾，您只要更新資料庫環境變數，就可以執行 Python Flask 應用程式。

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

![在本機執行的 Python Flask 應用程式](./media/app-service-web-tutorial-python-postgresql/local-app.png)

## <a name="deploy-to-azure"></a>部署至 Azure

在此步驟中，您要將與 PostgreSQL 連線的 Python 應用程式部署至 Azure App Service。

您的 Git 存放庫已經包含要在 App Service 中執行 Flask Web 應用程式所需的下列檔案：

- `.deployment`：指定要執行的自訂部署指令碼。
- `deploy.cmd`：部署指令碼。 它是執行 `pip install` 的位置。
- `web.config`：指定要在 IIS 的 `httpPlatformHandler` 中執行的進入點指令碼。
- `run_waitress_server.py`：進入點指令碼。 這會在 [`waitress`](https://docs.pylonsproject.org/projects/waitress) 伺服器中啟動 Flask Web 應用程式。

### <a name="configure-a-deployment-user"></a>設定部署使用者

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>建立應用程式服務方案

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-no-h.md)]

<a name="create"></a>
### <a name="create-a-web-app"></a>建立 Web 應用程式

[!INCLUDE [Create web app no h](../../includes/app-service-web-create-web-app-no-h.md)] 

### <a name="install-python"></a>安裝 Python

在此步驟中，您會在 App Service 中使用[網站延伸模組](https://www.siteextensions.net/packages?q=Tags%3A%22python%22)安裝 Python 3.6.2。 您將使用您在[設定部署使用者](#configure-a-deployment-user)中設定的認證，對 REST 端點進行驗證。

在 Cloud Shell 中執行下一個命令，以取得 Python 3.6.2 套件資訊。 以您設定的部署使用者名稱取代 *\<deployment_user>*，並以您的應用程式的名稱取代 *\<app_name>*。 出現提示時，使用您所設定的部署密碼。

```bash
packageinfo=$(curl -u <deployment_user> https://<app_name>.scm.azurewebsites.net/api/extensionfeed/python362x86)
```

在 Cloud Shell 中執行下一個命令，以安裝 Python 套件。 以您設定的部署使用者名稱取代 *\<deployment_user>*，並以您的應用程式的名稱取代 *\<app_name>*。 出現提示時，使用您所設定的部署密碼。

```bash
curl -X PUT -u <deployment_user> -H "Content-Type: application/json" -d '$packageinfo' https://<app_name>.scm.azurewebsites.net/api/siteextensions/python362x86
```

在命令輸出中，您可以看到 Python 已安裝於路徑 `D:\home\python362x86\python.exe`。

### <a name="configure-database-settings"></a>設定資料庫設定

稍早在本教學課程中，您定義了環境變數來連線至 PostgreSQL 資料庫。

在 App Service 中，您可以使用 [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) 命令將環境變數設定為「應用程式設定」。

下列範例會指定資料庫連線詳細資料作為應用程式設定。 以您的應用程式名稱取代 *\<app_name>*，並以您的 PostgreSQL 伺服器名稱取代 *\<postgresql_name>*。

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="eventregistration"
```

### <a name="push-to-azure-from-git"></a>從 Git 推送至 Azure

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash
Counting objects: 5, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (5/5), done.
Writing objects: 100% (5/5), 489 bytes | 0 bytes/s, done.
Total 5 (delta 3), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '6c7c716eee'.
remote: Running custom deployment command...
remote: Running deployment command...
remote: Handling node.js deployment.
.
.
.
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
``` 

### <a name="browse-to-the-azure-web-app"></a>瀏覽至 Azure Web 應用程式 

使用 Web 瀏覽器，瀏覽至已部署的 Web 應用程式。 

```bash 
http://<app_name>.azurewebsites.net 
```

您會看到先前已註冊的來賓，儲存至上一個步驟中的 Azure 生產環境資料庫。

![在本機執行的 Python Flask 應用程式](./media/app-service-web-tutorial-python-postgresql/docker-app-deployed.png)

**恭喜！** 您正在 Azure App Service 中執行 Python Flask 應用程式。

## <a name="update-data-model-and-redeploy"></a>更新資料模型並重新部署

在此步驟中，您會更新 `Guest` 模式，將出席者的人數新增至每個事件註冊。

簽出 `modelChange` 認可所標記的檔案：

```bash
git checkout modelChange -- *
```

此版本已對檢視、控制器及模型進行必要變更。 它也會包含透過 alembic (`flask db migrate`) 產生的資料庫移轉。 您可以在 [GitHub 認可檢視](https://github.com/Azure-Samples/flask-postgresql-app/commit/139a53023688631c3cc2caefd70086f4722ecd7e)中查看所有檔案的變更。

### <a name="test-your-changes-locally"></a>本機測試您的變更

透過執行 Flask 伺服器，可執行下列命令在本機測試您的變更。

```bash
source venv/bin/activate
cd app
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

在瀏覽器中瀏覽至 http://localhost:5000 可檢視變更。 建立測試註冊。

![在本機執行的 Python Flask 應用程式](./media/app-service-web-tutorial-python-postgresql/local-app-v2.png)

### <a name="publish-changes-to-azure"></a>將變更發佈至 Azure

在本機終端機視窗中，在 Git 中認可所有變更，然後將程式碼變更推送至 Azure。

```bash
git add .
git commit -m "updated data model"
git push azure master
```

瀏覽至 Azure Web 應用程式，然後再次嘗試執行新功能。 建立另一個事件註冊。

```bash 
http://<app_name>.azurewebsites.net 
```

![Azure App Service 中的 Python Flask 應用程式](./media/app-service-web-tutorial-python-postgresql/docker-flask-in-azure.png)

## <a name="manage-your-azure-web-app"></a>管理您的 Azure Web 應用程式

請移至 [Azure 入口網站](https://portal.azure.com)，以查看您所建立的 Web 應用程式。

按一下左側功能表中的 [應用程式服務]，然後按一下 Azure Web 應用程式的名稱。

![入口網站瀏覽至 Azure Web 應用程式](./media/app-service-web-tutorial-python-postgresql/app-resource.png)

根據預設，入口網站會顯示 Web 應用程式的 [概觀] 分頁。 此頁面可讓您檢視應用程式的執行方式。 您也可以在這裡執行基本管理工作，像是瀏覽、停止、啟動、重新啟動及刪除。 分頁左側的索引標籤會顯示您可開啟的各種設定分頁。

![Azure 入口網站中的 App Service 頁面](./media/app-service-web-tutorial-python-postgresql/app-mgmt.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>後續步驟

前往下一個教學課程，了解如何將自訂的 DNS 名稱對應至 Web 應用程式。

> [!div class="nextstepaction"]
> [將現有的自訂 DNS 名稱對應至 Azure Web Apps](app-service-web-tutorial-custom-domain.md)
