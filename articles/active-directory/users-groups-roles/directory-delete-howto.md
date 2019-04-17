---
title: 刪除 Azure AD 目錄-Azure Active Directory |Microsoft Docs
description: 說明如何準備 Azure AD 目錄刪除，包括自助服務目錄
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/15/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 91ac6b4530414850c52605bac8cb701aa2b877d4
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2019
ms.locfileid: "59607291"
---
# <a name="delete-a-directory-in-azure-active-directory"></a>刪除 Azure Active Directory 中的目錄

刪除 Azure AD 目錄時，會一併刪除目錄中所包含的所有資源。 藉由減少其相關聯的資源刪除之前，先準備您的組織。 只有 Azure Active Directory (Azure AD) 的全域系統管理員可以從入口網站刪除 Azure AD 目錄。

## <a name="prepare-the-directory"></a>準備目錄

它會傳遞數次檢查之前的 Azure AD 中，您無法刪除目錄。 這些檢查會降低風險，刪除 Azure AD 目錄造成負面影響使用者的存取權，例如登入在 Azure 中的 Office 365 或存取資源的能力。 比方說，如果不小心刪除訂用帳戶相關聯的目錄，則使用者無法存取該訂用帳戶的 Azure 資源。 會檢查下列條件：

* 若要刪除的目錄一位全域系統管理員以外的目錄中可以有任何使用者。 必須先刪除任何其他使用者，才能刪除目錄。 如果從內部部署同步處理使用者，然後同步處理，必須先關閉，並必須使用 Azure 入口網站或 Azure PowerShell cmdlet 的雲端目錄中刪除的使用者。
* 目錄中可能沒有任何應用程式。 必須先移除任何應用程式，才能刪除目錄。
* 可以有任何連結至目錄的多重要素驗證提供者。
* 任何 Microsoft Online Service 都沒有任何訂用帳戶 (例如與目錄相關聯 Microsoft Azure、Office 365 或 Azure AD Premium)。 例如，在 Azure 中建立預設目錄時，如果您的 Azure 訂用帳戶仍然依賴此目錄來進行驗證，則無法刪除此目錄。 同樣地，如果另一位使用者擁有與目錄相關聯的訂用帳戶，您無法刪除目錄。

## <a name="delete-the-directory"></a>刪除目錄

1. 登入[Azure AD 系統管理中心](https://aad.portal.azure.com)是貴組織的全域系統管理員的帳戶。

2. 選取 **Azure Active Directory**。

3. 切換至您想要刪除的目錄。
  
   ![確認刪除之前的組織](./media/directory-delete-howto/delete-directory-command.png)

4. 選取 [刪除目錄]。
  
   ![選取要刪除的組織的命令](./media/directory-delete-howto/delete-directory-list.png)

5. 如果您的目錄未通過一或多個檢查，系統會提供您有關如何將傳遞的詳細資訊的連結。 在您通過所有檢查之後，選取 [刪除] 即可完成流程。

## <a name="if-you-cant-delete-the-directory"></a>如果您無法刪除目錄

當您設定 Azure AD 目錄時，您可能也啟用授權為基礎的訂用帳戶為您的組織，例如 Azure AD Premium P2、 Office 365 商務進階版或 Enterprise Mobility + Security E5。 若要避免發生意外資料遺失，您無法刪除目錄，直到完全刪除訂用帳戶。 訂用帳戶必須位於**已取消佈建**狀態，以允許目錄刪除。 **已過期**或是**Canceled**訂用帳戶移至**已停用**的狀態和最後一個階段是**已取消佈建**狀態。

至於試用 Office 365 訂用帳戶過期時會發生的情況 (不含付費合作夥伴/CSP、Enterprise 合約或大量授權)，請見下表。 如需 Office 365 資料保留期與訂用帳戶生命週期的詳細資訊，請參閱 [What happens to my data and access when my Office 365 for business subscription ends?](https://support.office.com/article/what-happens-to-my-data-and-access-when-my-office-365-for-business-subscription-ends-4436582f-211a-45ec-b72e-33647f97d8a3) (當商務用 Office 365 訂閱結束時，我的資料和存取權會如何？)。 

訂用帳戶狀態 | 資料 | 資料存取權
----- | ----- | -----
有效 (試用 30 天) | 資料可供全體存取 | 使用者可以正常存取 Office 365 檔案或應用程式<br>系統管理員有一般 Microsoft 365 系統管理中心和資源的存取 
過期 (30 天) | 資料可供全體存取| 使用者可以正常存取 Office 365 檔案或應用程式<br>系統管理員有一般 Microsoft 365 系統管理中心和資源的存取
停用 (30 天) | 資料僅供管理員存取 | 使用者無法存取 Office 365 檔案或應用程式<br>系統管理員可以存取 Microsoft 365 系統管理中心，但無法指派授權或更新使用者
取消佈建 (「停用」後 30 天) | 資料已刪除 (若沒有其他使用中的服務，就會自動刪除) | 使用者無法存取 Office 365 檔案或應用程式<br>系統管理員可以存取 Microsoft 365 系統管理中心購買及管理其他訂用帳戶

## <a name="delete-a-subscription"></a>刪除訂閱

您可以將訂用帳戶放入已取消佈建狀態中使用 Microsoft 365 系統管理中心的三天刪除。

1. 登入[Microsoft 365 系統管理中心](https://admin.microsoft.com)與您組織中的全域管理員帳戶。 如果您嘗試刪除 contoso.onmicrosoft.com 的初始預設網域"Contoso"目錄，以登入的 UPN 例如admin@contoso.onmicrosoft.com。

2. 選取 **計費**，然後選取**訂用帳戶**，然後選擇您想要取消的訂用帳戶。 在按一下 [取消] 後，重新整理頁面。
  
   ![用於刪除訂用帳戶的 [刪除] 連結](./media/directory-delete-howto/delete-command.png)
  
3. 選取 [刪除] 以刪除訂用帳戶並接受條款及條件。 所有資料都會在三天內永久刪除。 若您改變了心意，在這三天內都可以重新啟用訂用帳戶。
  
   ![請仔細閱讀條款及條件](./media/directory-delete-howto/delete-terms.png)

4. 訂用帳戶狀態現已變更，而訂用帳戶會標示為待刪除。 訂用帳戶會在 72 小時候進入 [已取消佈建] 狀態。

5. 一旦您已在您目錄中，刪除訂用帳戶並經過 72 小時，您可以登入回 Azure AD 系統管理中心有再應該沒有必要的動作，並封鎖您的目錄刪除沒有訂用帳戶。 您應該能夠成功地刪除您的 Azure AD 目錄。
  
   ![刪除畫面上的通過訂用帳戶檢查](./media/directory-delete-howto/delete-checks-passed.png)

## <a name="i-have-a-trial-subscription-that-blocks-deletion"></a>我有封鎖刪除試用版訂用帳戶

有[自助式註冊產品](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide)像 Microsoft Power BI、 Rights Management Services、 Microsoft Power 應用程式或 Dynamics 365 的個別使用者可以註冊透過 Office 365，這也會建立來賓使用者的驗證您的 Azure AD 目錄。 這些自助服務的產品會封鎖目錄刪除，直到它們完全刪除從目錄中，以避免資料遺失。 它們可以刪除只能由 Azure AD 系統管理員的使用者不論個別註冊或是已指派產品。

有兩種類型的自助式註冊產品指派的方式： 

* 組織層級指派：Azure AD 系統管理員會將產品指派給整個組織和使用者可以主動使用服務與這個組織層級指派即使不個別授權。
* 使用者層級指派：個別使用者在自助式註冊期間基本上給產品本身而不是系統管理員。當組織會變成受管理的系統管理員 (請參閱[系統管理員身分接管非受控目錄](domains-admin-takeover.md)，則系統管理員可以直接將指派給產品沒有自助式註冊的使用者。  

當您開始自助式註冊產品的刪除時，動作永久刪除資料，並移除所有使用者存取服務。 任何已指派該供應項目，個別地或組織層級的使用者則會封鎖登入或存取任何現有的資料。 如果您想要防止資料遺失的自助式註冊產品也[Microsoft Power BI 儀表板](https://docs.microsoft.com/power-bi/service-export-to-pbix)或是[Rights Management Services 原則組態](https://docs.microsoft.com/azure/information-protection/configure-policy#how-to-configure-the-azure-information-protection-policy)，確保資料的備份和儲存在其他地方。

如需目前可用的自助式註冊產品和服務的詳細資訊，請參閱[可用的 「 自助 」 程式](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide#available-self-service-programs)。

至於試用 Office 365 訂用帳戶過期時會發生的情況 (不含付費合作夥伴/CSP、Enterprise 合約或大量授權)，請見下表。 如需有關 Office 365 資料保留和訂用帳戶生命週期的詳細資訊，請參閱 <<c0>  [ 我的 Office 365 商務訂用帳戶結束時，我的資料和存取會發生什麼事？](https://docs.microsoft.com/office365/admin/subscriptions-and-billing/what-if-my-subscription-expires?view=o365-worldwide)。

產品狀態 | 資料 | 資料存取權
------------- | ---- | --------------
有效 (試用 30 天) | 資料可供全體存取 | 使用者可以自助註冊的產品、 檔案或應用程式的一般存取<br>系統管理員有一般 Microsoft 365 系統管理中心和資源的存取
Deleted | 刪除的資料 | 使用者無法存取自助註冊的產品、 檔案或應用程式<br>系統管理員可以存取 Microsoft 365 系統管理中心購買及管理其他訂用帳戶

## <a name="how-can-i-delete-a-self-service-sign-up-product-in-the-azure-portal"></a>如何刪除 Azure 入口網站中，自助服務的註冊產品？

您可以將自助式註冊產品如 Microsoft Power BI 或 Azure Rights Management Services 到放**刪除**立即在 Azure AD 入口網站中刪除的狀態。

1. 登入 [Azure AD 系統管理中心](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) 與組織中的全域管理員帳戶。 如果您嘗試刪除 contoso.onmicrosoft.com 的初始預設網域"Contoso"目錄，使用登入的 UPN 例如admin@contoso.onmicrosoft.com。

2. 選取 **授權**，然後選取 **自助式註冊產品**。 您可以看到所有的自助式註冊產品分開以基座為基礎的訂用帳戶。 選擇您想要永久刪除此的產品。 以下是 Microsoft Power BI 中的範例：

    ![使用者名稱輸入錯誤，或是找不到](./media/directory-delete-howto/licenses-page.png)

3. 選取  **刪除** 刪除產品，並接受條款立即且無法變更，會刪除該資料。 此刪除動作會移除所有使用者，並都移除對產品的組織存取權。 按一下 [是] 繼續進行刪除。  

    ![使用者名稱輸入錯誤，或是找不到](./media/directory-delete-howto/delete-product.png)

4. 當您選取**是**，便會起始自助式產品刪除。 沒有通知，告訴您刪除作業的進行中。  

    ![使用者名稱輸入錯誤，或是找不到](./media/directory-delete-howto/progress-message.png)

5. 現在的自助式註冊的產品狀態已變更為**Deleted**。 當您重新整理頁面時，應該從移除產品**自助式註冊產品**頁面。  

    ![使用者名稱輸入錯誤，或是找不到](./media/directory-delete-howto/product-deleted.png)

6. 一旦您已刪除的所有產品，您可以再次簽署回 Azure AD 系統管理中心，並應具有所需的動作及封鎖您的目錄刪除任何產品。 您應該能夠成功地刪除您的 Azure AD 目錄。

    ![使用者名稱輸入錯誤，或是找不到](./media/directory-delete-howto/delete-organization.png)

## <a name="next-steps"></a>後續步驟

[Azure Active Directory 文件](https://docs.microsoft.com/azure/active-directory/)
