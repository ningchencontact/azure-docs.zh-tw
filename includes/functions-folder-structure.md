
在特定的函式應用程式中的所有函式的程式碼位於根資料夾，其中包含主機設定檔和一或多個子資料夾中。 每個子資料夾包含個別的函式，如下列範例所示的程式碼：

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

Host.json 檔案包含某些特定的執行階段組態中，並位於函式應用程式的根資料夾中。 如需可用設定的資訊，請參閱[host.json 參考](../articles/azure-functions/functions-host-json.md)。

每個函數都有包含一或多個程式碼檔案、 function.json 組態及其他相依性的資料夾。

