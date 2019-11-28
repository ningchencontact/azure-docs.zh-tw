---
title: 教學課程 - 設定適用於 Terraform 的 Azure Cloud Shell
description: 使用 Terraform 與 Azure Cloud Shell 來簡化驗證與範本設定。
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: db9edfadbe01edc1ee9df09c284e3895ee11f3d3
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2019
ms.locfileid: "74159133"
---
# <a name="tutorial-configure-azure-cloud-shell-for-terraform"></a>教學課程：設定適用於 Terraform 的 Azure Cloud Shell

Terraform 適用於 macOS、Windows 或 Linux 中的 Bash 命令列。 在 [Azure Cloud Shell](/azure/cloud-shell/overview) 的 Bash 體驗中執行您的 Terraform 設定，具有一些獨特優點。 本教學課程將說明如何使用 Cloud Shell 來撰寫部署至 Azure 的 Terraform 指令碼。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>自動認證設定

Terraform 已安裝且立即可在 Cloud Shell 中使用。 不使用任何額外的設定登入 Cloud Shell 來管理結礎結構時，使用 Azure 驗證 Terraform 指令碼。 自動驗證會略過兩個手動程序：
- 建立 Azure Active Directory 服務主體
- 設定 Azure Terraform 提供者變數


## <a name="use-modules-and-providers"></a>使用模組和提供者

Azure Terraform 模組需要認證才能存取和修改 Azure 資源。 若要在 Cloud Shell 中使用 Terraform 模組，請新增下列程式碼：


```hcl
# Configure the Microsoft Azure Provider
provider "azurerm" {
}
```

使用任何 `terraform` CLI 命令時，Cloud Shell 會透過環境變數傳遞 `azurerm` 提供者的必要值。

## <a name="other-cloud-shell-developer-tools"></a>其他 Cloud Shell 開發人員工具

檔案和殼層狀態會保存在 Cloud Shell 工作階段之間的 Azure 儲存體中。 使用 [Azure 儲存體總管](/azure/vs-azure-tools-storage-manage-with-storage-explorer)，將檔案從本機電腦複製並上傳至 Cloud Shell。

Azure CLI 隨附於 Cloud Shell，是在完成 `terraform apply` 或 `terraform destroy` 後測試設定以及檢查工作的適用工具。


## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用模組登錄建立小型 VM 叢集](terraform-create-vm-cluster-module.md)
