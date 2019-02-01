---
title: 在 Azure Stack 中搭配使用 API 版本設定檔與 .NET SDK | Microsoft Docs
description: 了解如何在 Azure Stack 中搭配使用 API 版本設定檔與 .NET。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2018
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 12/07/2018
ms.openlocfilehash: e335f4cb112c0f029768ccf050f888e00a6efdc1
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/30/2019
ms.locfileid: "55250607"
---
# <a name="use-api-version-profiles-with-net-in-azure-stack"></a>在 Azure Stack 中搭配使用 API 版本設定檔與 .NET

*適用於：Azure Stack 整合式系統和 Azure Stack 開發套件*

適用於 Azure Stack Resource Manager 的 .NET SDK 會提供工具來協助您建置及管理基礎結構。 SDK 中的資源提供者包括計算、網路、儲存體、應用程式服務和 [KeyVault](../../key-vault/key-vault-whatis.md)。 .NET SDK 包含 14 個 NuGet 套件。 每次都必須將這些套件下載到您的專案解決方案中，以合併設定檔資訊。 不過，您可以具體下載要用於 2018-03-01-hybrid 或 2017-03-09-profile 的資源提供者，以將應用程式的記憶體最佳化。 每個套件都包含資源提供者、個別的 API 版本和其所屬的 API 設定檔。 .NET SDK 中的 API 設定檔透過協助您在全域 Azure 資源和 Azure Stack 的資源來回切換，提供混合式雲端開發功能。

## <a name="net-and-api-version-profiles"></a>.NET 和 API 版本設定檔

API 設定檔是資源提供者和 API 版本的組合。 您可以使用 API 設定檔，取得資源提供者套件中每個資源類型的最新、最穩定版本。

-   若要利用所有服務的最新版本，請使用套件的**最新**設定檔。 此設定檔是 **Microsoft.Azure.Management** NuGet 套件的一部分。

-   若要使用與 Azure Stack 相容的服務，請使用 **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01.ResourceProvider.0.9.0-preview.nupkg** 或 **Microsoft.Azure.Management.Profiles.hybrid\_2017\_03\_09.ResourceProvider.0.9.0-preview.nupkg** 套件。

    -   每個設定檔的每個資源提供者各有兩個套件。

    -   請確保上述 NuGet 套件的 **ResourceProvider** 部分會變更為正確的提供者。

-   若要使用服務的最新 API 版本，請使用特定 NuGet 套件的**最新**設定檔。 例如，如果您想要單獨使用計算服務的**最新 API** 版本，請使用**計算**套件的**最新**設定檔。 **最新**設定檔是 **Microsoft.Azure.Management** NuGet 套件的一部分。

-   若要使用特定資源提供者中某個資源類型的特定 API 版本，請使用套件內所定義的特定 API 版本。

您可以在相同應用程式中組合所有選項。

## <a name="install-the-azure-net-sdk"></a>安裝 Azure .NET SDK

1.  安裝 GIT。 如需相關指示，請參閱[使用者入門 - 安裝 Git][]。

2.  若要安裝正確的 NuGet 套件，請參閱[尋找和安裝套件][]。

3.  需要安裝的套件取決於您想要使用的設定檔版本。 設定檔版本的套件名稱是：

    1.  **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01.ResourceProvider.0.9.0-preview.nupkg**

    2.  **Microsoft.Azure.Management.Profiles.hybrid\_2017\_03\_09.ResourceProvider.0.9.0-preview.nupkg**

4.  若要安裝適用於 Visual Studio Code 的正確 NuGet 套件，請參閱下列連結來下載 [NuGet 套件管理員指示][]。

5.  如果無法使用，請建立訂用帳戶，並儲存訂用帳戶識別碼供後續使用。 如需訂用帳戶的建立指示，請參閱[在 Azure Stack 中建立供應項目的訂用帳戶][]。

6.  建立服務主體，並儲存用戶端識別碼和用戶端密碼。 如需如何建立 Azure Stack 服務主體的指示，請參閱[為 Azure Stack 提供應用程式存取][]。 建立服務主體時，「用戶端識別碼」也稱為「應用程式識別碼」。

7.  確保服務主體在訂用帳戶中擁有參與者/擁有者角色。 如需如何對服務主體指派角色的指示，請參閱[為 Azure Stack 提供應用程式存取][]。

## <a name="prerequisites"></a>必要條件

若要搭配使用 .NET Azure SDK 與 Azure Stack，您必須提供下列值，然後以環境變數設定值。 若要設定環境變數，請參閱資料表後面適用於作業系統的指示。

| 值                     | 環境變數   | 說明                                                                                                             |
|---------------------------|-------------------------|-------------------------------------------------------------------------------------------------------------------------|
| 租用戶識別碼                 | AZURE_TENANT_ID       | 您 Azure Stack [*租用戶識別碼*][]的值。                                                                          |
| 用戶端識別碼                 | AZURE_CLIENT_ID       | 在本文上一節建立服務主體時所儲存的服務主體應用程式識別碼。 |
| 訂用帳戶識別碼           | AZURE_SUBSCRIPTION_ID | [*訂用帳戶識別碼*][]是您存取 Azure Stack 中供應項目的方式。                                                      |
| 用戶端密碼             | AZURE_CLIENT_SECRET   | 建立服務主體時所儲存的服務主體應用程式祕密。                                      |
| Resource Manager 端點 | ARM_ENDPOINT           | 請參閱 [*Azure Stack Resource Manager 端點*][]。                                                                    |
| 位置                  | RESOURCE_LOCATION     | Azure Stack 的位置。

若要尋找 Azure Stack 的租用戶識別碼，請遵循[此處](../azure-stack-csp-ref-operations.md)所提供的指示。 若要設定環境變數，請執行下列步驟：

### <a name="microsoft-windows"></a>Microsoft Windows

若要在 Windows 命令提示字元中設定環境變數，請使用下列格式：

```shell
Set Azure_Tenant_ID=Your_Tenant_ID
```

### <a name="macos-linux-and-unix-based-systems"></a>MacOS、Linux 和以 Unix 為基礎的系統

在以 Unix 為基礎的系統中，您可以使用下列命令：

```shell
Export Azure_Tenant_ID=Your_Tenant_ID
```

### <a name="the-azure-stack-resource-manager-endpoint"></a>Azure Stack Resource Manager 端點

Microsoft Azure Resource Manager 是可讓系統管理員進行部署、管理及監視 Azure 資源的管理架構。 Azure Resource Manager 能夠以群組方式處理這些工作，而非個別單獨作業的方式。

您可以從 Resource Manager 端點取得中繼資料資訊。 此端點會傳回 JSON 檔案，該檔案中有執行您程式碼所需的資訊。

請注意下列考量：

- Azure Stack 開發套件 (ASDK) 中的 **ResourceManagerUrl** 為： https://management.local.azurestack.external/

- 整合系統中的 **ResourceManagerUrl** 為：`https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/` 若要擷取所需的中繼資料：`<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`

範例 JSON 檔案：

```json
{ 
   "galleryEndpoint": "https://portal.local.azurestack.external:30015/",
   "graphEndpoint": "https://graph.windows.net/",
   "portal Endpoint": "https://portal.local.azurestack.external/",
   "authentication": 
      {
      "loginEndpoint": "https://login.windows.net/",
      "audiences": ["https://management.yourtenant.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
      }
}
```

## <a name="existing-api-profiles"></a>現有的 API 設定檔

1.  **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01.*ResourceProvider*.0.9.0-preview.nupkg**:針對 Azure Stack 建置的最新設定檔。 只要您是在 1808 以上的戳記上，使用此設定檔就能讓服務與 Azure Stack 有最高的相容性。

2.  **Microsoft.Azure.Management.Profiles.hybrid\_2017\_03\_09.*ResourceProvider*.0.9.0-preview.nupkg**:如果您的戳記低於 1808 組建，請使用此設定檔。

3.  **最新**︰包含所有服務最新版本的設定檔。 使用所有服務的最新版本。 此設定檔是 **Microsoft.Azure.Management** NuGet 套件的一部分。

如需有關 Azure Stack 和 API 設定檔的詳細資訊，請參閱 [API 設定檔的摘要][]。

## <a name="azure-net-sdk-api-profile-usage"></a>Azure .NET SDK API 設定檔使用方式

以下是應該用來將資源管理用戶端具現化的程式碼。 類似的程式碼可用來將其他資源提供者 (例如計算、網路及儲存體) 用戶端具現化。 

```csharp
var client = new ResourceManagementClient(armEndpoint, credentials)
{
    SubscriptionId = subscriptionId
};
```

上方程式碼中的 `credentials` 參數是將用戶端具現化的必要參數。 下列程式碼會依租用戶識別碼和服務主體來產生驗證權杖。

```csharp
var azureStackSettings = getActiveDirectoryServiceSettings(armEndpoint);
var credentials = ApplicationTokenProvider.LoginSilentAsync(tenantId, servicePrincipalId, servicePrincipalSecret, azureStackSettings).GetAwaiter().GetResult();
```
程式碼中的 `getActiveDirectoryServiceSettings` 呼叫會從中繼資料端點中擷取 Azure Stack 端點。 它會從發出的呼叫中指出環境變數： 

```csharp
public static ActiveDirectoryServiceSettings getActiveDirectoryServiceSettings(string armEndpoint)
{
    var settings = new ActiveDirectoryServiceSettings();
    try
    {
        var request = (HttpWebRequest)HttpWebRequest.Create(string.Format("{0}/metadata/endpoints?api-version=1.0", armEndpoint));
        request.Method = "GET";
        request.UserAgent = ComponentName;
        request.Accept = "application/xml";
        using (HttpWebResponse response = (HttpWebResponse)request.GetResponse())
        {
            using (StreamReader sr = new StreamReader(response.GetResponseStream()))
            {
                var rawResponse = sr.ReadToEnd();
                var deserialized = JObject.Parse(rawResponse);
                var authenticationObj = deserialized.GetValue("authentication").Value<JObject>();
                var loginEndpoint = authenticationObj.GetValue("loginEndpoint").Value<string>();
                var audiencesObj = authenticationObj.GetValue("audiences").Value<JArray>();
                settings.AuthenticationEndpoint = new Uri(loginEndpoint);
                settings.TokenAudience = new Uri(audiencesObj[0].Value<string>());
                settings.ValidateAuthority = loginEndpoint.TrimEnd('/').EndsWith("/adfs", StringComparison.OrdinalIgnoreCase) ? false : true;
            }
        }
    }
    catch (Exception ex)
    {
        Console.WriteLine(String.Format("Could not get AD service settings. Exception: {0}", ex.Message));
    }
    return settings;
}
```
這可讓您使用 API 設定檔 NuGet 套件，將應用程式成功地部署至 Azure Stack。

## <a name="samples-using-api-profiles"></a>使用 API 設定檔的範例

若要使用 .NET 和 Azure Stack API 設定檔來建立解決方案，可以使用下列範例作為參考。
- [管理資源群組](https://github.com/Azure-Samples/hybrid-resources-dotnet-manage-resource-group)
- [管理儲存體帳戶](https://github.com/Azure-Samples/hybird-storage-dotnet-manage-storage-accounts)
- [管理虛擬機器](https://github.com/Azure-Samples/hybrid-compute-dotnet-manage-vm)

## <a name="next-steps"></a>後續步驟

如需 API 設定檔的詳細資訊，請參閱：

- [在 Azure Stack 中管理 API 版本設定檔](azure-stack-version-profiles.md)
- [設定檔所支援的資源提供者 API 版本](azure-stack-profiles-azure-resource-manager-versions.md)

  [使用者入門 - 安裝 Git]: https://git-scm.com/book/en/v2/Getting-Started-Installing-Git
  [尋找和安裝套件]: /nuget/tools/package-manager-ui
  [NuGet 套件管理員指示]: https://marketplace.visualstudio.com/items?itemName=jmrog.vscode-nuget-package-manager
  [在 Azure Stack 中建立供應項目的訂用帳戶]: ../azure-stack-subscribe-plan-provision-vm.md
  [為 Azure Stack 提供應用程式存取]: ../azure-stack-create-service-principals.md
  [租用戶識別碼]: ../azure-stack-identity-overview.md
  [訂用帳戶識別碼]: ../azure-stack-plan-offer-quota-overview.md#subscriptions
  [*Azure Stack Resource Manager 端點*]: ../user/azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint
  [API 設定檔的摘要]: ../user/azure-stack-version-profiles.md#summary-of-api-profiles
  [Test Project to Virtual Machine, vNet, resource groups, and storage account]: https://github.com/seyadava/azure-sdk-for-net-samples/tree/master/TestProject
  [Use Azure PowerShell to create a service principal with a certificate]: ../azure-stack-create-service-principals.md
  [Run unit tests with Test Explorer.]: /visualstudio/test/run-unit-tests-with-test-explorer?view=vs-2017
