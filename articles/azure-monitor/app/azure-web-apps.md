---
title: 監視 Azure 應用程式服務效能 | Microsoft Docs
description: Azure 應用程式服務的應用程式效能監視。 圖表載入和回應時間、 相依性資訊，以及設定效能警示。
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: mbullwin
ms.openlocfilehash: 7386f6bd92143cf3fb7b37725900425f99371cd0
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2019
ms.locfileid: "58804987"
---
# <a name="monitor-azure-app-service-performance"></a>監視 Azure App Service 效能

啟用您的.NET 和.NET Core 上監視 Azure App Service 上執行的基礎的 web 應用程式現在較以往更為容易。 先前，您需要手動安裝網站延伸模組，而最新的延伸模組/代理程式現在內建應用程式服務映像的預設值。 這篇文章會逐步引導您完成啟用 Application Insights 監視，以及提供將進行大規模的部署程序自動化的初步指引。

> [!NOTE]
> 手動新增 Application Insights 網站延伸模組，透過**開發工具** > **延伸模組**已被取代。 最新穩定版本的延伸模組現[預先安裝](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions)納入應用程式服務映像。 檔案位於`d:\Program Files (x86)\SiteExtensions\ApplicationInsightsAgent`和每一個穩定版本時，會自動更新。 如果您遵循架構的代理程式來啟用監視，它會自動移除已被取代的延伸模組供您。

## <a name="enable-application-insights"></a>啟用 Application Insights

有兩種方式可啟用應用程式監視 Azure App Service 裝載應用程式：

* **代理程式為基礎的應用程式監視**(ApplicationInsightsAgent)。  
    * 這個方法最為輕鬆，若要啟用，並不需要任何進階的設定。 它通常稱為 「 執行階段 」 監視。 對於 Azure App Service，我們建議您啟用此層級的監視，至少，然後根據您的特定案例，您可以評估是否需要透過手動檢測的更進階監視。

* **以手動方式進行檢測的應用程式，透過程式碼**藉由安裝 Application Insights SDK。

    * 這種方法可更多自訂，但它需要[新增 Application Insights SDK NuGet 套件的相依性](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)。 這種方法，也表示您不必自行管理套件的最新版本的更新。

    * 如果您需要進行自訂的 API 呼叫，不會擷取與代理程式為基礎的監視預設的追蹤事件/相依性，您必須使用這個方法。 請參閱[API 的自訂事件和度量的發行項](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics)若要深入了。

> [!NOTE]
> 如果代理程式的基礎監視和手動的 SDK 為基礎的檢測偵測到會接受僅手動檢測設定。 這是為了防止重複的資料傳送。 若要深入了解這個取出[疑難排解 區段](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting)如下。

## <a name="enable-agent-based-monitoring-net"></a>啟用代理程式為基礎的監視.NET

1. 在 Azure 控制台中，選取您應用程式服務的 [Application Insights]。

    ![在 [設定] 之下，選擇 Application Insights](./media/azure-web-apps/settings-app-insights-01.png)

   * 除非您已經為此應用程式設定 Application Insights 資源，否則請選擇建立新的資源。 

     > [!NOTE]
     > 當您按一下 [確定] 來建立新資源時，系統會提示您 [套用監視設定]。 選取 [繼續] 會將新的 Application Insights 資源連結至您的應用程式服務，這麼做也會**觸發應用程式服務的重新啟動**。 

     ![檢測 Web 應用程式](./media/azure-web-apps/create-resource-01.png)

2. 在指定要使用的資源之後，您可以選擇要如何讓 Application Insights 收集每個應用程式平台的資料。 ASP.NET 應用程式監視是在預設為具有兩個不同的層級的集合。

    ![選擇每個平台的選項](./media/azure-web-apps/choose-options-new.png)

   * .NET **基本收集**層級提供基本的單一執行個體 APM 功能。

   * .NET **建議收集**層級：
       * 新增 CPU、記憶體和 I/O 使用趨勢。
       * 將不同要求/相依性界限間的微服務相互關聯。
       * 收集使用趨勢，並啟用從可用性結果到交易的相互關聯。
       * 收集主機程序未處理的例外狀況。
       * 在使用取樣時，改善 APM 計量在負載下的精確度。

3. 若要設定的設定，例如取樣，您可能先前控制透過 applicationinsights.config 檔案中您現在可以與這些相同的設定，透過應用程式設定對應的前置詞與互動。 

    * 例如，若要變更初始的取樣百分比，您可以建立的應用程式設定：`MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_InitialSamplingPercentage`值，並針對`100`。

    * 如需支援的調適性取樣遙測處理器設定的清單，您可以查閱[程式碼](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/master/src/ServerTelemetryChannel/AdaptiveSamplingTelemetryProcessor.cs)並[相關聯的文件](https://docs.microsoft.com/azure/azure-monitor/app/sampling)。

## <a name="enable-agent-based-monitoring-net-core"></a>啟用代理程式型監視的.NET Core

支援下列版本的.NET Core:ASP.NET Core 2.0, ASP.NET Core 2.1, ASP.NET Core 2.2

目標為.NET Core、 自封式的部署，和 ASP.NET Core 3.0 完整 framework 正在**不支援**使用監視代理程式/延伸模組為基礎。 ([手動檢測](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)透過程式碼可在所有先前的案例。)

1. 在 Azure 控制台中，選取您應用程式服務的 [Application Insights]。

    ![在 [設定] 之下，選擇 Application Insights](./media/azure-web-apps/settings-app-insights-01.png)

   * 除非您已經為此應用程式設定 Application Insights 資源，否則請選擇建立新的資源。 

     > [!NOTE]
     > 當您按一下 [確定] 來建立新資源時，系統會提示您 [套用監視設定]。 選取 [繼續] 會將新的 Application Insights 資源連結至您的應用程式服務，這麼做也會**觸發應用程式服務的重新啟動**。 

     ![檢測 Web 應用程式](./media/azure-web-apps/create-resource-01.png)

2. 指定要使用的資源之後, 您可以選擇您要如何收集資料，每個平台應用程式的 Application Insights。 .NET core 可提供**建議集合**或是**停用**適用於.NET Core 2.0、 2.1 和 2.2。

    ![選擇每個平台的選項](./media/azure-web-apps/choose-options-new-net-core.png)

## <a name="enable-client-side-monitoring-net"></a>啟用用戶端監視的.NET

用戶端監視是選用功能的 ASP.NET。 若要啟用用戶端監視：

* 選取 **設定**> * * * * 應用程式設定 * * *
   * 在應用程式設定 下加入新**應用程式設定名稱**並**值**:

     名稱：`APPINSIGHTS_JAVASCRIPT_ENABLED`

     值: `true`

   * **儲存**設定並**重新啟動**您的應用程式。

![應用程式的螢幕擷取畫面設定 UI](./media/azure-web-apps/appinsights-javascript-enabled.png)

若要停用用戶端監視移除相關聯的金鑰值組，從應用程式設定中，或將值設為 false。

## <a name="enable-client-side-monitoring-net-core"></a>啟用用戶端監視的.NET Core

用戶端監視**預設會啟用**使用的.NET Core 應用程式**建議集合**，而不論應用程式設定 'APPINSIGHTS_JAVASCRIPT_ENABLED' 是否存在。

如果基於某些原因，您想要停用用戶端監視：

* 選取 **設定** > **應用程式設定**
   * 在應用程式設定 下加入新**應用程式設定名稱**並**值**:

     名稱： `APPINSIGHTS_JAVASCRIPT_ENABLED`

     值: `false`

   * **儲存**設定並**重新啟動**您的應用程式。

![應用程式的螢幕擷取畫面設定 UI](./media/azure-web-apps/appinsights-javascript-disabled.png)

## <a name="automate-monitoring"></a>自動化監視作業

若要啟用遙測收集與 Application Insights，應用程式設定必須設定：

   ![使用可用的 Application Insights 設定 app Service 應用程式設定](./media/azure-web-apps/application-settings.png)

### <a name="application-settings-definitions"></a>應用程式設定定義

|應用程式設定名稱 |  定義 | 值 |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | 主要擴充功能，控制執行階段監視。 | `~2` |
|XDT_MicrosoftApplicationInsights_Mode |  在預設模式的唯一、 不可或缺的功能會啟用以確保最佳效能。 | `default` 或 `recommended`。 |
|InstrumentationEngine_EXTENSION_VERSION | 如果可控制二進位重寫引擎`InstrumentationEngine`將會開啟。 此設定會影響效能，並會影響冷起始/啟動時間。 | `~1` |
|XDT_MicrosoftApplicationInsights_BaseExtensions | 如果 SQL 和 Azure 資料表的文字控制項將會擷取以及相依性呼叫。 效能警告： 此設定需要`InstrumentationEngine`。 | `~1` |

### <a name="app-service-application-settings-with-azure-resource-manager"></a>應用程式服務應用程式設定使用 Azure Resource Manager

應用程式服務的應用程式設定可管理且設有[Azure Resource Manager 範本](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)。 部署使用 Azure Resource Manager 自動化，或修改現有的資源設定新的 App Service 資源時，就可以使用這個方法。

App service 的應用程式設定 JSON 的基本結構如下：

```JSON
      "resources": [
        {
          "name": "appsettings",
          "type": "config",
          "apiVersion": "2015-08-01",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites', variables('webSiteName'))]"
          ],
          "tags": {
            "displayName": "Application Insights Settings"
          },
          "properties": {
            "key1": "value1",
            "key2": "value2"
          }
        }
      ]

```

如需設定 Application insights 的應用程式設定的 Azure Resource Manager 範本的範例這[樣板](https://github.com/Andrew-MSFT/BasicImageGallery)很有幫助，特別是起的一節[行 238](https://github.com/Andrew-MSFT/BasicImageGallery/blob/c55ada54519e13ce2559823c16ca4f97ddc5c7a4/CoreImageGallery/Deploy/CoreImageGalleryARM/azuredeploy.json#L238)。

### <a name="automate-the-creation-of-an-application-insights-resource-and-link-to-your-newly-created-app-service"></a>自動建立 Application Insights 資源並連結至您新建立的 App Service。

若要建立的 Azure Resource Manager 範本與設定的所有預設 Application Insights 設定，請開始程序，因為如果您要建立新的 Web 應用程式已啟用 Application insights。

選取**自動化選項**

   ![App Service web 應用程式建立功能表](./media/azure-web-apps/create-web-app.png)

此選項會產生所有必要的設定，設定最新的 Azure Resource Manager 範本。

  ![App Service web 應用程式範本](./media/azure-web-apps/arm-template.png)

以下是範例中，所有的執行個體取代`AppMonitoredSite`與您的網站名稱：

```json
{
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "Microsoft.Web/sites",
            "properties": {
                "siteConfig": {
                    "appSettings": [
                        {
                            "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
                            "value": "[reference('microsoft.insights/components/AppMonitoredSite', '2015-05-01').InstrumentationKey]"
                        },
                        {
                            "name": "ApplicationInsightsAgent_EXTENSION_VERSION",
                            "value": "~2"
                        }
                    ]
                },
                "name": "[parameters('name')]",
                "serverFarmId": "[concat('/subscriptions/', parameters('subscriptionId'),'/resourcegroups/', parameters('serverFarmResourceGroup'), '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "hostingEnvironment": "[parameters('hostingEnvironment')]"
            },
            "dependsOn": [
                "[concat('Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "microsoft.insights/components/AppMonitoredSite"
            ],
            "apiVersion": "2016-03-01",
            "location": "[parameters('location')]"
        },
        {
            "apiVersion": "2016-09-01",
            "name": "[parameters('hostingPlanName')]",
            "type": "Microsoft.Web/serverfarms",
            "location": "[parameters('location')]",
            "properties": {
                "name": "[parameters('hostingPlanName')]",
                "workerSizeId": "[parameters('workerSize')]",
                "numberOfWorkers": "1",
                "hostingEnvironment": "[parameters('hostingEnvironment')]"
            },
            "sku": {
                "Tier": "[parameters('sku')]",
                "Name": "[parameters('skuCode')]"
            }
        },
        {
            "apiVersion": "2015-05-01",
            "name": "AppMonitoredSite",
            "type": "microsoft.insights/components",
            "location": "West US 2",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Request_Source": "IbizaWebAppExtensionCreate"
            }
        }
    ],
    "parameters": {
        "name": {
            "type": "string"
        },
        "hostingPlanName": {
            "type": "string"
        },
        "hostingEnvironment": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "sku": {
            "type": "string"
        },
        "skuCode": {
            "type": "string"
        },
        "workerSize": {
            "type": "string"
        },
        "serverFarmResourceGroup": {
            "type": "string"
        },
        "subscriptionId": {
            "type": "string"
        }
    },
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0"
}
```

> [!NOTE]
> 在 「 預設 」 模式中，範本會產生應用程式設定。 此模式會是效能最佳化，雖然您可以修改範本，以啟用任何您喜歡的功能。

### <a name="enabling-through-powershell"></a>透過 PowerShell 啟用

若要讓透過 PowerShell 監視的應用程式，需要變更基礎應用程式設定。 以下是範例，可讓應用程式監視 「 AppMonitoredRG"的資源群組中稱為 「 AppMonitoredSite 」 網站，並設定要傳送至 「 012345678-abcd-ef01-2345年-6789abcd 「 檢測金鑰的資料。

```powershell
$app = Get-AzureRmWebApp -ResourceGroupName "AppMonitoredRG" -Name "AppMonitoredSite" -ErrorAction Stop
$newAppSettings = @{} # case-insensitive hash map
$app.SiteConfig.AppSettings | %{$newAppSettings[$_.Name] = $_.Value} #preserve non Application Insights Application settings.
$newAppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"] = "012345678-abcd-ef01-2345-6789abcd"; # enable the ApplicationInsightsAgent
$newAppSettings["ApplicationInsightsAgent_EXTENSION_VERSION"] = "~2"; # enable the ApplicationInsightsAgent
$app = Set-AzureRmWebApp -AppSettings $newAppSettings -ResourceGroupName $app.ResourceGroup -Name $app.Name -ErrorAction Stop
```

## <a name="upgrade-monitoring-extensionagent"></a>監視擴充功能/代理程式升級

### <a name="upgrading-from-versions-289-and-up"></a>升級版本 2.8.9 和註冊

從版本 2.8.9 升級會自動發生，而不需要任何額外的動作。 新的監視位元會傳遞至目標應用程式服務、 背景，並在應用程式重新啟動它們將會挑選。

若要檢查的延伸模組的版本中，您正在執行瀏覽 `http://yoursitename.scm.azurewebsites.net/ApplicationInsights`

![Url 路徑的螢幕擷取畫面 http://yoursitename.scm.azurewebsites.net/ApplicationInsights](./media/azure-web-apps/extension-version.png)

### <a name="upgrade-from-versions-100---265"></a>從版本 1.0.0-2.6.5 升級

版本 2.8.9 開始使用預先安裝的網站延伸模組。 如果您是較早版本時，您可以更新透過兩種方式之一：

* [透過入口網站啟用升級](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights)。 (即使您將 Application Insights 延伸模組的已安裝的 Azure App Service 時，UI 僅會顯示**啟用** 按鈕。 在幕後，舊的私人網站擴充功能將會移除。）

* [透過 PowerShell 升級](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell):

    1. 將應用程式設定設為啟用 ApplicationInsightsAgent 預先安裝的站台延伸模組。 請參閱[透過 powershell 啟用](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell)。
    2. 手動移除私用網站延伸模組為 Azure App Service 中的 Application Insights 延伸模組。

如果從 2.5.1 之前的版本進行升級，請檢查 ApplicationInsigths dll 會從應用程式 bin 資料夾移除[請參閱疑難排解步驟](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting)。

## <a name="troubleshooting"></a>疑難排解

以下是我們為基礎的延伸模組/代理程式監視適用於.NET 的逐步疑難排解指南和.NET Core 架構的 Azure App Service 上執行的應用程式。

> [!NOTE]
> Java 和 Node.js 應用程式才支援 Azure App Service 上透過手動基礎的 SDK 檢測，因此下列步驟並不適用於這些案例。

1. 應用程式透過監視的核取`ApplicationInsightsAgent`。
    * 請檢查 ' ApplicationInsightsAgent_EXTENSION_AGENT 應用程式設定設為"~ 2"的值。
2. 請確定應用程式符合要監視的需求。
    * 瀏覽至 `https://yoursitename.scm.azurewebsites.net/ApplicationInsights`

    ![螢幕擷取畫面的 https://yoursitename.scm.azurewebsites/applicationinsights結果頁面](./media/azure-web-apps/app-insights-sdk-status.png)

    * 確認`Application Insights Extension Status`是 `Pre-Installed Site Extension, version 2.8.12.1527, is running.`
        * 如果未執行，請遵循[啟用 Application Insights 監視的指示](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights)

    * 確認狀態來源存在，且看起來像： `Status source D:\home\LogFiles\ApplicationInsights\status\status_RD0003FF0317B6_4248_1.json`
        * 如果不存在類似的值，表示應用程式目前未執行或不支援。 若要確認應用程式正在執行，嘗試以手動方式瀏覽應用程式 url/應用程式端點，這可讓執行階段資訊變成可用。

    * 確認`IKeyExists`是 `true`
        * 如果為 false，新增 ' APPINSIGHTS_INSTRUMENTATIONKEY 以您的 ikey 的 guid 至您的應用程式設定。

    * 確認沒有任何項目`AppAlreadyInstrumented`， `AppContainsDiagnosticSourceAssembly`，和`AppContainsAspNetTelemetryCorrelationAssembly`。
        * 如果任何這些項目存在，請從您的應用程式移除下列封裝： `Microsoft.ApplicationInsights`， `System.Diagnostics.DiagnosticSource`，和`Microsoft.AspNet.TelemetryCorrelation`。

下表提供這些值所代表的意義的更詳細的說明，其基礎造成，並建議修正程式：

|問題的值|說明|修正
|---- |----|---|
| `AppAlreadyInstrumented:true` | 這個值表示，延伸模組偵測到 SDK 的某些方面已經在應用程式，且將會退避法。 它可能是因為參考`System.Diagnostics.DiagnosticSource`， `Microsoft.AspNet.TelemetryCorrelation`，或 `Microsoft.ApplicationInsights`  | 移除的參考。 這些參考的一些預設會新增從特定的 Visual Studio 範本，並較舊版本的 Visual Studio 可能會將參考加入`Microsoft.ApplicationInsights`。
|`AppAlreadyInstrumented:true` | 此值也可能被因上述的 dll 檔，從先前的部署的應用程式資料夾中的目前狀態。 | 清除 [應用程式] 資料夾，以確保這些 dll 會移除。|
|`AppContainsAspNetTelemetryCorrelationAssembly: true` | 這個值表示延伸模組偵測到參考`Microsoft.AspNet.TelemetryCorrelation`中應用程式，並將會退避法。 | 移除參考。
|`AppContainsDiagnosticSourceAssembly**:true`|這個值表示延伸模組偵測到參考`System.Diagnostics.DiagnosticSource`中應用程式，並將會退避法。| 移除參考。
|`IKeyExists:false`|這個值表示的檢測金鑰是不存在於 AppSetting， `APPINSIGHTS_INSTRUMENTATIONKEY`。 可能的原因：值可能已被不小心移除，忘記等自動化指令碼中設定的值。 | 請確定設定中的 App Service 應用程式設定。

如需有關 Application Insights 代理程式/擴充功能的最新資訊，請參閱[版本資訊](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/app-insights-web-app-extensions-releasenotes.md)。

## <a name="next-steps"></a>後續步驟

* [在即時應用程式上執行分析工具](../../azure-monitor/app/profiler.md)。
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample) - 使用 Application Insights 監視 Azure Functions
* [能夠讓 Azure 診斷](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md) 傳送至 Application Insights。
* [監視服務健康狀態計量](../../azure-monitor/platform/data-collection.md)，確保您的服務可用且回應正常。
* 每當發生作業事件或計量超過臨界值時，[接收警示通知](../../azure-monitor/platform/alerts-overview.md)。
* [設定可用性 Web 測試](../../azure-monitor/app/monitor-web-app-availability.md) ，以在您的網站關閉時發出警示。