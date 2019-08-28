---
title: 使用 Chef 的 Azure 虛擬機器部署 | Microsoft Docs
description: 了解如何使用 Chef 執行自動化的虛擬機器部署和設定 Microsoft Azure
services: virtual-machines-windows
documentationcenter: ''
author: diegoviso
manager: gwallace
tags: azure-service-management,azure-resource-manager
editor: ''
ms.assetid: 0b82ca70-89ed-496d-bb49-c04ae59b4523
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.topic: article
ms.date: 07/09/2019
ms.author: diviso
ms.openlocfilehash: 5cbf53da5a0af0a511350b9f30153e2fefe72dcf
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70080130"
---
# <a name="automating-azure-virtual-machine-deployment-with-chef"></a>使用 Chef 自動化 Azure 虛擬機器部署

Chef 是個很棒的工具，可提供自動化和所需狀態組態。

在我們最新的雲端 API 版本中，Chef 提供與 Azure 的緊密整合，您可以透過單一命令佈建及部署組態狀態。

在本文中，您將設定可佈建 Azure 虛擬機器的 Chef 環境，並會逐步指導您建立原則或 "CookBook"，然後將此操作手冊部署到 Azure 虛擬機器中。

## <a name="chef-basics"></a>Chef 基本概念
開始之前，請[檢閱 Chef 的基本概念](https://www.chef.io/chef) \(英文\)。

下圖說明高層級的 Chef 架構。

![][2]

Chef 有三個主要的架構元件：Chef Server、Chef 用戶端 (節點) 及 Chef 工作站。

Chef 伺服器是管理的重點，Chef 伺服器包含兩個選項：代管解決方案或內部部署解決方案。

Chef 用戶端 (節點)是位於您所管理之伺服器上的代理程式。

Chef 工作站，它是您建立原則和執行管理命令的管理工作站以及 Chef 工具軟體套件的名稱。

一般而言，您會看到_您的工作站_作為執行動作的位置，_Chef 工作站_作為軟體套件。
例如，您將下載 knife 命令作為 _Chef 工作站_的一部分，但是您從_工作站_執行 knife 命令來管理基礎結構。

此外，Chef 也會使用 "Cookbooks" 和 "Recipes" 的概念，這些概念實際上是我們定義並套用至伺服器的原則。

## <a name="preparing-your-workstation"></a>準備工作站

首先，藉由建立可儲存 Chef 設定檔和 cookbook 的目錄來準備您的工作站。

建立名為 C:\chef。的目錄

將最新的[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)版本下載並安裝到您的工作站。

## <a name="configure-azure-service-principal"></a>設定 Azure 服務主體

最簡單的條款和 Azure 服務主體是服務帳戶。   我們將使用服務主體, 協助我們從我們的 Chef 工作站建立 Azure 資源。  若要建立具有必要許可權的相關服務主體, 我們需要在 PowerShell 中執行下列命令:
 
```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<yourSubscriptionName>"
$myApplication = New-AzureRmADApplication -DisplayName "automation-app" -HomePage "https://chef-automation-test.com" -IdentifierUris "https://chef-automation-test.com" -Password "#1234p$wdchef19"
New-AzureRmADServicePrincipal -ApplicationId $myApplication.ApplicationId
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $myApplication.ApplicationId
```

請記下您的 SubscriptionID、TenantID、ClientID 和用戶端密碼 (您在上面設定的密碼), 稍後將會用到。 

## <a name="setup-chef-server"></a>設定 Chef 伺服器 URL

本指南假設您將會註冊受控 Chef。

如果您尚未使用 Chef 伺服器，則可以：

* 註冊[受控 Chef](https://manage.chef.io/signup)，這是開始使用 Chef 的最快方式。
* 按照 [Chef Docs](https://docs.chef.io/) 中的[安裝說明](https://docs.chef.io/install_server.html)，在 Linux 型電腦上安裝獨立的 Chef 伺服器。

### <a name="creating-a-hosted-chef-account"></a>建立受控 Chef 帳戶

在[這裡](https://manage.chef.io/signup)註冊受控 Chef 帳戶。

在註冊過程中，我們將要求您建立新的組織。

![][3]

建立組織後，請下載「入門套件」。

![][4]

> [!NOTE]
> 如果您收到提示，警告您將重新設定金鑰，您可以繼續作業，因為我們尚未設定任何基礎結構。
>

此入門套件 zip 檔案包含 `.chef` 目錄中您的組織組態檔和使用者金鑰。

`organization-validator.pem` 必須個別下載，因為它是私用金鑰，且私密金鑰不應該儲存在 Chef 伺服器上。 在 [ [Chef 管理](https://manage.chef.io/)] 中, 移至 [管理] 區段, 然後選取 [重設驗證金鑰], 它會提供檔案供您個別下載。 將檔案儲存至 c:\chef。

### <a name="configuring-your-chef-workstation"></a>設定 Chef 工作站

將 chef-starter.zip 的內容解壓縮到 c:\chef。

將 chef-starter\chef-repo\.chef 下的所有檔案複製到您的 c:\chef 目錄。

將 `organization-validator.pem` 檔案複製到 c:\chef (如果它儲存在 c:\Downloads 中)

您的目錄現在看起來應該類似以下範例。

```powershell
    Directory: C:\Users\username\chef

Mode           LastWriteTime    Length Name
----           -------------    ------ ----
d-----    12/6/2018   6:41 PM           .chef
d-----    12/6/2018   5:40 PM           chef-repo
d-----    12/6/2018   5:38 PM           cookbooks
d-----    12/6/2018   5:38 PM           roles
-a----    12/6/2018   5:38 PM       495 .gitignore
-a----    12/6/2018   5:37 PM      1678 azuredocs-validator.pem
-a----    12/6/2018   5:38 PM      1674 user.pem
-a----    12/6/2018   5:53 PM       414 knife.rb
-a----    12/6/2018   5:38 PM      2341 README.md
```

您現在應該在 c:\chef 的根目錄中有五個檔案和四個目錄 (包括空的 chef-repo 目錄)。

### <a name="edit-kniferb"></a>編輯 knife.rb

PEM 檔案包含可進行通訊的組織和管理員私密金鑰，而 knife.rb檔案則包含 knife 組態。 我們將需要編輯 knife.rb 檔案。

在您選擇的編輯器中開啟 knife.rb 檔案。 未變更的檔案應如下所示：

```rb
current_dir = File.dirname(__FILE__)
log_level           :info
log_location        STDOUT
node_name           "mynode"
client_key          "#{current_dir}/user.pem"
chef_server_url     "https://api.chef.io/organizations/myorg"
cookbook_path       ["#{current_dir}/cookbooks"]
```

將下列資訊新增至 knife.rb：

validation_client_name   "myorg-validator"

validation_key           "#{current_dir}/myorg.pem"

knife[:azure_tenant_id] =         "0000000-1111-aaaa-bbbb-222222222222"

knife[:azure_subscription_id] =   "11111111-bbbbb-cccc-1111-222222222222"

knife[:azure_client_id] =         "11111111-bbbbb-cccc-1111-2222222222222"

knife[:azure_client_secret] =     "#1234p$wdchef19"


這些行會確保刀行會參考 c:\chef\cookbooks 下的操作手冊目錄, 也會使用您在 Azure 作業期間建立的 Azure 服務主體。

現在 knife.rb 檔案看起來應該會類似下列範例：

![][14]

<!--- Giant problem with this section: Chef 12 uses a config.rb instead of knife.rb
// However, the starter kit hasn't been updated
// So, I don't think this will even work on the modern Chef -->

<!--- update image [6] knife.rb -->

```rb
current_dir = File.dirname(__FILE__)
log_level                :info
log_location             STDOUT
node_name                "myorg"
client_key               "#{current_dir}/myorg.pem"
validation_client_name   "myorg-validator"
validation_key           "#{current_dir}/myorg-validator.pem"
chef_server_url          "https://api.chef.io/organizations/myorg"
cookbook_path            ["#{current_dir}/../cookbooks"]
knife[:azure_tenant_id] = "0000000-1111-aaaa-bbbb-222222222222"
knife[:azure_subscription_id] = "11111111-bbbbb-cccc-1111-222222222222"
knife[:azure_client_id] = "11111111-bbbbb-cccc-1111-2222222222222"
knife[:azure_client_secret] = "#1234p$wdchef19"
```

## <a name="install-chef-workstation"></a>安裝 Chef 工作站

接下來，[下載並安裝](https://downloads.chef.io/chef-workstation/) Chef 工作站。
安裝 Chef 工作站的預設位置。 此安裝可能需要幾分鐘的時間。

在桌面上，您將看到 "CW PowerShell"，這是一個載入與 Chef 產品互動所需之工具的環境。 CW PowerShell 會提供新的臨機操作命令, 例如`chef-run` , 以及傳統的 Chef CLI 命令`chef`(例如)。 使用 `chef -v` 查看已安裝的 Chef 工作站版本和 Chef 工具。 您還可以從 Chef 工作站應用程式中選取「關於 Chef 工作站 」，以檢查您的工作站的版本。

`chef --version` 應該會傳回類似下列內容：

```
Chef Workstation: 0.4.2
  chef-run: 0.3.0
  chef-client: 15.0.300
  delivery-cli: 0.0.52 (9d07501a3b347cc687c902319d23dc32dd5fa621)
  berks: 7.0.8
  test-kitchen: 2.2.5
  inspec: 4.3.2
```

> [!NOTE]
> 路徑的順序很重要！ 如果您的 opscode 路徑順序不正確，則會出現問題。
>

在繼續之前，請重新啟動您的工作站。

### <a name="install-knife-azure"></a>安裝 Knife Azure

本教學課程假設您使用 Azure Resource Manager 來與您的虛擬機器互動。

安裝 Knife Azure 延伸模組。 這會以「Azure 外掛程式」的形式提供 Knife。

執行下列命令。

    chef gem install knife-azure ––pre

> [!NOTE]
> -pre 引數可確保您會收到最新的 RC 版本 Knife Azure 外掛程式，該版本可讓您存取最新的 API 組合。
>
>

同時也可能安裝多個相依性。

![][8]

若要確保一切都已正確設定，請執行下列命令。

    knife azurerm server list

如果一切都已正確設定，您會在捲動時看到可用的 Azure 映像清單。

恭喜！ 您的工作站已設定！

## <a name="creating-a-cookbook"></a>建立 Cookbook

Chef 會使用 Cookbook 來定義一組您想在受控用戶端上執行的命令。 建立 Cookbook 非常簡單，只要使用 **chef generate cookbook** 命令來產生 Cookbook 範本。 此 cookbook 適用於自動部署 IIS Web 伺服器。

在 C:\Chef 目錄下，執行下列命令。

    chef generate cookbook webserver

此命令會在 C:\Chef\cookbooks\webserver 目錄下產生一組檔案。 接下來，定義一組需要 Chef 用戶端在受控虛擬機器上執行的命令。

這些命令會儲存在 default.rb. 在這個檔案中，請定義一組用來安裝 IIS、啟動 IIS 並將範本檔案複製到 wwwroot 資料夾的命令。

修改 C:\chef\cookbooks\webserver\recipes\default.rb 檔並加入下列幾行程式碼。

    powershell_script 'Install IIS' do
         action :run
         code 'add-windowsfeature Web-Server'
    end

    service 'w3svc' do
         action [ :enable, :start ]
    end

    template 'c:\inetpub\wwwroot\Default.htm' do
         source 'Default.htm.erb'
         rights :read, 'Everyone'
    end

完成後，請儲存檔案。

## <a name="creating-a-template"></a>建立範本
在此步驟中，您將產生作為 default.html 頁面的範本檔案。

執行下列命令以產生範本：

    chef generate template webserver Default.htm

瀏覽至 `C:\chef\cookbooks\webserver\templates\default\Default.htm.erb` 檔案。 加入一些簡單的 "Hello World" HTML 程式碼來編輯檔案，然後儲存檔案。

## <a name="upload-the-cookbook-to-the-chef-server"></a>將 Cookbook 上傳到 Chef 伺服器
在此步驟中，您會將在本機電腦上建立的 Cookbook 複本，上傳到 Chef 代管伺服器。 上傳後，Cookbook 便會出現在 [原則] 索引標籤底下。

    knife cookbook upload webserver

![][9]

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>使用 Knife Azure 部署虛擬機器
部署 Azure 虛擬機器，並套用 "Webserver" Cookbook，如此便會安裝 IIS Web 服務和預設網頁。

若要這麼做, 請使用**刀 azurerm 伺服器 create**命令。

接下來會顯示此命令的範例。

    knife azurerm server create `
    --azure-resource-group-name rg-chefdeployment `
    --azure-storage-account store `
    --azure-vm-name chefvm `
    --azure-vm-size 'Standard_DS2_v2' `
    --azure-service-location 'westus' `
    --azure-image-reference-offer 'WindowsServer' `
    --azure-image-reference-publisher 'MicrosoftWindowsServer' `
    --azure-image-reference-sku '2016-Datacenter' `
    --azure-image-reference-version 'latest' `
    -x myuser -P myPassword123 `
    --tcp-endpoints '80,3389' `
    --chef-daemon-interval 1 `
    -r "recipe[webserver]"


上述範例會建立 Standard_DS2_v2 虛擬機器, 並在美國西部區域中安裝 Windows Server 2016。 替換特定變數並執行。

> [!NOTE]
> 透過命令列，我還打算使用 -tcp-endpoints 參數將端點網路篩選規則自動化。 我已開啟埠80和 3389, 以提供網頁和 RDP 會話的存取權。
>
>

執行命令後，前往 Azure 入口網站，以查看您的機器已經開始佈建。

![][15]

命令提示字元會顯示下一步。

![][16]

部署完成後, 新虛擬機器的公用 IP 位址將會在部署完成時顯示, 您可以複製並貼到網頁瀏覽器中, 並查看您所部署的網站。 當我們部署虛擬機器時, 我們開啟了埠 80, 讓它可以在外部使用。   

![][11]

此範例會使用有創意的 HTML 程式碼。

您也可以查看節點的狀態[Chef 管理](https://manage.chef.io/)。 

![][17]

別忘了您也可以透過連接埠 3389，從 Azure 入口網站的 RDP 工作階段進行連線。

感謝您！ 現在就開始使用 Azure 來體驗基礎結構即程式碼！

<!--Image references-->
[2]: media/chef-automation/2.png
[3]: media/chef-automation/3.png
[4]: media/chef-automation/4.png
[5]: media/chef-automation/5.png
[6]: media/chef-automation/6.png
[7]: media/chef-automation/7.png
[8]: media/chef-automation/8.png
[9]: media/chef-automation/9.png
[10]: media/chef-automation/10.png
[11]: media/chef-automation/11.png
[13]: media/chef-automation/13.png
[14]: media/chef-automation/14.png
[15]: media/chef-automation/15.png
[16]: media/chef-automation/16.png
[17]: media/chef-automation/17.png


<!--Link references-->
