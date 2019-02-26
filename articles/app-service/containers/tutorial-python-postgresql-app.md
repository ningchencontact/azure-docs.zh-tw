---
title: 在 Linux 上建置搭配 PostgreSQL 的 Python 應用程式 - Azure App Service | Microsoft Docs
description: 了解如何在 Azure 中連線至 PostgreSQL 資料庫，以執行資料驅動的 Python 應用程式。
services: app-service\web
documentationcenter: python
author: cephalin
manager: jeconnoc
ms.service: app-service-web
ms.workload: web
ms.devlang: python
ms.topic: tutorial
ms.date: 11/29/2018
ms.author: beverst;cephalin
ms.custom: seodec18
ms.openlocfilehash: 11918ecf741ef31354cb281d3e439585506683f4
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/19/2019
ms.locfileid: "56417222"
---
# <a name="build-a-python-and-postgresql-app-in-azure-app-service"></a>在 Azure App Service 中建置 Python 和 PostgreSQL 應用程式

[Linux 上的 App Service](app-service-linux-intro.md) 提供可高度擴充、自我修復的 Web 主機服務。 本教學課程示範如何使用 PostgreSQL 作為資料庫後端，來建立資料驅動的 Python 應用程式。 完成之後，您就會有在 Linux 上的 App Service 中執行的 Django 應用程式。

![Linux 上的 App Service 中的 Python Django 應用程式](./media/tutorial-python-postgresql-app/django-admin-azure.png)

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 在 Azure 中建立 PostgreSQL 資料庫
> * 將 Python 應用程式連線至 PostgreSQL
> * 將應用程式部署至 Azure
> * 檢視診斷記錄
> * 在 Azure 入口網站中管理應用程式

> [!NOTE]
> 在建立適用於 PostgreSQL 的 Azure 資料庫之前，請先確認[可在您的區域中產生的計算](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores)。

您可以在 macOS 上依照本文中的步驟進行。 Linux 和 Windows 指示在大部分情況下都相同，本教學課程對差異不加詳述。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>必要條件

若要完成本教學課程：

1. [安裝 Git](https://git-scm.com/)
2. [安裝 Python](https://www.python.org/downloads/)
3. [安裝及執行 PostgreSQL](https://www.postgresql.org/download/)

## <a name="test-local-postgresql-installation-and-create-a-database"></a>測試本機 PostgreSQL 安裝並建立資料庫

在本機終端機視窗中，執行 `psql` 以連線至本機 PostgreSQL 伺服器。

```bash
sudo -u postgres psql postgres
```

如果您收到類似於 `unknown user: postgres` 的錯誤訊息，請使用您的登入使用者名稱來設定 PostgreSQL 安裝。 改為嘗試下列命令。

```bash
psql postgres
```

如果連線成功，則您的 PostgreSQL 資料庫就已在執行中。 如果沒有，請確定本機 PostgresQL 資料庫已遵循 [Downloads - PostgreSQL Core Distribution](https://www.postgresql.org/download/) (下載 - PostgresQL 核心散發) 中針對您作業系統的指示來啟動。

建立名為 pollsdb 的資料庫，並且設定名為 manager、密碼為 supersecretpass 的個別資料庫使用者。

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

輸入 `\q` 來結束 PostgreSQL 用戶端。

<a name="step2"></a>

## <a name="create-local-python-app"></a>建立本機 Python 應用程式

在此步驟中，您要設定本機 Python Django 專案。

### <a name="clone-the-sample-app"></a>複製範例應用程式

開啟終端機視窗，然後 `CD` 至工作目錄。

執行下列命令來複製範例存放庫。

```bash
git clone https://github.com/Azure-Samples/djangoapp.git
cd djangoapp
```

此範例存放庫包含 [Django](https://www.djangoproject.com/) 應用程式。 這個應用程式就是您藉由遵循 [Django 文件中的入門教學課程](https://docs.djangoproject.com/en/2.1/intro/tutorial01/)所獲得的同一個資料驅動應用程式。 本教學課程不會教您 Django，而是會說明如何對 App Service 部署和執行 Django 應用程式 (或另一個資料驅動的 Python 應用程式)。

### <a name="configure-environment"></a>設定環境

建立 Python 虛擬環境，並使用指令碼來設定資料庫連線設定。

```bash
# Bash
python3 -m venv venv
source venv/bin/activate
source ./env.sh

# PowerShell
py -3 -m venv venv
venv\scripts\activate
.\env.ps1
```

env.sh 和 env.ps1 中所定義的環境變數，會用於 azuresite/settings.py 以便定義資料庫設定。

### <a name="run-app-locally"></a>在本機執行應用程式

安裝必要的套件、[執行 Django 移轉](https://docs.djangoproject.com/en/2.1/topics/migrations/)，然後[建立管理使用者](https://docs.djangoproject.com/en/2.1/intro/tutorial02/#creating-an-admin-user)。

```bash
pip install -r requirements.txt
python manage.py migrate
python manage.py createsuperuser
```

建立了管理使用者之後，請執行 Django 伺服器。

```bash
python manage.py runserver
```

當應用程式完全載入時，您會看到類似下列的訊息：

```bash
Performing system checks...

System check identified no issues (0 silenced).
October 26, 2018 - 10:54:59
Django version 2.1.2, using settings 'azuresite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

在瀏覽器中，瀏覽至 `http://localhost:8000` 。 您應該會看見訊息 `No polls are available.`。 

瀏覽至 `http://localhost:8000/admin`，然後使用您在上一個步驟中建立的管理使用者來登入。 按一下 [問題] 旁的 [新增]，然後建立具有一些選擇的投票問題。

![在本機執行的 Python Django 應用程式](./media/tutorial-python-postgresql-app/django-admin-local.png)

再次瀏覽至 `http://localhost:8000`，然後查看所顯示的投票問題。

Django 範例應用程式會將使用者資料儲存於資料庫中。 如果您成功新增投票問題，您的應用程式會將資料寫入本機 PostgreSQL 資料庫。

如需隨時停止 Django 伺服器，請在終端機上輸入 Ctrl+C。

## <a name="create-a-production-postgresql-database"></a>建立生產環境 PostgreSQL 資料庫

在此步驟中，您要在 Azure 中建立 PostgreSQL 資料庫。 當您的應用程式部署至 Azure 時，它會使用此雲端資料庫。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-a-resource-group"></a>建立資源群組

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)]

### <a name="create-an-azure-database-for-postgresql-server"></a>建立適用於 PostgreSQL 的 Azure 資料庫伺服器

在 Cloud Shell 中使用 [`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create) 命令建立 PostgreSQL 伺服器。

在下列範例命令中，請將 *\<postgresql_name>* 取代為唯一的伺服器名稱，並將 *\<admin_username>* 和 *\<admin_password>* 取代為所需的使用者認證。 使用者認證是用於資料庫管理員帳戶。 這個伺服器名稱會用來作為 PostgreSQL 端點 (`https://<postgresql_name>.postgres.database.azure.com`) 的一部分，所以在 Azure 的所有伺服器中必須是唯一的名稱。

```azurecli-interactive
az postgres server create --resource-group myResourceGroup --name <postgresql_name> --location "West Europe" --admin-user <admin_username> --admin-password <admin_password> --sku-name B_Gen4_1
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
    "capacity": 1,
    "family": "Gen4",
    "name": "B_Gen4_1",
    "size": null,
    "tier": "Basic"
  },
  < JSON data removed for brevity. >
}
```

> [!NOTE]
> 請記住 \<管理員使用者名稱> 和 \<管理員密碼> 以便稍後使用。 您需要這些資訊，才能登入 Postgre 伺服器及其資料庫。


### <a name="create-firewall-rules-for-the-postgresql-server"></a>建立 PostgreSQL 伺服器的防火牆規則

在 Cloud Shell 中執行下列 Azure CLI 命令，以允許從 Azure 資源存取資料庫。

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=0.0.0.0 --end-ip-address=0.0.0.0 --name AllowAllAzureIPs
```

> [!NOTE]
> 此設定允許來自 Azure 網路內所有 IP 的網路連線。 針對生產用途，請嘗試[僅使用您應用程式所用的輸出 IP 位址](../overview-inbound-outbound-ips.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips)，來設定可能最嚴格的防火牆規則。

在 Cloud Shell 中，將 *\<您的 IP 位址>* 取代為[您的本機 IPv4 IP 位址](http://www.whatsmyip.org/)並再次執行命令，以允許從您的本機電腦進行存取。

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=<your_ip_address> --end-ip-address=<your_ip_address> --name AllowLocalClient
```

## <a name="connect-python-app-to-production-database"></a>將 Python 應用程式連線至生產資料庫

在此步驟中，您會將 Django 範例應用程式連線至您所建立的「適用於 PostgreSQL 的 Azure 資料庫」伺服器。

### <a name="create-empty-database-and-user-access"></a>建立空的資料庫和使用者存取

在 Cloud Shell 中，執行下列命令以連線至資料庫。 在出現管理員密碼的提示時，請使用您在[建立適用於 PostgreSQL 的 Azure 資料庫伺服器](#create-an-azure-database-for-postgresql-server)中指定的相同密碼。

```bash
psql -h <postgresql_name>.postgres.database.azure.com -U <my_admin_username>@<postgresql_name> postgres
```

如同您的本機 Postgres 伺服器，在 Azure Postgres 伺服器中建立資料庫和使用者。

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

輸入 `\q` 來結束 PostgreSQL 用戶端。

> [!NOTE]
> 最佳做法是建立具有特定應用程式限制權限的資料庫使用者，而不是使用管理使用者。 在此範例中，`manager` 使用者「只」具有 `pollsdb` 資料庫的完整權限。

### <a name="test-app-connectivity-to-production-database"></a>測試應用程式與生產資料庫的連線

在本機終端機視窗中，變更資料庫環境變數 (您已於稍早執行 env.sh 或 env.ps1 加以設定)：

```bash
# Bash
export DBHOST="<postgresql_name>.postgres.database.azure.com"
export DBUSER="manager@<postgresql_name>"
export DBNAME="pollsdb"
export DBPASS="supersecretpass"

# PowerShell
$Env:DBHOST = "<postgresql_name>.postgres.database.azure.com"
$Env:DBUSER = "manager@<postgresql_name>"
$Env:DBNAME = "pollsdb"
$Env:DBPASS = "supersecretpass"
```

將 Django 移轉至 Azure 資料庫，並建立管理使用者。

```bash
python manage.py migrate
python manage.py createsuperuser
```

建立了管理使用者之後，請執行 Django 伺服器。

```bash
python manage.py runserver
```

再次瀏覽至 `http://localhost:8000`。 您應該會再次看見訊息 `No polls are available.`。 

瀏覽至 `http://localhost:8000/admin`，然後使用您建立的管理使用者登入，並建立如同前面的投票問題。

![在本機執行的 Python Django 應用程式](./media/tutorial-python-postgresql-app/django-admin-local.png)

再次瀏覽至 `http://localhost:8000`，然後查看所顯示的投票問題。 應用程式現在會將資料寫入 Azure 中的資料庫。

## <a name="deploy-to-azure"></a>部署至 Azure

在此步驟中，您會將與 Postgres 連線的 Python 應用程式部署至 Azure App Service。

### <a name="configure-repository"></a>設定存放庫

Django 會驗證連入要求中的 `HTTP_HOST` 標題。 若要讓 Django 應用程式在 App Service 中運作，您必須將應用程式的完整網域名稱新增至允許的主機。 開啟 azuresite/settings.py 並尋找 `ALLOWED_HOSTS` 設定。 將該行變更為：

```python
ALLOWED_HOSTS = [os.environ['WEBSITE_SITE_NAME'] + '.azurewebsites.net', '127.0.0.1'] if 'WEBSITE_SITE_NAME' in os.environ else []
```

接下來，Django 不支援[在生產環境中提供靜態檔案](https://docs.djangoproject.com/en/2.1/howto/static-files/deployment/)，因此您必須手動啟用此功能。 在本教學課程中，您會使用 [WhiteNoise](https://whitenoise.evans.io/en/stable/)。 WhiteNoise 套件已包含在 requirements.txt 中。 您只需要設定 Django 以便使用它。 

在 azuresite/settings.py 中，尋找 `MIDDLEWARE` 設定，然後新增 `whitenoise.middleware.WhiteNoiseMiddleware` 中介軟體到清單中 (使其位於 `django.middleware.security.SecurityMiddleware` 中介軟體正下方)。 `MIDDLEWARE` 設定應該如下所示：

```python
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'whitenoise.middleware.WhiteNoiseMiddleware',
    ...
]
```

在 azuresite/settings.py 結尾處新增下列幾行。

```python
STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'

STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
```

如需如何設定 WhiteNoise 的詳細資訊，請參閱 [WhiteNoise 文件](https://whitenoise.evans.io/en/stable/)。

> [!IMPORTANT]
> [資料庫設定] 區段已遵循使用環境變數的安全性最佳做法。 如需完整的部署建議，請參閱 [Django 文件：部署檢查清單](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/)。


將變更認可至存放庫。

```bash
git commit -am "configure for App Service"
```

### <a name="configure-a-deployment-user"></a>設定部署使用者

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>建立應用程式服務方案 

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>建立 Web 應用程式 

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-python-linux-no-h.md)]

### <a name="configure-environment-variables"></a>設定環境變數

稍早在本教學課程中，您定義了環境變數來連線至 PostgreSQL 資料庫。

在 App Service 中，您可以在 Cloud Shell 中使用 [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) 命令將環境變數設定為「應用程式設定」。

下列範例會指定資料庫連線詳細資料作為應用程式設定。 

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="pollsdb"
```

### <a name="push-to-azure-from-git"></a>從 Git 推送至 Azure

[!INCLUDE [app-service-plan-no-h](../../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash 
Counting objects: 7, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (7/7), done.
Writing objects: 100% (7/7), 775 bytes | 0 bytes/s, done.
Total 7 (delta 4), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '6520eeafcc'.
remote: Generating deployment script.
remote: Running deployment command...
remote: Python deployment.
remote: Kudu sync from: '/home/site/repository' to: '/home/site/wwwroot'
. 
. 
. 
remote: Deployment successful.
remote: App container will begin restart within 10 seconds.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git 
   06b6df4..6520eea  master -> master
```  

App Service 部署伺服器會在存放庫的根目錄看到 requirements.txt，並在 `git push` 之後自動執行 Python 套件管理。

### <a name="browse-to-the-azure-app"></a>瀏覽至 Azure 應用程式

瀏覽至已部署的應用程式。 它需要一些時間才能啟動，因為在第一次要求應用程式時必須下載並執行容器。 如果頁面逾時或顯示錯誤訊息，請稍候幾分鐘並重新整理頁面。

```bash
http://<app_name>.azurewebsites.net
```

您應該會看到您稍早建立的投票問題。 

App Service 會藉由尋找每個子目錄中的 wsgi.py (預設會由 `manage.py startproject` 建立)，來偵測存放庫中的 Django 專案。 它找到檔案時，就會載入 Django 應用程式。 如需 App Service 如何載入 Python 應用程式的詳細資訊，請參閱[設定內建 Python 映像](how-to-configure-python.md)。

瀏覽至 `<app_name>.azurewebsites.net`，然後使用您建立的同一個管理使用者來登入。 如有需要，也可嘗試再建立一些投票問題。

![在本機執行的 Python Django 應用程式](./media/tutorial-python-postgresql-app/django-admin-azure.png)

**恭喜！** 您正在適用於 Linux 的 App Service 中執行 Python 應用程式。

## <a name="access-diagnostic-logs"></a>存取診斷記錄檔

在 Linux 上的 App Service 中，應用程式會在來自預設 Docker 映像的容器內執行。 您可以存取從容器內產生的主控台記錄。 若要取得記錄，請先在 Cloud Shell 中執行下列命令來開啟容器記錄：

```azurecli-interactive
az webapp log config --name <app_name> --resource-group myResourceGroup --docker-container-logging filesystem
```

開啟容器記錄後，請執行下列命令來查看記錄資料流：

```azurecli-interactive
az webapp log tail --name <app_name> --resource-group myResourceGroup
```

## <a name="manage-your-app-in-the-azure-portal"></a>在 Azure 入口網站管理您的應用程式

移至 [Azure 入口網站](https://portal.azure.com)，以查看您所建立的應用程式。

按一下左側功能表中的 [應用程式服務]，然後按一下 Azure 應用程式的名稱。

![入口網站瀏覽至 Azure 應用程式](./media/tutorial-python-postgresql-app/app-resource.png)

根據預設，入口網站會顯示應用程式的 [概觀] 頁面。 此頁面可讓您檢視應用程式的執行方式。 您也可以在這裡執行基本管理工作，像是瀏覽、停止、啟動、重新啟動及刪除。 分頁左側的索引標籤會顯示您可開啟的各種設定分頁。

![Azure 入口網站中的 App Service 頁面](./media/tutorial-python-postgresql-app/app-mgmt.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 在 Azure 中建立 PostgreSQL 資料庫
> * 將 Python 應用程式連線至 PostgreSQL
> * 將應用程式部署至 Azure
> * 檢視診斷記錄
> * 在 Azure 入口網站中管理應用程式

前往下一個教學課程，了解如何將自訂的 DNS 名稱對應至應用程式。

> [!div class="nextstepaction"]
> [將現有的自訂 DNS 名稱對應至 Azure App Service](../app-service-web-tutorial-custom-domain.md)

> [!div class="nextstepaction"]
> [設定內建 Python 映像並針對錯誤進行疑難排解](how-to-configure-python.md)

