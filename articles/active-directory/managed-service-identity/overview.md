---
title: 受控服務識別 (MSI) (適用於 Azure Active Directory)
description: 受控服務識別 (適用於 Azure 資源) 概觀。
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 12/19/2017
ms.author: skwan
ms.openlocfilehash: e4f9d9e4e0f84610ad072d889abf68b62c0dd41f
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2018
---
#  <a name="managed-service-identity-msi-for-azure-resources"></a>受控服務識別 (MSI) (適用於 Azure 資源)

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

建置雲端應用程式常見的難題是如何管理程式碼中必須存在的認證，以向雲端服務進行驗證。 保護好這些認證是相當重要的工作。 在理想情況下，這些認證永不會出現在開發人員工作站或簽入原始程式碼控制。 Azure Key Vault 可安全地儲存認證和其他金鑰及密碼，但是您的程式碼必須向 Key Vault 進行驗證，才可取得這些項目。 受控服務身分識別 (MSI) 可以輕易地解決此問題，因為 MSI 可在 Azure Active Directory (Azure AD) 中提供自動受控身分識別給 Azure 服務。 您可以使用此身分識別來完成任何支援 Azure AD 驗證的服務驗證 (包括 Key Vault)，不需要任何您程式碼中的認證。

## <a name="how-does-it-work"></a>運作方式

當您在 Azure 服務上啟用受控服務身分識別時，Azure 會自動為 Azure AD 租用戶 (由您的 Azure 訂用帳戶所使用) 中的服務執行個體建立身分識別。  實際上，Azure 會將身分識別認證佈建到服務執行個體。  您的程式碼可以執行索取存取權杖的本機要求，來存取支援 Azure AD 驗證的服務。  Azure 會負責更新服務個體使用的認證。  如果服務執行個體已刪除，則 Azure 會自動清除 Azure AD 中的認證和身分識別。

以下是受控服務身分識別與 Azure 虛擬機器的運作方式範例。

![虛擬機器 MSI 範例](../media/msi-vm-example.png)

1. Azure Resource Manager 收到訊息，啟用 VM 上的 MSI。
2. Azure Resource Manager 在 Azure AD 中建立服務主體，代表 VM 的身分識別。 服務主體會建立在此訂用帳戶信任的 Azure AD 租用戶中。
3. Azure Resource Manager 會在 VM 的 MSI VM 延伸模組中設定服務主體詳細資料。  此步驟包括設定延伸模組用來從 Azure AD 取得存取權杖的用戶端識別碼和認證。
4. 現在我們已知道 VM 的服務主體身分識別，可以將 Azure 資源的存取權授與此身分識別。  例如，您的程式碼需要呼叫 Azure Resource Manager，您會使用 Azure AD 中的角色型存取控制 (RBAC)，指派適當角色給 VM 的服務主體。  如果您的程式碼需要呼叫 Key Vault，您會將存取權授與程式碼，以取得 Key Vault 中特定的密碼或金鑰。
5. 在 VM 上執行的程式碼會向 MSI VM 解除安裝所主控的本機端點要求權杖：http://localhost:50342/oauth2/token。  resource 參數指定將權杖傳送至哪個服務。 例如，如果您希望程式碼向 Azure Resource Manager 進行驗證，您會使用 resource=https://management.azure.com/。
6. MSI VM 延伸模組會使用其設定的用戶端識別碼和認證，來向 Azure AD 要求存取權杖。  Azure AD 會傳回 JSON Web 權杖 (JWT) 存取權杖。
7. 您的程式碼會在呼叫上傳送存取權杖給支援 Azure AD 驗證的服務。

每個支援受控服務識別的 Azure 服務都自有一套方法，讓您的程式碼取得存取權杖。 請查看每個服務的教學課程，找出取得權杖的特定方法。

## <a name="try-managed-service-identity"></a>試用受控服務身分識別

試用「受控服務身分識別」教學課程，以了解用來存取不同的 Azure 資源的端對端案例：
<br><br>
| 從啟用 MSI 的資源 | 了解如何 |
| ------- | -------- |
| Azure VM (Windows) | [使用 Windows VM 受控服務識別來存取 Azure Data Lake Store](tutorial-windows-vm-access-datalake.md) |
|                    | [使用 Windows VM 受控服務身分識別存取 Azure Resource Manager](tutorial-windows-vm-access-arm.md) |
|                    | [使用 Windows VM 受控服務身分識別存取 Azure SQL](tutorial-windows-vm-access-sql.md) |
|                    | [透過存取金鑰與 Windows VM 受控服務身分識別存取 Azure 儲存體](tutorial-windows-vm-access-storage.md) |
|                    | [透過 SAS 與 Windows VM 受控服務身分識別存取 Azure 儲存體](tutorial-windows-vm-access-storage-sas.md) |
|                    | [使用 Windows VM 受控服務身分識別和 Azure Key Vault 存取非 Azure AD 資源](tutorial-windows-vm-access-nonaad.md) |
| Azure VM (Linux)   | [使用 Linux VM 受控服務識別來存取 Azure Data Lake Store](tutorial-linux-vm-access-datalake.md) |
|                    | [使用 Linux VM 受控服務身分識別存取 Azure Resource Manager](tutorial-linux-vm-access-arm.md) |
|                    | [透過存取金鑰與 Linux VM 受控服務身分識別存取 Azure 儲存體](tutorial-linux-vm-access-storage.md) |
|                    | [透過 SAS 與 Linux VM 受控服務身分識別存取 Azure 儲存體](tutorial-linux-vm-access-storage-sas.md) |
|                    | [使用 Linux VM 受控服務身分識別和 Azure Key Vault 存取非 Azure AD 資源](tutorial-linux-vm-access-nonaad.md) |
| Azure App Service  | [使用受控服務身分識別與 Azure App Service 或 Azure Functions](/azure/app-service/app-service-managed-service-identity) |
| Azure Functions    | [使用受控服務身分識別與 Azure App Service 或 Azure Functions](/azure/app-service/app-service-managed-service-identity) |
| Azure 服務匯流排  | [搭配使用受控服務身分識別與 Azure 服務匯流排](../../service-bus-messaging/service-bus-managed-service-identity.md) |
| Azure 事件中心   | [搭配使用受控服務身分識別與 Azure 事件中樞](../../event-hubs/event-hubs-managed-service-identity.md) |

## <a name="which-azure-services-support-managed-service-identity"></a>哪些 Azure 服務支援受控服務身分識別？

支援「受控服務身分識別」的 Azure 服務若支援 Azure AD 驗證，則可使用 MSI 向服務進行驗證。  我們正在努力整合 MSI 和整個 Azure 的 Azure AD 驗證。  請經常回來檢查更新。

### <a name="azure-services-that-support-managed-service-identity"></a>支援「受控服務身分識別」的 Azure 服務

下列 Azure 服務支援受控服務身分識別。

| 服務 | 狀態 | 日期 | 設定 | 取得權杖 |
| ------- | ------ | ---- | --------- | ----------- |
| Azure 虛擬機器 | 預覽 | 2017 年 9 月 | [Azure 入口網站](qs-configure-portal-windows-vm.md)<br>[PowerShell](qs-configure-powershell-windows-vm.md)<br>[Azure CLI](qs-configure-cli-windows-vm.md)<br>[Azure 資源管理員範本](qs-configure-template-windows-vm.md) | [REST](how-to-use-vm-token.md#get-a-token-using-http)<br>[.NET](how-to-use-vm-token.md#get-a-token-using-c)<br>[Bash/Curl](how-to-use-vm-token.md#get-a-token-using-curl)<br>[Go](how-to-use-vm-token.md#get-a-token-using-go)<br>[PowerShell](how-to-use-vm-token.md#get-a-token-using-azure-powershell) |
| Azure App Service | 預覽 | 2017 年 9 月 | [Azure 入口網站](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Azure Resource Manager 範本](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol) |
| Azure Functions<sup>1</sup> | 預覽 | 2017 年 9 月 | [Azure 入口網站](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Azure Resource Manager 範本](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol) |
| Azure Data Factory V2 | 預覽 | 2017 年 11 月 | [Azure 入口網站](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity)<br>[PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-powershell)<br>[REST](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-rest-api)<br>[SDK](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-sdk) |

<sup>1</sup> Azure Functions 支援允許使用者程式碼使用身分識別，但觸發程序和繫結可能仍然需要連接字串。

### <a name="azure-services-that-support-azure-ad-authentication"></a>支援 Azure AD 驗證的 Azure 服務

下列服務支援 Azure AD 驗證，也經過使用受控服務識別的用戶端服務所測試。

| 服務 | 資源識別碼 | 狀態 | 日期 | 指派存取權 |
| ------- | ----------- | ------ | ---- | ------------- |
| Azure Resource Manager | https://management.azure.com | 可用 | 2017 年 9 月 | [Azure 入口網站](howto-assign-access-portal.md) <br>[PowerShell](howto-assign-access-powershell.md) <br>[Azure CLI](howto-assign-access-CLI.md) |
| Azure 金鑰保存庫 | https://vault.azure.net | 可用 | 2017 年 9 月 | |
| Azure Data Lake | https://datalake.azure.net | 可用 | 2017 年 9 月 | |
| Azure SQL | https://database.windows.net | 可用 | 2017 年 10 月 | |
| Azure 事件中心 | https://eventhubs.azure.net | 可用 | 2017 年 12 月 | |
| Azure 服務匯流排 | https://servicebus.azure.net | 可用 | 2017 年 12 月 | |

## <a name="how-much-does-managed-service-identity-cost"></a>「受控服務身分識別」的售價如何？

「受控服務身分識別」隨附於 Azure Active Directory Free，這是 Azure 訂用帳戶的預設功能。  無須針對「受控服務身分識別」支付其他費用。

## <a name="support-and-feedback"></a>支援與意見反應

希望能夠聽到您的意見！

* 請在有 [azure msi](http://stackoverflow.com/questions/tagged/azure-msi) 標記的 Stack Overflow 上詢問作法問題。
* 請在[適用於開發人員的 Azure AD 意見反應論壇](https://feedback.azure.com/forums/169401-azure-active-directory/category/164757-developer-experiences)中索取功能或給予意見反應。






