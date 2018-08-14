---
title: Azure 快速入門 - 設定 Azure Web 應用程式以從 Key Vault 設定及擷取祕密 | Microsoft Docs
description: 快速入門會示範如何設定 ASP.Net Core 應用程式以從 Key Vault 設定及擷取祕密
services: key-vault
author: prashanthyv
manager: sumedhb
ms.service: key-vault
ms.topic: quickstart
ms.date: 07/24/2018
ms.author: barclayn
ms.custom: mvc
ms.openlocfilehash: 8b5624ae3083d92213b4ee919dc0860bf5ff4ab7
ms.sourcegitcommit: fc5555a0250e3ef4914b077e017d30185b4a27e6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2018
ms.locfileid: "39480197"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-a-net-web-app"></a>快速入門：使用 .NET Web 應用程式從 Azure Key Vault 設定及擷取祕密

在本快速入門中，我們將引導您完成讓 Azure Web 應用程式使用受控服務識別從 Key Vault 讀取資訊所需的步驟。 您會了解如何：

> [!div class="checklist"]
> * 建立 Key Vault。
> * 將密碼儲存在 Key Vault 中。
> * 從 Key Vault 擷取祕密。
> * 建立 Azure Web 應用程式。
> * [啟用受控服務識別](../active-directory/managed-service-identity/overview.md)。
> * 授與 Web 應用程式從 Key Vault 讀取資料所需的權限。

在我們繼續之前，請閱讀[基本概念](key-vault-whatis.md#basic-concepts)，特別是[受控服務識別](../active-directory/managed-service-identity/overview.md)

## <a name="prerequisites"></a>必要條件

* 在 Windows 上：
  * [Visual Studio 2017 15.7.3 版或更新版本](https://www.microsoft.com/net/download/windows)，具有下列工作負載：
    * ASP.NET 和 Web 開發
    * .NET Core 跨平台開發
  * [.NET Core 2.1 SDK 或更新版本](https://www.microsoft.com/net/download/windows)

* 在 Mac 上：
  * https://visualstudio.microsoft.com/vs/mac/

* 所有平台：
  * 請從[這裡](https://git-scm.com/downloads)下載 GIT。
  * Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。
  * [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 您需要 Azure CLI 2.0.4 版或更新版本。 這個工具適用於 Windows、Mac 和 Linux

## <a name="login-to-azure"></a>登入 Azure

   若要使用 CLI 登入 Azure，您可以輸入：

```azurecli
az login
```

## <a name="create-a-resource-group"></a>建立資源群組

使用 [az group create](/cli/azure/group#az-group-create) 命令來建立資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。

請選取資源群組名稱，並填入預留位置。
下列範例會在 eastus 位置建立名為 <YourResourceGroupName> 的資源群組。

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "East US"
```

本文中會使用您剛建立的資源群組。

## <a name="create-an-azure-key-vault"></a>建立 Azure Key Vault

接下來，您會在上一個步驟中建立的資源群組中建立 Key Vault。 請提供下列資訊：

* Vault 名稱 - **請在這裡選取 Key Vault 名稱**。 Key Vault 名稱必須是包含 3-24 個字元的字串，而且只可使用 (0-9、a-z、A-Z 及 -)。
* 資源群組名稱 - **請在這裡選取資源群組名稱**。
* 位置 - **美國東部**。

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "East US"
```

此時，您的 Azure 帳戶是唯一獲得授權在此新保存庫上執行任何作業的帳戶。

## <a name="add-a-secret-to-key-vault"></a>將祕密新增至 Key Vault

我們將新增密碼，以協助說明其運作方式。 您可能正在儲存 SQL 連接字串，或任何您必須安全保存但可供您的應用程式使用的其他資訊。 在本教學課程中，密碼會稱為 **AppSecret**，並將在其中儲存 **MySecret** 的值。

輸入下列命令，以在 Key Vault 中建立稱為 **AppSecret** 並將儲存 **MySecret** 值的祕密：

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

若要以純文字檢視包含在祕密中的值：

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

此命令會顯示祕密資訊，包括 URI。 完成這些步驟之後，您的 Azure Key Vault 中應該會有密碼的 URI。 請記下此資訊。 您在稍後的步驟中需要此資訊。

## <a name="clone-the-repo"></a>複製存放庫

複製存放庫以便製作本機複本，讓您可以藉由執行下列命令以編輯來源：

```
git clone https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart.git
```

## <a name="open-and-edit-the-solution"></a>開啟及編輯解決方案

編輯 program.cs 檔案，以便使用您的特定金鑰保存庫名稱來執行範例。

1. 瀏覽至 key-vault-dotnet-core-quickstart 資料夾
2. 在 Visual Studio 2017 中開啟 key-vault-dotnet-core-quickstart.sln 檔案
3. 開啟 Program.cs 檔案，並且使用您稍早建立的 Key Vault 名稱更新預留位置 <YourKeyVaultName>。

這個解決方案會使用 [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) 和 [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) NuGet 程式庫

## <a name="run-the-app"></a>執行應用程式

從 Visual Studio 2017 的主功能表中，選擇 [偵錯 > 啟動但不偵錯]。 當瀏覽器出現時，瀏覽至 [關於] 頁面。 AppSecret 的值隨即顯示。

## <a name="publish-the-web-application-to-azure"></a>將 Web 應用程式發佈至 Azure

我們會將此應用程式發佈至 Azure，以 Web 應用程式的形式即時查看，同時查看擷取祕密值

1. 在 Visual Studio 中，選取 **key-vault-dotnet-core-quickstart** 專案。
2. 選取 [發佈]，然後選取 [開始]。
3. 建立新的 **App Service**，然後選取 [發佈]。
4. 將應用程式名稱變更為 "keyvaultdotnetcorequickstart"
5. 選取 [建立] 。

>[!VIDEO https://sec.ch9.ms/ch9/e93d/a6ac417f-2e63-4125-a37a-8f34bf0fe93d/KeyVault_high.mp4]

## <a name="enable-managed-service-identities-msi"></a>啟用受控服務識別 (MSI)

Azure Key Vault 可安全地儲存認證和其他金鑰及祕密，但是您的程式碼必須向 Azure Key Vault 進行驗證，才可擷取這些項目。 受控服務識別 (MSI) 可以輕易地解決此問題，因為 MSI 可在 Azure Active Directory (Azure AD) 中將自動受控識別提供給 Azure 服務。 您可以使用此身分識別來完成任何支援 Azure AD 驗證的服務驗證 (包括 Key Vault)，不需要任何您程式碼中的認證。

1. 返回 Azure CLI
2. 執行 assign-identity 命令來建立此應用程式的識別：

```azurecli
az webapp identity assign --name "keyvaultdotnetcorequickstart" --resource-group "<YourResourceGroupName>"
```

>[!NOTE]
>此命令等同於前往入口網站，並在 Web 應用程式屬性中將 [受控服務識別] 切換為 [開啟]。

## <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>將權限指派給您的應用程式，以便從 Key Vault 讀取秘密

當您 [將應用程式發佈到 Azure][] 時，請記下輸出。 其格式應為：
        
        {
          "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "SystemAssigned"
        }
        
然後，使用 Key Vault 名稱以及從上方複製的 PrincipalId 值，來執行這個命令：

```azurecli

az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get

```

## <a name="next-steps"></a>後續步驟

* [Azure Key Vault 首頁](https://azure.microsoft.com/services/key-vault/)
* [Azure Key Vault 文件](https://docs.microsoft.com/azure/key-vault/)
* [適用於 .NET 的 Azure SDK](https://github.com/Azure/azure-sdk-for-net)
* [Azure REST API 參考](https://docs.microsoft.com/rest/api/keyvault/)
