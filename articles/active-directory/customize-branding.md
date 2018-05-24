---
title: 自訂 Azure Active Directory 租用戶的登入頁面 | Microsoft Docs
description: 了解如何將公司商標新增到 Azure 登入頁面
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 01/19/2018
ms.author: curtand
ms.reviewer: kexia
custom: it-pro
ms.openlocfilehash: ac8562b77ba43dbb07e886dfa235586198027796
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
ms.locfileid: "33764439"
---
# <a name="quickstart-add-company-branding-to-your-sign-in-page-in-azure-ad"></a>快速入門：在 Azure AD 中將公司商標新增至登入頁面
為了避免混淆，許多公司都想對其管理的所有網站和服務套用一致的外觀與風格。 Azure Active Directory (Azure AD) 是透過讓您利用公司標誌和自訂色彩配置來自訂登入頁面外觀的方式，提供這項功能。 登入頁面會在您登入 Web 型應用程式 (例如使用 Azure AD 作為識別提供者的 Office 365) 時顯示。 您可以與此頁面互動來輸入您的認證。

> [!NOTE]
> * 公司商標只有在您已購買 Azure AD 的「進階」或「基本」授權或擁有 Office 365 授權時，才可供使用。 若要了解您的授權類型是否支援某功能，請查看 [Azure Active Directory 價格資訊頁面](https://azure.microsoft.com/pricing/details/active-directory/)。
> 
> * Azure AD Premium 和 Basic 版本適用於使用全球 Azure Active Directory 執行個體的中國客戶。 由 21Vianet 在中國營運的 Azure 服務目前不支援 Azure AD Premium 和 Basic 版本。 如需詳細資訊，請透過 [Azure Active Directory 論壇](https://feedback.azure.com/forums/169401-azure-active-directory/) \(英文\) 與我們連絡。

## <a name="customizing-the-sign-in-page"></a>自訂登入頁面

<!--You can customize the following elements on the sign-in page: <attach image>-->

當使用者存取租用戶特定的 URL (例如 [*https://outlook.com/contoso.com*](https://outlook.com/contoso.com)) 時，公司商標自訂會出現在 Azure AD 登入頁面上。

例如，當使用者瀏覽 www.office.com 時，由於使用者尚未輸入認證，因此登入頁面不會顯示任何公司商標自訂項目。 在使用者輸入其使用者識別碼或選取使用者圖格之後，就會顯示公司商標。

> [!NOTE]
> * 在您已設定商標之 Azure 入口網站的 [網域]  部分，您的網域名稱必須顯示為 [作用中]。 如需詳細資訊，請參閱[新增自訂網域名稱](add-custom-domain.md)。
> * 登入頁面商標不會延續到個人 Microsoft 帳戶的登入頁面。 如果您的員工或商務來賓使用個人 Microsoft 帳戶登入，其登入頁面不會反映貴組織的商標。


### <a name="banner-logo"></a>橫幅標誌 

說明 | 條件約束 | 建議
------- | ------- | ----------
橫幅標誌會顯示在 [登入] 和 [存取面板] 頁面上。<br>在登入頁面上，會在輸入使用者名稱之後顯示標誌。 | 透明 JPG 或 PNG<br>最大高度：36 像素<br>最大寬度：245 像素 | 在這裡使用您組織的標誌。<br>使用透明的映像。 請勿假設背景為白色。<br>請勿在映像中的標誌周圍加上邊框間距，否則您的標誌會看起來不成比例。

### <a name="username-hint"></a>使用者名稱提示   
說明 | 條件約束 | 建議
------- | ------- | ----------
此選項可自訂使用者名稱欄位中的提示文字。 | Unicode 文字最多 64 個字元<br>僅限純文字 | 如果您預期會有您組織外的來賓使用者登入應用程式，建議您不要設定此選項。
            
### <a name="sign-in-page-text"></a>登入頁面文字   
說明 | 條件約束 | 建議
------- | ------- | ----------
此選項會出現在登入表單底部，可用來提供其他資訊 (例如技術支援中心的電話號碼)，或顯示法律聲明。 | Unicode 文字最多 256 個字元<br>僅純文字 (沒有連結或 HTML 標記)    

### <a name="sign-in-page-image"></a>登入頁面映像  
說明 | 條件約束 | 建議
------- | ------- | ----------
此選項會出現在登入頁面的背景中，錨定至可檢視空間的中心，並且會調整並裁切以填滿瀏覽器視窗。    <br>在手機這類螢幕狹窄的裝置上，不會顯示此映像。<br>載入頁面時，會在這整個影像套用 0.55 不透明度的黑色遮罩。 | JPG 或 PNG<br>映像尺寸：1920 x 1080 像素<br>檔案大小：&lt; 300 KB | <br>使用沒有強烈主題焦點的映像。 不透明的登入表單會出現在此影像的中央，並且可根據瀏覽器視窗的大小來覆蓋此影像的任何部分。<br>請將檔案大小維持在小型，以確保載入時間快速。 

### <a name="sign-in-page-background-color"></a>登入頁面背景色彩
說明 | 條件約束 | 建議
------- | ------- | ----------
在低頻寬連線時，此顏色會用來取代背景映像。 | 十六進位格式的 RGB 色彩 (範例：#FFFFFF | 建議使用橫幅標誌的主要顏色或貴組織的代表顏色。

### <a name="square-logo-image"></a>方形標誌影像
說明 | 條件約束 | 建議
------- | ------- | ----------
此影像會在進行新的企業版 Windows 10 電腦設定時出現。 它會在員工設定其新工作電腦時，為其提供內容。 此影像的顯示對象為使用 [Windows AutoPilot](https://blogs.windows.com/business/2017/06/29/delivering-modern-promise-windows-10/?utm_source=dlvr.it&utm_medium=twitter#gDTp1u6q35bvDWIS.97) 來部署其工作裝置的租用戶，而在其他 Windows 10 體驗中則是會在密碼輸入頁面上顯示。 | 透明的 PNG (慣用) 或 JPG<br>影像尺寸：240 x 240 像素<br>檔案大小：&lt; 10 KB | 在這裡使用您組織的標誌。<br> 使用透明的映像。<br>請勿假設背景為白色。<br>請勿為影像中的標誌加上邊框間距，否則標誌會看起來不成比例且較小。

### <a name="show-option-to-remain-signed-in"></a>顯示保持登入的選項
說明 | 條件約束 | 建議
------- | ------- | ----------
Azure AD 登入可讓使用者選擇在關閉並重新開啟其瀏覽器時保持登入。 此設定會隱藏該選項。<br>設定為 [否] 可向您的使用者隱藏此選項。 | &nbsp; | 隱藏此選項並不會影響工作階段存留期。<br>SharePoint Online 和 Office 2010 的某些功能取決於能夠選擇此選項以保持登入的使用者。 如果您將此選項設定為 [否]，使用者可能就會在登入時看見額外和非預期的提示。

> [!NOTE]
> 所有元素都是選用的。 例如，如果您指定的橫幅標誌沒有任何背景映像，登入頁面會針對目的地網站 (例如，Office 365) 顯示您的標誌和背景映像。

## <a name="add-company-branding-to-your-directory"></a>將公司商標新增到您的目錄

1. 使用具備租用戶全域管理員身分的帳戶來登入 [Azure AD 系統管理中心](https://aad.portal.azure.com)。
2. 選取 [使用者和群組] > [公司商標] > [編輯]。
  
  ![開啟自訂商標](./media/customize-branding/navigation-to-branding.png)
3. 修改您想要自訂的元素。 所有元素都是選用的。
  
  ![編輯自訂商標](./media/customize-branding/edit-branding.png)
5. 完成時，選取 [儲存]。

您對登入頁面商標所做的任何變更可能最多需要一個小時才會顯示。

## <a name="add-language-specific-company-branding-to-your-directory"></a>將特定語言公司商標新增到您的目錄

1. 使用具備目錄全域管理員身分的帳戶來登入 [Azure AD 管理中心](https://aad.portal.azure.com)。
2. 選取 [使用者和群組] > [公司商標] > [新增語言]。
  
  ![新增語言特定商標元素](./media/customize-branding/add-language.png)
5. 修改您想要自訂的元素。 所有元素都是選用的。
6. 完成時，選取 [儲存]。

您對登入頁面商標所做的任何變更可能最多需要一個小時才會顯示。

## <a name="next-steps"></a>後續步驟
本快速入門中，您會了解如何將公司商標新增到您的 Azure AD 目錄。 

您可以從 Azure 入口網站使用下列連結在 Azure AD 中設定公司商標。

> [!div class="nextstepaction"]
> [設定公司商標](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/LoginTenantBrandingBlade) 