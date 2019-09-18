---
title: 針對 Azure EA 入口網站存取進行疑難排解
description: 本文將說明 Azure EA 入口網站中可能發生的一些 Azure Enterprise 合約 (EA) 常見問題。
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/09/2019
ms.topic: troubleshooting
ms.service: billing
manager: boalcsva
ms.openlocfilehash: a2214ef0dd3b7c4f706ebbb529b6c44e956f9242
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2019
ms.locfileid: "70900874"
---
# <a name="troubleshoot-azure-ea-portal-access"></a>針對 Azure EA 入口網站存取進行疑難排解

本文將說明 Azure Enterprise 合約 (EA) 可能發生的一些常見問題。 Azure EA 入口網站可用來管理 Enterprise 合約使用者和成本。 當您設定或更新 Azure EA 入口網站的存取權時，可能會遇到這些問題。

## <a name="issues-adding-an-admin-to-an-enrollment"></a>將系統管理員新增至註冊的問題

企業註冊有不同類型的驗證層級。 如果未正確套用驗證層級，當您嘗試登入 Azure EA 入口網站時，可能會發生問題。

您可以使用 Azure EA 入口網站，將存取權授與不同驗證層級的使用者。 企業系統管理員可以更新驗證層級，以符合其組織的安全性需求。

### <a name="authentication-level-types"></a>驗證層級類型

- 僅限 Microsoft 帳戶 - 適用於想要透過 Microsoft 帳戶使用、建立和管理使用者的組織。
- 公司或學校帳戶 - 適用於已設定 Active Directory 與雲端同盟，且所有帳戶都在單一租用戶上的組織。
- 跨租用戶的公司或學校帳戶 - 適用於已設定 Active Directory 與雲端同盟，且在多個租用戶上有帳戶的組織。
- 混合帳戶 - 可讓您使用 Microsoft 帳戶和/或使用公司或學校帳戶新增使用者。

新增至註冊的第一個公司或學校帳戶會決定「預設」  或「主要」  網域。 若要以另一個租用戶新增公司或學校帳戶，您必須將註冊下的驗證層級變更為跨租用戶驗證。

若要更新驗證層級：

1. 以企業系統管理員身分登入 Azure EA 入口網站。
2. 從左方導覽面板按一下 [管理]  。
3. 按一下 [註冊]  索引標籤。
4. 在 [註冊詳細資料]  底下，選取 [驗證層級]  。
5. 按一下鉛筆符號。
6. 按一下 [檔案]  。

![顯示驗證層級的範例 ](./media/billing-ea-portal-troubleshoot/create-ea-authentication-level-types.png)

Microsoft 帳戶必須在 [https://signup.live.com](https://signup.live.com/) 上建立相關聯的識別碼。

公司或學校帳戶適用於已設定 Active Directory 同盟，且所有帳戶都在單一租用戶上的組織。 如果公司的內部 Active Directory 是同盟的，則可以使用公司或學校的同盟使用者驗證來新增使用者。

如果您的組織未使用 Active Directory 同盟，您就無法使用公司或學校電子郵件地址。 請改為註冊或建立新的電子郵件地址，並將其註冊為 Microsoft 帳戶。

## <a name="unable-to-access-the-azure-ea-portal"></a>無法存取 Azure EA 入口網站

如果您在嘗試登入 Azure EA 入口網站時收到錯誤訊息，請使用下列疑難排解步驟：

- 請確定您是使用正確的 Azure EA 入口網站 URL，也就是 https://ea.azure.com 。
- 判斷您是否已將 Azure EA 入口網站的存取新增為公司或學校帳戶，或是 Microsoft 帳戶。
  - 如果您使用的是公司帳戶，請輸入您的公司電子郵件和公司密碼。 您的組織會提供您公司密碼。 如果您遇到密碼相關問題，可以向 IT 部門詢問如何重設密碼。
  - 如果您使用的是 Microsoft 帳戶，請輸入您的 Microsoft 帳戶電子郵件地址和密碼。 如果您忘記 Microsoft 帳戶密碼，可以在 [https://account.live.com/password/reset](https://account.live.com/password/reset) 上重設密碼。
- 請使用私密或無痕瀏覽器工作階段進行登入，如此便不會保留先前或現有工作階段中的 Cookie 或快取資訊。 清除瀏覽器的快取，並使用私密或無痕視窗來開啟 https://ea.azure.com 。
- 如果您在使用 Microsoft 帳戶時收到「無效使用者」  錯誤，可能是因為您有多個 Microsoft 帳戶。 您嘗試用來登入的使用者不是主要的電子郵件地址。
或者，如果您收到「無效使用者」  錯誤，可能是因為使用者新增至註冊時使用了錯誤的帳戶類型。 例如，使用了公司或學校帳戶，而不是 Microsoft 帳戶。 在此範例中，您可以讓另一位 EA 系統管理員新增正確的帳戶，或您需要連絡[支援人員](https://support.microsoft.com/supportforbusiness/productselection?sapId=cf791efa-485b-95a3-6fad-3daf9cd4027c)。

## <a name="no-activation-email-received"></a>未收到啟用電子郵件

來自 Azure EA 入口網站的啟用電子郵件會從 waep@microsoft.com  寄出。 如果您未收到啟用電子郵件，請檢查電子郵件的垃圾郵件資料夾。 郵件主旨是「檢視/管理 Microsoft Azure 服務的邀請_主題」  。 該郵件會傳送給每位新增的 EA 系統管理員。

如果您確定您已設定為 EA 系統管理員，則不需要等待收到啟用電子郵件才能登入 Azure EA 入口網站。 相反地，您可以移至 https://ea.azure.com ，並使用您的電子郵件地址 (公司、學校或 Microsoft 帳戶) 和密碼登入。

如果您需要檢查主要別名，請移至 [https://account.live.com](https://account.live.com)。 然後，按一下 [您的資訊]  ，並按一下 [管理登入 Microsoft 的方式]  。 遵循提示以確認替代電子郵件地址，並取得代碼來存取機密資訊。 輸入安全性驗證碼。 如果您不想要設定雙重要素驗證，請選取 [稍後設定]  。

您會看到 [管理登入 Microsoft 的方式]  頁面，其中會顯示您擁有的帳戶別名。 檢查主要別名是否為您用來登入 Azure EA 入口網站的別名。 如果不是，您可以將其設為您的主要別名。 或者，您可以改為將此主要別名用於 Azure EA 入口網站。

## <a name="next-steps"></a>後續步驟

- Azure EA 入口網站系統管理員應閱讀 [ Azure EA 入口網站的系統管理](billing-ea-portal-administration.md)，以了解常見的管理工作。
