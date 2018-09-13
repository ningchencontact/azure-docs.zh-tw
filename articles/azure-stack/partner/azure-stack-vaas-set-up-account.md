---
title: 設定您的 Azure Stack 驗證即服務帳戶 | Microsoft Docs
description: 了解如何設定您的驗證即服務帳戶。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 3f87721dcf7485e15a473e82597f6bf4baeca659
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2018
ms.locfileid: "43775055"
---
# <a name="set-up-your-validation-as-a-service-account"></a>設定您的驗證即服務帳戶

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

驗證即服務 (VaaS) 是一項 Azure 服務，可供 Microsoft Azure Stack 合作夥伴使用，這些合作夥伴與 Microsoft 有共同工程合約，可以設計、開發、驗證、銷售、部署及支援市場中的 Azure Stack 解決方案。

了解如何讓您的系統準備好驗證即服務。 設定 Azure Active Directory 執行個體並且執行其他必要工作，以準備好使用 VaaS。 

工作包括：

- 建立用來儲存記錄的 Azure 儲存體 Blob
- 部署本機代理程式
- 下載 Azure Stack 執行個體上，要用來進行測試的測試映像虛擬機器

## <a name="create-an-azure-active-directory-tenant-id"></a>建立 Azure Active Directory 租用戶識別碼

1. 在 [Azure 入口網站](https://portal.azure.com)中建立 Azure Active Directory 租用戶，或使用現有的租用戶。

    建議建立一個租用戶，特別用於 VaaS 且具有描述性名稱，例如，ContosoVaaS@onmicrosoft.com。 合作夥伴會使用租用戶的角色型存取控制 (RBAC) 功能，來管理合作夥伴組織中有誰可以使用 VaaS。  
    
    如需詳細資訊，請參閱[管理您的 Azure AD 目錄](https://docs.microsoft.com/azure/active-directory/active-directory-administer)。

    > [!Note]  
    > 如需建立新 Azure Active Directory 租用戶的詳細資訊，請參閱[開始使用 Azure AD](https://docs.microsoft.com/azure/active-directory/get-started-azure-ad) (英文)。

2. 新增貴組織的成員，該成員負責對租用戶使用服務。 為租用戶中的每個成員指派下列其中一個角色，控制他們對 VaaS 的存取層級：

    | 角色名稱 | 說明 |
    |---------------------|------------------------------------------|
    | 擁有者 | 具有所有資源的完整存取權。 |
    | 讀取者 | 可以檢視所有資源，但是無法編輯。 |
    | 測試參與者 | 可以管理測試資源。 |
    | 目錄參與者 | 可以管理解決方案發行資源。 |

## <a name="set-up-your-tenant"></a>設定您的租用戶

在 **Azure Stack 驗證服務**應用程式中設定您的租用戶。 

1. 將租用戶相關的下列資訊傳送給 Microsoft：vaashelp@microsoft.com。

    | 資料 | 說明 |
    |--------------------------------|---------------------------------------------------------------------------------------------|
    | 組織名稱 | 官方組織名稱。 |
    | Azure AD 租用戶目錄名稱 | 要註冊的 Azure AD 租用戶目錄名稱。 |
    | Azure AD 租用戶目錄識別碼 | 與目錄相關聯的 Azure AD 租用戶目錄 GUID。<br> 如需如何尋找 Azure AD 租用戶目錄識別碼的詳細資訊，請參閱「[取得租用戶識別碼](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#get-tenant-id)」。 |

    

2. Azure Stack 小組會提供確認，確保您的租用戶可以使用 VaaS 入口網站。

3. 使用租用戶的全域管理員認證，來登入 [VaaS 入口網站](https://azurestackvalidation.com/
)。 選取 [我的帳戶]。

    ![登入 VaaS 入口網站](media/vaas_portalsignin.png)

3. 網站會提示您授予 VaaS 的存取權。 接受條款才能繼續。

## <a name="assign-user-roles"></a>指派使用者角色

若要指派使用者角色：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在 [身分識別] 群組中選取 [所有服務] > [Azure Active Directory]。
3. 選取 [企業應用程式] > [Azure Stack 驗證服務] 應用程式。
4. 選取 [使用者和群組]。 [Azure Stack 驗證服務 - 使用者和群組] 刀鋒視窗會列出具有使用應用程式權限的使用者。
5. 選取 [+ 新增使用者] 以新增指派。

## <a name="create-an-azure-storage-blob-to-store-logs"></a>建立用來儲存記錄的 Azure 儲存體 Blob

VaaS 會在執行驗證測試時，建立診斷記錄。 您需要 Azure Blob 服務 SAS URL 的位置，以儲存您的記錄。 儲存體帳戶也可以用來儲存 OEM 自訂套件。

這些步驟會逐步引導如何為 Azure 儲存體帳戶設定及產生儲存體即服務 (SAS) URI，以及在入口網站中開始測試時，要在 VaaS 入口網站的哪個位置指定儲存體帳戶。

### <a name="create-an-azure-storage-account"></a>建立 Azure 儲存體帳戶

1. 若要建立儲存體帳戶，請依照指示，[建立儲存體帳戶](../../storage/common/storage-quickstart-create-account.md)。

2. 選取儲存體帳戶的類型時，請選取 **Blob 儲存體**帳戶類型。

### <a name="generate-a-sas-url-for-the-storage-account"></a>為儲存體帳戶產生 SAS URL

1. 巡覽至上方所建立的儲存體帳戶。

2. 在 [設定] 之下的刀鋒視窗上，選取 [共用存取簽章]。

3. 從 [允許的服務] 選項中，僅核取 [Blob] (取消核取其餘項目)。

4. 從 [允許的資源類型] 中核取 [服務]、**容器，以及 [物件]。

5. 從 [允許的權限] 中核取 [讀取]、[寫入]、[列出]、[新增]、[建立] (取消核取其餘項目)。

6. 將 [開始時間] 設為目前時間，以及將 [結束時間] 設為目前時間起的三個月。

7. 選取 [產生 SAS 與連接字串]，並且儲存 [Blob 服務 SAS URL] 字串。

> [!Note]  
> SAS URL 會在產生 URL 時所設定的結束時間到期。 請先確定 URL 充分有效，再與產品小組共用以進行偵錯，或者 URL 在排程測試時其有效期限超過 30 天。

## <a name="next-steps"></a>後續步驟

- 使用 VaaS 本機代理程式來檢查您的硬體。 如需指示，請參閱[部署本機代理程式並測試虛擬機器](azure-stack-vaas-test-vm.md)。
- 深入了解 [Azure Stack 驗證即服務](https://docs.microsoft.com/azure/azure-stack/partner)。