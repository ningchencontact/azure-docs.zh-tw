## <a name="deployment-customization"></a>部署自訂

部署程序假設您推送的.zip 檔案包含執行已備妥應用程式。 根據預設，會不執行任何自訂。 若要啟用相同的建置程序，您會得到搭配持續整合，請將下列加入您的應用程式設定：

    SCM_DO_BUILD_DURING_DEPLOYMENT=true 

當您使用.zip 推入部署時，這項設定是**false**預設。 預設值是**true**連續整合的部署。 當設定為**true**，部署期間使用您部署相關的設定。 為應用程式設定或.deployment 組態檔位於根目錄中的.zip 檔案中，您可以設定這些設定。 如需詳細資訊，請參閱[儲存機制和部署相關的設定](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings)部署參考中。