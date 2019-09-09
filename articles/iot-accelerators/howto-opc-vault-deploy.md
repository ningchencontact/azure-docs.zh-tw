---
title: 如何部署 OPC 保存庫憑證管理服務-Azure |Microsoft Docs
description: 如何從頭開始部署 OPC 保存庫憑證管理服務。
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 1625f0e6f9bfe8297cae2770e63107bf4f19f95e
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/23/2019
ms.locfileid: "70012992"
---
# <a name="build-and-deploy-the-opc-vault-certificate-management-service"></a>建立並部署 OPC 保存庫憑證管理服務

本文說明如何在 Azure 中部署 OPC 保存庫憑證管理服務。

> [!NOTE]
> 如需部署詳細資料和指示的詳細資訊，請參閱 GitHub [OPC 保存庫儲存](https://github.com/Azure/azure-iiot-opc-vault-service)機制。

## <a name="prerequisites"></a>必要條件

### <a name="install-required-software"></a>安裝所需的軟體

組建和部署作業目前僅限於 Windows。
這些範例全都是針對C# .net Standard 所撰寫，這是建立服務和部署範例所需的。
.Net Standard 所需的所有工具都隨附 .Net Core 工具。 如需瞭解您的需求，請參閱[這裡](https://docs.microsoft.com/dotnet/articles/core/getting-started)。

1. [安裝 .Net Core 2.1 +][dotnet-install]。
2. [安裝 Docker][docker-url]（選擇性，只有在需要本機 docker 組建時）。
4. 安裝[適用于 PowerShell 的 Azure 命令列工具][powershell-install]。
5. 註冊[Azure 訂][azure-free]用帳戶。

### <a name="clone-the-repository"></a>複製儲存機制

如果您尚未這麼做，請複製此 GitHub 存放庫。  開啟命令提示字元或終端機，然後執行：

```bash
git clone https://github.com/Azure/azure-iiot-opc-vault-service
cd azure-iiot-opc-vault-service 
```

或直接在 Visual Studio 2017 中複製存放庫。

### <a name="build-and-deploy-the-azure-service-on-windows"></a>在 Windows 上建立及部署 Azure 服務

Powershell 腳本提供簡單的方法來部署 OPC 保存庫微服務和應用程式。<br>

1. 在存放庫根目錄中開啟 Powershell 視窗。 
3. 移至 [部署] 資料夾`cd deploy`
3. 選擇的名稱`myResourceGroup`可能會與其他已部署的網頁發生衝突。 請參閱[下面](#website-name-already-in-use)的如何根據資源群組的名稱選擇網頁名稱。
5. 開始進行部署`.\deploy.ps1` ，以進行互動式安裝<br>
或是輸入完整的命令列：  
`.\deploy.ps1  -subscriptionName "MySubscriptionName" -resourceGroupLocation "East US" -tenantId "myTenantId" -resourceGroupName "myResourceGroup"`
7. 如果您打算使用此部署進行開發，請`-development 1`新增來啟用 Swagger UI，並部署 debug 組建。
6. 依照腳本中的指示登入您的訂用帳戶，並提供其他資訊。
9. 成功完成組建和部署作業之後，您應該會看到下列訊息：
   ```
   To access the web client go to:
   https://myResourceGroup.azurewebsites.net

   To access the web service go to:
   https://myResourceGroup-service.azurewebsites.net

   To start the local docker GDS server:
   .\myResourceGroup-dockergds.cmd

   To start the local dotnet GDS server:
   .\myResourceGroup-gds.cmd
   ```

如果您遇到[問題，請遵循下列步驟。](#troubleshooting-deployment-failures)

8. 開啟您慣用的瀏覽器，並開啟應用程式頁面：`https://myResourceGroup.azurewebsites.net`
8. 提供 web 應用程式和 OPC 保存庫微服務幾分鐘的時間，以在部署之後準備。 Web 首頁可能會在第一次使用時停止回應，直到您取得第一個回應為止。
11. 若要查看 Swagger Api，請開啟：`https://myResourceGroup-service.azurewebsites.net`
13. 若要啟動具有 dotnet start `.\myResourceGroup-gds.cmd`或 with docker start `.\myResourceGroup-dockergds.cmd`的本機 GDS 伺服器。

身為 sidenote，您可以使用完全相同的設定來重新部署組建。 請注意，這類作業會更新所有應用程式秘密，而且可能會重設 Azure Active Directory （Azure AD）應用程式註冊中的某些設定。

您也可以只重新部署 web 應用程式二進位檔。 具有參數`-onlyBuild 1`的新服務 zip 套件，並將應用程式部署至 web 應用程式。

部署成功之後，您可以隨意開始使用服務：[如何管理 OPC 保存庫憑證管理服務](howto-opc-vault-manage.md)

## <a name="delete-the-services-from-the-subscription"></a>從訂用帳戶刪除服務

1. 登入 Azure 入口網站： `https://portal.azure.com`。
2. 移至部署服務的資源群組。
3. 選取`Delete resource group`並確認。
4. 一小段時間之後，所有已部署的服務元件都會被刪除。
5. 現在請移`Azure Active Directory/App registrations`至。
6. 針對每個已部署的資源群組，應該會列出三個註冊， `resourcegroup-client`其`resourcegroup-module`名稱`resourcegroup-service`如下：、、。
每個註冊都必須個別刪除。
7. 現在已移除所有已部署的元件。

## <a name="troubleshooting-deployment-failures"></a>針對部署失敗進行疑難排解

### <a name="resource-group-name"></a>資源群組名稱

請確定您使用簡短且簡單的資源組名。  此名稱也會用來命名資源和服務 url 前置詞，因此，它必須符合資源命名需求。  

### <a name="website-name-already-in-use"></a>網站名稱已在使用中

網站的名稱可能已在使用中。  如果您遇到此錯誤，您需要使用不同的資源組名。 部署腳本所使用的主機名稱為： https://resourcegroupname.azurewebsites.net 和。 https://resourgroupname-service.azurewebsites.net
服務的其他名稱是由簡短名稱雜湊的組合所建立，而且不太可能與其他服務衝突。

### <a name="azure-active-directory-azure-ad-registration"></a>Azure Active Directory （Azure AD）註冊 

部署腳本會嘗試在 Azure Active Directory 中註冊三個 Azure AD 應用程式。  
視所選 Azure AD 租使用者中的許可權而定，這項作業可能會失敗。   有兩個選項：

1. 如果您從租使用者清單中選擇了 [Azure AD]，請重新開機腳本，並從清單中選擇不同的租使用者。
2. 或者，在另一個訂用帳戶中部署私用 Azure AD 租使用者、重新開機腳本，然後選取加以使用。

## <a name="deployment-script-options"></a>部署腳本選項

腳本會採用下列參數：


```
-resourceGroupName
```

可以是現有或新資源群組的名稱。

```
-subscriptionId
```


選擇性，這是將部署資源的訂用帳戶識別碼。

```
-subscriptionName
```


或者訂用帳戶名稱。

```
-resourceGroupLocation
```


選擇性，資源群組位置。 如果指定，將會嘗試在這個位置建立新的資源群組。


```
-tenantId
```


Azure AD 要使用的租使用者。 

```
-development 0|1
```

選擇性，用於部署以進行開發。 使用 debug 組建，並將 ASP.Net 環境設定為開發。 建立 ' .publishsettings ' 以匯入 Visual Studio 2017，讓它能夠直接部署應用程式和服務。

```
-onlyBuild 0|1
```

選擇性，用於重建和只重新部署 web 應用程式，以及重建 docker 容器。

[azure-free]:https://azure.microsoft.com/free/
[powershell-install]:https://azure.microsoft.com/downloads/#powershell
[docker-url]: https://www.docker.com/
[dotnet-install]: https://www.microsoft.com/net/learn/get-started

## <a name="next-steps"></a>後續步驟

既然您已瞭解如何從頭開始部署 OPC 保存庫，以下是建議的後續步驟：

> [!div class="nextstepaction"]
> [管理 OPC 保存庫](howto-opc-vault-manage.md)
