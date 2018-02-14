---
title: "受控服務識別 (MSI) (適用於 Azure Active Directory)"
description: "受控服務識別 (適用於 Azure 資源) 概觀。"
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: identity
ms.date: 12/15/2017
ms.author: daveba
ms.reviewer: skwan
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 95980c082b09ad959ab8bbaae0250b40ac08d2c8
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/03/2018
---
#  <a name="managed-service-identity-msi-for-azure-resources"></a>受控服務識別 (MSI) (適用於 Azure 資源)

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

建置雲端應用程式常見的難題是如何管理程式碼中必須存在的認證，以向雲端服務進行驗證。 保護好這些認證是相當重要的工作。 在理想情況下，這些認證永不會出現在開發人員工作站或簽入原始程式碼控制。 Azure Key Vault 可安全地儲存認證和其他金鑰及密碼，但是您的程式碼必須向 Key Vault 進行驗證，才可取得這些項目。 受控服務身分識別 (MSI) 可以輕易地解決此問題，因為 MSI 可在 Azure Active Directory (Azure AD) 中提供自動受控身分識別給 Azure 服務。 您可以使用此身分識別來完成任何支援 Azure AD 驗證的服務驗證 (包括 Key Vault)，不需要任何您程式碼中的認證。

## <a name="how-does-it-work"></a>運作方式

在 Azure 服務執行個體上使用受控服務識別時，Azure 會在 Azure 訂用帳戶所用的 Azure AD 租用戶中建立一個身分識別。 此外，Azure 會將身分識別的認證佈建到服務執行個體。 因此，您的程式碼可以執行索取存取權杖的本機要求，來存取支援 Azure AD 驗證的服務。 在此期間，Azure 會負責更新服務執行個體所使用的認證。

## <a name="how-do-i-enable-my-resources-to-use-a-managed-service-identity"></a>如何讓我的資源能夠使用受控服務識別？

可用的受控服務識別有兩種：「系統指派」和「使用者指派」。

- **系統指派**的 MSI 會直接在 Azure 服務執行個體上啟用。 透過啟用程序，Azure 會在 Azure AD 租用戶中建立服務執行個體的身分識別，並且將身分識別的認證佈建到服務執行個體上。 系統指派的 MSI 生命週期會直接繫結至已啟用該 MSI 的 Azure 服務執行個體。 如果服務執行個體已刪除，則 Azure 會自動清除 Azure AD 中的認證和身分識別。

- **使用者指派**的 MSI (私人預覽) 會建立為獨立的 Azure 資源。 透過建立程序，Azure 會在 Azure AD 租用戶中建立身分識別。 建立身分識別之後，即可將它指派給一個或多個 Azure 服務執行個體。 由於使用者指派的 MSI 可供多個 Azure 服務執行個體使用，所以會分開管理其生命週期。

以下範例顯示系統指派的 MSI 如何與 Azure 虛擬機器一起運作。

![虛擬機器 MSI 範例](~/articles/active-directory/media/msi-vm-example.png)

1. Azure Resource Manager 收到一則訊息，以便在 VM 上啟用系統指派的 MSI。
2. Azure Resource Manager 在 Azure AD 中建立服務主體，代表 VM 的身分識別。 服務主體會建立在此訂用帳戶信任的 Azure AD 租用戶中。
3. Azure Resource Manager 會在 VM 的 MSI VM 延伸模組中設定服務主體詳細資料。 此步驟包括設定延伸模組用來從 Azure AD 取得存取權杖的用戶端識別碼和認證。
4. 現在我們已知道 VM 的服務主體身分識別，可以將 Azure 資源的存取權授與此身分識別。 例如，您的程式碼需要呼叫 Azure Resource Manager，您會使用 Azure AD 中的角色型存取控制 (RBAC)，指派適當角色給 VM 的服務主體。  如果您的程式碼需要呼叫 Key Vault，您會將存取權授與程式碼，以取得 Key Vault 中特定的密碼或金鑰。
5. 在 VM 上執行的程式碼會向 MSI VM 解除安裝所主控的本機端點要求權杖：http://localhost:50342/oauth2/token。 resource 參數指定將權杖傳送至哪個服務。 例如，如果您希望程式碼向 Azure Resource Manager 進行驗證，您會使用resource=https://management.azure.com/。
6. MSI VM 延伸模組會使用其設定的用戶端識別碼和認證，來向 Azure AD 要求存取權杖。  Azure AD 會傳回 JSON Web 權杖 (JWT) 存取權杖。
7. 您的程式碼會在呼叫上傳送存取權杖給支援 Azure AD 驗證的服務。

使用相同的圖表，以下範例可顯示使用者指派的 MSI 如何與 Azure 虛擬機器一起運作。

![虛擬機器 MSI 範例](~/articles/active-directory/media/msi-vm-example.png)

1. Azure Resource Manager 收到一則訊息，以便建立使用者指派的 MSI。
2. Azure Resource Manager 會在 Azure AD 中建立服務主體，以代表 MSI 的身分識別。 服務主體會建立在此訂用帳戶信任的 Azure AD 租用戶中。
3. Azure Resource Manager 會收到一則訊息，以便在虛擬機器的 MSI VM 解除安裝中設定服務主體詳細資料。 此步驟包括設定延伸模組用來從 Azure AD 取得存取權杖的用戶端識別碼和認證。
4. 現在我們已知道 MSI 的服務主體身分識別，可以將 Azure 資源的存取權授與此身分識別。 例如，您的程式碼需要呼叫 Azure Resource Manager，您會使用 Azure AD 中的角色型存取控制 (RBAC)，指派適當角色給 MSI 的服務主體。 如果您的程式碼需要呼叫 Key Vault，您會將存取權授與程式碼，以取得 Key Vault 中特定的密碼或金鑰。 注意：不需要進行步驟 3 也能完成步驟 4。 一旦 MSI 存在，不論其是否設定於 VM 上，都可以獲得資源的存取權。
5. 執行於 VM 上的程式碼會從本機端點要求由 MSI VM 延伸模組：http://localhost:50342/oauth2/token 託管的權杖。 用戶端識別碼參數指定要使用的 MSI 身分識別名稱。 此外，資源參數可指定將權杖傳送至哪個服務。 例如，如果您希望程式碼向 Azure Resource Manager 進行驗證，您會使用resource=https://management.azure.com/。
6. MSI VM 解除安裝會檢查是否已設定所要求用戶端識別碼的憑證，並且會向 Azure AD 要求存取權杖。 Azure AD 會傳回 JSON Web 權杖 (JWT) 存取權杖。
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






