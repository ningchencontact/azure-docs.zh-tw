---
title: Azure Stack 將服務主體認證儲存在 Key Vault 中 | Microsoft Docs
description: 了解如何在 Azure Stack 上使用 Key Vault 儲存服務主體認證
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2019
ms.author: sethm
ms.openlocfilehash: 3fcbf2b3160d57e56a59ba9c374c9b1b2a75a159
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2019
ms.locfileid: "54330240"
---
# <a name="store-service-principal-credentials-in-key-vault"></a>將服務主體認證儲存在 Key Vault 中

在 Azure Stack 上開發應用程式需要建立服務主體，並在部署前使用那些認證來進行驗證。 不過，針對服務主體所儲存的認證經常會被錯置。 本文會說明如何建立服務主體，並將其值儲存在 Azure Key Vault 中以供日後擷取。

如需 Key Vault 的詳細資訊，請參閱[這篇文章](azure-stack-key-vault-intro.md)。

## <a name="prerequisites"></a>必要條件

- 包含 Azure Key Vault 服務之供應項目的訂閱。
- 已將 PowerShell 設定為可搭配 Azure Stack 使用。

## <a name="key-vault-in-azure-stack"></a>Azure Stack 中的 Key Vault

Azure Stack 中的 Key Vault 可協助保護雲端應用程式和服務所使用的密碼編譯金鑰和祕密。 藉由使用 Key Vault，您可以對金鑰和祕密進行加密。

若要建立金鑰保存庫，請遵循下列步驟：

1. 登入 Azure Stack 入口網站。

2. 從儀表板選取 [+ 建立資源]，選取 [安全性 + 識別]，然後選取 [Key Vault]。

   ![建立金鑰保存庫](media/azure-stack-key-vault-store-credentials/create-key-vault.png)

3. 在 [建立金鑰保存庫] 窗格中，為您的保存庫指派 [名稱]。 保存庫名稱只能包含英數字元和連字號 (-) 字元。 不得以數字開頭。

4. 從可用的訂用帳戶清單中選擇訂用帳戶。

5. 選取現有的資源群組或建立新群組。

6. 選取定價層。

7. 選擇其中一個現有的存取原則或建立新原則。 存取原則可讓您授與使用者、應用程式或安全性群組權限，以執行此保存庫的作業。

8. 您也可以選擇進階存取權原則以允許存取功能。

9. 在您進行設定之後，請選取 [確定]，然後選取 [建立]。 系統將會開始進行金鑰保存庫的部署。

## <a name="create-a-service-principal"></a>建立服務主體

1. 透過 Azure 入口網站登入您的 Azure 帳戶。

2. 選取 [Azure Active Directory]，選取 [應用程式註冊]，然後選取 [新增]。

3. 提供應用程式的名稱和 URL。 針對您想要建立的應用程式類型，選取 [Web 應用程式/API] 或 [原生]。 設定值之後，選取 [建立]。

4. 選取 [Active Directory]，選取 [應用程式註冊]，然後選取您的應用程式。

5. 複製 [應用程式識別碼] 並儲存在您的應用程式碼中。 範例應用程式中的應用程式會在參考 [應用程式識別碼] 時使用**用戶端識別碼**。

6. 若要產生驗證金鑰，請選取 [金鑰]。

7. 提供金鑰的描述和持續時間。

8. 選取 [ **儲存**]。

9. 複製系統在您按一下 [儲存] 後所提供的**金鑰**。

## <a name="store-the-service-principal-inside-key-vault"></a>將服務主體儲存在 Key Vault 內

1. 登入 Azure Stack 的使用者入口網站，選取您先前所建立的金鑰保存庫，然後選取 [祕密] 圖格。

2. 在 [祕密] 窗格中，選取 [產生/匯入]。

3. 在 [建立祕密] 窗格中，從選項清單中選取 [手動]。 如果您已使用憑證建立服務主體，請從下拉式清單中選取憑證，然後上傳檔案。

4. 輸入從服務主體複製的**應用程式識別碼**作為金鑰的名稱。 金鑰名稱只能包含英數字元和連字號 (-) 字元。

5. 將金鑰的值從服務主體貼至 [值] 索引標籤。

6. 選取 [服務主體] 作為 [內容類型]。

7. 為金鑰設定 [啟用日期] 和 [到期日期] 值。

8. 選取 [建立] 以開始部署。

成功建立祕密之後，系統便會將服務主體資訊儲存在那裡。 您可以隨時在 [祕密] 底下選取它，並檢視或修改其屬性。 properties 區段包含祕密識別碼，其為外部應用程式用來存取此祕密的統一資源識別項 (URI)。

## <a name="next-steps"></a>後續步驟

- [使用服務主體](azure-stack-create-service-principals.md)
- [使用入口網站管理 Azure Stack 中的 Key Vault](azure-stack-key-vault-manage-portal.md)  
- [使用 PowerShell 管理 Azure Stack 中的 Key Vault](azure-stack-key-vault-manage-powershell.md)