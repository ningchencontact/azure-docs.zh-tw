## <a name="deploy-uploaded-zip-file"></a>部署上傳的 ZIP 檔案

在 Cloud Shell 中，使用 [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az_webapp_deployment_source_config_zip) 命令將上傳的 ZIP 檔案部署至 web 應用程式。 請務必將 *\<app_name>* 取代為您的 Web 應用程式名稱。

```azurecli-interactive
az webapp deployment source config-zip --resource-group myResouceGroup --name <app_name> --src clouddrive/myAppFiles.zip
```

此命令會將檔案和目錄從 ZIP 檔案部署到預設的 App Service 應用程式資料夾 (`\home\site\wwwroot`)，並重新啟動應用程式。 如果已設定任何其他的自訂建置程序，它也會執行。
