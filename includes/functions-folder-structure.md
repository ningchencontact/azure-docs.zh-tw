
特定函式應用程式中所有函式的程式碼，都位於包含主機設定檔和一或多個子資料夾的根資料夾中。 每個子資料夾都包含個別函式的程式碼，如以下範例所示：

```
wwwroot
 | - host.json
 | - mynodefunction
 | | - function.json
 | | - index.js
 | | - node_modules
 | | | - ... packages ...
 | | - package.json
 | - mycsharpfunction
 | | - function.json
 | | - run.csx
```

host.json 檔案包含一些執行階段特定的組態，並位在函數應用程式的根資料夾中。 如需可用設定的相關資訊，請參閱 [host.json 參考](../articles/azure-functions/functions-host-json.md)。

每個函數都會有一個資料夾，其中包含一或多個程式碼檔案、function.json 組態及其他相依性。

