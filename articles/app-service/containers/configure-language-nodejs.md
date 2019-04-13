---
title: 設定 Node.js 應用程式-Azure App Service |Microsoft Docs
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
ms.openlocfilehash: 43dc76e6d1e1ec2a6167f1d3e3cc7b8780f843db
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2019
ms.locfileid: "59551318"
---
# <a name="configure-a-linux-nodejs-app-for-azure-app-service"></a>適用於 Azure App Service 中設定 Linux Node.js 應用程式

Node.js 應用程式必須部署具有所有必要的 NPM 相依性。 App Service 部署引擎 」 (Kudu) 會自動執行`npm install --production`為您在部署時[Git 儲存機制](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)，或有[Zip 套件](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)電源已開啟的建置程序。 如果您部署您使用的檔案[FTP/S](../deploy-ftp.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)，不過，您需要手動上傳所需的套件。

本指南提供重要概念和使用 App Service 中的內建的 Linux 容器的 Node.js 開發人員的指示。 如果您從未使用過 Azure App Service，請遵循[Node.js 快速入門](quickstart-nodejs.md)並[MongoDB 教學課程使用 Node.js](tutorial-nodejs-mongodb-app.md)第一次。

## <a name="show-nodejs-version"></a>顯示 Node.js 版本

若要顯示目前的 Node.js 版本，請執行下列命令[Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

若要顯示所有支援的 Node.js 版本，請執行下列命令[Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep NODE
```

## <a name="set-nodejs-version"></a>設定 Node.js 版本

將您的應用程式設定為[支援的 Node.js 版本](#show-nodejs-version)，執行下列命令[Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "NODE|10.14"
```

此設定會指定要使用，請在執行階段和 kudu 自動的套件還原期間的 Node.js 版本。

> [!NOTE]
> 您應該在您的專案設定 Node.js 版本`package.json`。 部署引擎會執行在不同的容器，其中包含所有支援的 Node.js 版本。

## <a name="configure-nodejs-server"></a>設定 Node.js 伺服器

Node.js 容器隨附[PM2](http://pm2.keymetrics.io/)，實際執行程序管理員。 您可以設定您的應用程式使用 PM2，或使用 NPM，或自訂的命令來開始。

- [執行自訂命令](#run-custom-command)
- [執行 npm 啟動](#run-npm-start)
- [使用 PM2 執行](#run-with-pm2)

### <a name="run-custom-command"></a>執行自訂命令

App Service 可以開始使用自訂的命令，您的應用程式，例如可執行檔，像是*run.sh*。例如，若要執行`npm run start:prod`，執行下列命令， [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "npm run start:prod"
```

### <a name="run-npm-start"></a>執行 npm 啟動

若要開始您的應用程式使用`npm start`，您只需要確定`start`指令碼位於*package.json*檔案。 例如︰

```json
{
  ...
  "scripts": {
    "start": "gulp",
    ...
  },
  ...
}
```

若要使用自訂*package.json*在您專案中，執行下列命令[Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filename>.json"
```

### <a name="run-with-pm2"></a>使用 PM2 執行

在您的專案中找到的其中一個常見的 Node.js 檔案時，容器會將您的應用程式會自動啟動使用 PM2:

- *bin/www*
- *server.js*
- *app.js*
- *index.js*
- *hostingstart.js*
- 下列其中一種[PM2 檔案](http://pm2.keymetrics.io/docs/usage/application-declaration/#process-file): *process.json*和*ecosystem.config.js*

您也可以設定的自訂啟動檔案具有下列副檔名：

- A *.js*檔案
- A [PM2 檔](http://pm2.keymetrics.io/docs/usage/application-declaration/#process-file)副檔名 *.json*， *。 config.js*， *.yaml*，或 *.yml*

若要新增的自訂啟動檔案，請執行下列命令[Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filname-with-extension>"
```

## <a name="debug-remotely"></a>遠端偵錯

> [!NOTE]
> 遠端偵錯目前為預覽狀態。

您可以偵錯 Node.js 應用程式從遠端在[Visual Studio Code](https://code.visualstudio.com/)如果您將它設定為[使用 PM2 執行](#run-with-pm2)，但當您執行使用 *。 config.js、 *.yml，或 *.yaml*。

在大部分情況下，額外就不需要設定您的應用程式。 如果您的應用程式會執行*process.json*檔案 （預設或自訂），它必須要有`script`JSON 根目錄中的屬性。 例如︰

```json
{
  "name"        : "worker",
  "script"      : "./index.js",
  ...
}
```

若要設定的遠端偵錯的 Visual Studio Code，請安裝[App Service 延伸模組](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)。 遵循 [延伸模組] 頁面上的指示，並登入 Visual Studio Code 中的 Azure。

在 [Azure 總管] 中，尋找您想要偵錯，以滑鼠右鍵按一下並選取應用的程式**啟動遠端偵錯**。 按一下 **是**為您的應用程式加以啟用。 App Service 啟動您的通道 proxy，並將附加偵錯工具。 然後，您可以對應用程式提出要求，並查看偵錯工具在中斷點暫停。

一旦完成偵錯，請選取停止偵錯工具**中斷連線**。 出現提示時，您應該按一下**是**停用遠端偵錯。 若要稍後再停用它，以滑鼠右鍵按一下您的應用程式，一次在 [Azure 總管]，然後選取**停用遠端偵錯**。

## <a name="access-environment-variables"></a>存取環境變數

在 App Service 中，您可以[設定應用程式設定](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings)外部應用程式程式碼。 然後，您可以使用標準的 Node.js 模式。 例如，若要存取稱為 `NODE_ENV` 的應用程式設定，請使用下列程式碼：

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>執行 Grunt/Bower/Gulp

根據預設，Kudu 執行`npm install --production`時它會辨識部署 Node.js 應用程式。 如果您的應用程式需要任何受歡迎的自動化工具，例如 Grunt、 Bower 或 Gulp，您必須提供[自訂部署指令碼](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)執行它。

若要啟用您的存放庫，才能執行這些工具，您需要將它們新增至中的相依性*package.json。* 例如︰

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

從本機終端機視窗中，將目錄變更至您儲存機制的根目錄，並執行下列命令：

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

存放庫根目錄現在有兩個額外的檔案： *.deployment*並*deploy.sh*。

開啟*deploy.sh*並尋找`Deployment`區段中，哪一個看起來像這樣：

```bash
##################################################################################################################################
# Deployment
# ----------
```

本章節的結尾執行`npm install --production`。 新增您要執行必要的工具將程式碼區段*結尾*的`Deployment`區段：

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

請參閱[MEAN.js 範例中的範例](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135)，其中的部署指令碼也會執行自訂`npm install`命令。

### <a name="bower"></a>Bower

此程式碼片段執行`bower install`。

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gulp

此程式碼片段執行`gulp imagemin`。

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

此程式碼片段執行`grunt`。

```bash
if [ -e "$DEPLOYMENT_TARGET/Gruntfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/grunt
  exitWithMessageOnError "Grunt failed"
  cd - > /dev/null
fi
```

## <a name="detect-https-session"></a>偵測 HTTPS 工作階段

在 App Service 中，[SSL 終止](https://wikipedia.org/wiki/TLS_termination_proxy)會在網路負載平衡器上發生，因此所有的 HTTPS 要求都會以未加密 HTTP 要求的形式進入您的應用程式。 如果您的應用程式邏輯需要檢查使用者要求是否有加密，請檢查 `X-Forwarded-Proto` 標頭。

熱門的 Web 架構可讓您在標準的應用程式模式中存取 `X-Forwarded-*` 資訊。 在  [Express](https://expressjs.com/)，您可以使用[信任 proxy](http://expressjs.com/guide/behind-proxies.html)。 例如︰

```javascript
app.set('trust proxy', 1)
...
if (req.secure) {
  // Do something when HTTPS is used
}
```

## <a name="access-diagnostic-logs"></a>存取診斷記錄檔

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>在瀏覽器中開啟 SSH 工作階段

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>疑難排解

當可運作的 Node.js 應用程式在 App Service 中的行為不同，或發生錯誤時，請嘗試下列方法：

- [存取記錄資料流](#access-diagnostic-logs)。
- 在生產模式中，在本機測試應用程式。 App Service 在生產模式中執行您的 Node.js 應用程式，因此您必須先確定您的專案運作如預期般在生產模式在本機中。 例如︰
    - 取決於您*package.json*，不同的封裝可能會安裝用於實際執行模式 (`dependencies`與`devDependencies`)。
    - 某些 web 架構可能部署在生產模式中有不同的靜態檔案。
    - 在生產模式中執行時，某些 web 架構可能會使用自訂的啟動指令碼。
- 在開發模式中，在 App Service 中執行您的應用程式。 例如，在[MEAN.js](http://meanjs.org/)，您可以設定您的應用程式執行階段中的開發模式[設定`NODE_ENV`應用程式設定](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [教學課程：使用 MongoDB 的 Node.js 應用程式](tutorial-nodejs-mongodb-app.md)

> [!div class="nextstepaction"]
> [App Service Linux 常見問題集](app-service-linux-faq.md)