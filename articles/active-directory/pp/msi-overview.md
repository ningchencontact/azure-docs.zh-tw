---
title: "受控服務識別 (MSI) (適用於 Azure Active Directory)"
description: "受控服務識別 (適用於 Azure 資源) 概觀。"
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: identity
ms.date: 12/15/2017
ms.author: bryanla
ms.reviewer: skwan
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 53577c8da5f82235284d1cb9e48f2d47254aa6bd
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/22/2017
---
#  <a name="managed-service-identity-msi-for-azure-resources"></a>受控服務識別 (MSI) (適用於 Azure 資源)

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

建置雲端應用程式常見的難題是如何管理程式碼中必須存在的認證，以向雲端服務進行驗證。 保護好這些認證是相當重要的工作。 在理想情況下，這些認證永不會出現在開發人員工作站或簽入原始程式碼控制。 Azure Key Vault 可安全地儲存認證和其他金鑰及密碼，但是您的程式碼必須向 Key Vault 進行驗證，才可取得這些項目。 受控服務身分識別 (MSI) 可以輕易地解決此問題，因為 MSI 可在 Azure Active Directory (Azure AD) 中提供自動受控身分識別給 Azure 服務。 您可以使用此身分識別來完成任何支援 Azure AD 驗證的服務驗證 (包括 Key Vault)，不需要任何您程式碼中的認證。

## <a name="how-does-it-work"></a>運作方式

當 Azure 服務執行個體上使用受管理的服務身分識別，Azure 會建立您的 Azure 訂用帳戶所使用的 Azure AD 租用戶中的身分識別。 此外，Azure 會佈建身分識別服務執行個體上的認證。 如此一來，您的程式碼可以請本機要求支援 Azure AD 驗證的服務取得存取權杖。 所有 Azure 時，負責循環服務執行個體所使用的認證。

## <a name="how-do-i-enable-my-resources-to-use-a-managed-service-identity"></a>如何啟用我要使用受管理的服務身分識別的資源？

有兩種類型的管理服務身分識別：*系統指派*和*指派給使用者*。

- A**系統指派**MSI 已直接在 Azure 服務執行個體上啟用。 在啟用程序，透過 Azure 在 Azure AD 租用戶中建立服務執行個體的身分識別，並佈建到服務執行個體上的身分識別的認證。 系統指派的 MSI 直接繫結至 Azure 的生命週期服務執行個體已啟用。 如果服務執行個體已刪除，則 Azure 會自動清除 Azure AD 中的認證和身分識別。

- A**指派給使用者**MSI *（私人預覽中）*建立為獨立的 Azure 資源。 透過建立程序中，Azure 會建立在 Azure AD 租用戶身分識別。 建立身分識別之後，可以將它指派至一個或多個 Azure 服務執行個體。 指派給使用者的 MSI 可供多個 Azure 服務執行個體，因為它的生命週期是分開管理。

以下是由系統指派給 MSI 使用 Azure 虛擬機器的運作方式的範例。

![虛擬機器 MSI 範例](~/articles/active-directory/media/msi-vm-example.png)

1. Azure 資源管理員接收到啟用的系統指派的 MSI 在 VM 上的訊息。
2. Azure Resource Manager 在 Azure AD 中建立服務主體，代表 VM 的身分識別。 服務主體會建立在此訂用帳戶信任的 Azure AD 租用戶中。
3. Azure Resource Manager 會在 VM 的 MSI VM 延伸模組中設定服務主體詳細資料。 此步驟包括設定延伸模組用來從 Azure AD 取得存取權杖的用戶端識別碼和認證。
4. 現在我們已知道 VM 的服務主體身分識別，可以將 Azure 資源的存取權授與此身分識別。 例如，您的程式碼需要呼叫 Azure Resource Manager，您會使用 Azure AD 中的角色型存取控制 (RBAC)，指派適當角色給 VM 的服務主體。  如果您的程式碼需要呼叫 Key Vault，您會將存取權授與程式碼，以取得 Key Vault 中特定的密碼或金鑰。
5. 您在 VM 上執行的程式碼從裝載的 MSI VM 擴充功能的本機端點要求權杖： http://localhost:50342/oauth2 權杖。 resource 參數指定將權杖傳送至哪個服務。 例如，如果您希望程式碼向 Azure Resource Manager 進行驗證，您會使用resource=https://management.azure.com/。
6. MSI VM 延伸模組會使用其設定的用戶端識別碼和認證，來向 Azure AD 要求存取權杖。  Azure AD 會傳回 JSON Web 權杖 (JWT) 存取權杖。
7. 您的程式碼會在呼叫上傳送存取權杖給支援 Azure AD 驗證的服務。

使用同一個圖表，此處的範例指派給使用者的 MSI 與 Azure 虛擬機器的運作方式。

![虛擬機器 MSI 範例](~/articles/active-directory/media/msi-vm-example.png)

1. Azure 資源管理員會收到訊息，以建立指派給使用者的 MSI。
2. Azure 資源管理員來代表 MSI 的身分識別的 Azure AD 中建立服務主體。 服務主體會建立在此訂用帳戶信任的 Azure AD 租用戶中。
3. Azure 資源管理員接收訊息在 MSI VM 擴充功能的 VM 中設定的服務主體的詳細資料。 此步驟包括設定延伸模組用來從 Azure AD 取得存取權杖的用戶端識別碼和認證。
4. 既然已知 MSI 的服務主體身分識別，它可以授與 Azure 資源的存取權。 例如，如果您的程式碼需要呼叫 Azure 資源管理員，然後您會指派 MSI 的服務主體適當的角色在 Azure AD 中使用角色型存取控制 (RBAC)。 如果您的程式碼需要呼叫 Key Vault，您會將存取權授與程式碼，以取得 Key Vault 中特定的密碼或金鑰。 注意： 不需要完成步驟 4 到步驟 3。 一旦 MSI 已存在，它可以授與資源，不論在 VM 上設定的存取權。
5. 執行於 VM 上的程式碼會從本機端點要求由 MSI VM 延伸模組：http://localhost:50342/oauth2/token 託管的權杖。 用戶端 ID 參數指定要使用之 MSI 識別的名稱。 此外，資源參數會指定權杖會傳送的服務。 例如，如果您希望程式碼向 Azure Resource Manager 進行驗證，您會使用resource=https://management.azure.com/。
6. 如果要求的用戶端識別碼的憑證設定，並從 Azure AD 要求存取權杖，則會檢查 MSI VM 延伸模組。 Azure AD 會傳回 JSON Web 權杖 (JWT) 存取權杖。
7. 您的程式碼會在呼叫上傳送存取權杖給支援 Azure AD 驗證的服務。

每個支援受控服務識別的 Azure 服務都自有一套方法，讓您的程式碼取得存取權杖。 請查看每個服務的教學課程，找出取得權杖的特定方法。

## <a name="try-managed-service-identity"></a>試用受控服務身分識別

試用「受控服務身分識別」教學課程，以了解用來存取不同的 Azure 資源的端對端案例：
<br><br>
| 從啟用 MSI 的資源 | 了解如何 |
| ------- | -------- |
| Azure VM (Linux)   | 
            [使用 Linux VM 受控服務身分識別存取 Azure Resource Manager](msi-tutorial-linux-vm-access-arm.md) |
|                    | 
            [透過存取金鑰與 Linux VM 受控服務身分識別存取 Azure 儲存體](msi-tutorial-linux-vm-access-storage.md) |

## <a name="which-azure-services-support-managed-service-identity"></a>哪些 Azure 服務支援受控服務身分識別？

支援「受控服務身分識別」的 Azure 服務若支援 Azure AD 驗證，則可使用 MSI 向服務進行驗證。  我們正在努力整合 MSI 和整個 Azure 的 Azure AD 驗證。  請經常回來檢查更新。

### <a name="azure-services-that-support-managed-service-identity"></a>支援「受控服務身分識別」的 Azure 服務

下列 Azure 服務支援受控服務身分識別。

| 服務 | 狀態 | 日期 | 設定 | 取得權杖 |
| ------- | ------ | ---- | --------- | ----------- |
| Azure 虛擬機器 | 預覽 | 2017 年 9 月 | [Azure CLI](msi-qs-configure-cli-windows-vm.md)<br>[Azure 資源管理員範本](msi-qs-configure-template-windows-vm.md) | [Bash/Curl](msi-how-to-use-vm-msi-token.md#get-a-token-using-curl)<br>[HTTP/REST](msi-how-to-use-vm-msi-token.md#get-a-token-using-http) |

### <a name="azure-services-that-support-azure-ad-authentication"></a>支援 Azure AD 驗證的 Azure 服務

下列服務支援 Azure AD 驗證，也經過使用受控服務識別的用戶端服務所測試。

| 服務 | 資源識別碼 | 狀態 | 日期 | 指派存取權 |
| ------- | ----------- | ------ | ---- | ------------- |
| Azure Resource Manager | https://management.azure.com/ | 可用 | 2017 年 9 月 | [Azure CLI](msi-howto-assign-access-CLI.md) |
| Azure 金鑰保存庫 | https://vault.azure.net/ | 可用 | 2017 年 9 月 | |
| Azure Data Lake | https://datalake.azure.net/ | 可用 | 2017 年 9 月 | |
| Azure SQL | https://database.windows.net/ | 可用 | 2017 年 10 月 | |

## <a name="how-much-does-managed-service-identity-cost"></a>「受控服務身分識別」的售價如何？

「受控服務身分識別」隨附於 Azure Active Directory Free，這是 Azure 訂用帳戶的預設功能。  無須針對「受控服務身分識別」支付其他費用。

## <a name="support-and-feedback"></a>支援與意見反應

希望能夠聽到您的意見！

* 請在有 [azure msi](http://stackoverflow.com/questions/tagged/azure-msi) 標記的 Stack Overflow 上詢問作法問題。
* 請在[適用於開發人員的 Azure AD 意見反應論壇](https://feedback.azure.com/forums/169401-azure-active-directory/category/164757-developer-experiences)中索取功能或給予意見反應。






