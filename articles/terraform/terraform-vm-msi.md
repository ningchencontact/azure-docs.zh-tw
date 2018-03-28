---
title: 使用 Azure Marketplace 映像透過受控服務識別來建立 Terraform Linux 虛擬機器
description: 使用 Azure Marketplace 映像，透過受控服務識別與遠端狀態管理來建立 Terraform Linux 虛擬機器，輕鬆地將資源部署至 Azure。
keywords: terraform, devops, MSI, 虛擬機器, 遠端狀態, azure
author: VaijanathB
manager: rloutlaw
ms.author: tarcher
ms.date: 3/12/2018
ms.topic: article
ms.openlocfilehash: ce8a3e8b813bf4224a1fd77d60ec30f2f8e080a7
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/16/2018
---
# <a name="use-an-azure-marketplace-image-to-create-a-terraform-linux-virtual-machine-with-managed-service-identity"></a>使用 Azure Marketplace 映像透過受控服務識別來建立 Terraform Linux 虛擬機器

本文會示範如何使用 [Terraform Marketplace 映像](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.terraform?tab=Overview)來建立 `Ubuntu Linux VM (16.04 LTS)`，並使用[受控服務識別 (MSI)](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview) 來安裝及設定最新的 [Terraform](https://www.terraform.io/intro/index.html) 版本。 此映像也會設定遠端後端，以使用 Terraform 啟用[遠端狀態](https://www.terraform.io/docs/state/remote.html)管理。 Terraform Marketplace 映像可讓您輕鬆快速地在 Azure 上開始使用 Terraform，而不必手動安裝 Terraform 及設定驗證。 

這個 Terraform VM 映像沒有任何軟體費用。 您僅需支付 Azure 硬體使用費，這是根據佈建的虛擬機器大小來評估。 您可以在 [Linux 虛擬機器定價頁面](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)上找到更多計算費用的詳細資料。

## <a name="prerequisites"></a>先決條件
您必須先具有 Azure 訂用帳戶，才可以建立 Linux Terraform 虛擬機器。 如果您還沒有訂用帳戶，請參閱 [立即建立免費的 Azure 帳戶](https://azure.microsoft.com/free/)。  

## <a name="create-your-terraform-virtual-machine"></a>建立 Terraform 虛擬機器 

建立 Linux Terraform 虛擬機器執行個體的步驟如下。 

1. 巡覽至 Azure 入口網站上的[建立資源](https://ms.portal.azure.com/#create/hub) 清單。
2. 在 `Search the Marketplace` 搜尋列中搜尋 `Terraform`。 選取 `Terraform` 範本。 在右下方的 Terraform 詳細資料索引標籤中選取 [建立] 按鈕。
![替代文字](media\terraformmsi.png)
3. 下列各節提供精靈中每個步驟 (**列舉於右邊**) 的輸入，可用來建立 Terraform Linux 虛擬機器。  以下是設定每個步驟所需的輸入

## <a name="details-in-create-terraform-tab"></a>[建立 Terraform] 索引標籤中的詳細資料

以下是 [建立 Terraform] 索引標籤中必須輸入的詳細資料。

a. **基本概念**
    
* **名稱**：Terraform 虛擬機器的名稱。
* **使用者名稱**：第一個帳戶登入識別碼。
* **密碼**︰第一個帳戶密碼 (您可以使用 SSH 公開金鑰來代替密碼)。
* **訂用帳戶**：如果您有多個訂用帳戶，請選取要用來建立機器和開立帳單的訂用帳戶。 您必須有此訂用帳戶的資源建立權限。
* **資源群組**：您可以建立新群組或使用現有的群組。
* **位置**：選取最適合的資料中心。 它通常是擁有您大部分資料的資訊中心，或是最接近您實際位置可進行最快速網路存取的資料中心。

b. **其他設定**

* 大小：虛擬機器的大小。
* VM 磁碟類型：選擇 SSD 或 HDD

c. **摘要 Terraform**

* 請確認您輸入的所有資訊都正確無誤。 

d. **購買**

* 若要開始佈建，請按一下 [購買]。 會提供一個交易條款的連結。 VM 除了計算您在 [大小] 步驟中所選擇的伺服器大小之外，不會收取任何其他費用。

Terraform VM 映像會執行下列步驟

* 使用系統根據 Ubuntu 16.04 LTS 映像指派的身分識別來建立 VM
* 在 VM 上安裝 MSI 擴充功能，以允許要針對 Azure 資源發行的 OAuth 權杖
* 將 RBAC 權限指派給受控身分識別，從而授與資源群組的擁有者權限
* 建立 Terraform 範本資料夾 (tfTemplate)
* 使用 Azure 後端預先設定 Terraform 遠端狀態

## <a name="how-to-access-and-configure-linux-terraform-virtual-machine"></a>如何存取及設定 Linux Terraform 虛擬機器

建立 VM 之後，您就可以使用 SSH 登入。 針對文字殼層介面，使用您在步驟 3 的「基本資料」一節中建立的帳戶認證。 在 Windows 上，您可以下載 SSH 用戶端工具，例如 [Putty](http://www.putty.org/)

使用 `SSH` 連線至虛擬機器後，您必須將整個訂用帳戶的參與者權限授予虛擬機器上的受控服務識別。 參與者權限可協助 VM 的 MSI 使用 Terraform 來建立 VM 資源群組外部的資源。 您可以執行一次指令碼，輕鬆達成這個動作。 執行這個動作的命令如下。

`. ~/tfEnv.sh`

先前的指令碼會使用 [AZ CLI 2.0 版互動式登入](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest#interactive-log-in)機制向 Azure 驗證，並指派整個訂用帳戶上的虛擬機器受控服務識別參與者權限。 

 VM 已建立 Terraform 遠端狀態後端，若要在您的 Terraform 部署中加以啟用，您必須將 remoteState.tf 檔案從 tfTemplate 目錄複製到 Terraform 指令碼的根目錄。  

 `cp  ~/tfTemplate/remoteState.tf .`

 您可以在[這裡](https://www.terraform.io/docs/state/remote.html)進一步了解遠端狀態管理。 儲存體存取金鑰會在此檔案中公開，且必須仔細簽入原始檔控制。  

## <a name="next-steps"></a>後續步驟
在本文中，您已了解如何在 Azure 上設定 Terraform Linux 虛擬機器。 以下有一些額外的資源，可供您深入了解 Azure 上的 Terraform。 

 [Microsoft.com 中的 Terraform 中樞](https://docs.microsoft.com/azure/terraform/)  
 [Terraform Azure 提供者文件](http://aka.ms/terraform)  
 [Terraform Azure 提供者來源](http://aka.ms/tfgit)  
 [Terraform Azure 模組](http://aka.ms/tfmodules)
 

















