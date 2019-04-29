---
title: 設定 PHP 應用程式-Azure App Service |Microsoft Docs
description: 了解如何設定 Azure App Service 中運作的 PHP 應用程式
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
ms.openlocfilehash: 11d0648ee5090f02cb96c2d42a8d90cc3ea0ed28
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60853298"
---
# <a name="configure-a-linux-php-app-for-azure-app-service"></a>適用於 Azure App Service 中設定 Linux PHP 應用程式

本指南將說明如何在 Azure App Service 中設定 web 應用程式、 行動後端和 API 應用程式內建的 PHP 執行階段。

本指南提供重要概念和使用 App Service 中的內建的 Linux 容器的 PHP 開發人員的指示。 如果您從未使用過 Azure App Service，請遵循[PHP 的快速入門](quickstart-php.md)並[PHP 與 MySQL 教學課程](tutorial-php-mysql-app.md)第一次。

## <a name="show-php-version"></a>顯示的 PHP 版本

若要顯示目前的 PHP 版本，請執行下列命令[Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

若要顯示所有支援的 PHP 版本，請執行下列命令[Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep PHP
```

## <a name="set-php-version"></a>設定 PHP 版本

執行下列命令[Cloud Shell](https://shell.azure.com) 7.2 設的 PHP 版本：

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "PHP|7.2"
```

## <a name="run-composer"></a>執行編輯器

根據預設，不執行 Kudu[編輯器](https://getcomposer.org/)。 若要啟用 「 編輯器 」 自動化，Kudu 部署期間，您需要提供[自訂部署指令碼](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)。

從本機終端機視窗中，將目錄變更至您的儲存機制根目錄中。 請遵循[命令列安裝步驟](https://getcomposer.org/download/)以下載*composer.phar*。

執行下列命令：

```bash
npm install kuduscript -g
kuduscript --php --scriptType bash --suppressPrompt
```

存放庫根目錄現在有兩個新的檔案，除了*composer.phar*: *.deployment*並*deploy.sh*。這些檔案可運作的 Windows 和 Linux 的 App Service 的類別。

開啟*deploy.sh*並尋找`Deployment`一節。 取代為下列程式碼的整個區段：

```bash
##################################################################################################################################
# Deployment
# ----------

echo PHP deployment

# 1. KuduSync
if [[ "$IN_PLACE_DEPLOYMENT" -ne "1" ]]; then
  "$KUDU_SYNC_CMD" -v 50 -f "$DEPLOYMENT_SOURCE" -t "$DEPLOYMENT_TARGET" -n "$NEXT_MANIFEST_PATH" -p "$PREVIOUS_MANIFEST_PATH" -i ".git;.hg;.deployment;deploy.sh"
  exitWithMessageOnError "Kudu Sync failed"
fi

# 3. Initialize Composer Config
initializeDeploymentConfig

# 4. Use composer
echo "$DEPLOYMENT_TARGET"
if [ -e "$DEPLOYMENT_TARGET/composer.json" ]; then
  echo "Found composer.json"
  pushd "$DEPLOYMENT_TARGET"
  php composer.phar install $COMPOSER_ARGS
  exitWithMessageOnError "Composer install failed"
  popd
fi
##################################################################################################################################
```

認可所有變更，然後重新部署您的程式碼。 編輯器現在應該執行一部分的部署自動化。

## <a name="customize-start-up"></a>自訂啟動

根據預設，內建的 PHP 容器會執行 Apache 伺服器。 在啟動時，它會執行`apache2ctl -D FOREGROUND"`。 如果您喜歡，，您可以在啟動時，執行不同的命令中執行下列命令[Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

## <a name="access-environment-variables"></a>存取環境變數

在 App Service 中，您可以於應用程式的程式碼外部[設定應用程式設定](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings)。 然後您可以存取它們使用標準[getenv （)](https://secure.php.net/manual/function.getenv.php)模式。 例如，若要存取稱為 `DB_HOST` 的應用程式設定，請使用下列程式碼：

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>變更站台根目錄

您選擇的 web 架構可作為網站根目錄的子目錄。 例如， [Laravel](https://laravel.com/)，會使用`public/`作為站台根目錄的子目錄。

預設的 PHP 影像適用於 App Service 會使用 Apache，且不會讓您自訂您的應用程式的網站根目錄。 若要解決這項限制，加入 *.htaccess*檔案，以存放庫根目錄中，以下列內容：

```
<IfModule mod_rewrite.c>
    RewriteEngine on

    RewriteRule ^.*$ /public/$1 [NC,L,QSA]
</IfModule>
```

如果您不要使用 .htaccess 重寫，您可以改為使用[自訂 Docker 映像](quickstart-docker-go.md)來部署 Laravel 應用程式。

## <a name="detect-https-session"></a>偵測 HTTPS 工作階段

在 App Service 中，[SSL 終止](https://wikipedia.org/wiki/TLS_termination_proxy)會在網路負載平衡器上發生，因此所有的 HTTPS 要求都會以未加密 HTTP 要求的形式進入您的應用程式。 如果您的應用程式邏輯需要檢查使用者要求是否有加密，請檢查 `X-Forwarded-Proto` 標頭。

```php
if (isset($_SERVER['X-Forwarded-Proto']) && $_SERVER['X-Forwarded-Proto'] === 'https') {
  // Do something when HTTPS is used
}
```

熱門的 Web 架構可讓您在標準的應用程式模式中存取 `X-Forwarded-*` 資訊。 在 [CodeIgniter](https://codeigniter.com/) 中，[is_https()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) 預設會檢查 `X_FORWARDED_PROTO` 的值。

## <a name="customize-phpini-settings"></a>自訂 php.ini 設定

如果您需要變更您的 PHP 安裝，您可以變更的任何[php.ini 指示詞](http://www.php.net/manual/ini.list.php)依照下列步驟。

> [!NOTE]
> 若要查看的 PHP 版本和目前的最佳方式*php.ini*組態就是呼叫[phpinfo （)](https://php.net/manual/function.phpinfo.php)應用程式中。
>

### <a name="customize-non-phpinisystem-directives"></a>自訂非 PHP_INI_SYSTEM 指示詞

若要自訂 PHP_INI_USER、 PHP_INI_PERDIR 和 PHP_INI_ALL 指示詞 (請參閱[php.ini 指示詞](http://www.php.net/manual/ini.list.php))，新增 *.htaccess*檔案至您的應用程式的根目錄。

在  *.htaccess*檔案中，新增使用指示詞`php_value <directive-name> <value>`語法。 例如︰

```
php_value upload_max_filesize 1000M
php_value post_max_size 2000M
php_value memory_limit 3000M
php_value max_execution_time 180
php_value max_input_time 180
php_value display_errors On
php_value upload_max_filesize 10M
```

重新部署您的應用程式所做的變更，然後重新啟動它。 如果您使用 kudu 進行部署 (例如，使用[Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json))，在部署之後會自動重新啟動。

若要使用替代 *.htaccess*，您可以使用[ini_set （)](http://www.php.net/manual/function.ini-set.php)在您的應用程式來自訂這些非 PHP_INI_SYSTEM 指示詞。

### <a name="customize-phpinisystem-directives"></a>自訂 PHP_INI_SYSTEM 指示詞

若要自訂 PHP_INI_SYSTEM 指示詞 (請參閱[php.ini 指示詞](http://www.php.net/manual/ini.list.php))，您無法使用 *.htaccess*方法。 App Service 可讓您提供不同的機制，使用`PHP_INI_SCAN_DIR`應用程式設定。

首先，執行下列命令， [Cloud Shell](https://shell.azure.com)以新增應用程式設定呼叫`PHP_INI_SCAN_DIR`:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="/usr/local/etc/php/conf.d:/home/site/ini"
```

`/usr/local/etc/php/conf.d` 是預設的目錄位置*php.ini*存在。 `/home/site/ini` 是，您將在其中加入自訂的自訂目錄 *.ini*檔案。 分隔的值與`:`。

瀏覽至您的 Linux 容器的 web SSH 工作階段 (`https://cephalin-container.scm.azurewebsites.net/webssh/host`)。

中建立目錄`/home/site`呼叫`ini`，然後建立 *.ini*中的檔案`/home/site/ini`目錄 (例如*settings.ini)* 與您想要自訂的指示詞。 使用相同的語法，您可以使用*php.ini*檔案。 

> [!TIP]
> 在 App Service 中的內建的 Linux 容器 */首頁*做為保存的共用儲存體。 
>

例如，若要變更的值[expose_php](http://php.net/manual/ini.core.php#ini.expose-php)執行下列命令：

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

針對以使變更生效，請重新啟動應用程式。

## <a name="enable-php-extensions"></a>啟用 PHP 延伸模組

內建的 PHP 安裝包含最常用的擴充功能。 您可以啟用其他的擴充功能，在同一個方法您[來自訂 php.ini 指示詞](#customize-php_ini_system-directives)。

> [!NOTE]
> 若要查看的 PHP 版本和目前的最佳方式*php.ini*組態就是呼叫[phpinfo （)](https://php.net/manual/function.phpinfo.php)應用程式中。
>

若要啟用其他延伸模組，請依照下列步驟執行：

新增`bin`目錄的根目錄，您的應用程式和 put`.so`延伸模組檔案 (例如*mongodb.so*)。 請確定擴充功能，都與在 Azure 與 vc9 及非執行緒安全 (nts) 相容的 PHP 版本相容。

部署您的變更。

請依照下列中的步驟[自訂 PHP_INI_SYSTEM 指示詞](#customize-php_ini_system-directives)，將擴充功能新增至自訂 *.ini*檔案[擴充功能](https://www.php.net/manual/ini.core.php#ini.extension)或[zend_extension](https://www.php.net/manual/ini.core.php#ini.zend-extension)指示詞。

```ini
extension=/home/site/wwwroot/bin/mongodb.so
zend_extension=/home/site/wwwroot/bin/xdebug.so
```

針對以使變更生效，請重新啟動應用程式。

## <a name="access-diagnostic-logs"></a>存取診斷記錄

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>在瀏覽器中開啟 SSH 工作階段

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>疑難排解

當開發 PHP 應用程式在 App Service 中的行為不同，或發生錯誤時，請嘗試下列方法：

- [存取記錄資料流](#access-diagnostic-logs)。
- 在生產模式中，在本機測試應用程式。 App Service 在生產模式中執行您的 Node.js 應用程式，因此您必須先確定您的專案運作如預期般在生產模式在本機中。 例如︰
    - 取決於您*composer.json*，不同的封裝可能會安裝用於實際執行模式 (`require`與`require-dev`)。
    - 某些 web 架構可能部署在生產模式中有不同的靜態檔案。
    - 在生產模式中執行時，某些 web 架構可能會使用自訂的啟動指令碼。
- 在 App Service 中偵錯模式執行您的應用程式。 例如，在[Laravel](http://meanjs.org/)，您可以設定您的應用程式輸出生產環境中的偵錯訊息[設定`APP_DEBUG`應用程式設定設`true` ](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)。

### <a name="robots933456"></a>robots933456

您可能會看到容器記錄檔中的下列訊息：

```
2019-04-08T14:07:56.641002476Z "-" - - [08/Apr/2019:14:07:56 +0000] "GET /robots933456.txt HTTP/1.1" 404 415 "-" "-"
```

您可以放心忽略此訊息。 `/robots933456.txt` 是虛擬應用程式服務用來檢查容器是否能夠為要求 URL 路徑。 404 回應只是表示，路徑不存在，但它可讓 App Service 知道容器是狀況良好且準備好回應要求。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [教學課程：使用 MySQL 的 PHP 應用程式](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [App Service Linux 常見問題集](app-service-linux-faq.md)