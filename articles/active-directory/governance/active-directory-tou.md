---
title: Azure Active Directory 使用規定 | Microsoft Docs
description: 描述如何開始使用 Azure AD 使用規定來在員工或來賓取得存取權之前向其呈現資訊。
services: active-directory
author: rolyon
manager: mtillman
editor: ''
ms.assetid: d55872ef-7e45-4de5-a9a0-3298e3de3565
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: compliance
ms.date: 01/10/2019
ms.author: rolyon
ms.openlocfilehash: 341565bf621fa63ad578489cd04bcfff3510265b
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2019
ms.locfileid: "54229467"
---
# <a name="azure-active-directory-terms-of-use-feature"></a>Azure Active Directory 使用規定特性
Azure AD 使用規定提供一種簡單的方法，組織可用來將資訊呈現給終端使用者。 此呈現可確保使用者看到合法或合規性需求的相關免責聲明。 本文將說明如何開始使用使用規定。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview-videos"></a>概觀影片

下列影片提供使用規定的快速概觀。

>[!VIDEO https://www.youtube.com/embed/tj-LK0abNao]

如需其他影片，請參閱：
- [如何在 Azure Active Directory 中部署使用規定](https://www.youtube.com/embed/N4vgqHO2tgY)
- [如何在 Azure Active Directory 中推出使用規定](https://www.youtube.com/embed/t_hA4y9luCY)

## <a name="what-can-i-do-with-terms-of-use"></a>我可以用使用規定來做什麼？
Azure AD 使用規定具有下列功能：
- 要求員工或來賓在取得存取權之前，先接受您的使用規定。
- 要求員工或來賓在取得存取權之前，於每一個裝置上先接受您的使用規定。
- 要求員工或來賓週期性地接受您的使用規定。
- 發佈適用於您組織中所有使用者的一般使用規定。
- 發佈以使用者屬性為基礎的特定使用規定 (例如， 醫生與護士或國內員工與國際員工，透過使用[動態群組](../users-groups-roles/groups-dynamic-membership.md)完成)。
- 發佈在存取高商業影響應用程式 (例如 Salesforce) 時的特定使用規定。
- 發佈不同語言的使用規定。
- 列出已接受或未接受使用規定的人員。
- 協助符合隱私權法規。
- 顯示使用規定活動的記錄，以符合合規性及進行稽核。
- 使用 [Microsoft Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/agreement) \(英文\) (目前處於預覽狀態) 來建立和管理使用規定。

## <a name="prerequisites"></a>必要條件
若要使用及設定 Azure AD 使用規定，您必須有：

- Azure AD Premium P1、P2、EMS E3 或 EMS E5 訂用帳戶。
    - 如果您沒有其中任何一個訂用帳戶，您可以[取得 Azure AD Premium](../fundamentals/active-directory-get-started-premium.md) 或[啟用 Azure AD Premium 試用版](https://azure.microsoft.com/trial/get-started-active-directory/)。
- 下列其中一個管理帳戶，將用於您要設定的目錄：
    - 全域管理員
    - 安全性系統管理員
    - 條件式存取系統管理員

## <a name="terms-of-use-document"></a>使用規定文件

Azure AD 使用規定使用 PDF 格式來呈現內容。 此 PDF 檔案可以是任何內容 (例如現有的合約文件) 可讓您在使用者登入期間收集終端使用者合約。 若要支援行動裝置上的使用者，建議在 PDF 中使用 24 點的字型大小。

## <a name="add-terms-of-use"></a>新增使用規定
一旦完成了您的使用規定文件，請使用下列程序來新增它。

1. 以全域管理員、安全性系統管理員或條件式存取系統管理員的身分登入 Azure。

1. 瀏覽至 [https://aka.ms/catou](https://aka.ms/catou) 上的 [使用規定]。

    ![[使用規定] 刀鋒視窗](./media/active-directory-tou/tou-blade.png)

1. 按一下 [新增規定]。

    ![新增 TOU](./media/active-directory-tou/new-tou.png)

1. 在 [名稱] 方塊中，輸入要用於 Azure 入口網站中的使用規定名稱。

1. 在 [顯示名稱] 方塊中，輸入使用者在登入時會看見的標題。

1. 針對 [使用規定文件]，請瀏覽至您最終版本的的使用規定 PDF 並加以選取。

1. 選取使用規定文件的語言。 語言選項可讓您上傳多個使用規定，各有不同的語言。 終端使用者會看到的使用規定版本將以其瀏覽器喜好設定為基礎。

1. 若要要求使用者在接受使用規定之前必須檢閱其內容，請將 [要求使用者展開使用規定] 設定為 [開啟]。

1. 若要要求使用者在其所存取的所有裝置上接受您的使用規定，請將 [需要使用者在每部裝置上同意] 設定為 [開啟]。 如需詳細資訊，請參閱[每部裝置的使用規定](#per-device-terms-of-use)。

1. 如果您想要定期使針對使用規定的同意到期，請將 [到期同意] 設定為 [開啟]。 設定為 [開啟] 時，系統會顯示兩個額外的排程設定。

    ![到期同意](./media/active-directory-tou/expire-consents.png)

1. 使用 [到期生效時間] 和 [頻率] 設定來指定使用規定到期的排程。 下表會顯示幾個範例設定的結果：

    | 到期生效時間 | 頻率 | 結果 |
    | --- | --- | --- |
    | 今天的日期  | 每月 | 從今天開始，使用者必須接受使用規定，且必須於每個月重新接受。 |
    | 未來的日期  | 每月 | 從今天開始，使用者必須接受使用規定。 抵達未來的日期時，同意將會到期，且使用者必須於每個月重新接受。  |

    例如，如果您將 [到期生效時間] 設定為 [1 月 1 日]，並將 [頻率] 設定為 [每月]，則下列兩個使用者所遇到的到期方式將如下所示：

    | 使用者 | 第一次接受日期 | 第一次到期日期 | 第二次到期日期 | 第三次到期日期 |
    | --- | --- | --- | --- | --- |
    | Alice | 1 月 1 日 | 2 月 1 日 | 3 月 1 日 | 4 月 1 日 |
    | Bob | 1 月 15 日 | 2 月 1 日 | 3 月 1 日 | 4 月 1 日 |

1. 使用 [需要重新接受之前的期間 (天)] 設定來指定使用者必須重新接受使用規定之前的天數。 這可讓使用者遵循自己的排程。 例如，如果您將期間設定為 **30** 天，則下列兩個使用者所遇到的到期方式將如下所示：

    | 使用者 | 第一次接受日期 | 第一次到期日期 | 第二次到期日期 | 第三次到期日期 |
    | --- | --- | --- | --- | --- |
    | Alice | 1 月 1 日 | 1 月 31 日 | 3 月 2 日 | 4 月 1 日 |
    | Bob | 1 月 15 日 | 2 月 14 日 | 3 月 16 日 | 4 月 15 日 |

    您可以同時使用 [到期同意] 和 [需要重新接受之前的期間 (天)] 設定，但通常您只會使用其中一個。

1. 在 [條件式存取] 下，使用 [搭配條件式存取原則範本強制執行] 清單來選取要強制執行使用規定的範本。

    ![條件式存取範本](./media/active-directory-tou/conditional-access-templates.png)

    | 範本 | 說明 |
    | --- | --- |
    | **所有來賓的雲端應用程式存取權** | 系統將會針對所有來賓和所有雲端應用程式建立條件式存取原則。 此原則會影響 Azure 入口網站。 建立此原則之後，您可能需要登出然後再登入。 |
    | **所有使用者的雲端應用程式存取權** | 系統將會針對所有使用者和所有雲端應用程式建立條件式存取原則。 此原則會影響 Azure 入口網站。 建立此原則之後，您必須登出然後再登入。 |
    | **自訂原則** | 選取這份使用規定適用的使用者、群組及應用程式。 |
    | **稍後建立條件式存取原則** | 此使用規定將會在建立條件式存取原則時在授與控制清單中出現。 |

    >[!IMPORTANT]
    >條件式存取原則控制項 (包括使用規定) 不支援服務帳戶的強制執行。 建議您排除條件式存取原則中的所有服務帳戶。

     自訂的條件式存取原則可讓細微使用條款下降到特定雲端應用程式或使用者群組。 如需詳細資訊，請參閱[快速入門：必須接受使用規定才可存取雲端應用程式](../conditional-access/require-tou.md)。

1. 按一下頁面底部的 [新增] 。

    如果您已選取自訂的條件式存取範本，則會出現新的畫面以讓您建立自訂條件式存取原則。

    ![自訂原則](./media/active-directory-tou/custom-policy.png)

    您現在應該會看到新的使用規定。

    ![新增 TOU](./media/active-directory-tou/create-tou.png)

## <a name="view-report-of-who-has-accepted-and-declined"></a>檢視已接受和已拒絕的人員報表
[使用規定] 刀鋒視窗會顯示已接受和已拒絕的使用者計數。 這些計數和接受/拒絕人員會加以儲存，直到使用規定的存留期結束。

1. 登入 Azure 並瀏覽至 [https://aka.ms/catou](https://aka.ms/catou) 上的 [使用規定]。

    ![[使用規定] 刀鋒視窗](./media/active-directory-tou/view-tou.png)

1. 針對使用規定，按一下 [已接受] 或 [已拒絕] 下方的數字，來檢視使用者目前的狀態。

    ![針對使用規定的同意](./media/active-directory-tou/accepted-tou.png)

1. 若要檢視個別使用者的歷程記錄，請按一下省略符號 (**...**)，然後按一下 [檢視記錄]。

    ![[檢視記錄] 功能表](./media/active-directory-tou/view-history-menu.png)

    在 [檢視記錄] 窗格中，您可以看見所有接受、拒絕及到期的歷程記錄。

    ![[檢視記錄] 窗格](./media/active-directory-tou/view-history-pane.png)

## <a name="view-azure-ad-audit-logs"></a>檢視 Azure AD 稽核記錄
如果您想要檢視其他活動，Azure AD 使用規定包含稽核記錄。 使用者每次同意時都會觸發稽核記錄事件，並將儲存 **30 天**。 您可以在入口網站中檢視這些記錄或將其下載為 .csv 檔案。

若要開始使用 Azure AD 稽核記錄，請使用下列程序：

1. 登入 Azure 並瀏覽至 [https://aka.ms/catou](https://aka.ms/catou) 上的 [使用規定]。

1. 選取使用規定。

1. 按一下 [檢視稽核記錄]。

    ![[使用規定] 刀鋒視窗](./media/active-directory-tou/audit-tou.png)

1. 在 Azure AD 稽核記錄畫面上，您可以使用提供的清單來篩選資訊，以找出特定的稽核記錄資訊。

    您也可以按一下 [下載]，將資訊下載成 .csv 檔案，以在本機中使用。

    ![稽核記錄](./media/active-directory-tou/audit-logs-tou.png)

    如果您按一下某個記錄，螢幕會出現具有額外活動詳細資料的窗格。

    ![活動詳細資料](./media/active-directory-tou/audit-log-activity-details.png)

## <a name="what-terms-of-use-looks-like-for-users"></a>使用者看到的使用規定
一旦建立並強制執行使用規定，範圍內的使用者將會在登入時看到下列畫面。

![使用者網頁登入](./media/active-directory-tou/user-tou.png)

下列畫面顯示行動裝置上的使用規定。

![使用者行動裝置登入](./media/active-directory-tou/mobile-tou.png)

使用者只需要接受使用規定一次，後續登入時不會再看到使用規定。

### <a name="how-users-can-review-their-terms-of-use"></a>使用者可檢閱使用規定的方式
使用者可以使用下列程序，檢閱並查看他們已接受的使用規定。

1. 登入 [https://myapps.microsoft.com](https://myapps.microsoft.com)。

1. 在右上角，按一下您的名稱並選取 [設定檔]。

    ![設定檔](./media/active-directory-tou/tou14.png)

1. 在您的設定檔頁面上，按一下 [檢閱使用規定]。

    ![設定檔 - 檢閱使用規定](./media/active-directory-tou/tou13a.png)

1. 您可以在此檢閱您已接受的使用規定。

## <a name="edit-terms-of-use-details"></a>編輯使用規定詳細資料
您可以編輯使用規定的一些詳細資料，但無法修改現有的文件。 下列程序說明如何編輯詳細資料。

1. 登入 Azure 並瀏覽至 [https://aka.ms/catou](https://aka.ms/catou) 上的 [使用規定]。

1. 選取您要編輯的使用規定。

1. 按一下 [編輯使用規定]。

1. 在 [編輯使用規定] 窗格中，變更名稱、顯示名稱，或要求使用者展開值。

    如果有其他想要變更的設定，例如 PDF 文件、[需要使用者在每部裝置上同意]、[到期同意]、重新接受之前的期間，或是條件式存取原則，您必須建立新的使用規定。

    ![編輯使用規定](./media/active-directory-tou/edit-tou.png)

1. 按一下 [確定] 儲存變更。

    儲存您的變更後，使用者將不需要重新接受這些編輯後的規定。

## <a name="add-a-terms-of-use-language"></a>新增使用規定語言
下列程序說明如何新增使用規定語言。

1. 登入 Azure 並瀏覽至 [https://aka.ms/catou](https://aka.ms/catou) 上的 [使用規定]。

1. 選取您要編輯的使用規定。

1. 在 [詳細資料] 窗格中，按一下 [語言] 索引標籤。

    ![新增 TOU](./media/active-directory-tou/languages-tou.png)

1. 按一下 [新增語言]。

1. 在 [新增使用規定語言] 窗格中，上傳當地語系化的 PDF 並選取的語言。

    ![新增 TOU](./media/active-directory-tou/language-add-tou.png)

1. 按一下 [新增] 來新增語言。

## <a name="per-device-terms-of-use"></a>每部裝置的使用規定

[需要使用者在每部裝置上同意] 設定可讓您要求使用者在其所存取的所有裝置上接受您的使用規定。 使用者必須在 Azure AD 中加入其裝置。 裝置加入之後，系統將會使用裝置識別碼來對每個裝置強制執行使用規定。

以下是支援的平台與軟體清單。

> [!div class="mx-tableFixed"]
> |  | iOS | Android | Windows 10 | 其他 |
> | --- | --- | --- | --- | --- |
> | **原生應用程式** | 是 | 是 | 是 |  |
> | **Microsoft Edge** | 是 | 是 | 是 |  |
> | **Internet Explorer** | 是 | 是 | 是 |  |
> | **Chrome (搭配擴充功能)** | 是 | 是 | 是 |  |

每部裝置的使用規定具有下列限制：

- 一部裝置只能加入至單一租用戶。
- 使用者必須具有加入其裝置的權限。
- 不支援 Intune 註冊應用程式。

如果使用者的裝置未加入，他們將會收到要求加入其裝置的訊息。 使用者的體驗將取決於平台和軟體。

### <a name="join-a-windows-10-device"></a>加入 Windows 10 裝置

如果使用者是使用 Windows 10 和 Microsoft Edge，他們將會收到類似下列的訊息以[加入其裝置](../user-help/user-help-join-device-on-network.md#to-join-an-already-configured-windows-10-device)。

![Windows 10 和 Microsoft Edge - 加入裝置提示](./media/active-directory-tou/per-device-win10-edge.png)

如果他們是使用 Chrome，系統將會提示他們安裝 [Windows 10 Accounts 擴充功能](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji) \(英文\)。

### <a name="browsers"></a>瀏覽器

如果使用者是使用不支援的瀏覽器，系統將會提示他們使用其他瀏覽器。

![不支援的瀏覽器](./media/active-directory-tou/per-device-browser-unsupported.png)

## <a name="delete-terms-of-use"></a>刪除使用規定
您可以使用下列程序，刪除舊的使用規定。

1. 登入 Azure 並瀏覽至 [https://aka.ms/catou](https://aka.ms/catou) 上的 [使用規定]。

1. 選取您想要移除的使用規定。

1. 按一下 [刪除規定]。

1. 出現詢問您是否要繼續的訊息時，請按一下 [是]。

    ![刪除使用規定](./media/active-directory-tou/delete-tou.png)

    您應該再也看不到您的使用規定。

## <a name="deleted-users-and-active-terms-of-use"></a>已刪除的使用者和有效使用規定
根據預設，已刪除的使用者會在 Azure AD 中以已刪除的狀態保留 30 天，在這段期間內，系統管理員可以視需要加以還原。 經過 30 天後，該使用者將永久刪除。 此外，全域管理員可以使用 Azure Active Directory 入口網站，在該時段結束之前明確地[永久刪除最近刪除的使用者](../fundamentals/active-directory-users-restore.md)。 使用者永久刪除後，關於該使用者的資料將會從作用中的使用規定中移除。 與已刪除的使用者有關的稽核資訊仍會保留在稽核記錄中。

## <a name="policy-changes"></a>原則變更
條件式存取原則會立即生效。 當此情況發生時，系統管理員便會開始看到「悲傷雲端」或「Azure AD 權杖問題」。 系統管理員必須登出並再次登入，才能符合新的原則。

>[!IMPORTANT]
> 如果情況如下，範圍中的使用者必須登出並登入，才能符合新的原則：
> - 對使用規定啟用條件式存取原則
> - 或建立第二個使用條款

## <a name="b2b-guests-preview"></a>B2B 來賓 (預覽)

大部分的組織都具有供其員工同意該組織之使用規定和隱私權聲明的程序。 但在 Azure AD 企業對企業 (B2B) 來賓透過 SharePoint 或 Teams 新增的情況下，您要如何強制執行相同的同意程序呢？ 透過使用條件式存取和使用規定，您可以直接對 B2B 來賓使用者強制執行原則。 在邀請兌換流程期間，系統會向該使用者呈現使用規定。 這項支援目前只能預覽。

使用規定只會在使用者於 Azure AD 中具有來賓帳戶的情況下顯示。 SharePoint Online 目前提供[臨機操作外部共用收件者體驗](/sharepoint/what-s-new-in-sharing-in-targeted-release)，讓使用者不需要具有來賓帳戶即可共用文件或資料夾。 在此情況下，系統將不會顯示使用規定。

![所有來賓使用者](./media/active-directory-tou/b2b-guests.png)

## <a name="support-for-cloud-apps-preview"></a>針對雲端應用程式的支援 (預覽)

使用規定可以用於不同的雲端應用程式，例如 Azure 資訊保護和 Microsoft Intune。 這項支援目前只能預覽。

### <a name="azure-information-protection"></a>Azure 資訊保護

您可以針對 Azure 資訊保護應用程式設定條件式存取原則，然後在使用者存取受保護的文件時要求使用規定。 這將會在使用者首次存取受保護文件之前觸發使用規定。

![Azure 資訊保護雲端應用程式](./media/active-directory-tou/cloud-app-info-protection.png)

### <a name="microsoft-intune-enrollment"></a>Microsoft Intune 註冊

您可以針對 Microsoft Intune 註冊應用程式設定條件式存取原則，然後在使用者於 Intune 中註冊裝置之前要求使用規定。 如需詳細資訊，請參閱[為您組織的部落格文章選擇正確的規定解決方案](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409) \(英文\)。

![Microsoft Intune 雲端應用程式](./media/active-directory-tou/cloud-app-intune.png)

## <a name="frequently-asked-questions"></a>常見問題集

**問：如何查看使用者何時或是否已接受使用規定？**<br />
答：在 [使用規定] 刀鋒視窗上，按一下 [已接受] 底下的數字。 您也可以檢視或搜尋 Azure AD 稽核記錄中的接受活動。 如需詳細資訊，請參閱[檢視已接受和已拒絕的人員報表](#view-who-has-accepted-and-declined)和[檢視 Azure AD 稽核記錄](#view-azure-ad-audit-logs)。

**問：資訊會儲存多久時間？**<br />
答：使用規定報表中的使用者計數和已接受/已拒絕的人員都會儲存到使用規定的存留期結束。 Azure AD 稽核記錄會儲存 30 天。

**問：為什麼我在使用規定報表與 Azure AD 稽核記錄中看到同意數目不同？**<br />
答：使用規定報表會儲存到該使用規定的存留期結束，而 Azure AD 稽核記錄會儲存 30 天。 此外，使用規定報表只會顯示使用者目前的同意狀態。 例如，如果使用者先拒絕，然後接受，則使用規定報表將只會顯示該使用者的接受。 如果您需要查看歷程記錄，您可以使用 Azure AD 稽核記錄。

**問：如果我編輯使用規定的詳細資料，使用者是否需要重新接受？**<br />
答：否。如果系統管理員編輯使用規定的詳細資料 (名稱、顯示名稱、要求使用者展開，或新增語言)，系統並不會要求使用者重新接受新的規定。

**問：是否可以更新現有的使用規定文件？**<br />
答：您目前無法更新現有的使用規定文件。 若要變更使用規定文件，您必須建立新的使用規定執行個體。

**問：如果使用規定 PDF 文件中有超連結，使用者是否能點選那些超連結？**<br />
答：PDF 預設會以 JPEG 形式呈現，因此無法點選超連結。 使用者可以選取**檢視時發生問題嗎？請按一下這裡**的選項，這樣就會以原生方式呈現支援超連結的 PDF。

**問：使用規定是否支援多種語言？**<br />
答：是。 目前有 108 種語言可供系統管理員為單一使用規定設定。 系統管理員可以上傳多個 PDF 文件，並以相對應的語言 (最多 108 個) 標記那些文件。 當使用者登入時，我們會查看其瀏覽器語言偏好設定，並顯示相符的文件。 如果沒有相符項目，我們將會顯示預設文件，也就是第一個上傳的文件。

**問：何時會觸發使用規定？**<br />
答：使用規定會在登入體驗期間觸發。

**問：我可以將哪些應用程式設定為使用規定的目標？**<br />
答：您可以使用新式驗證，在企業應用程式上建立條件式存取原則。 如需詳細資訊，請參閱[企業應用程式](./../manage-apps/view-applications-portal.md)。

**問：是否可以將多個使用規定新增至指定的使用者或應用程式？**<br />
答：是。方法為建立多個條件式存取原則，並將那些群組或應用程式設為目標。 如果使用者落在多個使用規定的範圍內，他們必須逐一同意每個使用規定。

**問：如果使用者拒絕使用規定，會發生什麼事？**<br />
答：使用者會被封鎖而無法存取應用程式。 使用者必須再次登入並同意規定，才能取得存取權。

**問：是否可以取消接受先前接受的使用規定？**<br />
答：您可以[檢閱先前接受的使用規定](#how-users-can-review-their-terms-of-use)，但目前沒有辦法取消接受。

**問：如果同時使用 Intune 條款及條件，會發生什麼事？**<br />
答：如果您已同時設定 Azure AD 使用規定和 [Intune 條款及條件](/intune/terms-and-conditions-create)，使用者就必須同時接受兩者。 如需詳細資訊，請參閱[為您組織的部落格文章選擇正確的條款解決方案](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409) \(英文\)。

## <a name="next-steps"></a>後續步驟

- [快速入門：必須接受使用規定才可存取雲端應用程式](../conditional-access/require-tou.md)
- [Azure Active Directory 中條件式存取的最佳做法](../conditional-access/best-practices.md)
