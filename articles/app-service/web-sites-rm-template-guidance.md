---
title: "使用範本部署 Azure Web 應用程式的指引 |Microsoft Docs"
description: "建立 Azure Resource Manager 範本以部署 Web 應用程式的建議。"
services: app-service
documentationcenter: app-service
author: tfitzmac
manager: timlt
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: tomfitz
ms.openlocfilehash: eff0b0e6258217fa8fbf7f15606f98d70fecd7c5
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2018
---
# <a name="guidance-on-deploying-web-apps-with-azure-resource-manager-templates"></a>使用 Azure Resource Manager 範本部署 Web 應用程式的指引

本文提供建立 Azure Resource Manager 範本以部署 App Service 解決方案的建議。 這些建議可協助您避免常見的問題。

## <a name="define-dependencies"></a>定義相依性

要定義 Web 應用程式的相依性，必須先了解 Web 應用程式中的資源彼此互動的方式。 如果您以不正確的順序指定相依性，將可能會導致部署錯誤，或產生競爭情形而致使部署停止。

> [!WARNING]
> 如果您在範本中納入 MS Deploy 網站延伸模組，則必須將任何組態資源設定為相依於 MS Deploy 資源。 組態變更會導致網站以非同步方式重新啟動。 將組態資源設定為相依於 MS Deploy，可確保在 MS Deploy 完成後，網站才會重新啟動。 若沒有這些相依性，網站即可能在 MS Deploy 的部署程序期間重新啟動。 如需範例範本，請參閱[具有 Web Deploy 相依性的 Wordpress 範本](https://github.com/davidebbo/AzureWebsitesSamples/blob/master/ARMTemplates/WordpressTemplateWebDeployDependency.json)。

下圖顯示各種 App Service 資源的相依性順序。

![Web 應用程式相依性](media/web-sites-rm-template-guidance/web-dependencies.png)

您可以依照下列順序部署資源︰

**第 1 層**
* App Service 方案
* 任何其他相關資源，例如資料庫或儲存體帳戶

**第 2 層**
* Web 應用程式 - 視 App Service 方案而定
* 以伺服器陣列為目標的 Application Insights - 視 App Service 方案而定

**第 3 層**
* 原始檔控制 - 視 Web 應用程式而定
* MSDeploy 網站延伸模組 - 視 Web 應用程式而定
* 以伺服器陣列為目標的 Application Insights - 視 Web 應用程式而定

**第 4 層**
* App Service 憑證 - 取決於存在的原始檔控制或 MSDeploy；若都不存在，則視 Web 應用程式而定
* 組態設定 (連接字串、Web 組態值、應用程式設定) - 取決於存在的原始檔控制或 MSDeploy；若都不存在，則視 Web 應用程式而定

**第 5 層**
* 主機名稱繫結 - 取決於存在的憑證；若不存在，則為較高層級的資源
* 網站延伸模組 - 取決於存在的組態設定；若不存在，則為較高層級的資源

一般而言，您的解決方案只會包含前述部分的資源和層級。 對於缺漏的層級，會將較低的資源往高一層對應。

下列範例將說明範本的一部分。 連接字串組態值取決於 MSDeploy 延伸模組。 MSDeploy 延伸模組則取決於 Web 應用程式和資料庫。

```json
{
    "name": "[parameters('name')]",
    "type": "Microsoft.Web/sites",
    "resources": [
      {
          "name": "MSDeploy",
          "type": "Extensions",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('name'))]",
            "[concat('SuccessBricks.ClearDB/databases/', parameters('databaseName'))]"
          ],
          ...
      },
      {
          "name": "connectionstrings",
          "type": "config",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('name'), '/Extensions/MSDeploy')]"
          ],
          ...
      }
    ]
}
```

## <a name="find-information-about-msdeploy-errors"></a>尋找 MSDeploy 錯誤的相關資訊

如果您的 Resource Manager 範本使用 MSDeploy，部署錯誤訊息可能會難以了解。 若要在部署失敗後取得詳細資訊，請嘗試執行下列步驟：

1. 移至網站的 [Kudu 主控台](https://github.com/projectkudu/kudu/wiki/Kudu-console)。
2. 瀏覽至 `D:\home\LogFiles\SiteExtensions\MSDeploy` 上的資料夾。
3. 尋找 *appManagerStatus.xml* 和 *appManagerLog.xml* 檔案。 第一個檔案會記錄狀態。 第二個檔案會記錄錯誤的相關資訊。 如果您不清楚錯誤的涵義，可以在論壇上尋求協助。

## <a name="unique-web-app-name"></a>唯一的 Web 應用程式名稱

Web 應用程式的名稱必須是全域唯一的。 您可以使用很可能是唯一的命名慣例，或使用 [uniqueString 函式](../azure-resource-manager/resource-group-template-functions-string.md#uniquestring)以利產生唯一的名稱。

```json
{
  "apiVersion": "2016-08-01",
  "name": "[concat(parameters('siteNamePrefix'), uniqueString(resourceGroup().id))]",
  "type": "Microsoft.Web/sites",
  ...
}
```

## <a name="next-steps"></a>後續步驟

* 如需使用範本部署 Web 應用程式的教學課程，請參閱[透過可預測方式在 Azure 中佈建和部署微服務](app-service-deploy-complex-application-predictably.md)。