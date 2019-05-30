---
title: 使用-Azure Active Directory 中的條款 |Microsoft Docs
description: 開始使用 Azure Active Directory 使用規定可取得存取權之前將資訊呈現給員工或來賓。
services: active-directory
ms.service: active-directory
ms.subservice: compliance
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1abae0a454e17e8f633f68bc5853bfb4a4b24d14
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/30/2019
ms.locfileid: "66383183"
---
# <a name="azure-active-directory-terms-of-use"></a>Azure Active Directory 使用規定

Azure AD 使用規定提供簡單的方法，組織可以使用來呈現給終端使用者的資訊。 此呈現可確保使用者看到合法或合規性需求的相關免責聲明。 本文說明如何開始使用的使用規定。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview-videos"></a>概觀影片

下列影片提供使用規定的快速概觀。

>[!VIDEO https://www.youtube.com/embed/tj-LK0abNao]

如需其他影片，請參閱：
- [如何部署 Azure Active Directory 中的使用規定](https://www.youtube.com/embed/N4vgqHO2tgY)
- [如何推出的 Azure Active Directory 中的使用規定](https://www.youtube.com/embed/t_hA4y9luCY)

## <a name="what-can-i-do-with-terms-of-use"></a>我可以使用規定用來做什麼？

Azure AD 使用規定具有下列功能：

- 需要接受才能存取的使用規定的員工或來賓。
- 需要接受您的每個裝置上使用，才能存取條款的員工或來賓。
- 需要接受其按照週期性排程的使用規定的員工或來賓。
- 需要接受之前註冊安全性資訊在 Azure Multi-factor Authentication (MFA) 的使用規定的員工或來賓。
- 要求員工在接受之前在 Azure AD 自助式密碼重設 (SSPR) 註冊安全性資訊的使用規定。
- 出現的一般使用規定您的組織中的所有使用者。
- 顯示特定使用者屬性 （例如為基礎的使用規定 醫生與護士或國內員工與國際員工，透過使用[動態群組](../users-groups-roles/groups-dynamic-membership.md)完成)。
- 存取高商業影響應用程式，例如 Salesforce 時所提供特定使用的規定。
- 以不同語言表示使用的規定。
- 有或沒有接受使用規定的清單。
- 協助符合隱私權法規。
- 顯示條款使用活動的符合性與稽核記錄的檔。
- 建立和管理使用使用中的條款[Microsoft Graph Api](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/agreement) （目前處於預覽狀態）。

## <a name="prerequisites"></a>必要條件

若要使用及設定 Azure AD 使用規定，您必須：

- Azure AD Premium P1、P2、EMS E3 或 EMS E5 訂用帳戶。
   - 如果您沒有其中任何一個訂用帳戶，您可以[取得 Azure AD Premium](../fundamentals/active-directory-get-started-premium.md) 或[啟用 Azure AD Premium 試用版](https://azure.microsoft.com/trial/get-started-active-directory/)。
- 下列其中一個管理帳戶，將用於您要設定的目錄：
   - 全域管理員
   - 安全性系統管理員
   - 條件式存取系統管理員

## <a name="terms-of-use-document"></a>使用規定文件

Azure AD 使用規定使用 PDF 格式來呈現內容。 此 PDF 檔案可以是任何內容 (例如現有的合約文件) 可讓您在使用者登入期間收集終端使用者合約。 若要支援行動裝置上的使用者，建議在 PDF 中使用 24 點的字型大小。

## <a name="add-terms-of-use"></a>新增使用規定

一旦完成您使用文件的條款，請使用下列程序將它加入。

1. 以全域管理員、安全性系統管理員或條件式存取系統管理員的身分登入 Azure。
1. 瀏覽至 [https://aka.ms/catou](https://aka.ms/catou) 上的 [使用規定]  。

   ![[使用規定] 刀鋒視窗](./media/terms-of-use/tou-blade.png)

1. 按一下 [新增規定]  。

   ![新增 TOU](./media/terms-of-use/new-tou.png)

1. 在 **名稱**方塊中，輸入將使用的使用規定的名稱在 Azure 入口網站中。
1. 在 [顯示名稱]  方塊中，輸入使用者在登入時會看見的標題。
1. 針對**使用規定文件**，瀏覽至您最終使用規定 PDF 並加以選取。
1. 選取您使用規定文件的語言。 語言選項可讓您上傳多個使用規定，各有不同的語言。 終端使用者會看到的使用規定版本將以其瀏覽器喜好設定為基礎。
1. 若要要求若要檢視使用規定再接受它們的使用者，設定**要求使用者展開使用規定**要**上**。
1. 若要要求使用者接受從存取每個裝置上的使用規定，設定**要求每個裝置上同意的使用者**要**上**。 如需詳細資訊，請參閱 <<c0> [ 每一裝置的使用規定](#per-device-terms-of-use)。
1. 如果您想要到期的排程同意使用條款，設定**到期同意**要**上**。 設定為 [開啟] 時，系統會顯示兩個額外的排程設定。

   ![到期同意](./media/terms-of-use/expire-consents.png)

1. 使用**到期時間起**並**頻率**設定，以指定的排程中的條款使用到期時間。 下表會顯示幾個範例設定的結果：

   | 到期生效時間 | 頻率 | 結果 |
   | --- | --- | --- |
   | 今天的日期  | 每月 | 從現在開始，使用者必須接受使用規定，並再接受每個月。 |
   | 未來的日期  | 每月 | 從現在開始，使用者必須接受使用規定。 抵達未來的日期時，同意將會到期，且使用者必須於每個月重新接受。  |

   例如，如果您將 [到期生效時間] 設定為 [1 月 1 日]  ，並將 [頻率] 設定為 [每月]  ，則下列兩個使用者所遇到的到期方式將如下所示：

   | 使用者 | 第一次接受日期 | 第一次到期日期 | 第二次到期日期 | 第三次到期日期 |
   | --- | --- | --- | --- | --- |
   | Alice | 1 月 1 日 | 2 月 1 日 | 3 月 1 日 | 4 月 1 日 |
   | Bob | 1 月 15 日 | 2 月 1 日 | 3 月 1 日 | 4 月 1 日 |

1. 使用 **持續時間之前重新接受需要 （天）** 設定來指定使用者必須接受使用規定之前的天數。 這可讓使用者遵循自己的排程。 例如，如果您將期間設定為 **30** 天，則下列兩個使用者所遇到的到期方式將如下所示：

   | 使用者 | 第一次接受日期 | 第一次到期日期 | 第二次到期日期 | 第三次到期日期 |
   | --- | --- | --- | --- | --- |
   | Alice | 1 月 1 日 | 1 月 31 日 | 3 月 2 日 | 4 月 1 日 |
   | Bob | 1 月 15 日 | 2 月 14 日 | 3 月 16 日 | 4 月 15 日 |

   您可以同時使用 [到期同意]  和 [需要重新接受之前的期間 (天)]  設定，但通常您只會使用其中一個。

1. 底下**條件式存取**，使用**強制使用條件式存取原則範本**清單中選取的範本，以強制執行的使用規定。

   ![條件式存取範本](./media/terms-of-use/conditional-access-templates.png)

   | 範本 | 描述 |
   | --- | --- |
   | **所有來賓的雲端應用程式存取權** | 系統將會針對所有來賓和所有雲端應用程式建立條件式存取原則。 此原則會影響 Azure 入口網站。 建立此原則之後，您可能需要登出然後再登入。 |
   | **所有使用者的雲端應用程式存取權** | 系統將會針對所有使用者和所有雲端應用程式建立條件式存取原則。 此原則會影響 Azure 入口網站。 建立此原則之後，您必須登出然後再登入。 |
   | **自訂原則** | 選取使用者、 群組，以及使用此規定將會套用到的應用程式。 |
   | **稍後建立條件式存取原則** | 此使用規定將會在建立條件式存取原則時在授與控制清單中出現。 |

   >[!IMPORTANT]
   >條件式存取原則控制項 (包括使用條款) 不支援服務帳戶的強制執行。 建議您排除條件式存取原則中的所有服務帳戶。

    自訂的條件式存取原則可讓細微使用條款，向特定的雲端應用程式或使用者群組。 如需詳細資訊，請參閱[快速入門：必須接受使用規定才可存取雲端應用程式](require-tou.md)。

1. 按一下頁面底部的 [新增]  。

   如果您已選取自訂的條件式存取範本，則會出現新的畫面以讓您建立自訂條件式存取原則。

   ![自訂原則](./media/terms-of-use/custom-policy.png)

   現在，您應該會看到新使用規定。

   ![新增 TOU](./media/terms-of-use/create-tou.png)

## <a name="view-report-of-who-has-accepted-and-declined"></a>檢視已接受和已拒絕的人員報表

[使用規定] 刀鋒視窗會顯示已接受和已拒絕的使用者計數。 這些計數和人員接受/拒絕會儲存生命週期的使用規定。

1. 登入 Azure 並瀏覽至 [https://aka.ms/catou](https://aka.ms/catou) 上的 [使用規定]  。

   ![[使用規定] 刀鋒視窗](./media/terms-of-use/view-tou.png)

1. 使用規定，請按一下下方的數字**Accepted**或是**已拒絕**若要檢視使用者的目前狀態。

   ![針對使用規定的同意](./media/terms-of-use/accepted-tou.png)

1. 若要檢視個別使用者的歷程記錄，請按一下省略符號 ( **...** )，然後按一下 [檢視記錄]  。

   ![[檢視記錄] 功能表](./media/terms-of-use/view-history-menu.png)

   在 [檢視記錄] 窗格中，您可以看見所有接受、拒絕及到期的歷程記錄。

   ![[檢視記錄] 窗格](./media/terms-of-use/view-history-pane.png)

## <a name="view-azure-ad-audit-logs"></a>檢視 Azure AD 稽核記錄

如果您想要檢視其他的活動，Azure AD 使用規定會包含稽核記錄檔。 使用者每次同意時都會觸發稽核記錄事件，並將儲存 **30 天**。 您可以在入口網站中檢視這些記錄或將其下載為 .csv 檔案。

若要開始使用 Azure AD 稽核記錄，請使用下列程序：

1. 登入 Azure 並瀏覽至 [https://aka.ms/catou](https://aka.ms/catou) 上的 [使用規定]  。
1. 選取 使用規定。
1. 按一下 [檢視稽核記錄]  。

   ![[使用規定] 刀鋒視窗](./media/terms-of-use/audit-tou.png)

1. 在 Azure AD 稽核記錄畫面上，您可以使用提供的清單來篩選資訊，以找出特定的稽核記錄資訊。

   您也可以按一下 [下載]  ，將資訊下載成 .csv 檔案，以在本機中使用。

   ![稽核記錄](./media/terms-of-use/audit-logs-tou.png)

   如果您按一下某個記錄，螢幕會出現具有額外活動詳細資料的窗格。

   ![活動詳細資料](./media/terms-of-use/audit-log-activity-details.png)

## <a name="what-terms-of-use-looks-like-for-users"></a>使用者的使用條款看起來像

一旦建立並強制執行使用規定，使用者會在範圍內，會在登入時看到下列畫面。

![使用者網頁登入](./media/terms-of-use/user-tou.png)

使用者可以檢視使用規定，如果有必要，請使用按鈕來放大和縮小。

![檢視與縮放按鈕的使用規定](./media/terms-of-use/zoom-buttons.png)

下列畫面顯示使用規定行動裝置上的外觀。

![使用者行動裝置登入](./media/terms-of-use/mobile-tou.png)

使用者只需要接受使用條款之後，它們將不會看到的使用規定再次後續的登入。

### <a name="how-users-can-review-their-terms-of-use"></a>使用者可以列印文件的檢閱，請使用其規定

使用者可以檢閱，並查看他們已使用下列程序接受使用規定。

1. 登入 [https://myapps.microsoft.com](https://myapps.microsoft.com)。
1. 在右上角，按一下您的名稱並選取 [設定檔]  。

   ![設定檔](./media/terms-of-use/tou14.png)

1. 在您的設定檔頁面上，按一下 [檢閱使用規定]  。

   ![設定檔 - 檢閱使用規定](./media/terms-of-use/tou13a.png)

1. 您可以在此檢閱您已接受的使用規定。

## <a name="edit-terms-of-use-details"></a>編輯使用規定詳細資料

您可以編輯使用條款的一些詳細資料，但您無法修改現有的文件。 下列程序說明如何編輯詳細資料。

1. 登入 Azure 並瀏覽至 [https://aka.ms/catou](https://aka.ms/catou) 上的 [使用規定]  。
1. 選取您想要編輯的使用規定。
1. 按一下 [編輯使用規定]  。
1. 在 [編輯使用規定] 窗格中，變更名稱、顯示名稱，或要求使用者展開值。

   如果您想要變更，例如 PDF 文件的其他設定要求使用者同意每個裝置上，過期同意、 持續時間 reacceptance 或之前的條件式存取原則，您必須建立新的使用條款。

   ![編輯使用規定](./media/terms-of-use/edit-tou.png)

1. 按一下 [確定]  儲存變更。

   儲存您的變更後，使用者將不需要重新接受這些編輯後的規定。

## <a name="add-a-terms-of-use-language"></a>將使用語言的詞彙

下列程序描述如何將使用語言的詞彙。

1. 登入 Azure 並瀏覽至 [https://aka.ms/catou](https://aka.ms/catou) 上的 [使用規定]  。
1. 選取您想要編輯的使用規定。
1. 在 [詳細資料] 窗格中，按一下 [語言]  索引標籤。

   ![新增 TOU](./media/terms-of-use/languages-tou.png)

1. 按一下 [新增語言]  。
1. 在 [新增使用規定語言] 窗格中，上傳當地語系化的 PDF 並選取的語言。

   ![新增 TOU](./media/terms-of-use/language-add-tou.png)

1. 按一下 [新增]  來新增語言。

## <a name="per-device-terms-of-use"></a>每一裝置的使用規定

**使用者必須同意每個裝置上**設定可讓您要求使用者接受從存取每個裝置上的使用規定。 使用者必須在 Azure AD 中加入其裝置。 當已加入裝置時，裝置識別碼用來強制執行的每個裝置上的使用規定。

以下是支援的平台與軟體清單。

> [!div class="mx-tableFixed"]
> |  | iOS | Android | Windows 10 | 其他 |
> | --- | --- | --- | --- | --- |
> | **原生應用程式** | 是 | 是 | 是 |  |
> | **Microsoft Edge** | 是 | 是 | 是 |  |
> | **Internet Explorer** | 是 | 是 | 是 |  |
> | **Chrome (搭配擴充功能)** | 是 | 是 | 是 |  |

每一裝置的使用規定會有下列限制：

- 一部裝置只能加入至單一租用戶。
- 使用者必須具有加入其裝置的權限。
- 不支援「Intune 註冊」應用程式。
- 不支援 azure AD B2B 使用者。

如果使用者的裝置未加入，他們將會收到要求加入其裝置的訊息。 使用者的體驗將取決於平台和軟體。

### <a name="join-a-windows-10-device"></a>加入 Windows 10 裝置

如果使用者是使用 Windows 10 和 Microsoft Edge，他們將會收到類似下列的訊息以[加入其裝置](../user-help/user-help-join-device-on-network.md#to-join-an-already-configured-windows-10-device)。

![Windows 10 和 Microsoft Edge - 加入裝置提示](./media/terms-of-use/per-device-win10-edge.png)

如果他們是使用 Chrome，系統將會提示他們安裝 [Windows 10 Accounts 擴充功能](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji) \(英文\)。

### <a name="browsers"></a>瀏覽器

如果使用者是使用不支援的瀏覽器，系統將會提示他們使用其他瀏覽器。

![不支援的瀏覽器](./media/terms-of-use/per-device-browser-unsupported.png)

## <a name="delete-terms-of-use"></a>刪除使用規定

您可以刪除舊的使用下列程序的使用規定。

1. 登入 Azure 並瀏覽至 [https://aka.ms/catou](https://aka.ms/catou) 上的 [使用規定]  。
1. 選取您想要移除的使用規定。
1. 按一下 [刪除規定]  。
1. 出現詢問您是否要繼續的訊息時，請按一下 [是]  。

   ![刪除使用規定](./media/terms-of-use/delete-tou.png)

   您應該不會再看到使用規定。

## <a name="deleted-users-and-active-terms-of-use"></a>已刪除的使用者與使用規定

根據預設，已刪除的使用者會在 Azure AD 中以已刪除的狀態保留 30 天，在這段期間內，系統管理員可以視需要加以還原。 經過 30 天後，該使用者將永久刪除。 此外，全域管理員可以使用 Azure Active Directory 入口網站，在該時段結束之前明確地[永久刪除最近刪除的使用者](../fundamentals/active-directory-users-restore.md)。 其中一個使用者已永久刪除，該使用者相關的後續資料將會移除從作用中的使用規定。 與已刪除的使用者有關的稽核資訊仍會保留在稽核記錄中。

## <a name="policy-changes"></a>原則變更

條件式存取原則會立即生效。 當此情況發生時，系統管理員便會開始看到「悲傷雲端」或「Azure AD 權杖問題」。 系統管理員必須登出並再次登入，才能符合新的原則。

> [!IMPORTANT]
> 如果情況如下，範圍中的使用者必須登出並登入，才能符合新的原則：
>
> - 對使用規定啟用條件式存取原則
> - 或建立第二個使用條款

## <a name="b2b-guests-preview"></a>B2B 來賓 (預覽)

大部分的組織有一個程序為其員工同意其組織的規定與隱私權聲明。 但在 Azure AD 企業對企業 (B2B) 來賓透過 SharePoint 或 Teams 新增的情況下，您要如何強制執行相同的同意程序呢？ 使用條件式存取和使用規定，您可以強制執行原則，以直接針對 B2B 來賓使用者。 邀請兌換在流程期間，使用者會看到與使用規定。 這項支援目前只能預覽。

使用規定只會在使用者於 Azure AD 中具有來賓帳戶的情況下顯示。 SharePoint Online 目前有[臨機操作外部共用收件者經驗](/sharepoint/what-s-new-in-sharing-in-targeted-release)共用文件或不需要使用者具有來賓帳戶的資料夾。 在此情況下，不會顯示使用規定。

![所有來賓使用者](./media/terms-of-use/b2b-guests.png)

## <a name="support-for-cloud-apps-preview"></a>針對雲端應用程式的支援 (預覽)

使用規定可以用於不同的雲端應用程式，例如 Azure 資訊保護和 Microsoft Intune。 這項支援目前只能預覽。

### <a name="azure-information-protection"></a>Azure 資訊保護

您可以設定 Azure 資訊保護應用程式的條件式存取原則，並要求使用規定，當使用者存取受保護的文件。 這會觸發在使用者第一次存取受保護的文件之前的使用規定。

![Azure 資訊保護雲端應用程式](./media/terms-of-use/cloud-app-info-protection.png)

### <a name="microsoft-intune-enrollment"></a>Microsoft Intune 註冊

您可以設定 Microsoft Intune 註冊應用程式的條件式存取原則，並要求在 Intune 中的裝置註冊之前的使用規定。 如需詳細資訊，請參閱[為您組織的部落格文章選擇正確的規定解決方案](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409) \(英文\)。

![Microsoft Intune 雲端應用程式](./media/terms-of-use/cloud-app-intune.png)

> [!NOTE]
> Intune 註冊應用程式不支援[每一裝置的使用規定](#per-device-terms-of-use)。

## <a name="frequently-asked-questions"></a>常見問題集

**问：如何查看何時或是否使用者已接受使用規定？**<br />
答：在 [使用規定] 刀鋒視窗上，按一下 [已接受]  底下的數字。 您也可以檢視或搜尋 Azure AD 稽核記錄中的接受活動。 如需詳細資訊，請參閱檢視已接受和已拒絕的人員報表和[檢視 Azure AD 稽核記錄](#view-azure-ad-audit-logs)。

**问：資訊會儲存多久時間？**<br />
答：使用者計算中使用的報表和人員接受/拒絕使用規定的存留期間儲存中的條款。 Azure AD 稽核記錄會儲存 30 天。

**问：為什麼看到稽核記錄檔不同數目的同意在使用的報表，與 Azure AD 中的條款？**<br />
答：使用報表中的條款會儲存該規定，在 Azure AD 稽核記錄檔會儲存 30 天的存留期。 此外，使用的報表中的條款只會顯示目前使用者的同意狀態。 例如，如果使用者拒絕，然後接受，使用的報表中的條款將只會顯示該使用者的接受。 如果您需要查看歷程記錄，您可以使用 Azure AD 稽核記錄。

**问：如果我編輯使用規定詳細資料，不會要求使用者重新接受？**<br />
答：否，如果系統管理員編輯使用規定詳細資料 （名稱、 顯示名稱、 要求使用者展開，或新增的語言），它不需要使用者重新接受新規定。

**问：我是否可以更新現有的條款使用文件？**<br />
答：目前，您無法更新現有的條款使用文件。 若要變更使用規定文件，您必須建立新的條款使用執行個體。

**问：使用規定 PDF 文件中的超連結時，將終端使用者能夠按一下這些嗎？**<br />
答：PDF 預設會以 JPEG 形式呈現，因此無法點選超連結。 使用者可以選取**檢視時發生問題嗎？請按一下這裡**的選項，這樣就會以原生方式呈現支援超連結的 PDF。

**问：使用規定可支援多種語言？**<br />
答：是。 目前有 108 不同的語言，系統管理員可以設定單一使用規定。 系統管理員可以上傳多個 PDF 文件，並以相對應的語言 (最多 108 個) 標記那些文件。 當使用者登入時，我們會查看其瀏覽器語言偏好設定，並顯示相符的文件。 如果沒有相符項目，我們將會顯示預設文件，也就是第一個上傳的文件。

**问：何時觸發使用規定？**<br />
答：登入體驗期間，會觸發使用規定。

**问：哪些應用程式可以設為目標來使用規定？**<br />
答：您可以使用新式驗證，在企業應用程式上建立條件式存取原則。 如需詳細資訊，請參閱[企業應用程式](./../manage-apps/view-applications-portal.md)。

**问：可以指定的使用者或應用程式新增多個使用規定嗎？**<br />
答：是。方法為建立多個條件式存取原則，並將那些群組或應用程式設為目標。 如果使用者落在範圍內的多個使用規定，它們會接受一個一次的使用規定。

**问：如果使用者拒絕使用規定，會發生什麼事？**<br />
答：使用者會被封鎖而無法存取應用程式。 使用者必須再次登入並同意規定，才能取得存取權。

**问：是否可以 unaccept 先前已接受的使用規定？**<br />
答：您可以[檢閱先前接受使用規定](#how-users-can-review-their-terms-of-use)，但目前沒有辦法 unaccept。

**问：如果同時使用 Intune 條款及條件，會發生什麼事？**<br />
答：如果您已設定這兩個 Azure AD 使用規定及[Intune 條款和條件](/intune/terms-and-conditions-create)，使用者必須接受兩者。 如需詳細資訊，請參閱[為您組織的部落格文章選擇正確的條款解決方案](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409) \(英文\)。

**问：使用服務合約會使用哪些端點進行驗證？**<br />
答：使用規定會利用下列端點進行驗證： https://tokenprovider.termsofuse.identitygovernance.azure.com和 https://account.activedirectory.windowsazure.com。 如果您的組織已註冊的允許清單的 Url，您必須新增這些端點，以您的允許清單，以及 Azure AD 端點的登入。

## <a name="next-steps"></a>後續步驟

- [快速入門：必須接受使用規定才可存取雲端應用程式](require-tou.md)
- [Azure Active Directory 中條件式存取的最佳做法](best-practices.md)
