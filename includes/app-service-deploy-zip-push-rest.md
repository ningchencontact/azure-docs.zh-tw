## <a name="rest"></a>使用 REST API 進行部署 
 
您可以使用[部署服務 REST API](https://github.com/projectkudu/kudu/wiki/REST-API)，在 Azure 中將 .zip 檔案部署至您的應用程式。 只要將 POST 要求傳送至 https://<app_name>.scm.azurewebsites.net/api/zipdeploy。 POST 要求必須在訊息本文中包含 .zip 檔案。 系統會使用 HTTP 基本驗證，在要求中提供應用程式的部署認證。 如需詳細資訊，請參閱 [.zip 推送部署參考](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)。 

下列範例使用 cURL 工具來傳送含有 .zip 檔案的要求。 您可以在 Mac 或 Linux 電腦上從終端機執行 cURL，或在 Windows 上使用 Bash 來執行 cURL。 使用專案之 .zip 檔案的位置路徑取代 `<zip_file_path>` 預留位置。 也請使用應用程式的唯一名稱取代 `<app_name>`。

使用部署認證的使用者名稱取代 `<deployment_user>` 預留位置。 當 cURL 顯示提示時，請輸入密碼。 若要了解如何設定應用程式的部署認證，請參閱[設定及重設使用者層級的認證](../articles/app-service/app-service-deployment-credentials.md#userscope)。   

```bash
curl -X POST -u <deployment_user> --data-binary @"<zip_file_path>" https://<app_name>.scm.azurewebsites.net/api/zipdeploy
```

此要求會觸發從上傳的 .zip 檔案推送部署。 您可以使用 https://<app_name>.scm.azurewebsites.net/api/deployments 端點來檢閱目前和過去的部署，如下列 cURL 範例所示。 再次使用您的應用程式名稱取代 `<app_name>`，以及使用部署認證的使用者名稱取代 `<deployment_user>`。

```bash
curl -u <deployment_user> https://<app_name>.scm.azurewebsites.net/api/deployments
```