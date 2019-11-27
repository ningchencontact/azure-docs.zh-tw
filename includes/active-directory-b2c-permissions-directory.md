---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: marsma
ms.openlocfilehash: 43bcd1f11eb228bd1454b2ad0f2addb851029f2f
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2019
ms.locfileid: "73799707"
---
#### <a name="applicationstabapplications"></a>[](#tab/applications/)

1. 在 [**已註冊的應用程式**總覽] 頁面上，選取 [**設定**]。
1. 在 [ **API 存取**] 底下，選取 [**必要許可權**]。
1. 選取 [Windows Azure Active Directory]。
1. 在 [**應用程式許可權**] 底下，選取 [**讀取和寫入目錄資料**]。
1. 選取 [ **儲存**]。
1. 選取 [授與權限]，然後選取 [是]。 可能需要幾分鐘的時間才能完全傳播許可權。

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[應用程式註冊 (預覽)](#tab/app-reg-preview/)

1. 在 [管理] 之下選取 [API 權限]。
1. 在 [已設定的權限] 底下，選取 [新增權限]。
1. 選取 [ **Azure Active Directory 圖形]** 。
1. 選取 [應用程式權限]。
1. 展開 [**目錄**]，然後選取 [**目錄. ReadWrite. 全部**] 核取方塊。
1. 選取 [新增權限]。 依照指示，稍等幾分鐘，然後再繼續進行下一個步驟。
1. 選取 [授與管理員同意 (您的租用戶名稱)]。
1. 選取租使用者系統管理員帳戶。
1. 選取 [接受]。
1. 選取 **[** 重新整理]，然後確認「已授與 ...」出現在 [**狀態**] 之下。 權限可能需要幾分鐘的時間來傳播。