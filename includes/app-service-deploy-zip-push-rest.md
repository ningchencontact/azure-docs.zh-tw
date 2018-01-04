## <a name="rest"></a>使用 REST Api 部署 
 
您可以使用[部署服務 REST Api](https://github.com/projectkudu/kudu/wiki/REST-API)要將.zip 檔案部署至 Azure 中的應用程式。 只傳送 POST 要求給 https://<app_name>.scm.azurewebsites.net/api/zipdeploy。 POST 要求必須包含訊息本文中的.zip 檔案。 您的應用程式的部署認證是在利用 HTTP 基本驗證要求中提供。 如需詳細資訊，請參閱[.zip 發送部署參考](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)。 

下列範例會使用 cURL 工具來傳送要求，其中包含的.zip 檔案。 您可以從終端機執行 cURL Mac 或 Linux 電腦上或在 Windows 上使用 Bash。 取代`<zip_file_path>`預留位置 project.zip 檔案的位置的路徑。 也取代`<app_name>`與您的應用程式的唯一名稱。

取代`<deployment_user>`您的部署認證的使用者名稱的預留位置。 出現提示時 cURL，輸入密碼。 若要了解如何設定您的應用程式的部署認證，請參閱[設定重設使用者層級認證和](../articles/app-service/app-service-deployment-credentials.md#userscope)。   

```bash
curl POST -u <deployment_user> --data-binary @"<zip_file_path>" https://<app_name>.scm.azurewebsites.net/api/zipdeploy
```

此要求觸發推入部署從已上傳的.zip 檔案。 下列 cURL 範例所示，您可以使用 https://<app_name>.scm.azurewebsites.net/api/deployments 端點中，檢閱目前和過去的部署。 同樣地，取代`<app_name>`與您的應用程式的名稱和`<deployment_user>`您的部署認證的使用者名稱。

```bash
curl -u <deployment_user> https://<app_name>.scm.azurewebsites.net/api/deployments
```