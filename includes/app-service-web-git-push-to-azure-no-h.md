在本機終端視窗中，將 Azure 遠端新增至本機 Git 存放庫。 將 _&lt;paste\_copied\_url\_here>_ 取代為您從[建立 Web 應用程式](#create)儲存之 Git 遠端的 URL。

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

推送到 Azure 遠端，使用下列命令來部署您的應用程式。 當系統提示輸入密碼時，務必輸入您在[設定部署使用者](#configure-a-deployment-user)中建立的密碼，而不是您用來登入 Azure 入口網站的密碼。

```bash
git push azure master
```

此命令可能會花數分鐘執行。 執行上述命令時，會顯示類似下列範例的資訊：
