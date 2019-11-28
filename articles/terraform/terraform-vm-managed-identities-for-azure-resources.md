---
title: 教學課程 - 使用 Terraform 從 Azure Marketplace 映像建立具有受控識別的 Linux VM
description: 使用 Azure Marketplace 映像，建立具有受控識別與遠端狀態管理功能的 Terraform Linux VM
ms.topic: tutorial
ms.date: 11/07/2019
ms.openlocfilehash: 6bc6bec2a13dcd5747823de739d1dd11c6027091
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158985"
---
# <a name="tutorial-create-a-linux-vm-with-a-managed-identity-from-the-azure-marketplace-image-using-terraform"></a>教學課程：使用 Terraform 從 Azure Marketplace 映像建立具有受控識別的 Linux VM

本文示範如何使用 [Terraform Marketplace 映像](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.terraform?tab=Overview)來建立 Ubuntu Linux VM (16.04 LTS)，並使用 [Azure 資源的受控識別](/azure/active-directory/managed-service-identity/overview)來安裝及設定最新的 [Terraform](https://www.terraform.io/intro/index.html) 版本。 此映像也會設定遠端後端，以使用 Terraform 啟用[遠端狀態](https://www.terraform.io/docs/state/remote.html)管理。 

Terraform Marketplace 映像可讓您輕鬆地在 Azure 上開始使用 Terraform，而不必手動安裝及設定 Terraform。 

這個 Terraform VM 映像沒有任何軟體費用。 您只需要根據佈建的 VM 大小支付 Azure 硬體使用費用。 

如需費用計算的詳細資料，請參閱 [Linux VM 定價頁面](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)。

## <a name="prerequisites"></a>必要條件
您必須具有 Azure 訂用帳戶，才能夠建立 Linux Terraform VM。 如果您還沒有訂用帳戶，請參閱[立即建立免費的 Azure 帳戶](https://azure.microsoft.com/free/)。  

## <a name="create-your-terraform-vm"></a>建立 Terraform VM 

建立 Linux Terraform VM 執行個體的步驟如下： 

1. 在 Azure 入口網站中，移至 [建立資源](https://ms.portal.azure.com/#create/hub) 清單。

1. 在 **搜尋 Marketplace** 搜尋列中搜尋 **Terraform**。 

1. 選取 [建立]  。 

1. 下列各節提供精靈中每個步驟的輸入資訊，可用來建立 Terraform Linux VM。 下一節列出設定每個步驟所需的輸入資訊。

## <a name="details-on-the-create-terraform-tab"></a>建立 Terraform 索引標籤的詳細資料

在 [建立 Terraform]  索引標籤中輸入以下的詳細資料：

1. **基本概念**
    
   * **名稱**：Terraform VM 的名稱。
   * **使用者名稱**：第一個帳戶登入識別碼。
   * **密碼**：第一個帳戶密碼。 (您可以使用 SSH 公開金鑰而不使用密碼。)
   * 訂用帳戶  ：要用來建立虛擬機器和開立帳單的訂用帳戶。 您必須有此訂用帳戶的資源建立權限。
   * **資源群組**：新的或現有的資源群組。
   * **位置**：最適合的資料中心。 通常是擁有您大部分資料的資訊中心，或是最接近您實際位置可進行最快速網路存取的資料中心。

2. **其他設定**

   * **大小**：VM 的大小。 
   * **VM 磁碟類型**：SSD 或 HDD。

3. **摘要 Terraform**

   * 請確認您輸入的所有資訊都正確無誤。 

4. **購買**

   * 若要開始佈建程序，選取 [購買]  。 會提供一個交易條款的連結。 VM 除了計算您在「大小」步驟中選擇的伺服器大小以外，不會收取任何其他費用。

Terraform VM 映像會執行下列步驟：

* 使用系統根據 Ubuntu 16.04 LTS 映像指派的身分識別來建立 VM。
* 在 VM 上安裝 Azure 資源擴充功能的受控識別，以允許要針對 Azure 資源發行的 OAuth 權杖。
* 將 RBAC 權限指派給受控身分識別，從而授與資源群組的擁有者權限。
* 建立 Terraform 範本資料夾 (tfTemplate)。
* 使用 Azure 後端預先設定 Terraform 遠端狀態。

## <a name="access-and-configure-a-linux-terraform-vm"></a>存取及設定 Linux Terraform VM

建立 VM 之後，請執行下列步驟：

1. 使用 SSH 登入 VM。 請使用您在上一節中建立的帳戶憑證。 在 Windows 上，您可以下載 SSH 用戶端工具，例如 [Putty](https://www.putty.org/)。

1. 將整個訂用帳戶的參與者權限授與 VM 上 Azure 資源的受控識別。 

    參與者權限可協助 VM 上 Azure 資源的受控識別使用 Terraform 來建立 VM 資源群組外部的資源。 請執行下列指令碼以執行此動作： 
    
    ```bash
    . ~/tfEnv.sh
    ```

    此指令碼會使用 [Azure CLI 互動式登入](/cli/azure/authenticate-azure-cli?view=azure-cli-latest#sign-in-interactively)機制向 Azure 進行驗證。 此程序會指派整個訂用帳戶的[受控識別參與者權限](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)。 

1. VM 有一個 Terraform 遠端狀態後端。 若要在您的 Terraform 部署上啟用此後端，您必須將 `remoteState.tf` 檔案複製到 Terraform 指令碼的根目錄。

    ```bash
    cp  ~/tfTemplate/remoteState.tf .
    ```

    如需遠端狀態管理的詳細資訊，請參閱 [Terraform 遠端狀態](https://www.terraform.io/docs/state/remote.html)。 儲存體存取金鑰會公開於此檔案中。 將 Terraform 組態檔認可至原始檔控制之前，請先排除該金鑰。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"] 
> [深入了解如何使用 Azure 中的 Terraform](/azure/terraform)