---
title: 如何重新啟用已停用的 Azure 存取控制服務 (ACS) 命名空間
description: 了解如何尋找並啟用您的 Azure 存取控制服務 (ACS) 命名空間，並要求將它們的維持啟用時間延長至 2019 年 2 月 4 日。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/07/2018
ms.author: celested
ms.reviewer: jlu
ms.custom: aaddev
ms.openlocfilehash: 0f0de122dc3dbd770e91a8412430423bee222b30
ms.sourcegitcommit: 0fc99ab4fbc6922064fc27d64161be6072896b21
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2018
ms.locfileid: "51577939"
---
# <a name="how-to-reactivate-disabled-access-control-service-namespaces"></a>操作說明：重新啟用已停用的存取控制服務命名空間

我們在 2017 年 11 月宣布 Microsoft Azure 存取控制服務 (ACS) 這項 Azure Active Directory (Azure AD) 服務，將於 2018 年 11 月 7 日淘汰。

從那時開始，我們已在 ACS 2018 年 11 月 7 日淘汰日期的 12 個月、9 個月、6 個月、3 個月、1個月、2 週、1 週和 1 天之前，傳送電子郵件至 ACS 訂用帳戶的系統管理員電子郵件地址。

在 2018 年 10 月 3 日時，我們宣布 (透過電子郵件和[部落格文章](https://azure.microsoft.com/blog/one-month-retirement-notice-access-control-service/)) 為無法在 2018 年 11 月 7 日之前完成遷移的客戶提供延長時間。 公告也包含要求延長遷移時間的指示。

## <a name="why-your-namespace-is-disabled"></a>為何您的命名空間遭到停用

如果您還沒有選擇加入延長時間，我們將從 2018 年 11 月 7 日開始停用 ACS 命名空間。 如果您錯過延長機會並仍想要加入延長至 2019 年 2 月 4 日的行列，請遵循下列各節中的指示。

> [!NOTE]
> 您必須是訂用帳戶的服務管理員或共同管理員，才能執行 PowerShell 命令並要求延長。

## <a name="find-and-enable-your-acs-namespaces"></a>請尋找並啟用您的 ACS 命名空間

您可以使用 ACS PowerShell 列出您所有的 ACS 命名空間，並重新啟用已停用的命名空間。

1. 下載並安裝 ACS PowerShell：
    1. 移至 PowerShell 資源庫並下載 [Acs.Namespaces](https://www.powershellgallery.com/packages/Acs.Namespaces/1.0.2)。
    1. 安裝模組：

        ```powershell
        Install-Module -Name Acs.Namespaces
        ```

    1. 取得所有可能命令的清單：

        ```powershell
        Get-Command -Module Acs.Namespaces
        ```

        若要取得特定命令的說明，請執行：

        ```
        Get-Help [Command-Name] -Full
        ```
    
        其中 `[Command-Name]` 是 ACS 命令的名稱。
1. 使用 **Connect-AcsAccount** Cmdlet 連線至 ACS。 

    您可能需要先執行 **Set-ExecutionPolicy** 來變更執行原則，才能執行命令。
1. 使用 **Get-AcsSubscription** Cmdlet 列出可用的 Azure 訂用帳戶。
1. 使用 **Get-AcsNamespace** Cmdlet 列出您的 ACS 命名空間。
1. 透過確認 `State` 為 `Disabled` 以確認已停用的命名空間。

    [![確認已停用的命名空間](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png)](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png#lightbox)

    您也可以使用 `nslookup {your-namespace}.accesscontrol.windows.net` 確認網域是否仍在作用中。

1. 使用 **Enable-AcsNamespace** Cmdlet 啟用您的 ACS 命名空間。

    一旦您啟用命名空間之後，就可以要求延長，命名空間就不會在 2019 年 2 月 4 日之前再次停用。 從該日期開始，對 ACS 提出的所有要求都會失敗。

## <a name="request-an-extension"></a>要求延長

1. 移至 `https://{your-namespace}.accesscontrol.windows.net` 以瀏覽至您 ACS 命名空間的管理入口網站。
1. 選取 [閱讀條款] 按鈕來閱讀[已更新使用規定](https://azure.microsoft.com/support/legal/access-control/)，這會將您導向到包含已更新使用規定的頁面。

    [![選取 [閱讀條款] 按鈕](./media/howto-reactivate-disabled-acs-namespaces/read-terms-button-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/read-terms-button-expanded.png#lightbox)

1. 在頁面頂端的橫幅上選取 [要求延長]。 只有在您閱讀完[已更新使用規定](https://azure.microsoft.com/support/legal/access-control/)之後，按鈕才會啟用。

    [![選取 [要求延長] 按鈕](./media/howto-reactivate-disabled-acs-namespaces/request-extension-button-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/request-extension-button-expanded.png#lightbox)

1. 註冊延長要求之後，頁面會重新整理並在頁面頂端顯示新橫幅。

    [![包含已重新整理橫幅的更新後頁面](./media/howto-reactivate-disabled-acs-namespaces/updated-banner-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/updated-banner-expanded.png#lightbox)

## <a name="help-and-support"></a>說明及支援

- 如果您遵循此操作說明之後遇到任何問題，請連絡 [Azure 支援](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。
- 如果您有和 ACS 淘汰有關的問題或意見反應，請透過 acsfeedback@microsoft.com 與我們聯絡。

## <a name="next-steps"></a>後續步驟

- 檢閱[操作說明：從 Azure 存取控制服務遷移](active-directory-acs-migration.md)中的 ACS 淘汰相關資訊。
